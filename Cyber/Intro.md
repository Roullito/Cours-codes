# Introduction à la Cybersécurité

## 0. Contexte du projet

Ce module "Introduction to Cyber Security" a pour but de te donner les bases nécessaires pour :

- Comprendre **ce qu’est la cybersécurité**
- Expliquer les **principes fondamentaux** (CIA Triad)
- Identifier les **menaces principales**
- Comprendre les **cadres / frameworks** (NIST, OWASP, etc.)
- Situer l’**ethical hacking** et **Kali Linux**

À la fin, tu dois être capable d’expliquer ces notions **à l’oral, sans Google**.

---

## 1. Qu’est-ce que la cybersécurité ?

### 1.1 Définition simple

> **La cybersécurité, c’est l’ensemble des pratiques, technologies et règles qui protègent :**
> - les **systèmes informatiques** (ordinateurs, serveurs, smartphones, objets connectés),
> - les **réseaux** (Internet, réseaux d’entreprise),
> - les **données** (fichiers, bases de données, identifiants, cartes bancaires),
> **contre les attaques, les dommages, les fuites et les accès non autorisés.**

Dit autrement :
La cybersécurité sert à **empêcher les “méchants”** (hackers malveillants, employés négligents, malware…) de :

- voler des données,
- modifier des données,
- détruire des données,
- bloquer des services,
- se faire passer pour quelqu’un d’autre.

### 1.2 Pourquoi c’est important ?

Quelques exemples concrets :

- Vol de numéro de carte bancaire lors d’un achat en ligne.
- Piratage d’un compte Instagram / mail.
- Rançongiciel (ransomware) dans un hôpital qui bloque tous les dossiers patients.
- Fuite de données personnelles d’une entreprise (mots de passe, numéros de sécu, etc.).

Les impacts possibles :

- Perte d’argent
- Perte de confiance des clients
- Pertes de données critiques
- Problèmes juridiques (RGPD, etc.)
- Parfois impact sur la vie réelle (hôpitaux, transports, électricité…)

---

## 2. Les grands domaines de la cybersécurité

Sans entrer dans les détails, on peut découper la cybersécurité en plusieurs grands types (les formulations varient, mais l’idée générale est la même) :

1. **Sécurité des réseaux (Network Security)**
   - Protéger les routeurs, switches, firewalls, VPN…
   - Exemple : filtrer le trafic avec un pare-feu, segmenter le réseau.

2. **Sécurité des applications (Application Security)**
   - Protéger les sites web, API, applications mobiles / desktop.
   - Exemple : se défendre contre **SQL injection**, **XSS**, **CSRF**, etc.

3. **Sécurité des endpoints (Endpoint Security)**
   - Protéger les machines utilisateur : PC, smartphones, serveurs…
   - Exemple : antivirus, EDR, mises à jour, chiffrement du disque.

4. **Sécurité des données (Data Security)**
   - Protéger les données stockées ou en transit (chiffrement, backup, contrôle d’accès).
   - Exemple : chiffrer une base de données ou des disques.

5. **Sécurité opérationnelle / gouvernance (Operational Security / Governance)**
   - Politiques, procédures, gestion des risques, formation des utilisateurs.
   - Exemple : politique de mots de passe, gestion des incidents, audits.

---

## 3. Les principes fondamentaux : la CIA Triad

Le **cœur de la cybersécurité**, ce sont 3 grands principes, souvent représentés par un triangle : **C I A**.

### 3.1 Confidentialité (Confidentiality)

> **La bonne information, seulement pour les bonnes personnes.**

- Empêcher qu’une personne non autorisée **accède** à l’information.
- Exemples :
  - Messages chiffrés (HTTPS, VPN…)
  - Gestion des droits d’accès (un simple employé n’a pas accès aux salaires de toute l’entreprise)
  - Masquage d’informations sensibles (numéro de carte tronqué : 1234 **** **** 5678)

### 3.2 Intégrité (Integrity)

> **L’information doit rester correcte, complète et non modifiée de manière non autorisée.**

- Empêcher qu’un attaquant **modifie** les données sans être détecté.
- Exemples :
  - Signatures numériques, hash (SHA-256, etc.)
  - Contrôle de version (journalisation des modifications)
  - Vérification d’intégrité des fichiers (checksums)

### 3.3 Disponibilité (Availability)

> **L’information et les services doivent être accessibles quand les utilisateurs autorisés en ont besoin.**

- Éviter ou limiter :
  - Pannes,
  - Attaques de type DoS / DDoS (saturation d’un service),
  - Pannes d’électricité, incendies, etc.
