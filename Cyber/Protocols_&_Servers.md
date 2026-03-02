# Protocols and Servers — Cours ultra détaillé (Kali Linux 2023.2)

> Objectif : comprendre **à quoi servent les protocoles**, **comment ils fonctionnent**, **quels ports ils utilisent**, **comment les reconnaître en audit**, et **comment les sécuriser**.

---

## 0) Pré-requis : vocabulaire minimum

### Protocole
Un **protocole** = un ensemble de règles qui définissent **comment** deux systèmes communiquent (format des messages, ordre, erreurs, sécurité…).

### Service / serveur
Un **serveur** (service) = un programme qui **écoute** sur un port (ex : `sshd` sur 22/TCP) et répond à un protocole (SSH).

### Client
Un **client** = un programme qui initie la connexion (ex : `ssh user@host`).

### Socket
Un **socket** = l’“extrémité” d’une communication réseau (IP + port + protocole).

---

## 1) Les couches réseau (pile TCP/IP) — ce que tu dois visualiser

La pile TCP/IP (pratique, côté cyber) :

1. **Link (L2)** : Ethernet / Wi‑Fi (MAC, trames)
2. **Internet (L3)** : IP, ICMP (routage)
3. **Transport (L4)** : TCP / UDP (ports, fiabilité)
4. **Application (L7)** : DNS, HTTP(S), SMTP, SMB, LDAP, NFS, SNMP, SSH…

### TCP vs UDP (ultra important)
- **TCP** : orienté connexion, fiable, ordre garanti, contrôle de congestion.
  - “Je veux être sûr que chaque octet arrive dans l’ordre.”
- **UDP** : sans connexion, plus simple/rapide, pas de garantie.
  - “Je préfère la vitesse, je gère la perte autrement.”

### ICMP (diagnostic)
- Utilisé pour `ping`, `traceroute` (selon implémentation), erreurs réseau.
- Ne porte pas de “ports”, c’est L3.

---

## 2) Ports et numéros de ports : le “standard” du diagnostic

### Port = “porte d’entrée” applicative
Un port identifie **quel service** reçoit le trafic sur une machine.

- **Well-known ports** : 0–1023 (services standards)
- **Registered** : 1024–49151
- **Dynamic/ephemeral** : 49152–65535 (souvent ports clients)

### Pourquoi c’est critique en pentest/défense ?
- Tu peux **deviner le service** (ex: 445 → SMB).
- Tu peux **scanner** et **inventorier** les surfaces d’attaque.
- Tu peux repérer du **shadow IT** (services non autorisés).

### Commandes Kali utiles
```bash
sudo ss -lntup
sudo netstat -tulpn
sudo lsof -i -P -n
```

---

## 3) Méthodo d’identification des services (recon “propre”)

### Étape 1 — Scan des ports
```bash
nmap -sS -sV -p- --min-rate 2000 -T4 $TARGET
```

### Étape 2 — Détection de scripts NSE ciblés
```bash
nmap -sV --script "default and safe" -p 22,25,80,443,445,389,2049,161 $TARGET
```

### Étape 3 — Bannière / handshake applicatif (preuve)
- SMTP : `nc $TARGET 25` puis `EHLO`
- HTTPS : `openssl s_client -connect $TARGET:443 -servername host`
- SMB : `smbclient -L //$TARGET -N`
- SNMP : `snmpwalk -v2c -c public $TARGET 1.3.6.1.2.1.1`

---

## 4) NFS — Network File System

### 4.1 But et usage
**NFS** permet de **partager un système de fichiers** sur le réseau :
- Un serveur “exporte” des répertoires
- Un client “monte” ces répertoires comme un disque local

**Objectif** : partager des fichiers (souvent Linux/Unix) dans un LAN/DMZ.

### 4.2 Ports typiques
- **2049/TCP,UDP** : NFS
- **111/TCP,UDP** : rpcbind/portmapper (historique, critique en audit)
- Et selon versions : `mountd`, `nlockmgr`, etc. (ports dynamiques RPC si mal configuré)

