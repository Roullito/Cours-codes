# TryHackMe – Linux Fundamentals (Cours complet)

Ce module correspond à l’esprit de **Linux Fundamentals (1/2/3)** de TryHackMe : te donner une base solide pour être à l’aise dans un shell Linux, comprendre le système, lancer/enquêter des commandes, et préparer la suite (pentest, forensic, dev, administration…).

Le cours est organisé en **3 parties**, comme les rooms TryHackMe :

1. **Partie 1 – Shell & système de fichiers**
2. **Partie 2 – Utilisateurs, permissions & processus**
3. **Partie 3 – Réseau, paquets & logs**

---

## Objectifs globaux

À la fin, tu dois être capable de :

- Expliquer ce qu’est une **distribution Linux**, un **shell** et la différence **root / user**.
- Naviguer dans le **système de fichiers** et manipuler dossiers/fichiers en ligne de commande.
- Utiliser les **redirections**, **pipes** et commandes de **filtrage** (grep, sort, etc.).
- Comprendre la gestion des **utilisateurs/groupes** et des **permissions** (chmod, chown).
- Gérer et inspecter des **processus** (ps, top, kill…).
- Utiliser quelques commandes **réseau** essentielles (ip, ping, curl, nc, ss/netstat).
- Installer et mettre à jour des **paquets** (apt) et consulter les **logs** principaux.

---

# Partie 1 – Shell & système de fichiers

## 1. Les briques de base : kernel, distribution, shell

- **Kernel (noyau)** : cœur du système. Gère la mémoire, les processus, le matériel.
- **Distribution** : ensemble *kernel + outils + configuration + gestionnaire de paquets*  
  Ex : Ubuntu, Debian, Kali, Arch, Fedora…  
  TryHackMe utilise souvent des systèmes basés sur **Debian/Ubuntu**.
- **Shell** : programme qui interprète les commandes que tu tapes.  
  Le plus courant : **bash** (Bourne Again SHell). Il s’exécute dans un **terminal**.

Quand tu vois un prompt du type :

```bash
user@machine:~$
```

- `user` : ton utilisateur
- `machine` : nom de la machine
- `~` : ton répertoire **home**
- `$` : utilisateur normal (`#` pour root)

---

## 2. Structure du système de fichiers

Linux utilise une structure **hiérarchique** commençant par `/` (la racine). Quelques répertoires importants :

- `/` : racine de tout.
- `/home` : dossiers des utilisateurs.
- `/root` : home de l’utilisateur root.
- `/etc` : fichiers de **configuration** système.
- `/var` : données variables (logs, caches, queues, etc.).
- `/tmp` : fichiers temporaires (souvent nettoyés au reboot).
- `/bin`, `/usr/bin` : binaires des commandes usuelles.
- `/sbin`, `/usr/sbin` : binaires système (admin).
- `/dev` : représentation des périphériques.
- `/proc`, `/sys` : pseudo-systèmes de fichiers liés au kernel & aux processus.

> **Important :** Sous Linux, tout est considéré comme un **fichier** (même les périphériques).

---

## 3. Navigation de base

### 3.1 Où suis-je ?

```bash
pwd        # print working directory – affiche ton dossier courant
```

### 3.2 Lister les fichiers

```bash
ls         # liste simple
ls -l      # liste détaillée (droits, propriétaire, taille, date)
ls -a      # inclut les fichiers cachés (commençant par .)
ls -lh     # taille lisible (K, M, G)
```

### 3.3 Changer de dossier

```bash
cd /etc            # aller dans /etc
cd ~               # revenir dans ton home
cd                 # équivalent : home
cd ..              # remonter d’un niveau
cd -               # revenir au dossier précédent
```

### 3.4 Astuces : complétion & jokers

- **Tab** : complète automatiquement les noms de fichiers/dossiers.
- Jokers (wildcards) :
  - `*` : n’importe quelle suite de caractères
  - `?` : un seul caractère

Exemples :

```bash
ls *.log          # tous les fichiers se terminant par .log
ls file?.txt      # file1.txt, file2.txt, etc.
```

---

## 4. Manipuler fichiers & dossiers

### 4.1 Créer fichiers/dossiers