- Moyens :
  - Redondance (serveurs en double, backups),
  - Plan de reprise d’activité,
  - Surdimensionnement de l’infrastructure.

---

## 4. Menaces, vulnérabilités et risques

Avant de parler de types d’attaques, il faut distinguer trois notions importantes :

- **Menace (Threat)**
  Tout ce qui **pourrait** causer un dommage.
  Exemple : un pirate, un ransomware, un employé négligent, une catastrophe naturelle.

- **Vulnérabilité (Vulnerability)**
  Une **faiblesse** dans un système, un processus ou un humain.
  Exemple : mot de passe “123456”, logiciel non mis à jour, serveur mal configuré.

- **Risque (Risk)**
  Le **niveau de danger** résultant de la combinaison :
  > *Probabilité qu’une menace exploite une vulnérabilité* × *Impact si ça arrive.*

L’objectif de la cybersécurité est de **réduire le risque** à un niveau acceptable :
- soit en réduisant les vulnérabilités,
- soit en réduisant l’impact,
- soit les deux.

---

## 5. Types de menaces et attaques

### 5.1 Malware (logiciels malveillants)

- **Virus**
  - Programme malveillant qui **a besoin d’un fichier hôte** pour se propager (ex. un .exe infecté, un document avec macro).
  - Il se déclenche quand l’utilisateur exécute le fichier.

- **Worm (ver)**
  - Programme capable de **se propager tout seul sur le réseau**, sans intervention de l’utilisateur.
  - Il exploite des failles dans les systèmes ou services réseau.
  - Ex : un ver scanne Internet à la recherche de machines vulnérables et les infecte automatiquement.

> 🔑 **Différence simple :**
> - **Virus** : nécessite l’action d’un utilisateur (ouvrir un fichier infecté).
> - **Worm** : se propage **automatiquement** à travers le réseau.

- **Trojan (cheval de Troie)** : programme qui se fait passer pour légitime (un jeu, une app crackée…) mais contient du code malveillant.
- **Ransomware** : chiffre les fichiers et demande une rançon.
- **Spyware / Keylogger** : espionne l’utilisateur, enregistre les frappes clavier, etc.

### 5.2 Attaques réseau

- **DoS / DDoS** : saturer un serveur ou un service pour le rendre indisponible.
- **Man-in-the-Middle (MITM)** : intercepter la communication entre 2 parties.
- **Sniffing** : capture de paquets réseau non chiffrés (ex : Wi-Fi ouvert).

### 5.3 Attaques applicatives (surtout web)

- **SQL Injection** : injection de code SQL dans une requête pour lire/modifier des données.
- **XSS (Cross-Site Scripting)** : injection de JavaScript dans une page vue par d’autres utilisateurs.
- **CSRF (Cross-Site Request Forgery)** : faire exécuter à un utilisateur authentifié une action qu’il n’a pas voulu.

### 5.4 Menaces humaines : l’ingénierie sociale

#### Qu’est-ce que le social engineering ?

> **L’ingénierie sociale, c’est l’art de manipuler les gens pour leur faire faire quelque chose ou révéler des informations, sans forcément passer par une faille technique.**

Exemples :

- Email de phishing : “Votre compte bancaire est bloqué, cliquez ici pour le débloquer.”
- Appel d’un faux technicien IT qui demande votre mot de passe.
- Message sur les réseaux : “Tu peux me renvoyer le code reçu par SMS ?” (attaque sur l’authentification).

Le problème principal :
> **Le maillon le plus faible, souvent, c’est l’humain.**

---

## 6. Gestion des risques en cybersécurité (Risk Management)

### 6.1 Objectif

La **gestion des risques** consiste à :
1. Identifier ce qui est important à protéger (actifs : données, services, systèmes…),
2. Identifier les menaces et vulnérabilités,
3. Estimer le niveau de risque,
4. Décider quoi faire pour réduire ce risque.

### 6.2 Processus typique simplifié

1. **Inventaire des actifs** : que veut-on protéger ? (serveurs, bases de données, appli web, données clients…)
2. **Identification des menaces et vulnérabilités** :
   - Exemple : mot de passe faible, serveur exposé, logiciel non patché.
3. **Évaluation des risques** :
   - Probabilité × Impact.
4. **Traitement des risques** (4 stratégies classiques) :
   - **Réduire** (mettre en place des contrôles : chiffrement, patchs, firewalls…)
   - **Transférer** (assurance, sous-traitance)
   - **Accepter** (le risque est faible / acceptable)
   - **Éviter** (supprimer l’activité à risque)

5. **Surveillance et révision** :
   - Les technologies, les menaces et l’entreprise évoluent → le risque doit être réévalué régulièrement.

---

