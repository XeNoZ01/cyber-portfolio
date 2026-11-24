+++
title = "Mise en conformité RGPD d'un centre de santé"
date = 2026-11-24
draft = true
description = "Démarche de conformité sur une organisation fictive de 30 personnes : registre des traitements, sous-traitants, droits des personnes, analyse d'impact, sécurité et gestion d'une violation de données."
case_study = "fictive"
translationKey = "rgpd-centre-sante"
+++

## Cadre de l'étude

**Organisation fictive : le centre de santé Les Alisiers.** Centre de santé de proximité d'environ 30 personnes (6 médecins, 12 infirmiers et paramédicaux, 6 secrétaires médicales et agents d'accueil, 4 personnes d'administration et de direction, 2 personnes pour la logistique et le technique), qui suit environ 6 000 patients actifs (hypothèse). Le nom, les effectifs et les chiffres sont inventés.

**Objectif :** montrer la démarche d'une première mise en conformité au règlement général sur la protection des données (RGPD) : cartographier les traitements, vérifier les bases légales et les durées de conservation, encadrer les sous-traitants, organiser les droits des personnes, réaliser une analyse d'impact sur un traitement à risque, relier la sécurité à l'ISO/IEC 27002:2022 et savoir réagir à une violation de données.

**Ce qui est inventé :** le centre, ses outils, ses prestataires, les chiffres et l'incident. **Ce qui est réel :** les articles du RGPD cités et les mesures de référence de l'ISO/IEC 27002:2022.

**Ce que ce n'est pas :** une mission vécue, ni un conseil juridique. Les données de santé relèvent aussi de règles propres au secteur (secret médical, dossier du patient, hébergement des données de santé) qui ne sont qu'effleurées ici.

---

## 1. Cartographier les traitements (art. 30)

L'article 30 demande un **registre des activités de traitement**. Pour chaque traitement : finalité, catégories de personnes et de données, destinataires, durée de conservation et description générale des mesures de sécurité. La première étape est d'interroger chaque métier (accueil, soins, facturation, RH, technique) sur ce qu'il fait réellement des données.

Les durées ci-dessous sont des **hypothèses d'étude** : en situation réelle, elles se confirment auprès des règles sectorielles et du conseil juridique.

| Réf. | Traitement | Finalité | Données | Base légale | Conservation (hypothèse) |
|---|---|---|---|---|---|
| T1 | Dossier patient informatisé | Suivi médical et soins | Identité, antécédents, comptes rendus, prescriptions (**santé**) | Art. 6(1)(b) ou (c) selon le cas ; art. 9(2)(h) | 20 ans après le dernier contact |
| T2 | Prise de rendez-vous en ligne | Planifier les consultations | Identité, coordonnées, spécialité demandée (**révèle de la santé**) | Art. 6(1)(b) ; art. 9(2)(h) | Rendez-vous passés : 12 mois |
| T3 | Rappels de rendez-vous par SMS | Réduire les absences | Nom, téléphone, date du rendez-vous | Art. 6(1)(a) : consentement | Jusqu'au retrait du consentement |
| T4 | Facturation et tiers payant | Facturer, être payé | Identité, actes, informations d'assurance | Art. 6(1)(b) et (c) ; art. 9(2)(h) | 10 ans (pièces comptables) |
| T5 | Gestion du personnel et paie | Contrats, paie, planning | Identité, coordonnées bancaires, absences | Art. 6(1)(b) et (c) | Durée du contrat, puis archivage légal |
| T6 | Vidéosurveillance de l'accueil | Sécurité des personnes et des locaux | Images | Art. 6(1)(f) : intérêt légitime | 30 jours maximum |

### Lecture critique

- **T1, T2 et T4 touchent des données de santé** : l'article 9(1) les interdit par principe, sauf exception. Ici, l'exception de l'article 9(2)(h) (médecine préventive, diagnostic, soins, par un professionnel tenu au secret, art. 9(3)) fonde le traitement. Le consentement n'est **pas** la bonne base pour les soins.
- **T3 est le seul traitement fondé sur le consentement** : il doit être libre, éclairé et retirable à tout moment (art. 7). Le refus ne doit pas empêcher d'être soigné. Un SMS qui nommerait la spécialité serait en outre excessif.
- **T6 repose sur l'intérêt légitime** (art. 6(1)(f)) : il suppose une pondération écrite avec les droits des personnes et une information claire à l'entrée (art. 13).
- **Principe de limitation de la conservation** (art. 5(1)(e)) : au départ, le centre n'avait aucune durée définie. Le registre force à les décider, puis à organiser la purge (mesure ISO 27002 : 8.10, suppression d'informations).

### Besoin de délégué à la protection des données (DPO) ?

L'article 37(1)(c) impose un DPO en cas de traitement **à grande échelle** de catégories particulières de données. Un centre de 6 000 patients se discute : c'est une **question à trancher et à documenter**, pas une évidence. Hypothèse d'étude : le centre désigne un référent interne et étudie l'option d'un DPO externe mutualisé.

### Inventaire des actifs d'information

Le registre dit **ce que** l'on fait des données ; l'inventaire dit **où elles vivent** (applications, machines, supports, papier, prestataires). Sans lui, on ne sait ni où appliquer les mesures de sécurité ni où chercher en cas de demande de droits ou de violation. Référence : ISO 27002:2022, 5.9 (inventaire des informations et actifs associés), 5.12 et 5.13 (classification et étiquetage).

**Classification retenue (hypothèse) :** C1 Public, C2 Interne, C3 Confidentiel, C4 Santé (données de santé et données qui en révèlent). **Besoins D/I/C** notés de 1 à 4 : **D**isponibilité (4 = interruption tolérée moins de 4 h), **I**ntégrité (4 = une altération peut nuire à la santé du patient), **C**onfidentialité (4 = une divulgation porte une atteinte grave).