### 4.3 Versions NFS (vision sécurité)
- **NFSv3** : souvent dépendant de RPC/portmapper, plus exposé si filtrage laxiste
- **NFSv4** : plus intégré, moins de services RPC annexes, support d’auth plus moderne

### 4.4 Comment ça marche (simplifié)
1. Le client découvre les exports (liste des partages)
2. Il demande à monter un export
3. Les accès fichiers sont traduits en requêtes réseau

### 4.5 Recon & tests sur Kali
Lister les exports :
```bash
showmount -e $TARGET
```

Interroger RPC :
```bash
rpcinfo -p $TARGET
```

Monter un export (si autorisé) :
```bash
sudo mkdir -p /mnt/nfs
sudo mount -t nfs -o vers=3 $TARGET:/export /mnt/nfs
ls -la /mnt/nfs
```

### 4.6 Risques & erreurs classiques
- Exports ouverts à `*` (tous) ou à un réseau trop large
- Options dangereuses :
  - `no_root_squash` : **root côté client** devient root sur le partage → souvent escalade
  - `rw` sans contrôle
- Dépendance à RPC et ports dynamiques difficiles à filtrer

### 4.7 Défense (best practices)
- Préférer **NFSv4** et limiter strictement les clients
- Utiliser `root_squash` (par défaut) + permissions correctes
- Filtrer au firewall : **uniquement** les IP clientes nécessaires
- Surveiller les montages et accès (auditd, logs)

---

## 5) SMTP — Simple Mail Transfer Protocol

### 5.1 But et rôle
**SMTP** sert à **transporter les emails** entre :
- un client (MUA) et un serveur (MSA)
- des serveurs entre eux (MTA ↔ MTA)

SMTP ne sert pas à “lire” les mails (ça c’est IMAP/POP3).

### 5.2 Ports typiques
- **25/TCP** : MTA ↔ MTA (transfert entre serveurs)
- **587/TCP** : Submission (client → serveur) (souvent avec authent)
- **465/TCP** : SMTPS (TLS implicite, encore vu)

### 5.3 Comment SMTP envoie un email (séquence)
1. Connexion TCP (25/587/465)
2. Bannière : `220 ...`
3. Négociation :
   - `EHLO domaine`
   - options (`SIZE`, `AUTH`, `STARTTLS`…)
4. Adresse expéditeur : `MAIL FROM:<...>`
5. Destinataire : `RCPT TO:<...>`
6. Contenu : `DATA` puis message, fin par `\r\n.\r\n`
7. Fin : `QUIT`

### 5.4 Démo rapide en “preuve protocolaire”
```bash
nc -nv $TARGET 25
EHLO example.com
MAIL FROM:<test@example.com>
RCPT TO:<user@target.tld>
DATA
Subject: test
Hello
.
QUIT
```

### 5.5 STARTTLS vs SMTPS
- **STARTTLS** : démarre en clair, puis “upgrade” en TLS
- **SMTPS (465)** : TLS dès le début

Test TLS :
```bash
openssl s_client -starttls smtp -connect $TARGET:587
```

### 5.6 Risques majeurs SMTP
- **Open relay** : le serveur relaie des mails pour n’importe qui → spam, blacklist
- Auth faible / brute-force sur `AUTH`
- Mauvaise config TLS (ciphers faibles, pas de MTA‑STS, etc.)
- Usurpation d’identité sans SPF/DKIM/DMARC (contexte domaine)

### 5.7 Défense
- Bloquer l’open relay (restriction `RCPT TO`)
- Forcer TLS sur submission (587), limiter auth (rate limit, MFA si possible)
- Mettre en place SPF/DKIM/DMARC (niveau domaine)
- Patcher et durcir (Postfix/Exim/Exchange…)

---

## 6) SNMP — Simple Network Management Protocol

### 6.1 But
**SNMP** sert à **surveiller et gérer** des équipements réseau (switch, routeur, imprimante, serveurs…).