```bash
touch fichier.txt        # crée (ou met à jour la date) d’un fichier
mkdir mon_dossier        # crée un dossier
mkdir -p a/b/c           # crée toute l’arborescence si besoin
```

### 4.2 Copier / déplacer / supprimer

```bash
cp source.txt dest.txt           # copie simple
cp source.txt /tmp/              # copie dans /tmp
cp -r doss1 doss2                # copie récursive de dossiers

mv fichier.txt /tmp/             # déplace
mv ancien_nom.txt nouveau.txt    # renomme

rm fichier.txt                   # supprime un fichier
rm -r dossier                    # supprime un dossier et son contenu
rm -rf dossier                   # supprime sans confirmation (dangereux)
```

> ⚠️ Attention avec `rm -rf` surtout en root. Une erreur de chemin peut *tout* détruire.

### 4.3 Lire le contenu d’un fichier

```bash
cat fichier.txt        # affiche tout le contenu
less fichier.txt       # affichage page par page (q pour quitter)
head fichier.txt       # les 10 premières lignes
head -n 20 fichier.txt # les 20 premières lignes
tail fichier.txt       # les 10 dernières lignes
tail -f fichier.log    # suit en temps réel la fin (log)
```

### 4.4 Éditer un fichier

Éditeurs courants :

- `nano` : simple, pratique pour débuter.
- `vim` ou `vi` : très puissant, mais plus dur au début.

Exemple avec nano :

```bash
nano fichier.txt
```

---

## 5. Redirections, pipes & filtres

### 5.1 Redirections

- `>` : redirige la sortie **et écrase** le fichier.
- `>>` : redirige la sortie **et ajoute** à la fin.

```bash
echo "Hello" > test.txt     # écrit "Hello" dans test.txt (écrase)
echo "World" >> test.txt    # ajoute "World" à la fin
```

Rediriger les erreurs :

- `2>` : redirige la **sortie d’erreur** (stderr)
- `&>` : stdout + stderr (bash récent)

```bash
commande > out.txt 2> err.txt
```

### 5.2 Pipes `|`

Le pipe envoie la sortie d’une commande **comme entrée** d’une autre :

```bash
commande1 | commande2
```

Exemples :

```bash
ls -l | less
dmesg | grep error
ps aux | grep apache
```

### 5.3 Filtres utiles

- `grep` : chercher dans du texte.
- `sort` : trier.
- `uniq` : supprimer les doublons consécutifs.
- `wc` : compter lignes/mots/caractères.
- `cut` : découper par colonne.

Exemples :

```bash
grep "error" /var/log/syslog
grep -i "ssh" /var/log/auth.log       # -i : ignore la casse

cat liste.txt | sort | uniq           # tri + suppression des doublons

wc -l fichier.txt                     # nombre de lignes
```

---

# Partie 2 – Utilisateurs, permissions & processus

## 6. Utilisateurs & groupes

### 6.1 Qui suis-je ?

```bash
whoami      # nom de l’utilisateur courant
id          # affiche UID, GID et groupes
```

Dans Linux :

- Chaque **utilisateur** a un **UID** (User ID).
- Chaque **groupe** a un **GID** (Group ID).
- Informations dans :
  - `/etc/passwd` : liste des utilisateurs.
  - `/etc/group` : liste des groupes.
  - `/etc/shadow` : hash des mots de passe (protégé).

### 6.2 Root vs user

- **root** : super-utilisateur, peut tout faire (et tout casser).
- Utilisateurs normaux : droits limités.

Tu peux reconnaître root à :

- son prompt : souvent `#` au lieu de `$`,
- son home : `/root`,
- son UID : 0.

### 6.3 Sudo & su

- `sudo commande` : exécute la commande avec les droits de root (si autorisé).
- `su` : switch user (souvent vers root).

Exemples :

```bash
sudo apt update
sudo nano /etc/hosts

su -                # passer root (si tu as le mot de passe)
su - autreuser      # switch vers un autre utilisateur
```

Les droits sudo sont configurés dans `/etc/sudoers` (à modifier via `visudo`).

---

## 7. Permissions de fichiers

### 7.1 Lire les permissions

```bash
ls -l
```

Exemple :

