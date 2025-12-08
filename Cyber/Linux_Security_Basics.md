# Linux Security Basics  
*Cours complet pour la spécialisation cybersécurité (Kali / Linux)*

---

## 0. Objectifs du cours

À la fin de ce cours, tu dois être capable d’expliquer clairement :

- Ce qu’est **Linux** et une **commande Linux**
- Comment est structurée l’**architecture d’un système Linux**
- Ce qu’est la **FHS (Filesystem Hierarchy Standard)** et à quoi servent les principaux répertoires (`/etc`, `/var`, `/home`, etc.)
- Comment **protéger des fichiers et répertoires** (permissions, utilisateurs, groupes)
- Comment **surveiller et investiguer l’activité du système** (processus, journaux, réseau)
- Comment **transférer des fichiers de manière sécurisée** (`scp`, `ssh`)
- Comment **configurer et gérer un pare-feu** (`iptables`, `ufw`)
- Comment **identifier et tuer un processus malveillant**
- Comment utiliser les commandes de base de la sécurité réseau :  
  `ps`, `kill`, `netstat`, `ss`, `nmap`, `lynis`, `tcpdump`, `iptables`, `ufw`

Et côté projet Holberton :

- Comprendre pourquoi les scripts doivent être :
  - en **Bash**
  - avec la première ligne `#!/bin/bash`
  - **exactement 2 lignes**
  - sans backticks, `&&`, `||` ou `;`
  - utilisant **`$1`** pour recevoir une IP / plage d’IP
  - au **style Betty** et **exécutables**

---

## 1. C’est quoi Linux ?

### 1.1 Linux, un système d’exploitation

Un **système d’exploitation (OS)** = couche logicielle qui fait le lien entre :
- le **matériel** (CPU, RAM, disque, carte réseau…)
- les **applications** (navigateur, éditeur de texte, outils de sécurité…)

**Linux** est un noyau (*kernel*) autour duquel différentes distributions sont construites :

- **Ubuntu**, **Debian**, **Fedora**, **Arch**, **Kali**, etc.
- Chacune a son système de packages, ses choix par défaut, ses outils additionnels

### 1.2 Kali Linux dans la cybersécurité

**Kali Linux** = distribution basée sur Debian, spécialisée pour :

- **Tests d’intrusion (pentest)**
- **Audit de sécurité**
- **Forensics**
- **Red teaming / CTF**

Caractéristiques importantes :

- Livré avec un grand nombre d’outils préinstallés :
  - `nmap`, `wireshark`, `metasploit`, `burpsuite`, `hydra`, etc.
- Pensé pour des **environnements de test** (lab, VM, machines dédiées)
- Doit être **manipulé avec prudence** (beaucoup d’outils offensifs)

---

## 2. C’est quoi une commande Linux ?

Une **commande Linux** est :

- Un **programme** ou un **script** accessible dans le PATH (ex. `/bin/ls`, `/usr/bin/nmap`)
- Ou une **fonction / alias** du shell (ex. `alias ll='ls -l'`)

La forme générale :

```bash
commande [options] [arguments]
```

Exemples :

```bash
ls -l /etc
ps aux
ufw allow 22/tcp
nmap -sV 192.168.0.0/24
```

- `commande` = le binaire ou le script
- `options` = modifient le comportement (`-l`, `-sV`, `-n`, etc.)
- `arguments` = fichiers, IP, ports, répertoires, etc.

Pour **obtenir de l’aide** :

```bash
man commande      # manuel
commande --help   # aide rapide
```

---

## 3. Structure d’un système Linux

Un système Linux est construit en plusieurs couches :

1. **Matériel**  
   - CPU, RAM, disque, carte réseau, carte graphique…

2. **Kernel (noyau Linux)**  
   - Gère :
     - la mémoire
     - le CPU et l’ordonnancement des processus
     - les pilotes de périphériques (drivers)
     - le système de fichiers
     - le réseau
   - Fournit des **syscalls** aux programmes (read, write, open, etc.)

3. **Bibliothèques système**  
   - Exemple : **glibc**
   - Permettent aux programmes de dialoguer avec le noyau plus facilement