| Réf. | Actif | Propriétaire | Localisation | Données | Classif. | D/I/C | Traitements |
|---|---|---|---|---|---|---|---|
| A1 | Logiciel de dossier patient (service en ligne) | Médecin coordonnateur | Centre de données de l'éditeur (lieu à confirmer) | Dossiers, prescriptions | C4 | 4/4/4 | T1, T4 |
| A2 | Plateforme de rendez-vous en ligne | Responsable de l'accueil | Cloud du prestataire (à confirmer) | Identité, spécialité demandée | C4 | 3/3/3 | T2, T3 |
| A3 | Messagerie professionnelle | Responsable IT | Service en ligne | Échanges, pièces jointes pouvant contenir de la santé | C4 | 3/3/4 | T1, T2, T4, T5 |
| A4 | Serveur de fichiers local | Responsable IT | Local technique | Documents administratifs, exports, RH | C3 (peut contenir du C4) | 3/3/3 | T4, T5 |
| A5 | Postes fixes (18, hypothèse) | Responsable IT | Cabinets de consultation, accueil | Accès à A1 à A3, fichiers temporaires | C4 | 2/3/4 | T1 à T4 |
| A6 | Portables (8, hypothèse) | Responsable IT | Nomades (visites, domicile) | Idem + copies locales possibles | C4 | 2/3/4 | T1, T2 |
| A7 | Sauvegardes (NAS local + copie externalisée) | Responsable IT | Local technique + hébergeur de sauvegarde | Copies de A4, et de A1 selon l'éditeur | C4 | 3/4/4 | T1, T4, T5 |
| A8 | Dossiers papier et archives | Secrétariat médical | Local d'archives fermé | Anciens dossiers, courriers, pièces comptables | C4 | 2/3/4 | T1, T4, T5 |
| A9 | Copieur-scanner multifonction | Responsable IT | Secrétariat | Documents numérisés, disque interne | C4 | 1/2/3 | T1, T4, T5 |
| A10 | Enregistreur de vidéosurveillance | Direction | Local technique | Images | C3 | 2/2/3 | T6 |
| A11 | Logiciel de paie (cabinet externe) | Responsable RH | Chez le prestataire | Données RH et bancaires | C3 | 2/4/3 | T5 |
| A12 | Supports amovibles (clés USB) | Responsable IT | Dispersés | Copies ponctuelles | C4 | 1/2/4 | T1, T4 |

**Lecture critique :**
- Le **copieur-scanner (A9)** et les **clés USB (A12)** sont les actifs que l'on oublie : le premier garde des copies sur son disque, les secondes sortent du centre sans contrôle. Mesures : chiffrement ou effacement du disque en fin de location ou de vie (ISO 27002 : 7.14, mise au rebut sécurisée), interdiction des supports non chiffrés (7.10, supports de stockage).
- Chaque traitement du registre doit pointer vers au moins un actif, et chaque actif C4 vers un propriétaire nommé : c'est lui qui valide les droits d'accès et la revue annuelle.
- **A1 et A7 cumulent les besoins les plus élevés** : ils concentrent l'AIPD (section 4) et les mesures de sauvegarde (section 5).

---

## 2. Encadrer les sous-traitants (art. 28)

Un sous-traitant traite des données **pour le compte** du centre, qui reste responsable de traitement. L'article 28 impose un **contrat** (ou autre acte juridique) et des garanties suffisantes du prestataire.

| Prestataire (fictif) | Rôle | Traitements concernés | Point d'attention |
|---|---|---|---|
| Éditeur du logiciel de dossier patient | Hébergement et maintenance | T1, T4 | Hébergement des données de santé : exigence propre au droit français de la santé, à faire vérifier |
| Plateforme de rendez-vous en ligne | Collecte et stockage | T2 | Localisation des données, sous-traitants ultérieurs |
| Prestataire de SMS | Envoi des rappels | T3 | Pas de contenu médical dans le message |
| Société de maintenance informatique | Accès à distance aux postes | Tous | Accès privilégié : journalisation et accès limités |
| Cabinet de paie externe | Production des bulletins | T5 | Contrat et confidentialité |

### Contenu minimal du contrat (art. 28(3))

Objet, durée, nature et finalité du traitement, types de données et de personnes ; **instructions documentées** du centre ; engagement de confidentialité du personnel du prestataire ; mesures de sécurité (art. 32) ; conditions de recours à un **sous-traitant ultérieur** ; aide pour répondre aux demandes de droits et aux violations ; **suppression ou restitution** des données en fin de contrat ; mise à disposition des informations nécessaires aux audits.

### Faits constatés (hypothèses d'étude)

Sur les 5 prestataires, 2 ont un contrat qui mentionne la protection des données, 3 n'ont rien. Action : demander l'avenant de sous-traitance, et à défaut, envisager un changement de prestataire. Référence ISO 27002 : 5.19 à 5.23 (relations avec les fournisseurs, accords, services cloud).

---

## 3. Organiser les droits des personnes (art. 12 à 22)

Les patients disposent notamment des droits d'**accès** (art. 15), de **rectification** (art. 16), d'**effacement** (art. 17), de **limitation** (art. 18), d'**opposition** (art. 21), et, selon la base légale, de **portabilité** (art. 20). L'information préalable relève des articles 13 et 14.

### Procédure de réponse retenue

