# General Data Protection & Privacy

> **Cours complet — GDPR / RGPD, protection des données et gestion des violations de données**

---

## Table des matières

1. [Introduction](#1-introduction)
2. [Définitions fondamentales](#2-définitions-fondamentales)
3. [Le RGPD : objectifs et champ d'application](#3-le-rgpd--objectifs-et-champ-dapplication)
4. [Les principes fondamentaux du RGPD](#4-les-principes-fondamentaux-du-rgpd)
5. [Les bases légales du traitement](#5-les-bases-légales-du-traitement)
6. [Les acteurs et leurs responsabilités](#6-les-acteurs-et-leurs-responsabilités)
7. [Les droits des personnes](#7-les-droits-des-personnes)
8. [Privacy by Design et Privacy by Default](#8-privacy-by-design-et-privacy-by-default)
9. [Les violations de données personnelles](#9-les-violations-de-données-personnelles)
10. [Incident Response : réponse à une violation](#10-incident-response--réponse-à-une-violation)
11. [Analyse du risque lié à une violation](#11-analyse-du-risque-lié-à-une-violation)
12. [Notification à la CNIL](#12-notification-à-la-cnil)
13. [Communication aux personnes concernées](#13-communication-aux-personnes-concernées)
14. [Plan de notification](#14-plan-de-notification)
15. [Rôles lors d'une violation](#15-rôles-lors-dune-violation)
16. [Mesures techniques préventives](#16-mesures-techniques-préventives)
17. [Mesures organisationnelles](#17-mesures-organisationnelles)
18. [DPIA / AIPD](#18-dpia--aipd)
19. [Gestion des sous-traitants](#19-gestion-des-sous-traitants)
20. [Post-Incident Activity](#20-post-incident-activity)
21. [Construire une culture de protection des données](#21-construire-une-culture-de-protection-des-données)
22. [Scénario complet](#22-scénario-complet)
23. [Checklist de réponse à une violation](#23-checklist-de-réponse-à-une-violation)
24. [Points à connaître par cœur](#24-points-à-connaître-par-cœur)
25. [Références](#25-références)

---

# 1. Introduction

La **protection des données personnelles** vise à garantir que les informations concernant une personne physique sont utilisées de manière :

- légale ;
- transparente ;
- proportionnée ;
- sécurisée ;
- contrôlée.

Dans l'Union européenne, le texte central est le :

**General Data Protection Regulation — GDPR**

En français :

**Règlement Général sur la Protection des Données — RGPD**

Son nom juridique est :

> **Règlement (UE) 2016/679 du Parlement européen et du Conseil du 27 avril 2016.**

Le RGPD est applicable depuis le **25 mai 2018**.

Il ne s'agit pas uniquement d'un texte juridique.

Pour une entreprise, la conformité RGPD implique également :

- la cybersécurité ;
- la gouvernance ;
- la gestion des risques ;
- la gestion des incidents ;
- le contrôle des accès ;
- la gestion des fournisseurs ;
- la formation ;
- la documentation ;
- la protection de la vie privée.

---

# 2. Définitions fondamentales

## 2.1 Personal Data

Une **donnée à caractère personnel** est toute information se rapportant à une personne physique identifiée ou identifiable.

Exemples :

```text
Nom
Prénom
Adresse
Adresse e-mail
Numéro de téléphone
Adresse IP
Identifiant utilisateur
Numéro client
Photo
Localisation GPS
Cookie identifier
Données bancaires
```

Une donnée n'a donc pas besoin de contenir directement un nom pour être considérée comme personnelle.

Une adresse IP ou un identifiant permettant de retrouver une personne peut constituer une donnée personnelle.

---

## 2.2 Data Subject

La **personne concernée** (*Data Subject*) est la personne physique à laquelle appartiennent les données.

Exemple :

```text
Entreprise : ShopExample

Client :
Alice Martin
alice@example.com
+33...
```

Alice est la **Data Subject**.

---

## 2.3 Processing

Un **traitement** correspond pratiquement à toute opération réalisée sur des données personnelles.

Par exemple :

```text
Collecter
Stocker
Lire
Modifier
Analyser
Classer
Partager
Transférer
Sauvegarder
Archiver
Supprimer
```

Une simple base SQL contenant des utilisateurs constitue donc déjà un traitement de données personnelles.

---

## 2.4 Controller

Le **responsable du traitement** (*Data Controller*) détermine :

> pourquoi et comment les données sont traitées.

Exemple :

Une boutique en ligne décide :

```text
Pourquoi ?

→ gérer les commandes

Quelles données ?

→ nom
→ adresse
→ téléphone
→ historique des commandes
```

La boutique est le **Data Controller**.

---

## 2.5 Processor

Le **sous-traitant** (*Data Processor*) traite des données pour le compte du responsable du traitement.

Exemples :

```text
Cloud provider
SaaS CRM
Hébergeur
Prestataire de paiement
Service d'envoi d'e-mails
Entreprise de support informatique
```

Un sous-traitant doit notamment informer le responsable du traitement d'une violation de données personnelles dans les meilleurs délais après en avoir pris connaissance.

---

## 2.6 DPO

**DPO = Data Protection Officer**

En français :

**Délégué à la Protection des Données**

Il intervient comme spécialiste de la conformité et de la protection des données.

Ses principales missions sont notamment :

- informer ;
- conseiller ;
- surveiller la conformité ;
- sensibiliser le personnel ;
- participer aux audits ;
- conseiller concernant les DPIA/AIPD ;
- coopérer avec l'autorité de contrôle ;
- servir de point de contact avec l'autorité.

Ces missions sont notamment prévues par l'article 39 du RGPD.

---

# 3. Le RGPD : objectifs et champ d'application

Le RGPD cherche principalement à protéger :

```text
les droits
      +
les libertés
      +
la vie privée
des personnes physiques
```

Une idée fondamentale est que :

> une entreprise ne devient pas propriétaire des données personnelles qu'elle collecte.

Elle obtient seulement le droit de les traiter dans certaines conditions.

---

## 3.1 Application territoriale

Le RGPD s'applique notamment aux organismes établis dans l'Union européenne.

Mais il peut également concerner une entreprise située hors de l'Union européenne lorsqu'elle cible ou surveille des personnes situées dans l'UE.

Exemple :

```text
Entreprise américaine
        ↓
Site e-commerce
        ↓
Clients français
        ↓
Traitement de données des clients
        ↓
RGPD potentiellement applicable
```

---

# 4. Les principes fondamentaux du RGPD

L'article 5 du RGPD établit plusieurs principes essentiels relatifs au traitement des données.

Une bonne méthode mnémotechnique est :

```text
Legal
Purpose
Minimum
Correct
Limited
Secure
Accountable
```

---

## 4.1 Licéité, loyauté et transparence

Les données doivent être traitées :

- légalement ;
- loyalement ;
- de manière transparente.

L'entreprise doit notamment expliquer :

```text
Qui collecte ?
Pourquoi ?
Quelles données ?
Pendant combien de temps ?
Avec qui sont-elles partagées ?
Quels sont les droits de la personne ?
```

---

# 4.2 Purpose Limitation

## Limitation des finalités

Les données doivent être collectées pour un objectif :

- déterminé ;
- explicite ;
- légitime.

Exemple correct :

```text
Collecte de l'adresse
        ↓
Livraison d'une commande
```

Exemple problématique :

```text
Collecte de l'adresse
        ↓
Stockage sans raison
        ↓
Réutilisation future "au cas où"
```

La finalité doit être définie avant la collecte.

---

# 4.3 Data Minimization

## Minimisation des données

> Collecter uniquement les informations réellement nécessaires.

Exemple :

Pour une newsletter :

```text
E-mail : nécessaire ✅

Adresse postale : généralement inutile ❌

Numéro de passeport : totalement inutile ❌
```

Plus une entreprise conserve de données, plus :

```text
surface d'exposition ↑
impact potentiel d'une fuite ↑
responsabilité ↑
```

---

# 4.4 Accuracy

## Exactitude

Les données doivent être :

- correctes ;
- tenues à jour lorsque nécessaire ;
- corrigées lorsqu'elles sont incorrectes.

---

# 4.5 Storage Limitation

## Limitation de la conservation

Les données personnelles ne doivent pas être conservées indéfiniment.

Une organisation doit déterminer :

```text
Pourquoi je conserve cette donnée ?

Combien de temps en ai-je besoin ?

Que se passe-t-il après ?
```

Lorsque la conservation n'est plus nécessaire, les données peuvent selon les obligations applicables être :

```text
supprimées
      ↓
anonymisées
      ↓
ou archivées dans un cadre approprié
```

La CNIL rappelle que la durée doit être définie en fonction de la finalité ayant justifié la collecte.

---

# 4.6 Integrity & Confidentiality

## Intégrité et confidentialité

Les données doivent être protégées contre :

```text
accès non autorisé
modification
destruction
perte
divulgation
vol
```

Ce principe établit un lien direct entre :

```text
RGPD
  +
Cybersécurité
```

---

# 4.7 Accountability

## Responsabilité

L'organisation ne doit pas seulement respecter le RGPD.

Elle doit également être capable de :

> **démontrer qu'elle le respecte.**

C'est le principe d'**Accountability**.

Cela nécessite par exemple :

```text
Policies
Logs
Audits
Risk assessments
DPIA
Training records
Incident reports
Processing register
Security procedures
```

---

# 5. Les bases légales du traitement

Un traitement de données personnelles doit disposer d'un **fondement juridique**.

L'article 6 du RGPD prévoit six grandes bases légales.

| Base légale | Exemple |
|---|---|
| Consentement | Newsletter marketing |
| Contrat | Livraison d'une commande |
| Obligation légale | Conservation de documents comptables |
| Intérêts vitaux | Situation médicale urgente |
| Mission d'intérêt public | Administration publique |
| Intérêt légitime | Certaines opérations de sécurité ou prévention de fraude |

---

## Exemple

Lorsqu'un utilisateur achète un produit :

```text
Nom
Adresse
Produit commandé
```

peuvent être nécessaires pour :

```text
exécuter le contrat
```

Il n'est donc pas nécessaire de demander artificiellement :

```text
"Acceptez-vous que nous utilisions votre adresse
pour vous livrer votre commande ?"
```

La base légale peut être l'**exécution du contrat**.

---

# 6. Les acteurs et leurs responsabilités

Une gestion correcte des données nécessite des responsabilités clairement définies.

---

## Data Controller

Responsabilités :

```text
Déterminer la finalité
Choisir la base légale
Mettre en œuvre la sécurité
Respecter les droits
Évaluer les risques
Documenter les traitements
Gérer les violations
Notifier l'autorité lorsque nécessaire
```

---

## Data Processor

Responsabilités :

```text
Suivre les instructions du controller
Protéger les données
Contrôler ses accès
Alerter le controller en cas de violation
Respecter ses obligations contractuelles et réglementaires
```

---

## DPO

Responsabilités :

```text
Conseiller
Contrôler la conformité
Participer aux analyses de risques
Conseiller concernant les DPIA
Sensibiliser
Coopérer avec l'autorité
```

Le DPO doit pouvoir exercer ses missions de manière indépendante et sans conflit d'intérêts. La CNIL a encore rappelé ce principe en août 2026.

---

# 7. Les droits des personnes

Le RGPD donne aux personnes un ensemble de droits permettant de conserver une certaine maîtrise de leurs données.

Les principaux sont :

### Right to Information

Droit d'être informé sur l'utilisation des données.

### Right of Access

Droit de demander quelles données sont détenues.

### Right to Rectification

Droit de corriger les données incorrectes.

### Right to Erasure

Également appelé :

**Right to be Forgotten**

Droit de demander l'effacement dans certaines circonstances.

### Right to Restriction

Droit de demander la limitation temporaire d'un traitement.

### Right to Data Portability

Droit de récupérer certaines données dans un format permettant leur réutilisation.

### Right to Object

Droit de s'opposer à certains traitements.

### Rights concerning automated decisions

Protection vis-à-vis de certaines décisions entièrement automatisées produisant des effets juridiques ou similaires significatifs.

Les droits disponibles peuvent varier selon la base légale utilisée pour le traitement.

---

# 8. Privacy by Design et Privacy by Default

Deux concepts fondamentaux du RGPD sont :

```text
Privacy by Design
Privacy by Default
```

---

## Privacy by Design

La protection des données doit être pensée **dès la conception** d'un système.

Mauvaise méthode :

```text
Créer l'application
       ↓
Déployer
       ↓
Ajouter la sécurité et le RGPD ensuite
```

Bonne méthode :

```text
Design
  ↓
Privacy requirements
  ↓
Threat modeling
  ↓
Minimisation
  ↓
Security
  ↓
Development
  ↓
Testing
  ↓
Deployment
```

---

## Privacy by Default

Les paramètres par défaut doivent être les plus protecteurs raisonnablement possibles.

Exemple :

Une application possède :

```text
Share location publicly
```

Le réglage par défaut devrait être :

```text
OFF
```

et non :

```text
ON
```

---

# 9. Les violations de données personnelles

Une **Personal Data Breach** est une violation de sécurité entraînant accidentellement ou illicitement :

```text
destruction
perte
altération
divulgation non autorisée
accès non autorisé
```

à des données personnelles.

La CNIL rattache notamment les violations aux pertes de :

```text
Confidentiality
Integrity
Availability
```



---

# 9.1 Confidentiality Breach

Une personne non autorisée obtient accès aux données.

Exemple :

```text
Attacker
   ↓
SQL Injection
   ↓
Database
   ↓
10 000 clients exfiltrés
```

---

# 9.2 Integrity Breach

Les données sont modifiées sans autorisation.

Exemple :

```text
Attacker
   ↓
modifie les IBAN des clients
```

---

# 9.3 Availability Breach

Les données deviennent indisponibles.

Exemple :

```text
Ransomware
   ↓
Database encrypted
   ↓
Patient records unavailable
```

Une violation RGPD n'est donc **pas nécessairement une fuite de données**.

Une perte ou destruction peut également constituer une violation.

---

# 10. Incident Response : réponse à une violation

Lorsqu'une violation est suspectée, la rapidité est essentielle.

Un processus efficace peut être représenté par :

```text
Detection
   ↓
Containment
   ↓
Preservation
   ↓
Investigation
   ↓
Risk Assessment
   ↓
Notification Decision
   ↓
Eradication
   ↓
Recovery
   ↓
Post-Incident Review
```

---

# 10.1 Detection

Déterminer :

```text
Que s'est-il passé ?

Quand ?

Quels systèmes ?

Quelles données ?

Combien de personnes ?

L'attaque continue-t-elle ?
```

Sources possibles :

```text
SIEM alert
EDR
IDS/IPS
User report
DLP
Firewall logs
Cloud logs
Third-party notification
Attacker message
```

---

# 10.2 Activer l'équipe de réponse

Dès qu'un incident pouvant affecter des données personnelles est identifié :

```text
Incident Response Team
Security Team
DPO
Legal
IT
Management
Communication
```

doivent être mobilisés selon la gravité.

---

# 10.3 Containment

Objectif :

> empêcher l'incident de continuer ou de s'étendre.

Exemples :

```text
Disable compromised accounts

Block malicious IP addresses

Revoke sessions

Reset credentials

Isolate compromised machines

Disable vulnerable service

Block API keys

Segment network

Revoke stolen tokens
```

Attention :

La réponse ne doit pas détruire inutilement les preuves.

---

# 10.4 Preserve Evidence

Conserver :

```text
System logs
Authentication logs
Firewall logs
EDR telemetry
Memory dumps
Disk images
Network captures
Cloud audit logs
Malware samples
Emails
Timeline
```

Les preuves peuvent être nécessaires pour :

```text
forensic investigation
insurance
legal proceedings
regulatory investigation
law enforcement
```

---

# 10.5 Investigation

L'enquête doit déterminer :

```text
Attack vector

Initial access

Affected accounts

Affected systems

Persistence mechanisms

Privilege escalation

Lateral movement

Data accessed

Data modified

Data exfiltrated

Duration of compromise
```

---

# 10.6 Identifier les données concernées

Exemple :

```text
Database customers
│
├── First name
├── Last name
├── Email
├── Password hash
├── Postal address
├── Payment information
└── Purchase history
```

Il faut également estimer :

```text
nombre de personnes
nombre d'enregistrements
types de données
sensibilité
niveau de protection
```

---

# 10.7 Eradication

Supprimer la cause et les traces de l'attaque.

Exemples :

```text
Patch vulnerability

Remove malware

Remove persistence

Delete malicious accounts

Rotate credentials

Rotate API keys

Update firewall rules

Rebuild compromised servers
```

---

# 10.8 Recovery

Rétablir les services de façon sécurisée.

Exemples :

```text
Restore clean backups

Rebuild infrastructure

Enable monitoring

Verify integrity

Reconnect systems progressively

Increase logging

Monitor suspicious authentication
```

---

# 11. Analyse du risque lié à une violation

L'obligation de notification dépend largement du **risque pour les droits et libertés des personnes**.

Il faut analyser notamment :

```text
Nature des données
Sensibilité
Volume
Nombre de victimes
Possibilité d'identifier les personnes
Existence d'un chiffrement
Type d'attaquant
Conséquences potentielles
Population concernée
```

---

## Exemples de conséquences

Une violation peut provoquer :

```text
Identity theft
Fraud
Financial loss
Discrimination
Reputational damage
Loss of confidentiality
Account takeover
Phishing
Physical danger
Social engineering
```

---

# 11.1 Matrice RGPD simplifiée

| Niveau | Documentation interne | CNIL | Personnes |
|---|---:|---:|---:|
| Aucun risque | ✅ | ❌ | ❌ |
| Risque | ✅ | ✅ | ❌ |
| Risque élevé | ✅ | ✅ | ✅ |

C'est l'un des tableaux les plus importants à connaître.

La CNIL confirme cette logique : toutes les violations doivent être documentées ; les violations présentant un risque doivent être notifiées à l'autorité et celles présentant un risque élevé nécessitent également une information des personnes concernées.

---

# 12. Notification à la CNIL

En France, l'autorité de contrôle est :

**CNIL — Commission Nationale de l'Informatique et des Libertés**

---

## Règle des 72 heures

Lorsqu'une violation est susceptible d'engendrer un risque pour les droits et libertés des personnes :

> le responsable du traitement doit notifier la CNIL **dans les meilleurs délais et, si possible, au plus tard 72 heures après en avoir pris connaissance**.



---

## Attention

Cela ne signifie pas :

```text
72h après le début de l'attaque
```

mais :

```text
72h après que le responsable du traitement
a pris connaissance de la violation
```

La CNIL explique que cela correspond en pratique au moment où le responsable dispose d'un degré de certitude raisonnable qu'un incident a eu lieu et qu'il a touché des données personnelles.

---

# 12.1 Ne pas attendre la fin de l'enquête

Une erreur classique consiste à faire :

```text
Incident
   ↓
Forensic investigation pendant 10 jours
   ↓
Rapport final
   ↓
Notification CNIL
```

Ce comportement peut conduire à une notification trop tardive.

Le RGPD permet une notification en plusieurs étapes.

```text
T0
Violation connue

↓

< 72h

Notification initiale

↓

Investigation continue

↓

Notification complémentaire
```

La CNIL recommande de ne pas attendre d'avoir toutes les informations lorsqu'une violation devant être notifiée est déjà établie.

---

# 12.2 Informations à notifier

La notification doit notamment permettre de comprendre :

```text
Nature de la violation

Catégories de personnes

Nombre approximatif de personnes

Catégories de données

Nombre approximatif d'enregistrements

Conséquences probables

Mesures prises

Mesures envisagées

Contact DPO / point de contact
```

Ces éléments correspondent aux exigences de l'article 33.

---

# 12.3 Exemple de notification à l'autorité

```text
Subject: Personal Data Breach Notification

On 14 May 20XX at approximately 09:30, our security team
identified unauthorized access to a customer database.

The affected database contained names, email addresses and
hashed passwords.

Approximately 4,500 individuals may be affected.

The incident was contained at 11:15 by disabling the
compromised account and revoking all active authentication
tokens.

At this stage, our investigation indicates that payment card
information was not affected.

Potential consequences include phishing attempts and account
compromise.

We have forced password resets, increased monitoring and
started a forensic investigation.

Additional information will be provided as the investigation
progresses.

Contact:

Data Protection Officer
dpo@example.com
```

---

# 13. Communication aux personnes concernées

L'article 34 du RGPD prévoit une communication aux personnes lorsque la violation est susceptible d'engendrer :

> **un risque élevé pour leurs droits et libertés.**

Cette communication doit intervenir **sans retard indu**.

---

## Exemple

Une base contenant :

```text
Name
Email
Hashed password
```

est volée.

Selon les circonstances, le risque peut notamment être :

```text
credential attacks
phishing
identity fraud
```

Si l'analyse conclut à un risque élevé :

```text
CNIL
+
Affected users
```

doivent être informés.

---

# 13.1 Le message doit être clair

Éviter :

```text
An anomalous cybersecurity event resulted in an
information governance irregularity.
```

Préférer :

```text
An attacker gained unauthorized access to a database
containing your name, email address and password hash.
```

La communication doit utiliser un langage clair et simple et expliquer les mesures de protection possibles.

---

# 13.2 Exemple de message destiné aux personnes

```text
Subject: Important security notice regarding your account

We recently identified unauthorized access to one of our
systems.

The incident may have exposed your name, email address and
password hash.

We have contained the incident and disabled the compromised
access.

As a precaution, we have reset your password.

We recommend that you:

- create a new unique password;
- change the password on other services if you reused it;
- enable multi-factor authentication;
- remain cautious of unexpected emails or messages;
- report suspicious activity to our support team.

Our investigation is continuing.

For further information, you can contact our Data Protection
Officer at dpo@example.com.
```

---

# 14. Plan de notification

Un **Notification Plan** doit répondre à quatre questions :

```text
WHO?
WHEN?
HOW?
WHAT?
```

---

## WHO?

Selon le risque :

```text
DPO
Management
CNIL
Affected individuals
Processor/controller
Legal
Insurance
Law enforcement
```

---

## WHEN?

```text
Immediately
        ↓
Internal incident escalation

≤ 72 hours
        ↓
CNIL if required

Without undue delay
        ↓
Affected individuals if high risk
```

---

## HOW?

Canaux possibles :

```text
Official CNIL notification service
Email
Customer account notification
Telephone
SMS
Website notice
Postal mail
```

Le canal choisi doit être adapté à la situation.

---

## WHAT?

Communiquer :

```text
What happened

When

Data affected

Potential consequences

Actions already taken

Actions recommended

Contact details

Updates expected
```

---

# 15. Rôles lors d'une violation

Une organisation doit définir les responsabilités **avant l'incident**.

| Rôle | Responsabilité |
|---|---|
| Incident Commander | Coordonne la réponse |
| Security / IR Team | Investigation, containment, eradication |
| DPO | Analyse RGPD et conseille sur les notifications |
| Legal | Analyse juridique |
| IT / Infrastructure | Restauration et changements techniques |
| Communications Lead | Communications internes/externes |
| Management | Décisions stratégiques |
| Business Owner | Analyse de l'impact métier |
| Processor/Vendor | Informations et actions sur ses systèmes |

---

# 15.1 Incident Commander

Coordonne :

```text
timeline
teams
priorities
meetings
decisions
resources
```

Il doit éviter :

```text
chaos
duplicate work
untracked decisions
contradictory communication
```

---

# 15.2 Security Team

Responsable de :

```text
Detection

Containment

Forensics

Root cause analysis

Evidence preservation

Eradication

Recovery

Monitoring
```

---

# 15.3 DPO

Le DPO :

```text
évalue les obligations RGPD
conseille le responsable de traitement
vérifie la documentation
participe à l'évaluation du risque
sert de contact avec la CNIL
```

Il ne faut pas confondre :

```text
DPO
```

avec :

```text
Incident Commander
```

Le DPO conseille et contrôle la conformité ; il n'est pas nécessairement responsable opérationnel de toute la réponse cyber.

---

# 15.4 Communication Lead

Une seule communication coordonnée évite :

```text
informations contradictoires
fuites
spéculations
fausses promesses
```

Il travaille avec :

```text
DPO
Legal
Security
Management
```

---

# 16. Mesures techniques préventives

L'article 32 impose des mesures techniques et organisationnelles adaptées aux risques et cite notamment le chiffrement, la pseudonymisation ainsi que la capacité de garantir confidentialité, intégrité, disponibilité et résilience.

---

# 16.1 Encryption

## Data at Rest

Chiffrer :

```text
Databases
Backups
Laptops
Hard drives
USB devices
Cloud storage
```

Exemples :

```text
AES-256
Full Disk Encryption
Database encryption
```

---

## Data in Transit

Utiliser :

```text
TLS
HTTPS
VPN
SSH
```

Éviter :

```text
HTTP
FTP
Telnet
```

pour des communications sensibles.

---

# 16.2 Access Control

Appliquer :

> **Least Privilege**

Chaque utilisateur doit posséder uniquement les permissions nécessaires.

Exemple :

```text
Marketing employee
       ↓
marketing database access

NOT

administrator access to all production databases
```

---

# 16.3 MFA

Utiliser la :

**Multi-Factor Authentication**

particulièrement pour :

```text
Admin accounts
VPN
Cloud consoles
Email
Source code
Production systems
Remote access
```

---

# 16.4 Strong Authentication

Mesures :

```text
Strong password policy

No shared accounts

Password manager

Account lockout

Credential monitoring

MFA

Session expiration
```

---

# 16.5 Logging & Monitoring

Collecter les événements importants :

```text
Login
Failed login
Admin actions
Database access
Privilege changes
File access
API calls
Data exports
```

Centraliser idéalement dans un :

```text
SIEM
```

Exemples :

```text
Microsoft Sentinel
Splunk
Elastic
Wazuh
```

---

# 16.6 Network Segmentation

Éviter :

```text
Internet
   ↓
One flat network
   ↓
All databases
```

Préférer :

```text
Internet
   ↓
DMZ
   ↓
Application network
   ↓
Database network
   ↓
Restricted management network
```

---

# 16.7 Backups

Appliquer une stratégie de sauvegarde robuste.

Exemple :

**3-2-1**

```text
3 copies

2 different media

1 copy off-site/offline
```

Tester régulièrement la restauration.

Une sauvegarde inutilisable n'est pas une sauvegarde fiable.

---

# 16.8 Patch Management

Maintenir à jour :

```text
Operating systems
Applications
Libraries
Containers
Frameworks
Firmware
Network appliances
```

Processus :

```text
Inventory
   ↓
Vulnerability detection
   ↓
Prioritization
   ↓
Patch
   ↓
Validation
```

---

# 16.9 Vulnerability Management

Utiliser :

```text
Vulnerability scanners
SAST
DAST
Dependency scanning
Container scanning
Cloud security tools
Penetration tests
```

---

# 16.10 Data Loss Prevention

**DLP = Data Loss Prevention**

Peut détecter :

```text
Mass downloads
Sensitive files sent externally
Credit card numbers
Personal information
Unauthorized USB transfer
```

---

# 16.11 Pseudonymisation

Remplacer les identifiants directs.

Avant :

```text
User ID: 574
Name: Alice Martin
Medical result: ...
```

Après :

```text
Subject ID: A94F7B2
Medical result: ...
```

La correspondance est stockée séparément.

Attention :

> des données pseudonymisées restent généralement des données personnelles lorsqu'une réidentification reste possible.

---

# 16.12 Anonymisation

Avec une anonymisation réellement effective :

```text
Person cannot reasonably be re-identified
```

La différence est donc essentielle :

```text
Pseudonymisation
      ↓
Re-identification possible

Anonymisation
      ↓
Re-identification non raisonnablement possible
```

---

# 17. Mesures organisationnelles

La protection ne repose jamais uniquement sur des outils techniques.

---

# 17.1 Security Policies

Créer des politiques concernant :

```text
Access control
Password management
Data classification
Incident response
Remote working
Backups
Retention
Third parties
Acceptable use
Data breach response
```

---

# 17.2 Awareness Training

Former les employés sur :

```text
Phishing
Social engineering
Password security
Sensitive data handling
Reporting incidents
Physical security
Privacy
GDPR
```

Une entreprise peut disposer d'un excellent firewall et subir une violation à cause :

```text
d'un mot de passe communiqué lors d'un phishing
```

---

# 17.3 Incident Response Exercises

Réaliser des :

```text
Tabletop exercises
Simulations
Purple-team exercises
Breach drills
```

Exemple :

```text
09:00

A database containing 50,000 customers
has been stolen.

What do you do?
```

L'équipe doit savoir :

```text
qui appeler
qui décide
qui enquête
qui contacte le DPO
qui notifie
```

---

# 17.4 Audits

Effectuer régulièrement :

```text
Security audits
GDPR audits
Access reviews
Vendor reviews
Configuration reviews
Penetration tests
```

---

# 17.5 Data Inventory

Une organisation doit savoir :

```text
What data do we have?

Where?

Why?

Who can access it?

Who receives it?

How long is it stored?

How is it protected?
```

Impossible de protéger correctement une donnée dont l'organisation ignore l'existence.

---

# 17.6 Data Retention Policy

Définir des durées.

Exemple :

```text
Customer account:
while active + defined legal retention

Temporary logs:
90 days

Inactive candidate information:
defined HR retention

Temporary uploaded documents:
30 days
```

Les durées exactes doivent être déterminées en fonction des finalités et des obligations légales applicables.

---

# 18. DPIA / AIPD

**DPIA**

= Data Protection Impact Assessment

En français :

**AIPD**

= Analyse d'Impact relative à la Protection des Données

---

## Quand ?

Lorsqu'un traitement est susceptible d'engendrer :

> **un risque élevé pour les droits et libertés des personnes.**

La CNIL rappelle qu'une AIPD doit notamment décrire le traitement et ses finalités, évaluer sa nécessité et sa proportionnalité, analyser les risques et déterminer les mesures permettant de traiter ces risques.

---

## Exemple

Un système utilisant :

```text
Facial recognition

+

Employee monitoring

+

Large-scale processing
```

peut présenter un risque élevé.

Il peut être nécessaire d'effectuer :

```text
DPIA
```

avant le déploiement.

---

## Structure simplifiée

```text
1. Describe processing

2. Identify data

3. Identify purposes

4. Evaluate necessity

5. Identify threats

6. Assess risks

7. Define controls

8. Determine residual risk

9. Approve / modify / reject processing
```

---

# 19. Gestion des sous-traitants

Les prestataires peuvent constituer un risque important.

Exemple :

```text
Company
   ↓
CRM SaaS
   ↓
Cloud provider
   ↓
Third-party backup provider
```

Les données peuvent traverser plusieurs organisations.

---

## Avant de choisir un fournisseur

Évaluer :

```text
Security controls
Certifications
Data location
Encryption
Access controls
Incident response
Backup policy
Sub-processors
Deletion procedures
Breach notification process
```

---

## Contrats

Les contrats doivent définir clairement :

```text
responsibilities
security requirements
data processing rules
breach reporting
data deletion
audit rights
sub-processing
```

---

# 20. Post-Incident Activity

Une violation ne se termine pas lorsque le serveur revient en ligne.

Il faut réaliser un :

# Lessons Learned

---

## Questions à poser

```text
What happened?

Why?

How was it detected?

Why did the controls fail?

Could we have detected it earlier?

Was containment fast enough?

Did communication work?

Was the DPO informed quickly?

Was notification performed correctly?

What must change?
```

---

# Root Cause Analysis

Exemple :

```text
Data breach
   ↓
Compromised admin account
   ↓
Credential phishing
   ↓
No MFA
```

La véritable action corrective n'est donc pas seulement :

```text
Reset password
```

mais potentiellement :

```text
Enable MFA

Improve phishing training

Conditional access

Privileged access management

Better monitoring
```

---

# 20.1 Corrective Action Plan

Exemple :

| Finding | Action | Owner | Priority |
|---|---|---|---|
| MFA absent | Deploy MFA | IT | Critical |
| Logs incomplets | Improve SIEM | SOC | High |
| Employees non formés | Security training | HR | High |
| Excessive permissions | Access review | IT | High |
| Old software | Patch | IT | Critical |

---

# 20.2 Update Documentation

Après l'incident :

```text
Update IR Plan

Update GDPR policies

Update risk register

Update processing register

Update DPIA

Update security architecture

Update training

Update supplier requirements
```

---

# 21. Construire une culture de protection des données

La protection des données doit devenir :

> **un processus continu et non un exercice annuel de conformité.**

---

## Mauvaise culture

```text
"GDPR is the DPO's problem."
```

---

## Bonne culture

```text
Developer
   ↓
Secure code

System administrator
   ↓
Secure configuration

Employee
   ↓
Protect information

Manager
   ↓
Enforce processes

Security Team
   ↓
Monitor threats

DPO
   ↓
Advise and monitor compliance
```

---

# 21.1 Continuous Monitoring

Surveiller :

```text
Threats
Vulnerabilities
Access
Logs
Cloud configuration
Data transfers
Third-party risk
Regulatory changes
```

---

# 21.2 Continuous Improvement

Cycle :

```text
PLAN
 ↓
DO
 ↓
CHECK
 ↓
ACT
 ↓
PLAN...
```

Ce principe est similaire à celui rencontré dans :

```text
ISO 27001
```

---

# 22. Scénario complet

## Situation

Une entreprise e-commerce possède :

```text
100,000 customers
```

Une alerte SIEM détecte :

```text
03:12
Large database export

03:15
Unknown foreign IP

03:18
Admin authentication
```

---

## Étape 1 — Detection

Le SOC constate qu'un compte administrateur a été compromis.

---

## Étape 2 — Containment

```text
Disable account

Block IP

Revoke sessions

Rotate credentials

Isolate server
```

---

## Étape 3 — Investigation

La forensic investigation montre :

```text
SQL database exported
```

Données :

```text
Names
Emails
Addresses
Phone numbers
Password hashes
```

Personnes concernées :

```text
~37,000
```

---

## Étape 4 — DPO

Le DPO est informé.

L'équipe évalue :

```text
Confidentiality breach

Large number of users

Identity information

Passwords involved

Potential phishing

Potential account takeover
```

---

## Étape 5 — Risk Assessment

Conclusion :

```text
Risk to rights and freedoms
```

voire :

```text
High risk
```

selon les circonstances et protections existantes.

---

## Étape 6 — CNIL

Si le seuil de risque est atteint :

```text
Notify CNIL

≤ 72 hours after awareness
```

---

## Étape 7 — Users

Si le risque est considéré élevé :

```text
Notify affected users
without undue delay
```

---

## Étape 8 — Mitigation

```text
Force password reset

Enable MFA

Monitor fraud

Provide phishing warning

Increase authentication monitoring
```

---

## Étape 9 — Root Cause

Cause :

```text
Phishing
   ↓
Admin credentials stolen
   ↓
No MFA
```

---

## Étape 10 — Corrective measures

```text
Mandatory MFA

Privileged Access Management

Phishing-resistant authentication

Admin network restrictions

Enhanced monitoring

Security awareness training
```

---

# 23. Checklist de réponse à une violation

## 0–1 heure

- [ ] Confirmer l'incident.
- [ ] Activer l'Incident Response Team.
- [ ] Informer le Security Lead.
- [ ] Informer le DPO.
- [ ] Limiter l'accès compromis.
- [ ] Préserver les preuves.
- [ ] Commencer une timeline.

---

## 1–24 heures

- [ ] Identifier les systèmes concernés.
- [ ] Identifier les données concernées.
- [ ] Estimer le nombre de victimes.
- [ ] Identifier la cause.
- [ ] Vérifier une éventuelle exfiltration.
- [ ] Évaluer les conséquences.
- [ ] Déterminer le niveau de risque.
- [ ] Documenter toutes les décisions.

---

## Avant 72 heures

Si risque pour les personnes :

- [ ] Préparer la notification.
- [ ] Notifier la CNIL.
- [ ] Expliquer tout éventuel retard.
- [ ] Effectuer une notification initiale même si l'enquête n'est pas terminée.
- [ ] Prévoir une notification complémentaire.

Si risque élevé :

- [ ] Préparer la communication aux personnes concernées.
- [ ] Expliquer clairement la violation.
- [ ] Donner des recommandations de protection.

---

## Après containment

- [ ] Supprimer la persistance.
- [ ] Corriger la vulnérabilité.
- [ ] Révoquer les credentials compromis.
- [ ] Restaurer les services.
- [ ] Augmenter la surveillance.

---

## Post-Incident

- [ ] Organiser un Lessons Learned.
- [ ] Réaliser un Root Cause Analysis.
- [ ] Mettre à jour le Risk Register.
- [ ] Mettre à jour les procédures.
- [ ] Mettre à jour les contrôles.
- [ ] Former les employés.
- [ ] Tester les mesures correctives.
- [ ] Réévaluer les fournisseurs concernés.

---

# 24. Points à connaître par cœur

## Question : Qu'est-ce qu'une donnée personnelle ?

> Toute information concernant une personne physique identifiée ou identifiable.

---

## Question : Qu'est-ce qu'un traitement ?

> Toute opération réalisée sur des données personnelles : collecte, stockage, consultation, modification, transmission ou suppression.

---

## Question : Qui est le Data Controller ?

> L'organisation qui détermine les finalités et les moyens du traitement.

---

## Question : Qui est le Data Processor ?

> L'organisation qui traite les données pour le compte du responsable du traitement.

---

## Question : Qu'est-ce qu'un DPO ?

> Le Data Protection Officer conseille l'organisation, surveille la conformité et sert notamment de point de contact avec l'autorité de contrôle.

---

## Question : Quels sont les trois types principaux de violation ?

```text
Confidentiality
Integrity
Availability
```

---

## Question : Toutes les violations doivent-elles être documentées ?

> **Oui.**

---

## Question : Quand notifier la CNIL ?

Lorsqu'une violation :

> est susceptible d'engendrer un **risque pour les droits et libertés** des personnes.

---

## Question : Quel est le délai ?

> **Dans les meilleurs délais et, si possible, maximum 72 heures après avoir pris connaissance de la violation.**

---

## Question : Doit-on attendre d'avoir terminé l'enquête ?

> **Non.**

Une notification initiale peut être complétée ultérieurement.

---

## Question : Quand informer les personnes ?

Lorsque la violation est susceptible d'engendrer :

> **un risque élevé pour leurs droits et libertés.**

---

# La règle la plus importante

```text
                  PERSONAL DATA BREACH
                          │
                          ▼
                 Document internally
                          │
                          ▼
                 Assess the risk
                          │
          ┌───────────────┼───────────────┐
          │               │               │
          ▼               ▼               ▼
       No risk           Risk         High risk
          │               │               │
          ▼               ▼               ▼
      Document        Document         Document
                          │               │
                          ▼               ▼
                     Notify CNIL      Notify CNIL
                       ≤ 72h            ≤ 72h
                                          │
                                          ▼
                                   Notify individuals
                                  without undue delay
```

---

# Sécurité : règle à retenir

Le RGPD ne dit pas :

```text
"Installez le firewall X
et l'antivirus Y."
```

Il impose plutôt :

> un **niveau de sécurité approprié au risque**.

Cela implique une approche basée sur :

```text
Risk
  ↓
Appropriate controls
  ↓
Continuous evaluation
```

L'article 32 cite notamment :

```text
Encryption
Pseudonymisation
Confidentiality
Integrity
Availability
Resilience
Recovery capabilities
Regular testing
```



---

# Résumé final

Le RGPD repose sur cinq idées centrales :

```text
1. Know your data

2. Collect only what you need

3. Protect it

4. Respect people's rights

5. Be able to prove what you did
```

Lors d'une violation :

```text
Detect
   ↓
Contain
   ↓
Preserve evidence
   ↓
Investigate
   ↓
Identify personal data
   ↓
Assess risk
   ↓
Document
   ↓
Notify if required
   ↓
Recover
   ↓
Improve
```

Et la règle essentielle concernant la notification est :

```text
No risk
→ Internal documentation

Risk
→ Documentation + CNIL ≤ 72h

High risk
→ Documentation + CNIL ≤ 72h
  + affected individuals without undue delay
```

La protection des données ne doit pas être considérée comme une simple obligation administrative.

Elle combine :

```text
Privacy
+
Cybersecurity
+
Risk Management
+
Incident Response
+
Governance
+
Legal Compliance
```

---

# 25. Références

## Textes officiels

- **Regulation (EU) 2016/679 — General Data Protection Regulation (GDPR)**.
- **CNIL — Règlement général sur la protection des données**.
- **Article 5 — Principles relating to processing of personal data**.
- **Article 6 — Lawfulness of processing**.
- **Article 32 — Security of processing**.
- **Article 33 — Notification of a personal data breach to the supervisory authority**.
- **Article 34 — Communication of a personal data breach to the data subject**.
- **Article 35 — Data Protection Impact Assessment**.
- **Article 39 — Tasks of the Data Protection Officer**.

## CNIL

- **Notifier une violation de données personnelles**.
- **Violations de données personnelles : les règles à suivre**.
- **Sécurité : gérer les incidents et les violations**.
- **Les six grands principes du RGPD**.
- **Les bases légales du traitement**.
- **Les durées de conservation des données**.

## Ressources du projet Holberton

- *Complete guide to GDPR compliance*
- *General Data Protection Regulation (GDPR)*
- *Data Protection Best Practices*
- *Regulation (EU) 2016/679*
- *GDPR checklist for data controllers*
- *7 Security Controls You Need For General Data Protection Regulation*