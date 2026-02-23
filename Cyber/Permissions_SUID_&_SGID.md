# 🛡️ Linux Permissions, SUID & SGID — Cours Ultra Complet (avec TP & Sécurité)

> **Objectif** : maîtriser **les permissions Linux**, **SUID/SGID**, **Sticky bit**, **umask**, et savoir **auditer** + appliquer les **best practices** (dev + cyber).  
> **Environnement** : Kali Linux • shell `bash` • usage CLI.  
> **Niveau** : débutant → avancé (progressif, très pédagogique).  

---

## 📌 Sommaire

1. [Pré-requis & mental model](#pré-requis--mental-model)  
2. [Les 3 groupes de permissions](#1-les-3-groupes-de-permissions)  
3. [Lire un `ls -l` comme un pro](#2-lire-un-ls--l-comme-un-pro)  
4. [r w x : ce que ça veut dire en vrai](#3-r-w-x--ce-que-ça-veut-dire-en-vrai)  
5. [Permissions numériques (octal) + conversions](#4-permissions-numériques-octal--conversions)  
6. [Commandes indispensables](#5-commandes-indispensables)  
   - chmod • chown • chgrp • sudo • su • id • groups • useradd/adduser • groupadd/addgroup  
7. [Permissions spéciales : SUID / SGID / Sticky](#6-permissions-spéciales--suid--sgid--sticky)  
8. [Umask : permissions par défaut](#7-umask--permissions-par-défaut)  
9. [Audit & détection : trouver les risques](#8-audit--détection--trouver-les-risques)  
10. [Sécurité : erreurs classiques & bonnes pratiques](#9-sécurité--erreurs-classiques--bonnes-pratiques)  
11. [TP guidés (labs) : comprendre par la pratique](#10-tp-guidés-labs--comprendre-par-la-pratique)  
12. [Checklists (dev / sysadmin / pentest)](#11-checklists-dev--sysadmin--pentest)  
13. [Pièges & questions d’entretien](#12-pièges--questions-dentretien)  
14. [Annexe : mémo rapide](#13-annexe--mémo-rapide)  

---

## Pré-requis & mental model

### Pourquoi les permissions existent ?
Linux est **multi-utilisateur** : plusieurs personnes/process cohabitent.  
Les permissions répondent à 3 questions :
1. **Qui** veut accéder ? (owner / group / others)
2. **À quoi** ? (fichier / dossier)
3. **Quel type d’accès** ? (lecture / écriture / exécution)

### Le bon modèle mental
- Un **fichier** : tu lis, écris, exécutes.
- Un **dossier** : tu listes, traverses, modifies son contenu.
- Une permission n’est pas “morale” : c’est une **règle de contrôle d’accès**.

---

## 1) Les 3 groupes de permissions

Linux sépare les permissions en 3 blocs :

| Bloc | Symbole | Qui ? |
|------|---------|------|
| User | `u` | propriétaire (owner) |
| Group | `g` | groupe du fichier |
| Others | `o` | le reste du monde |
| All | `a` | u+g+o |

📌 Important : “others” = **tous ceux qui ne sont pas owner et pas dans le group**.

---

## 2) Lire un `ls -l` comme un pro

Commande :
```bash
ls -l
```

Exemple de sortie :
```text
-rwxr-xr-- 1 root users 1234 Feb 23 10:12 script.sh
drwxr-sr-x 2 alice devs 4096 Feb 23 10:20 shared/
lrwxrwxrwx 1 bob   bob    11 Feb 23 10:25 link -> target.txt
```

Découpage du premier champ (`-rwxr-xr--`) :

### 2.1 Type de fichier (1er caractère)
| Caractère | Type |
|----------:|------|
| `-` | fichier normal |
| `d` | dossier |
| `l` | lien symbolique |
| `c` | device caractère |
| `b` | device bloc |
| `p` | pipe (FIFO) |
| `s` | socket |

### 2.2 Les 9 bits de permissions (3 blocs de 3)
Format :
```text
[uuu][ggg][ooo]
 r w x r w x r w x
```

Exemple :
```text
-rwx r-x r--
```
- user : `rwx`
- group : `r-x`
- others : `r--`

---

## 3) r w x : ce que ça veut dire en vrai

### 3.1 Sur un fichier
| Permission | Lettre | Effet concret |
|-----------|--------|---------------|
| Read | `r` | lire le contenu (`cat`, `less`) |
| Write | `w` | modifier le contenu (éditer, truncater) |
| Execute | `x` | exécuter comme programme/script |

📌 **Exécuter** un script = avoir `x` **sur le fichier** + souvent `r` pour que l’interpréteur puisse le lire.

### 3.2 Sur un dossier (très important)
Sur un dossier, `r/w/x` ne signifient pas la même chose.

| Permission | Lettre | Effet concret |
|-----------|--------|---------------|
| Read | `r` | lister les noms (`ls`) |
| Write | `w` | créer/supprimer/renommer dans le dossier |
| Execute | `x` | **traverser** (`cd`, accéder aux fichiers) |

💥 Cas piégeux :
- Tu peux avoir `x` sans `r` : tu peux “entrer” si tu connais le nom exact d’un fichier, sans pouvoir lister.
- Tu peux avoir `w` sans `x` : souvent inutile, car tu ne peux pas accéder au contenu.

---

## 4) Permissions numériques (octal) + conversions

### 4.1 Valeurs de base
| Lettre | Valeur |
|--------|--------|
| `r` | 4 |
| `w` | 2 |
| `x` | 1 |

Un bloc = somme :
- `rwx` = 4+2+1 = **7**
- `rw-` = 4+2+0 = **6**
- `r-x` = 4+0+1 = **5**
- `r--` = 4+0+0 = **4**
- `---` = **0**

### 4.2 Exemples classiques
| Octal | Résultat | Usage typique |
|------:|----------|---------------|
| 644 | `rw-r--r--` | fichiers texte, config |
| 600 | `rw-------` | clés privées, secrets |
| 755 | `rwxr-xr-x` | scripts, binaires |
| 700 | `rwx------` | dossiers privés |
| 775 | `rwxrwxr-x` | partage groupe |

---

## 5) Commandes indispensables

### 5.1 `chmod` — changer les permissions
#### Mode octal
```bash
chmod 644 file.txt
chmod 755 script.sh
```

#### Mode symbolique
Ajouter `x` au user :
```bash
chmod u+x script.sh
```

Retirer l’écriture au groupe :
```bash
chmod g-w file.txt
```

Définir exactement :
```bash
chmod u=rwx,g=rx,o= file.txt
```

✅ Bon à savoir :
- `chmod -R` = récursif (attention en prod)
```bash
chmod -R 750 mydir/
```

---

### 5.2 `chown` — changer propriétaire (et groupe optionnel)
```bash
sudo chown alice file.txt
sudo chown alice:devs file.txt
```

Récursif :
```bash
sudo chown -R alice:devs project/
```

---

### 5.3 `chgrp` — changer uniquement le groupe
```bash
sudo chgrp devs file.txt
sudo chgrp -R devs shared/
```

📌 **Différence chown vs chgrp**
- `chown` : owner (et éventuellement group)
- `chgrp` : uniquement group

---

### 5.4 `sudo` — exécuter une commande avec privilèges
```bash
sudo apt update
sudo -l
```

📌 `sudo -l` : liste tes droits sudo (très utile en audit/pentest).

⚠️ Danger : autoriser des commandes “innocentes” peut permettre une élévation (ex: `sudo vim`, `sudo less`, `sudo tar`…).

---

### 5.5 `su` — changer d’utilisateur (login shell)
```bash
su
su alice
su - alice
```

- `su alice` : change user
- `su - alice` : charge l’environnement de login (PATH, variables…)

---

### 5.6 `id` / `groups` — identité et groupes
```bash
id
id alice
groups
groups alice
```

---

### 5.7 Gestion utilisateurs / groupes
Selon distro, `adduser` peut être plus “user-friendly” que `useradd`.

Créer un user :
```bash
sudo useradd -m -s /bin/bash alice
sudo passwd alice
```

Créer un groupe :
```bash
sudo groupadd devs
```

Ajouter user au groupe (Debian/Kali) :
```bash
sudo usermod -aG devs alice
```

Vérifier :
```bash
id alice
```

---

## 6) Permissions spéciales : SUID / SGID / Sticky

Ces bits modifient le comportement “normal” des permissions.

### 6.1 Où se voient-ils dans `ls -l` ?
Ils remplacent le `x` dans les blocs :

- **SUID** : dans le bloc **user** → `s` à la place du `x`
- **SGID** : dans le bloc **group** → `s` à la place du `x`
- **Sticky** : dans le bloc **others** (sur dossier) → `t` à la place du `x`

Exemples :
```text
-rwsr-xr-x   # SUID (user execute devient s)
-rwxr-sr-x   # SGID (group execute devient s)
drwxrwxrwt   # Sticky (others execute devient t) sur /tmp
```

### 6.2 SUID (setuid) — exécution avec UID du propriétaire
**Idée** : quand tu exécutes un binaire SUID, ton process prend l’**EUID** (effective UID) du propriétaire du fichier (souvent root).

Exemple réel :
```bash
ls -l /usr/bin/passwd
```

Pourquoi `passwd` a SUID root ?
Parce qu’il doit modifier des fichiers protégés comme `/etc/shadow`.

📌 Concepts clés :
- **RUID** (real UID) : qui tu es vraiment
- **EUID** (effective UID) : droits utilisés par le process
- SUID = EUID = owner du fichier pendant l’exécution

### 6.3 SGID (setgid)
#### Sur fichier
Le process prend l’**EGID** (effective GID) du groupe.

#### Sur dossier (très utilisé en partage d’équipe)
Les fichiers créés dans ce dossier **héritent du groupe** du dossier (au lieu du groupe principal du créateur).

Exemple de dossier partagé :
```text
drwxr-sr-x 2 alice devs 4096 shared/
```

Ici le `s` dans le bloc groupe montre SGID sur dossier.

### 6.4 Sticky bit (sur dossiers)
Le sticky bit empêche qu’un utilisateur supprime/renomme les fichiers d’un autre dans un dossier partagé.
Exemple : `/tmp`

```bash
ls -ld /tmp
```

Résultat typique :
```text
drwxrwxrwt 10 root root 4096 /tmp
```

---

### 6.5 Comment activer/désactiver SUID/SGID/Sticky ?

#### Octal (bit spécial + permissions)
Bits spéciaux :
- SUID = **4**
- SGID = **2**
- Sticky = **1**

On ajoute un **4e chiffre** devant :

| Valeur | Effet |
|------:|------|
| 4755 | SUID + 755 |
| 2755 | SGID + 755 |
| 1777 | Sticky + 777 |
| 6755 | SUID+SGID + 755 |

Exemples :
```bash
chmod 4755 mybin     # SUID
chmod 2755 mybin     # SGID
chmod 1777 mydir     # Sticky
```

#### Symbolique
```bash
chmod u+s mybin      # SUID
chmod u-s mybin      # retire SUID

chmod g+s mybin      # SGID
chmod g-s mybin

chmod +t mydir       # Sticky
chmod -t mydir
```

---

### 6.6 `s` vs `S` et `t` vs `T` (piège)
- `s` = bit set + execute présent
- `S` = bit set MAIS execute absent (souvent une erreur)
- `t` = sticky + execute présent
- `T` = sticky MAIS execute absent

Exemple :
```text
-rwSr--r--  # SUID set mais pas exécutable => incohérent
```

---

## 7) Umask — permissions par défaut

### 7.1 Pourquoi umask existe ?
Quand tu crées un fichier/dossier, Linux part d’un “max” puis retire des droits via umask.

- Fichiers : base = `666` (pas d’exec par défaut)
- Dossiers : base = `777`

Formules :
- fichier final = `666 - umask`
- dossier final = `777 - umask`

Voir umask :
```bash
umask
```

Exemple : umask `022`
- fichier : 666 - 022 = **644**
- dossier : 777 - 022 = **755**

Changer temporairement :
```bash
umask 027
```

Effets :
- fichiers : 666 - 027 = 640
- dossiers : 777 - 027 = 750

✅ Bon usage sécurité : 027 (team), 077 (privé/secret).

---

## 8) Audit & détection : trouver les risques

### 8.1 Trouver tous les binaires SUID
```bash
find / -perm -4000 -type f 2>/dev/null
```

### 8.2 Trouver tous les binaires SGID
```bash
find / -perm -2000 -type f 2>/dev/null
```

### 8.3 Trouver sticky sur dossiers
```bash
find / -perm -1000 -type d 2>/dev/null
```

### 8.4 Lister permissions “dangereuses”
#### Écriture pour everyone (world-writable)
```bash
find / -type f -perm -0002 2>/dev/null
```

#### Dossiers world-writable sans sticky (dangereux)
```bash
find / -type d -perm -0002 -a ! -perm -1000 2>/dev/null
```

---

### 8.5 Audit des changements : `auditd` (approche pro)
Installer (si nécessaire) :
```bash
sudo apt install auditd
```

Exemple : surveiller `/etc/passwd` et `/etc/shadow`
```bash
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
sudo auditctl -w /etc/shadow -p wa -k shadow_changes
```

Lire les logs :
```bash
sudo ausearch -k passwd_changes
sudo ausearch -k shadow_changes
```

📌 `-p wa` :
- `w` = write
- `a` = attribute changes (chmod/chown)

---

## 9) Sécurité : erreurs classiques & bonnes pratiques

### 9.1 Erreurs classiques (et pourquoi c’est grave)
- `chmod 777` sur un dossier sensible → tout le monde peut écrire/supprimer/placer un payload
- SUID sur un script (mauvaise idée) → comportements inattendus selon l’interpréteur
- SGID sur des exécutables non contrôlés → escalade de privilèges de groupe
- “World-writable” sans sticky → un user peut supprimer/renommer le fichier d’un autre
- `sudo` trop large (`ALL=(ALL) ALL`) sans justification → surface d’attaque énorme

### 9.2 Best practices (règles simples)
✅ Appliquer le **principe du moindre privilège**  
✅ Préférer 640/750/755 plutôt que 777  
✅ Utiliser des groupes pour le partage (SGID sur dossier)  
✅ Sticky sur dossiers partagés world-writable (`/tmp`, dépôts communs)  
✅ Auditer régulièrement les SUID/SGID  
✅ Utiliser umask raisonnable (027 / 077)  
✅ Éviter SUID custom : si besoin, privilégier capabilities (`setcap`) ou sudo ciblé (selon contexte)

---

## 10) TP guidés (labs) : comprendre par la pratique

> ⚠️ Tout se fait dans un dossier de test, jamais sur `/bin` ou `/usr/bin`.

### TP0 — Préparer l’environnement
```bash
mkdir -p ~/perm_lab
cd ~/perm_lab
```

Créer fichiers/dossiers :
```bash
touch a.txt b.txt
mkdir dir1 dir2
```

---

### TP1 — Comprendre r/w/x sur dossier
1) Retire `x` sur dir1 pour toi :
```bash
chmod u-x dir1
```
2) Essaie :
```bash
cd dir1
```
➡ tu devrais être bloqué : tu ne peux plus “traverser”.

Remets :
```bash
chmod u+x dir1
```

---

### TP2 — Partage via groupe + SGID sur dossier
1) Crée un groupe `devs` et ajoute-toi dedans :
```bash
sudo groupadd devs
sudo usermod -aG devs "$USER"
```

2) Crée un dossier partagé, groupe devs :
```bash
sudo mkdir -p /srv/shared_devs
sudo chgrp devs /srv/shared_devs
sudo chmod 2775 /srv/shared_devs
```

Explication :
- 2xxx = SGID sur dossier
- 775 = rwx rwx r-x
- les fichiers créés dedans héritent de `devs`

3) Vérifie :
```bash
ls -ld /srv/shared_devs
```
Tu dois voir un `s` dans le bloc groupe.

---

### TP3 — Sticky bit sur dossier partagé
1) Crée un dossier world-writable “comme /tmp” :
```bash
mkdir public
chmod 777 public
```

2) Ajoute sticky :
```bash
chmod 1777 public
ls -ld public
```
Tu dois voir `t`.

---

### TP4 — Identifier SUID/SGID sur le système (lecture)
Lister quelques SUID :
```bash
find / -perm -4000 -type f 2>/dev/null | head
```

Objectif :
- reconnaître rapidement ce qui est “normal” (passwd, su, sudo…)
- repérer ce qui est “suspect” (binaire custom, emplacement étrange…)

---

### TP5 — Comprendre RUID/EUID (conceptuel)
Tu peux observer avec :
```bash
id
```
Puis comprendre que :
- ton shell normal : EUID = ton user
- un binaire SUID root : EUID = root pendant l’exécution

(En lab pentest, c’est la base de l’escalade.)

---

## 11) Checklists (dev / sysadmin / pentest)

### Dev
- [ ] mes scripts ne demandent pas `777`
- [ ] les secrets sont `600` ou `640` max
- [ ] je sais quel user exécute mon service (systemd, docker, etc.)
- [ ] je n’ajoute pas de SUID “pour dépanner”

### Sysadmin
- [ ] umask correct (027/077)
- [ ] audit des SUID/SGID régulier
- [ ] sticky sur dossiers world-writable
- [ ] groupes bien utilisés (SGID sur dossiers d’équipe)
- [ ] sudo minimal, commandes justifiées

### Pentest / CTF
- [ ] je liste SUID/SGID
- [ ] je regarde sudo -l
- [ ] je check world-writable + PATH hijack
- [ ] je vérifie dossiers sans sticky
- [ ] je repère capabilities éventuelles (bonus)

---

## 12) Pièges & questions d’entretien

1) **“Quelles sont les 3 catégories de permissions ?”**  
   Owner / Group / Others

2) **“Différence entre chown et chgrp ?”**  
   chown change owner (et group optionnel), chgrp change seulement group.

3) **“Que fait le x sur un dossier ?”**  
   Permet la traversée (cd / accès aux fichiers).

4) **“SUID sert à quoi ?”**  
   Exécuter un binaire avec l’EUID du propriétaire (souvent root), pour des tâches légitimes contrôlées.

5) **“Pourquoi 777 est dangereux ?”**  
   Tout le monde peut écrire → injection de fichiers, suppression, contournement.

6) **“Qu’est-ce que umask ?”**  
   Masque de permissions par défaut : retire des droits lors de la création.

---

## 13) Annexe : mémo rapide

### Lire les permissions
- `-rwxr-xr--`
- 1er char : type
- 3 blocs : user / group / others

### Octal
- r=4 w=2 x=1
- 755 = rwx r-x r-x
- 644 = rw- r-- r--

### Bits spéciaux
- SUID 4000 → `s` sur user exec
- SGID 2000 → `s` sur group exec
- Sticky 1000 → `t` sur others exec (dossier)

### Find utiles
```bash
find / -perm -4000 -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null
find / -type d -perm -0002 -a ! -perm -1000 2>/dev/null
```

---

## ✅ Bonus “Holberton / scripts”

Rappels fréquents :
- Première ligne exacte :
```bash
#!/bin/bash
```
- fichier exécutable :
```bash
chmod +x script.sh
```
- fin de fichier : **nouvelle ligne** (POSIX, outils, linting, diff)

---

### ✅ Fin

Si tu veux, je peux te faire la **version “cyber offensif”** (plus avancée) avec :
- patterns d’escalade via SUID (sans donner de “pwn” direct)
- analyse de risques par catégorie (sudoers, PATH, env, cron)
- mini-quizz + corrigé