## 7. Politiques, programmes de sécurité et frameworks

### 7.1 Programme de sécurité de l’information

Un **programme de sécurité** dans une organisation inclut :

- **Politiques de sécurité** : règles générales (ex : politique de mot de passe, usage acceptable d’Internet).
- **Normes / standards** : “comment” on applique les politiques.
- **Procédures** : pas à pas concrets (ex : que faire en cas d’incident).
- **Formation des utilisateurs** : pour éviter les erreurs humaines.
- **Gestion des incidents** : détecter, répondre, corriger.
- **Audit & conformité** : vérifier que les règles sont respectées.

### 7.2 Rôle des frameworks (CISA, NIST, OWASP, SANS, ISF, ISC²)

- **NIST** : propose des frameworks de cybersécurité largement utilisés (ex : NIST Cybersecurity Framework) pour structurer l’analyse des risques, les contrôles, etc.
- **CISA** : agence américaine chargée de la cybersécurité et de la sécurité des infrastructures, fournit des guides, alertes, bonnes pratiques.
- **OWASP** : se concentre sur la **sécurité des applications web**.
  - Le **OWASP Top Ten** liste les **10 catégories de vulnérabilités web les plus critiques**.
  - Objectif : aider développeurs et entreprises à prioriser la sécurité applicative.
- **SANS** : organisme de formation et de recherche en cybersécurité, maintient des listes de bonnes pratiques (ex : CIS Controls).
- **ISF** : forum de sécurité de l’information, publie des référentiels et analyses.
- **ISC²** : organisme de certification (ex : CISSP) pour les professionnels de la cybersécurité.

> 🔑 **Idée clé :**
> Les frameworks et organismes ne "protègent" pas directement, mais ils donnent **des modèles, des standards et des bonnes pratiques** pour construire un programme de sécurité solide.

---

## 8. Contrôle d’accès et authentification

### 8.1 Rôle du contrôle d’accès

> **Le contrôle d’accès, c’est décider qui a le droit d’accéder à quoi, et dans quelles conditions.**

Il répond à deux questions :
1. **Qui es-tu ?** → **Authentification**
2. **Que peux-tu faire ?** → **Autorisation**

Exemples :
- Un utilisateur normal ne peut pas accéder au panneau d’administration.
- Un employé du service RH peut accéder aux fiches de salaire, mais pas un stagiaire en marketing.

### 8.2 Authentification et MFA

**Authentification = prouver son identité.**

Les facteurs classiques :

1. **Ce que tu sais** (mot de passe, PIN)
2. **Ce que tu as** (smartphone, token, carte à puce)
3. **Ce que tu es** (empreinte, visage, iris…)

> **Multi-Factor Authentication (MFA)** = combiner **au moins deux types** différents.
> Exemple : mot de passe **+** code SMS, ou mot de passe **+** app d’authentification.

**Pourquoi ça renforce la sécurité ?**

- Si un attaquant vole ton mot de passe, il lui manque encore ton téléphone.
- Si ton téléphone est volé, il lui manque ton mot de passe.

---

## 9. Chiffrement (Encryption)

### 9.1 Rôle du chiffrement

> **Le chiffrement transforme des données lisibles (texte en clair) en données illisibles (texte chiffré), de façon à ce que seules les personnes autorisées puissent les lire.**

Il participe surtout à :

- La **confidentialité** (personne ne comprend les données sans la clé),
- Parfois à l’**intégrité** (via signatures, MAC…).

Exemples :

- HTTPS : ton navigateur chiffre les données entre toi et le site.
- Chiffrement disque complet (BitLocker, LUKS…) : si quelqu’un vole ton disque, il ne peut pas lire les fichiers.

---

## 10. Sécurisation d’un réseau (vue d’ensemble)

Quelques méthodes classiques :

- **Pare-feu (Firewall)** : filtre le trafic réseau (ports, IP, protocoles).
- **Segmentation réseau** : séparer les parties critiques (serveurs, prod) des parties moins sûres (Wi-Fi invité).
- **VPN** : tunnel chiffré pour accéder à distance au réseau de l’entreprise.
- **Wi-Fi sécurisé** : WPA2/WPA3, mots de passe forts.
- **IDS/IPS** : systèmes de détection/prévention d’intrusion.

---

## 11. Introduction à l’ethical hacking et Kali Linux

### 11.1 Ethical hacking

> **L’ethical hacker (ou “white hat”) utilise ses compétences de hacking pour aider à sécuriser les systèmes, avec l’autorisation du propriétaire.**

Types :

