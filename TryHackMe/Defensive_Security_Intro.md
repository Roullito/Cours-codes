# TryHackMe – Defensive Security Intro

Ce deuxième module TryHackMe t’introduit au monde de la **defensive security** (la “blue team”) : ceux qui protègent les systèmes, surveillent les réseaux et réagissent quand un incident se produit.

Ce cours reprend le contenu de la room *Defensive Security Intro* en le structurant comme un mini-chapitre de formation.

---

## Objectifs du cours

À la fin de ce module, tu dois être capable de :

- Expliquer ce qu’est la **defensive security** et la différence avec l’offensive security.
- Connaître les **grands types de missions** d’une équipe de défense (blue team).
- Comprendre le rôle d’un **SOC (Security Operations Center)**.
- Expliquer à quoi sert la **digital forensics** (informatique légale).
- Décrire les grandes étapes du **processus d’Incident Response**.
- Comprendre la notion de **SIEM** et en quoi c’est un outil central pour un analyste.

---

## 1. Qu’est-ce que la Defensive Security ?

### 1.1 Blue team vs Red team

En simplifiant :

- **Red team / Offensive security**
  → Attaque les systèmes (pentest, exploitation de failles) pour les tester.

- **Blue team / Defensive security**
  → Défend les systèmes : prépare, protège, surveille, alerte et réagit.

La **defensive security** a deux grandes missions :

1. **Empêcher les intrusions**
2. **Détecter les intrusions qui passent quand même et y répondre**

Il ne s’agit pas seulement de “poser un antivirus”, mais de construire un **écosystème complet** de protection.

---

## 2. Les grandes tâches de la Defensive Security

Dans une vraie organisation, la défense se décline en plusieurs axes complémentaires.

### 2.1 Cyber Security Awareness (sensibilisation)

> “L’utilisateur est souvent le maillon le plus faible… mais aussi le plus stratégique.”

La sensibilisation consiste à :

- Former les utilisateurs aux risques : **phishing, malware, social engineering**, mots de passe, etc.
- Leur apprendre à **reconnaître un mail suspect**, un lien douteux, un comportement anormal.
- Leur expliquer **quoi faire** en cas de doute (ne pas cliquer, prévenir l’IT, etc.).

L’objectif : réduire la probabilité qu’un utilisateur **tombe dans un piège** et ouvre la porte à un attaquant.

---

### 2.2 Documenter & gérer les assets (inventaire)

> “Tu ne peux pas protéger ce que tu ne connais pas.”

Un **asset** = un bien numérique ou matériel de l’entreprise :

- Machines (serveurs, PC, laptops, smartphones),
- Applications, services, bases de données,
- Équipements réseau (switch, routeur, firewall…),
- Données sensibles (bases clients, secrets, clés…).

La blue team doit :

- Maintenir un **inventaire** des systèmes (quoi ? où ? qui ?).
- Connaître les **versions**, les **adresses IP**, les **rôles** (serveur web, BDD, etc.).
- Identifier les assets **critiques** (ceux qui, s’ils tombent, font très mal au business).

Sans inventaire, tu as des “machines fantômes” qui ne sont ni surveillées ni mises à jour → parfaites cibles.

---

### 2.3 Préventif : Firewalls & Intrusion Prevention Systems (IPS)

Les **firewalls** et **IPS** sont souvent la **première ligne de défense**.

- **Firewall** : filtre le trafic réseau selon des règles (IP, port, protocole…).
  → “Ce type de trafic a le droit d’entrer/sortir, celui-là non.”

- **IPS (Intrusion Prevention System)** : analyse le trafic à la recherche de **signatures d’attaque** ou de comportements anormaux, et peut bloquer en temps réel.
  → “Ce paquet ressemble à une attaque connue, je le bloque.”

Objectif :

- **Limiter la surface d’attaque** dès l’entrée du réseau.
- Empêcher des attaques basiques ou connues d’atteindre les serveurs internes.

---

### 2.4 Logging & Monitoring (journaux & surveillance)

> “Si tu ne logues rien, tu ne vois rien. Si tu logues mal, tu ne comprends rien.”

Les **logs** sont les journaux d’activité :

- Connexions, erreurs, requêtes web, accès fichiers, authentifications, etc.

La blue team doit :

1. **Activer et configurer le logging** sur les systèmes, applis, équipements réseau.
2. **Centraliser** ces logs (par exemple dans un SIEM).
3. **Surveiller** les logs en temps réel ou quasi-réel pour :
   - repérer des comportements suspects,
   - détecter des intrusions ou tentatives d’attaque,
   - reconstruire ce qui s’est passé après coup.

Sans logs, une attaque peut se dérouler **sans aucune trace exploitable** → impossible d’enquêter correctement.

---

### 2.5 Frameworks, politiques & procédures

La sécurité, ce n’est pas que de la technique, c’est aussi des **règles** :