4. **Espace utilisateur (userland)**  
   - Programmes, services, démons, shells, environnement graphique…

5. **Shell**  
   - Interface en ligne de commande (CLI) :
     - `bash`, `zsh`, `sh`, etc.
   - Permet de lancer des programmes, scripts, combiner des commandes…

6. **Services / démons**  
   - Programmes qui tournent en tâche de fond :
     - Serveur SSH (`sshd`)
     - Serveur web (`apache2`, `nginx`)
     - Journaux (`rsyslogd`, `systemd-journald`)
     - etc.

---

## 4. FHS et système de fichiers Linux

### 4.1 FHS : Filesystem Hierarchy Standard

La **FHS** définit comment les répertoires principaux sont organisés sur un système Linux.

- Objectif :
  - Que tous les systèmes respectent une **structure cohérente**
  - Faciliter :
    - l’administration
    - les scripts
    - la portabilité des logiciels

**Point de départ :** `/` (root du système de fichiers)

### 4.2 Répertoires importants et rôle

Voici les plus importants à connaître pour la sécurité :

- `/`  
  Racine de tout. Tout est un fichier ou un répertoire sous `/`.

- `/bin`  
  Binaries essentiels pour tous les utilisateurs :
  - `ls`, `cp`, `mv`, `rm`, `cat`, etc.

- `/sbin`  
  Binaries systèmes pour l’**administration** :
  - `iptables`, `ifconfig` (legacy), `reboot`, etc.

- `/etc`  
  **Configuration** globale du système :
  - `passwd`, `shadow`, `hosts`, `ssh/sshd_config`, `ufw/`, etc.
  - Fichiers souvent **critiques** en sécurité

- `/home`  
  Dossiers personnels des utilisateurs :
  - `/home/user1`, `/home/user2`, etc.
  - Contient les fichiers, configs, clés SSH, etc.

- `/root`  
  Dossier personnel de l’utilisateur **root**.

- `/var`  
  Données variables :
  - **Logs** : `/var/log`
  - **Mail**, **spools**, **bases de données**, etc.
  - Cible classique lors d’attaques (logs à effacer, etc.)

- `/tmp`  
  Fichiers temporaires :
  - Accessible en écriture pour tous
  - Souvent monté avec des options de sécurité (`noexec`, `nosuid`)

- `/usr`  
  Applications et données “utilisateur” (non essentielles au boot) :
  - `/usr/bin`, `/usr/sbin`, `/usr/share`, `/usr/local`

- `/dev`  
  Fichiers de périphériques (disques, pseudo-terminaux, etc.)

- `/proc`  
  Système de fichiers virtuel exposant les **processus** et l’état du noyau :
  - `/proc/<PID>`, `/proc/cpuinfo`, `/proc/meminfo`

- `/sys`  
  Infos sur le matériel et les périphériques (autre FS virtuel).

- `/run`  
  Infos runtime (PID, sockets, etc.).

### 4.3 Intérêt sécurité de la FHS

Connaître la FHS permet de :

- Savoir où chercher :
  - les **configurations critiques** (`/etc/ssh/sshd_config`)
  - les **journaux** (`/var/log/auth.log`, `/var/log/syslog`)
  - les fichiers utilisateurs (`/home/<user>`)
- Appliquer des stratégies :
  - Permissions adaptées sur `/etc`, `/var/log`, `/home`
  - Restrictions sur `/tmp` et autres répertoires sensibles
  - Séparer certaines partitions (ex. `/home`, `/var`, `/tmp`)

---

## 5. Protéger fichiers et répertoires

### 5.1 Utilisateurs, groupes, root

Linux gère les permissions avec :

- **UID** (User ID) pour les utilisateurs
- **GID** (Group ID) pour les groupes
- Un **utilisateur spécial** : `root` (UID 0) :
  - Peut tout faire, ignore la plupart des permissions normales
  - Doit être utilisé **avec parcimonie**

Fichiers système :

- `/etc/passwd` : liste des utilisateurs
- `/etc/group` : liste des groupes
- `/etc/shadow` : mots de passe chiffrés (accessible seulement par root)

### 5.2 Permissions classiques : `rwx`

Pour chaque fichier / dossier, on a :

