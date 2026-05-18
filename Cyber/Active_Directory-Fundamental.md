# Active Directory - Fundamentals

> **Module cyber / Holberton — Cours complet en français**  
> **Objectif :** comprendre l’architecture Active Directory, les Domain Controllers, les domaines, LDAP, l’authentification, l’autorisation, les Users, Groups, OUs et GPOs, puis apprendre à raisonner comme un analyste sécurité / pentester interne dans un lab autorisé.

---

## Table des matières

1. [Introduction générale](#1-introduction-générale)
2. [Pourquoi Active Directory est critique](#2-pourquoi-active-directory-est-critique)
3. [Prérequis du module et environnement de lab](#3-prérequis-du-module-et-environnement-de-lab)
4. [Vocabulaire essentiel](#4-vocabulaire-essentiel)
5. [Qu’est-ce qu’Active Directory ?](#5-quest-ce-quactive-directory-)
6. [Authentication, Authorization, Accounting](#6-authentication-authorization-accounting)
7. [Active Directory Domain Services — AD DS](#7-active-directory-domain-services--ad-ds)
8. [Architecture logique : forêt, arbre, domaine, OU](#8-architecture-logique--forêt-arbre-domaine-ou)
9. [Architecture physique : Domain Controllers, sites, réplication](#9-architecture-physique--domain-controllers-sites-réplication)
10. [Domain Controllers en détail](#10-domain-controllers-en-détail)
11. [DNS et Active Directory](#11-dns-et-active-directory)
12. [Objets Active Directory : users, computers, groups](#12-objets-active-directory--users-computers-groups)
13. [Groupes AD : types, scopes et privilèges](#13-groupes-ad--types-scopes-et-privilèges)
14. [SIDs, ACLs, ACEs, DACLs et modèle de permissions](#14-sids-acls-aces-dacls-et-modèle-de-permissions)
15. [LDAP : rôle, syntaxe et requêtes](#15-ldap--rôle-syntaxe-et-requêtes)
16. [Kerberos : fonctionnement complet](#16-kerberos--fonctionnement-complet)
17. [NTLM : pourquoi il existe encore et pourquoi il est risqué](#17-ntlm--pourquoi-il-existe-encore-et-pourquoi-il-est-risqué)
18. [GPO — Group Policy Objects](#18-gpo--group-policy-objects)
19. [SYSVOL, NETLOGON et scripts de domaine](#19-sysvol-netlogon-et-scripts-de-domaine)
20. [WinRM, RDP, SMB : administration distante en environnement AD](#20-winrm-rdp-smb--administration-distante-en-environnement-ad)
21. [Création de test users, groups et OUs](#21-création-de-test-users-groups-et-ous)
22. [Méthodologie d’énumération depuis Kali Linux](#22-méthodologie-dénumération-depuis-kali-linux)
23. [Chemins d’attaque courants en AD](#23-chemins-dattaque-courants-en-ad)
24. [Mauvaises configurations classiques](#24-mauvaises-configurations-classiques)
25. [Détection, durcissement et bonnes pratiques](#25-détection-durcissement-et-bonnes-pratiques)
26. [TP guidés](#26-tp-guidés)
27. [Checklists opérationnelles](#27-checklists-opérationnelles)
28. [Questions d’auto-évaluation](#28-questions-dauto-évaluation)
29. [Glossaire](#29-glossaire)
30. [Sources et ressources complémentaires](#30-sources-et-ressources-complémentaires)

---

# 1. Introduction générale

Active Directory, souvent abrégé **AD**, est l’un des piliers historiques des réseaux d’entreprise Microsoft. Quand une entreprise possède des postes Windows, des serveurs Windows, des comptes utilisateurs centralisés, des droits d’accès, des partages réseau, des imprimantes, des politiques de mot de passe, des groupes d’administration, des scripts de connexion ou des règles de configuration communes, il y a de fortes chances qu’Active Directory soit au centre de l’infrastructure.

Dans un environnement d’entreprise, on ne veut pas créer manuellement un compte local différent sur chaque machine. On veut un système centralisé capable de répondre à des questions comme :

- Qui est cet utilisateur ?
- A-t-il le droit de se connecter ?
- À quels groupes appartient-il ?
- A-t-il le droit d’accéder à ce partage ?
- Quels paramètres doivent s’appliquer à son poste ?
- Son mot de passe respecte-t-il la politique de sécurité ?
- Peut-il administrer cette machine ?
- Ce serveur fait-il partie du domaine ?
- Quelles relations de confiance existent avec d’autres domaines ?

Active Directory répond à ces questions à travers un ensemble de services : annuaire, authentification, autorisation, stratégie de groupe, gestion des objets, réplication, DNS, Kerberos, LDAP, SMB, SYSVOL, etc.

Ce module est orienté **fondamentaux cyber**. L’objectif n’est pas seulement de connaître les définitions. L’objectif est de comprendre comment l’ensemble fonctionne réellement, pourquoi les attaquants ciblent Active Directory, et comment un pentester ou un défenseur peut analyser un domaine de manière structurée.

---

# 2. Pourquoi Active Directory est critique

Active Directory est critique parce qu’il concentre l’identité, les droits et l’administration.

Dans une entreprise classique, un utilisateur ne se connecte pas seulement à son ordinateur. Il accède à :

- son poste Windows ;
- des partages fichiers ;
- des applications internes ;
- des bases de données ;
- des serveurs métiers ;
- des imprimantes ;
- des ressources cloud synchronisées ;
- des outils d’administration ;
- des messageries ;
- des VPN ;
- des services internes exposés uniquement au réseau d’entreprise.

Si Active Directory est compromis, l’attaquant ne compromet pas seulement “un serveur”. Il peut potentiellement compromettre **la logique de confiance de toute l’organisation**.

## 2.1 La logique “keys to the kingdom”

On dit souvent qu’Active Directory contient les “clés du royaume” parce qu’il contrôle :

- les comptes utilisateurs ;
- les comptes administrateurs ;
- les groupes privilégiés ;
- les machines membres du domaine ;
- les Domain Controllers ;
- les secrets nécessaires à Kerberos ;
- les politiques de sécurité ;
- les chemins d’administration ;
- les relations de confiance ;
- les permissions sur les objets ;
- les scripts et configurations distribués.

Un attaquant qui obtient un simple compte utilisateur peut parfois découvrir des erreurs de configuration qui lui permettent de progresser :

```text
Compte utilisateur faible
        ↓
Énumération des groupes et machines
        ↓
Découverte d'un partage contenant des identifiants
        ↓
Accès local admin sur une machine
        ↓
Récupération de sessions ou tickets
        ↓
Escalade vers un compte privilégié
        ↓
Contrôle du domaine
```

## 2.2 Pourquoi les red teams adorent AD

Active Directory est très riche. Cette richesse crée une grande surface d’attaque :

- beaucoup d’objets ;
- beaucoup de permissions héritées ;
- beaucoup de groupes imbriqués ;
- beaucoup d’anciens comptes ;
- beaucoup de scripts historiques ;
- beaucoup de GPOs anciennes ;
- beaucoup de compatibilité avec des protocoles anciens ;
- beaucoup d’exceptions faites “temporairement” puis jamais retirées.

L’attaquant n’a pas toujours besoin d’exploiter une CVE. Dans beaucoup de cas, il abuse simplement :

- d’un mot de passe faible ;
- d’un compte de service mal configuré ;
- d’une délégation excessive ;
- d’une GPO modifiable ;
- d’un groupe trop privilégié ;
- d’un partage lisible ;
- d’un service WinRM ou SMB accessible ;
- d’une mauvaise séparation entre utilisateurs et administrateurs.

---

# 3. Prérequis du module et environnement de lab

## 3.1 Machines nécessaires

Le module mentionne les ISOs / VMs suivantes :

- **Kali Linux** : machine d’attaque / analyse.
- **Windows Server 2019** : serveur cible, futur Domain Controller ou serveur déjà intégré au domaine selon le lab.
- **Windows 11 Enterprise** : poste victime / workstation membre du domaine.

## 3.2 Règle importante sur les OVA

Si le module fournit des fichiers OVA :

> Télécharger uniquement les OVA fournis.  
> Ne pas modifier les paramètres internes.  
> Les importer tels quels.

Cela garantit que tout le monde travaille sur le même environnement et que les comportements observés sont comparables.

## 3.3 Réseau

Les trois VMs doivent être connectées au même réseau virtuel pour communiquer :

```text
+----------------+       +----------------------+       +------------------+
| Kali Linux     | <---> | Windows Server 2019  | <---> | Windows 11       |
| Attacker box   |       | Domain Controller ?  |       | Victim workstation|
+----------------+       +----------------------+       +------------------+
```

Selon ton hyperviseur, cela peut être :

- NAT network ;
- host-only network ;
- internal network ;
- réseau VMware “NAT” commun ;
- réseau VirtualBox “Host-only” ou “NAT Network”.

Le point important : **Kali doit pouvoir joindre Windows Server et Windows 11**.

## 3.4 Accès WinRM fourni

Le module indique :

```text
username: labuser
password: P@ssw0rd123!
```

Ces identifiants sont mentionnés pour se connecter à une VM Windows via WinRM depuis Kali. Cependant, la note finale précise aussi :

> Les étudiants doivent obtenir les credentials Windows Server par énumération et exploitation, plutôt que de les recevoir directement.

Il faut donc distinguer :

- les identifiants fournis pour un accès pédagogique à une machine Windows spécifique ;
- les identifiants du Windows Server / Domain Controller qui doivent être obtenus par la logique du lab ;
- le travail offensif qui doit toujours être effectué depuis Kali Linux.

Commande typique de connexion WinRM dans un lab :

```bash
evil-winrm -i <IP_WINDOWS> -u labuser -p 'P@ssw0rd123!'
```

> À utiliser uniquement dans l’environnement autorisé du module.

## 3.5 Éditeurs autorisés

Le module autorise :

- `vi`
- `vim`
- `emacs`

Et tous les fichiers doivent se terminer par une nouvelle ligne.

---

# 4. Vocabulaire essentiel

Avant de rentrer dans les détails, il faut maîtriser les termes suivants.

| Terme | Explication simple |
|---|---|
| Directory | Annuaire qui stocke des objets et leurs attributs. |
| Directory Service | Service qui rend cet annuaire consultable et utilisable par le réseau. |
| AD DS | Active Directory Domain Services, service d’annuaire de domaine. |
| Domain | Périmètre logique d’administration, d’authentification et de sécurité. |
| Domain Controller | Serveur qui héberge AD DS et répond aux demandes d’authentification/LDAP/Kerberos. |
| Forest | Conteneur logique le plus haut dans AD, regroupant un ou plusieurs domaines. |
| Tree | Ensemble de domaines partageant un espace de noms DNS continu. |
| OU | Organizational Unit, conteneur logique pour organiser des objets et appliquer des GPOs. |
| User | Objet représentant un utilisateur humain ou parfois un compte applicatif. |
| Computer | Objet représentant une machine jointe au domaine. |
| Group | Objet permettant d’agréger des utilisateurs/computers pour gérer des droits. |
| GPO | Group Policy Object, objet de stratégie appliquant des configurations. |
| LDAP | Protocole utilisé pour interroger et modifier l’annuaire. |
| Kerberos | Protocole principal d’authentification en domaine Windows. |
| NTLM | Ancien mécanisme d’authentification Windows, encore présent pour compatibilité. |
| SID | Security Identifier, identifiant unique d’un principal de sécurité. |
| ACL | Access Control List, liste de permissions sur un objet. |
| ACE | Access Control Entry, une règle dans une ACL. |
| DACL | Liste des permissions accordées/refusées. |
| SACL | Liste d’audit utilisée pour la journalisation d’accès. |
| SYSVOL | Partage répliqué contenant notamment les GPOs et scripts de domaine. |
| DNS | Service essentiel pour localiser les Domain Controllers et services AD. |
| Trust | Relation de confiance entre domaines ou forêts. |

---

# 5. Qu’est-ce qu’Active Directory ?

## 5.1 Définition simple

**Active Directory** est un service d’annuaire Microsoft qui permet de centraliser la gestion des identités, des machines, des groupes, des droits et des politiques dans un réseau d’entreprise.

Plus précisément, le composant central est **Active Directory Domain Services**, ou **AD DS**.

AD DS stocke des informations sur des objets réseau :

- utilisateurs ;
- ordinateurs ;
- groupes ;
- imprimantes ;
- unités d’organisation ;
- stratégies de groupe ;
- comptes de service ;
- contacts ;
- objets DNS ;
- relations de confiance ;
- etc.

Ces objets possèdent des attributs :

```text
User: Alice Martin
    cn: Alice Martin
    sAMAccountName: amartin
    userPrincipalName: amartin@corp.local
    memberOf: Helpdesk, VPN-Users
    department: IT
    lastLogonTimestamp: ...
    userAccountControl: ...
```

## 5.2 Analogie simple

Imagine une grande école avec :

- des étudiants ;
- des professeurs ;
- des salles ;
- des badges ;
- des groupes ;
- des droits d’accès ;
- des règles ;
- des emplois du temps ;
- des imprimantes ;
- des bâtiments.

Sans système central, chaque bâtiment devrait gérer ses propres listes. Ce serait ingérable.

Active Directory joue le rôle de **registre central de confiance**. Il sait :

- qui existe ;
- à quels groupes chaque personne appartient ;
- quelles machines font partie du réseau ;
- quelles règles appliquer ;
- quels accès sont autorisés.

## 5.3 Ce qu’AD n’est pas

Active Directory n’est pas seulement :

- une base de données utilisateurs ;
- un outil de login Windows ;
- un serveur LDAP ;
- un serveur DNS ;
- un serveur Kerberos.

C’est un **écosystème complet** qui combine tous ces éléments.

---

# 6. Authentication, Authorization, Accounting

La sécurité d’accès repose souvent sur trois notions : **AAA**.

## 6.1 Authentication — Authentification

L’authentification répond à la question :

> “Qui es-tu ?”

Exemples :

- un utilisateur saisit un mot de passe ;
- une machine présente son compte machine ;
- un service utilise un ticket Kerberos ;
- un administrateur utilise une smart card ;
- une application utilise un compte de service.

En AD, l’authentification est principalement réalisée via :

- Kerberos ;
- NTLM en fallback ou compatibilité ;
- certificats dans certains scénarios ;
- parfois LDAP bind selon les applications.

## 6.2 Authorization — Autorisation

L’autorisation répond à la question :

> “As-tu le droit de faire cette action ?”

Exemples :

- lire un fichier ;
- modifier un objet AD ;
- se connecter en RDP ;
- redémarrer un serveur ;
- modifier une GPO ;
- ajouter un utilisateur à un groupe ;
- créer un compte machine ;
- accéder à un partage SMB.

L’autorisation se base sur :

- l’identité ;
- les groupes ;
- les permissions ;
- les ACLs ;
- les droits locaux ;
- les GPOs ;
- les privilèges Windows ;
- l’appartenance à des groupes administratifs.

## 6.3 Accounting — Traçabilité

L’accounting répond à la question :

> “Qu’a fait cet utilisateur ou ce système ?”

Exemples :

- un logon réussi ;
- un échec de connexion ;
- une modification de groupe ;
- une création de compte ;
- une modification de GPO ;
- une connexion WinRM ;
- une création de service ;
- une requête Kerberos suspecte.

Dans Windows, cela se retrouve dans :

- Event Viewer ;
- Security logs ;
- Directory Service logs ;
- PowerShell logs ;
- Sysmon si installé ;
- logs Defender ;
- logs EDR ;
- SIEM.

---

# 7. Active Directory Domain Services — AD DS

## 7.1 Rôle d’AD DS

AD DS est le service qui fournit la base d’Active Directory. Il permet de :

- stocker les objets du domaine ;
- authentifier les utilisateurs ;
- autoriser l’accès aux ressources ;
- appliquer les stratégies de groupe ;
- publier des services ;
- gérer les relations de confiance ;
- répliquer les données entre Domain Controllers.

## 7.2 Base de données AD

La base de données principale d’un Domain Controller est :

```text
C:\Windows\NTDS\NTDS.dit
```

Elle contient les objets de l’annuaire.

À côté, on retrouve notamment :

```text
C:\Windows\SYSVOL\
```

SYSVOL contient des éléments répliqués comme :

- scripts de connexion ;
- politiques de groupe ;
- modèles de stratégie ;
- fichiers nécessaires aux GPOs.

## 7.3 Pourquoi c’est sensible

Si un attaquant obtient un accès complet à un Domain Controller, il peut potentiellement accéder à des secrets critiques du domaine, notamment des informations nécessaires à l’authentification des comptes. C’est pourquoi un Domain Controller est considéré comme une machine **Tier 0**.

Tier 0 signifie :

> Si cet actif est compromis, tout le domaine peut être compromis.

---

# 8. Architecture logique : forêt, arbre, domaine, OU

## 8.1 Forest — Forêt

La forêt est le conteneur logique le plus élevé dans Active Directory.

Elle contient :

- un ou plusieurs domaines ;
- un schéma commun ;
- une configuration commune ;
- un catalogue global ;
- des relations de confiance internes ;
- des règles d’administration au niveau forest.

Schéma mental :

```text
Forest: corp.local
│
├── Domain: corp.local
│   ├── OU: Users
│   ├── OU: Workstations
│   └── OU: Servers
│
└── Domain: child.corp.local
    ├── OU: Users
    └── OU: Servers
```

## 8.2 Tree — Arbre

Un arbre est un ensemble de domaines partageant un espace de noms DNS continu.

Exemple :

```text
corp.local
dev.corp.local
hr.corp.local
```

Ces domaines appartiennent au même arbre car ils partagent le suffixe `corp.local`.

## 8.3 Domain — Domaine

Un domaine est une frontière logique d’administration et de sécurité.

Exemple :

```text
corp.local
```

Dans un domaine, on trouve :

- users ;
- groups ;
- computers ;
- OUs ;
- GPOs ;
- Domain Controllers ;
- politiques de mot de passe ;
- permissions ;
- relations de confiance.

Le domaine est souvent ce que tu vas énumérer en premier dans un lab AD.

## 8.4 OU — Organizational Unit

Une OU est un conteneur logique dans un domaine.

Elle sert à :

- organiser les objets ;
- déléguer l’administration ;
- appliquer des GPOs ;
- séparer les postes, serveurs, utilisateurs, services, etc.

Exemple :

```text
corp.local
│
├── OU=Users
│   ├── OU=IT
│   ├── OU=Finance
│   └── OU=HR
│
├── OU=Computers
│   ├── OU=Workstations
│   └── OU=Servers
│
└── OU=Admins
```

## 8.5 OU vs Group

Une confusion fréquente :

| OU | Group |
|---|---|
| Sert à organiser et appliquer des GPOs. | Sert à accorder des droits. |
| Un objet ne peut être que dans une OU principale à la fois. | Un objet peut être membre de plusieurs groupes. |
| Structure administrative. | Structure de permissions. |

Exemple :

- Alice est placée dans `OU=Finance`.
- Alice est membre de `GG_Finance_Read`.
- La GPO “Finance Workstation Hardening” s’applique à l’OU Finance.
- Le groupe `GG_Finance_Read` donne accès au partage `\\filesrv\finance`.

---

# 9. Architecture physique : Domain Controllers, sites, réplication

## 9.1 Architecture logique vs physique

Active Directory a deux dimensions :

| Dimension | Contenu |
|---|---|
| Logique | Forest, domain, OU, users, groups, GPOs |
| Physique | Domain Controllers, sites, subnets, réplication |

L’architecture logique dit : “comment l’annuaire est organisé”.

L’architecture physique dit : “où sont les serveurs et comment ils communiquent”.

## 9.2 Sites AD

Un site AD représente généralement un emplacement réseau physique ou logique :

- siège ;
- agence ;
- datacenter ;
- réseau VPN ;
- cloud ;
- sous-réseau géographique.

Exemple :

```text
Site: Paris
    Subnet: 10.10.0.0/16

Site: Lyon
    Subnet: 10.20.0.0/16

Site: Genève
    Subnet: 10.30.0.0/16
```

Les sites servent à optimiser :

- l’authentification ;
- la réplication ;
- la localisation des Domain Controllers ;
- l’application de certaines GPOs ;
- la bande passante inter-sites.

## 9.3 Réplication

Dans un domaine réel, il peut y avoir plusieurs Domain Controllers. Ils doivent partager la même vision de l’annuaire.

Si un admin crée un utilisateur sur DC1, l’information doit finir par arriver sur DC2.

```text
DC1 <---- réplication ----> DC2 <---- réplication ----> DC3
```

La réplication AD permet :

- la résilience ;
- la haute disponibilité ;
- la répartition de charge ;
- la continuité de service.

## 9.4 Risque sécurité de la réplication

La réplication est essentielle, mais très sensible.

Certaines permissions liées à la réplication peuvent être abusées pour extraire des secrets du domaine. C’est le principe général derrière des attaques comme **DCSync** : un compte disposant de droits de réplication suffisants peut se faire passer pour un contrôleur légitime qui demande des secrets.

À retenir :

> Tout droit de réplication AD doit être considéré comme extrêmement sensible.

---

# 10. Domain Controllers en détail

## 10.1 Définition

Un **Domain Controller** est un serveur Windows qui exécute AD DS et qui héberge une copie de la base de données du domaine.

Il répond notamment à :

- des requêtes LDAP ;
- des demandes Kerberos ;
- des authentifications NTLM ;
- des demandes DNS liées au domaine ;
- des accès SYSVOL ;
- des opérations d’administration AD ;
- des demandes de réplication.

## 10.2 Services typiques sur un Domain Controller

Un DC expose souvent plusieurs ports :

| Port | Protocole | Usage |
|---:|---|---|
| 53 | DNS | Résolution de noms AD |
| 88 | Kerberos | Authentification Kerberos |
| 135 | RPC Endpoint Mapper | Services Windows RPC |
| 139 | NetBIOS Session Service | Ancien SMB/NetBIOS |
| 389 | LDAP | Requêtes annuaire |
| 445 | SMB | SYSVOL, NETLOGON, partages |
| 464 | Kerberos kpasswd | Changement de mot de passe |
| 593 | RPC over HTTP | RPC |
| 636 | LDAPS | LDAP sur TLS |
| 3268 | Global Catalog | Catalogue global LDAP |
| 3269 | Global Catalog LDAPS | Catalogue global sécurisé |
| 5985 | WinRM HTTP | Administration distante |
| 5986 | WinRM HTTPS | Administration distante sécurisée |

En reconnaissance, voir ces ports sur une même machine est un indicateur fort d’un Domain Controller.

Exemple Nmap en lab :

```bash
nmap -sV -sC -p 53,88,135,139,389,445,464,593,636,3268,3269,5985,5986 <IP_DC>
```

## 10.3 Rôles FSMO

Active Directory utilise des rôles spéciaux appelés **FSMO** :

- Schema Master ;
- Domain Naming Master ;
- RID Master ;
- PDC Emulator ;
- Infrastructure Master.

Ces rôles évitent les conflits sur certaines opérations sensibles.

### Schema Master

Contrôle les modifications du schéma AD.

Le schéma définit les classes et attributs possibles dans l’annuaire.

Exemple :

```text
Class: user
Attributes: cn, sn, mail, memberOf, userPrincipalName, ...
```

### Domain Naming Master

Contrôle l’ajout ou la suppression de domaines dans la forêt.

### RID Master

Distribue des blocs de RIDs utilisés pour créer les SIDs des objets.

Un SID ressemble à :

```text
S-1-5-21-1111111111-2222222222-3333333333-1105
```

La fin `1105` est le RID.

### PDC Emulator

Rôle très important :

- compatibilité avec anciens systèmes ;
- source de temps principale dans le domaine ;
- traitement prioritaire des changements de mots de passe ;
- gestion de certaines politiques de compte.

### Infrastructure Master

Gère les références entre objets de domaines différents.

## 10.4 Domain Controller = cible critique

Un DC doit être traité comme un coffre-fort.

Bonnes pratiques :

- aucun usage utilisateur classique ;
- pas de navigation web ;
- pas d’outils inutiles ;
- patching strict ;
- accès admin limité ;
- monitoring avancé ;
- sauvegardes AD contrôlées ;
- séparation Tier 0 ;
- pas de comptes faibles ;
- pas de sessions admin inutiles ;
- pas de logiciel tiers non maîtrisé.

---

# 11. DNS et Active Directory

## 11.1 Pourquoi DNS est essentiel

Active Directory dépend fortement de DNS.

Les clients utilisent DNS pour trouver :

- les Domain Controllers ;
- les services Kerberos ;
- les services LDAP ;
- les Global Catalogs ;
- les contrôleurs du bon site.

Exemple de records DNS importants :

```text
_ldap._tcp.dc._msdcs.corp.local
_kerberos._tcp.corp.local
_gc._tcp.corp.local
```

## 11.2 Symptômes d’un DNS AD cassé

Si DNS est mal configuré :

- les machines ne rejoignent pas le domaine ;
- les utilisateurs ne se connectent pas ;
- les GPOs ne s’appliquent pas ;
- Kerberos échoue ;
- les clients contactent le mauvais DC ;
- la réplication peut dysfonctionner.

## 11.3 Côté Kali : observer DNS

Depuis Kali, on peut interroger DNS dans un lab :

```bash
nslookup corp.local <IP_DC>
dig @<IP_DC> corp.local
dig @<IP_DC> _ldap._tcp.dc._msdcs.corp.local SRV
```

Si tu identifies le nom du domaine, tu peux tenter :

```bash
dig @<IP_DC> ANY corp.local
```

Attention : dans beaucoup d’environnements, les transferts de zone DNS sont bloqués.

Test en lab :

```bash
dig axfr corp.local @<IP_DC>
```

Si un transfert de zone est autorisé par erreur, cela peut révéler beaucoup de noms internes.

---

# 12. Objets Active Directory : users, computers, groups

## 12.1 Objet User

Un objet user représente généralement une personne, mais peut aussi représenter un compte applicatif.

Attributs importants :

| Attribut | Rôle |
|---|---|
| `cn` | Common Name |
| `sAMAccountName` | Nom de login historique |
| `userPrincipalName` | Format user@domain |
| `memberOf` | Groupes dont l’utilisateur est membre |
| `description` | Description, parfois source de secrets mal stockés |
| `lastLogonTimestamp` | Dernière connexion approximative |
| `pwdLastSet` | Date de dernier changement de mot de passe |
| `userAccountControl` | Flags du compte |
| `servicePrincipalName` | SPN si compte de service |
| `adminCount` | Indicateur lié à certains objets protégés |

Exemple :

```text
CN=Alice Martin,OU=IT,OU=Users,DC=corp,DC=local
```

## 12.2 Objet Computer

Quand une machine rejoint le domaine, un objet computer est créé.

Exemple :

```text
CN=WS-01,OU=Workstations,DC=corp,DC=local
```

Un compte machine a aussi un mot de passe, géré automatiquement.

Son nom se termine souvent par `$` :

```text
WS-01$
DC-01$
```

## 12.3 Objet Group

Un groupe sert à rassembler des identités.

Exemple :

```text
CN=IT-Admins,OU=Groups,DC=corp,DC=local
```

Un groupe peut contenir :

- des users ;
- des computers ;
- d’autres groups.

C’est cette imbrication qui rend parfois l’analyse AD complexe.

## 12.4 Objet Contact

Un contact représente une personne externe ou une identité sans capacité de connexion.

## 12.5 Objet OU

Une OU organise les objets et permet la délégation/GPO.

---

# 13. Groupes AD : types, scopes et privilèges

## 13.1 Deux types de groupes

Dans AD, il existe deux grands types :

| Type | Usage |
|---|---|
| Security Group | Utilisé pour accorder des permissions. |
| Distribution Group | Utilisé pour des listes de distribution email. |

En cyber, on s’intéresse surtout aux **Security Groups**.

## 13.2 Scopes de groupes

Les groupes ont aussi un scope :

| Scope | Usage simplifié |
|---|---|
| Global | Regroupe des utilisateurs du même domaine. |
| Domain Local | Donne accès à une ressource dans le domaine. |
| Universal | Peut contenir des objets de plusieurs domaines dans une forêt. |

## 13.3 Méthode AGDLP

Modèle classique :

```text
A  = Accounts
G  = Global groups
DL = Domain Local groups
P  = Permissions
```

Logique :

```text
Users -> Global Group -> Domain Local Group -> Permission
```

Exemple :

```text
Alice
  ↓
GG_Finance_Users
  ↓
DL_Share_Finance_Read
  ↓
Read permission on \\filesrv\finance
```

Avantage :

- plus propre ;
- plus scalable ;
- facilite les audits ;
- évite d’accorder directement des droits à des utilisateurs.

## 13.4 Groupes privilégiés importants

Groupes à connaître absolument :

| Groupe | Risque |
|---|---|
| Domain Admins | Admin complet du domaine. |
| Enterprise Admins | Admin sur toute la forêt. |
| Schema Admins | Peut modifier le schéma AD. |
| Administrators | Groupe admin local/domain selon contexte. |
| Account Operators | Peut gérer beaucoup de comptes/groupes. |
| Server Operators | Peut gérer des serveurs, souvent dangereux. |
| Backup Operators | Peut lire des fichiers sensibles via privilèges backup. |
| Print Operators | Historiquement sensible sur DC. |
| DNSAdmins | Peut devenir critique selon configuration DNS. |
| Group Policy Creator Owners | Peut créer des GPOs, à surveiller. |
| Remote Management Users | Peut se connecter via WinRM selon droits. |
| Remote Desktop Users | Peut se connecter en RDP selon droits. |

## 13.5 Group nesting

Un utilisateur peut être privilégié indirectement.

Exemple :

```text
Alice
  memberOf: Helpdesk-Level2

Helpdesk-Level2
  memberOf: Workstation-Admins

Workstation-Admins
  local admin on: WS-01, WS-02
```

Alice n’est pas “Domain Admin”, mais elle peut être admin local sur certaines machines. Si un Domain Admin se connecte sur une de ces machines, Alice peut potentiellement récupérer des artefacts sensibles selon le niveau de protection.

---

# 14. SIDs, ACLs, ACEs, DACLs et modèle de permissions

## 14.1 Security Principal

Un security principal est une entité qui peut être authentifiée et autorisée.

Exemples :

- user ;
- computer ;
- group ;
- service account.

Chaque principal a un **SID**.

## 14.2 SID

Un SID identifie de manière unique un principal.

Exemple :

```text
S-1-5-21-1234567890-123456789-123456789-1104
```

Structure simplifiée :

```text
S-1-5-21-<domain-id>-<RID>
```

Le RID final identifie l’objet dans le domaine.

RIDs connus :

| RID | Signification |
|---:|---|
| 500 | Administrator intégré |
| 501 | Guest |
| 512 | Domain Admins |
| 513 | Domain Users |
| 514 | Domain Guests |
| 518 | Schema Admins |
| 519 | Enterprise Admins |
|  krbtgt souvent 502 | Compte Kerberos du domaine |

## 14.3 Access Token

Quand un utilisateur se connecte, Windows construit un token contenant :

- son SID ;
- les SIDs de ses groupes ;
- ses privilèges ;
- des informations de session.

Lorsqu’il accède à une ressource, Windows compare ce token aux permissions de la ressource.

## 14.4 ACL

Une ACL est une liste de contrôle d’accès.

Elle contient des ACEs.

## 14.5 ACE

Une ACE est une entrée de permission.

Exemple logique :

```text
Allow GG_Finance_Read Read
Deny  Interns Write
Allow IT_Admins FullControl
```

## 14.6 DACL et SACL

| Type | Rôle |
|---|---|
| DACL | Définit qui a le droit d’accéder ou modifier. |
| SACL | Définit quels accès doivent être audités. |

## 14.7 Pourquoi les ACLs sont centrales en attaque AD

Beaucoup de chemins d’escalade AD reposent sur des permissions trop larges.

Exemples de droits sensibles :

| Droit | Risque |
|---|---|
| GenericAll | Contrôle total de l’objet. |
| GenericWrite | Modification de plusieurs attributs sensibles. |
| WriteDACL | Modifier les permissions de l’objet. |
| WriteOwner | Changer le propriétaire puis modifier la DACL. |
| ForceChangePassword | Réinitialiser le mot de passe d’un compte. |
| AddMember | Ajouter un membre à un groupe. |
| AllExtendedRights | Peut inclure des actions sensibles. |
| Replicating Directory Changes | Risque DCSync si combiné correctement. |

Exemple :

```text
User Bob has GenericAll over Group IT-Admins
```

Cela signifie que Bob peut potentiellement modifier le groupe IT-Admins, par exemple en y ajoutant un membre.

---

# 15. LDAP : rôle, syntaxe et requêtes

## 15.1 Définition

**LDAP** signifie Lightweight Directory Access Protocol.

C’est un protocole permettant d’interroger et modifier des services d’annuaire.

Active Directory expose LDAP pour permettre :

- la recherche d’utilisateurs ;
- la recherche de groupes ;
- la lecture d’attributs ;
- l’administration ;
- l’intégration applicative ;
- l’authentification de certaines applications.

## 15.2 Ports LDAP

| Port | Usage |
|---:|---|
| 389 | LDAP classique |
| 636 | LDAPS, LDAP sur TLS |
| 3268 | Global Catalog |
| 3269 | Global Catalog sur TLS |

## 15.3 Distinguished Name — DN

Un DN est le chemin complet d’un objet dans l’annuaire.

Exemple :

```text
CN=Alice Martin,OU=IT,OU=Users,DC=corp,DC=local
```

Lecture :

- `CN=Alice Martin` : objet Alice Martin ;
- `OU=IT` : dans l’OU IT ;
- `OU=Users` : dans l’OU Users ;
- `DC=corp,DC=local` : domaine corp.local.

## 15.4 Base DN

Pour le domaine :

```text
corp.local
```

Le Base DN est :

```text
DC=corp,DC=local
```

## 15.5 LDAP filters

Les filtres LDAP permettent de chercher des objets.

Exemples :

Tous les users :

```text
(objectClass=user)
```

Tous les computers :

```text
(objectClass=computer)
```

Un utilisateur par sAMAccountName :

```text
(sAMAccountName=alice)
```

Tous les objets ayant un SPN :

```text
(servicePrincipalName=*)
```

Les comptes sans pré-authentification Kerberos :

```text
(userAccountControl:1.2.840.113556.1.4.803:=4194304)
```

> Cette dernière recherche est utile en audit car les comptes sans pré-authentification peuvent être exposés à l’AS-REP Roasting.

## 15.6 Requêtes LDAP depuis Kali

Découvrir le naming context :

```bash
ldapsearch -x -H ldap://<IP_DC> -s base namingcontexts
```

Recherche anonyme simple, si autorisée :

```bash
ldapsearch -x -H ldap://<IP_DC> -b "DC=corp,DC=local"
```

Recherche authentifiée :

```bash
ldapsearch -x -H ldap://<IP_DC> \
  -D "corp\\labuser" \
  -w 'P@ssw0rd123!' \
  -b "DC=corp,DC=local"
```

Rechercher les utilisateurs :

```bash
ldapsearch -x -H ldap://<IP_DC> \
  -D "corp\\labuser" \
  -w 'P@ssw0rd123!' \
  -b "DC=corp,DC=local" \
  "(objectClass=user)" sAMAccountName memberOf
```

## 15.7 À retenir

LDAP répond à la question :

> “Que contient l’annuaire ?”

Kerberos répond plutôt à :

> “Comment prouver mon identité pour obtenir un accès ?”

---

# 16. Kerberos : fonctionnement complet

## 16.1 Définition

Kerberos est le protocole principal d’authentification utilisé dans les domaines Active Directory modernes.

Il repose sur des tickets.

Au lieu d’envoyer ton mot de passe à chaque service, tu t’authentifies auprès d’un centre de confiance, puis tu obtiens des tickets pour accéder à des services.

## 16.2 Acteurs Kerberos

| Acteur | Rôle |
|---|---|
| Client | Utilisateur ou machine qui veut accéder à un service. |
| KDC | Key Distribution Center, service Kerberos sur le Domain Controller. |
| AS | Authentication Service, partie du KDC qui donne le TGT. |
| TGS | Ticket Granting Service, partie du KDC qui donne des tickets de service. |
| Service | Ressource cible : SMB, HTTP, MSSQL, LDAP, etc. |
| krbtgt | Compte spécial utilisé pour signer/chiffrer les TGT. |

## 16.3 Tickets Kerberos

Deux types importants :

| Ticket | Usage |
|---|---|
| TGT | Ticket Granting Ticket, prouve que tu t’es authentifié au domaine. |
| TGS | Ticket de service, utilisé pour accéder à un service précis. |

## 16.4 Flux Kerberos simplifié

```text
1. User -> DC/KDC : je veux m’authentifier
2. DC/KDC -> User : voici un TGT
3. User -> DC/KDC : avec mon TGT, je veux accéder à CIFS/filesrv
4. DC/KDC -> User : voici un TGS pour CIFS/filesrv
5. User -> filesrv : voici mon TGS
6. filesrv : ticket valide, accès autorisé selon permissions
```

## 16.5 Flux Kerberos détaillé

### Étape 1 — AS-REQ

Le client demande un TGT au KDC.

```text
Client -> KDC : AS-REQ
```

Selon la configuration, le client prouve sa connaissance du secret du compte.

### Étape 2 — AS-REP

Le KDC renvoie un TGT.

```text
KDC -> Client : AS-REP + TGT
```

Le TGT est lié au compte `krbtgt`.

### Étape 3 — TGS-REQ

Le client veut accéder à un service.

Exemple :

```text
cifs/filesrv.corp.local
http/intranet.corp.local
mssql/sqlsrv.corp.local
```

Il envoie son TGT au TGS :

```text
Client -> KDC : TGS-REQ
```

### Étape 4 — TGS-REP

Le KDC renvoie un ticket de service.

```text
KDC -> Client : TGS-REP
```

### Étape 5 — AP-REQ

Le client présente le TGS au service.

```text
Client -> Service : AP-REQ
```

Le service vérifie le ticket.

## 16.6 SPN — Service Principal Name

Un SPN identifie un service Kerberos.

Format :

```text
service/host
```

Exemples :

```text
cifs/filesrv.corp.local
http/intranet.corp.local
mssql/sqlsrv.corp.local
ldap/dc01.corp.local
```

Les comptes de service avec SPN sont très importants en sécurité car ils peuvent être exposés à des attaques de type **Kerberoasting** si leurs mots de passe sont faibles.

## 16.7 krbtgt

`krbtgt` est un compte spécial du domaine.

Il est utilisé par Kerberos pour protéger les TGT.

Si le secret de `krbtgt` est compromis, l’attaquant peut potentiellement forger des tickets très puissants. C’est le principe général des **Golden Tickets**.

À retenir :

> La compromission de krbtgt est un scénario critique de compromission du domaine.

## 16.8 Pourquoi l’heure est importante

Kerberos dépend fortement de l’heure.

Si le client et le Domain Controller ont un décalage trop important, l’authentification peut échouer.

Symptômes :

```text
KRB_AP_ERR_SKEW
Clock skew too great
```

C’est pourquoi le PDC Emulator joue un rôle important comme référence de temps.

## 16.9 Kerberos côté pentest

En lab AD, Kerberos est souvent utilisé pour comprendre :

- quels comptes ont des SPNs ;
- quels comptes n’exigent pas la pré-authentification ;
- quels services existent ;
- quelles délégations Kerberos sont configurées ;
- si des tickets peuvent être utilisés pour mouvement latéral ;
- si des erreurs de configuration ouvrent des chemins d’escalade.

---

# 17. NTLM : pourquoi il existe encore et pourquoi il est risqué

## 17.1 Définition

NTLM est un ancien mécanisme d’authentification Windows.

Il fonctionne via un système de challenge-réponse basé sur des secrets dérivés du mot de passe.

## 17.2 Pourquoi NTLM existe encore

NTLM reste présent pour compatibilité :

- vieux systèmes ;
- applications legacy ;
- environnements mal configurés ;
- services hors domaine ;
- problèmes DNS/Kerberos ;
- accès par IP au lieu de nom DNS ;
- SPN manquant ;
- relations de confiance particulières.

## 17.3 Risques NTLM

NTLM est souvent ciblé dans les attaques :

- capture de challenge-réponse ;
- relay NTLM ;
- pass-the-hash selon contexte ;
- authentification forcée ;
- mauvaise configuration SMB/LDAP signing.

## 17.4 À retenir

Kerberos est généralement préféré dans un domaine AD sain.

NTLM devrait être :

- réduit ;
- surveillé ;
- désactivé progressivement si possible ;
- remplacé par Kerberos ou mécanismes modernes ;
- protégé par SMB signing, LDAP signing/channel binding selon contexte.

---

# 18. GPO — Group Policy Objects

## 18.1 Définition

Une **GPO** est un objet de stratégie de groupe qui permet d’appliquer des paramètres à des utilisateurs ou ordinateurs.

Exemples de paramètres :

- politique de mot de passe ;
- verrouillage de compte ;
- pare-feu Windows ;
- scripts de démarrage ;
- scripts de connexion ;
- restriction PowerShell ;
- paramètres Windows Defender ;
- droits utilisateurs locaux ;
- déploiement de raccourcis ;
- mappage de lecteurs réseau ;
- configuration proxy ;
- restrictions de panneau de configuration ;
- configuration RDP/WinRM ;
- audit ;
- paramètres de sécurité.

## 18.2 Où sont stockées les GPOs ?

Une GPO existe à deux endroits :

1. Dans Active Directory, comme objet logique.
2. Dans SYSVOL, comme fichiers répliqués.

Chemin typique :

```text
\\corp.local\SYSVOL\corp.local\Policies\{GUID}
```

## 18.3 Ordre d’application LSDOU

Les GPOs s’appliquent selon l’ordre :

```text
L = Local
S = Site
D = Domain
OU = Organizational Unit
```

Donc :

```text
Local GPO
   ↓
Site GPO
   ↓
Domain GPO
   ↓
Parent OU GPO
   ↓
Child OU GPO
```

La GPO la plus proche de l’objet peut souvent écraser des paramètres précédents, selon les paramètres.

## 18.4 Héritage

Les GPOs peuvent être héritées.

Exemple :

```text
Domain corp.local
│
├── GPO: Default Domain Policy
│
└── OU=Workstations
    ├── GPO: Workstation Hardening
    │
    └── OU=Finance
        └── GPO: Finance Proxy Settings
```

Un poste dans `OU=Finance` peut recevoir :

- Default Domain Policy ;
- Workstation Hardening ;
- Finance Proxy Settings.

## 18.5 Enforced et Block Inheritance

Deux mécanismes importants :

| Mécanisme | Effet |
|---|---|
| Enforced | Force l’application d’une GPO malgré certains blocages. |
| Block Inheritance | Bloque l’héritage des GPOs parent sauf celles enforced. |

## 18.6 Security Filtering

Une GPO peut être liée à une OU mais ne s’appliquer qu’à certains groupes via le security filtering.

Exemple :

```text
GPO: VPN Client Config
Linked to: OU=Workstations
Applies to: GG_VPN_Users_Computers
```

## 18.7 WMI Filtering

Une GPO peut s’appliquer selon des conditions WMI.

Exemples :

- seulement Windows 11 ;
- seulement laptops ;
- seulement machines avec telle version ;
- seulement serveurs.

## 18.8 Loopback Processing

Le loopback processing modifie la façon dont les paramètres utilisateurs sont appliqués selon l’ordinateur.

Utile pour :

- postes kiosques ;
- salles de classe ;
- serveurs RDS ;
- machines partagées.

## 18.9 Pourquoi les GPOs sont sensibles

Une GPO peut :

- ajouter un utilisateur local admin ;
- exécuter un script ;
- modifier le pare-feu ;
- activer WinRM ;
- changer des paramètres de sécurité ;
- déployer des tâches planifiées ;
- pousser des fichiers ;
- modifier des droits locaux.

Donc, un utilisateur capable de modifier une GPO appliquée à des machines sensibles peut potentiellement compromettre ces machines.

À retenir :

> Les droits d’édition sur GPO sont des droits très sensibles.

---

# 19. SYSVOL, NETLOGON et scripts de domaine

## 19.1 SYSVOL

SYSVOL est un partage répliqué entre Domain Controllers.

Chemin :

```text
\\domain.local\SYSVOL
```

Il contient notamment :

- GPOs ;
- scripts ;
- fichiers de configuration liés au domaine.

## 19.2 NETLOGON

NETLOGON est souvent utilisé pour les scripts de connexion.

Chemin :

```text
\\domain.local\NETLOGON
```

## 19.3 Risques historiques

Dans certains environnements, on trouve dans SYSVOL ou NETLOGON :

- scripts contenant des mots de passe ;
- anciens fichiers de configuration ;
- fichiers `.xml` de Group Policy Preferences ;
- chemins de déploiement ;
- informations internes ;
- noms de serveurs ;
- clés ou tokens mal stockés.

L’ancien problème célèbre : **GPP cpassword**.

Historiquement, des mots de passe déployés via Group Policy Preferences pouvaient être stockés dans SYSVOL dans des fichiers XML avec un champ `cpassword`. La clé de chiffrement étant publique, cela permettait de récupérer les mots de passe. Ce comportement est ancien, mais des fichiers hérités peuvent encore exister dans des environnements négligés.

En lab :

```bash
smbclient //corp.local/SYSVOL -U 'corp\labuser'
```

Puis chercher :

```bash
find . -type f | grep -Ei "groups.xml|services.xml|scheduledtasks.xml|datasources.xml|printers.xml|drives.xml"
grep -Rni "password\|cpassword\|pwd\|user" .
```

---

# 20. WinRM, RDP, SMB : administration distante en environnement AD

## 20.1 SMB

SMB est utilisé pour :

- partages fichiers ;
- SYSVOL ;
- NETLOGON ;
- accès admin `C$` ;
- IPC ;
- certaines opérations d’administration.

Ports :

```text
445/tcp
139/tcp
```

Exemples depuis Kali :

```bash
smbclient -L //<IP> -U ''
smbclient -L //<IP> -U 'corp\labuser'
```

Avec NetExec :

```bash
nxc smb <IP> -u labuser -p 'P@ssw0rd123!' -d corp.local
```

## 20.2 RDP

RDP permet une session graphique distante.

Port :

```text
3389/tcp
```

Commande Linux :

```bash
xfreerdp /v:<IP> /u:labuser /p:'P@ssw0rd123!' /d:corp.local
```

## 20.3 WinRM

WinRM permet l’administration distante en ligne de commande.

Ports :

```text
5985/tcp HTTP
5986/tcp HTTPS
```

Commande :

```bash
evil-winrm -i <IP> -u labuser -p 'P@ssw0rd123!' -r <DOMAIN>
```

ou :

```bash
evil-winrm -i <IP> -u labuser -p 'P@ssw0rd123!'
```

## 20.4 Pourquoi WinRM est important en pentest AD

Si un compte est membre d’un groupe autorisé à utiliser WinRM sur une machine, cela peut donner un accès shell distant.

Groupes associés :

- Administrators ;
- Remote Management Users ;
- groupes locaux personnalisés ;
- groupes poussés par GPO.

En audit, il faut distinguer :

```text
Credentials valid
    ≠
Remote shell possible
```

Un compte peut être valide mais ne pas avoir de droit WinRM/RDP/SMB admin.

---

# 21. Création de test users, groups et OUs

Cette section est utile pour comprendre côté administrateur. Selon les règles du module, tu peux ne pas avoir directement les droits admin sur le Windows Server. Mais il faut savoir comment les objets sont créés.

## 21.1 Structure de test recommandée

Exemple pédagogique :

```text
corp.local
│
├── OU=Users
│   ├── OU=IT
│   ├── OU=Finance
│   └── OU=HR
│
├── OU=Groups
│   ├── OU=Global
│   └── OU=DomainLocal
│
├── OU=Computers
│   ├── OU=Workstations
│   └── OU=Servers
│
└── OU=Admins
```

## 21.2 Créer une OU avec GUI

Sur Windows Server :

1. Ouvrir **Active Directory Users and Computers**.
2. Clic droit sur le domaine.
3. New → Organizational Unit.
4. Nommer l’OU.
5. Cocher la protection contre suppression accidentelle si nécessaire.

## 21.3 Créer une OU avec PowerShell

```powershell
New-ADOrganizationalUnit -Name "Users" -Path "DC=corp,DC=local"
New-ADOrganizationalUnit -Name "IT" -Path "OU=Users,DC=corp,DC=local"
New-ADOrganizationalUnit -Name "Finance" -Path "OU=Users,DC=corp,DC=local"
New-ADOrganizationalUnit -Name "HR" -Path "OU=Users,DC=corp,DC=local"
```

## 21.4 Créer un user de test

```powershell
New-ADUser `
  -Name "Alice Martin" `
  -GivenName "Alice" `
  -Surname "Martin" `
  -SamAccountName "amartin" `
  -UserPrincipalName "amartin@corp.local" `
  -Path "OU=IT,OU=Users,DC=corp,DC=local" `
  -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
  -Enabled $true
```

## 21.5 Créer des groupes

```powershell
New-ADGroup `
  -Name "GG_IT_Users" `
  -SamAccountName "GG_IT_Users" `
  -GroupCategory Security `
  -GroupScope Global `
  -Path "OU=Global,OU=Groups,DC=corp,DC=local"
```

```powershell
New-ADGroup `
  -Name "DL_IT_Share_Read" `
  -SamAccountName "DL_IT_Share_Read" `
  -GroupCategory Security `
  -GroupScope DomainLocal `
  -Path "OU=DomainLocal,OU=Groups,DC=corp,DC=local"
```

## 21.6 Ajouter un user à un groupe

```powershell
Add-ADGroupMember -Identity "GG_IT_Users" -Members "amartin"
```

## 21.7 Imbriquer les groupes

```powershell
Add-ADGroupMember -Identity "DL_IT_Share_Read" -Members "GG_IT_Users"
```

## 21.8 Visualiser

```powershell
Get-ADUser amartin -Properties memberOf
Get-ADGroupMember GG_IT_Users
Get-ADGroupMember DL_IT_Share_Read
```

---

# 22. Méthodologie d’énumération depuis Kali Linux

> Cette section est destinée à un lab autorisé. Ne jamais exécuter ces actions sur un système hors périmètre.

## 22.1 Objectif de l’énumération

L’énumération AD consiste à répondre progressivement à ces questions :

1. Quelles machines existent ?
2. Laquelle est le Domain Controller ?
3. Quel est le nom du domaine ?
4. Quels services sont exposés ?
5. Est-ce que SMB/LDAP/WinRM/RDP sont accessibles ?
6. L’accès anonyme révèle-t-il des informations ?
7. Les identifiants fournis sont-ils valides ?
8. Quels users et groups existent ?
9. Quels groupes sont privilégiés ?
10. Quels partages sont accessibles ?
11. Y a-t-il des scripts ou secrets ?
12. Quelles GPOs s’appliquent ?
13. Quels chemins d’escalade existent ?

## 22.2 Identifier son réseau

```bash
ip a
ip route
```

Exemple :

```text
192.168.56.0/24
```

## 22.3 Découverte de machines

```bash
nmap -sn 192.168.56.0/24
```

ou :

```bash
netdiscover -r 192.168.56.0/24
```

## 22.4 Scan ciblé des ports AD

```bash
nmap -sV -sC -p 53,88,135,139,389,445,464,593,636,3268,3269,3389,5985,5986 <IP>
```

Interprétation rapide :

- `53 + 88 + 389 + 445` : possible Domain Controller.
- `5985` : WinRM disponible.
- `3389` : RDP disponible.
- `3268` : Global Catalog, très probable DC.
- `88` : Kerberos.

## 22.5 Découvrir le domaine via SMB/Nmap

```bash
nmap --script smb-os-discovery -p445 <IP>
```

Tu peux obtenir :

- NetBIOS name ;
- domain name ;
- forest name ;
- OS ;
- signing SMB ;
- time.

## 22.6 Tester SMB anonyme

```bash
smbclient -L //<IP> -N
```

Si refusé, c’est normal dans un environnement durci.

## 22.7 Tester SMB avec credentials

```bash
smbclient -L //<IP> -U 'corp\labuser'
```

Avec NetExec :

```bash
nxc smb <IP> -u labuser -p 'P@ssw0rd123!' -d corp.local
```

## 22.8 Énumérer LDAP

Base naming contexts :

```bash
ldapsearch -x -H ldap://<IP_DC> -s base namingcontexts
```

Avec identifiants :

```bash
ldapsearch -x -H ldap://<IP_DC> \
  -D "corp\\labuser" \
  -w 'P@ssw0rd123!' \
  -b "DC=corp,DC=local" \
  "(objectClass=user)" sAMAccountName
```

## 22.9 Énumérer les partages

```bash
nxc smb <IP> -u labuser -p 'P@ssw0rd123!' -d corp.local --shares
```

Chercher les partages lisibles :

```bash
smbclient //<IP>/SYSVOL -U 'corp\labuser'
```

## 22.10 Vérifier WinRM

```bash
nxc winrm <IP> -u labuser -p 'P@ssw0rd123!' -d corp.local
```

Si WinRM est possible :

```bash
evil-winrm -i <IP> -u labuser -p 'P@ssw0rd123!' -r corp.local
```

## 22.11 Énumération avec BloodHound

BloodHound représente AD comme un graphe :

```text
User -> Group -> ACL -> Computer -> Session -> Admin rights -> Domain Admin
```

Dans un lab, des collecteurs peuvent être utilisés pour comprendre les chemins d’attaque. L’idée n’est pas de cliquer au hasard sur “shortest path to domain admin”, mais de comprendre :

- pourquoi le chemin existe ;
- quelle permission le permet ;
- quel objet est mal configuré ;
- comment corriger le problème.

Concepts BloodHound à comprendre :

| Relation | Signification |
|---|---|
| MemberOf | Appartenance à un groupe. |
| AdminTo | Admin local sur une machine. |
| HasSession | Session active sur une machine. |
| GenericAll | Contrôle total sur un objet. |
| GenericWrite | Droit d’écriture large. |
| WriteDacl | Peut modifier la DACL. |
| WriteOwner | Peut prendre possession. |
| AddMember | Peut ajouter un membre à un groupe. |
| ForceChangePassword | Peut reset un password. |
| CanRDP | Peut se connecter RDP. |
| CanPSRemote | Peut utiliser PowerShell Remoting/WinRM. |

---

# 23. Chemins d’attaque courants en AD

Cette partie explique les chemins d’attaque classiques à un niveau pédagogique. L’objectif est de comprendre les risques pour mieux les identifier et les corriger dans un lab ou un audit autorisé.

## 23.1 Foothold

Un foothold est un premier accès.

Exemples :

- compte utilisateur faible ;
- mot de passe trouvé dans un partage ;
- mot de passe réutilisé ;
- application interne vulnérable ;
- phishing dans un scénario red team ;
- accès VPN ;
- poste utilisateur compromis ;
- credentials fournis dans un lab.

## 23.2 Énumération interne

Une fois un compte obtenu, l’attaquant énumère :

- domaine ;
- users ;
- groups ;
- computers ;
- sessions ;
- partages ;
- GPOs ;
- ACLs ;
- SPNs ;
- délégations ;
- trusts ;
- serveurs d’intérêt.

Objectif :

> Construire une carte des chemins possibles vers des privilèges plus élevés.

## 23.3 Password spraying

Le password spraying consiste à tester un petit nombre de mots de passe probables sur beaucoup de comptes pour éviter les verrouillages massifs.

Exemples de mots de passe faibles :

```text
Welcome2026!
Spring2026!
Company2026!
P@ssw0rd123!
```

Défense :

- MFA ;
- détection d’échecs répartis ;
- politiques de verrouillage ;
- password protection ;
- interdiction des mots de passe communs ;
- sensibilisation ;
- surveillance des logs.

## 23.4 AS-REP Roasting

Condition :

- un compte AD n’exige pas la pré-authentification Kerberos.

Risque :

- un attaquant peut demander une réponse AS-REP liée au compte ;
- cette réponse peut être attaquée hors ligne si le mot de passe est faible.

Indicateur LDAP :

```text
DONT_REQ_PREAUTH
```

Mitigation :

- exiger la pré-authentification Kerberos ;
- mots de passe forts ;
- surveillance des demandes AS-REP anormales.

## 23.5 Kerberoasting

Condition :

- compte avec SPN ;
- mot de passe faible ou ancien.

Principe :

- demander un ticket de service Kerberos ;
- tenter une attaque hors ligne contre le ticket ;
- récupérer le mot de passe du compte de service si faible.

Mitigation :

- comptes de service avec mots de passe longs et aléatoires ;
- gMSA ;
- rotation ;
- surveillance des requêtes TGS anormales ;
- limitation des privilèges des comptes de service.

## 23.6 Abus de partages SMB

Partages souvent intéressants :

```text
\\domain\SYSVOL
\\domain\NETLOGON
\\filesrv\public
\\filesrv\it
\\filesrv\backup
\\filesrv\deploy
```

Ce qu’un attaquant cherche :

- scripts ;
- mots de passe ;
- fichiers de configuration ;
- sauvegardes ;
- clés SSH ;
- fichiers `.kdbx` ;
- exports ;
- procédures internes ;
- listes d’utilisateurs ;
- outils d’administration.

Défense :

- nettoyage des partages ;
- permissions minimales ;
- audit ;
- classification ;
- suppression des secrets ;
- coffre-fort de secrets.

## 23.7 Abus de droits locaux admin

Si un utilisateur est admin local sur une machine, il peut souvent :

- lire des fichiers sensibles ;
- installer des outils ;
- extraire des informations de session selon protections ;
- pivoter vers d’autres ressources ;
- modifier services et tâches planifiées.

Défense :

- pas d’admin local permanent ;
- Windows LAPS ;
- séparation des comptes admin ;
- tiering ;
- surveillance des connexions admin ;
- interdiction de connexion admin sur postes non sûrs.

## 23.8 ACL abuse

Exemples :

```text
User has GenericAll on another user
User has WriteDACL on a group
User can AddMember to IT-Admins
User can ForceChangePassword on service account
```

Défense :

- audit régulier des ACL ;
- BloodHound en mode défense ;
- revue des délégations ;
- suppression des droits historiques ;
- séparation des rôles.

## 23.9 GPO abuse

Si un utilisateur peut modifier une GPO appliquée à des machines, il peut potentiellement modifier leur configuration.

Risque :

- ajout d’un admin local ;
- déploiement d’un script ;
- modification de services ;
- changement de règles de sécurité ;
- affaiblissement du poste.

Défense :

- limiter les droits de modification GPO ;
- journaliser les changements ;
- utiliser des workflows de validation ;
- séparer GPO critique et GPO standard ;
- revoir les GPO liées aux OUs sensibles.

## 23.10 Délégation Kerberos

Types :

- unconstrained delegation ;
- constrained delegation ;
- resource-based constrained delegation.

La délégation permet à un service d’agir pour le compte d’un utilisateur dans certains scénarios.

Risque :

- mauvaise configuration ;
- comptes ou machines contrôlables ;
- possibilité de rebond vers services sensibles.

Défense :

- limiter fortement la délégation ;
- préférer des modèles modernes ;
- Protected Users pour admins ;
- “Account is sensitive and cannot be delegated” pour comptes sensibles ;
- surveillance.

## 23.11 Domain trusts

Les trusts permettent à des domaines de se faire confiance.

Risque :

- chemin de mouvement latéral inter-domaines ;
- mauvaise compréhension des flux ;
- SID filtering absent/mal configuré ;
- comptes privilégiés inter-domaines.

Défense :

- documenter les trusts ;
- supprimer les trusts inutiles ;
- surveiller ;
- limiter les privilèges transitifs.

## 23.12 Objectif final d’un attaquant AD

Selon le scénario :

- obtenir Domain Admin ;
- obtenir accès à données sensibles ;
- compromettre un serveur métier ;
- établir persistance ;
- déployer ransomware ;
- exfiltrer ;
- pivoter vers cloud/hybride ;
- compromettre sauvegardes.

Dans un lab pédagogique, l’objectif est de comprendre le chemin, pas de causer un dommage.

---

# 24. Mauvaises configurations classiques

## 24.1 Comptes avec mots de passe faibles

Symptômes :

- mots de passe basés sur saison/année ;
- comptes de service jamais changés ;
- mots de passe identiques ;
- comptes partagés ;
- mots de passe dans descriptions.

Mesures :

- politique de mot de passe moderne ;
- blocage des mots de passe communs ;
- longueur élevée ;
- MFA là où possible ;
- gMSA pour services ;
- audit régulier.

## 24.2 Description contenant un mot de passe

Exemple :

```text
description: temp password: Welcome2026!
```

C’est une erreur fréquente dans les labs et parfois dans le réel.

Recherche LDAP :

```text
(description=*pass*)
```

## 24.3 SPNs sur comptes faibles

Un compte de service avec SPN et mot de passe faible est exposé.

Mesures :

- gMSA ;
- mot de passe long ;
- privilèges minimaux ;
- rotation ;
- surveillance.

## 24.4 Pré-auth Kerberos désactivée

Flag dangereux :

```text
Do not require Kerberos preauthentication
```

Mesure :

- le désactiver sauf cas exceptionnel documenté.

## 24.5 Groupes privilégiés trop larges

Exemples :

- trop de Domain Admins ;
- anciens prestataires encore membres ;
- groupes nested invisibles ;
- comptes de service admin ;
- utilisateurs standards dans groupes locaux admin.

Mesures :

- revue régulière ;
- principe du moindre privilège ;
- Just Enough Administration ;
- Privileged Access Workstations ;
- Tier model.

## 24.6 GPOs modifiables par des non-admins

Risque élevé.

Vérifier :

- qui peut éditer ;
- qui peut lier ;
- qui peut modifier la sécurité ;
- à quelles OUs la GPO s’applique.

## 24.7 Partages trop ouverts

Exemple :

```text
Everyone: Full Control
Authenticated Users: Write
Domain Users: Modify
```

Mesures :

- lecture minimale ;
- écriture contrôlée ;
- audit des accès ;
- classification ;
- pas de secrets.

## 24.8 MachineAccountQuota

Par défaut, dans certains environnements AD, les utilisateurs peuvent créer un certain nombre de comptes machines. Cette configuration peut être abusée dans des chemins avancés.

Mesure :

- réduire MachineAccountQuota si non nécessaire ;
- surveiller les créations de comptes machines ;
- déléguer proprement les joins au domaine.

## 24.9 Comptes inactifs

Risques :

- comptes oubliés ;
- mots de passe anciens ;
- groupes privilégiés non retirés ;
- comptes prestataires.

Mesures :

- désactivation ;
- processus d’offboarding ;
- revue RH/IT ;
- expiration.

## 24.10 Mauvaise séparation admin/user

Erreur classique :

- le même compte sert à lire les mails et administrer des serveurs.

Risque :

- compromission poste utilisateur → compromission compte admin.

Mesures :

- compte utilisateur standard ;
- compte admin séparé ;
- admin seulement depuis PAW ;
- pas d’accès internet depuis comptes admin ;
- tiering.

---

# 25. Détection, durcissement et bonnes pratiques

## 25.1 Modèle Tier

Le modèle Tier sépare les niveaux d’administration.

```text
Tier 0 : AD, Domain Controllers, PKI, identity core
Tier 1 : serveurs applicatifs
Tier 2 : postes utilisateurs
```

Principe :

> Un compte Tier 0 ne doit jamais se connecter à une machine Tier 1 ou Tier 2 non maîtrisée.

## 25.2 Protéger les Domain Controllers

Bonnes pratiques :

- DCs dédiés ;
- patching ;
- pas d’applications tierces inutiles ;
- accès admin strict ;
- sauvegardes sécurisées ;
- monitoring ;
- Defender/EDR compatible DC ;
- journalisation renforcée ;
- pas de sessions interactives inutiles ;
- firewall restrictif ;
- LDAP signing/channel binding selon contexte ;
- désactivation progressive de NTLM si possible.

## 25.3 Protéger les comptes privilégiés

- comptes séparés ;
- MFA si applicable ;
- Protected Users ;
- smart cards / certificats selon contexte ;
- pas de délégation ;
- mots de passe très forts ;
- pas de connexion sur postes utilisateurs ;
- revue de groupes ;
- alertes sur ajout à Domain Admins.

## 25.4 Gérer les comptes de service

- préférer gMSA ;
- mots de passe longs ;
- pas Domain Admin ;
- privilèges minimaux ;
- SPNs documentés ;
- rotation ;
- pas de password dans scripts.

## 25.5 Sécuriser les GPOs

- limiter les éditeurs ;
- journaliser les modifications ;
- sauvegarder les GPOs ;
- nommage clair ;
- ne pas surcharger Default Domain Policy ;
- séparer politiques mot de passe, hardening, scripts ;
- tester avant déploiement ;
- documenter les exceptions.

## 25.6 Sécuriser SYSVOL/NETLOGON

- supprimer secrets ;
- rechercher anciens XML GPP ;
- vérifier permissions ;
- auditer scripts ;
- éviter mots de passe en clair ;
- utiliser un coffre-fort.

## 25.7 Surveiller les événements importants

Événements Windows utiles :

| Event ID | Signification |
|---:|---|
| 4624 | Logon réussi |
| 4625 | Échec logon |
| 4672 | Privilèges spéciaux attribués à une session |
| 4720 | Compte utilisateur créé |
| 4722 | Compte activé |
| 4726 | Compte supprimé |
| 4728 | Ajout à groupe global privilégié |
| 4732 | Ajout à groupe local |
| 4738 | Compte modifié |
| 4740 | Compte verrouillé |
| 4768 | TGT Kerberos demandé |
| 4769 | TGS Kerberos demandé |
| 4771 | Échec pré-auth Kerberos |
| 4776 | Authentification NTLM |
| 5136 | Objet AD modifié |
| 5140 | Accès à partage réseau |
| 7045 | Service installé |

## 25.8 Détection d’idées d’attaque

| Risque | Détection possible |
|---|---|
| Password spraying | Nombreux 4625 répartis sur plusieurs comptes |
| Kerberoasting | Pics de 4769 pour SPNs inhabituels |
| AS-REP Roasting | 4768 sans pré-auth pour comptes suspects |
| Ajout à groupe admin | 4728 / 4732 |
| GPO modifiée | 5136 + logs GPMC |
| DCSync | Événements Directory Service + surveillance réplication |
| WinRM latéral | 4624 type 3, PowerShell logs, 5985 |
| Service créé | 7045 |
| Accès SYSVOL massif | 5140 / accès SMB |

---

# 26. TP guidés

## TP 1 — Identifier le Domain Controller

Objectif : trouver le DC depuis Kali.

Étapes :

1. Identifier le subnet.
2. Scanner le réseau.
3. Scanner les ports AD.
4. Identifier la machine ayant DNS/Kerberos/LDAP/SMB/GC.
5. Noter le nom NetBIOS et le domaine.

Commandes :

```bash
ip route
nmap -sn <SUBNET>
nmap -sV -sC -p 53,88,135,139,389,445,464,636,3268,3269 <IP>
nmap --script smb-os-discovery -p445 <IP>
```

Livrable :

```text
DC IP:
Hostname:
Domain:
Forest:
Ports AD observés:
Preuves:
```

## TP 2 — Comprendre LDAP

Objectif : interroger l’annuaire.

Étapes :

1. Trouver le Base DN.
2. Lister quelques users.
3. Lister quelques groups.
4. Identifier les groupes privilégiés.

Commandes :

```bash
ldapsearch -x -H ldap://<IP_DC> -s base namingcontexts
ldapsearch -x -H ldap://<IP_DC> -D "corp\\labuser" -w 'P@ssw0rd123!' -b "DC=corp,DC=local" "(objectClass=user)" sAMAccountName
ldapsearch -x -H ldap://<IP_DC> -D "corp\\labuser" -w 'P@ssw0rd123!' -b "DC=corp,DC=local" "(objectClass=group)" cn member
```

Livrable :

```text
Base DN:
Nombre de users observés:
Groupes sensibles:
Comptes intéressants:
Hypothèses:
```

## TP 3 — SYSVOL et NETLOGON

Objectif : comprendre le rôle des partages de domaine.

Étapes :

1. Lister les partages.
2. Accéder à SYSVOL.
3. Observer la structure.
4. Chercher scripts et fichiers de configuration.
5. Documenter les risques.

Commandes :

```bash
smbclient -L //<IP_DC> -U 'corp\labuser'
smbclient //<IP_DC>/SYSVOL -U 'corp\labuser'
```

Recherche locale après téléchargement :

```bash
grep -Rni "password\|passwd\|pwd\|secret\|token\|cpassword" .
```

Livrable :

```text
Partages accessibles:
Fichiers intéressants:
Présence de secrets:
Risques:
Recommandations:
```

## TP 4 — GPO

Objectif : comprendre l’application des GPOs.

Questions :

1. Où sont stockées les GPOs ?
2. Quel est l’ordre LSDOU ?
3. Que fait “Enforced” ?
4. Que fait “Block inheritance” ?
5. Pourquoi une GPO modifiable est-elle dangereuse ?
6. Quelle différence entre GPO computer et user ?

Livrable :

```text
Schéma d'application GPO:
Exemple d'une GPO sécurité:
Exemple d'une mauvaise configuration:
Correction proposée:
```

## TP 5 — Groupes et chemins de privilèges

Objectif : comprendre l’impact des groupes imbriqués.

Construire ce graphe :

```text
alice -> GG_IT_Users -> DL_Server_Admins -> Local Administrators on SRV-01
```

Questions :

1. Alice est-elle Domain Admin ?
2. Alice est-elle dangereuse ?
3. Sur quelles machines Alice peut-elle agir ?
4. Que se passe-t-il si un Domain Admin se connecte à SRV-01 ?
5. Comment réduire le risque ?

## TP 6 — Kerberos

Objectif : expliquer Kerberos sans outil.

À produire :

```text
1. Définition de KDC
2. Définition de TGT
3. Définition de TGS
4. Rôle de krbtgt
5. Rôle de SPN
6. Différence entre authentification et autorisation
7. Pourquoi l'heure est importante
```

---

# 27. Checklists opérationnelles

## 27.1 Checklist reconnaissance AD depuis Kali

```text
[ ] Identifier le subnet
[ ] Découvrir les hôtes
[ ] Identifier les DCs
[ ] Identifier le domaine
[ ] Scanner les ports AD
[ ] Vérifier SMB signing
[ ] Vérifier accès anonyme SMB
[ ] Vérifier LDAP anonymous bind
[ ] Tester credentials fournis en lab
[ ] Lister shares accessibles
[ ] Lire SYSVOL/NETLOGON
[ ] Chercher secrets dans scripts/configs
[ ] Lister users
[ ] Lister groups
[ ] Identifier groupes privilégiés
[ ] Identifier SPNs
[ ] Identifier comptes sans pré-auth
[ ] Identifier machines accessibles WinRM/RDP
[ ] Cartographier chemins de privilèges
[ ] Documenter preuves
```

## 27.2 Checklist audit users

```text
[ ] Comptes désactivés
[ ] Comptes inactifs
[ ] Comptes avec password never expires
[ ] Comptes sans Kerberos pre-auth
[ ] Comptes avec description sensible
[ ] Comptes de service avec SPN
[ ] Comptes privilégiés
[ ] Comptes prestataires
[ ] Comptes partagés
[ ] Comptes avec logon scripts
```

## 27.3 Checklist audit groups

```text
[ ] Domain Admins
[ ] Enterprise Admins
[ ] Schema Admins
[ ] Administrators
[ ] Account Operators
[ ] Backup Operators
[ ] Server Operators
[ ] DNSAdmins
[ ] Group Policy Creator Owners
[ ] Remote Management Users
[ ] Remote Desktop Users
[ ] Groupes imbriqués
[ ] Groupes vides ou obsolètes
[ ] Groupes avec délégations spéciales
```

## 27.4 Checklist audit GPO

```text
[ ] GPOs liées au domaine
[ ] GPOs liées aux OUs sensibles
[ ] Droits d’édition
[ ] Droits de link
[ ] Security filtering
[ ] WMI filtering
[ ] Enforced
[ ] Block inheritance
[ ] Scripts startup/logon
[ ] Tâches planifiées
[ ] Group Policy Preferences
[ ] Paramètres d’admin local
[ ] Paramètres WinRM/RDP
[ ] Paramètres Defender/Firewall
[ ] Sauvegardes et documentation
```

## 27.5 Checklist hardening minimal

```text
[ ] Réduire Domain Admins
[ ] Comptes admin séparés
[ ] Tiering
[ ] PAW pour Tier 0
[ ] Windows LAPS
[ ] Mots de passe de service longs
[ ] gMSA
[ ] Désactiver SMBv1
[ ] Activer SMB signing selon besoin
[ ] LDAP signing/channel binding selon contexte
[ ] Surveiller NTLM
[ ] Protéger SYSVOL
[ ] Nettoyer scripts
[ ] Auditer ACLs
[ ] Surveiller Kerberos
[ ] Sauvegarder AD
[ ] Tester restauration AD
[ ] Journaliser changements de groupes/GPOs
```

---

# 28. Questions d’auto-évaluation

## 28.1 Questions fondamentales

1. Qu’est-ce qu’Active Directory ?
2. Quelle différence entre AD et AD DS ?
3. Qu’est-ce qu’un Domain Controller ?
4. Pourquoi DNS est-il essentiel à AD ?
5. Quelle différence entre authentication et authorization ?
6. Qu’est-ce qu’un domaine ?
7. Qu’est-ce qu’une forêt ?
8. Qu’est-ce qu’une OU ?
9. Quelle différence entre OU et Group ?
10. Qu’est-ce qu’un SID ?

## 28.2 Questions LDAP

1. Que signifie LDAP ?
2. À quoi sert le Base DN ?
3. Que représente `DC=corp,DC=local` ?
4. Que représente `CN=Alice,OU=Users,DC=corp,DC=local` ?
5. Quel port utilise LDAP ?
6. Quel port utilise LDAPS ?
7. À quoi sert le Global Catalog ?
8. Comment rechercher les objets avec SPN ?
9. Pourquoi LDAP est important pour un attaquant ?
10. Pourquoi LDAP doit être protégé ?

## 28.3 Questions Kerberos

1. Qu’est-ce qu’un KDC ?
2. Quelle différence entre AS et TGS ?
3. Qu’est-ce qu’un TGT ?
4. Qu’est-ce qu’un TGS ?
5. Qu’est-ce qu’un SPN ?
6. Quel est le rôle de krbtgt ?
7. Pourquoi l’heure est importante ?
8. Pourquoi un compte avec SPN faible est dangereux ?
9. Qu’est-ce que l’AS-REP Roasting ?
10. Pourquoi Kerberos est préférable à NTLM ?

## 28.4 Questions GPO

1. Qu’est-ce qu’une GPO ?
2. Où sont stockées les GPOs ?
3. Que signifie LSDOU ?
4. Qu’est-ce que Block Inheritance ?
5. Qu’est-ce que Enforced ?
6. Qu’est-ce que Security Filtering ?
7. Pourquoi une GPO modifiable peut mener à une compromission ?
8. Quelle différence entre Computer Configuration et User Configuration ?
9. Que contient SYSVOL ?
10. Pourquoi faut-il auditer les scripts de connexion ?

## 28.5 Questions sécurité

1. Pourquoi Active Directory est-il une cible majeure ?
2. Pourquoi Domain Admin est-il critique ?
3. Pourquoi les groupes imbriqués compliquent l’audit ?
4. Qu’est-ce qu’un chemin d’escalade ?
5. Pourquoi les comptes de service sont sensibles ?
6. Pourquoi un partage lisible peut être dangereux ?
7. Pourquoi WinRM peut aider au mouvement latéral ?
8. Qu’est-ce qu’un droit GenericAll ?
9. Pourquoi DCSync est critique ?
10. Comment réduire les risques AD ?

---

# 29. Glossaire

| Terme | Définition |
|---|---|
| AD | Active Directory, service d’annuaire Microsoft. |
| AD DS | Active Directory Domain Services. |
| DC | Domain Controller. |
| LDAP | Protocole d’accès à l’annuaire. |
| LDAPS | LDAP chiffré via TLS. |
| Kerberos | Protocole d’authentification basé sur tickets. |
| NTLM | Ancien protocole d’authentification Windows. |
| KDC | Key Distribution Center. |
| TGT | Ticket Granting Ticket. |
| TGS | Ticket Granting Service ou ticket de service selon contexte. |
| SPN | Service Principal Name. |
| OU | Organizational Unit. |
| GPO | Group Policy Object. |
| SYSVOL | Partage répliqué contenant GPOs/scripts. |
| NETLOGON | Partage de scripts de logon. |
| SID | Security Identifier. |
| RID | Relative Identifier, partie finale du SID. |
| ACL | Access Control List. |
| ACE | Access Control Entry. |
| DACL | Discretionary ACL. |
| SACL | System ACL pour audit. |
| Forest | Conteneur logique AD le plus haut. |
| Domain | Périmètre logique d’administration et sécurité. |
| Tree | Ensemble de domaines DNS contigus. |
| Trust | Relation de confiance entre domaines/forêts. |
| FSMO | Flexible Single Master Operations. |
| Global Catalog | Catalogue partiel des objets de la forêt. |
| WinRM | Windows Remote Management. |
| RDP | Remote Desktop Protocol. |
| SMB | Server Message Block. |
| BloodHound | Outil de graphe pour analyser chemins AD. |
| Tier 0 | Niveau critique : AD, DC, PKI, identité. |
| gMSA | Group Managed Service Account. |
| LAPS | Local Administrator Password Solution. |

---

# 30. Sources et ressources complémentaires

## Documentation Microsoft officielle

- Active Directory Domain Services Overview  
  https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview

- Group Policy overview  
  https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-policy/group-policy-overview

- Kerberos authentication overview  
  https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview

- Active Directory Security Groups  
  https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups

- Manage User Accounts in Windows Server  
  https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage-user-accounts-in-windows-server

- Best practices for securing Active Directory  
  https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory

- Windows Remote Management documentation  
  https://learn.microsoft.com/en-us/windows/win32/winrm/portal

- WinRM installation and configuration  
  https://learn.microsoft.com/en-us/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

## Références sécurité

- MITRE ATT&CK — Domain Trust Discovery  
  https://attack.mitre.org/techniques/T1482/

- MITRE ATT&CK — Permission Groups Discovery: Domain Groups  
  https://attack.mitre.org/techniques/T1069/002/

- MITRE ATT&CK — Group Policy Modification  
  https://attack.mitre.org/techniques/T1484/001/

- MITRE ATT&CK — Steal or Forge Kerberos Tickets  
  https://attack.mitre.org/techniques/T1558/

- MITRE ATT&CK — Kerberoasting  
  https://attack.mitre.org/techniques/T1558/003/

- CISA — Red Team Shares Key Findings to Improve Monitoring and Hardening of Networks  
  https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-059a

- NSA/CISA/International guidance — Detecting and Mitigating Active Directory Compromises  
  https://media.defense.gov/2024/sep/25/2003553985/-1/-1/0/ctr-detecting-and-mitigating-ad-compromises.pdf

- CIS — Active Directory and Group Policy Management Best Practices  
  https://www.cisecurity.org/insights/white-papers/active-directory-group-policy-management-best-practices

## Outils à connaître en lab autorisé

- Nmap
- smbclient
- ldapsearch
- rpcclient
- enum4linux-ng
- NetExec
- Evil-WinRM
- BloodHound / SharpHound
- Impacket
- PowerView
- Windows Event Viewer
- Sysmon
- Microsoft Defender for Identity

---

# Conclusion

Active Directory n’est pas simplement un “annuaire Windows”. C’est le centre nerveux de l’identité, des accès et de l’administration dans de nombreux réseaux d’entreprise.

Pour maîtriser AD, il faut comprendre quatre couches :

```text
1. Structure
   Forest, domain, OU, users, groups, computers

2. Protocoles
   DNS, LDAP, Kerberos, NTLM, SMB, WinRM

3. Permissions
   SID, groups, ACL, GPO, delegation

4. Sécurité
   Misconfigurations, lateral movement, privilege escalation, hardening
```

Le bon réflexe cyber n’est pas de chercher directement “l’exploit magique”. Dans Active Directory, la majorité des chemins viennent de la compréhension :

- des identités ;
- des groupes ;
- des relations ;
- des héritages ;
- des permissions ;
- des anciennes configurations ;
- des habitudes d’administration.

Un bon pentester AD sait lire un domaine comme une carte.  
Un bon défenseur AD sait réduire les chemins dangereux avant qu’un attaquant ne les exploite.

