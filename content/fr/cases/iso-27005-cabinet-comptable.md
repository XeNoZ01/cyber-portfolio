+++
title = "Analyse de risques ISO 27005 d'un cabinet comptable"
date = 2026-11-24
draft = false
description = "Démarche complète sur une organisation fictive de 14 personnes : contexte, identification, évaluation, traitement et suivi des risques."
case_study = "fictive"
translationKey = "iso27005-cabinet"
+++

## Cadre de l'étude

**Organisation fictive : le cabinet Aubépine.** Cabinet d'expertise comptable de 14 personnes (2 associés, 9 collaborateurs, 3 assistants), qui tient la comptabilité et la paie d'environ 180 clients, majoritairement des TPE.

**Objectif :** montrer une analyse de risques de bout en bout, avec la démarche de la norme ISO/IEC 27005 (gestion des risques liés à la sécurité de l'information), rattachée aux mesures de l'ISO/IEC 27002:2022. C'est la première brique d'un SMSI ISO 27001, mais l'étude s'arrête à l'analyse et au plan de traitement.

**Ce qui est inventé :** le cabinet, ses effectifs, ses outils et les notes attribuées. **Ce qui est réel :** la méthode, les critères et les mesures de référence utilisés.

---

## 1. Établir le contexte

### Périmètre

Tout le cabinet : un site unique, le télétravail occasionnel, les postes, le serveur de fichiers, la messagerie, le logiciel de comptabilité et de paie (service en ligne) et les sauvegardes.

### Enjeux

- **Confidentialité :** données financières et de paie de clients, dont des données personnelles (RGPD) ; secret professionnel.
- **Disponibilité :** les échéances fiscales et sociales ne se décalent pas ; une panne en période de clôture coûte cher.
- **Intégrité :** une écriture comptable ou un virement de paie falsifié a des conséquences directes pour les clients.

### Critères de l'étude

Échelles sur 4 niveaux, volontairement simples pour un petit cabinet.

| Niveau | Gravité (impact) | Vraisemblance (sur 12 mois) |
|---|---|---|
| 1 | Négligeable : gêne sans conséquence client | Improbable : moins d'une fois en 5 ans |
| 2 | Limité : retard d'un ou deux jours | Possible : environ une fois en 2 à 5 ans |
| 3 | Important : clients touchés, obligation de notification | Probable : environ une fois par an |
| 4 | Critique : perte de clients, sanction, arrêt prolongé | Quasi certain : plusieurs fois par an |

**Niveau de risque = gravité × vraisemblance.**

| Score | Niveau | Décision |
|---|---|---|
| 1 à 3 | Faible | Accepté, suivi |
| 4 à 6 | Moyen | Traité sous 12 mois |
| 8 à 9 | Élevé | Traité en priorité (6 mois) |
| 12 à 16 | Critique | Traité immédiatement |

---

## 2. Identifier les risques

### Actifs principaux

| Actif | Valeur pour le cabinet |
|---|---|
| Dossiers clients (comptables, paie, pièces d'identité) | Cœur de l'activité, données personnelles |
| Logiciel de compta et paie en ligne | Production quotidienne, dépendance à un fournisseur |
| Serveur de fichiers | Documents de travail et archives |
| 14 postes de travail, dont 6 portables | Accès aux dossiers, nomadisme |
| Messagerie | Échanges avec les clients, vecteur d'attaque courant |
| Sauvegardes | Dernier recours en cas de sinistre |

### Scénarios de risque

L'approche retenue est **par scénarios** : une menace, exploitant une vulnérabilité, touche un actif et produit un impact.

| Réf. | Scénario | Vulnérabilité constatée (hypothèse d'étude) |
|---|---|---|
| R1 | Hameçonnage : le compte de messagerie d'un collaborateur est compromis | Pas d'authentification multifacteur, peu de sensibilisation |
| R2 | Rançongiciel sur le serveur de fichiers et les postes | Antivirus basique, mises à jour irrégulières |
| R3 | Perte ou vol d'un portable | Disques non chiffrés |
| R4 | Envoi de données de paie au mauvais destinataire | Pas de règle de vérification, pièces jointes en clair |
| R5 | Indisponibilité du logiciel en ligne en période de clôture | Aucune solution de repli prévue |
| R6 | Accès conservés après le départ d'un salarié | Pas de revue des droits |
| R7 | Panne du serveur sans sauvegarde exploitable | Sauvegarde jamais testée en restauration |
| R8 | Intrusion par un accès à distance exposé | Bureau à distance ouvert sur Internet |

---

## 3. Analyser et évaluer

| Réf. | Gravité | Vraisemblance | Score | Niveau |
|---|---|---|---|---|
| R1 Compromission de messagerie | 4 | 3 | 12 | **Critique** |
| R2 Rançongiciel | 4 | 3 | 12 | **Critique** |
| R3 Perte d'un portable | 3 | 3 | 9 | **Élevé** |
| R4 Erreur d'envoi | 3 | 3 | 9 | **Élevé** |
| R7 Sauvegarde inexploitable | 4 | 2 | 8 | **Élevé** |
| R8 Accès distant exposé | 4 | 2 | 8 | **Élevé** |
| R5 Logiciel indisponible | 3 | 2 | 6 | Moyen |
| R6 Droits non retirés | 3 | 2 | 6 | Moyen |

**Lecture :** deux risques critiques, tous deux liés à l'humain et à la messagerie. Ils mènent souvent l'un à l'autre (un hameçonnage réussi ouvre la porte au rançongiciel), d'où une réponse commune sur l'authentification et la sensibilisation.

---

## 4. Traiter les risques

Quatre options sont possibles : **réduire**, **éviter**, **transférer**, **accepter**. Les mesures sont rattachées à l'ISO/IEC 27002:2022.

| Réf. | Option | Mesures retenues | Réf. ISO 27002:2022 | Échéance |
|---|---|---|---|---|
| R1 | Réduire | MFA sur la messagerie et le logiciel métier ; sensibilisation et exercices d'hameçonnage | 8.5, 6.3 | 1 mois |
| R2 | Réduire + transférer | Protection des postes (EDR ou antivirus géré), mises à jour automatiques, sauvegarde hors ligne ; étude d'une assurance cyber | 8.7, 8.8, 8.13 | 3 mois |
| R3 | Réduire | Chiffrement des disques des portables, verrouillage automatique | 8.1, 8.24 | 2 mois |
| R4 | Réduire | Règle de double vérification du destinataire ; partage par lien sécurisé plutôt que pièce jointe | 5.14, 6.3 | 2 mois |
| R7 | Réduire | Sauvegarde 3-2-1 (3 copies, 2 supports, 1 hors site) et test de restauration trimestriel | 8.13, 5.30 | 3 mois |
| R8 | Éviter | Fermeture du bureau à distance exposé ; accès via VPN avec MFA | 8.20, 8.5 | 1 mois |
| R5 | Accepter, avec plan de repli | Procédure papier/Excel pour les échéances critiques ; clause de disponibilité à vérifier chez le fournisseur | 5.23, 5.30 | 6 mois |
| R6 | Réduire | Procédure d'arrivée/départ avec retrait des accès ; revue semestrielle des droits | 5.18, 6.5 | 4 mois |

### Risque résiduel attendu

| Réf. | Avant | Après (estimation) |
|---|---|---|
| R1 | 12 | 4 (gravité 4 × vraisemblance 1) |
| R2 | 12 | 4 |
| R3 | 9 | 3 (le vol reste possible, l'impact tombe à 1) |
| R4 | 9 | 4 |
| R7 | 8 | 2 (gravité ramenée à 2 par une restauration testée, vraisemblance 1) |
| R8 | 8 | 0 (exposition supprimée) |
| R5 | 6 | 6 (accepté) |
| R6 | 6 | 3 |

Le risque **R5 (6)** reste accepté : la direction le valide explicitement, car l'éliminer coûterait plus que l'impact attendu. **R1, R2 et R4 (4)** restent au seuil de traitement : à revoir à l'échéance.

---

## 5. Communication et suivi

- **Acceptation :** les deux associés valident le plan et le risque résiduel R5 par écrit.
- **Communication :** synthèse d'une page pour les collaborateurs ; sensibilisation de 30 minutes sur l'hameçonnage.
- **Indicateurs :** taux d'activation du MFA, taux de clic sur les exercices d'hameçonnage, date et résultat du dernier test de restauration, nombre de postes à jour.
- **Revue :** tous les 12 mois, et à chaque changement important (nouvel outil, incident, nouveau fournisseur).

---

## Ce que cette étude démontre, et ses limites

**Compétences mobilisées :** cadrage d'un périmètre, critères d'évaluation, scénarios de risque, hiérarchisation, plan de traitement rattaché à l'ISO 27002, suivi.

**Limites assumées :**
- Les vulnérabilités sont des hypothèses d'étude, pas le résultat d'un audit réel.
- Les notes sont une démonstration de la méthode ; en situation réelle, elles se discutent avec la direction et les métiers.
- L'étude ne chiffre pas les coûts des mesures, ce qui serait nécessaire pour arbitrer.