- `r` : read (lecture)
- `w` : write (écriture)
- `x` : execute (exécution / traversée de répertoire)

Et ces permissions sont définies pour :

- **u** : user (propriétaire)
- **g** : group (groupe)
- **o** : others (autres)

Exemple :

```bash
ls -l fichier.txt
-rw-r----- 1 alice dev 1200 Dec  8 09:00 fichier.txt
```

- `-` = fichier normal
- `rw-` = propriétaire (alice) peut lire/écrire
- `r--` = groupe (dev) peut lire
- `---` = les autres n’ont aucun droit

### 5.3 Changer les permissions : `chmod`

Deux syntaxes :

1. **Symbolique** :

```bash
chmod u+r fichier      # ajoute lecture à l'utilisateur
chmod go-rwx fichier   # enlève tous les droits pour groupe et autres
chmod u=rwx,g=rx,o= fichier
```

2. **Numérique** (octal) :

- r = 4, w = 2, x = 1 → addition
- Ex. :
  - `7` → `rwx`
  - `5` → `r-x`
  - `6` → `rw-`

```bash
chmod 640 fichier.txt  # u=rw-, g=r--, o=---
chmod 700 script.sh    # u=rwx, g=---, o=---
```

### 5.4 Changer propriétaire et groupe : `chown`, `chgrp`

```bash
chown alice fichier.txt        # change le propriétaire
chown alice:dev fichier.txt    # change propriétaire et groupe
chgrp dev fichier.txt          # change uniquement le groupe
```

### 5.5 Permissions spéciales

Pour l’instant, retiens surtout :

- **Sticky bit** (sur les répertoires) :
  - Exemple classique : `/tmp`
  - Permet à chacun d’écrire dans le répertoire, mais **on ne peut supprimer que ses propres fichiers**
  - Visible comme `t` à la fin des permissions :

```bash
ls -ld /tmp
drwxrwxrwt 10 root root 4096 Dec  8 09:00 /tmp
```

---

## 6. Surveiller et investiguer l’activité du système

### 6.1 Processus : `ps`, `top`, `htop`

#### `ps`

Commande de base pour lister les processus :

```bash
ps aux
```

- `a` : tous les utilisateurs
- `u` : format “user” lisible
- `x` : inclut les processus sans terminal

Champs importants :

- `USER` : utilisateur propriétaire du process
- `PID` : Process ID
- `%CPU`, `%MEM` : consommation
- `STAT` : état du process
- `COMMAND` : commande lancée

Exemples utile en sécurité :

```bash
ps aux | head              # voir les premiers
ps aux | grep ncat         # chercher un process suspect
ps aux | grep -E "nc|ncat" # plusieurs patterns
```

#### `top` / `htop`

- `top` affiche en temps réel les processus triés par CPU / mémoire
- `htop` (si installé) est plus ergonomique.

### 6.2 Tuer un processus : `kill`

Pour **terminer un processus** :

1. Trouver son **PID** :

```bash
ps aux | grep nom_processus
```

2. Envoyer un signal :

```bash
kill PID        # SIGTERM (15) = demande propre d'arrêt
kill -9 PID     # SIGKILL (9) = arrêt forcé
```

Exemples :

```bash
kill 1234
kill -9 1234
```

En sécurité, on préfèrera :

1. Comprendre **ce que fait** le process
2. Regarder :
   - son binaire
   - son chemin
   - son utilisateur
   - ses connexions réseau
3. Puis seulement **le tuer** si nécessaire

---

## 7. Logs et traces : /var/log, journalctl

### 7.1 Répertoire `/var/log`

- **`/var/log/auth.log`** (Debian/Kali) : authentification, sudo, sshd
- **`/var/log/syslog`** : messages système généraux
- Autres logs selon les services (apache2, nginx, etc.)

### 7.2 `journalctl` (avec systemd)

Si le système utilise **systemd** :

```bash
journalctl -xe          # logs récents avec détails
journalctl -u ssh       # logs du service SSH
journalctl --since "1 hour ago"
```

C’est crucial pour :

- voir les tentatives de connexion
- suivre une intrusion
- comprendre un comportement étrange