```text
-rw-r--r-- 1 user user  1234 Jan  1 12:00 fichier.txt
drwxr-x--- 2 user www   4096 Jan  1 12:00 site
```

Décryptage de la première colonne :

- 1er caractère :
  - `-` : fichier
  - `d` : dossier
  - `l` : lien symbolique
- Puis 3 blocs de 3 caractères :
  - **user** (propriétaire)
  - **group** (groupe)
  - **other** (les autres)

Chaque bloc : `r` (read), `w` (write), `x` (execute).

Ex : `-rw-r--r--` :

- user : `rw-` → lecture+écriture
- group : `r--` → lecture seule
- other : `r--` → lecture seule

### 7.2 Changer les permissions : `chmod`

Deux notations : symbolique et numérique.

#### Notation numérique

- r = 4, w = 2, x = 1.
- Tu additionnes pour chaque bloc.

Exemples :

- `chmod 644 fichier`  → `rw-r--r--`
- `chmod 755 script.sh` → `rwxr-xr-x`

```bash
chmod 600 id_rsa       # seulement l’utilisateur peut lire/écrire (clé privée)
chmod 700 mon_script   # seul l’utilisateur peut tout faire
```

#### Notation symbolique

```bash
chmod u+x script.sh       # ajoute x au user
chmod g-w fichier.txt     # retire w au groupe
chmod o-r fichier.txt     # retire r aux autres
```

### 7.3 Changer propriétaire/groupe : `chown`, `chgrp`

```bash
chown user fichier.txt          # change le propriétaire
chown user:group fichier.txt    # change propriétaire + groupe
chgrp groupe fichier.txt        # change le groupe
```

> Nécessite généralement les droits root (`sudo`).

---

## 8. Processus & jobs

### 8.1 Voir les processus

```bash
ps           # simple
ps aux       # tous les processus (format étendu)
ps aux | grep ssh
```

### 8.2 top / htop

```bash
top          # vue dynamique des processus (CPU, RAM)
```

Touches utiles dans `top` :

- `q` : quitter
- `k` : tuer un processus (entre PID)
- `h` : aide

(`htop` est une version plus agréable, si installée.)

### 8.3 Tuer un processus : `kill`

```bash
kill PID             # demande au process de se terminer proprement
kill -9 PID          # SIGKILL : tue brutalement (à utiliser en dernier recours)
```

Tu récupères le PID via `ps` ou `top`.

### 8.4 Lancer en arrière-plan & jobs

```bash
commande &          # lance en arrière-plan
jobs                # liste les jobs du shell courant
fg %1               # ramène le job 1 au premier plan
bg %1               # renvoie le job 1 en arrière-plan
```

Exemple :

```bash
sleep 100 &
jobs
```

---

# Partie 3 – Réseau, paquets & logs

## 9. Commandes réseau de base

### 9.1 Interfaces & IP

Sur les distros récentes, on utilise `ip` :

```bash
ip a           # liste les interfaces et leurs adresses IP
ip r           # table de routage
```

(Anciennement `ifconfig`, `route`.)

### 9.2 Tester la connectivité

```bash
ping 8.8.8.8         # teste la connectivité vers une IP
ping google.com      # teste la résolution DNS + connectivité
```

Ctrl+C pour arrêter.

### 9.3 Résolution DNS

```bash
host google.com
dig google.com       # plus détaillé (si installé)
```

### 9.4 Télécharger des données

```bash
curl http://example.com
curl -I http://example.com       # seulement les headers

wget http://example.com/fichier
```

### 9.5 Ports & connexions

Anciennement :

```bash
netstat -tulnp
```

Plus moderne :

```bash
ss -tulnp
```

- `-t` : TCP
- `-u` : UDP
- `-l` : listening (en écoute)
- `-n` : ne résout pas les noms
- `-p` : montre les processus

### 9.6 netcat (nc)

Outil très utile en sécu pour tester des connexions, créer des listeners, etc.

```bash
nc -lvp 4444           # écoute sur le port 4444 (verbose)
nc target 4444         # se connecte au port 4444 sur target
```

> netcat est un couteau suisse réseau ; très utilisé dans les CTF & labs.

---

## 10. Gestion des paquets (Debian/Ubuntu)

