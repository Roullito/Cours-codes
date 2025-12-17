# Windows Fundamentals (TryHackMe) — Cours ultra-complet (FR)

> Objectif : te donner une base **solide, pédagogique et opérationnelle** sur Windows (utilisation, admin, sécurité et Active Directory) — avec **cheat-sheets**, **commandes**, **mini-labs** et **checklists**.
>
> Public : débutant → intermédiaire (cybersécurité / sysadmin / pentest).  
> Pré-requis : savoir utiliser un PC, notions de fichiers/dossiers, et un peu de réseau.

---

## Sommaire

- [Niveau 1 — Fondamentaux Windows](#niveau-1--fondamentaux-windows)
  - [Task 1 — Windows Editions](#task-1--windows-editions)
  - [Task 2 — The Desktop (GUI)](#task-2--the-desktop-gui)
  - [Task 3 — Introduction to Windows](#task-3--introduction-to-windows)
  - [Task 4 — The File System](#task-4--the-file-system)
  - [Task 5 — The Windows\System32 Folders](#task-5--the-windowssystem32-folders)
  - [Task 6 — User Accounts, Profiles, and Permissions](#task-6--user-accounts-profiles-and-permissions)
  - [Task 7 — User Account Control](#task-7--user-account-control)
  - [Task 8 — Settings and the Control Panel](#task-8--settings-and-the-control-panel)
  - [Task 9 — Task Manager](#task-9--task-manager)
  - [Task 10 — Conclusion](#task-10--conclusion-niveau-1)
- [Niveau 2 — Outils système & administration](#niveau-2--outils-système--administration)
  - [Task 1 — Introduction](#task-1--introduction-niveau-2)
  - [Task 2 — System Configuration & Advanced System Settings](#task-2--system-configuration--advanced-system-settings)
  - [Task 3 — Change UAC Settings](#task-3--change-uac-settings)
  - [Task 4 — Computer Management](#task-4--computer-management)
  - [Task 5 — System Information](#task-5--system-information)
  - [Task 6 — Resource Monitor](#task-6--resource-monitor)
  - [Task 7 — Command Prompt](#task-7--command-prompt)
  - [Task 8 — Registry Editor](#task-8--registry-editor)
  - [Task 9 — Conclusion](#task-9--conclusion-niveau-2)
- [Niveau 3 — Sécurité Windows](#niveau-3--sécurité-windows)
  - [Task 1 — Introduction](#task-1--introduction-niveau-3)
  - [Task 2 — Windows Updates](#task-2--windows-updates)
  - [Task 3 — Windows Security](#task-3--windows-security)
  - [Task 4 — Virus & threat protection](#task-4--virus--threat-protection)
  - [Task 5 — Firewall & network protection](#task-5--firewall--network-protection)
  - [Task 6 — App & browser control](#task-6--app--browser-control)
  - [Task 7 — Device security](#task-7--device-security)
  - [Task 8 — BitLocker](#task-8--bitlocker)
  - [Task 9 — Volume Shadow Copy Service](#task-9--volume-shadow-copy-service)
  - [Task 10 — Conclusion](#task-10--conclusion-niveau-3)
- [Niveau 4 — Domaines & Active Directory](#niveau-4--domaines--active-directory)
  - [Task 1 — Introduction](#task-1--introduction-niveau-4)
  - [Task 2 — Windows Domains](#task-2--windows-domains)
  - [Task 3 — Active Directory](#task-3--active-directory)
  - [Task 4 — Managing Users in AD](#task-4--managing-users-in-ad)
  - [Task 5 — Managing Computers in AD](#task-5--managing-computers-in-ad)
  - [Task 6 — Group Policies](#task-6--group-policies)
  - [Task 7 — Authentication Methods](#task-7--authentication-methods)
  - [Task 8 — Trees, Forests and Trusts](#task-8--trees-forests-and-trusts)
  - [Task 9 — Conclusion](#task-9--conclusion-niveau-4)
- [Annexes](#annexes)
  - [Cheat-sheet — chemins essentiels](#cheat-sheet--chemins-essentiels)
  - [Cheat-sheet — commandes utiles](#cheat-sheet--commandes-utiles)
  - [Mini-labs (révision rapide)](#mini-labs-révision-rapide)
  - [Checklist “je suis à l’aise”](#checklist-je-suis-à-laise)

---

# Niveau 1 — Fondamentaux Windows

## Task 1 — Windows Editions

### 1) Pourquoi il existe plusieurs “éditions” ?
Windows est décliné en éditions car :
- **les fonctionnalités** (chiffrement, domaine, GPO, virtualisation…) varient,
- **le public** ciblé varie (particulier, entreprise, éducation, serveur),
- **les licences** et modes de gestion (MDM/Intune, Volume Licensing) varient.

### 2) Les éditions les plus courantes
- **Windows Home**  
  Pour usage perso. Souvent limité : pas de **join domaine**, pas de **GPO locale avancée**, BitLocker parfois absent/partiel selon version/édition.
- **Windows Pro**  
  Pour pros/PME : **join domaine / Azure AD**, **BitLocker** (souvent), **Remote Desktop host**, Hyper-V (selon SKU), politiques.
- **Windows Enterprise / Education**  
  Pour grandes orgs : fonctions sécurité/gestion avancées (AppLocker/WDAC, Credential Guard, etc. selon versions).
- **Windows Server**  
  Pour les rôles serveurs : **AD DS**, DNS, DHCP, fichiers, etc.

> **Point TryHackMe** : en cybersécurité, l’édition est importante car elle change :
> - les **mécanismes de sécurité** disponibles,
> - la présence d’un **domaine**, d’une **GPO**, de BitLocker,
> - les **outils** de gestion (RDP, stratégie, journaux, etc.).

### 3) Identifier l’édition et la version
- `Win + R` → `winver`
- Paramètres → Système → À propos
- CMD :
  - `systeminfo | findstr /B /C:"OS Name" /C:"OS Version"`

---

## Task 2 — The Desktop (GUI)

### 1) Les zones principales
- **Bureau** : raccourcis, fichiers (attention : “fichiers sur le bureau” = dans ton profil)
- **Barre des tâches** : applis épinglées, fenêtres, zone de notification
- **Menu Démarrer** : recherche, applis, alimentation
- **Zone de notification** (systray) : réseau, audio, sécurité, batterie
- **Explorateur de fichiers** : navigation fichiers/dossiers

### 2) Les raccourcis clavier indispensables
- `Win + E` : Explorateur
- `Win + R` : Exécuter
- `Win + X` : menu admin (Terminal, Gestion du disque, etc.)
- `Ctrl + Shift + Esc` : Gestionnaire des tâches
- `Win + I` : Paramètres
- `Win + L` : verrouiller la session

### 3) “Exécuter” (Win+R) — super-pouvoir
C’est un accès direct à des outils systèmes.
Exemples :
- `cmd` : Invite de commandes
- `powershell` / `pwsh`
- `control` : Panneau de configuration
- `compmgmt.msc` : Gestion de l’ordinateur
- `taskmgr` : Gestionnaire des tâches
- `services.msc` : Services
- `eventvwr.msc` : Observateur d’événements
- `regedit` : Registre
- `msconfig` : Configuration système
- `msinfo32` : Informations système

---

## Task 3 — Introduction to Windows

### 1) Windows, c’est quoi “techniquement” ?
Un OS composé de :
- **Kernel** (noyau) : gère mémoire, CPU, drivers
- **Userland** : interface graphique, applis, API
- **Services** : tâches en arrière-plan (ex: Windows Update)
- **Registry** : base de config centrale
- **Logs** : journaux d’événements (Event Logs)

### 2) Concepts clés
- **Processus** : programme en exécution (ex: `notepad.exe`)
- **Thread** : “fil” d’exécution d’un processus
- **Service** : processus géré par le Service Control Manager (SCM)
- **Session** : contexte utilisateur (logon)
- **SID** : identifiant unique de sécurité (utilisateur/groupe)
- **ACL** : listes de contrôle d’accès (droits)

### 3) La séparation important : Admin vs Standard
- Un utilisateur **standard** : moins de droits, meilleure sécurité
- Un admin : peut tout casser… et être abusé par un malware
- Windows pousse donc des mécanismes comme **UAC** (voir Task 7)

---

## Task 4 — The File System

### 1) Lettres de lecteur & chemins
- `C:\` : disque principal (souvent)
- `D:\` : second disque / lecteur optique / etc.
- Chemin absolu : `C:\Users\Jules\Documents\file.txt`
- Chemin relatif (dans un terminal) : `.\file.txt` ou `..\parent\file.txt`

### 2) NTFS vs FAT32/exFAT (très important)
- **NTFS** : permissions (ACL), chiffrement (EFS), journaling, quotas, liens, ADS…
- **FAT32** : ancien, pas d’ACL, limites sur taille fichier (4GB)
- **exFAT** : portable, mieux que FAT32, mais ACL Windows = limité

En cybersécurité, NTFS est majeur car il supporte :
- des **ACL** détaillées,
- des **Alternate Data Streams (ADS)** (pouvant cacher des données),
- des **timestamps** utiles en forensic.

### 3) Attributs de fichiers
Attributs classiques : lecture seule, caché, système, archive.
CMD :
- `attrib`
- `attrib +h secret.txt` (hidden)
- `attrib -h secret.txt` (unhide)

### 4) Extensions : Windows “cache” parfois
Par défaut, Windows peut cacher les extensions connues (`.txt`, `.exe`…).
C’est un risque (phishing/malware).
➡️ Explorer → Affichage → **Afficher les extensions de noms de fichiers**.

### 5) Dossiers importants
- `C:\Windows\` : OS
- `C:\Program Files\` : applis 64-bit
- `C:\Program Files (x86)\` : applis 32-bit
- `C:\Users\` : profils utilisateurs
- `C:\Temp\` ou `%TEMP%` : temporaires (souvent ciblé par malwares)

### 6) Variables d’environnement (super utiles)
- `%USERPROFILE%` → `C:\Users\<user>`
- `%APPDATA%` → Roaming appdata (souvent persistance malware)
- `%LOCALAPPDATA%`
- `%TEMP%`
- `%WINDIR%` → `C:\Windows`
- `%SYSTEMROOT%` → souvent `C:\Windows`

CMD :
- `echo %USERPROFILE%`

---

## Task 5 — The Windows\System32 Folders

### 1) System32 : pourquoi ce nom ?
Historiquement lié à la compatibilité.
Aujourd’hui, **System32 contient des binaires 64-bit** sur un OS 64-bit (oui, c’est contre-intuitif).

### 2) Pourquoi c’est important ?
`C:\Windows\System32\` contient :
- des **outils système** (ex: `cmd.exe`, `ipconfig.exe`, `schtasks.exe`)
- des **DLL** et composants critiques
- des sous-dossiers de config

C’est une zone :
- **protégée** (ACL élevées),
- **ciblée** (living-off-the-land : utiliser des outils Windows légitimes),
- critique pour le boot et le fonctionnement.

### 3) Dossiers liés
- `C:\Windows\SysWOW64\` : composants 32-bit sur OS 64-bit
- `C:\Windows\System32\drivers\` : pilotes (drivers)
- `C:\Windows\System32\config\` : fichiers liés aux hives du registre

> **Warning** : modifier/supprimer dans System32 peut casser Windows.

---

## Task 6 — User Accounts, Profiles, and Permissions

### 1) Comptes locaux vs Microsoft vs domaine
- **Compte local** : existe uniquement sur la machine
- **Compte Microsoft** : connecté au cloud (OneDrive, Store)
- **Compte domaine** : contrôlé par AD (entreprise)

### 2) Profil utilisateur
Un profil = dossiers + paramètres :
- `C:\Users\<username>\`
  - `Desktop`, `Documents`, `Downloads`, `Pictures`
  - `AppData\Roaming` (profil itinérant logique)
  - `AppData\Local`
  - `AppData\LocalLow`

### 3) Groupes importants
- **Administrators** : droits admin
- **Users** : standard
- **Remote Desktop Users** : accès RDP (si autorisé)
- **Backup Operators** (sur certains systèmes) : puissant

CMD :
- `whoami`
- `whoami /groups`
- `net user`
- `net localgroup`

### 4) Permissions NTFS (ACL)
Droits typiques :
- Read, Write, Modify, Full Control
Concepts :
- **Héritage** : droits hérités du dossier parent
- **Propriétaire** : peut changer les permissions
- **Deny** : refus explicite (prioritaire)

Outil :
- GUI : Propriétés → Sécurité
- CMD : `icacls`
  - `icacls "C:\path\file.txt"`
  - `icacls "C:\path" /T` (récursif)

### 5) Permissions de partage vs NTFS
Sur un partage réseau :
- Permissions **Share** (partage) + permissions **NTFS**
➡️ **Le plus restrictif gagne**.

---

## Task 7 — User Account Control (UAC)

### 1) Le but de l’UAC
Réduire l’impact d’actions admin.
Même si tu es admin, tes applis tournent souvent avec un “token” limité, et Windows te demande confirmation pour élever.

### 2) Ce que tu vois
Fenêtre :
- “Voulez-vous autoriser cette application à apporter des modifications…”
- Parfois demande de mot de passe (si tu es standard)

### 3) Pourquoi c’est important en sécurité
- Beaucoup de malwares cherchent à **bypasser UAC** ou tromper l’utilisateur.
- Réglage UAC trop bas = surface d’attaque plus grande.

---

## Task 8 — Settings and the Control Panel

### 1) Pourquoi deux interfaces ?
- **Settings** : moderne (Windows 10/11), orienté grand public + MDM
- **Control Panel** : legacy, plein d’applets historiques

Tu dois savoir naviguer dans les deux.

### 2) Applets / outils utiles
- `control` : Panneau de config
- `appwiz.cpl` : Programmes et fonctionnalités
- `ncpa.cpl` : Connexions réseau
- `sysdm.cpl` : Propriétés système
- `firewall.cpl` : Pare-feu
- `inetcpl.cpl` : Options Internet

---

## Task 9 — Task Manager

### 1) À quoi il sert ?
- Visualiser processus, performances, services, démarrage
- Terminer un programme bloqué
- Diagnostiquer CPU/RAM/disque/réseau
- Voir la **ligne de commande** d’un process (super utile en forensics)

Ouverture :
- `Ctrl + Shift + Esc`
- `taskmgr`
- clic droit barre des tâches → Gestionnaire des tâches

### 2) Onglets à connaître
- **Processes** : vue simple par app/process
- **Details** : vue technique (PID, priorité, CPU…)
- **Performance** : graph CPU/RAM/Disque/Réseau
- **Startup** : programmes au démarrage
- **Users** : sessions
- **Services** : services Windows

### 3) Détection de trucs “suspects” (méthodo simple)
- Nom bizarre (typosquatting : `svch0st.exe` au lieu de `svchost.exe`)
- Emplacement étrange (un “svchost.exe” hors System32)
- Parent process incohérent
- Forte conso CPU/RAM
- Lancement depuis `%TEMP%` / `%APPDATA%`

> Bonus : clic droit → “Open file location” / “Search online”.

---

## Task 10 — Conclusion (Niveau 1)

À ce stade tu dois savoir :
- identifier édition/version,
- te repérer dans l’UI et les outils,
- comprendre NTFS + profils utilisateurs,
- comprendre System32 et pourquoi c’est critique,
- distinguer permissions/ACL et rôle de l’UAC,
- utiliser Task Manager pour diagnostiquer.

---

# Niveau 2 — Outils système & administration

## Task 1 — Introduction (Niveau 2)

Ici on passe en mode “admin” :
- config système,
- gestion avancée,
- monitoring,
- ligne de commande,
- registre.

Objectif : que tu puisses **investiguer** une machine Windows et comprendre où sont les “leviers” principaux.

---

## Task 2 — System Configuration & Advanced System Settings

### 1) System Configuration (`msconfig`)
`Win + R` → `msconfig`

Utilités :
- Mode démarrage normal / diagnostic
- Gestion des options de boot
- Accès rapide à des outils (selon versions)

> Sur Windows 10/11, la gestion du démarrage se retrouve souvent dans Task Manager (Startup).

### 2) Advanced System Settings (`sysdm.cpl`)
`Win + R` → `sysdm.cpl` → onglet **Advanced**

Sections importantes :
- **Performance** : effets visuels, mémoire virtuelle (pagefile)
- **User Profiles** : profils stockés
- **Environment Variables** : variables env (PATH, TEMP…)
- **Startup and Recovery** : dumps, redémarrage auto

### 3) Variables d’environnement (rappel)
Très utile pour scripting et debug :
- `PATH` : où Windows cherche les exécutables
- `TEMP`, `TMP` : temp
- `COMPUTERNAME`, `USERNAME`

### 4) Restauration système (System Restore)
Permet de revenir à un état précédent via points de restauration (pas un backup complet).
Utile en dépannage, et important à connaître pour la **Volume Shadow Copy** (niveau 3).

---

## Task 3 — Change UAC Settings

`Win + R` → taper “UAC” dans la recherche → **Change User Account Control settings**

Le slider va de :
- Toujours notifier (très strict)
- Notifier (par défaut)
- Notifier sans assombrir le bureau
- Ne jamais notifier (déconseillé)

> En entreprise, les policies/GPO peuvent forcer ces réglages.

---

## Task 4 — Computer Management

`Win + R` → `compmgmt.msc`

C’est un “hub” :
- **System Tools**
  - Event Viewer
  - Task Scheduler
  - Local Users and Groups
  - Device Manager
- **Storage**
  - Disk Management
- **Services and Applications**
  - Services
  - WMI Control (selon versions)

### 1) Event Viewer (`eventvwr.msc`)
Logs essentiels :
- **Windows Logs**
  - Application
  - Security
  - System
- **Applications and Services Logs** (plus spécifique)

En sécurité, tu vas souvent chercher :
- échecs de connexion,
- élévations, création de services,
- exécution de tâches planifiées,
- erreurs de drivers, etc.

### 2) Local Users and Groups (`lusrmgr.msc`)
Créer/éditer comptes et groupes locaux (pas dispo sur Home généralement).

### 3) Disk Management (`diskmgmt.msc`)
- partitions, lettres, formats
- utile pour comprendre volumes, BitLocker, etc.

### 4) Services (`services.msc`)
Un service = composant lancé/arrêté, startup auto/manual/disabled.
Attention : des malwares se “camouflent” en services.

CMD :
- `sc query`
- `sc qc <service>`

---

## Task 5 — System Information

`Win + R` → `msinfo32`

Tu obtiens :
- OS, build, BIOS/UEFI, Secure Boot
- matériel
- pilotes
- “Software Environment” : services, drivers, variables env, tâches, etc.

CMD alternative :
- `systeminfo`
- `driverquery`

---

## Task 6 — Resource Monitor

`Win + R` → `resmon`

Plus fin que Task Manager pour :
- **CPU** (process + threads + handles)
- **Memory** (hard faults, commit)
- **Disk** (I/O par process, fichiers ouverts)
- **Network** (ports utilisés, connexions, DNS)

Méthodo :
- Si CPU haut : quel process ? quel thread ?
- Si disque saturé : quel fichier est spammé ?
- Si réseau : quels ports sortants ? vers quelles IP ?

---

## Task 7 — Command Prompt

### 1) Pourquoi la ligne de commande ?
Parce qu’en cybersécurité :
- c’est **scriptable**,
- c’est rapide à distance,
- ça révèle des infos précises,
- et beaucoup d’outils d’investigation existent en CLI.

### 2) Commandes de base (navigation)
- `whoami`
- `hostname`
- `cd`, `dir`
- `tree`
- `type file.txt`
- `more`
- `copy`, `move`, `del`
- `mkdir`, `rmdir`

### 3) Redirection & pipes (indispensable)
- `>` écrase, `>>` ajoute
- `|` pipe
Exemples :
- `dir C:\Windows\System32 > list.txt`
- `ipconfig /all | findstr /I "DNS IPv4"`

### 4) Réseau
- `ipconfig /all`
- `ping 8.8.8.8`
- `tracert example.com`
- `nslookup example.com`
- `netstat -ano` (connexions + PID)
- `arp -a`

### 5) Processus / services
- `tasklist`
- `tasklist /svc` (liaison services)
- `taskkill /PID 1234 /F`
- `sc query`
- `sc qc <service>`

### 6) Système / inventaire
- `systeminfo`
- `driverquery`
- `wmic` (souvent présent mais de plus en plus déprécié)
- `set` (variables env)

### 7) Utilisateurs & groupes
- `net user`
- `net user <name>`
- `net localgroup`
- `net localgroup Administrators`

### 8) Tâches planifiées
- `schtasks /query /fo LIST /v`
- `schtasks /query /fo TABLE`

> Les tâches planifiées sont une technique classique de persistance.

---

## Task 8 — Registry Editor

### 1) Le registre : c’est quoi ?
Base centrale de configuration Windows.
Structure : **hives** → clés → sous-clés → valeurs.

### 2) Hives principales
- `HKEY_LOCAL_MACHINE (HKLM)` : machine
- `HKEY_CURRENT_USER (HKCU)` : utilisateur courant
- `HKEY_USERS (HKU)` : tous les profils chargés
- `HKEY_CLASSES_ROOT (HKCR)` : associations de fichiers (fusion)
- `HKEY_CURRENT_CONFIG (HKCC)` : config matérielle

### 3) Emplacements “sécurité / persistance” classiques (à connaître)
> Pas pour “pirater”, mais pour **détecter** où ça s’installe.

- **Run keys** (démarrage utilisateur) :
  - `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
- **Run keys** (démarrage machine) :
  - `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- Services :
  - `HKLM\SYSTEM\CurrentControlSet\Services`
- Policies :
  - `HKLM\Software\Policies\...`
  - `HKCU\Software\Policies\...`

### 4) Bonnes pratiques
- Toujours **exporter** une clé avant de modifier
- Attention aux permissions (tu peux te bloquer toi-même)
- Comprendre la différence machine vs user

### 5) CLI pour requêter le registre (utile)
- `reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- `reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run`

---

## Task 9 — Conclusion (Niveau 2)

Tu dois savoir :
- ouvrir et utiliser `msconfig`, `sysdm.cpl`, `msinfo32`, `resmon`
- naviguer dans `compmgmt.msc`
- utiliser CMD pour inventorier réseau/process/services/users/tâches
- comprendre la structure du registre + emplacements sensibles

---

# Niveau 3 — Sécurité Windows

## Task 1 — Introduction (Niveau 3)
Ici, on se concentre sur :
- mise à jour (patching),
- Defender/Windows Security,
- firewall,
- protections navigateur/app,
- sécurité matérielle (TPM/Secure Boot),
- chiffrement (BitLocker),
- snapshots (VSS).

---

## Task 2 — Windows Updates

### 1) Pourquoi c’est crucial ?
La majorité des compromissions “simples” exploitent :
- des failles déjà patchées,
- des machines en retard de mises à jour.

### 2) Types de mises à jour
- **Quality updates** : cumulatives mensuelles (sécurité + corrections)
- **Feature updates** : versions majeures (Win10/11)
- **Driver updates** : pilotes
- **Definitions** : signatures Defender

### 3) Vérifier / diagnostiquer
- Paramètres → Windows Update
- “Update history” (historique)
- Logs / outils : Event Viewer, `systeminfo` (informations OS), etc.

---

## Task 3 — Windows Security

### 1) Le “dashboard” central
Windows Security regroupe :
- Virus & threat protection (Defender AV)
- Firewall & network protection
- App & browser control (SmartScreen, réputation)
- Device security (TPM, Secure Boot, isolation)
- (selon versions) Account protection, etc.

### 2) Lecture “cyber”
L’idée est de savoir :
- quelles protections sont ON/OFF,
- quelles surfaces sont exposées (RDP, firewall désactivé…),
- quelles politiques sont appliquées (entreprise).

---

## Task 4 — Virus & threat protection

### 1) Defender (principes)
- protection temps réel
- cloud-delivered protection (option)
- tamper protection (empêche modifications malveillantes)
- scans : quick/full/custom/offline

### 2) Ce que tu dois regarder
- état (vert/jaune/rouge)
- exclusions (suspicious si trop larges)
- historique de protection
- quarantaines

---

## Task 5 — Firewall & network protection

### 1) Profils
- **Domain**
- **Private**
- **Public**

Le firewall applique des règles par profil.
Ex : laptop au café = Public (plus strict).

### 2) Concepts
- règles entrantes/sortantes
- ports, programmes, services
- “allowlist” vs “blocklist”

### 3) CLI utiles
- `netsh advfirewall show allprofiles`
- `netsh advfirewall firewall show rule name=all`

---

## Task 6 — App & browser control

### 1) SmartScreen
Protège contre :
- téléchargements réputés dangereux,
- sites/phishing,
- applis non reconnues.

### 2) Protection basée sur la réputation
C’est une barrière très utile contre le social engineering.

> En entreprise : peut être renforcé ou désactivé via GPO.

---

## Task 7 — Device security

### 1) Secure Boot & TPM
- **Secure Boot** : boot uniquement de composants signés
- **TPM** : puce de confiance (clés, BitLocker, etc.)

### 2) Isolation / Core isolation (selon version)
Fonctions visant à isoler des parties sensibles de l’OS (VBS, Memory Integrity…).

Objectif : rendre plus difficile l’implantation de rootkits/attaques kernel.

---

## Task 8 — BitLocker

### 1) C’est quoi ?
Chiffrement de disque (full disk encryption).
But : si on vole ton PC/disque → données illisibles.

### 2) Concepts à connaître
- clé stockée TPM (souvent)
- PIN + TPM (plus strict)
- **Recovery Key** : à sauvegarder
- chiffrement du volume OS vs data
- impact sur forensic (très important)

### 3) Outils
- GUI : “Manage BitLocker”
- CLI :
  - `manage-bde -status`
  - `manage-bde -on C:`
  - `manage-bde -protectors -get C:`

---

## Task 9 — Volume Shadow Copy Service

### 1) C’est quoi ?
VSS crée des “snapshots” (copies d’ombre) permettant :
- restauration système,
- “Previous versions”,
- backup cohérent de fichiers ouverts.

### 2) Pourquoi c’est important en cyber
- Forensic : récupérer anciennes versions
- Ransomware : souvent cible VSS (efface les shadow copies)
- IT : peut sauver une journée

### 3) Outils / commandes
- `vssadmin list shadows`
- `vssadmin list shadowstorage`
- (souvent admin) `vssadmin delete shadows /all`

> En investigation : si les shadow copies ont disparu “bizarrement”, c’est un signal.

---

## Task 10 — Conclusion (Niveau 3)

Tu dois être capable de :
- expliquer le rôle des updates,
- décrire Windows Security (et où vérifier l’état),
- comprendre profils firewall,
- comprendre SmartScreen,
- connaître Secure Boot/TPM et leur intérêt,
- expliquer BitLocker (et utiliser `manage-bde`),
- expliquer VSS et pourquoi ça intéresse défenseurs/attaquants.

---

# Niveau 4 — Domaines & Active Directory

## Task 1 — Introduction (Niveau 4)

Ici tu rentres dans le monde entreprise :
- identité centralisée,
- gestion des droits,
- politiques de sécurité,
- authentification à grande échelle.

AD = la colonne vertébrale de beaucoup d’infras Windows.

---

## Task 2 — Windows Domains

### 1) Workgroup vs Domain
- **Workgroup** : chaque machine gère ses comptes localement (petit réseau)
- **Domain** : comptes centralisés, politiques, auth centralisée

### 2) Pourquoi un domaine ?
- un seul compte pour plusieurs machines
- contrôle des mots de passe / MFA (selon)
- déploiement de logiciels, policies
- gestion des droits plus cohérente

### 3) Le rôle de DNS
Un domaine Windows repose fortement sur DNS (découverte DC, services AD).
Si DNS est cassé, beaucoup de choses AD cassent.

---

## Task 3 — Active Directory

### 1) AD DS : le “service annuaire”
Active Directory Domain Services stocke :
- utilisateurs, groupes
- ordinateurs
- OU (Organizational Units)
- politiques (via GPO)
- objets divers (imprimantes, partages…)

### 2) Notions clés
- **Domain Controller (DC)** : serveur qui héberge AD
- **LDAP** : protocole pour interroger l’annuaire
- **Kerberos** : auth principale (souvent)
- **OU** : conteneur logique pour organiser et appliquer des GPO
- **Groupes** : RBAC (gestion des droits)
- **SYSVOL** : réplication de scripts & policies

Schéma mental :
```mermaid
flowchart LR
  U[User] -->|logon| DC[Domain Controller]
  U --> PC[Domain-joined PC]
  DC --> DNS[DNS]
  DC --> SYSVOL[SYSVOL / Policies]
  DC --> ADDB[AD Database (NTDS.dit)]
```

### 3) Où sont les données AD ?
- Base AD : `NTDS.dit` (sur DC)
- SYSVOL : scripts / policies (sur DC)

> En défense : protéger DC = priorité absolue.

---

## Task 4 — Managing Users in AD

### 1) Outils
- **ADUC** (Active Directory Users and Computers)
- PowerShell (module AD) : `Get-ADUser`, `New-ADUser`, etc. (si installé)
- Gestions via groupes/OU

### 2) Concepts pratiques
- Création d’utilisateur + mot de passe initial
- Forcer changement au prochain logon
- Désactiver/activer compte
- Ajouter aux groupes (éviter de donner “Domain Admin”)

### 3) Principes sécurité
- principe du moindre privilège
- séparation comptes admin / comptes perso
- revue régulière des groupes sensibles

---

## Task 5 — Managing Computers in AD

### 1) Domain-join
Quand un PC rejoint le domaine :
- il obtient un objet “Computer” dans AD,
- il reçoit des policies (GPO),
- il utilise Kerberos/NTLM selon contexte.

### 2) Organisation par OU
Tu places les PCs dans des OU par :
- site,
- service (RH, IT…),
- type (serveurs, laptops…),
- niveau de confiance.

---

## Task 6 — Group Policies

### 1) C’est quoi une GPO ?
Une Group Policy Object applique :
- configurations Windows,
- règles sécurité,
- scripts logon,
- déploiements (selon),
- restrictions.

### 2) Où ça s’applique ?
GPO peut être liée à :
- domaine
- site
- OU

### 3) Inheritance & precedence
Ordre mental :
- Local policy
- Site
- Domain
- OU (du parent vers enfant)

“Le dernier qui s’applique gagne” (avec exceptions : Enforced/Block Inheritance).

### 4) Commandes utiles côté client
- `gpupdate /force` : forcer l’application
- `gpresult /r` : résumé
- `gpresult /h report.html` : rapport complet

---

## Task 7 — Authentication Methods

### 1) Kerberos vs NTLM (simplifié)
- **Kerberos** : tickets, plus moderne, standard AD
- **NTLM** : plus ancien, utilisé en fallback / compat

Tu dois savoir reconnaître ces mots car ils apparaissent partout (logs, docs, attaques).

### 2) MFA / Smartcard / Windows Hello (selon contexte)
- MFA : souvent via Azure AD / Entra ID, ou solutions tierces
- Smartcard : carte à puce/certificat
- Hello for Business : auth moderne (biométrie/PIN + TPM)

---

## Task 8 — Trees, Forests and Trusts

### 1) Tree
Plusieurs domaines partageant un namespace DNS (ex: `corp.example.com`, `it.example.com`).

### 2) Forest
Ensemble de trees/domaines partageant :
- schéma AD
- catalogue global (GC)
C’est la “frontière” la plus forte.

### 3) Trusts
Permettent l’accès entre domaines/forests :
- trust transitive vs non-transitive
- one-way vs two-way

> En sécurité : les trusts peuvent étendre la surface d’attaque.

---

## Task 9 — Conclusion (Niveau 4)

Tu dois :
- expliquer workgroup vs domain
- connaître DC, LDAP, Kerberos, OU, SYSVOL, NTDS.dit
- savoir ce qu’est une GPO et comment vérifier l’application
- comprendre tree/forest/trust à haut niveau

---

# Annexes

## Cheat-sheet — chemins essentiels

- OS : `C:\Windows\`
- System32 : `C:\Windows\System32\`
- SysWOW64 : `C:\Windows\SysWOW64\`
- Drivers : `C:\Windows\System32\drivers\`
- Users : `C:\Users\`
- AppData (Roaming) : `%APPDATA%`
- AppData (Local) : `%LOCALAPPDATA%`
- Temp : `%TEMP%`
- Program Files : `C:\Program Files\`
- Program Files (x86) : `C:\Program Files (x86)\`

## Cheat-sheet — commandes utiles

### Identité / sessions
- `whoami`
- `whoami /groups`
- `hostname`

### Système
- `systeminfo`
- `msinfo32`
- `driverquery`

### Processus / services
- `tasklist`
- `tasklist /svc`
- `taskkill /PID <pid> /F`
- `sc query`
- `sc qc <service>`

### Réseau
- `ipconfig /all`
- `ping <host>`
- `tracert <host>`
- `nslookup <host>`
- `netstat -ano`
- `arp -a`

### Utilisateurs
- `net user`
- `net localgroup`
- `net localgroup Administrators`

### Permissions NTFS
- `icacls "C:\path"`

### Registre
- `reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- `reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run`

### GPO (si domaine)
- `gpupdate /force`
- `gpresult /r`
- `gpresult /h report.html`

### Sécurité / disque
- `manage-bde -status`
- `vssadmin list shadows`

---

## Mini-labs (révision rapide)

1) **Inventaire rapide**
- Lance CMD
- `systeminfo`
- `whoami /groups`
- `ipconfig /all`

2) **Process suspect**
- Ouvre Task Manager
- Onglet Details : regarde PID, “Command line”
- Vérifie l’emplacement du binaire

3) **Permissions**
- Crée un dossier `C:\LabPerm`
- Vérifie ACL avec `icacls`
- Observe héritage via l’UI

4) **Startup / persistance**
- Vérifie Startup apps (Task Manager)
- Liste les Run keys (reg query)

5) **Réseau**
- `netstat -ano`
- mappe un PID à un process via Task Manager

6) **Sécurité**
- Ouvre Windows Security
- Vérifie Defender / Firewall
- Liste status BitLocker si dispo (`manage-bde -status`)

---

## Checklist “je suis à l’aise”

- [ ] Je sais identifier édition/version (`winver`, `systeminfo`)
- [ ] Je sais naviguer dans `C:\Windows`, `System32`, `C:\Users`
- [ ] Je comprends NTFS, ACL, héritage, et je sais utiliser `icacls`
- [ ] Je comprends UAC et je sais où le régler
- [ ] Je sais utiliser Task Manager + Resource Monitor
- [ ] Je sais faire un inventaire réseau (`ipconfig`, `netstat`)
- [ ] Je comprends Defender/Firewall/SmartScreen à haut niveau
- [ ] Je comprends BitLocker et pourquoi c’est critique
- [ ] Je sais ce qu’est un domaine, AD, OU, GPO, Kerberos
- [ ] Je sais générer un `gpresult` (si domaine)

---

> Fin du cours — si tu veux, je peux aussi te faire :
> - une version **“cheat-sheet”** (1-2 pages),
> - une liste de **QCM** façon TryHackMe,
> - des **mini-exercices** guidés (sans solution directe).