---

## 8. Réseau sous Linux : bases

### 8.1 Concepts rapides

- **Adresse IP** : identifiant réseau de ta machine
- **Port** : “porte” logique pour un service (22=SSH, 80=HTTP, 443=HTTPS, etc.)
- **TCP/UDP** : principaux protocoles de transport
- **Client/Serveur** : un service écoute sur un port, un client se connecte

### 8.2 Quelques commandes réseau basiques

- `ip a` ou `ip addr` : adresses IP des interfaces
- `ip route` : table de routage
- `ping` : tester la connectivité
- `traceroute` (ou `tracepath`) : chemin réseau d’un paquet

Exemples :

```bash
ip a
ip route
ping 8.8.8.8
ping google.com
```

---

## 9. Surveiller le trafic réseau : `netstat` et `ss`

### 9.1 `netstat` (legacy)

`netstat` est ancien mais encore très utilisé dans les docs / défis :

```bash
netstat -tulnp
```

- `-t` : TCP
- `-u` : UDP
- `-l` : listening (ports en écoute)
- `-n` : ne pas résoudre les noms (affiche IP/port)
- `-p` : affiche le PID/processus

Très utile pour :

- Voir quels services écoutent sur quels ports
- Associer un port à un processus

### 9.2 `ss` (remplaçant moderne)

`ss` est plus rapide et plus moderne :

```bash
ss -tulnp
```

Même logique que `netstat`. On peut filtrer, par exemple :

```bash
ss -tunap | grep 4444
```

Utilisation en sécurité :

- Détecter une **backdoor** ou un **reverse shell** en écoute
- Vérifier si un service inattendu expose un port au réseau

---

## 10. Outils réseau pour l’analyse de sécurité

### 10.1 `nmap` : scanner de ports

**`nmap`** = outil de base en cybersécurité pour :

- Scanner des plages d’IP / réseaux
- Découvrir :
  - ports ouverts
  - services
  - versions
  - parfois le système d’exploitation

Exemples :

```bash
nmap 192.168.1.10
nmap -sV 192.168.1.10      # détecte les versions des services
nmap -p 1-1000 192.168.1.10
nmap -sS 192.168.1.0/24    # scan SYN (semi-ouvert)
```

**Attention :** toujours s’assurer que tu as l’**autorisation** de scanner une cible.

### 10.2 `tcpdump` : capture de paquets

**`tcpdump`** permet de capturer le trafic :

```bash
tcpdump -i eth0
tcpdump -i eth0 port 80
tcpdump -i eth0 host 192.168.1.10
tcpdump -i eth0 port 22 -w capture.pcap
```

- `-i` : interface
- `port`, `host` : filtres
- `-w` : écrire dans un fichier

Tu peux ensuite analyser `capture.pcap` avec **Wireshark**.

### 10.3 `lynis` : audit de sécurité

**`lynis`** = outil d’audit :

```bash
lynis audit system
```

Il :

- scanne la configuration du système
- donne des **recommandations** :
  - permissions
  - services
  - pare-feu
  - logs
  - etc.

C’est très utile pour avoir une vision globale des faiblesses de ta machine.

---

## 11. Transfert sécurisé de fichiers : `scp`, `ssh`

### 11.1 SSH

**SSH** (Secure Shell) permet de :

- se connecter à distance à une machine
- faire passer un terminal crypté
- transférer des fichiers de façon sécurisée

### 11.2 `scp` : Secure Copy

Syntaxe générale :

```bash
scp [options] source destination
```

#### Local → Distant

```bash
scp fichier.txt user@10.0.0.5:/home/user/
```

#### Distant → Local

```bash
scp user@10.0.0.5:/home/user/fichier.txt .
```

#### Répertoires (option `-r`)

```bash
scp -r dossier/ user@10.0.0.5:/home/user/dossier/
```

Points importants :

- Transfert chiffré via SSH
- Authentification par mot de passe ou clé (`~/.ssh/id_rsa`…)

Alternatives :

- `sftp` (mode interactif)
- `rsync -e ssh` (plus puissant, synchronisation)

---

## 12. Pare-feu sous Linux : `iptables` et `ufw`