- **Politiques de sécurité** : règles d’usage des systèmes, mots de passe, accès à distance, utilisation des données, etc.
- **Procédures** : comment réagir en cas de fuite de données, de ransomware, de compromission de compte, etc.
- **Frameworks** : modèles/références (ISO 27001, NIST, etc.) qui aident à structurer la sécurité de manière globale.

Objectif :

- Que tout le monde sache **ce qu’il a le droit de faire** ou non,
- Que l’organisation sache **comment réagir** face à un incident,
- Assurer une **cohérence** de la sécurité à travers toute l’entreprise.

---

## 3. Le Security Operations Centre (SOC)

Le **SOC** (Security Operations Centre) est le **cœur de la surveillance** de la sécurité d’une organisation.

Tu peux le voir comme une **tour de contrôle** :

- Des analystes y surveillent les alertes,
- Enquêtent sur les événements suspects,
- Coordonnent la réponse en cas d’attaque.

### 3.1 Missions principales d’un SOC

Un SOC se concentre notamment sur :

#### a) Tendances & vulnérabilités

- Suivre les **nouvelles failles** découvertes (CVE, zero-days…),
- Rester au courant des **nouveaux types d’attaques**,
- Adapter les **règles de détection** et les défenses en fonction de l’actualité.

#### b) Violations de politiques

- Une **politique de sécurité** définit ce qui est autorisé ou non (ex : pas de stockage de données clients en local, pas de partage de compte, etc.).
- Le SOC surveille les **violations** : comportements non conformes aux règles internes.

Exemple :
Un employé copie des données sensibles sur un service cloud personnel → alerte.

#### c) Activité non autorisée & illégale

Le SOC définit un **baseline** : ce qui est “normal” pour le réseau (volumes, horaires, types d’accès…).
Ensuite :

- Toute activité qui sort de ce modèle est suspecte,
- L’activité illégale (ex : exfiltration massive de données) doit être détectée et traitée.

#### d) Détection d’intrusions & de brèches

Même avec une bonne sécurité, **le risque zéro n’existe pas**.

Le SOC doit :

- Détecter rapidement les intrusions,
- Comprendre ce qui se passe (type d’attaque, périmètre impacté),
- Alerter et collaborer avec les équipes **Incident Response**.

---

## 4. Digital Forensics (informatique légale)

La **digital forensics** applique les principes de la criminologie/forensique au monde numérique.

Objectifs :

- **Préserver**, analyser et interpréter les preuves numériques,
- Aider à comprendre **comment** un incident s’est produit,
- Parfois, fournir des preuves pour **des procédures légales**.

### 4.1 Sources d’indices en forensique

#### a) File system (système de fichiers)

Analyse d’une copie *bas niveau* du disque :

- Programmes installés,
- Fichiers créés, modifiés, supprimés,
- Fichiers partiellement écrasés mais encore récupérables,
- Métadonnées (dates de création, modification, propriétaire…).

#### b) System memory (mémoire vive)

Un attaquant peut :

- Lancer un malware **uniquement en mémoire** (sans écrire sur disque),
- Charger des payloads, des clés, ou des secrets en RAM.

En analysant un **dump mémoire**, on peut :

- Retrouver des processus malveillants,
- Voir des clés de chiffrement, des commandes, des connexions actives.

#### c) System logs (journaux système)

Les logs système peuvent montrer :

- Connexions réussies et échouées,
- Changements de droits,
- Élévation de privilèges,
- Installations de logiciels, etc.

Même si l’attaquant tente de **effacer ses traces**, il reste souvent des indices dispersés.

#### d) Network logs (journaux réseau)

Les logs réseau (pare-feu, proxies, équipements) permettent de :

- Voir quelles connexions sont passées,
- Identifier des transferts de données suspects,
- Relier des actions sur une machine à une **adresse IP externe**.

---

## 5. Incident Response (IR) – Réponse aux incidents

Quand un incident survient (intrusion, ransomware, fuite de données…), il faut **savoir quoi faire**.

L’**Incident Response** est un processus structuré, souvent découpé en grandes étapes.

### 5.1 Les 4 grandes phases

1. **Preparation (Préparation)**
2. **Detection & Analysis (Détection & Analyse)**
3. **Containment, Eradication & Recovery (Confinement, Éradication & Récupération)**
4. **Post-Incident Activity (Retour d’expérience)**

#### 1) Preparation

Mettre en place **tout ce qui sera utile** le jour où ça chauffe :

- Créer une équipe d’Incident Response (rôles, responsabilités),
- Documenter les **procédures**, les moyens de communication de crise,
- Installer et configurer les outils nécessaires (SIEM, IDS, EDR…),
- Former les employés (ex : **phishing awareness**).

L’idée : ne pas improviser le jour J.

#### 2) Detection & Analysis

Quand un événement suspect survient :