- **White hat** : légal, autorisé (tests d’intrusion, audit).
- **Black hat** : criminel, illégal.
- **Grey hat** : entre les deux (trouve des failles sans autorisation, mais ne fait pas forcément de dégâts).

Un **test d’intrusion** (pentest) suit typiquement ces phases :

1. **Reconnaissance** (collecte d’infos)
2. **Scan** (ports, services, versions)
3. **Exploitation** (exploiter une vulnérabilité)
4. **Post-exploitation** (maintenir l’accès, escalade de privilèges)
5. **Rapport** (documenter, proposer des mesures correctives)

### 11.2 Kali Linux

- Distribution Linux basée sur Debian.
- Spécialisée pour le **pentest**, avec **des centaines d’outils** :
  - Scan de ports (nmap),
  - Exploitation (Metasploit),
  - Attaques Wi-Fi,
  - Forçage de mots de passe, etc.
- Elle doit être utilisée **uniquement** :
  - dans des environnements de test,
  - avec **autorisation explicite**,
  - dans un cadre légal (cours, entreprise, labo Perso).

---

## 12. Mini checklist de révision (alignée avec le projet)

Tu dois pouvoir répondre **sans réfléchir trop longtemps** aux questions suivantes :

- Expliquer simplement :
  - Ce qu’est la cybersécurité.
  - La différence **menace / vulnérabilité / risque**.
- Donner la définition de la **CIA Triad** :
  - Confidentialité ?
  - Intégrité ?
  - Disponibilité ?
- Expliquer la différence principale entre un **virus** et un **worm**.
- Donner des exemples :
  - d’**ingénierie sociale**,
  - de **malware**,
  - d’attaques **réseau** et **web**.
- Expliquer à quoi sert :
  - le **chiffrement**,
  - le **contrôle d’accès**,
  - la **MFA**.
- Donner le rôle général de :
  - **NIST**, **OWASP**, **SANS**, **CISA**, **ISC²**.
- Expliquer en une phrase le **OWASP Top Ten**.
- Expliquer ce qu’est l’**ethical hacking** et le rôle de **Kali Linux**.

---

# Mise en place de l’environnement de cybersécurité (Kali + VM + VSCode)

Cette partie explique comment installer, configurer et utiliser les outils nécessaires pour travailler proprement sur les projets de cybersécurité :

- Machine virtuelle **Kali Linux**
- Hyperviseur (**VirtualBox** ou **VMware**)
- Éditeur de code (**VSCode**)
- Quelques outils de base dans Kali (terminal, apt, git…)

---

## 1. Vue d’ensemble de l’environnement

Avant de rentrer dans les commandes, il faut comprendre l’architecture :

- **Machine hôte** : ton PC principal (Windows / macOS / Linux)
- **Hyperviseur** : logiciel qui fait tourner des machines virtuelles
  - Exemples : **VirtualBox**, **VMware Workstation Player**
- **Machine virtuelle Kali** : un “PC dans le PC” dédié à la cybersécurité
- **VSCode** : éditeur de code que tu utilises sur ta machine hôte
  (et éventuellement connecté à Kali via SSH ou partage de fichiers)

L’idée :

> On **isole** toutes les expériences de hacking / outils de cybersécurité dans **Kali**, pour ne pas casser / polluer ton système principal.

---

## 2. Installation de la machine virtuelle Kali

### 2.1 Installer l’hyperviseur

1. Télécharger et installer **VirtualBox** (recommandé pour débuter) ou **VMware Workstation Player**.
2. Redémarrer la machine si nécessaire.

### 2.2 Récupérer l’image de Kali

1. Télécharger l’ISO de **Kali Linux 64 bits (Installer)**.
2. Vérifier que tu disposes d’au moins :
   - **4 Go de RAM** à allouer à la VM
   - **40 Go d’espace disque** pour la VM

### 2.3 Créer la VM Kali (exemple VirtualBox)

1. Ouvrir VirtualBox → **New** / **Nouvelle machine**
2. Paramètres :
   - Nom : `Kali-2023.2`
   - Type : `Linux`
   - Version : `Debian (64-bit)`
3. Mémoire vive : **4096 MB (4 Go)** minimum si possible
4. Disque dur virtuel :
   - Type : VDI
   - Allocation : Dynamically allocated
   - Taille : **40 Go** ou plus

### 2.4 Attacher l’ISO et installer Kali

1. Dans les paramètres de la VM → **Storage / Stockage**
   - Choisir le lecteur CD → sélectionner le fichier ISO de Kali