Il expose des infos : interface up/down, CPU, RAM, uptime, tables de routage, etc.

### 6.2 Ports typiques
- **161/UDP** : requêtes SNMP
- **162/UDP** : traps (alertes envoyées par l’équipement)

### 6.3 Concept clé : MIB / OID
- **MIB** = base “d’objets” monitorables
- **OID** = identifiant numérique d’une métrique
  - Exemple : `1.3.6.1.2.1.1` (system)

### 6.4 SNMP v1/v2c vs v3 (sécurité)
- **v1/v2c** : “community string” (souvent `public`/`private`), quasi équivalent à un mot de passe **en clair**
- **v3** : authentification + chiffrement (recommandé)

### 6.5 Recon / enumeration (Kali)
Test classique (v2c) :
```bash
snmpwalk -v2c -c public $TARGET 1.3.6.1.2.1.1
```

Infos utiles (si accessible) :
- Uptime
- Nom/description système
- Interfaces réseau (noms, MAC, IP)
- Routes
- Process (selon implémentation)

### 6.6 Risques SNMP
- Community string par défaut → fuite d’infos massives
- SNMP “write” activé (`private`) → changement de config (grave)
- Exposition sur Internet (trop fréquent) → cartographie attaquants

### 6.7 Défense
- Désactiver SNMP si inutile
- Sinon : **SNMPv3** + ACL strictes + segmentation réseau
- Filtrer 161/162 en interne uniquement
- Superviser les requêtes anormales

---

## 7) SMB — Server Message Block (partage Windows)

### 7.1 But
**SMB** permet :
- Partage de fichiers/dossiers
- Partage d’imprimantes
- Accès à des ressources Windows/AD

### 7.2 Ports typiques
- **445/TCP** : SMB direct (principal aujourd’hui)
- **139/TCP** : NetBIOS/SMB historique (encore présent parfois)

### 7.3 Interop Windows/Linux
Linux peut parler SMB via Samba (`smbclient`, `mount -t cifs`).

### 7.4 Recon rapide (Kali)
Lister les partages :
```bash
smbclient -L //$TARGET -N
```

Enum plus riche :
```bash
nmap -p 445 --script smb-os-discovery,smb-enum-shares,smb-enum-users $TARGET
```

### 7.5 Risques SMB
- SMBv1 activé (vieux, vulnérable, propagation type WannaCry)
- Partages anonymes (guest) ou droits excessifs
- NTLM relay, pass-the-hash, weak credentials
- Exposition latérale interne (mouvement latéral)

### 7.6 Défense
- Désactiver SMBv1
- Restreindre les partages, moindre privilège
- SMB signing (selon contexte), durcir NTLM, préférer Kerberos en AD
- Segmentation réseau, filtrage 445

---

## 8) LDAP — annuaires, authentification, autorisation

### 8.1 But
**LDAP** = protocole d’accès à un **annuaire** (base hiérarchique d’identités et d’objets).
- Utilisé par **Active Directory** (LDAP + Kerberos + autres)
- Sert à :
  - chercher des utilisateurs/groupes
  - authentifier (bind)
  - obtenir des attributs (mail, groupe, droits…)

### 8.2 Ports typiques
- **389/TCP,UDP** : LDAP
- **636/TCP** : LDAPS (TLS implicite)
- StartTLS possible sur 389

### 8.3 Concepts
- **DN** (Distinguished Name) : chemin complet d’un objet
- **OU** (Organizational Unit) : conteneur logique
- **Bind** : opération d’auth LDAP
- **Search** : requête (filtre LDAP)
- **ACL** : contrôle d’accès aux attributs

### 8.4 Recon (Kali)
Découverte et test de bind anonymes (selon config) :
```bash
ldapsearch -x -H ldap://$TARGET -s base
```

StartTLS :
```bash
ldapsearch -x -H ldap://$TARGET -ZZ -s base
```

### 8.5 Risques LDAP
- Bind anonyme + lecture d’attributs sensibles
- LDAP sans TLS → creds en clair (bind simple)
- Mauvais contrôles d’accès (exposition de groupes, users, etc.)
- LDAP injection (côté application) si filtres construits sans validation