- Les outils (SIEM, IDS, EDR, antivirus, etc.) génèrent des **alertes**,
- On collecte des **indicateurs** : logs, traces, comportement réseau,
- On évalue :
  - **La nature de l’incident** (intrusion, malware, fuite, etc.),
  - **La portée** (combien de systèmes, quelles données),
  - **La gravité** (impact business et technique).

#### 3) Containment, Eradication & Recovery

Trois sous-étapes :

- **Containment (Confinement)**
  Limiter la propagation et l’impact :
  - Isoler des machines,
  - Bloquer des IP, comptes, accès.

- **Eradication**
  Supprimer la cause de l’incident :
  - Nettoyer ou réinstaller les systèmes,
  - Retirer les malwares, backdoors, comptes créés par l’attaquant.

- **Recovery (Récupération)**
  Rétablir les services de façon contrôlée :
  - Restaurer depuis des sauvegardes,
  - Réouvrir le réseau petit à petit,
  - Surveiller de près après le retour à la normale.

#### 4) Post-Incident Activity (Retour d’expérience)

Une fois l’incident terminé, on **analyse à froid** :

- Comment l’incident est-il arrivé ?
- Qu’est-ce qui a bien fonctionné dans la réponse ?
- Qu’est-ce qui a mal fonctionné ?
- Quelles **améliorations** mettre en place (techniques, organisationnelles, humaines) ?
- Faut-il renforcer la **sensibilisation** ou ajuster les politiques ?

Objectif : chaque incident doit **renforcer** l’organisation, pas juste être “éteint”.

---

## 6. SIEM & rôle d’un analyste SOC

### 6.1 Qu’est-ce qu’un SIEM ?

Un **SIEM** (*Security Information and Event Management*) est une plateforme qui :

- **Collecte** les logs et événements de sécurité de multiples sources :
  - Firewalls, serveurs, systèmes, bases de données, applications, etc.
- **Corrèle** ces données pour repérer des patterns suspects,
- Génère des **alertes** quand des comportements anormaux sont détectés,
- Fournit des **dashboards** et des vues pour enquêter.

Tu peux voir le SIEM comme :

> “La console centrale où la blue team voit tout ce qui se passe.”

### 6.2 Scénario TryHackMe

Dans ce module, tu incarnes un **analyste SOC** :

- Tu as accès à une interface qui simule un SIEM (un “static site” dans TryHackMe).
- Tu dois :
  - Parcourir les événements,
  - Identifier des indices,
  - Suivre un mini-scénario jusqu’à trouver un **flag** (preuve que tu as suivi le bon chemin d’analyse).

Ce type de scénario reflète le travail réel d’un analyste SOC :

- Filtrer les **faux positifs** (alertes bruit),
- Se concentrer sur les événements **vraiment dangereux**,
- Reconstituer une **histoire** à partir de logs bruts.

---

## 7. Fiche mémo – Defensive Security

### Missions clés de la blue team

- Sensibiliser les utilisateurs (phishing, social engineering, bonnes pratiques).
- Inventorier et gérer les assets de l’organisation.
- Mettre en place firewalls, IPS, antivirus, EDR…
- Configurer et surveiller les logs (systèmes, applis, réseau).
- Définir politiques, procédures, et respecter des frameworks de sécurité.
- Surveiller continuellement (SOC + SIEM).
- Enquêter (forensics) et répondre aux incidents (Incident Response).

### Vision d’ensemble

- **Offensive security** → trouve les failles en attaquant.
- **Defensive security** → construit et maintient la muraille, détecte les assauts, réagit.

Les deux sont **complémentaires** : une bonne sécurité globale a besoin des deux.

---

## 8. Glossaire

- **Blue team** : équipe de défense, chargée de protéger les systèmes.
- **Red team** : équipe offensive/pentest, simule l’attaquant.
- **Asset** : ressource de l’organisation (machine, service, donnée…).
- **Firewall** : équipement/logiciel qui filtre le trafic réseau selon des règles.
- **IPS (Intrusion Prevention System)** : outil qui détecte et bloque des attaques en temps réel.
- **Log** : journal d’événements (connexions, requêtes, erreurs…).
- **Security policy** : document qui définit les règles d’usage et de protection des systèmes.
- **SOC (Security Operations Centre)** : centre opérationnel de surveillance de la sécurité.
- **Digital forensics** : analyse de preuves numériques pour comprendre un incident.
- **Incident Response (IR)** : processus de gestion et de réponse aux incidents.
- **SIEM** : plateforme de collecte, corrélation et analyse des logs de sécurité.

---

Tu peux maintenant :

- Relire ce cours comme “fiche de référence blue team”,
- Refaire le module TryHackMe en ayant la **vision d’ensemble**,
- Et ensuite passer à des rooms plus avancées (SOC, SIEM, forensics, IR pratiques, etc.).