2. Démarrer la VM
3. Sélectionner **Graphical install**
4. Suivre les étapes :
   - Langue : `English`
   - Clavier : `French`
   - Hostname : par exemple `kali`
   - Création utilisateur : `user` / `holberton` (comme tu veux)
   - Mot de passe : suffisamment fort
   - Partitionnement :
     - **Guided – use entire disk** (on parle du disque virtuel, pas de ton vrai disque)
     - `Write changes to disk` → Yes

À la fin, Kali redémarre et tu arrives sur l’écran de login.

---

## 3. Première configuration de Kali

Une fois connecté à Kali (dans la VM) :

### 3.1 Mise à jour du système

Ouvrir un terminal :

```bash
sudo apt update && sudo apt upgrade -y
```

Cela met à jour la liste des paquets et installe les dernières versions.

### 3.2 Outils de base à vérifier

Kali vient déjà avec beaucoup d’outils, mais pour les projets, tu auras souvent besoin de :

- **git**
- **curl**
- **vim** / **nano** (éditeur dans le terminal)

Tu peux vérifier / installer :

```bash
sudo apt install -y git curl vim
```

---

## 4. Utiliser le terminal dans Kali

La majorité des projets cyber / Holberton se font **en ligne de commande**.
Quelques bases indispensables :

### 4.1 Se déplacer dans le système de fichiers

```bash
pwd          # Afficher le dossier courant
ls           # Lister les fichiers
cd /chemin   # Changer de dossier
cd ..        # Remonter d'un dossier
mkdir dir    # Créer un dossier
rm fichier   # Supprimer un fichier
rm -r dir    # Supprimer un dossier
```

### 4.2 Créer et éditer un script Bash

```bash
cd ~
mkdir holberton_cyber
cd holberton_cyber
vim 0-hello_kali.sh
```

Dans `vim` (exemple de contenu minimal) :

```bash
#!/bin/bash
echo "Hello Kali"
```

Sauvegarder / quitter `vim` :

- `ESC` → `:wq` → `ENTER`

Rendre le script exécutable :

```bash
chmod +x 0-hello_kali.sh
./0-hello_kali.sh
```

---

## 5. Intégrer VSCode dans ton workflow

Tu veux travailler **avec VSCode**, ce qui est très confortable.

Il y a deux grandes façons de faire :

### 5.1 Option simple : fichiers sur Kali, VSCode sur la machine hôte

1. Créer un dossier partagé entre la machine hôte et la VM (dans les paramètres VirtualBox).
2. Monter ce dossier dans Kali (Guest Additions + configuration des “Shared Folders”).
3. Ouvrir ce dossier avec VSCode sur la machine hôte.
4. Dans Kali, tu exécutes les scripts dans le terminal, mais tu les édites avec VSCode côté hôte.

Avantages :

- Tu gardes VSCode “normal” sur ta machine principale.
- Tu exécutes le code **dans Kali**, comme demandé par le projet.

### 5.2 Option plus avancée : VSCode Remote - SSH vers Kali

1. Installer l’extension **Remote - SSH** dans VSCode.
2. Configurer Kali pour accepter les connexions SSH (si ce n’est pas déjà activé) :

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

3. Récupérer l’IP de Kali :

```bash
ip addr
```

4. Depuis VSCode (machine hôte), configurer une connexion SSH vers l’IP de la VM.

Résultat :

> VSCode “ouvre” directement le système de fichiers de Kali,
> tu édites les fichiers **dans Kali**, tu lances un terminal intégré VSCode qui est en fait un terminal dans Kali.

C’est très confortable pour un workflow dev / cyber avancé.

---

## 6. Bonnes pratiques pour les projets cybersécurité

1. **Toujours exécuter** tes scripts / tests sur **Kali** (ou l’environnement spécifié par le projet).
2. **Isoler** les outils de pentest dans Kali, pas sur ton OS principal.
3. Garder un dossier de travail structuré, par exemple :

```text
~/holberton_cyber/
  ├── 0x00-intro_cyber/
  ├── 0x01-network_scanning/
  └── 0x02-web_security/
```

4. Utiliser un gestionnaire de versions (git) même pour tes labs :

```bash
git init
git add .
git commit -m "Initial setup: Kali cyber lab"
```

---

## 7. Rappel : “environnement virtuel” vs “machine virtuelle”

Attention à ne pas confondre :

- **Machine virtuelle (VM)** :
  Un système complet (Kali) qui tourne à l’intérieur de ton PC via VirtualBox/VMware.
- **Environnement virtuel Python (`venv`)** :
  Un mini environnement Python isolé pour un projet (librairies, versions).

Dans ce module **Introduction to Cyber Security**,
le plus important est d’avoir :

> Une **machine virtuelle Kali** bien installée, à jour,
> avec un **terminal fonctionnel** et un **workflow clair avec VSCode**.

---