1. **Réception** : un point d'entrée unique (adresse dédiée et accueil), sans formalisme imposé. Date de réception enregistrée dans un registre des demandes.
2. **Vérification d'identité** : proportionnée, pour ne pas communiquer des données de santé à un tiers.
3. **Qualification** : quel droit, quels traitements, quelles exceptions (par exemple, l'effacement n'est pas absolu : l'article 17(3) en prévoit, notamment une obligation légale de conservation).
4. **Instruction** avec le médecin concerné pour tout ce qui touche au dossier médical : l'accès au dossier suit aussi les règles propres au secteur de la santé, à vérifier avec un conseil juridique.
5. **Réponse dans un délai d'un mois** à compter de la réception (art. 12(3)). Ce délai est prorogeable de deux mois si la demande est complexe ou nombreuse, à condition d'en informer la personne **dans le premier mois**, en expliquant pourquoi.
6. **Réponse écrite, gratuite** (en principe), et traçabilité de la décision, y compris en cas de refus motivé.

**Indicateur :** délai moyen de réponse, nombre de demandes en retard.

---

## 4. Analyse d'impact (AIPD, art. 35) sur un traitement à risque

### Pourquoi une AIPD, et pourquoi sur T1

L'article 35 exige une AIPD quand un traitement est **susceptible d'engendrer un risque élevé** pour les droits et libertés. L'article 35(3)(b) cite le traitement à grande échelle de catégories particulières de données. Les lignes directrices européennes sur l'AIPD (WP248, reprises par le Comité européen de la protection des données) listent aussi des critères combinables : données sensibles, personnes vulnérables, volume, etc.

Le traitement **T1 (dossier patient)** combine ici : données de santé, personnes en situation de vulnérabilité, accès par de nombreux professionnels, et un **projet de migration vers un logiciel hébergé en ligne** (changement important). Il est retenu pour l'AIPD.

### Méthode simple retenue

Le risque s'évalue **pour les personnes concernées**, et non pour le centre. Contenu minimal selon l'article 35(7) : description du traitement, nécessité et proportionnalité, risques, mesures.

**Échelles** (4 niveaux, sur le modèle de l'autre étude de cas) : gravité pour la personne de 1 (négligeable) à 4 (maximale, atteinte durable à la vie privée ou à la santé) ; vraisemblance de 1 (improbable) à 4 (quasi certain). **Niveau = gravité × vraisemblance.**

### Nécessité et proportionnalité

| Question | Réponse (hypothèse d'étude) |
|---|---|
| Finalité déterminée et légitime ? | Oui : soins et suivi médical |
| Données limitées au nécessaire ? | À revoir : champs libres du logiciel, antécédents non utiles à l'équipe d'accueil |
| Droit d'accès par profil ? | À mettre en place : l'accueil ne voit pas les comptes rendus |
| Information des patients ? | Affichage et livret d'accueil à compléter (art. 13) |

### Événements redoutés

| Réf. | Événement redouté | Gravité | Vraisemblance (avant) | Niveau avant | Mesures | Niveau après |
|---|---|---|---|---|---|---|
| E1 | **Accès illégitime** : un compte compromis ou un collègue curieux consulte des dossiers | 4 | 3 | 12 | MFA, profils d'accès, journalisation et revue des accès | 4 (4 × 1) |
| E2 | **Modification non désirée** : une erreur ou une attaque altère un dossier (allergie effacée) | 4 | 2 | 8 | Droits d'écriture limités, historique des modifications, sauvegardes | 4 (4 × 1) |
| E3 | **Disparition** : indisponibilité ou perte du dossier lors d'une consultation | 3 | 2 | 6 | Sauvegardes testées, mode dégradé papier, engagement de disponibilité du prestataire | 3 (3 × 1) |

### Conclusion de l'AIPD (fictive)

Le risque **résiduel** est ramené à un niveau acceptable **si** les mesures sont réellement en place avant la migration. L'article 36 prévoit une consultation préalable de l'autorité de contrôle si un risque élevé subsiste malgré les mesures : ce n'est pas le cas dans cette hypothèse. L'AIPD est **vivante** : elle se révise à chaque changement important.

---

## 5. Sécurité des traitements (art. 32) et ISO/IEC 27002:2022

L'article 32 ne fournit pas de liste fermée : il demande des mesures **appropriées au risque**, citant par exemple la pseudonymisation et le chiffrement, la capacité à garantir la confidentialité, l'intégrité, la disponibilité et la résilience, la capacité à rétablir l'accès aux données, et des **tests réguliers**. L'ISO/IEC 27002:2022 fournit un catalogue de mesures pour structurer la réponse.

| Besoin (art. 32) | Mesure | Réf. ISO 27002:2022 | Priorité |
|---|---|---|---|
| Confidentialité : accès | Profils par métier, MFA sur le dossier patient et la messagerie | 5.15, 5.18, 8.5 | Haute |
| Confidentialité : appareils | Chiffrement des disques des portables, verrouillage de session | 8.24, 7.7 | Haute |
| Disponibilité, rétablissement | Sauvegarde hors site, test de restauration régulier | 8.13 | Haute |
| Intégrité, détection | Journalisation des accès au dossier patient, revue périodique | 8.15 | Moyenne |
| Sensibilisation du personnel | Formation courte annuelle, rappels sur les envois de données | 6.3 | Haute |
| Vulnérabilités, logiciels malveillants | Mises à jour automatiques, protection des postes | 8.7, 8.8 | Moyenne |
| Fournisseurs | Contrats et suivi des sous-traitants | 5.19 à 5.23 | Haute |
| Fuite de données | Règles d'envoi, partage sécurisé plutôt que pièce jointe | 8.12, 5.14 | Moyenne |
| Gestion des incidents | Procédure de violation de données, registre | 5.24 à 5.27 | Haute |
| Protection de la vie privée | Politique et rôles pour les données personnelles | 5.34 | Moyenne |

**Remarque :** l'ISO 27002 est un référentiel de bonnes pratiques de sécurité de l'information. Appliquer ses mesures ne vaut pas conformité au RGPD, qui impose d'autres obligations (bases légales, information, droits). Les deux se complètent.

### Conservation et purge, traitement par traitement

Fixer une durée ne suffit pas (art. 5(1)(e)) : il faut un **mécanisme** qui l'applique et une **preuve** que la purge a eu lieu. Distinction utilisée : base **active** (accessible aux équipes), **archive** restreinte (accès exceptionnel, tracé), puis suppression. Les durées sont des hypothèses d'étude.

| Traitement | Base active | Archive | Mécanisme de purge | Preuve / contrôle |
|---|---|---|---|---|
| T1 Dossier patient | 5 ans après le dernier contact | Jusqu'à 20 ans | Liste annuelle des dossiers échus, validée par un médecin, puis suppression dans le logiciel | Journal de purge (date, périmètre, validateur) |
| T2 Rendez-vous | 12 mois | Aucune | Purge automatique mensuelle paramétrée chez le prestataire | Export de contrôle trimestriel |
| T3 SMS | Jusqu'au retrait du consentement | Aucune | Retrait du numéro à la demande, puis purge | Registre des retraits |
| T4 Facturation | Exercice en cours | 10 ans | Archivage comptable, purge annuelle des pièces échues | Journal de purge |
| T5 RH | Durée du contrat | Obligations légales (à confirmer) | Revue annuelle par les RH | Compte rendu de revue |
| T6 Vidéo | 30 jours | Aucune | Écrasement automatique par l'enregistreur | Test trimestriel de la plus ancienne image |
| Papier (A8) | Selon le traitement | Selon le traitement | Destruction par broyage ou prestataire de destruction | Bordereau de destruction |

**Point technique :** une donnée purgée de la base **reste** dans les sauvegardes jusqu'à expiration de leur cycle de rétention (hypothèse : 35 jours). La durée de rétention des sauvegardes doit être courte, documentée, et expliquée si une personne demande l'effacement (ISO 27002 : 8.10, suppression d'informations).

### Gestion des accès et moindre privilège

Principe : chaque personne n'accède qu'à ce que sa fonction exige (ISO 27002 : 5.15, 5.18, 8.2 droits d'accès privilégiés, 8.3 restriction d'accès à l'information). Comptes **nominatifs** uniquement, aucun compte partagé, MFA pour le dossier patient et la messagerie.

| Profil | Accès au dossier patient (T1) | Remarque |
|---|---|---|
| Médecin | Lecture et écriture sur ses patients et ceux de l'équipe de soins | |
| Infirmier / paramédical | Lecture et écriture sur les actes de soins | Pas de modification des comptes rendus médicaux |
| Secrétaire médicale | Identité, agenda, facturation | **Pas d'accès aux comptes rendus** |
| Accueil | Agenda et identité | Pas d'accès aux antécédents |
| Facturation | Actes et assurance | Pas d'accès aux antécédents |
| Administrateur technique | Gestion des comptes, pas de lecture du contenu médical | Accès exceptionnel journalisé |
| Direction | Statistiques agrégées | Pas d'accès nominatif |

**Bris de glace :** en urgence, un professionnel peut accéder à un dossier hors de son périmètre ; l'accès est motivé, **journalisé et revu** par le référent sous 48 h.

**Revue semestrielle des droits :** le responsable IT extrait la liste des comptes et des profils ; chaque responsable métier confirme ou retire ; les comptes non confirmés sont suspendus sous 5 jours ; l'extraction signée est conservée comme preuve.

### Arrivées, mutations et départs

| Événement | Actions | Réf. ISO 27002 |
|---|---|---|
| Arrivée | Demande écrite du responsable métier ; création d'un compte nominatif avec profil type ; **engagement de confidentialité signé** ; sensibilisation avant l'ouverture des accès | 6.1, 6.2, 6.6 |
| Mutation | Réexamen du profil, retrait des droits devenus inutiles | 5.18 |
| Départ | Suspension des comptes **le jour du départ** (dossier patient, messagerie, VPN, badge) ; restitution du matériel ; suppression sous 30 jours ; rappel de la confidentialité après le départ | 5.11, 6.5 |
| Remplaçants, stagiaires | Comptes à durée limitée, profil restreint | 5.18 |

### Chiffrement : au repos et en transit

- **Au repos :** chiffrement intégral du disque sur les portables et les postes nomades ; supports amovibles chiffrés (ou interdits) ; sauvegardes chiffrées, clés conservées à part. Pour le service en ligne (A1), le centre **ne maîtrise pas** le chiffrement : il demande à l'éditeur, par questionnaire et par contrat, de décrire ce qui est chiffré et comment.
- **En transit :** accès aux services en ligne uniquement via des connexions chiffrées (TLS) ; accès à distance par VPN avec MFA.
- **Limites à ne pas taire :** le chiffrement réduit le risque en cas de **vol ou de perte**, mais ne protège pas contre un compte compromis ni contre un poste déverrouillé. Un e-mail ordinaire n'est pas chiffré de bout en bout : les données de santé passent par une messagerie sécurisée de santé ou par un lien à accès contrôlé, pas en pièce jointe. La gestion des clés (qui, où, quelle procédure de récupération) est une mesure à part entière (ISO 27002 : 8.24).

### Journalisation et détection

- **À journaliser :** connexions réussies et échouées, consultation, modification et export d'un dossier, actions d'administration, bris de glace.
- **Protection des journaux :** accès restreint, durée de conservation proportionnée (hypothèse : 12 mois), information du personnel, car les journaux sont aussi des données personnelles.
- **Revue :** alertes automatiques simples si le logiciel le permet (accès à un grand nombre de dossiers en peu de temps, accès hors horaires, accès au dossier d'un proche ou d'un collègue) ; à défaut, échantillon trimestriel de 10 dossiers consultés. Référence : 8.15 (journalisation), 8.16 (activités de surveillance).
- **Limite assumée :** un centre de cette taille n'a pas de centre de supervision ; la détection repose sur des contrôles par échantillon et sur le signalement par le personnel.

### Sauvegardes et rétablissement

- Règle 3-2-1, sauvegardes **chiffrées**, au moins une copie **hors ligne ou protégée contre l'effacement**.
- Objectifs (hypothèses) : perte de données maximale de 24 h, rétablissement du dossier patient en moins de 8 h ; un **mode dégradé papier** est prévu pour les consultations.
- **Test de restauration** : trimestriel sur un échantillon, complet une fois par an ; le résultat est daté et conservé.
- Pour le service en ligne, la sauvegarde par l'éditeur ne dispense pas le centre d'obtenir une **possibilité d'export** et une clause de réversibilité (ISO 27002 : 8.13, 5.30).

### Pseudonymisation et minimisation

- **Minimisation (art. 5(1)(c)) :** ne collecter que le nécessaire ; supprimer ou encadrer les champs libres ; formulaire de rendez-vous sans motif médical détaillé.
- **Pseudonymisation (art. 25(1), art. 32(1)(a)) :** pour les statistiques de direction et les extraits d'audit, l'identité est remplacée par un identifiant interne, la table de correspondance étant conservée à part. Une donnée pseudonymisée reste une donnée personnelle ; la mesure réduit le risque, elle ne l'élimine pas.
- **Tests :** aucune donnée réelle dans les environnements de test ou de formation du logiciel (ISO 27002 : 8.33, 8.11).

### Sécurité des échanges avec les patients

| Canal | Règle |
|---|---|
| Portail ou messagerie sécurisée du logiciel | Canal par défaut pour comptes rendus et documents |
| SMS | Date et heure du rendez-vous uniquement, jamais de spécialité ni de motif |
| E-mail ordinaire | Pas de données de santé ; envoi d'un lien temporaire dont le code passe par un autre canal si nécessaire |
| Téléphone | Vérification de l'identité sur deux éléments avant toute information médicale |
| Courrier | Vérification de l'adresse, enveloppe fermée, mention « confidentiel » |
| Demande d'accès | Remise via un canal sécurisé ou en main propre contre pièce d'identité |

Références : ISO 27002 : 5.14 (transfert d'information), 8.12 (prévention des fuites de données).

---

## 6. Violation de données : évaluer et notifier (art. 33 et 34)

### Rappel

Une **violation de données à caractère personnel** (art. 4(12)) est une atteinte à la sécurité entraînant, de manière accidentelle ou illicite, la destruction, la perte, l'altération, la divulgation ou l'accès non autorisé à des données. Trois grandes familles : confidentialité, intégrité, disponibilité.

- **Art. 33** : le responsable de traitement notifie à l'autorité de contrôle (en France, la CNIL) **dans les 72 heures** après en avoir pris connaissance, **sauf** si la violation n'est pas susceptible d'engendrer un risque pour les personnes. Au-delà de 72 heures, la notification est accompagnée des motifs du retard.
- **Art. 34** : si le risque est **élevé**, les personnes concernées sont aussi informées, sans retard injustifié.
- **Art. 33(5)** : **toute** violation est documentée en interne, même non notifiée.
- Un sous-traitant informe le responsable de traitement **dans les meilleurs délais** après en avoir pris connaissance (art. 33(2)).

### Procédure complète retenue

| Étape | Contenu | Responsable |
|---|---|---|
| 1. Détecter et signaler | Tout membre du personnel signale un incident **sans délai**, par un canal unique (adresse dédiée et téléphone du référent), sans crainte de reproche | Tous |
| 2. Qualifier | Est-ce une atteinte à la confidentialité, l'intégrité ou la disponibilité de données personnelles (art. 4(12)) ? Ouverture d'une fiche d'incident ; **l'horloge des 72 h part de la prise de connaissance** | Référent |
| 3. Contenir | Rappel du message, suspension du compte, isolement du poste, demande de suppression écrite | Responsable IT |
| 4. Évaluer le risque pour les personnes | Nature et sensibilité des données, volume, facilité d'identification, gravité des conséquences, personnes vulnérables, nature du destinataire, mesures qui limitent l'impact (chiffrement, suppression confirmée) | Référent, avec le responsable métier |
| 5. Décider | Aucun risque : documentation interne seulement. Risque : notification à l'autorité. Risque **élevé** : notification **et** information des personnes | Direction |
| 6. Notifier l'autorité | Contenu de l'art. 33(3). Si toutes les informations ne sont pas disponibles, l'information peut être fournie **par phases**, sans retard indu (art. 33(4)) | Référent |
| 7. Informer les personnes (si risque élevé) | Langage clair et simple, nature de la violation, contact, conséquences probables, mesures (art. 34(2)). Non exigé dans trois cas (art. 34(3)) : données rendues incompréhensibles (chiffrement), mesures ultérieures écartant le risque élevé, effort disproportionné (communication publique à la place) | Direction, référent |
| 8. Corriger et tirer les leçons | Cause racine, mesures correctives, mise à jour des procédures, retour au personnel | Référent, responsable IT |
| 9. Documenter | Enregistrement dans le registre des violations, **même si aucune notification n'est faite** (art. 33(5)) | Référent |

Si le centre découvre la violation chez un **sous-traitant**, celui-ci doit l'informer sans délai (art. 33(2)) : le contrat (section 2) précise le canal et le délai. Un **exercice annuel** (simulation d'envoi erroné ou de perte d'un portable) vérifie que la procédure fonctionne.

### Incident fictif

**Lundi 9 h 10 :** une secrétaire envoie par erreur à une adresse externe un export de rendez-vous de la semaine suivante, destiné à la direction. Le fichier contient **38 patients** : nom, prénom, date de naissance, spécialité consultée et motif de consultation. Le destinataire, une personne sans lien avec le centre, répond le jour même, signale l'erreur et **atteste par écrit avoir supprimé le message**. L'erreur est signalée au référent à **11 h 30** : c'est le moment où le centre « a connaissance » de la violation (T0).

### Évaluation

| Question | Analyse (hypothèse d'étude) |
|---|---|
| Est-ce une violation ? | Oui : divulgation non autorisée de données (confidentialité) |
| Quelles données ? | Identité, date de naissance et motifs de consultation : **données de santé** |
| Combien de personnes ? | 38 patients, dont des personnes vulnérables |
| Qui a eu accès ? | Un seul destinataire identifié, de bonne foi, qui atteste la suppression |
| Risque pour les personnes ? | **Présent** : données de santé divulguées, même brièvement ; atténué par le cercle réduit et la suppression attestée |
| Risque élevé ? | **Non retenu** dans cette hypothèse, mais décision motivée et à relire si un élément change (par exemple, absence de suppression confirmée) |

### Décision et chronologie (fictives)

| Moment | Action |
|---|---|
| T0 (lundi 11 h 30) | Prise de connaissance, ouverture d'une fiche d'incident, confinement : rappel du message et demande de suppression écrite |
| T0 + 4 h | Évaluation par le référent et la direction : violation de confidentialité de données de santé |
| T0 + 24 h | **Décision : notification à la CNIL** (le risque n'est pas exclu) ; **pas de communication individuelle** (art. 34), la décision est motivée par écrit |
| Avant T0 + 72 h | Notification avec le contenu de l'art. 33(3) : nature de la violation, catégories et nombre approximatif de personnes et de données, coordonnées du contact, conséquences probables, mesures prises ou envisagées |
| Ensuite | Retour d'expérience, mise en place d'une règle d'envoi, enregistrement dans le registre des violations (art. 33(5)) |

**Mesures correctives :** vérification du destinataire avant envoi, partage sécurisé plutôt qu'e-mail, sensibilisation. Références ISO 27002 : 5.24 à 5.27 (gestion des incidents), 8.12 (prévention des fuites), 6.3.

**Point de discussion honnête :** une autre personne pourrait conclure à une absence de risque, ou au contraire à un risque élevé. La logique à retenir n'est pas le verdict, mais la **démarche** : qualifier, évaluer le risque pour les personnes, décider, motiver et documenter dans les délais.

### Registre des violations : champs retenus

Numéro de fiche ; date et heure de la violation ; date et heure de **prise de connaissance** ; description et cause ; nature (confidentialité, intégrité, disponibilité) ; catégories et nombre approximatif de personnes ; catégories de données ; sous-traitant impliqué ; conséquences probables ; mesures prises ; **niveau de risque retenu et motif** (aucun, risque, élevé) ; décision de notification à l'autorité (date, référence) ; décision d'information des personnes (oui ou non, motif) ; actions correctives, responsable et échéance ; statut. Pour l'incident fictif ci-dessus : niveau « risque », notification à l'autorité oui, information des personnes non (motivé).

---

## 7. Plan d'actions priorisé

| Priorité | Action | Réf. | Échéance (hypothèse) |
|---|---|---|---|
| **P1** | Procédure de violation de données et registre des violations | art. 33, 34 ; ISO 5.24 à 5.27 | 1 mois |
| **P1** | MFA et profils d'accès sur le dossier patient et la messagerie | art. 32 ; ISO 5.15, 8.5 | 1 mois |
| **P1** | Régulariser les contrats de sous-traitance manquants | art. 28 ; ISO 5.19 à 5.23 | 2 mois |
| **P1** | Procédure de réponse aux demandes de droits | art. 12, 15 à 22 | 2 mois |
| **P2** | Finaliser le registre des traitements et les durées de conservation | art. 30, 5(1)(e) | 3 mois |
| **P2** | AIPD sur T1, avant la migration du logiciel | art. 35 | Avant la migration |
| **P2** | Chiffrement des portables, sauvegarde hors site testée | art. 32 ; ISO 8.24, 8.13 | 3 mois |
| **P2** | Mettre à jour l'information des patients (affichage, livret, site) | art. 13 | 3 mois |
| **P3** | Formation annuelle du personnel | ISO 6.3 | 6 mois |
| **P3** | Décision sur le DPO, journalisation des accès, revue annuelle du registre | art. 37 ; ISO 8.15 | 6 à 12 mois |

---

## 8. Gouvernance et responsabilités

### Rôles

- **Direction = responsable de traitement.** Le centre (en pratique sa direction) détermine les finalités et les moyens ; il est responsable de la conformité et doit pouvoir la **démontrer** (art. 5(2), art. 24(1)) : mesures techniques et organisationnelles appropriées, réexaminées et actualisées. La protection des données dès la conception et par défaut (art. 25) s'applique à tout nouveau projet, comme la migration du dossier patient.
- **Référent conformité ou DPO.** Le DPO informe, conseille, contrôle, coopère avec l'autorité et sert de point de contact (art. 39). S'il est désigné, il agit **sans instruction** sur ses missions et rend compte au plus haut niveau (art. 38(3)), ses coordonnées sont publiées et communiquées à l'autorité (art. 37(7)), et il ne doit pas être en **conflit d'intérêts** (art. 38(6)) : le directeur ou le responsable IT ne peut pas contrôler ses propres décisions. Il peut être interne ou externe (art. 37(6)).
- **Responsable IT / sécurité (RSSI ou équivalent à temps partiel).** Met en œuvre et exploite les mesures de l'article 32 ; fournit les preuves (extractions, journaux). ISO 27002 : 5.2 (rôles et responsabilités), 5.3 (séparation des tâches).
- **Responsables métiers** (médecin coordonnateur, accueil, RH, facturation) : propriétaires des actifs et des traitements de leur périmètre ; valident les droits d'accès et les durées.
- **Sous-traitants :** agissent sur instruction documentée (art. 28) ; leur responsabilité contractuelle ne dispense pas le centre de les choisir et de les contrôler.

**Désignation du DPO : décision à documenter.** Hypothèse d'étude : le centre ne désigne pas formellement de DPO à ce stade (la « grande échelle » est discutable, section 1), mais nomme un **référent conformité** (le responsable qualité) avec lettre de mission. Il évite de se faire appeler « DPO », titre qui emporte des garanties d'indépendance précises. La décision, ses motifs et sa date de revue sont écrits. Si un DPO devient obligatoire ou souhaitable, un DPO externe mutualisé est l'option la plus réaliste pour 30 personnes.

### Matrice RACI sur les activités clés

R = réalise ; A = décide et rend compte (la responsabilité juridique reste celle du responsable de traitement) ; C = consulté ; I = informé.

| Activité | Direction | Référent / DPO | Resp. IT | Resp. métiers | Prestataires | Auditeur indépendant |
|---|---|---|---|---|---|---|
| Tenir le registre des traitements | A | R | C | C | I | |
| Répondre aux droits des personnes | I | A / R | C | R | C | |
| Gérer une violation de données | A | R | R | C | C | |
| Réaliser une AIPD | A | R | C | C | C | |
| Encadrer les sous-traitants | A | R | C | I | C | |
| Former le personnel | A | R | C | I | | |
| Auditer la conformité | A | C | C | C | I | R |

### Les trois lignes de maîtrise

| Ligne | Qui | Rôle |
|---|---|---|
| 1re | Responsables métiers, équipes, responsable IT | Appliquent les règles et contrôlent leur propre activité au quotidien |
| 2e | Référent conformité ou DPO, responsable sécurité | Cadrent, conseillent, suivent les indicateurs, contrôlent l'application |
| 3e | Audit interne indépendant | Évalue l'ensemble et rend compte à la direction |

**Limite :** dans un centre de 30 personnes, l'indépendance de la 3e ligne est difficile à obtenir. Solutions : un auditeur d'un autre établissement ou un prestataire externe, avec la règle que **nul n'audite ce qu'il a conçu ou exploité**. Les limites restantes sont écrites dans le rapport.

---

## 9. Audit interne de conformité

### Programme d'audit

- **Fréquence :** un audit complet la première année (le socle), puis des audits thématiques (accès et habilitations, sous-traitants, droits et violations), avec un audit hors programme après une violation importante ou un changement majeur.
- **Périmètre (audit 1) :** les 6 traitements du registre, les actifs A1 à A12, les 5 sous-traitants.
- **Critères :** articles du RGPD applicables (5, 12 à 22, 25, 28, 30, 32 à 35), politiques et procédures internes du centre (politique de protection des données, procédure de droits, procédure de violation, politique de conservation, règles d'accès) et clauses contractuelles.
- **Cadre de référence :** la démarche s'inspire de l'audit interne et de la revue de direction de l'ISO/IEC 27001:2022 (clauses 9.2 et 9.3) et des lignes directrices d'audit de l'ISO 19011. Le centre n'est pas certifié : il s'agit d'un modèle d'organisation, non d'une revendication de conformité à ces normes.

### Méthode

1. **Préparation :** plan d'audit, périmètre, critères, échantillons, calendrier.
2. **Entretiens** avec la direction, le référent, le responsable IT et un échantillon de chaque métier.
3. **Revue documentaire :** registre, contrats, procédures, registre des demandes et des violations.
4. **Tests sur échantillons (hypothèses) :** 100 % des comptes actifs du dossier patient comparés à la liste du personnel ; 5 contrats de sous-traitance ; 10 dossiers consultés vérifiés dans les journaux ; toutes les demandes de droits de l'année ; test d'accès avec un compte de secrétaire.
5. **Rapport et réunion de clôture :** constats avec preuves, validation factuelle par les audités, puis plan d'actions.

### Grille de classement des constats

| Classe | Définition |
|---|---|
| Conforme | L'exigence est remplie et la preuve existe |
| Écart mineur | L'exigence est partiellement remplie ; pas d'exposition immédiate des personnes |
| Écart majeur | L'exigence n'est pas remplie, ou la preuve manque sur un point essentiel, avec exposition des personnes ou manquement à une obligation légale |
| Point sensible | Pas un écart aujourd'hui, mais un risque ou une amélioration à surveiller |

### Constats fictifs de l'audit 1

| Réf. | Constat et preuve | Critère | Classe | Risque pour les personnes | Action corrective | Responsable | Échéance |
|---|---|---|---|---|---|---|---|
| C1 | Le registre liste 5 traitements ; la vidéosurveillance (T6) en est absente (comparaison avec l'inventaire) | Art. 30 | Écart mineur | Faible : traitement non encadré ni informé | Ajouter T6, analyse d'intérêt légitime, panneau d'information | Référent | 1 mois |
| C2 | 3 prestataires sur 5 sans clause de sous-traitance (revue de 5 contrats) | Art. 28 | **Écart majeur** | Élevé : transferts de données sans garanties | Avenants ou changement de prestataire | Direction | 2 mois |
| C3 | Sur 30 comptes actifs du dossier patient, 4 appartiennent à des personnes parties, dont une depuis 7 mois (rapprochement avec la liste du personnel) | Art. 32 ; ISO 5.18, 6.5 | **Écart majeur** | Élevé : accès illégitime possible | Suspension immédiate, procédure de départ, revue semestrielle | Responsable IT | 1 semaine (suspension), 2 mois (procédure) |
| C4 | Le profil « secrétaire » peut ouvrir les comptes rendus (test avec un compte de secrétaire) | Art. 5(1)(f), 25, 32 | **Écart majeur** | Élevé : accès non nécessaire à des données de santé | Reparamétrer les profils selon la matrice | Resp. IT, médecin coordonnateur | 1 mois |
| C5 | La purge des rendez-vous n'est pas activée : environ 3 400 rendez-vous de plus de 12 mois (hypothèse) subsistent sur la plateforme | Art. 5(1)(e) | Écart mineur | Moyen : conservation excessive | Activer la purge, purge initiale tracée | Responsable accueil | 1 mois |
| C6 | Une demande d'accès reçue a été traitée en 26 jours, mais sans registre ni trace de l'identité vérifiée | Art. 12(3), 5(2) | Écart mineur | Moyen : délai respecté, preuve fragile | Registre des demandes, modèle de réponse | Référent | 2 mois |
| C7 | Aucune procédure de violation ; 2 incidents d'envoi erroné de l'année n'ont pas été documentés (entretiens) | Art. 33(5), 33 | **Écart majeur** | Élevé : notifications éventuellement manquées | Procédure et registre, qualification a posteriori des 2 incidents | Direction, référent | 1 mois |
| C8 | La restauration de la sauvegarde n'a jamais été testée (aucune trace) | Art. 32(1)(c) et (d) ; ISO 8.13 | Écart mineur | Moyen : indisponibilité prolongée du dossier | Test de restauration trimestriel | Responsable IT | 3 mois |
| C9 | 30 membres du personnel sur 30 ont signé un engagement de confidentialité (revue des dossiers RH) | Art. 32(4) ; ISO 6.6 | Conforme | | Maintenir pour les arrivées | Responsable RH | En continu |
| C10 | Le copieur-scanner est loué ; son disque conserve les numérisations et rien n'est prévu à la fin du contrat | ISO 7.14, 8.10 | Point sensible | Moyen si le matériel est restitué sans effacement | Clause d'effacement ou de destruction du disque | Responsable IT | À la fin du contrat |

### Suivi des actions et revue de direction

- **Suivi :** chaque écart reçoit un responsable, une échéance, un statut (ouvert, en cours, clos, en retard) et une **preuve de clôture** (copie de l'avenant, extraction des comptes, journal de purge). Les écarts majeurs sont revus chaque mois par le référent ; la clôture est vérifiée, pas déclarée.
- **Vérification d'efficacité :** pour les écarts majeurs, un contrôle de nouveau échantillon après 3 mois.
- **Revue de direction (annuelle, sur le modèle de la clause 9.3 de l'ISO 27001) :** entrées : résultats d'audit, indicateurs, violations, demandes de droits, changements prévus, état des risques ; sorties : décisions, ressources, priorités de l'année suivante, révision du registre et de l'AIPD. Un compte rendu est conservé.

---

## 10. Indicateurs de pilotage de la conformité

Valeurs de départ et cibles : hypothèses d'étude, issues de l'audit fictif.

| Indicateur | Définition | Départ | Cible | Fréquence | Source |
|---|---|---|---|---|---|
| Registre à jour | Part des traitements revus depuis moins de 12 mois | 50 % | 100 % | Semestrielle | Registre |
| Contrats de sous-traitance | Prestataires avec contrat conforme à l'art. 28 | 2 sur 5 | 5 sur 5 | Trimestrielle | Dossier contrats |
| Droits des personnes | Part des demandes traitées dans le délai d'un mois ; délai moyen | 100 % ; 26 jours | 100 % ; moins de 20 jours | Trimestrielle | Registre des demandes |
| Violations | Nombre détecté, notifié, délai moyen de notification | Non suivi | Suivi à 100 % ; moins de 72 h | Mensuelle | Registre des violations |
| Formation | Part du personnel formé depuis moins de 12 mois | 0 % | 100 % | Semestrielle | Liste d'émargement |
| Comptes à jour | Part des comptes actifs confirmés à la dernière revue | Non mesuré | 100 % | Semestrielle | Extraction signée |
| MFA | Part des comptes critiques avec MFA | 0 % | 100 % | Trimestrielle | Console d'administration |
| Sauvegardes | Date et résultat du dernier test de restauration | Jamais testée | Moins de 3 mois, réussi | Trimestrielle | Compte rendu de test |
| Purge | Part des purges planifiées réalisées dans les temps | 0 % | 100 % | Trimestrielle | Journal de purge |
| AIPD | AIPD réalisées sur AIPD requises | 0 sur 1 | 1 sur 1 | À chaque projet | Dossier AIPD |
| Audit | Écarts majeurs ouverts ; écarts en retard | 4 ; 0 | 0 ; 0 | Mensuelle | Suivi d'audit |

Ces indicateurs alimentent la revue de direction. Un indicateur sans seuil ni responsable n'est pas un indicateur de pilotage : chacun a ici un propriétaire (le référent, sauf mention contraire) et une cible.

---

## Ce que cette étude démontre, et ses limites

**Compétences mobilisées :** cartographie des traitements et registre, choix de bases légales, encadrement des sous-traitants, procédure de droits, analyse d'impact, rattachement à l'ISO 27002, qualification d'une violation de données, plan d'actions.

**Limites assumées :**
- **Ce n'est pas un conseil juridique.** Les qualifications (bases légales, délais, durées, besoin de DPO) sont des hypothèses d'étude à confirmer par un juriste ou le référent compétent.
- Les règles propres à la santé (secret médical, dossier du patient, hébergement des données de santé) ne sont pas traitées en détail.
- Les chiffres, durées et notes sont des hypothèses pour illustrer la méthode.
- L'étude ne chiffre pas le coût des mesures, nécessaire pour arbitrer en pratique.
- Les constats d'audit, les échantillons, les valeurs d'indicateurs et les niveaux de classification sont inventés pour illustrer la méthode ; les normes ISO sont citées comme modèles d'organisation, sans revendication de certification.
- Le centre, les prestataires et l'incident sont entièrement fictifs.