### 12.1 Concept de pare-feu (firewall)

Un **pare-feu** filtre les paquets réseau selon des règles :

- Autoriser / refuser :
  - une IP
  - un port
  - un protocole
- Décider d’une **politique par défaut** :
  - `ACCEPT`
  - `DROP` (recommandé en production pour INPUT)

### 12.2 `iptables` : outil bas niveau

**`iptables`** configure le pare-feu du noyau (tables et chaînes) :

- Tables principales :
  - `filter` (par défaut, filtrage de base)
  - `nat` (translation d’adresse)
- Chaînes importantes dans `filter` :
  - `INPUT` : trafic entrant
  - `OUTPUT` : trafic sortant
  - `FORWARD` : trafic routé

Exemples très simplifiés (à comprendre, pas forcément à lancer tel quel en prod) :

```bash
# Lister les règles actuelles
iptables -L -n -v

# Autoriser SSH sur le port 22
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Refuser tout le reste en entrée
iptables -P INPUT DROP
```

**Attention** : mauvaise config = tu peux te **couper toi-même** l’accès SSH 😅

### 12.3 `ufw` : Uncomplicated Firewall

`ufw` est une surcouche à `iptables` plus simple :

#### Commandes de base

```bash
ufw status
ufw enable
ufw disable
```

#### Autoriser / bloquer

```bash
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp

ufw deny 23/tcp        # bloquer telnet par exemple
```

#### Plages d’IP

```bash
ufw allow from 192.168.1.0/24 to any port 22 proto tcp
```

#### Politiques par défaut

```bash
ufw default deny incoming
ufw default allow outgoing
```

Utilisation classique en sécurité :

- **`deny incoming` / `allow outgoing`**
- Autoriser seulement les ports/services nécessaires
- Bloquer les ports inutiles / dangereux

---

## 13. Identifier et tuer des processus malveillants (pratique)

Objectif : être capable de :

1. **Repérer** des processus suspects
2. **Analyser** ce qu’ils font
3. **Les tuer** proprement si nécessaire

### 13.1 Étapes typiques

1. **Lister tous les processus :**

```bash
ps aux
```

2. **Chercher un motif suspect :**

```bash
ps aux | grep nc
ps aux | grep python
ps aux | grep reverse
```

3. **Trouver les connexions réseau associées :**

Avec `netstat` :

```bash
netstat -tulnp
netstat -tunap | grep 4444
```

Avec `ss` :

```bash
ss -tunap
ss -tunap | grep 4444
```

4. **Identifier le PID** du process (colonne `PID`).

5. **Tuer le process** :

```bash
kill PID          # essayer d’abord SIGTERM
kill -9 PID       # si nécessaire, SIGKILL
```

6. **Analyser le binaire / script** :

- Où se trouve le programme (`which`, `ls -l`, `file`) ?
- Est-ce un script dans `/tmp` ? dans `/home` ?
- Est-ce un service configuré pour redémarrer ?

---

## 14. Scripts Bash du projet (Kali)

Ton projet Holberton te donne des contraintes précises :

### 14.1 Contraintes importantes

