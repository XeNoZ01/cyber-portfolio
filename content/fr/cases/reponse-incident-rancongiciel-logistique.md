+++
title = "Réponse à incident : compte administrateur compromis et rançongiciel"
date = 2026-11-24
draft = false
description = "Scénario fictif dans une PME logistique de 60 personnes : chronologie heure par heure, décisions, notifications et retour d'expérience, selon le cycle de réponse à incident NIST et ISO/IEC 27035."
case_study = "fictive"
translationKey = "incident-response-ransomware"
+++

## Cadre de l'étude

**Organisation fictive : Fictilog.** PME de logistique et d'entreposage d'environ 60 personnes, répartie sur deux sites : un siège (bureaux et entrepôt principal) et un site secondaire (« site B », entrepôt et quai de chargement). Elle gère des stocks pour des clients industriels et emploie des conducteurs et des préparateurs de commandes.

**Objectif :** montrer comment se déroule la réponse à un incident, de la détection au retour d'expérience, avec des décisions justifiées et leurs conséquences. L'étude s'appuie sur le cycle de réponse à incident du NIST (SP 800-61, dont la révision 2 décrit quatre phases) et sur la norme ISO/IEC 27035, et rattache chaque étape aux mesures de l'ISO/IEC 27002:2022.

**Ce qui est inventé :** l'entreprise, les personnes, les systèmes, la chronologie, les indicateurs techniques, les durées et les chiffres. **Ce qui est réel :** les référentiels, la logique du cycle de réponse et les mesures de référence citées. Il ne s'agit d'aucun incident réel et d'aucune mission vécue.

---

## 1. Préparation : l'état de départ (hypothèses)

Une réponse se joue largement avant l'incident. Voici ce que l'étude suppose en place chez Fictilog le jour de l'attaque.

| Élément | Situation supposée | Mesure ISO 27002:2022 |
|---|---|---|
| Plan de réponse à incident | Un document de deux pages existe, jamais testé | 5.24 |
| Astreinte | Un prestataire informatique externe, joignable en journée ; astreinte de nuit informelle | 5.24, 6.8 |
| Journalisation | Journaux conservés 30 jours, non centralisés ; pas de supervision la nuit | 8.15, 8.16 |
| Comptes à privilèges | Un compte administrateur de domaine partagé entre deux techniciens, mot de passe ancien | 5.15, 8.2 |
| Authentification | Pas de multifacteur sur l'accès VPN | 8.5 |
| Sauvegarde | Copie en ligne quotidienne, plus une copie hors ligne hebdomadaire ; restauration testée une fois en deux ans | 8.13 |
| Contacts utiles | Liste incomplète (assureur, conseil juridique, référent données personnelles) | 5.5, 5.6 |

**Lecture :** les faiblesses sont banales dans une PME. Elles expliquent la suite de l'étude : l'incident n'a rien d'exceptionnel, c'est la préparation qui détermine sa durée.

### Inventaire des actifs du périmètre touché (fictif)

