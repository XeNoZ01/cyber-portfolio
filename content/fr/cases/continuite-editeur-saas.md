+++
title = "Continuité et reprise d'activité d'un éditeur SaaS"
date = 2026-11-24
draft = true
description = "Étude de cas fictive : analyse d'impact (BIA), scénarios de sinistre, stratégies de reprise, plan de tests et indicateurs pour un éditeur SaaS fictif de 40 personnes."
case_study = "fictive"
translationKey = "continuite-saas"
+++

## Cadre de l'étude

**Organisation fictive : Brumaille Logiciels.** Éditeur d'une application de gestion de réservations en ligne (SaaS) pour des commerces de proximité, environ 40 personnes (14 en développement, 6 en exploitation et sécurité, 8 au support et à la réussite client, 12 en commercial, finance et direction). L'application est hébergée chez un fournisseur cloud (non nommé), dans une seule région, avec un abonnement mensuel facturé aux clients.

**Objectif :** montrer comment construire un plan de continuité et de reprise d'activité de bout en bout : analyse d'impact sur l'activité (BIA), scénarios, stratégies, plan de reprise, tests et indicateurs. La démarche s'inspire des principes de l'ISO 22301 (management de la continuité d'activité) et, côté systèmes d'information, de l'ISO/IEC 27031 (préparation des technologies de l'information et de la communication à la continuité). Les mesures sont rattachées à l'ISO/IEC 27002:2022.

**Ce qui est inventé :** l'entreprise, ses effectifs, son architecture, tous les chiffres (durées, volumes, coûts) et les résultats de tests. **Ce qui est réel :** la méthode, les notions (MTPD, RTO, RPO) et les mesures de référence. Cette étude est une démonstration construite exprès ; ce n'est pas une mission réalisée pour une organisation existante.

**Convention :** tous les chiffres ci-dessous sont des **hypothèses d'étude**, à valider avec la direction et les métiers dans un vrai contexte.

---

## 1. Rappel des notions

| Terme | Définition simple |
|---|---|
| **MTPD** (durée maximale d'interruption tolérable) | Au-delà, l'impact devient inacceptable pour l'organisation (perte de clients, sanctions, survie en jeu) |
| **RTO** (objectif de temps de reprise) | Délai visé pour rétablir le processus ; il doit être **inférieur** au MTPD |
| **RPO** (objectif de point de reprise) | Quantité maximale de données qu'on accepte de perdre, exprimée en temps (par exemple 15 minutes de données) |

Le RTO et le RPO sont des **objectifs** ; ils ne valent que s'ils sont démontrés par des tests (partie 6).

---

## 2. Analyse d'impact sur l'activité (BIA)

### Processus identifiés et criticité

| Réf. | Processus | Pourquoi il compte | Criticité |
|---|---|---|---|
| P1 | Service applicatif de réservation (production) | C'est le produit vendu ; les commerces prennent leurs rendez-vous par ce service | Critique |
| P2 | Authentification et API des clients | Sans elle, plus aucun accès, y compris pour les intégrations | Critique |
| P3 | Facturation et encaissement des abonnements | Source de trésorerie ; dépend d'un prestataire de paiement | Haute |
| P4 | Support client | Information des clients pendant un incident | Haute |
| P5 | Développement et déploiement | Correctifs et évolutions ; indispensable pour corriger, pas pour servir | Moyenne |
| P6 | Paie, comptabilité, RH | Obligations légales à échéance fixe | Moyenne |
| P7 | Messagerie et outils de collaboration internes | Coordination de la gestion de crise | Haute |

### Objectifs par processus (hypothèses)

| Réf. | MTPD | RTO visé | RPO visé | Impact principal si dépassé (hypothèse) |
|---|---|---|---|---|
| P1 | 24 h | 4 h | 15 min | Résiliations, pénalités contractuelles de disponibilité, réputation |
| P2 | 24 h | 2 h | 15 min | Idem P1 ; comptes inaccessibles |
| P3 | 5 jours | 48 h | 24 h | Retard d'encaissement, relances à refaire à la main |
| P4 | 8 h | 2 h | 1 h | Clients sans information, saturation des canaux alternatifs |
| P5 | 5 jours | 48 h | 4 h | Correctifs retardés |
| P6 | 10 jours | 5 jours | 24 h | Échéances sociales et fiscales |
| P7 | 4 h | 1 h | 1 h | Crise mal coordonnée |

**Lecture :** P1 et P2 portent l'essentiel de l'enjeu. P7 a le MTPD le plus court : si l'équipe ne peut pas se parler, aucun autre plan ne se déroule. D'où la décision de prévoir un canal de crise **indépendant** de l'infrastructure habituelle (partie 4).

### Dépendances

| Processus | Dépend de |
|---|---|
| P1, P2 | Fournisseur cloud (calcul, base de données, stockage), DNS, certificats, outil de déploiement |
| P3 | Prestataire de paiement, P1 (pour l'état des abonnements) |
| P4 | Outil de support en ligne, messagerie |
| P5 | Dépôt de code hébergé, chaîne de déploiement automatisée |

La dépendance principale est un **point de défaillance unique** : une seule région chez un seul fournisseur.

### Inventaire des actifs et dépendances

Chaque ligne a un propriétaire nommé (un rôle), qui tient l'inventaire à jour. La criticité est celle de l'actif **dans le sinistre**, pas en fonctionnement normal.

| Actif | Type | Propriétaire | Criticité | Processus liés (BIA) |
|---|---|---|---|---|
| Application web et API de réservation | Applicatif (conteneurs) | Responsable développement | Critique | P1, P2, P3, P4 |
| Service d'authentification des clients | Applicatif | Responsable développement | Critique | P2, P1 |
| Base de données principale (relationnelle, gérée) | Données | Responsable exploitation | Critique | P1, P2, P3 |
| Stockage d'objets (exports, pièces jointes) | Données | Responsable exploitation | Haute | P1, P3 |
| Réseau, répartiteur de charge, pare-feu applicatif | Infrastructure cloud | Responsable exploitation | Critique | P1, P2 |
| DNS (zone publique) et certificats TLS | Infrastructure | Responsable exploitation | Critique | P1, P2, P4 |
| Identité et authentification unique (SSO) des salariés, service tiers | Identité | RSSI | Haute | P4, P5, P6, P7 |
| Dépôt de code, chaîne CI/CD, registre d'images | Chaîne de livraison | Responsable développement | Moyenne en fonctionnement, **haute en reprise** | P5 ; reconstruction de P1 et P2 |
| Supervision et alertes | Exploitation | Responsable exploitation | Haute | P1, P2 ; déclenchement du plan |
| Coffre de secrets et clés de chiffrement | Sécurité | RSSI | Critique | P1, P2 ; reprise |
| Sauvegardes (compte dédié, copie hors fournisseur) | Données | Responsable exploitation | Critique | P1, P2, P3 |
| Prestataire de paiement | Prestataire | Responsable finance | Haute | P3 |
| Outil de support en ligne | Service tiers | Responsable support | Haute | P4 |
| Messagerie, collaboration et canal de crise | Service tiers et outil dédié | Responsable continuité | Haute | P7 |
| Personnes clés : 2 exploitants, 1 administrateur de base de données, 1 responsable sécurité, la direction technique | Humain | Direction | Critique | P1, P2, P5 |

**Lecture :**
- Le DNS, l'identité, la supervision et le coffre de secrets sont des **dépendances transverses** que l'on oublie souvent : leur panne bloque la reprise de tout le reste.
- Les actifs « de reprise » (CI/CD, coffre, runbook) pèsent peu en temps normal mais sont indispensables en sinistre ; leur criticité est donc réévaluée « en reprise ».
- Quatre personnes concentrent les compétences de reprise : c'est le risque traité par le scénario S4.

---

## 3. Scénarios de sinistre

| Réf. | Scénario | Processus touchés | Gravité (hypothèse) |
|---|---|---|---|
| S1 | Panne majeure du fournisseur cloud dans la région d'hébergement (plusieurs heures à plusieurs jours) | P1, P2, P3, P5 | Critique |
| S2 | Perte ou corruption de données (erreur d'exploitation, suppression, rançongiciel) | P1, P2, P3 | Critique |
| S3 | Indisponibilité du site principal : bureaux inaccessibles (incendie, dégât des eaux, coupure prolongée) | P4, P6, P7 | Moyenne |
| S4 | Absence de personnes clés (maladie, départ, indisponibilité simultanée de plusieurs exploitants) | P1, P2, P5 | Haute |

Les scénarios sont **volontairement indépendants de la cause** : on planifie la perte d'un moyen (le fournisseur, les données, les locaux, les personnes), pas chaque incident possible. Le scénario S2 recoupe la cybersécurité (ISO/IEC 27002:2022, 5.29 : sécurité de l'information pendant une perturbation), mais la reprise est la même quelle que soit la cause.

---

## 4. Stratégies de reprise

### S1 : panne du fournisseur cloud

| Option | Principe | Coût (hypothèse) | RTO atteignable | Décision |
|---|---|---|---|---|
| Attendre le rétablissement | Aucun investissement | 0 | Inconnu, dépend du fournisseur | Rejetée : RTO non maîtrisé |
| Sauvegardes dans une seconde région, reconstruction à la demande | Infrastructure décrite en code, données copiées en continu | Environ 600 €/mois | 6 à 10 h | Insuffisante pour P1 (RTO 4 h) |
| **Environnement de secours « tiède » dans une seconde région** | Réplication des données, ressources réduites, montée en charge à la bascule | Environ 2 500 €/mois | 2 à 4 h | **Retenue pour P1 et P2** |
| Actif-actif multi-régions | Deux régions en service permanent | Environ 7 000 €/mois | Quasi nul | Rejetée : disproportionnée pour 40 personnes (prématuré) |

Complément : un **mode dégradé** pour P1 en consultation seule (les clients voient leurs réservations, les créations sont suspendues) si la bascule complète prend plus de temps que prévu.

### S2 : perte de données

- **Sauvegarde 3-2-1** : 3 copies, 2 supports différents, 1 copie hors site et **immuable** (non modifiable pendant 30 jours), afin qu'une suppression ou un chiffrement malveillant ne l'atteigne pas (ISO/IEC 27002:2022, 8.13).
- **Journalisation continue** de la base de données pour retrouver n'importe quel instant des 7 derniers jours (point de reprise à la minute) ; sauvegardes complètes quotidiennes conservées 30 jours.
- **Comptes de sauvegarde séparés** de ceux de la production : un accès administrateur compromis ne doit pas permettre d'effacer les copies.
- **Restauration testée** : une sauvegarde jamais restaurée est une hypothèse, pas une garantie.

### S3 : site principal indisponible

Les outils sont déjà en ligne : le **télétravail** sert de solution de repli. À prévoir : matériel de secours (portables), liste des contacts hors annuaire d'entreprise, accès sécurisé à distance (authentification multifacteur) et un **canal de crise indépendant** (messagerie chiffrée tierce, hors messagerie d'entreprise), pour que P7 survive à une panne de l'infrastructure habituelle.

### S4 : absence de personnes clés

- **Binômes** sur chaque compétence critique (exploitation, base de données, sécurité) et rotation d'astreinte.
- **Documentation d'exploitation** à jour, stockée hors de l'infrastructure qu'elle sert à rétablir.
- **Coffre de secrets** avec accès d'urgence encadré (procédure « bris de glace », journalisée).
- Chaque personne clé identifiée dans la matrice des rôles a un suppléant nommé.

### Redondance (ISO/IEC 27002:2022, 8.14)

La seconde région, la duplication des services de base (DNS, authentification) et la réplication de la base répondent à la mesure sur la redondance des moyens de traitement. Le niveau retenu est « tiède », en cohérence avec les RTO du BIA.

### Architecture cible (hypothèses)

- **Région A (principale) :** trois zones de disponibilité. L'application tourne sur au moins deux zones, la base de données principale réplique de façon synchrone vers une zone voisine. La perte d'une zone est absorbée **sans bascule** (hors des scénarios de sinistre, c'est un incident courant).
- **Région B (secours « tiède ») :** réplique asynchrone de la base (retard cible inférieur à 60 secondes, alerte au-delà de 5 minutes), copie du stockage d'objets, images de conteneurs répliquées, capacité applicative réduite à 50 % mais déployable à la demande.
- **Services transverses hors région A :** zone DNS secondaire chez un second prestataire, supervision externe, page d'état, canal de crise, miroir du dépôt de code.
- **Principe :** ne pas dupliquer tout, mais s'assurer que **rien de ce qui sert à reconstruire** ne dépende de la région à reconstruire.

### Stratégie de sauvegarde

La fréquence de chaque sauvegarde est choisie **pour tenir le RPO**. Une sauvegarde complète quotidienne ne suffit pas pour un RPO de 15 minutes : on y ajoute l'archivage continu des journaux de transactions.

| Donnée | Mécanisme et fréquence | Rétention | RPO couvert | Emplacement |
|---|---|---|---|---|
| Base de données | Archivage continu des journaux de transactions (au plus toutes les 5 min) | 7 jours (reprise à un instant donné) | 5 à 15 min | Région A, réplique en région B |
| Base de données | Sauvegarde complète quotidienne | 30 jours | 24 h | Compte de sauvegarde dédié, région B, **immuable** 30 jours |
| Base de données | Export chiffré hebdomadaire | 90 jours | 7 jours | **Hors fournisseur** (stockage d'un second prestataire) |
| Stockage d'objets | Versionnage et réplication inter-régions | 30 jours de versions | 15 min | Régions A et B |
| Code et configuration (IaC) | Miroir quotidien | 90 jours | 24 h | Dépôt miroir hors fournisseur |
| Coffre de secrets | Export chiffré hebdomadaire ; copies papier scellées des clés racine | 90 jours | 7 jours | Hors ligne, deux lieux distincts |

**Chiffrement et séparation des clés :**
- Les sauvegardes sont chiffrées avec des clés **distinctes** de celles de la production, conservées dans un compte différent ; un compromis du compte de production ne donne ni l'accès aux copies ni la possibilité de les déchiffrer ou de les effacer.
- La copie hors fournisseur est chiffrée **avant l'envoi**, avec une clé que le second prestataire ne détient pas.
- La clé racine est partagée en 3 parts, dont 2 suffisent à la reconstituer (hypothèse) ; chaque part a un détenteur nommé et un suppléant. Perdre la clé revient à perdre la sauvegarde : l'accès à la clé fait donc partie des tests.

### Restauration de la base (reprise à un instant donné) et son test

1. Fixer l'**instant cible** : le dernier état sain connu, légèrement antérieur à l'incident.
2. Restaurer dans une **instance isolée**, jamais par-dessus la production.
3. Rejouer les journaux de transactions jusqu'à l'instant cible.
4. Contrôler l'intégrité : nombre de lignes par table comparé à une référence, contrôle de somme sur les tables critiques, cohérence des clés étrangères, présence des dernières réservations attendues.
5. Tester l'application en lecture seule sur la copie restaurée.
6. Décider de la promotion de la copie en base de production (décision du responsable de crise sur avis de l'exploitation).

**Test :** chaque mois, restauration d'un échantillon avec comptage ; chaque trimestre (exercice T2 du calendrier), reprise complète chronométrée sur la volumétrie réelle (hypothèse : 400 Go), avec relevé du temps de chaque phase.

### Infrastructure as code pour reconstruire

- Réseau, calcul, base de données, DNS, supervision et droits d'accès sont décrits en **code versionné**. Aucune modification manuelle en production ; une vérification hebdomadaire détecte les écarts entre le code et l'existant.
- L'état de l'infrastructure est stocké **hors de la région A**.
- Les modules sont paramétrés par région : reconstruire en région B revient à changer un paramètre, pas à réécrire.
- Le déploiement de secours est exécutable **depuis un poste d'exploitation**, sans la chaîne CI/CD (qui peut elle-même être en panne).
- Une reconstruction complète depuis zéro est testée une fois par an (durée visée : 2 h, hypothèse).

### Gestion des secrets et accès de secours (break-glass)

- Secrets injectés au démarrage depuis le coffre, jamais dans le code ni les images ; coffre répliqué en région B.
- **Deux comptes d'urgence**, hors SSO (qui peut être indisponible), protégés par une clé matérielle d'authentification multifacteur. Les mots de passe sont conservés dans des enveloppes scellées dans deux coffres physiques distincts.
- Toute utilisation déclenche une **alerte immédiate** à la cellule de crise et une entrée au journal ; les secrets concernés sont changés et l'usage est revu sous 24 h.
- Test semestriel, avec mesure du temps d'accès (cible : 20 minutes ou moins).

### Supervision et alertes de déclenchement

Des sondes externes, situées hors de la région A, rejouent chaque minute un **parcours de réservation synthétique**. La supervision et ses alertes sont elles-mêmes hébergées hors région A, avec un signal de « vie » : si la supervision se tait, on est alerté.

| Niveau | Condition (hypothèses) | Réaction |
|---|---|---|
| SEV3 | Taux d'erreur supérieur à 2 % sur 5 min, ou retard de réplication supérieur à 1 min | Astreinte, en heures ouvrées |
| SEV2 | Parcours synthétique en échec pendant 3 min depuis au moins 2 sondes ; ou retard d'archivage des journaux supérieur à 10 min ; ou échec de la sauvegarde quotidienne | Astreinte immédiate, ouverture de la cellule opérationnelle |
| SEV1 | P1 ou P2 indisponible depuis plus de 10 min, ou perte de données suspectée | Alerte du responsable de crise, évaluation du déclenchement du plan |

### Mode dégradé

| Niveau | Description | Condition d'entrée |
|---|---|---|
| N0 | Service normal | n/a |
| N1 : lecture seule | Consultation des réservations et agendas ; création et modification suspendues ; bandeau d'information | Base en cours de restauration ou retard de réplication inconnu |
| N2 : minimal | Page d'information statique ; envoi, **la veille**, d'un export des rendez-vous du lendemain à chaque commerce (généré chaque nuit et stocké hors région A) | Bascule impossible, ou RTO dépassé |

**Sortie du mode dégradé :** contrôle d'intégrité réussi et validation écrite du support. Le mode N2 ne rétablit pas le service, il évite que les commerces ne perdent la visibilité sur leurs rendez-vous.

---

## 5. Plan de reprise séquencé (scénario S1)

Exemple de déroulé pour une panne majeure de la région d'hébergement. Les durées sont des **cibles d'étude**.

| Étape | Durée cible (depuis la décision) | Action | Responsable |
|---|---|---|---|
| 0 | T0 | Détection (supervision externe, alertes clients) ; premier diagnostic | Astreinte exploitation |
| 1 | T0 + 15 min | Qualification : panne fournisseur confirmée sur sa page d'état ? Durée annoncée ? | Responsable exploitation |
| 2 | T0 + 30 min | **Décision de bascule** (critère : pas de rétablissement annoncé sous 1 h, ou durée inconnue) | Responsable de crise |
| 3 | T0 + 35 min | Ouverture du canal de crise ; message d'information à tous les salariés | Responsable de crise |
| 4 | T0 + 45 min | Message aux clients (page d'état hébergée hors de la région touchée) | Support / communication |
| 5 | T0 + 1 h | Bascule des accès : promotion de la réplique de base de données dans la région de secours | Exploitation |
| 6 | T0 + 2 h | Montée en charge des services applicatifs ; **P2 (authentification) d'abord**, puis P1 | Exploitation |
| 7 | T0 + 3 h | Bascule DNS vers la région de secours ; contrôle de bon fonctionnement (parcours de réservation de test) | Exploitation, développement |
| 8 | T0 + 3 h 30 | Validation métier : le support teste avec deux comptes clients fictifs | Support |
| 9 | T0 + 4 h | Annonce du retour du service ; passage en surveillance renforcée | Responsable de crise |
| 10 | Après sinistre | Retour vers la région d'origine **planifié**, hors urgence ; retour d'expérience sous 5 jours | Direction technique |

**Point critique :** la décision de bascule (étape 2) est la plus difficile. Attendre un rétablissement qui ne vient pas consomme le MTPD ; basculer trop tôt expose à une perte de données récentes et à une seconde opération risquée. D'où un **critère de décision écrit** et un décideur nommé, avec un suppléant.

**Pour le scénario S2 (perte de données) :** identification du dernier état sain, isolement de la cause, restauration dans un environnement séparé, contrôle d'intégrité, puis bascule. Le RTO visé est de 4 h pour P1, plus long si le périmètre de corruption est mal connu.

### Runbook détaillé (scénario S1)

Le tableau précédent donne les jalons cibles ; celui-ci détaille les actions, à partir de la décision de bascule, avec une durée estimée et la vérification qui autorise l'étape suivante. Durées : hypothèses d'étude.

| N° | Action | Durée estimée | Vérification (critère de passage) |
|---|---|---|---|
| 1 | Ouvrir la cellule opérationnelle, consigner l'heure de décision au journal de crise | 5 min | Journal ouvert, rôles confirmés |
| 2 | Geler les déploiements et les tâches planifiées qui écrivent | 5 min | Aucun déploiement ni tâche en cours |
| 3 | Vérifier la réplique en région B : retard de réplication et dernier point reçu | 5 min | Perte estimée connue ; si elle dépasse 15 min, le responsable de crise en est informé avant de poursuivre |
| 4 | Promouvoir la réplique en base principale | 10 min | Base en écriture, test d'écriture réussi |
| 5 | Déployer l'infrastructure applicative en région B par le code, depuis le dernier état validé | 25 min | Plan d'exécution sans erreur, ressources créées |
| 6 | Récupérer les secrets depuis la réplique du coffre | 10 min | Les services démarrent avec leurs identifiants |
| 7 | Démarrer le service d'authentification (P2) | 15 min | Connexion d'un compte de test réussie |
| 8 | Démarrer l'application et l'API (P1) à 50 % de capacité | 20 min | Contrôles de santé verts sur tous les nœuds |
| 9 | Lancer le parcours de test automatisé (connexion, création et annulation d'une réservation) | 10 min | Parcours de bout en bout réussi |
| 10 | Basculer le DNS (procédure ci-dessous) | 15 min | Résolution vers la région B depuis 3 points externes |
| 11 | Monter à 100 % de capacité et activer la supervision sur la région B | 20 min | Latence et taux d'erreur dans les seuils |
| 12 | Validation par le support (comptes fictifs), annonce du retour du service | 15 min | Validation écrite du responsable support |

**Total estimé : 2 h 35**, soit environ 3 h 05 depuis la détection en comptant les 30 minutes jusqu'à la décision : une marge d'environ 55 minutes sur le RTO de 4 h, volontairement conservée pour absorber les imprévus.

### Procédure de bascule DNS

- **Préparation permanente :** durée de vie (TTL) de 60 secondes sur les enregistrements d'accès ; zone secondaire chez un second prestataire ; enregistrements décrits en code.
- **Bascule :** 1) confirmer le bon état de la région B ; 2) appliquer le changement par le code (à défaut, par la console avec le compte d'urgence) ; 3) vérifier depuis 3 points externes et par interrogation directe des serveurs de noms faisant autorité ; 4) surveiller le trafic résiduel vers la région A (certains résolveurs ignorent le TTL).
- **Éviter le double fonctionnement :** passer la base de la région A en lecture seule ou l'isoler dès que possible, pour qu'aucune écriture ne soit acceptée des deux côtés.
- **Retour arrière :** si le contrôle de santé de la région B échoue, remettre l'ancien enregistrement, ce qui reste possible tant que la région A n'a pas été isolée.

---

## 6. Plan de tests et d'exercices

Un plan de continuité non testé est un document, pas une capacité. L'ISO/IEC 27002:2022 (5.30) demande de s'assurer de la préparation des TIC à la continuité, et cela passe par des tests.

### Calendrier annuel (hypothèse)

| Période | Exercice | Type | Périmètre |
|---|---|---|---|
| Chaque mois | Restauration d'un échantillon de sauvegarde, vérification d'intégrité | Technique | P1, P2 |
| T1 | Exercice sur table : panne du fournisseur cloud (S1) | Table-top | Cellule de crise, exploitation, support |
| T2 | Restauration complète de la base en environnement isolé, chronométrée (S2) | Technique | P1, P2, P3 |
| T3 | Exercice sur table : absence de personnes clés pendant un incident (S4) | Table-top | Exploitation, direction |
| T4 | **Bascule réelle** vers la région de secours, hors heures de pointe, avec information préalable des clients (S1) | Technique | P1, P2 |
| Annuel | Test du canal de crise et de l'arbre d'appel | Communication | Tous |
| Après changement majeur | Rejeu partiel du plan | Mixte | Selon le changement |

### Critères de réussite (hypothèses)

| Exercice | Critère |
|---|---|
| Restauration mensuelle | 100 % des échantillons restaurés, intégrité vérifiée, durée relevée |
| Restauration complète | RTO mesuré ≤ 4 h ; perte de données (RPO) mesurée ≤ 15 min |
| Bascule réelle | Service rétabli en région de secours en ≤ 4 h ; aucun incident de sécurité lié à la bascule |
| Exercice sur table | Décision de bascule prise en ≤ 30 min ; tous les rôles tenus ; au moins 3 écarts consignés |
| Arbre d'appel | 90 % des personnes joignables en 30 min |

### Suite donnée

Chaque exercice produit un compte rendu court : écarts constatés, actions, responsable, échéance. Un exercice qui échoue n'est pas un problème : c'est le but. Un exercice qui n'est jamais suivi d'actions en est un.

### Retour d'expérience d'un exercice fictif

Exemple de compte rendu (résultats **inventés** pour illustrer la méthode) : exercice sur table du scénario S1 (T1) suivi, le trimestre suivant, du test de restauration de la base (T2).

**Exercice sur table (S1) :** 9 participants, durée 2 heures, injection d'une panne régionale annoncée sans durée par le fournisseur.

| Mesure | Cible | Résultat | Verdict |
|---|---|---|---|
| Décision de bascule | 30 min | 52 min | Non atteint |
| Rôles tenus | Tous | Suppléant du responsable de crise sans accès au canal de crise | Partiel |
| Premier message client | 45 min | 40 min | Atteint |

**Test de restauration (S2) :** base de 400 Go, restauration à un instant cible 20 minutes avant un incident simulé.

| Mesure | Cible | Résultat | Verdict |
|---|---|---|---|
| Temps de restauration et contrôle d'intégrité (copie nominale) | 4 h | 3 h 15 | Atteint |
| Perte de données mesurée (RPO) | 15 min | 22 min | Non atteint |
| Restauration depuis la copie hors fournisseur | 12 h (dernier recours) | 4 h 50, dont 40 min pour retrouver la clé de déchiffrement | Atteint, avec écart |

| Réf. | Écart constaté | Cause | Action | Responsable | Échéance | Statut |
|---|---|---|---|---|---|---|
| E1 | Décision de bascule trop lente | Critère « pas de rétablissement annoncé » ambigu quand le fournisseur ne donne pas de durée | Réécrire le critère (seuil chiffré) et le faire valider | Responsable continuité | 1 mois | Fermée |
| E2 | Runbook introuvable par l'astreinte | Stocké dans un wiki hébergé dans la région touchée | Copie hors ligne et sur le poste d'exploitation, mise à jour trimestrielle | Responsable exploitation | 1 mois | Fermée |
| E3 | Suppléant sans accès au canal de crise | Comptes créés pour les titulaires seulement | Accès créés et testés pour tous les suppléants | Responsable continuité | 2 semaines | Fermée |
| E4 | Aucun message type pour une perte de données | Cas non prévu au départ | Rédiger et faire valider le message par le juridique | Responsable support | 2 mois | En cours |
| E5 | RPO mesuré à 22 min | Archivage des journaux configuré toutes les 15 min, plus délai de copie | Passer à 5 min ; alerte sur retard d'archivage (SEV2) | Responsable exploitation | 1 mois | Fermée |
| E6 | Clé de la copie hors fournisseur retrouvée en 40 min | Procédure d'accès à l'enveloppe scellée non documentée | Documenter, tester avec un second détenteur | RSSI | 2 mois | En cours |

Les écarts E1 à E3 relèvent de l'organisation, E5 et E6 de la technique : seul un exercice les aurait fait apparaître avant un vrai sinistre.

---

## 7. Rôles et communication

### Rôles

| Rôle | Mission en crise | Suppléant |
|---|---|---|
| Responsable de crise (direction générale) | Décide de la bascule, arbitre | Direction technique |
| Responsable exploitation | Pilote la reprise technique | Ingénieur d'exploitation senior |
| Responsable sécurité (RSSI à temps partagé) | Évalue la dimension sécurité, conservation des preuves | Responsable exploitation |
| Responsable support | Information et traitement des clients | Un conseiller senior |
| Responsable juridique et conformité (externe, à temps partiel) | Obligations contractuelles et réglementaires, notification | Direction générale |

### Communication

- **En interne :** canal de crise indépendant, point de situation toutes les 30 minutes pendant l'incident.
- **Vers les clients :** page d'état hébergée **hors** de l'infrastructure touchée ; messages types préparés (premier signalement, point d'étape, retour à la normale) ; ton factuel, sans promesse de délai non tenable.
- **Vers les autorités et partenaires :** si des données personnelles sont concernées, évaluation de l'obligation de notification (RGPD) avec le conseil juridique ; information du prestataire de paiement si P3 est touché.
- **Fournisseur cloud :** contact support de niveau supérieur, numéro de dossier consigné dès l'ouverture.

### Gouvernance et niveaux de responsabilité

| Niveau | Qui | Responsabilités | Rythme |
|---|---|---|---|
| Direction (sponsor) | Direction générale | Approuve la politique de continuité, les objectifs et le budget ; accepte les risques résiduels ; préside la revue de direction | Revue annuelle et après tout incident majeur |
| Responsable continuité | Personne nommée (environ 20 % de son temps, hypothèse) | Tient le BIA, le plan, le calendrier d'exercices et le suivi des actions ; ne décide pas du déclenchement | Continu |
| Cellule de crise stratégique | Direction générale, direction technique, juridique, responsable support | Décisions à enjeu : bascule, notification, communication externe, dépenses exceptionnelles | À l'activation |
| Cellule opérationnelle | Responsable exploitation, développeur référent, administrateur de base de données, RSSI, support | Exécute le runbook, diagnostique, rend compte à la cellule stratégique toutes les 30 min | À l'activation |
| Responsables de processus | Propriétaires de P1 à P7 | Valident leurs MTPD, RTO et RPO ; participent aux tests ; valident la reprise métier | Annuel et exercices |
| Équipes techniques | Exploitation, développement | Maintiennent runbooks, code d'infrastructure, sauvegardes, supervision ; ouvrent des actions | Continu |
| Prestataires | Fournisseur cloud, DNS secondaire, paiement, support | Engagements de service et contacts d'escalade au contrat ; participation à un exercice quand le contrat le permet | Revue annuelle des contrats |

### Matrice RACI par scénario

R : réalise ; A : approuve (décide) ; C : consulté ; I : informé ; « - » : non concerné. Une ligne a une seule lettre A, parfois combinée à R.

| Scénario et activité | Direction générale | Resp. continuité | Resp. exploitation | RSSI | Resp. support | Juridique |
|---|---|---|---|---|---|---|
| S1 : décision de bascule | A | C | R | C | I | I |
| S1 : communication aux clients | A | C | C | I | R | C |
| S2 : choix du point de restauration | I | I | A/R | C | I | I |
| S2 : notification des données personnelles | A | I | C | C | I | R |
| S3 : activation du repli en télétravail | I | A | R | C | C | - |
| S3 : communication interne | A | R | I | I | I | - |
| S4 : appel aux suppléants | I | A/R | C | C | I | - |
| S4 : recours à un renfort externe | A | C | R | C | I | C |

### Chaîne et critères de déclenchement

| Étape | Qui décide | Seuil (hypothèses) | Suppléant |
|---|---|---|---|
| Qualification d'incident majeur | Astreinte exploitation | SEV1, ou SEV2 non résolu en 15 min | Astreinte secondaire |
| Ouverture de la cellule opérationnelle | Responsable exploitation | Incident majeur qualifié | Administrateur de base de données |
| Déclenchement du plan de reprise (bascule ou restauration) | Responsable de crise (direction générale), sur recommandation du responsable exploitation | P1 ou P2 indisponible depuis 30 min sans cause corrigeable en 1 h ; ou fournisseur sans rétablissement annoncé sous 1 h ; ou perte de données confirmée | Direction technique |
| Dernier recours | Responsable exploitation | Direction générale et suppléant injoignables pendant 20 min, et P1 ou P2 indisponible depuis plus de 1 h | RSSI |

**Délégations écrites (hypothèses) :** le responsable exploitation peut engager jusqu'à 10 000 € de dépenses de reprise sans accord préalable ; le responsable support publie les messages types sans validation supplémentaire ; seule la direction générale ou le juridique notifie une autorité.

---

## 8. Indicateurs de suivi

| Indicateur | Cible (hypothèse) |
|---|---|
| Âge de la dernière sauvegarde restaurée avec succès | ≤ 31 jours |
| RTO mesuré au dernier exercice / RTO visé | ≤ 100 % |
| RPO mesuré au dernier exercice / RPO visé | ≤ 100 % |
| Part des exercices annuels réalisés | 100 % |
| Actions issues des exercices closes dans les délais | ≥ 80 % |
| Couverture des suppléants sur les rôles clés | 100 % |
| Documentation de reprise revue il y a moins de 6 mois | 100 % |
| Ancienneté de la dernière revue du BIA | ≤ 12 mois |

**Revue :** le BIA et le plan sont revus tous les 12 mois, et à chaque changement important (nouveau fournisseur, nouveau produit, hausse notable de la clientèle, incident majeur).

---

## 9. Ordre de grandeur des coûts (hypothèses)

| Poste | Coût mensuel estimé |
|---|---|
| Environnement de secours « tiède » | Environ 2 500 € |
| Stockage de sauvegardes hors site et immuable | Environ 300 € |
| Page d'état et canal de crise hors infrastructure | Environ 100 € |
| Temps d'exercice (environ 6 jours par an et par personne impliquée dans la reprise) | Coût interne, non facturé |

Au total, environ 2 900 € par mois en hypothèse, à comparer avec le coût d'une interruption de 24 heures (résiliations, pénalités, image), qu'une vraie étude devrait chiffrer avec la direction.

---

## 10. Audit interne du dispositif

Les exercices vérifient que le plan fonctionne ; l'audit interne vérifie que **le dispositif** (politique, objectifs, preuves, suivi) est appliqué et efficace. Les constats ci-dessous sont **fictifs**.

### Programme et périmètre

- **Fréquence :** un audit par an, avec un cycle de trois ans pour couvrir tous les processus ; cette année : P1, P2, P3, P4, P7 et les mécanismes de sauvegarde, de restauration et de bascule.
- **Indépendance :** auditeur n'ayant pas participé à la construction du plan (un pair d'une autre équipe, ou un prestataire externe).
- **Méthode :** la conduite suit les lignes directrices de l'ISO 19011 pour l'audit des systèmes de management, adaptées à une petite structure.

### Critères

- La politique de continuité de l'organisation et les objectifs RTO/RPO du BIA.
- Le plan, le calendrier d'exercices et les rôles définis.
- Les mesures 5.29, 5.30, 8.13 et 8.14 de l'ISO/IEC 27002:2022.
- Les principes de l'ISO 22301 (analyse d'impact, stratégies proportionnées, exercices, amélioration continue), sans en auditer les exigences une à une.

### Méthode

1. **Revue documentaire :** politique, BIA, plan, runbooks, comptes rendus d'exercices.
2. **Entretiens :** direction, responsable continuité, exploitation, support.
3. **Observation d'un exercice :** présence lors d'un exercice sur table.
4. **Vérification de preuves :** journaux de restauration, rapports de sauvegarde, historique des alertes, comptes de secours.

**Classification :** *écart majeur* (objectif critique non démontré ou exigence du dispositif absente) ; *écart mineur* (application partielle, sans effet immédiat sur un objectif) ; *point sensible* (piste d'amélioration, pas un écart).

### Constats

| Réf. | Classe | Constat | Preuve | Risque | Action corrective | Resp. | Échéance |
|---|---|---|---|---|---|---|---|
| A1 | Majeur | RTO de 4 h pour P1 non démontré de bout en bout : seule la base a été chronométrée, aucune bascule réelle | Absence de rapport de bascule | Surprise en situation réelle | Réaliser l'exercice T4 (bascule réelle) et publier le rapport | Resp. exploitation | 3 mois |
| A2 | Majeur | Aucune revue de direction de la continuité cette année | Pas de compte rendu | Objectifs, budget et risques résiduels non arbitrés | Tenir la revue, la formaliser, la planifier chaque année | Direction générale | 1 mois |
| A3 | Mineur | RPO mesuré à 22 min contre 15 min visées ; correctif appliqué mais non revérifié | Rapport du test T2, historique de configuration | Perte de données supérieure à l'objectif | Mesurer à nouveau lors du prochain test et conserver la preuve | Resp. exploitation | 3 mois |
| A4 | Mineur | Runbook mis à jour il y a 8 mois, alors que la règle est de 3 mois | Date de dernière modification | Procédure obsolète | Rappel automatique de revue trimestrielle | Resp. continuité | 1 mois |
| A5 | Mineur | Couverture des suppléants à 85 % : le suppléant du RSSI absent du dernier exercice | Liste des présents | Rôle non tenu en crise | Former et inclure le suppléant au prochain exercice | RSSI | 2 mois |
| A6 | Mineur | BIA non revu depuis 14 mois (cible : 12 mois) | Date de la dernière version | Objectifs décalés par rapport à l'activité | Revue du BIA et validation par les responsables de processus | Resp. continuité | 2 mois |
| A7 | Point sensible | Aucune alternative documentée si le prestataire de paiement est indisponible (P3) | Plan et entretien | Retard d'encaissement prolongé | Étudier un second prestataire ou un mode d'encaissement manuel | Resp. finance | 6 mois |
| A8 | Point sensible | Test des comptes d'urgence limité à un seul des deux | Journal des tests | Second compte défaillant au moment critique | Tester les deux comptes à chaque cycle | RSSI | 2 mois |
| A9 | Point sensible | Coût de l'environnement de secours jamais rapproché d'un coût d'interruption chiffré | Entretien avec la direction | Dimensionnement non justifié | Estimer le coût d'une interruption de 24 h et réexaminer le niveau « tiède » | Direction générale | 4 mois |

### Suivi et revue de direction

- **Suivi :** les actions sont consignées dans un plan unique, suivi mensuel par le responsable continuité ; la clôture d'un écart majeur est **vérifiée par l'auditeur**, pas seulement déclarée.
- **Revue de direction (annuelle) :** entrées : résultats des exercices, indicateurs, constats d'audit, changements d'activité ou de fournisseurs, incidents ; sorties : décisions sur les objectifs RTO/RPO, ressources, niveau de la stratégie de reprise, risques résiduels acceptés.
- **Amélioration :** un écart répété d'une année sur l'autre est remonté à la direction comme défaut du dispositif, pas comme simple oubli.

---

## Ce que montre cette étude et ses limites

**Compétences mobilisées :** analyse d'impact sur l'activité, définition de MTPD, RTO et RPO cohérents, scénarios de sinistre, arbitrage coût et risque entre stratégies, plan de reprise séquencé avec critère de décision, plan de tests avec critères de réussite, rôles, communication de crise, indicateurs, rattachement aux mesures 5.29, 5.30, 8.13 et 8.14 de l'ISO/IEC 27002:2022.

**Limites assumées :**
- L'organisation, l'architecture, les durées, les coûts et les résultats attendus sont des **hypothèses d'étude**, sans audit ni mesure réelle.
- Les RTO et RPO d'un vrai contexte se négocient avec les métiers et la direction ; ils se démontrent par des tests, que cette étude ne peut que planifier.
- L'étude s'inspire des principes de l'ISO 22301 et de l'ISO/IEC 27031 sans prétendre démontrer une conformité à ces normes ni détailler leurs exigences.
- Le plan n'aborde pas en profondeur les aspects contractuels (clauses de disponibilité du fournisseur, assurances) ni la dépendance au prestataire de paiement.
- Les retours d'exercice et les constats d'audit sont **fictifs**, écrits pour illustrer la méthode ; aucun exercice ni audit n'a réellement eu lieu, et les critères de réussite sont des cibles.
- Les détails techniques (volumes, durées, seuils d'alerte, architecture) sont des hypothèses cohérentes entre elles, non éprouvées sur un système réel.
- Le plan est volontairement proportionné à une structure de 40 personnes ; pour une organisation plus grande, la gouvernance et les niveaux d'exercice seraient plus lourds.