Sur TryHackMe, tu verras souvent **apt**.

### 10.1 Mettre à jour

```bash
sudo apt update           # met à jour la liste des paquets
sudo apt upgrade          # met à jour les paquets installés
```

### 10.2 Installer / supprimer

```bash
sudo apt install nmap     # installe nmap
sudo apt remove nmap      # supprime le paquet (mais pas toujours les confs)
sudo apt purge nmap       # supprime paquet + confs
```

### 10.3 Rechercher des paquets

```bash
apt search nmap
apt show nmap
```

---

## 11. Logs & observation du système

Les logs sont souvent dans `/var/log/`. Exemples :

- `/var/log/syslog` : journal système principal (Ubuntu/Debian).
- `/var/log/auth.log` : connexions, sudo, ssh.
- `/var/log/kern.log` : messages du kernel.

### 11.1 Lire les logs

```bash
sudo less /var/log/syslog
sudo tail -f /var/log/auth.log
```

### 11.2 journalctl (systemd)

Sur les systèmes avec **systemd** :

```bash
journalctl -xe              # affiche les derniers événements avec détails
journalctl -u ssh           # logs du service ssh
```

### 11.3 Services : systemctl

```bash
systemctl status ssh         # état du service ssh
sudo systemctl restart ssh   # redémarrer ssh
sudo systemctl stop ssh      # arrêter ssh
```

---

## 12. Fiches mémo

### 12.1 Navigation & fichiers

```bash
pwd                     # où suis-je
ls, ls -la              # lister les fichiers
cd, cd .., cd ~         # changer de dossier
mkdir, rmdir            # créer/supprimer dossiers
cp, mv, rm              # copier/déplacer/supprimer
cat, less, head, tail   # lire des fichiers
nano, vim               # éditer des fichiers
```

### 12.2 Recherche & filtres

```bash
grep "mot" fichier
grep -R "mot" /etc
find / -name "nom"
which commande
```

### 12.3 Utilisateurs & droits

```bash
whoami
id
sudo commande
ls -l
chmod 755 script.sh
chown user:group fichier
```

### 12.4 Processus

```bash
ps aux | grep nom
top
kill PID
commande &
jobs
fg %1
```

### 12.5 Réseau

```bash
ip a
ping 8.8.8.8
curl http://site
ss -tulnp
nc -lvp 4444
```

### 12.6 Paquets & logs

```bash
sudo apt update && sudo apt upgrade
sudo apt install outil

sudo less /var/log/syslog
sudo tail -f /var/log/auth.log
```

---

## 13. Mini-exos d’entraînement (facultatifs)

1. Naviguer :
   - Aller dans `/etc` puis revenir dans ton home.
   - Lister tous les fichiers, y compris cachés, dans ton home.

2. Fichiers :
   - Créer un dossier `test_linux` dans ton home.
   - Créer un fichier `notes.txt` dedans avec `touch` puis l’ouvrir avec `nano`.
   - Ajouter quelques lignes et les relire avec `cat` puis `less`.

3. Permissions :
   - Créer un script `hello.sh` qui affiche “Hello Linux”.
   - Donner les droits d’exécution **seulement** à ton utilisateur (`chmod 700 hello.sh`).
   - Vérifier avec `ls -l` puis l’exécuter.

4. Processus :
   - Lancer `sleep 300 &`.
   - Le retrouver avec `ps` ou `top`.
   - Le terminer avec `kill`.

5. Réseau :
   - Afficher tes interfaces avec `ip a`.
   - Tester un ping vers `8.8.8.8`.
   - Utiliser `curl` ou `wget` pour récupérer la page d’accueil de `example.com`.

6. Logs :
   - Suivre en temps réel `/var/log/auth.log` tout en ouvrant une nouvelle session SSH (si dispo).
   - Repérer les lignes correspondant à ta connexion.

---

Tu as maintenant un **socle Linux** suffisant pour attaquer la plupart des labs TryHackMe, lire la doc, suivre des tutos et commencer à être autonome en ligne de commande.  
La suite logique : pratiquer encore et encore, et pousser certains sujets (bash scripting, cron, SSH avancé, forensic, etc.) au fur et à mesure des rooms.