- Éditeurs : `vi`, `vim`, `emacs`
- Scripts testés sur **Kali Linux**
- Tous tes scripts doivent :
  - Être **exactement deux lignes** (`wc -l fichier` → `2`)
  - La **première ligne** doit être :

    ```bash
    #!/bin/bash
    ```

  - La **seconde ligne** contient ta commande
  - Se terminer par un **saut de ligne** (sinon certains outils râlent)
  - **Ne pas utiliser** :
    - backticks `` ` ``
    - `&&`
    - `||`
    - `;`
  - Utiliser **`$1`** pour représenter l’IP ou la plage IP passée en argument :

    ```bash
    ./script.sh 192.168.1.0/24
    ```

  - Respecter le **style Betty** (Bash / shellcheck-like + conventions)
  - Être **exécutables** :

    ```bash
    chmod +x script.sh
    ```

### 14.2 En pratique, ça veut dire quoi ?

Tes scripts ressembleront à ça :

```bash
#!/bin/bash
commande_qui_utilise_$1
```

Exemples de patterns (ATTENTION : à adapter au projet, ce sont des exemples pédagogiques) :

```bash
#!/bin/bash
nmap -sV "$1"
```

```bash
#!/bin/bash
netstat -tulnp
```

```bash
#!/bin/bash
ps aux | grep "$1"
```

Rappels :

- Tu n’as le droit **qu’à une seule commande** sur la deuxième ligne (pas de `&&`, `;`, etc.).
- Tu dois penser ta commande en **une seule instruction**.
- Tu ne peux pas faire :

  ```bash
  # interdit
  ps aux | grep "$1" && echo "OK"
  ```

  car ça fait plusieurs commandes en chaîne.

---

## 15. Résumé – Checklist de sécurité Linux de base

Pour ancrer tout ça, voici une vision synthétique :

### 15.1 Comprendre le système

- Savoir ce qu’est :
  - le **noyau (kernel)**
  - le **shell**
  - les **processus** / **services**
  - la **FHS** (organisation des répertoires)

### 15.2 Protéger fichiers et répertoires

- Gérer les **permissions** (`chmod`, `chown`, `chgrp`)
- Comprendre **`rw-r--r--`**, **sticky bit** sur `/tmp`
- Limiter l’accès aux fichiers sensibles (`/etc/shadow`, logs, configs)

### 15.3 Surveiller les processus

- Utiliser `ps aux`, `top`, `htop`
- Identifier un **PID** suspect
- Tuer un processus (`kill`, `kill -9`) si nécessaire

### 15.4 Surveiller le réseau

- Voir les ports ouverts : `netstat -tulnp` / `ss -tulnp`
- Scanner (autorisé !) un hôte : `nmap`
- Sniffer un trafic : `tcpdump` (`-i`, `port`, `host`)

### 15.5 Auditer et renforcer

- Utiliser `lynis` pour un **audit système**
- Mettre à jour régulièrement (`apt update && apt upgrade` sur Debian/Kali)
- Désactiver les services inutiles (`systemctl`)

### 15.6 Pare-feu

- Comprendre les chaînes `INPUT`, `OUTPUT`, `FORWARD`
- Savoir lister les règles : `iptables -L -n -v`
- Savoir gérer un firewall simplifié avec `ufw` :
  - `ufw status`
  - `ufw enable`
  - `ufw allow 22/tcp`
  - `ufw default deny incoming`

### 15.7 Transfert sécurisé

- Comprendre l’usage de **SSH**
- Utiliser `scp` pour copier :
  - local → distant
  - distant → local
  - répertoires (`-r`)

---

## 16. Idées d’exercices (pour t’entraîner)

Quelques suggestions pour manipuler tout ça (dans une VM Kali) :

1. **Explorer la FHS** :
   - Liste le contenu de `/`, `/etc`, `/var`, `/home`, `/tmp`.
   - Trouve où sont stockés les logs d’authentification.

2. **Jeu sur les permissions** :
   - Crée un fichier et change ses permissions en `600`, `644`, `700`, etc.
   - Observe les effets.

3. **Processus & kill** :
   - Lance un script qui boucle (`while true; do ...; done`) en arrière-plan.
   - Retrouve son PID avec `ps aux`.
   - Tuer le processus proprement puis brutalement.

4. **Réseau & sockets** :
   - Lancer un serveur simple (par exemple avec `nc -lvp 4444`).
   - Vérifier son port avec `netstat -tulnp` ou `ss -tulnp`.

5. **Pare-feu UFW** :
   - Active `ufw`.
   - Autorise SSH.
   - Bloque un port.
   - Vérifie avec `nmap` depuis une autre machine du lab (si possible).

6. **Transfert de fichier** :
   - Utilise `scp` pour envoyer un fichier de ta VM Kali à une autre machine (ou l’inverse).

7. **Script 2 lignes** :
   - Écris plusieurs scripts Bash respectant les règles :
     - 2 lignes
     - `#!/bin/bash` en première ligne
     - utilisation de `$1` pour une IP / plage
     - pas de `&&`, `||`, `;`, backticks

---

Fin du cours.  
Tu peux maintenant l’utiliser comme support de révision ou base pour ton README.md de projet Linux Security Basics.