Criticité sur 4 niveaux (1 : faible, 4 : vitale pour l'activité). Les valeurs sont des hypothèses d'étude.

| Actif | Rôle | Propriétaire | Criticité | État pendant l'incident |
|---|---|---|---|---|
| Contrôleurs de domaine (2, siège) | Annuaire et authentification | Responsable informatique | 4 | Compte privilégié compromis ; machines non chiffrées |
| Serveur de fichiers, site B | Documents de travail, dossiers RH du site | Responsable du site B | 3 | Chiffré, copie de données probable |
| Serveur WMS, site B | Gestion d'entrepôt (stocks, préparation) | Responsable d'exploitation | 4 | Chiffré |
| Serveur d'applications et d'impression, site B | Services annexes | Responsable informatique | 2 | Chiffré |
| ERP, siège | Facturation, commandes clients | Directeur administratif et financier | 4 | Non touché |
| Accès VPN | Accès distant des techniciens et du prestataire | Responsable informatique | 3 | Point d'entrée |
| Sauvegarde en ligne | Copie quotidienne | Responsable informatique | 4 | Partiellement chiffrée |
| Sauvegarde hors ligne | Copie hebdomadaire déconnectée | Responsable informatique | 4 | Intacte |
| 22 postes de travail, site B | Poste des équipes | Responsable du site B | 2 | Chiffrés |
| Terminaux de scan, site B | Lecture des étiquettes | Responsable d'exploitation | 2 | Inutilisables (dépendent du WMS) |

### Chemin d'attaque reconstitué (fictif, niveau conceptuel)

| Étape | Ce qui s'est passé (hypothèse) | Faiblesse exploitée |
|---|---|---|
| 1. Accès initial | Connexion au VPN avec le compte administrateur partagé, dont le mot de passe était ancien et réutilisé ailleurs | Pas de multifacteur ; compte partagé (8.5, 5.15) |
| 2. Reconnaissance | Consultation de l'annuaire et des partages réseau pendant plusieurs nuits | Pas de supervision, journaux non centralisés (8.15, 8.16) |
| 3. Persistance | Création du compte `svc-backup2` et d'une tâche planifiée | Droits d'administration trop larges (8.2) |
| 4. Mouvement latéral | Rebond du siège vers le site B avec le compte privilégié | Réseaux peu cloisonnés (8.22) |
| 5. Collecte | Archivage nocturne et envoi de dossiers du serveur de fichiers vers l'extérieur | Pas de détection de volumes anormaux (8.16) |
| 6. Impact | Déclenchement du chiffrement sur le site B, puis dépôt de la note de rançon | Sauvegarde en ligne accessible avec un compte du domaine (8.13) |

Cette cartographie est utile en deux sens : elle délimite ce qu'il faut fouiller (chaque étape laisse des traces à chercher) et elle relie chaque faiblesse à un contrôle à renforcer.

---

## 2. Détection et analyse

### Chronologie fictive

Les heures sont fictives. J désigne le jour où l'incident est découvert (un vendredi).

| Moment | Événement (fictif) | Décision ou action |
|---|---|---|
| J-9, nuit | Connexion VPN réussie avec le compte administrateur partagé, depuis une adresse inhabituelle (203.0.113.45, plage réservée à la documentation) | Non détectée : aucune alerte, aucun contrôle de ces connexions |
| J-9 à J-2 | L'intrus explore le réseau, crée un compte de service supplémentaire, copie des dossiers du serveur de fichiers du site B | Non détectée |
| J, 01h10 | Début du chiffrement des serveurs et des postes du site B | |
| J, 05h30 | À l'ouverture de l'entrepôt B, les terminaux de scan ne se connectent plus ; le chef d'équipe appelle le responsable informatique | Alerte humaine, pas technique |
| J, 05h50 | Constat d'une note de rançon sur un serveur, de fichiers renommés avec une extension inconnue | **Qualification** : incident de sécurité majeur (au sens de ISO 27002, mesure 5.25) |
| J, 06h10 | Le prestataire externe confirme à distance que le chiffrement est en cours | |

### Décision de 06h15 : confiner avant d'analyser

Deux options existaient : couper les machines (arrête le chiffrement, mais efface la mémoire vive et complique l'analyse) ou isoler le réseau sans éteindre (garde des traces, demande plus de rigueur). **Choix : isoler le site B** en coupant son lien vers le siège et en désactivant les ports des commutateurs, **sans éteindre** les machines. Les machines encore en cours de chiffrement sont arrêtées au cas par cas. La décision et son heure sont consignées.

### Indicateurs de compromission (fictifs et génériques)

| Type | Exemple fictif | Utilité |
|---|---|---|
| Adresse IP | 203.0.113.45 (plage de documentation) | Connexion VPN de l'intrus |
| Compte | `svc-backup2` (créé par l'intrus) | Persistance ; à rechercher dans l'annuaire |
| Extension de fichier | `.fx-locked` | Périmètre du chiffrement |
| Fichier | `LISEZ_MOI.txt` (note de rançon) | Confirmation et suivi du périmètre |
| Outil | Utilitaire d'archivage lancé la nuit sur le serveur de fichiers | Indice probable d'une copie de données |
| Domaine | `exemple-collecte.invalid` | Destination suspecte d'un transfert |

Ces valeurs sont inventées. Leur rôle est de montrer à quoi sert un indicateur : chercher le même signe sur les autres machines et dans les journaux.

### Sources de journaux exploitées (fictives)

| Source | Ce qu'on y cherche | Conservation (hypothèse) |
|---|---|---|
| Journaux du VPN | Connexions par compte, adresse source, horaires, durée | 30 jours |
| Journal de sécurité des contrôleurs de domaine | Ouvertures de session (événements 4624 et 4625), création de compte (4720), ajout à un groupe (4728, 4732, 4756), tickets Kerberos (4768, 4769) | 30 jours |
| Journaux des serveurs | Création de tâche planifiée (4698), installation de service (7045) | 30 jours |
| Pare-feu | Flux sortants volumineux, destinations inhabituelles | 30 jours |
| Serveur de fichiers | Lectures massives, renommages en série | 14 jours (hypothèse) |
| Console de sauvegarde | Arrêt ou suppression de tâches, accès à la console | 30 jours |
| Protection des postes (au siège seulement) | Alertes, processus inhabituels | 30 jours |

### Règles de détection (pseudo-code fictif et défensif)

Ces règles servent à la fois à retrouver l'intrusion après coup et à détecter la suivante. Les seuils sont des hypothèses à calibrer sur l'activité réelle.

```text
REGLE 1 - Connexion privilégiée atypique
  SI événement = connexion VPN
  ET compte appartient au groupe "administrateurs"
  ET (heure hors 07h-19h OU adresse source jamais vue pour ce compte)
  ALORS alerte (priorité haute)

REGLE 2 - Nouveau compte privilégié
  SI événement = création de compte (4720)
  ET aucune demande de changement associée
  OU ajout à un groupe privilégié dans l'heure qui suit (4728/4732)
  ALORS alerte (priorité haute)

REGLE 3 - Exécution distante planifiée
  SI événement = tâche planifiée créée (4698) OU service installé (7045)
  SUR un serveur
  ET compte auteur != compte d'exploitation habituel
  ALORS alerte (priorité moyenne)

REGLE 4 - Chiffrement de masse
  SI plus de N fichiers renommés avec une même nouvelle extension
  EN moins de 5 minutes SUR un partage
  ALORS alerte (priorité critique) ET isolement automatique du poste source

REGLE 5 - Volume sortant anormal
  SI volume sortant d'un serveur interne > S par heure
  VERS une destination jamais vue
  ALORS alerte (priorité haute)

REGLE 6 - Sabotage de sauvegarde
  SI arrêt ou suppression d'une tâche de sauvegarde
  OU connexion à la console de sauvegarde hors fenêtre habituelle
  ALORS alerte (priorité critique)
```

**Application à Fictilog :** les règles 1, 2 et 5 auraient fait remonter l'intrusion plusieurs jours avant le chiffrement, à condition que les journaux soient centralisés et regardés. C'est exactement ce qui manquait.

---

## 3. Confinement, éradication et reprise

### Chronologie de la journée J

| Heure | Action | Raison ou arbitrage |
|---|---|---|
| 06h40 | **Cellule de crise** réunie : direction, responsable informatique, prestataire, finance, référent données personnelles, une personne dédiée à la communication | Une seule voix pour les décisions et un journal tenu en continu |
| 07h30 | Désactivation du compte administrateur partagé, révocation des sessions, changement hors bande des mots de passe privilégiés | Couper l'accès de l'intrus ; le VPN est fermé le temps de l'analyse |
| 08h30 | **Périmètre** estimé : 3 serveurs et 22 postes du site B ; le siège semble épargné. La copie de sauvegarde en ligne est en partie chiffrée ; la copie hors ligne de dimanche est intacte | Cette copie conditionne toute la reprise |
| 09h30 | Copie des disques et des journaux des machines touchées, sur un support à part, avec un registre de manipulation | Préserver les preuves (mesure 5.28) |
| 10h00 | **Mode dégradé** : listes de préparation papier, expéditions du site B reportées au siège | Continuité minimale (5.29, 5.30) |
| 11h00 | **Rançon : pas de paiement ni de négociation** à ce stade. La direction décide avec son conseil juridique, en tenant compte de l'absence de garantie de récupération des données et du risque de financer d'autres attaques | Décision de direction, non technique |
| 14h00 | Information de l'assureur cyber (hypothèse : un contrat existe) ; **dépôt de plainte à prévoir** | Les conditions du contrat sont à vérifier |
| 16h00 | Recherche de persistance sur tout le parc : compte `svc-backup2` trouvé, une tâche planifiée supprimée | Éradication avant la reprise, sinon l'intrus revient |
| 18h00 | **Communication de crise** (voir plus bas) | |

### Jours suivants (hypothèses)

- **J+1 :** reconstruction des serveurs du site B à partir d'images saines, annuaire contrôlé, mots de passe de tous les comptes changés, authentification multifacteur imposée sur le VPN.
- **J+2 :** restauration des données depuis la copie hors ligne. Elle date de dimanche, donc quatre jours d'écritures sont à ressaisir à partir des bons papier et des échanges avec les clients.
- **J+3 :** reprise partielle de l'activité du site B, sous surveillance renforcée.
- **J+5 :** retour à la normale supposé ; maintien d'une surveillance accrue pendant plusieurs semaines.

**Ordre à retenir :** confiner, comprendre, éradiquer, puis restaurer. Restaurer trop tôt sur un réseau où l'intrus a encore un accès recommence l'incident.

### Détail technique de la réponse (fictif)

**Confinement**
1. **Isolation réseau :** blocage du lien du site B au niveau du pare-feu, puis désactivation des ports de commutateurs des machines touchées. Les machines ne sont pas éteintes, sauf celles qui chiffrent encore.
2. **Révocation des accès :** désactivation du compte partagé et du compte `svc-backup2`, fermeture des sessions ouvertes, blocage des comptes VPN du prestataire le temps de les revalider.
3. **Invalidation des tickets Kerberos :** comme le compte privilégié a pu servir à obtenir des tickets, le mot de passe du compte `krbtgt` est changé **deux fois**, avec un délai entre les deux pour laisser la réplication se faire (la manipulation est coordonnée avec le prestataire, car elle déconnecte des services).
4. **Rotation des secrets :** mots de passe des comptes de service, des administrateurs locaux, de la console de sauvegarde et des équipements réseau ; clés partagées et certificats VPN renouvelés. Tout est fait depuis un poste sain, hors bande.

**Préservation des preuves** (mesure 5.28)
- Ordre de collecte : ce qui disparaît vite d'abord (connexions et processus en cours, journaux), puis les disques.
- Copie des disques en lecture seule, empreinte de chaque copie (SHA-256), numérotation des supports.
- Registre de manipulation : qui, quand, quoi, où est le support. Les journaux du VPN et des contrôleurs de domaine sont exportés avant toute rotation automatique.

**Éradication**
- Suppression des comptes, tâches planifiées et services créés par l'intrus, relevés grâce aux indicateurs de compromission.
- Contrôle des stratégies de groupe, des éléments de démarrage et des appartenances aux groupes privilégiés.
- Chaque suppression est consignée pour être comparée à l'inventaire des actifs.

**Reconstruction depuis une sauvegarde saine**
- **Point d'attention :** la copie hors ligne de dimanche date de J-5, donc **après** le début de l'intrusion (J-9). Elle peut contenir des éléments de persistance.
- Décision : **les systèmes sont reconstruits à partir d'images propres, et seules les données sont restaurées**, après analyse de la copie dans un environnement isolé (recherche des indicateurs, contrôle des comptes et tâches présents).
- Ordre de reprise : annuaire, puis WMS, puis serveur de fichiers, puis postes.

**Vérification avant la reprise** (liste de contrôle)
- Aucun compte ni tâche inconnus dans l'annuaire et sur les serveurs.
- Mots de passe privilégiés tous renouvelés, multifacteur actif sur le VPN.
- Journaux centralisés et alertes minimales actives avant de rebrancher le site.
- Restauration testée sur un échantillon de données, cohérence vérifiée avec l'exploitation.
- Validation écrite du responsable informatique et du responsable du site B avant la remise en réseau.

---

## 4. Obligations de notification et communication

### Ce qui est à examiner

| Sujet | Cadre | Application à Fictilog (fictive) |
|---|---|---|
| Violation de données personnelles | RGPD, article 33 : notification à l'autorité de contrôle (en France, la CNIL) dans les 72 heures après en avoir pris connaissance, sauf si la violation n'engendre probablement pas de risque pour les personnes | Des dossiers de salariés et des données de conducteurs se trouvaient sur le serveur touché, et une copie est probable : **le risque est jugé non négligeable**, notification initiale à J+1 |
| Information des personnes | RGPD, article 34 : si le risque est élevé, communication aux personnes concernées | Évaluée à J+2 ; salariés informés, car des données d'identité et bancaires y figurent (hypothèse) |
| Registre des violations | RGPD, article 33, paragraphe 5 : toute violation est documentée, même non notifiée | Le journal de la cellule de crise sert de base |
| Notification en plusieurs temps | RGPD, article 33, paragraphe 4 : l'information peut être fournie par étapes | Première notification incomplète, complétée ensuite |
| NIS2 | **À vérifier** : l'application dépend du secteur, de la taille et du statut de l'entité, ainsi que de la transposition en droit français | Aucune conclusion ici : un entrepôt logistique n'est pas forcément dans le champ. Un contrôle du statut devrait précéder l'incident |
| Clauses contractuelles | Contrats clients, assurances | Obligations d'information à relire (hypothèse : délai de 48 heures chez un client) |

### Communication de crise

- **En interne :** message court aux équipes dès 08h00 (ce qui est arrivé, ce qu'il faut faire ou ne pas faire, qui contacter). Consigne : ne rien allumer, ne rien brancher, ne pas payer.
- **Vers les clients et transporteurs :** déclaration de base à 18h00, factuelle : incident informatique, site B ralenti, mesures en cours, point de contact unique. Pas de supposition sur la cause ni sur les données.
- **Vers les autorités et l'assureur :** par la personne désignée, avec les mêmes faits que ceux annoncés en interne.
- **Principe :** ne rien affirmer qui ne soit vérifié, mettre à jour à intervalle fixe, garder une seule version des faits.

---

## 5. Retour d'expérience

Une réunion est tenue environ trois semaines après l'incident (hypothèse), sans recherche de coupable : la question est « que change-t-on ? ».

### Constats et actions

| Constat | Action | Mesure ISO 27002:2022 |
|---|---|---|
| Un compte administrateur partagé, sans multifacteur, a ouvert la porte | Comptes nominatifs, comptes d'administration séparés, multifacteur sur VPN et sur les accès privilégiés | 5.15, 8.2, 8.5 |
| Neuf jours de présence de l'intrus sans détection | Centralisation des journaux, conservation allongée, alertes sur les connexions VPN hors horaires | 8.15, 8.16 |
| Alerte venue d'un chef d'équipe, pas d'un outil | Protection des postes et serveurs avec détection, y compris au site B ; procédure d'alerte 24 h/24 | 8.7, 6.8 |
| La sauvegarde hors ligne a sauvé la reprise, la copie en ligne était chiffrée | Sauvegardes inaltérables ou hors ligne plus fréquentes, test de restauration programmé | 8.13 |
| Plan de réponse jamais testé, contacts incomplets | Exercice de simulation annuel, liste de contacts tenue à jour (assureur, juridique, autorités) | 5.24, 5.5, 5.6 |
| Données personnelles sur un serveur partagé du site B | Cloisonnement du réseau et minimisation des données | 8.22 |
| Retour d'expérience | Rapport d'incident, mise à jour de l'analyse de risques et du plan | 5.27 |

### Indicateurs de suivi

Temps entre première activité de l'intrus et détection, temps entre détection et confinement, part des comptes à privilèges avec multifacteur, date et résultat du dernier test de restauration, nombre d'exercices de crise par an.

### Chiffrage fictif (hypothèses étiquetées)

Dans cette étude, l'arrêt partiel du site B a duré cinq jours ouvrés, et quatre jours de données ont été ressaisis. Ces durées sont des hypothèses de démonstration et ne représentent aucune statistique.

---

## 6. Rôles, responsabilités et droits de décision

### Niveaux de la cellule de crise (fictive)

| Niveau | Rôle | Membres |
|---|---|---|
| Décisionnel | Arbitre les décisions engageantes (arrêt d'un site, rançon, notification) | Direction générale, directeur administratif et financier, conseil juridique |
| Opérationnel | Mène le confinement, l'analyse et la reprise | Responsable informatique, prestataire externe, responsable du site B |
| Communication | Prépare et diffuse les messages, tient le journal | Responsable communication, référent données personnelles |

### Matrice RACI par phase

R : réalise. A : responsable final (un seul par ligne). C : consulté. I : informé.

| Phase | Direction | Resp. informatique | Prestataire | Référent données | DAF | Resp. site B | Communication |
|---|---|---|---|---|---|---|---|
| Préparation | A | R | C | C | C | C | I |
| Détection et qualification | I | A/R | R | I | I | C | I |
| Confinement | I | A | R | I | I | R | I |
| Éradication et reprise | I | A | R | C | I | R | I |
| Notification aux autorités | A | C | I | R | C | I | C |
| Communication de crise | A | C | I | C | C | C | R |
| Retour d'expérience | A | R | C | C | C | C | C |

### Qui peut décider quoi

| Décision | Qui décide | Condition |
|---|---|---|
| Isoler un segment ou des machines | Responsable informatique, ou prestataire sur délégation | Information immédiate de la direction |
| Couper un site entier | Direction générale | En cas de chiffrement actif et de direction injoignable, le responsable informatique peut agir, avec validation a posteriori sous 30 minutes (hypothèse) |
| Contacter l'assureur | Directeur administratif et financier | Dès la qualification ; les délais du contrat sont à vérifier |
| Notifier l'autorité de protection des données | Direction générale, sur proposition du référent données | Le référent prépare le dossier |
| Informer les clients | Direction générale, rédaction par la communication | Faits vérifiés uniquement |
| Payer ou négocier une rançon | Direction générale seule, avec conseil juridique | Non délégable |

---

## 7. Audit interne post-incident

L'audit interne mesure les écarts entre ce qui était prévu et ce qui existait. Il alimente la revue de l'ISO/IEC 27001:2022 (clause 9.2, audit interne ; clause 10.2, non-conformité et action corrective). Les constats ci-dessous sont fictifs.

| Réf. | Constat | Risque | Action corrective | Responsable | Échéance (hypothèse) | Suivi |
|---|---|---|---|---|---|---|
| F1 | Compte administrateur partagé, mot de passe ancien (5.15, 8.2) | Critique | Comptes nominatifs, comptes d'administration séparés, coffre de mots de passe | Responsable informatique | 1 mois | Fait ; revue trimestrielle des droits |
| F2 | Pas de multifacteur sur le VPN ni sur les accès privilégiés (8.5) | Critique | Multifacteur généralisé | Responsable informatique | 1 mois | Fait ; taux d'activation suivi |
| F3 | Journalisation non centralisée, conservation de 30 jours, pas de supervision de nuit (8.15, 8.16) | Élevé | Centralisation, conservation allongée, règles 1 à 6, supervision externalisée | Prestataire, validé par la direction | 3 mois | En cours |
| F4 | Réseaux peu cloisonnés entre siège et site B (8.22) | Élevé | Segmentation, filtrage des flux d'administration | Responsable informatique | 4 mois | À planifier |
| F5 | Sauvegarde en ligne accessible avec un compte du domaine (8.13) | Élevé | Compte dédié, copie immuable ou hors ligne plus fréquente | Responsable informatique | 2 mois | En cours |
| F6 | Plan de réponse non testé, contacts incomplets (5.24, 5.5, 5.6) | Moyen | Exercice annuel de simulation, liste de contacts à jour | Direction générale | 6 mois | À planifier |
| F7 | Aucune protection avec détection sur les serveurs du site B (8.7) | Élevé | Déploiement sur tous les serveurs et postes | Prestataire | 3 mois | En cours |

**Suivi :** un point mensuel en comité de direction jusqu'à la clôture de chaque constat, avec preuve de clôture (capture de configuration, rapport d'exercice). Un constat n'est clos qu'après vérification, pas à la déclaration de l'action.

---

## Ce que montre cette étude, et ses limites

**Compétences mobilisées :** structuration d'une réponse à incident selon NIST SP 800-61 et ISO/IEC 27035, tenue d'une chronologie, arbitrages de confinement, préservation des preuves, qualification des obligations de notification, communication de crise, retour d'expérience rattaché à l'ISO/IEC 27002:2022.

**Limites assumées :**
- Tout est fictif : l'entreprise, les systèmes, les indicateurs, les durées et les décisions. Aucun incident réel n'est décrit, et ce texte ne relate pas une mission.
- Les choix (isoler sans éteindre, ne pas payer) sont des décisions raisonnables dans ce scénario, pas des règles universelles : en situation réelle, elles dépendent des faits, du conseil juridique et de l'assureur.
- L'analyse juridique est volontairement partielle. L'article 33 du RGPD est cité à titre d'exemple ; l'applicabilité de NIS2 et les obligations propres à un secteur doivent être vérifiées auprès d'un juriste ou de l'autorité compétente.
- L'étude ne chiffre pas les coûts réels (perte d'exploitation, rançon, remise en état), ni le coût des mesures proposées.
- La technique est simplifiée : aucune analyse forensique détaillée.
- Les règles de détection sont du pseudo-code illustratif, sans produit ni langage de requête précis, et leurs seuils (N, S) sont à calibrer. Les numéros d'événements Windows sont cités à titre d'exemple et dépendent de la configuration d'audit.
- Les échéances, responsables, niveaux de criticité et délais de l'audit sont des hypothèses de démonstration.