### 8.6 Défense
- Forcer TLS (LDAPS / StartTLS), interdire bind simple en clair
- Désactiver bind anonyme si inutile
- ACL strictes
- Patcher et durcir les services AD/LDAP

---

## 9) RDP — Remote Desktop Protocol (bureau à distance)

### 9.1 But
**RDP** permet de se connecter à une machine Windows et d’obtenir une session graphique distante.

### 9.2 Port typique
- **3389/TCP** : RDP

### 9.3 Recon / test
```bash
nmap -p 3389 -sV --script rdp-enum-encryption,rdp-ntlm-info $TARGET
```

Client :
```bash
xfreerdp /v:$TARGET /u:USER /p:'PASS'
```

### 9.4 Risques RDP (très fréquent en incidents)
- Exposition sur Internet → bruteforce, creds stuffing
- Vulnérabilités RDP historiques (ex: BlueKeep, selon patch)
- Mauvaise politique de mots de passe / pas de MFA
- Mouvement latéral si un compte est compromis
- Redirection clipboard/drive (exfiltration)

### 9.5 Défense
- Ne jamais exposer RDP directement sur Internet (préférer VPN / bastion)
- MFA + NLA (Network Level Authentication)
- Filtrage IP, segmentation
- Durcissement GPO, verrouillage après échecs
- Patching strict (Windows Update, cycles)

---

## 10) Protocoles “secure” vs “insecure” (comparaison attendue)

### 10.1 Exemples
- HTTP (80) → **HTTPS (443)** : TLS, confidentialité + intégrité
- FTP (21) → **SFTP (22)** ou FTPS (990/21+TLS)
- Telnet (23) → **SSH (22)**
- LDAP (389) → **LDAPS (636)** ou StartTLS
- SMTP (25/587) → SMTP + **STARTTLS**
- IMAP (143) → **IMAPS (993)**

### 10.2 Ce que TLS apporte
- Chiffrement : confidentialité
- Intégrité : anti‑modification
- Authentification : certificat serveur (et parfois client)

⚠️ Important : “HTTPS” ne signifie pas “site sûr”, seulement “transport chiffré + identité serveur”.

---

## 11) SSH — pourquoi c’est la base du remote secure

### 11.1 Avantages
- Chiffrement fort + intégrité
- Auth robuste (clé publique/privée)
- Port forwarding / tunnels (pivot)
- SFTP / SCP pour transferts

### 11.2 Hardening SSH (défense)
- Désactiver login root, forcer clés
- MFA si possible
- Fail2ban / rate-limit
- Ciphers modernes, patching OpenSSH

---

## 12) Chiffrement réseau : différencier les “types”

### 12.1 Chiffrement “en transit”
- TLS (HTTPS, SMTPS, LDAPS…)
- SSH (et SFTP)
- IPsec (VPN)

### 12.2 Auth vs chiffrement
- Un protocole peut être chiffré sans être bien authentifié (certificats mal gérés).
- Un protocole peut être authentifié sans être chiffré (cas dangereux).

### 12.3 Symétrique vs asymétrique (ultra simple)
- **Asymétrique** (RSA/ECDSA/Ed25519) : échange de clés, signature
- **Symétrique** (AES/ChaCha20) : chiffrement des données, rapide

TLS combine les deux : asymétrique pour démarrer, symétrique pour chiffrer le flux.

---

## 13) Patching et mises à jour : pourquoi c’est non négociable

### 13.1 Pourquoi “les protocoles” vieillissent mal
- Failles d’implémentation (bugs)
- Algorithmes cryptographiques cassés (ex: vieux ciphers)
- Standards dépréciés (SMBv1, SSLv3, TLS1.0…)

### 13.2 Bon réflexe défense
- Inventaire des services (ports ouverts)
- Cycle de patching
- Désactivation des services non utilisés
- Monitoring (logs + alerting)

---

## 14) Fiches rapides : ports à connaître (minimum vital)

| Service | Protocole | Port(s) | Usage |
|---|---|---:|---|
| SSH | TCP | 22 | Remote shell sécurisé |
| SMTP | TCP | 25 / 587 / 465 | Envoi mail |
| DNS | UDP/TCP | 53 | Résolution noms |
| HTTP/HTTPS | TCP | 80 / 443 | Web |
| SMB | TCP | 445 (139) | Partage Windows |
| LDAP/LDAPS | TCP | 389 / 636 | Annuaire / auth |
| RDP | TCP | 3389 | Bureau distant |
| NFS | TCP/UDP | 2049 (+111) | Partage Unix |
| SNMP | UDP | 161/162 | Supervision |
| DHCP | UDP | 67/68 | Attribution IP |

---

## 15) Mini-labs guidés (pour “savoir expliquer sans Google”)

### Lab A — Identifier un service inconnu
1. Scan complet : `nmap -p- -sS -sV $TARGET`
2. Vérifie les ports “standards”
3. Prouve par un handshake :
   - SMTP → `nc` + `EHLO`
   - HTTPS → `openssl s_client`
   - LDAP → `ldapsearch -s base`
   - SMB → `smbclient -L`
   - SNMP → `snmpwalk`
   - NFS → `showmount`

### Lab B — Risques & mesures
Pour chaque service trouvé, réponds :
- Quelles données il expose ?
- Quelles auth ?
- Chiffrement ? (TLS/SSH ?)
- Exploits historiques ? (au moins 1 exemple)
- Comment je le durcis / je le segmente ?

---

## 16) Lien avec tes exigences de projet Bash (Holberton / Kali)

Tes scripts (quand demandés) doivent souvent respecter :
- **Kali Linux 2023.2**
- Première ligne : `#!/bin/bash`
- Fin de fichier : newline
- Remplacer la plage IP par `$1`
- Parfois : **exactement 2 lignes** (`wc -l file` → 2)

👉 Dans ce cas, on fait typiquement :
1) shebang
2) commande unique avec `$1` (ou une commande + options)

Exemple “2 lignes” générique :
```bash
#!/bin/bash
nmap -sS -sV "$1"
```

⚠️ Pour “Betty style” : c’est une contrainte C à Holberton, pas Bash. Si ton énoncé le dit, suis-le, mais dans la pratique Betty cible surtout le C.

---

## 17) Résumé (ce que tu dois savoir réciter)

- Un protocole = règles d’échange ; un service = programme qui écoute sur un port.
- TCP ≠ UDP ; ports = surface d’attaque.
- **NFS** : partage fichiers Unix ; gros risque si `no_root_squash`/exports ouverts.
- **SMTP** : transport mails ; ports 25/587/465 ; `EHLO`, `MAIL FROM`, `RCPT TO`, `DATA` ; risques open relay.
- **SNMP** : monitoring ; 161/162 ; v2c dangereux (community) ; v3 recommandé.
- **SMB** : partage Windows ; 445 ; risques SMBv1, partages ouverts, mouvement latéral.
- **LDAP** : annuaire ; 389/636 ; risques bind anonyme, pas de TLS, LDAP injection applicative.
- **RDP** : 3389 ; risques d’exposition Internet, bruteforce, patching critique.
- Protocoles “secure” = TLS/SSH ; “insecure” = clair.
- Patching + désactivation + segmentation = base de la défense.

---

## 18) Checklist d’exam / oral (10 questions flash)

1. Différence TCP/UDP ?
2. À quoi sert un port ?
3. Quels ports pour SMTP et pourquoi plusieurs ?
4. C’est quoi STARTTLS ?
5. Que permet SNMP et pourquoi v2c est risqué ?
6. À quoi sert NFS et quel piège `no_root_squash` ?
7. Pourquoi SMBv1 est un problème ?
8. LDAP : DN / OU / bind = quoi ?
9. Risques d’exposer RDP ?
10. HTTPS vs HTTP : qu’est-ce qui change réellement ?
