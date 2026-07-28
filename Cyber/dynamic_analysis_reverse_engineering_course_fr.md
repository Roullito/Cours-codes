# Dynamic Analysis in Reverse Engineering

## Cours complet, pratique et orienté projet Holberton

> Niveau : débutant à intermédiaire  
> Environnement principal : Kali Linux dans une machine virtuelle  
> Objectif : comprendre ce qu’un binaire fait réellement pendant son exécution, résoudre les tâches du projet `dynamic_analysis`, et acquérir une méthode applicable en laboratoire, en CTF, en audit autorisé et en analyse de malware.

---

# Table des matières

1. [Pourquoi l’analyse dynamique est importante](#1-pourquoi-lanalyse-dynamique-est-importante)
2. [Analyse statique et analyse dynamique](#2-analyse-statique-et-analyse-dynamique)
3. [Construire un laboratoire sûr](#3-construire-un-laboratoire-sûr)
4. [Méthodologie professionnelle](#4-méthodologie-professionnelle)
5. [Rappels indispensables sur les binaires](#5-rappels-indispensables-sur-les-binaires)
6. [GDB de zéro à autonome](#6-gdb-de-zéro-à-autonome)
7. [Breakpoints, watchpoints et contrôle du flot](#7-breakpoints-watchpoints-et-contrôle-du-flot)
8. [Registres, pile et mémoire](#8-registres-pile-et-mémoire)
9. [Tracer les interactions avec le système](#9-tracer-les-interactions-avec-le-système)
10. [Dump mémoire et récupération de données déchiffrées](#10-dump-mémoire-et-récupération-de-données-déchiffrées)
11. [Analyse dynamique sous Windows](#11-analyse-dynamique-sous-windows)
12. [Wireshark, ProcMon et Sysinternals](#12-wireshark-procmon-et-sysinternals)
13. [Valgrind, Intel Pin et sandbox](#13-valgrind-intel-pin-et-sandbox)
14. [SAT, SMT et Z3](#14-sat-smt-et-z3)
15. [Traduire un binaire en contraintes](#15-traduire-un-binaire-en-contraintes)
16. [Résoudre avec Z3](#16-résoudre-avec-z3)
17. [Résoudre avec Angr](#17-résoudre-avec-angr)
18. [Task 0 — SAT Solving in Reverse Engineering](#18-task-0--sat-solving-in-reverse-engineering)
19. [Task 1 — Exploring Anti-Debugging Techniques](#19-task-1--exploring-anti-debugging-techniques)
20. [Task 2 — SAT Solving, approche hybride](#20-task-2--sat-solving-approche-hybride)
21. [Task 3 — Self-modifying code](#21-task-3--self-modifying-code)
22. [Task 4 — Solve the 100 Binaries](#22-task-4--solve-the-100-binaries)
23. [Automatiser une analyse sans perdre la compréhension](#23-automatiser-une-analyse-sans-perdre-la-compréhension)
24. [Documenter ses résultats](#24-documenter-ses-résultats)
25. [Erreurs fréquentes et dépannage](#25-erreurs-fréquentes-et-dépannage)
26. [Cheat sheets](#26-cheat-sheets)
27. [Questions de révision](#27-questions-de-révision)
28. [Glossaire](#28-glossaire)

---

# 1. Pourquoi l’analyse dynamique est importante

L’analyse statique consiste à examiner un programme sans l’exécuter. Elle permet de lire les chaînes de caractères, les symboles, les sections ELF, le code assembleur et parfois une représentation décompilée.

L’analyse dynamique consiste à exécuter le programme dans un environnement contrôlé et à observer son comportement réel.

Cette différence est essentielle. Un programme peut :

- déchiffrer son vrai code uniquement en mémoire ;
- générer une clé pendant l’exécution ;
- modifier ses propres instructions ;
- charger une bibliothèque seulement sous certaines conditions ;
- se connecter à un serveur uniquement après plusieurs minutes ;
- détecter la présence d’un debugger et changer de comportement ;
- cacher sa logique dans des centaines de branches difficiles à lire statiquement ;
- provoquer une vulnérabilité uniquement avec une entrée ou un état précis.

L’analyse dynamique répond donc à la question :

> Que fait réellement ce programme lorsqu’il s’exécute ?

Elle est utilisée pour :

- comprendre des crackmes et des challenges de reverse engineering ;
- analyser des logiciels malveillants dans un laboratoire isolé ;
- trouver la cause d’un crash ;
- identifier un buffer overflow ou un use-after-free ;
- observer les fichiers, processus et clés de registre créés ;
- capturer une configuration ou un flag déchiffré en mémoire ;
- contourner une vérification anti-debugging dans un environnement autorisé ;
- reconstruire un algorithme complexe à partir de son comportement.

---

# 2. Analyse statique et analyse dynamique

## 2.1 Analyse statique

Le programme n’est pas exécuté.

Outils typiques :

- `file`
- `strings`
- `readelf`
- `objdump`
- `nm`
- Ghidra
- Binary Ninja
- IDA Pro

Avantages :

- faible risque ;
- vue globale du code ;
- possibilité d’étudier toutes les branches ;
- utile pour identifier les fonctions intéressantes avant le debugging.

Limites :

- code obfusqué ou packé difficile à lire ;
- valeurs générées à l’exécution absentes ;
- comportement dépendant du système non visible ;
- chemins réellement exécutés inconnus ;
- code auto-modifiant potentiellement incompréhensible avant exécution.

## 2.2 Analyse dynamique

Le programme est exécuté et observé.

Outils typiques :

- GDB
- x64dbg
- OllyDbg
- ProcMon
- Process Explorer
- Wireshark
- Valgrind
- Intel Pin
- Cuckoo Sandbox
- Z3 et Angr pour raisonner sur les chemins d’exécution

Avantages :

- observation du vrai comportement ;
- accès aux valeurs des registres et de la mémoire ;
- possibilité d’arrêter le programme à une instruction précise ;
- récupération de données déchiffrées ;
- identification des appels système et API réellement utilisés.

Limites :

- seuls les chemins exécutés sont observés ;
- un malware peut détecter l’environnement ;
- certaines conditions sont difficiles à reproduire ;
- le programme peut endommager le système si le laboratoire est mal isolé ;
- l’analyse peut devenir lente si elle est faite instruction par instruction.

## 2.3 Les deux approches sont complémentaires

Une bonne analyse suit généralement ce cycle :

1. Inspection statique rapide.
2. Formulation d’hypothèses.
3. Exécution sous debugger.
4. Observation et collecte de preuves.
5. Retour à l’analyse statique avec les nouvelles informations.
6. Automatisation de la partie répétitive.

Ne choisis pas entre statique et dynamique : utilise l’une pour guider l’autre.

---

# 3. Construire un laboratoire sûr

## 3.1 Machine virtuelle

Travaille dans une VM dédiée, avec un snapshot propre.

Configuration recommandée :

- Kali Linux à jour ;
- 2 à 4 processeurs virtuels ;
- 4 à 8 Go de RAM ;
- dossier partagé désactivé pour un échantillon non fiable ;
- presse-papiers partagé désactivé si tu analyses un malware ;
- snapshot avant chaque nouvelle analyse ;
- réseau désactivé ou isolé selon le besoin.

## 3.2 Réseau

Trois modes utiles :

### Réseau désactivé

À utiliser si le programme n’a pas besoin de communiquer.

### Host-only

La VM peut communiquer avec une autre VM de laboratoire, sans accès direct à Internet.

### Réseau simulé

Une autre machine du laboratoire simule DNS, HTTP ou d’autres services. Cela permet d’observer les tentatives de connexion sans exposer Internet.

## 3.3 Ne jamais analyser un binaire inconnu comme root

Crée un utilisateur dédié et travaille avec des permissions limitées.

```bash
sudo useradd -m -s /bin/bash analyst
sudo passwd analyst
```

Les challenges Holberton sont normalement prévus pour un laboratoire, mais cette habitude reste importante.

## 3.4 Préserver l’original

Calcule les empreintes avant toute modification :

```bash
sha256sum ./target
md5sum ./target
file ./target
```

Enregistre le résultat :

```bash
sha256sum ./target > target.sha256
```

Validation ultérieure :

```bash
sha256sum -c target.sha256
```

Travaille sur une copie :

```bash
cp -- ./target ./target_working
chmod u+x ./target_working
```

## 3.5 Répertoire d’analyse recommandé

```text
analysis/
├── original/
├── working/
├── dumps/
├── scripts/
├── captures/
├── notes/
└── reports/
```

Création sans chemins absolus :

```bash
mkdir -p analysis/{original,working,dumps,scripts,captures,notes,reports}
```

---

# 4. Méthodologie professionnelle

Une analyse efficace n’est pas une suite de commandes lancées au hasard. Elle suit une méthode reproductible.

## Phase 1 — Préparation

- Vérifier le hash du binaire.
- Vérifier son type et son architecture.
- Identifier ses permissions.
- Préparer le snapshot de la VM.
- Choisir le mode réseau.
- Démarrer les outils de monitoring avant le programme.

## Phase 2 — Triage statique rapide

```bash
file ./target
readelf -h ./target
readelf -S ./target
readelf -l ./target
strings -a -n 4 ./target | less
nm -n ./target 2>/dev/null | less
objdump -d -M intel ./target | less
```

Questions à se poser :

- ELF 32 ou 64 bits ?
- PIE ou non-PIE ?
- Stripped ou non stripped ?
- Lié dynamiquement ou statiquement ?
- Quelles fonctions semblent intéressantes ?
- Le binaire appelle-t-il `strcmp`, `memcmp`, `ptrace`, `mprotect`, `mmap`, `read`, `fgets` ou `scanf` ?
- Existe-t-il des chaînes `Correct`, `Wrong`, `flag`, `debugger`, `TracerPid` ?

## Phase 3 — Exécution de référence

Exécute une fois le binaire sans debugger, avec une entrée contrôlée.

```bash
./target
```

Enregistre :

- sortie standard ;
- sortie d’erreur ;
- code de retour ;
- fichiers créés ;
- durée ;
- consommation CPU ;
- comportement réseau.

Code de retour :

```bash
./target
printf 'exit code: %d\n' "$?"
```

## Phase 4 — Debugging ciblé

Pose des breakpoints sur les fonctions ou événements intéressants :

- fonction principale ;
- lecture de l’entrée ;
- comparaison ;
- branche de succès ;
- appel à `ptrace` ;
- appel à `mprotect` ;
- zone mémoire modifiée.

## Phase 5 — Observation du système

Observe :

- accès aux fichiers ;
- créations de processus ;
- appels API ;
- allocations mémoire ;
- connexions réseau ;
- changements de permissions mémoire.

## Phase 6 — Extraction et automatisation

Lorsque la logique est comprise :

- écris un solveur ;
- crée un script reproductible ;
- ajoute des vérifications d’erreur ;
- évite les chemins absolus ;
- journalise les résultats.

## Phase 7 — Documentation

Distingue toujours :

- ce que tu as observé ;
- ce que tu en déduis ;
- ce que tu n’as pas encore confirmé.

---

# 5. Rappels indispensables sur les binaires

## 5.1 ELF

Sous Linux, le format le plus courant est ELF.

Sections importantes :

- `.text` : instructions exécutables ;
- `.rodata` : données en lecture seule, chaînes et constantes ;
- `.data` : variables initialisées ;
- `.bss` : variables non initialisées ;
- `.plt` et `.got` : résolution des fonctions externes ;
- `.symtab` et `.dynsym` : symboles.

## 5.2 PIE et ASLR

Un exécutable PIE peut être chargé à une adresse différente à chaque exécution. L’ASLR randomise les adresses mémoire.

Vérification :

```bash
readelf -h ./target | grep 'Type:'
```

- `EXEC` : généralement non-PIE ;
- `DYN` pour un exécutable : souvent PIE.

Dans GDB, désactiver temporairement la randomisation pour obtenir des adresses reproductibles :

```gdb
set disable-randomization on
```

Cela ne modifie pas le système entier et reste limité au processus debuggué.

## 5.3 Convention d’appel x86-64 Linux

Arguments d’une fonction, dans l’ordre :

1. `RDI`
2. `RSI`
3. `RDX`
4. `RCX`
5. `R8`
6. `R9`

Valeur de retour :

- `RAX`

Pointeurs importants :

- `RIP` : instruction courante ;
- `RSP` : sommet de la pile ;
- `RBP` : base de la frame courante, lorsqu’elle est utilisée.

Cette convention permet de comprendre rapidement les arguments avant un appel à `strcmp`, `read`, `memcmp`, `mprotect`, etc.

## 5.4 Instructions fréquemment rencontrées

```asm
mov     destination, source
lea     destination, [adresse]
cmp     opérande1, opérande2
test    opérande1, opérande2
je      destination
jne     destination
jg      destination
jl      destination
call    fonction
ret
push    valeur
pop     registre
xor     destination, source
and     destination, source
or      destination, source
shl     destination, quantité
shr     destination, quantité
```

`cmp` et `test` modifient les flags. Une instruction de saut conditionnel utilise ensuite ces flags.

Exemple :

```asm
cmp eax, 0
jne failure
```

Cela signifie : si `EAX` n’est pas égal à zéro, aller vers `failure`.

---

# 6. GDB de zéro à autonome

## 6.1 Installation

```bash
sudo apt update
sudo apt install -y gdb gdb-multiarch
```

Optionnel mais très utile : une extension visuelle comme GEF ou pwndbg. Le projet doit cependant rester compréhensible avec GDB seul.

## 6.2 Ouvrir un binaire

```bash
gdb -q ./target
```

`-q` masque le message d’accueil.

## 6.3 Commandes fondamentales

```gdb
help
file ./target
info files
info functions
info variables
info address main
```

## 6.4 Affichage Intel

```gdb
set disassembly-flavor intel
```

Le format Intel est souvent plus lisible pour les débutants : destination à gauche, source à droite.

Pour rendre ce réglage permanent :

```bash
printf 'set disassembly-flavor intel\n' >> ~/.gdbinit
```

## 6.5 Démarrer le programme

```gdb
run
```

Avec arguments :

```gdb
run argument1 argument2
```

Avec redirection d’entrée :

```gdb
run < input.txt
```

Relancer depuis le début :

```gdb
run
```

GDB demande parfois confirmation pour tuer l’ancienne exécution.

## 6.6 Arrêter au début de `main`

```gdb
start
```

`start` place automatiquement un breakpoint temporaire à `main`.

Pour commencer à la première instruction du programme :

```gdb
starti
```

## 6.7 Voir le code

```gdb
disassemble main
disassemble /r main
x/20i $rip
```

- `disassemble` : instructions de la fonction ;
- `/r` : affiche aussi les octets machine ;
- `x/20i $rip` : affiche 20 instructions à partir de `RIP`.

## 6.8 Avancer dans le programme

```gdb
stepi
nexti
step
next
continue
finish
```

Différences :

- `stepi` ou `si` : une instruction assembleur, entre dans les appels ;
- `nexti` ou `ni` : une instruction, mais exécute un `call` sans entrer dedans ;
- `step` : une ligne source, si symboles disponibles ;
- `next` : ligne source sans entrer dans les fonctions ;
- `continue` ou `c` : continue jusqu’au prochain arrêt ;
- `finish` : exécute jusqu’au retour de la fonction actuelle.

## 6.9 Voir les registres

```gdb
info registers
info registers rax rbx rcx rdx rsi rdi rip rsp rbp
p/x $rax
p/d $rax
p/c $al
```

Formats :

- `/x` : hexadécimal ;
- `/d` : décimal signé ;
- `/u` : décimal non signé ;
- `/c` : caractère ;
- `/t` : binaire.

## 6.10 TUI

GDB peut afficher une interface texte :

```gdb
layout asm
layout regs
focus cmd
refresh
```

Quitter le mode TUI :

```text
Ctrl-x puis a
```

---

# 7. Breakpoints, watchpoints et contrôle du flot

## 7.1 Breakpoint sur une fonction

```gdb
break main
break strcmp
break memcmp
break ptrace
break mprotect
```

Abréviation :

```gdb
b main
```

## 7.2 Breakpoint sur une adresse

```gdb
break *0x401234
```

Pour un PIE, préfère une fonction ou calcule l’adresse après chargement.

## 7.3 Breakpoint relatif à une fonction

```gdb
break *main+42
```

Cette technique est pratique lorsque le binaire contient les symboles.

## 7.4 Breakpoint temporaire

```gdb
tbreak main
```

Il est supprimé après le premier arrêt.

## 7.5 Lister et gérer les breakpoints

```gdb
info breakpoints
disable 1
enable 1
delete 1
clear main
```

## 7.6 Breakpoint conditionnel

```gdb
break *main+120 if $rax == 0
```

Ou sur une variable :

```gdb
break check_flag if index == 12
```

Cela évite de s’arrêter des milliers de fois dans une boucle.

## 7.7 Ignorer les premiers passages

```gdb
ignore 1 99
```

Le breakpoint 1 sera ignoré 99 fois, puis GDB s’arrêtera au passage suivant.

## 7.8 Watchpoint

Un watchpoint arrête le programme lorsqu’une valeur mémoire est modifiée.

```gdb
watch variable
watch *(int *)0x404040
```

Arrêter lors d’une lecture :

```gdb
rwatch *(char *)0x404040
```

Arrêter lors d’une lecture ou écriture :

```gdb
awatch *(char *)0x404040
```

Les watchpoints matériels sont limités en nombre, souvent quatre sur x86.

## 7.9 Commandes automatiques sur breakpoint

```gdb
commands 1
silent
printf "RAX=%#lx RDI=%#lx RSI=%#lx\n", $rax, $rdi, $rsi
x/s $rdi
x/s $rsi
continue
end
```

Cette fonctionnalité transforme GDB en outil de traçage ciblé.

## 7.10 Modifier l’exécution

Modifier un registre :

```gdb
set $rax = 0
set $rip = 0x401234
```

Modifier une variable mémoire :

```gdb
set {int}0x404040 = 1
set {unsigned char}0x404050 = 0x90
```

Sauter une fonction :

1. Arrêter avant le `call`.
2. Avancer `RIP` après le `call`.
3. Définir la valeur de retour attendue.

Exemple conceptuel :

```gdb
set $rax = 0
set $rip = 0x401250
```

Ne fais pas cela au hasard. Note toujours l’adresse sautée et la raison.

---

# 8. Registres, pile et mémoire

## 8.1 Examiner la mémoire

Syntaxe générale :

```text
x/NFU adresse
```

- `N` : nombre d’éléments ;
- `F` : format ;
- `U` : taille d’un élément.

Formats fréquents :

- `x` : hexadécimal ;
- `d` : décimal ;
- `u` : non signé ;
- `c` : caractère ;
- `s` : chaîne ;
- `i` : instruction.

Tailles :

- `b` : byte, 1 octet ;
- `h` : halfword, 2 octets ;
- `w` : word, 4 octets ;
- `g` : giant word, 8 octets.

Exemples :

```gdb
x/16xb $rsp
x/8gx $rsp
x/s $rdi
x/20c $rsi
x/10i $rip
```

## 8.2 La pile

La pile contient notamment :

- adresses de retour ;
- anciennes valeurs de registres ;
- variables locales ;
- arguments supplémentaires ;
- données temporaires.

Voir la pile :

```gdb
x/32gx $rsp
```

Afficher la backtrace :

```gdb
backtrace
bt full
```

Changer de frame :

```gdb
frame 1
info frame
info args
info locals
```

## 8.3 Comprendre un buffer local

Code assembleur :

```asm
lea rax, [rbp-0x40]
mov rdi, rax
call fgets
```

Interprétation :

- le buffer commence à `RBP - 0x40` ;
- son adresse est passée dans `RDI` ;
- `fgets` y écrit l’entrée.

Dans GDB :

```gdb
x/64bx $rbp-0x40
x/s $rbp-0x40
```

## 8.4 Observer les arguments d’une comparaison

Breakpoint :

```gdb
break strcmp
run
```

Au moment de l’arrêt :

```gdb
x/s $rdi
x/s $rsi
```

Tu vois les deux chaînes comparées.

Pour `memcmp` :

```gdb
x/32bx $rdi
x/32bx $rsi
p/u $rdx
```

`RDX` contient généralement la longueur.

## 8.5 Valeur de retour

Après une fonction :

```gdb
finish
p/x $rax
```

Exemple : `strcmp` renvoie généralement zéro lorsque les chaînes sont égales.

---

# 9. Tracer les interactions avec le système

Un programme n’agit pas seul. Il demande au noyau ou aux bibliothèques de :

- ouvrir des fichiers ;
- lire ou écrire ;
- allouer de la mémoire ;
- créer des processus ;
- changer les permissions d’une page mémoire ;
- ouvrir un socket ;
- se connecter à une adresse ;
- récupérer l’heure ;
- installer un gestionnaire de signal.

## 9.1 Catchpoints GDB

Arrêter sur un appel système précis :

```gdb
catch syscall openat
catch syscall read
catch syscall write
catch syscall mmap
catch syscall mprotect
catch syscall connect
catch syscall execve
```

Lister :

```gdb
info breakpoints
```

À l’entrée d’un syscall x86-64 Linux :

- numéro du syscall : `RAX` ;
- arguments : `RDI`, `RSI`, `RDX`, `R10`, `R8`, `R9`.

Au retour :

- résultat : `RAX`.

## 9.2 Exemple : observer un fichier ouvert

```gdb
catch syscall openat
run
```

À l’entrée du syscall :

```gdb
x/s $rsi
```

Pour `openat`, le chemin est normalement dans `RSI`.

## 9.3 Exemple : observer une connexion réseau

```gdb
catch syscall connect
run
```

Arguments de `connect` :

```c
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

Sous x86-64 :

- `RDI` : descripteur du socket ;
- `RSI` : pointeur vers `sockaddr` ;
- `RDX` : taille.

Afficher les octets :

```gdb
x/16bx $rsi
```

Pour IPv4, la structure contient généralement :

- famille d’adresse ;
- port en ordre réseau ;
- adresse IP.

Wireshark reste plus confortable pour interpréter le trafic complet.

## 9.4 Appels de bibliothèque

Breakpoint sur les fonctions importées :

```gdb
break fopen
break fread
break fwrite
break getenv
break system
break dlopen
break dlsym
```

Utilise `info functions` pour vérifier les noms disponibles.

---

# 10. Dump mémoire et récupération de données déchiffrées

## 10.1 Pourquoi dumper la mémoire ?

Un binaire peut stocker sur disque :

- du code chiffré ;
- une configuration chiffrée ;
- un flag chiffré ;
- une seconde charge compressée.

Pendant l’exécution, il doit souvent produire une version utilisable en mémoire. C’est le meilleur moment pour la récupérer.

## 10.2 Identifier les mappings mémoire

Dans GDB :

```gdb
info proc mappings
```

Depuis le shell pour un processus :

```bash
cat /proc/PID/maps
```

Remplace `PID` par le numéro réel uniquement dans la commande exécutée. Dans un script, récupère-le dynamiquement.

## 10.3 Dumper une plage mémoire

Dans GDB :

```gdb
dump binary memory dumps/region.bin START END
```

Exemple :

```gdb
dump binary memory dumps/unpacked.bin 0x7ffff7fc0000 0x7ffff7fd0000
```

Les adresses sont propres à l’exécution en cours. Ne les hardcode pas dans un script général.

## 10.4 Rechercher une chaîne en mémoire

```gdb
find START, END, "flag{"
```

Ou une séquence d’octets :

```gdb
find START, END, 0x66, 0x6c, 0x61, 0x67
```

## 10.5 Dumper une chaîne ou un buffer

```gdb
x/s $rax
x/128bx $rax
```

Puis :

```gdb
dump binary memory dumps/buffer.bin $rax $rax+128
```

## 10.6 Bon moment pour le dump

Le timing est essentiel. Place le breakpoint :

- juste après la boucle de déchiffrement ;
- juste après `mprotect` ;
- juste avant un saut vers une région nouvellement écrite ;
- juste avant `memcmp` ou `strcmp` ;
- après une fonction nommée `decrypt`, `unpack`, `decode`, `transform`.

---

# 11. Analyse dynamique sous Windows

## 11.1 x64dbg

x64dbg est un debugger Windows moderne, adapté aux binaires 32 et 64 bits.

Principales vues :

- CPU : désassemblage et registres ;
- Dump : mémoire ;
- Stack : pile ;
- Memory Map : mappings et permissions ;
- Call Stack : appels imbriqués ;
- Breakpoints : points d’arrêt ;
- Handles : objets système ouverts.

Actions courantes :

- F2 : ajouter ou retirer un breakpoint ;
- F7 : step into ;
- F8 : step over ;
- F9 : run ;
- Ctrl+G : aller à une adresse ou expression.

Méthode typique :

1. Ouvrir le binaire.
2. Laisser x64dbg atteindre l’entry point.
3. Chercher les chaînes de succès ou d’erreur.
4. Suivre leurs références.
5. Poser un breakpoint sur la comparaison ou le saut conditionnel.
6. Examiner les arguments et les buffers.
7. Observer les appels API dans les modules Windows.

## 11.2 OllyDbg

OllyDbg est historiquement très utilisé pour les exécutables 32 bits. Pour un projet moderne, x64dbg est généralement plus pratique, mais les concepts restent identiques :

- breakpoints ;
- stepping ;
- registres ;
- pile ;
- mémoire ;
- patch temporaire ;
- suivi des appels API.

## 11.3 Correspondances Linux et Windows

| Besoin | Linux | Windows |
|---|---|---|
| Debugger | GDB | x64dbg / OllyDbg |
| Processus | `info proc` | Process Explorer |
| Fichiers/API | catchpoints GDB | ProcMon |
| Réseau | Wireshark | Wireshark / TCPView |
| Mappings | `info proc mappings` | Memory Map |
| Chaînes en mémoire | `find`, `x/s` | Search / Dump |
| Dump mémoire | `dump binary memory` | Save memory region |

---

# 12. Wireshark, ProcMon et Sysinternals

# 12.1 Wireshark

Wireshark capture et analyse les paquets réseau.

Avant d’exécuter le programme :

1. Choisir la bonne interface.
2. Commencer la capture.
3. Lancer le programme.
4. Reproduire le comportement.
5. Arrêter la capture.
6. Enregistrer le fichier `.pcapng`.

Filtres d’affichage utiles :

```text
dns
http
tls
tcp
udp
icmp
tcp.port == 443
ip.addr == 192.168.56.10
dns.qry.name contains "example"
tcp.stream eq 0
```

Différence importante :

- filtre de capture : limite ce qui est enregistré ;
- filtre d’affichage : masque ou affiche les paquets déjà capturés.

Observations possibles :

- résolution DNS ;
- adresse distante ;
- port ;
- protocole ;
- requête HTTP ;
- User-Agent ;
- taille et fréquence des communications ;
- tentatives répétées ;
- certificat TLS et SNI.

Avec du TLS, le contenu applicatif est généralement chiffré, mais les métadonnées restent utiles.

## 12.2 Process Monitor

ProcMon surveille en temps réel :

- système de fichiers ;
- registre ;
- processus et threads ;
- chargement d’images et DLL.

Méthode :

1. Lancer ProcMon en administrateur dans la VM.
2. Arrêter temporairement la capture.
3. Effacer les événements précédents.
4. Ajouter un filtre sur le nom du processus cible.
5. Relancer la capture.
6. Exécuter le binaire.
7. Arrêter la capture.
8. Exporter les événements.

Filtres utiles :

- `Process Name is target.exe`
- `Operation is CreateFile`
- `Operation is WriteFile`
- `Operation begins with Reg`
- `Path contains AppData`
- `Result is ACCESS DENIED`

Un résultat `NAME NOT FOUND` n’indique pas forcément une erreur malveillante. De nombreux programmes testent plusieurs chemins.

## 12.3 Process Explorer

Permet d’observer :

- arbre des processus ;
- processus parent ;
- ligne de commande ;
- handles ;
- DLL chargées ;
- threads ;
- signatures numériques.

## 12.4 TCPView

Affiche rapidement les connexions TCP et UDP ouvertes par processus.

## 12.5 Autoruns

Permet d’identifier les mécanismes de démarrage automatique :

- clés Run ;
- services ;
- tâches planifiées ;
- extensions ;
- drivers.

Dans un exercice de malware, compare idéalement l’état avant et après exécution.

---

# 13. Valgrind, Intel Pin et sandbox

## 13.1 Valgrind

Valgrind détecte notamment :

- lectures invalides ;
- écritures invalides ;
- utilisation de mémoire non initialisée ;
- fuites mémoire ;
- double free dans certains cas.

Commande de base :

```bash
valgrind --leak-check=full --track-origins=yes ./target
```

Avec journal :

```bash
valgrind \
  --leak-check=full \
  --track-origins=yes \
  --log-file=valgrind.log \
  ./target
```

Pour un programme interactif :

```bash
valgrind --track-origins=yes ./target < input.txt
```

Limites :

- programme beaucoup plus lent ;
- comportement temporel modifié ;
- certaines protections peuvent détecter l’instrumentation ;
- tous les bugs ne sont pas automatiquement exploitables.

## 13.2 Intel Pin

Intel Pin permet l’instrumentation dynamique binaire. Il peut exécuter du code d’analyse avant ou après les instructions du programme.

Applications :

- compter les instructions ;
- tracer les branches ;
- enregistrer les accès mémoire ;
- suivre les appels de fonctions ;
- produire un profil d’exécution.

Concepts :

- Pin : moteur d’instrumentation ;
- Pintool : module d’analyse écrit en C/C++ ;
- instrumentation : emplacement où le code d’analyse est injecté ;
- analysis routine : code exécuté pendant l’analyse.

Intel Pin est plus avancé que GDB pour collecter énormément d’événements sans s’arrêter à chaque fois.

## 13.3 Cuckoo Sandbox

Cuckoo automatise l’exécution d’un échantillon dans une VM et collecte :

- processus ;
- fichiers ;
- registre ;
- appels API ;
- trafic réseau ;
- captures d’écran ;
- artefacts déposés.

Pour le projet, l’analyse doit rester locale. N’envoie pas l’échantillon vers un service public.

Limites d’une sandbox :

- faux positifs ;
- échantillon qui détecte la VM ;
- chemins non exécutés ;
- dépendance à une configuration correcte ;
- résultat automatisé qui doit être vérifié manuellement.

---

# 14. SAT, SMT et Z3

## 14.1 SAT

SAT signifie Boolean Satisfiability Problem.

Le problème est : existe-t-il une attribution vrai/faux aux variables qui rend une formule vraie ?

Exemple :

```text
(A OR B) AND (NOT A OR C) AND (NOT B OR C)
```

Un solveur SAT cherche une combinaison de valeurs pour `A`, `B` et `C` qui satisfait toutes les clauses.

## 14.2 CNF

Les solveurs SAT classiques utilisent souvent la forme normale conjonctive, ou CNF.

Une CNF est un `AND` de clauses, chaque clause étant un `OR` de littéraux.

Exemple :

```text
(A OR NOT B) AND (B OR C) AND (NOT A OR NOT C)
```

## 14.3 SMT

SMT signifie Satisfiability Modulo Theories.

SMT étend SAT avec des types et théories :

- entiers ;
- nombres réels ;
- tableaux ;
- chaînes ;
- bit-vectors ;
- arithmétique ;
- opérations binaires.

En reverse engineering, les bit-vectors sont particulièrement importants, car les registres et les octets ont une taille fixe.

## 14.4 Pourquoi Z3 est pratique

Z3 sait résoudre des contraintes comme :

```text
x + y == 100
x XOR 0x42 == 0x13
x est un octet
x est un caractère ASCII imprimable
```

Il évite de tester toutes les combinaisons par brute force.

## 14.5 SAT contre brute force

Pour un flag de 32 caractères imprimables, la recherche naïve est astronomique.

Mais si le programme impose de nombreuses relations entre les caractères, un solveur peut exploiter ces contraintes pour éliminer rapidement les valeurs impossibles.

## 14.6 Types Z3 utiles

```python
from z3 import Bool, Int, BitVec

flag_bool = Bool("flag_bool")
number = Int("number")
byte = BitVec("byte", 8)
word = BitVec("word", 32)
```

Pour reproduire exactement les débordements d’octets ou de registres, utilise `BitVec` plutôt que `Int`.

---

# 15. Traduire un binaire en contraintes

C’est la partie la plus importante du SAT solving en reverse engineering.

Le solveur ne comprend pas directement le binaire. Tu dois lui fournir une représentation logique fidèle.

## 15.1 Exemple simple

Pseudo-code :

```c
if (input[0] + input[1] != 150)
    fail();
if ((input[0] ^ input[2]) != 0x21)
    fail();
if (input[1] - input[2] != 7)
    fail();
success();
```

Traduction Z3 :

```python
from z3 import BitVec, Solver, sat

chars = [BitVec(f"c{i}", 8) for i in range(3)]
solver = Solver()

for char in chars:
    solver.add(char >= 0x20, char <= 0x7e)

solver.add(chars[0] + chars[1] == 150)
solver.add((chars[0] ^ chars[2]) == 0x21)
solver.add(chars[1] - chars[2] == 7)

if solver.check() == sat:
    model = solver.model()
    result = "".join(chr(model.eval(c).as_long()) for c in chars)
    print(result)
else:
    print("No solution")
```

## 15.2 Attention aux tailles

En C :

```c
unsigned char x = 250;
x += 10;
```

`x` devient 4 à cause du débordement modulo 256.

Avec un `BitVec` de 8 bits, Z3 reproduit automatiquement ce comportement.

Avec un `Int`, il obtiendrait 260, ce qui serait incorrect.

## 15.3 Signé contre non signé

Une comparaison assembleur peut être signée ou non signée.

Sauts signés :

- `jg`, `jge`, `jl`, `jle`

Sauts non signés :

- `ja`, `jae`, `jb`, `jbe`

Dans Z3, les comparaisons ordinaires sur `BitVec` sont signées. Pour une comparaison non signée, utilise :

```python
from z3 import ULT, ULE, UGT, UGE

solver.add(UGE(x, 0x20))
solver.add(ULE(x, 0x7e))
```

Pour les caractères ASCII imprimables, une comparaison signée fonctionne souvent car les valeurs sont inférieures à `0x80`, mais il est préférable d’être explicite.

## 15.4 Contraintes indirectes

Pseudo-code :

```c
sum = 0;
for (i = 0; i < 8; i++)
    sum += input[i] * (i + 1);
if (sum != 3137)
    fail();
```

Traduction :

```python
solver.add(
    sum(chars[i] * (i + 1) for i in range(8)) == 3137
)
```

Mais attention : si `sum` est un entier 32 bits dans le binaire, modélise-le correctement avec une extension de taille.

```python
from z3 import ZeroExt

solver.add(
    sum(ZeroExt(24, chars[i]) * (i + 1) for i in range(8)) == 3137
)
```

`ZeroExt(24, char)` transforme un octet de 8 bits en valeur de 32 bits non signée.

## 15.5 Rotation de bits

Le code peut utiliser `rol` ou `ror`.

Z3 fournit :

```python
from z3 import RotateLeft, RotateRight

solver.add(RotateLeft(x, 3) == 0x91)
```

## 15.6 Extraction et concaténation

```python
from z3 import Extract, Concat

high_byte = Extract(15, 8, word)
low_byte = Extract(7, 0, word)
combined = Concat(high_byte, low_byte)
```

## 15.7 Tableaux et lookups

Un binaire peut utiliser une table :

```c
if (table[input[i]] != expected[i])
    fail();
```

Tu peux parfois inverser la table en Python avant d’utiliser Z3. Cela est souvent plus rapide que de modéliser une grande mémoire symbolique.

---

# 16. Résoudre avec Z3

## 16.1 Installation

```bash
python3 -m venv .venv
. .venv/bin/activate
python3 -m pip install --upgrade pip
python3 -m pip install z3-solver
```

Le dossier `.venv` est relatif au projet.

## 16.2 Modèle de solveur robuste

Crée `scripts/solve_z3.py` :

```python
#!/usr/bin/env python3
"""Solve logical constraints extracted from a reverse-engineering target."""

from __future__ import annotations

import argparse
import sys
from pathlib import Path

from z3 import BitVec, Solver, UGE, ULE, sat


def build_solver(length: int) -> tuple[Solver, list]:
    """Create the symbolic input and add generic constraints."""
    solver = Solver()
    chars = [BitVec(f"flag_{index}", 8) for index in range(length)]

    for char in chars:
        solver.add(UGE(char, 0x20), ULE(char, 0x7E))

    # Add constraints extracted from the target below.
    # Example:
    # solver.add(chars[0] == ord("H"))

    return solver, chars


def solve(length: int) -> str:
    solver, chars = build_solver(length)

    result = solver.check()
    if result != sat:
        raise RuntimeError(f"Solver returned: {result}")

    model = solver.model()
    return "".join(chr(model.eval(char).as_long()) for char in chars)


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("--length", type=int, required=True)
    parser.add_argument(
        "--output",
        type=Path,
        default=Path("0-flag.txt"),
        help="Relative output path",
    )
    return parser.parse_args()


def main() -> int:
    args = parse_args()

    if args.length <= 0:
        print("error: --length must be positive", file=sys.stderr)
        return 2

    try:
        flag = solve(args.length)
    except RuntimeError as error:
        print(f"error: {error}", file=sys.stderr)
        return 1

    args.output.write_text(flag + "\n", encoding="utf-8")
    print(flag)
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

Rendre exécutable :

```bash
chmod +x scripts/solve_z3.py
```

Exécution :

```bash
./scripts/solve_z3.py --length 32 --output 0-flag.txt
```

## 16.3 Plusieurs solutions

Pour vérifier si la solution est unique :

```python
first_model = solver.model()
first_values = [first_model.eval(char) for char in chars]

solver.add(
    # Au moins un caractère doit être différent.
    # `Or` doit être importé depuis z3.
    Or(*[char != value for char, value in zip(chars, first_values)])
)

if solver.check() == sat:
    print("Another solution exists")
else:
    print("The solution is unique under the current constraints")
```

## 16.4 Optimiser un solveur lent

Un solveur lent n’est pas toujours causé par Z3. Le modèle peut être inutilement complexe.

Techniques :

1. Contraindre la longueur exacte.
2. Contraindre l’alphabet.
3. Ajouter le format connu, par exemple `flag{...}`.
4. Éviter les conversions répétées entre `Int` et `BitVec`.
5. Simplifier les constantes en Python.
6. Découper les groupes de contraintes indépendants.
7. Éviter de modéliser une énorme mémoire si une table Python suffit.
8. Utiliser les bonnes tailles de bit-vectors.
9. Vérifier les contraintes une par une.
10. Chercher les contradictions tôt.

Exemple de format :

```python
prefix = b"flag{"
for index, value in enumerate(prefix):
    solver.add(chars[index] == value)

solver.add(chars[-1] == ord("}"))
```

## 16.5 Diagnostiquer une formule `unsat`

Ajoute progressivement les contraintes.

```python
solver.push()
solver.add(new_constraint)
print(solver.check())
solver.pop()
```

Pour les projets complexes, nomme les contraintes :

```python
solver.assert_and_track(chars[0] == ord("H"), "first_char_H")
```

Puis utilise un unsat core lorsque nécessaire.

---

# 17. Résoudre avec Angr

## 17.1 Qu’est-ce qu’Angr ?

Angr est un framework d’analyse binaire. Il peut effectuer de l’exécution symbolique : au lieu de donner une valeur concrète à l’entrée, il représente les octets par des variables symboliques et suit les contraintes accumulées sur les chemins.

Le but typique est :

- atteindre l’adresse de succès ;
- éviter les adresses d’échec ;
- demander au solveur une entrée satisfaisant ce chemin.

## 17.2 Installation locale

Dans un environnement virtuel :

```bash
python3 -m venv .venv
. .venv/bin/activate
python3 -m pip install --upgrade pip
python3 -m pip install angr
```

## 17.3 Schéma de base

```python
#!/usr/bin/env python3
"""Find a valid stdin using Angr symbolic execution."""

from __future__ import annotations

import argparse
from pathlib import Path

import angr
import claripy


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("binary", type=Path)
    parser.add_argument("--length", type=int, required=True)
    parser.add_argument("--find", required=True, help="Success address in hex")
    parser.add_argument(
        "--avoid",
        action="append",
        default=[],
        help="Failure address in hex; may be repeated",
    )
    return parser.parse_args()


def parse_address(value: str) -> int:
    return int(value, 0)


def main() -> int:
    args = parse_args()
    binary = args.binary.resolve()

    project = angr.Project(str(binary), auto_load_libs=False)

    symbolic_input = claripy.BVS("stdin", args.length * 8)
    state = project.factory.full_init_state(
        args=[str(binary)],
        stdin=symbolic_input,
    )

    for index in range(args.length):
        byte = symbolic_input.get_byte(index)
        state.solver.add(byte >= 0x20, byte <= 0x7E)

    manager = project.factory.simulation_manager(state)
    manager.explore(
        find=parse_address(args.find),
        avoid=[parse_address(address) for address in args.avoid],
    )

    if not manager.found:
        print("No satisfying path found")
        return 1

    found = manager.found[0]
    solution = found.solver.eval(symbolic_input, cast_to=bytes)
    print(solution.decode("ascii", errors="replace"))
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

## 17.4 Trouver les adresses `find` et `avoid`

Méthode :

1. Rechercher les chaînes de succès et d’échec.
2. Trouver les références croisées dans le désassemblage.
3. Identifier l’adresse du bloc qui affiche le succès.
4. Identifier les blocs d’échec.
5. Vérifier les adresses dans GDB.

Commandes possibles :

```bash
strings -tx ./target | grep -Ei 'success|correct|wrong|fail'
objdump -d -M intel ./target | less
```

Dans un PIE, Angr utilise les adresses du projet chargé. Vérifie l’adresse de base utilisée par Angr. Selon la manière de créer l’état, les adresses du désassemblage peuvent nécessiter un ajustement.

## 17.5 Éviter l’explosion de chemins

L’exécution symbolique peut explorer énormément de branches.

Réductions possibles :

- `auto_load_libs=False` ;
- entrée de longueur exacte ;
- alphabet limité ;
- adresses `avoid` précises ;
- démarrage à une fonction interne plutôt qu’au loader ;
- hook d’une fonction coûteuse ;
- remplacement d’un délai ou d’une boucle inutile ;
- résolution manuelle d’une partie des contraintes ;
- exploration en plusieurs étapes.

## 17.6 Z3 ou Angr ?

Utilise Z3 lorsque :

- les contraintes sont accessibles et compréhensibles ;
- tu peux les transcrire proprement ;
- le programme possède beaucoup de chemins mais peu de relations mathématiques.

Utilise Angr lorsque :

- le flot est complexe ;
- les contraintes sont dispersées ;
- la branche de succès est identifiable ;
- la lecture manuelle de toutes les contraintes serait trop longue.

Utilise une approche hybride lorsque :

- Angr se perd dans certaines fonctions ;
- une partie est facile à modéliser manuellement ;
- tu peux extraire dynamiquement des constantes ;
- tu veux vérifier la solution avec le vrai binaire.

---

# 18. Task 0 — SAT Solving in Reverse Engineering

Le sujet indique qu’un binaire contient des contraintes logiques complexes et que Z3 peut prendre environ 35 minutes selon la machine.

L’objectif n’est pas simplement de lancer Angr et d’attendre. Il faut comprendre comment le binaire transforme l’entrée en contraintes.

## 18.1 Livrable

```text
holbertonschool-reverse_engineering/
└── dynamic_analysis/
    └── 0-flag.txt
```

Le fichier doit contenir uniquement le flag attendu, généralement terminé par un retour à la ligne.

## 18.2 Étape 1 — Préparer le binaire

Depuis le dossier `dynamic_analysis` :

```bash
chmod u+x ./target
sha256sum ./target > target.sha256
file ./target
```

Adapte `target` au nom réel du fichier téléchargé.

## 18.3 Étape 2 — Observer l’interface

```bash
./target
printf 'exit code: %d\n' "$?"
```

Teste une entrée simple :

```bash
printf 'AAAA\n' | ./target
```

Note :

- longueur attendue ;
- lecture depuis stdin ou argv ;
- message de succès ;
- message d’échec ;
- temps d’exécution.

## 18.4 Étape 3 — Triage statique

```bash
strings -a -n 4 ./target | less
nm -n ./target 2>/dev/null | less
objdump -d -M intel ./target > notes/target.asm
```

Recherche :

```bash
grep -Ein 'success|correct|wrong|fail|flag' notes/target.asm
```

Le texte des chaînes n’apparaît pas toujours directement dans le désassemblage. Utilise aussi :

```bash
strings -tx ./target | grep -Ei 'success|correct|wrong|fail|flag'
```

## 18.5 Étape 4 — Repérer la lecture de l’entrée

Dans GDB :

```gdb
set disassembly-flavor intel
start
info functions
break read
break fgets
break scanf
break __isoc99_scanf
continue
```

Tous les breakpoints ne seront pas valides. Garde ceux correspondant aux imports réels.

À l’arrêt :

```gdb
info registers rdi rsi rdx
x/s $rdi
x/s $rsi
```

Détermine :

- où est stockée l’entrée ;
- quelle longueur est lue ;
- si le retour à la ligne est conservé.

## 18.6 Étape 5 — Repérer les contraintes

Cherche les patterns assembleur :

```asm
movzx eax, BYTE PTR [buffer+index]
xor eax, constant
add eax, other_value
cmp eax, expected
jne failure
```

Pour chaque contrainte, note :

| ID | Octets impliqués | Opération | Constante | Condition | Signé ? |
|---|---|---|---|---|---|
| C1 | input[0] | XOR | 0x42 | == 0x17 | non |
| C2 | input[1], input[2] | addition | — | == 0x91 | selon taille |

Cette table évite les erreurs de transcription.

## 18.7 Étape 6 — Vérifier dynamiquement

Place un breakpoint avant une comparaison :

```gdb
break *ADDRESS
run
```

Puis :

```gdb
info registers
x/32bx BUFFER_ADDRESS
x/10i $rip
```

Avance avec `ni` et vérifie que ta compréhension du calcul correspond aux registres.

## 18.8 Étape 7 — Choisir l’approche

### Approche A : Z3 manuel

À choisir si les contraintes sont assez explicites.

### Approche B : Angr

À choisir si le binaire contient de nombreuses branches mais des blocs de succès/échec identifiables.

### Approche C : hybride

1. Extraire la longueur et les constantes statiquement.
2. Vérifier les opérations dans GDB.
3. Modéliser les contraintes importantes en Z3.
4. Utiliser Angr uniquement pour une fonction complexe.
5. Valider la solution sur le vrai binaire.

## 18.9 Étape 8 — Réduire le temps de résolution

Avant de lancer une résolution longue :

- ajouter le préfixe du flag lorsqu’il est connu ;
- ajouter la dernière accolade ;
- limiter les caractères à l’ASCII attendu ;
- vérifier la longueur exacte ;
- éviter le chargement des bibliothèques dans Angr ;
- ajouter toutes les branches d’échec dans `avoid` ;
- supprimer symboliquement le caractère de fin de ligne si le programme ne le traite pas ;
- démarrer après l’initialisation si elle n’est pas pertinente ;
- tester d’abord sur une version simplifiée.

## 18.10 Étape 9 — Valider le résultat

```bash
printf '%s\n' 'FLAG_TROUVE' | ./target
```

N’écris pas le flag dans le livrable avant cette validation.

Puis :

```bash
printf '%s\n' 'FLAG_TROUVE' > 0-flag.txt
cat -A 0-flag.txt
```

`cat -A` permet de vérifier qu’il n’y a pas d’espace ou de caractère invisible inattendu.

## 18.11 Checklist Task 0

- [ ] Binaire hashé et copié.
- [ ] Architecture identifiée.
- [ ] Entrée et longueur identifiées.
- [ ] Adresses de succès et d’échec trouvées.
- [ ] Contraintes vérifiées dans GDB.
- [ ] Tailles et signedness correctement modélisées.
- [ ] Solution validée par le vrai binaire.
- [ ] `0-flag.txt` contient uniquement le flag.

---

# 19. Task 1 — Exploring Anti-Debugging Techniques

L’anti-debugging désigne les techniques utilisées pour détecter, perturber ou tromper un debugger.

L’objectif de l’analyste est double :

1. reconnaître la technique ;
2. neutraliser son effet dans le laboratoire pour observer le vrai comportement.

## 19.1 `ptrace(PTRACE_TRACEME)`

Sous Linux, un programme peut appeler :

```c
ptrace(PTRACE_TRACEME, 0, NULL, NULL);
```

Un processus déjà debuggué ne peut généralement pas se déclarer lui-même traçable de cette manière. L’appel renvoie une erreur, ce qui permet au programme de détecter GDB.

### Détection statique

```bash
nm -D ./target | grep ptrace
objdump -d -M intel ./target | grep -n ptrace
strings -a ./target | grep -i debug
```

### Détection dynamique

```gdb
break ptrace
run
```

Au breakpoint, vérifie le premier argument :

```gdb
p/d $rdi
```

Sur Linux x86-64, `PTRACE_TRACEME` vaut typiquement zéro.

### Bypass par valeur de retour

```gdb
break ptrace
run
finish
set $rax = 0
continue
```

Tu forces l’appel à sembler réussi.

Une méthode plus propre consiste à mettre un breakpoint juste après le `call ptrace`, puis à modifier `RAX`.

## 19.2 `/proc/self/status` et `TracerPid`

Un programme peut lire :

```text
/proc/self/status
```

Puis chercher :

```text
TracerPid:	0
```

Sous debugger, la valeur est non nulle.

### Observation

```gdb
catch syscall openat
run
```

À l’arrêt :

```gdb
x/s $rsi
```

Continue jusqu’à voir `/proc/self/status`.

Puis observe `read`, `fgets`, `strstr` ou `atoi`.

### Bypass

Options :

- modifier le buffer après lecture ;
- forcer le résultat de la fonction qui analyse `TracerPid` ;
- modifier le saut conditionnel ;
- retourner une valeur normale depuis la fonction de détection.

Exemple conceptuel :

```gdb
break *ADDRESS_AFTER_CHECK
run
set $eax = 0
continue
```

La valeur exacte dépend du code. Vérifie si zéro signifie « pas de debugger ».

## 19.3 Mesure du temps

Le programme mesure la durée entre deux points. Le stepping ralentit l’exécution et révèle le debugger.

API ou instructions fréquentes :

- `clock_gettime`
- `gettimeofday`
- `time`
- `rdtsc`
- `rdtscp`

Pseudo-code :

```c
start = clock_gettime(...);
operation();
end = clock_gettime(...);
if (end - start > threshold)
    debugger_detected();
```

### Analyse

```gdb
break clock_gettime
run
```

Ou chercher `rdtsc` :

```bash
objdump -d -M intel ./target | grep -nE 'rdtsc|rdtscp'
```

### Bypass

- éviter le stepping dans la zone chronométrée ;
- placer un breakpoint après la mesure ;
- forcer la différence à une petite valeur ;
- forcer le résultat du test ;
- patcher temporairement le saut conditionnel.

## 19.4 `SIGTRAP` et `int3`

`int3` génère un breakpoint logiciel.

Un programme peut installer un gestionnaire de signal et vérifier qui reçoit `SIGTRAP`.

Commandes GDB :

```gdb
info signals SIGTRAP
handle SIGTRAP nostop noprint pass
```

Ou, selon le comportement recherché :

```gdb
handle SIGTRAP stop print nopass
```

Ne change pas cette configuration sans comprendre si le programme doit recevoir le signal.

## 19.5 Vérification du processus parent

Un programme peut vérifier son parent et rechercher `gdb`, `strace` ou un nom de debugger.

Appels possibles :

- `getppid`
- lecture de `/proc/<ppid>/cmdline`
- lecture de `/proc/<ppid>/comm`

Observe les chemins ouverts et le résultat de `getppid`.

## 19.6 Vérification des breakpoints logiciels

Un breakpoint logiciel remplace souvent le premier octet d’une instruction par `0xCC`.

Un programme peut vérifier le checksum de son propre code ou chercher cet octet.

Solutions :

- utiliser un breakpoint matériel si possible ;
- poser le breakpoint après la vérification ;
- utiliser un watchpoint ;
- neutraliser la routine de checksum ;
- lancer jusqu’à une adresse avec un breakpoint temporaire puis le supprimer.

Dans GDB, un breakpoint matériel :

```gdb
hbreak *ADDRESS
```

## 19.7 Saut conditionnel anti-debug

Pattern fréquent :

```asm
call detect_debugger
test eax, eax
jne debugger_found
```

Deux méthodes de laboratoire :

### Forcer le retour

```gdb
break *ADDRESS_AFTER_CALL
run
set $eax = 0
continue
```

### Modifier le flot

```gdb
set $rip = ADDRESS_NORMAL_PATH
continue
```

## 19.8 Script GDB automatisé

Crée `scripts/bypass_antidebug.gdb` :

```gdb
set pagination off
set confirm off
set disassembly-flavor intel

break ptrace
commands
silent
finish
set $rax = 0
continue
end

run
```

Lancer :

```bash
gdb -q -x scripts/bypass_antidebug.gdb --args ./target
```

Selon la version de GDB et le flot du programme, `finish` dans un bloc `commands` peut nécessiter une adaptation. La méthode la plus fiable reste un breakpoint explicitement placé après l’appel.

## 19.9 Méthode de résolution Task 1

1. Exécuter normalement.
2. Exécuter sous GDB et comparer le comportement.
3. Rechercher `ptrace`, `TracerPid`, les API de temps et `SIGTRAP`.
4. Poser un breakpoint sur la technique suspectée.
5. Identifier la variable ou valeur de retour utilisée.
6. Forcer uniquement cette valeur.
7. Continuer vers la logique réelle.
8. Documenter le mécanisme exact et le bypass.
9. Valider que le comportement normal est restauré.

## 19.10 Ce qu’il faut être capable d’expliquer

- Comment le programme détecte GDB.
- Quelle instruction décide du chemin anti-debug.
- Quelle valeur signifie « debugger détecté ».
- Quelle modification temporaire neutralise le contrôle.
- Pourquoi la modification ne résout pas encore la logique principale.

---

# 20. Task 2 — SAT Solving, approche hybride

La liste du projet affiche une deuxième tâche nommée SAT Solving. Le détail exact peut différer du Task 0. La méthode ci-dessous est volontairement adaptable : vérifie toujours l’énoncé et le binaire réel.

Le Task 0 peut être orienté traduction complète des contraintes. Une seconde tâche SAT peut demander davantage d’analyse dynamique, de récupération de constantes, ou d’utilisation d’Angr.

## 20.1 Approche recommandée

### Étape A — Identifier la fonction de validation

```bash
nm -n ./target 2>/dev/null | grep -Ei 'check|verify|validate|flag'
objdump -d -M intel ./target | less
```

Dans GDB :

```gdb
break main
run
```

Suis les appels depuis `main`.

### Étape B — Logger les comparaisons

Si le programme compare chaque caractère dans une boucle, pose un breakpoint à l’instruction `cmp` et affiche :

- index ;
- valeur calculée ;
- valeur attendue ;
- octets d’entrée impliqués.

Exemple de commandes GDB :

```gdb
commands 1
silent
printf "i=%u calc=%#x expected=%#x\n", $ecx, $eax, $edx
continue
end
```

Adapte les registres au binaire réel.

### Étape C — Extraire les constantes

Si une table est chargée depuis `.rodata` :

```bash
objdump -s -j .rodata ./target | less
```

Ou dans GDB :

```gdb
x/64wx ADDRESS
```

Tu peux dumper la table :

```gdb
dump binary memory dumps/constants.bin START END
```

### Étape D — Reproduire l’algorithme concrètement

Avant de rendre l’entrée symbolique, écris une fonction Python qui reproduit le calcul sur une entrée connue.

Exemple :

```python
def transform(data: bytes) -> list[int]:
    output = []
    for index, value in enumerate(data):
        result = ((value ^ 0x42) + index) & 0xFF
        output.append(result)
    return output
```

Compare les résultats Python aux valeurs observées dans GDB.

### Étape E — Symboliser uniquement l’entrée

Une fois la fonction validée, remplace les octets concrets par des `BitVec` Z3.

### Étape F — Valider avec le binaire

Le solveur peut satisfaire un modèle incorrect. Le vrai binaire reste la référence.

## 20.2 Extraction automatique d’une table

Exemple de script qui lit un dump relatif :

```python
#!/usr/bin/env python3
"""Read constants dumped from the target process."""

from __future__ import annotations

import argparse
from pathlib import Path


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("dump", type=Path)
    return parser.parse_args()


def main() -> int:
    args = parse_args()
    data = args.dump.read_bytes()

    for index, value in enumerate(data):
        print(f"{index:04d}: 0x{value:02x} ({value})")

    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

## 20.3 Quand Angr est bloqué

Causes fréquentes :

- boucle dépendant d’une entrée symbolique ;
- bibliothèque externe complexe ;
- anti-debugging ;
- code auto-modifiant ;
- appels système non modélisés ;
- trop de chemins ;
- mauvaise adresse de succès ;
- newline ou longueur incorrecte.

Stratégies :

- résoudre l’anti-debug avant l’exécution symbolique ;
- dumper le code déchiffré et analyser le dump ;
- hooker une fonction ;
- démarrer l’état directement à la fonction de validation ;
- imposer davantage de contraintes ;
- remplacer une boucle par une relation Z3 manuelle.

---

# 21. Task 3 — Self-modifying code

## 21.1 Définition

Un programme auto-modifiant écrit dans une zone mémoire contenant du code qui sera ensuite exécuté.

Cela peut servir à :

- déchiffrer une fonction ;
- décompresser un payload ;
- générer du code spécialisé ;
- compliquer le reverse engineering ;
- cacher la logique réelle jusqu’au runtime.

## 21.2 Pourquoi l’analyse statique échoue

Le fichier sur disque peut contenir :

- des octets chiffrés ;
- des instructions invalides ;
- un stub minimal ;
- une fonction qui n’existe réellement qu’après transformation.

Le désassemblage initial montre donc parfois du bruit, alors que la mémoire après déchiffrement contient un code parfaitement valide.

## 21.3 Indicateurs

Rechercher les imports ou syscalls :

```bash
nm -D ./target | grep -E 'mmap|mprotect|memcpy|memmove'
objdump -d -M intel ./target | grep -nE 'mmap|mprotect|memcpy|memmove'
```

Signaux typiques :

- allocation `RWX` ;
- page d’abord `RW`, puis `RX` ;
- boucle qui écrit dans une adresse puis saut indirect vers cette adresse ;
- `call rax`, `jmp rax`, `call [reg]` ;
- écriture dans `.text` après `mprotect` ;
- XOR ou addition appliquée à une grande série d’octets.

## 21.4 Permissions mémoire

Permissions habituelles :

- `R` : lecture ;
- `W` : écriture ;
- `X` : exécution.

Principe W^X : une page ne devrait idéalement pas être écrivable et exécutable simultanément.

Un programme auto-modifiant peut :

1. rendre une page `RW` ;
2. écrire le nouveau code ;
3. rendre la page `RX` ;
4. sauter vers le code.

## 21.5 Observer `mmap`

```gdb
break mmap
run
```

Arguments x86-64 :

```c
void *mmap(void *addr, size_t length, int prot,
           int flags, int fd, off_t offset);
```

Registres :

- `RDI` : adresse souhaitée ;
- `RSI` : longueur ;
- `RDX` : protections ;
- `RCX` : flags au niveau fonction C avant le syscall ;
- retour dans `RAX`.

Après retour :

```gdb
finish
p/x $rax
```

Enregistre l’adresse retournée.

Valeurs de protection courantes :

- `PROT_READ` = 1 ;
- `PROT_WRITE` = 2 ;
- `PROT_EXEC` = 4.

Donc 7 signifie généralement `RWX`.

## 21.6 Observer `mprotect`

```gdb
break mprotect
run
```

Arguments :

- `RDI` : début de la zone ;
- `RSI` : longueur ;
- `RDX` : nouvelles permissions.

Affiche :

```gdb
p/x $rdi
p/u $rsi
p/x $rdx
```

Place ensuite un breakpoint juste après le retour de `mprotect`.

## 21.7 Trouver la boucle de déchiffrement

Pattern fréquent :

```asm
movzx eax, BYTE PTR [source+index]
xor eax, key
mov BYTE PTR [destination+index], al
inc index
cmp index, length
jne loop
```

Méthode :

1. Identifier l’adresse destination.
2. Poser un watchpoint sur les premiers octets.
3. Continuer jusqu’à l’écriture.
4. Identifier la boucle.
5. Placer un breakpoint à sa sortie.
6. Dumper toute la zone.

## 21.8 Watchpoint sur le code généré

```gdb
watch *(unsigned char *)GENERATED_ADDRESS
continue
```

Après l’arrêt :

```gdb
x/32bx GENERATED_ADDRESS
x/10i GENERATED_ADDRESS
```

Au début, le désassemblage peut être incomplet. Réexécute `x/i` après la fin de la transformation.

## 21.9 Détecter le saut vers le code généré

Cherche :

```asm
call rax
jmp rax
call rbx
jmp rbx
```

Au moment du saut :

```gdb
p/x $rax
x/20i $rax
```

Puis :

```gdb
break *$rax
continue
```

Selon GDB, évalue d’abord l’adresse et pose le breakpoint avec sa valeur concrète.

## 21.10 Dumper après déchiffrement

```gdb
dump binary memory dumps/decrypted_code.bin START END
```

Puis analyse le dump :

```bash
file dumps/decrypted_code.bin
xxd -g 1 dumps/decrypted_code.bin | less
objdump -D -b binary -m i386:x86-64 -M intel dumps/decrypted_code.bin | less
```

Un dump brut ne possède pas d’en-tête ELF. Il faut donc préciser à `objdump` qu’il s’agit d’un binaire brut et choisir l’architecture.

## 21.11 Modifier `.text`

Certains programmes rendent leur propre `.text` écrivable.

Dans GDB :

```gdb
info proc mappings
```

Repère la région de l’exécutable. Si elle passe de `r-xp` à `rwxp` ou `rw-p`, c’est un indicateur fort.

## 21.12 Invalidations et cache d’instructions

Sur x86, la cohérence entre les caches de données et d’instructions facilite le code auto-modifiant. Sur d’autres architectures, il peut être nécessaire de synchroniser explicitement le cache d’instructions. Pour ce projet Kali x86-64, le point principal est surtout de capturer le code après son écriture et avant son exécution.

## 21.13 Méthode complète Task 3

1. Observer le comportement normal.
2. Rechercher `mmap`, `mprotect`, `memcpy` et les sauts indirects.
3. Breakpoint sur les allocations ou changements de permissions.
4. Noter adresse et taille de la région.
5. Identifier la boucle qui écrit dans cette région.
6. Breakpoint à la fin de la boucle.
7. Examiner la région comme données puis comme instructions.
8. Dumper la région.
9. Poser un breakpoint à son entrée.
10. Suivre la logique maintenant déchiffrée.
11. Extraire le flag ou la condition attendue.
12. Valider le résultat.

## 21.14 Script GDB de dump à adapter

```gdb
set pagination off
set confirm off
set disassembly-flavor intel

# Replace these symbolic locations only after identifying them in the target.
# break *DECRYPTION_END
# commands
# silent
# dump binary memory dumps/decrypted.bin START_ADDRESS END_ADDRESS
# x/20i START_ADDRESS
# continue
# end

run
```

Le fichier utilise des emplacements commentés pour éviter de prétendre que des adresses génériques fonctionnent sur tous les binaires.

---

# 22. Task 4 — Solve the 100 Binaries

Une série de 100 binaires teste généralement ta capacité à reconnaître des patterns et à automatiser sans analyser aveuglément chaque fichier.

Le piège serait de faire soit :

- 100 analyses entièrement manuelles ;
- une automatisation massive sans comprendre les familles de challenges.

La bonne approche est :

> classifier, comprendre un exemple de chaque famille, puis automatiser la logique répétitive.

## 22.1 Inventaire

Depuis le dossier contenant les binaires :

```bash
find . -maxdepth 1 -type f -print0 \
  | sort -z \
  | xargs -0 file
```

Empreintes :

```bash
find . -maxdepth 1 -type f -print0 \
  | sort -z \
  | xargs -0 sha256sum > binaries.sha256
```

## 22.2 Classifier

Critères :

- 32 ou 64 bits ;
- PIE ou non-PIE ;
- stripped ou non ;
- taille ;
- imports ;
- chaînes ;
- nom des fonctions ;
- structure de la fonction de validation ;
- type d’entrée ;
- présence de `strcmp`, `memcmp`, XOR, addition, rotation, table, anti-debugging ou code auto-modifiant.

## 22.3 Script d’inventaire

Crée `scripts/inventory.py` :

```python
#!/usr/bin/env python3
"""Create a local inventory of challenge binaries."""

from __future__ import annotations

import argparse
import hashlib
import json
import subprocess
from pathlib import Path


def sha256(path: Path) -> str:
    digest = hashlib.sha256()
    with path.open("rb") as stream:
        for chunk in iter(lambda: stream.read(1024 * 1024), b""):
            digest.update(chunk)
    return digest.hexdigest()


def run_command(command: list[str]) -> str:
    result = subprocess.run(
        command,
        check=False,
        capture_output=True,
        text=True,
        timeout=20,
    )
    return (result.stdout or result.stderr).strip()


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument(
        "directory",
        type=Path,
        nargs="?",
        default=Path("."),
    )
    parser.add_argument(
        "--output",
        type=Path,
        default=Path("inventory.json"),
    )
    return parser.parse_args()


def main() -> int:
    args = parse_args()
    directory = args.directory.resolve()

    records = []
    for path in sorted(directory.iterdir()):
        if not path.is_file():
            continue

        records.append(
            {
                "name": path.name,
                "size": path.stat().st_size,
                "sha256": sha256(path),
                "file": run_command(["file", "-b", str(path)]),
            }
        )

    args.output.write_text(
        json.dumps(records, indent=2) + "\n",
        encoding="utf-8",
    )
    print(f"Wrote {len(records)} records to {args.output}")
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

## 22.4 Tester les comportements

Utilise un runner avec timeout pour éviter qu’un binaire bloque.

Crée `scripts/run_samples.py` :

```python
#!/usr/bin/env python3
"""Execute local challenge binaries with controlled sample inputs."""

from __future__ import annotations

import argparse
import json
import os
import subprocess
from pathlib import Path


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("directory", type=Path)
    parser.add_argument("--input", default="AAAA")
    parser.add_argument("--timeout", type=float, default=2.0)
    parser.add_argument("--output", type=Path, default=Path("runs.json"))
    return parser.parse_args()


def is_executable(path: Path) -> bool:
    return path.is_file() and os.access(path, os.X_OK)


def main() -> int:
    args = parse_args()
    directory = args.directory.resolve()
    records = []

    for path in sorted(directory.iterdir()):
        if not is_executable(path):
            continue

        try:
            result = subprocess.run(
                [str(path)],
                input=args.input + "\n",
                capture_output=True,
                text=True,
                timeout=args.timeout,
                cwd=directory,
                check=False,
            )
            record = {
                "name": path.name,
                "returncode": result.returncode,
                "stdout": result.stdout,
                "stderr": result.stderr,
                "timeout": False,
            }
        except subprocess.TimeoutExpired as error:
            record = {
                "name": path.name,
                "returncode": None,
                "stdout": error.stdout or "",
                "stderr": error.stderr or "",
                "timeout": True,
            }

        records.append(record)
        print(f"{path.name}: {'timeout' if record['timeout'] else record['returncode']}")

    args.output.write_text(
        json.dumps(records, indent=2) + "\n",
        encoding="utf-8",
    )
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

## 22.5 Extraire les chaînes de tous les binaires

```bash
mkdir -p notes/strings
for binary in ./binaries/*; do
    [ -f "$binary" ] || continue
    name=$(basename "$binary")
    strings -a -n 4 -- "$binary" > "notes/strings/${name}.txt"
done
```

Le chemin `./binaries` est relatif au dossier de travail.

## 22.6 Grouper par imports

Exemple :

```bash
for binary in ./binaries/*; do
    [ -f "$binary" ] || continue
    printf '\n== %s ==\n' "$(basename "$binary")"
    nm -D -- "$binary" 2>/dev/null \
      | grep -E 'strcmp|memcmp|ptrace|mprotect|mmap|scanf|fgets|read' \
      || true
done
```

Groupes possibles :

- famille `strcmp` : secret probablement construit en mémoire ;
- famille `memcmp` : comparaison binaire ;
- famille mathématique : solveur Z3 ;
- famille anti-debug : bypass avant validation ;
- famille auto-modifiante : dump puis analyse ;
- famille simple XOR : inversion directe ;
- famille table : extraction et inversion ;
- famille Angr : chemin de succès commun.

## 22.7 GDB en mode batch

GDB peut être automatisé sans interface interactive.

Fichier `scripts/trace_compare.gdb` :

```gdb
set pagination off
set confirm off
set disassembly-flavor intel

break strcmp
commands
silent
printf "strcmp called\n"
x/s $rdi
x/s $rsi
continue
end

run
```

Lancement :

```bash
gdb -q -batch \
  -x scripts/trace_compare.gdb \
  --args ./binaries/challenge_001 \
  < input.txt
```

## 22.8 Script batch GDB avec timeout

```python
#!/usr/bin/env python3
"""Run a GDB command file against multiple local binaries."""

from __future__ import annotations

import argparse
import subprocess
from pathlib import Path


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("directory", type=Path)
    parser.add_argument("gdb_script", type=Path)
    parser.add_argument("--timeout", type=float, default=10.0)
    parser.add_argument("--logs", type=Path, default=Path("notes/gdb"))
    return parser.parse_args()


def main() -> int:
    args = parse_args()
    directory = args.directory.resolve()
    script = args.gdb_script.resolve()
    args.logs.mkdir(parents=True, exist_ok=True)

    for binary in sorted(directory.iterdir()):
        if not binary.is_file():
            continue

        log_path = args.logs / f"{binary.name}.log"
        try:
            result = subprocess.run(
                [
                    "gdb",
                    "-q",
                    "-batch",
                    "-x",
                    str(script),
                    "--args",
                    str(binary),
                ],
                capture_output=True,
                text=True,
                timeout=args.timeout,
                check=False,
            )
            content = result.stdout + result.stderr
        except subprocess.TimeoutExpired:
            content = "TIMEOUT\n"

        log_path.write_text(content, encoding="utf-8")
        print(f"{binary.name} -> {log_path}")

    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

## 22.9 Ne pas hardcoder les adresses

Sur 100 binaires, les adresses changent. Préfère :

- noms de fonctions ;
- offsets relatifs ;
- recherche de signatures ;
- parsing de symboles ;
- adresses calculées à partir de la base du module ;
- extraction via `objdump` ou une bibliothèque locale d’analyse.

Si tous les binaires partagent exactement le même template, documente la règle qui permet de calculer l’adresse au lieu de copier 100 constantes.

## 22.10 Workflow recommandé pour 100 binaires

1. Faire l’inventaire.
2. Exécuter une entrée de test avec timeout.
3. Extraire chaînes et imports.
4. Grouper par famille.
5. Choisir un représentant de chaque famille.
6. Résoudre manuellement le représentant.
7. Écrire un solveur générique pour la famille.
8. Vérifier sur 2 ou 3 autres binaires.
9. Lancer sur toute la famille.
10. Valider chaque résultat avec son binaire.
11. Journaliser succès et échecs.
12. Reprendre manuellement les exceptions.

## 22.11 Validation automatique

Ton script doit distinguer :

- flag trouvé ;
- sortie de succès confirmée ;
- crash ;
- timeout ;
- résultat ambigu ;
- binaire incompatible.

Ne considère jamais « le solveur a produit une chaîne » comme une validation suffisante.

---

# 23. Automatiser une analyse sans perdre la compréhension

## 23.1 Ce qui doit rester manuel au début

- identifier l’entrée ;
- reconnaître la fonction de validation ;
- comprendre les tailles et signes ;
- choisir le moment du dump ;
- identifier la branche de succès ;
- vérifier le bypass anti-debug.

## 23.2 Ce qui peut être automatisé

- hashes ;
- type de fichier ;
- imports ;
- chaînes ;
- exécution avec timeout ;
- lancement de GDB batch ;
- extraction de tables ;
- génération des contraintes ;
- validation de plusieurs candidats ;
- création des journaux.

## 23.3 Qualités d’un bon script d’analyse

- utilise `pathlib` ;
- accepte des arguments ;
- ne suppose pas le dossier courant sans l’indiquer ;
- gère les erreurs ;
- utilise un timeout ;
- ne lance pas de shell inutilement ;
- journalise les commandes et résultats ;
- ne modifie pas l’original ;
- produit un résultat reproductible ;
- se termine avec un code de retour cohérent.

## 23.4 Éviter `shell=True`

Préfère :

```python
subprocess.run(["file", "-b", str(path)], check=False)
```

À :

```python
subprocess.run(f"file {path}", shell=True)
```

La première version réduit les problèmes d’échappement et d’injection de commandes.

---

# 24. Documenter ses résultats

## 24.1 Structure de notes

Pour chaque binaire :

```markdown
# Binary: target

## Integrity
- SHA-256:
- Size:
- Architecture:

## Execution environment
- VM:
- OS:
- Network mode:
- Debugger:

## Baseline behavior
- Input:
- Output:
- Exit code:
- Duration:

## Static observations
- Interesting strings:
- Imported functions:
- Validation function:

## Dynamic observations
- Breakpoints:
- Registers:
- Memory regions:
- System calls:
- Network activity:

## Constraints or algorithm
- Input length:
- Transformations:
- Success condition:

## Bypass or extraction
- Technique:
- Exact location:
- Reason:

## Validation
- Candidate input:
- Program result:

## Conclusion
```

## 24.2 Observation contre interprétation

Mauvais :

> Le programme est malveillant.

Meilleur :

> ProcMon montre que le programme écrit un exécutable dans un répertoire temporaire puis le lance. Ce comportement est compatible avec un mécanisme de dropper, mais l’intention doit être confirmée par l’analyse du fichier créé.

## 24.3 Capturer les preuves

Conserve :

- commandes exactes ;
- sortie GDB ;
- adresses et offsets ;
- captures ProcMon ;
- fichier PCAP ;
- dumps mémoire ;
- hashes ;
- scripts ;
- version des outils ;
- date de l’analyse.

Versions :

```bash
gdb --version | head -n 1
python3 --version
sha256sum --version | head -n 1
```

## 24.4 Rapport de vulnérabilité

Si tu identifies un bug de sécurité, documente :

- titre ;
- résumé ;
- environnement ;
- préconditions ;
- étapes de reproduction ;
- comportement observé ;
- comportement attendu ;
- impact ;
- preuves ;
- cause racine ;
- recommandation ;
- limites de l’analyse.

---

# 25. Erreurs fréquentes et dépannage

## 25.1 `No debugging symbols found`

Ce n’est pas bloquant. Tu peux toujours :

- désassembler ;
- poser des breakpoints sur les imports ;
- utiliser des adresses ;
- examiner registres et mémoire.

## 25.2 Le breakpoint sur une fonction ne fonctionne pas

Causes :

- fonction non importée ;
- symbole absent ;
- fonction inlinée ;
- bibliothèque non encore chargée ;
- nom différent.

Solutions :

```gdb
info functions strcmp
set breakpoint pending on
break strcmp
```

## 25.3 Les adresses changent

Cause : PIE et ASLR.

Solutions :

- breakpoint par nom ;
- offset relatif ;
- `set disable-randomization on` dans le laboratoire ;
- récupérer la base avec `info proc mappings`.

## 25.4 GDB affiche une syntaxe différente

```gdb
set disassembly-flavor intel
```

## 25.5 L’entrée semble correcte mais échoue

Vérifie :

- retour à la ligne ;
- octet nul ;
- longueur ;
- encodage ;
- espace final ;
- signedness ;
- overflow ;
- transformation effectuée avant comparaison.

Affiche le fichier :

```bash
xxd -g 1 0-flag.txt
cat -A 0-flag.txt
```

## 25.6 Z3 renvoie `unsat`

Causes fréquentes :

- contrainte inversée ;
- mauvais opérateur signé/non signé ;
- taille incorrecte ;
- constante mal lue ;
- index décalé ;
- newline inclus ;
- addition modulaire mal représentée.

Ajoute les contraintes progressivement et compare à une exécution concrète.

## 25.7 Z3 prend trop de temps

- limite l’alphabet ;
- impose le format du flag ;
- simplifie les expressions ;
- découpe les contraintes ;
- remplace certaines parties par du calcul Python ;
- vérifie qu’une boucle n’est pas symboliquement non bornée.

## 25.8 Angr ne trouve rien

- vérifier `find` et `avoid` ;
- vérifier PIE et base address ;
- vérifier la longueur et le newline ;
- tester `entry_state` contre `full_init_state` ;
- désactiver les bibliothèques ;
- inspecter `manager.deadended`, `errored`, `active` ;
- chercher une technique anti-debugging ;
- vérifier si le code est modifié à l’exécution.

## 25.9 GDB s’arrête constamment dans le loader

Utilise :

```gdb
start
```

Puis pose les breakpoints applicatifs.

## 25.10 Le code désassemblé en mémoire semble faux

- le déchiffrement n’est peut-être pas terminé ;
- mauvaise adresse ;
- mauvaise architecture ;
- données mélangées au code ;
- zone compressée et non simplement chiffrée ;
- saut vers un offset au milieu de la zone.

Place le breakpoint juste avant le premier saut vers la région.

## 25.11 Le programme détecte GDB après un bypass `ptrace`

Il peut combiner plusieurs techniques :

- `TracerPid` ;
- timing ;
- signaux ;
- parent process ;
- checksum ;
- vérification de `/proc/self/maps`.

Compare systématiquement le flot normal et le flot sous debugger.

---

# 26. Cheat sheets

## 26.1 GDB essentiel

```gdb
file ./target
set disassembly-flavor intel
set pagination off
start
starti
run
run < input.txt
continue
stepi
nexti
finish
break main
break strcmp
break *0x401234
tbreak *main+40
hbreak *0x401234
info breakpoints
delete 1
disable 1
enable 1
info registers
p/x $rax
x/20i $rip
x/16gx $rsp
x/s $rdi
backtrace
info proc mappings
catch syscall openat
catch syscall mprotect
dump binary memory dumps/region.bin START END
set $rax = 0
set $rip = ADDRESS
```

## 26.2 Arguments x86-64 Linux

| Position | Registre |
|---|---|
| 1 | RDI |
| 2 | RSI |
| 3 | RDX |
| 4 | RCX |
| 5 | R8 |
| 6 | R9 |
| Retour | RAX |

Pour les syscalls, le quatrième argument est dans `R10`, pas `RCX`.

## 26.3 Comparaisons assembleur

| Instruction | Sens |
|---|---|
| `je` / `jz` | égal / zéro |
| `jne` / `jnz` | différent / non zéro |
| `jg` | supérieur signé |
| `jge` | supérieur ou égal signé |
| `jl` | inférieur signé |
| `jle` | inférieur ou égal signé |
| `ja` | supérieur non signé |
| `jae` | supérieur ou égal non signé |
| `jb` | inférieur non signé |
| `jbe` | inférieur ou égal non signé |

## 26.4 Z3 essentiel

```python
from z3 import (
    BitVec,
    Solver,
    UGE,
    ULE,
    RotateLeft,
    RotateRight,
    ZeroExt,
    sat,
)

chars = [BitVec(f"c{i}", 8) for i in range(length)]
solver = Solver()

for char in chars:
    solver.add(UGE(char, 0x20), ULE(char, 0x7E))

solver.add(chars[0] == ord("f"))
solver.add((chars[1] ^ 0x42) == 0x13)
solver.add(RotateLeft(chars[2], 3) == 0x91)

if solver.check() == sat:
    model = solver.model()
    result = bytes(model.eval(c).as_long() for c in chars)
    print(result)
```

## 26.5 Analyse initiale

```bash
sha256sum ./target
file ./target
readelf -h ./target
readelf -S ./target
readelf -l ./target
strings -a -n 4 ./target | less
strings -tx ./target | less
nm -n ./target 2>/dev/null | less
nm -D ./target 2>/dev/null | less
objdump -d -M intel ./target | less
objdump -s -j .rodata ./target | less
```

## 26.6 Valgrind

```bash
valgrind \
  --leak-check=full \
  --track-origins=yes \
  --log-file=valgrind.log \
  ./target < input.txt
```

## 26.7 Wireshark

```text
dns
http
tls
tcp.port == 443
ip.addr == 192.168.56.10
dns.qry.name contains "example"
tcp.stream eq 0
```

---

# 27. Questions de révision

## Questions fondamentales

1. Quelle est la différence principale entre analyse statique et dynamique ?
2. Pourquoi un code packé est-il difficile à analyser statiquement ?
3. Pourquoi faut-il utiliser une VM et un snapshot ?
4. Que contient `RIP` ?
5. Quels registres contiennent les trois premiers arguments d’une fonction sous Linux x86-64 ?
6. Quelle différence existe entre `stepi` et `nexti` ?
7. À quoi sert un watchpoint ?
8. Comment observer les arguments de `strcmp` ?
9. Pourquoi un dump mémoire doit-il être effectué au bon moment ?
10. Quelle différence existe entre SAT et SMT ?

## Questions SAT/SMT

11. Pourquoi `BitVec(8)` est-il souvent préférable à `Int` pour un caractère ?
12. Que signifie `unsat` ?
13. Pourquoi limiter les caractères à l’ASCII imprimable ?
14. Quelle différence existe entre `jg` et `ja` ?
15. À quoi sert `ZeroExt` ?
16. Pourquoi une solution Z3 doit-elle être testée sur le vrai binaire ?
17. Quand Angr est-il préférable à une transcription manuelle ?
18. Qu’est-ce que l’explosion de chemins ?
19. Quel est le rôle des adresses `find` et `avoid` ?
20. Pourquoi `auto_load_libs=False` peut-il accélérer Angr ?

## Questions anti-debugging

21. Comment `ptrace(PTRACE_TRACEME)` détecte-t-il un debugger ?
22. Que représente `TracerPid` ?
23. Comment une mesure de temps peut-elle détecter du stepping ?
24. Pourquoi un breakpoint logiciel peut-il être détecté ?
25. Quelle différence existe entre neutraliser une détection et résoudre la validation du flag ?

## Questions self-modifying code

26. Pourquoi un programme appelle-t-il `mprotect` avant de modifier son code ?
27. Que signifient les permissions `RWX` ?
28. Pourquoi le code sur disque peut-il être différent du code exécuté ?
29. Quel est le meilleur moment pour dumper une région déchiffrée ?
30. Comment repérer un saut vers du code généré dynamiquement ?

## Questions professionnelles

31. Quelle différence existe entre une observation et une interprétation ?
32. Pourquoi conserver les hashes et versions des outils ?
33. Pourquoi faut-il démarrer le monitoring avant le binaire ?
34. Pourquoi les chemins absolus rendent-ils les scripts moins portables ?
35. Pourquoi automatiser par familles pour 100 binaires ?

---

# 28. Glossaire

**Address Space Layout Randomization — ASLR**  
Randomisation des adresses mémoire afin de compliquer les attaques et de rendre les adresses moins prévisibles.

**Breakpoint**  
Point d’arrêt qui suspend l’exécution à un emplacement précis.

**Call stack**  
Pile des appels de fonctions actifs.

**Concrete execution**  
Exécution avec des valeurs réelles et déterminées.

**Constraint**  
Condition logique que les variables doivent satisfaire.

**Control flow**  
Ordre dans lequel les blocs et instructions sont exécutés.

**Debugger**  
Outil permettant d’arrêter, avancer et inspecter un programme pendant son exécution.

**Dump mémoire**  
Copie d’une plage mémoire dans un fichier.

**Dynamic analysis**  
Étude d’un programme pendant son exécution.

**ELF**  
Format courant des exécutables et bibliothèques sous Linux.

**Execution path**  
Suite des branches suivies pendant une exécution.

**Hook**  
Remplacement ou interception d’une fonction ou instruction pour observer ou modifier son comportement.

**Instrumentation**  
Ajout de logique d’observation pendant l’exécution.

**Mapping mémoire**  
Région de mémoire associée à du code, des données, une bibliothèque, une pile ou une allocation.

**Model**  
Attribution de valeurs aux variables qui satisfait les contraintes d’un solveur.

**Packé**  
Programme dont le code original est compressé, chiffré ou transformé, puis restauré au runtime.

**PIE**  
Executable indépendant de sa position, chargeable à différentes adresses.

**Register**  
Petite zone de stockage très rapide dans le processeur.

**SAT**  
Problème consistant à déterminer si une formule booléenne peut être satisfaite.

**Self-modifying code**  
Code qui modifie ses propres instructions pendant l’exécution.

**Signedness**  
Interprétation signée ou non signée d’une valeur binaire.

**SMT**  
Extension de SAT prenant en charge des théories comme les entiers et bit-vectors.

**Static analysis**  
Étude d’un programme sans l’exécuter.

**Symbolic execution**  
Exécution avec des variables symboliques plutôt que des valeurs concrètes.

**System call**  
Demande directe d’un programme au noyau du système d’exploitation.

**Watchpoint**  
Point d’arrêt déclenché par un accès ou une modification mémoire.

---

# Conclusion

L’analyse dynamique ne consiste pas à avancer instruction par instruction jusqu’à ce qu’un flag apparaisse. Elle consiste à construire une expérience contrôlée :

1. comprendre le binaire statiquement ;
2. formuler une hypothèse ;
3. choisir le bon événement à observer ;
4. placer un breakpoint ou un outil de monitoring ;
5. collecter les valeurs pertinentes ;
6. reconstruire la logique ;
7. automatiser ce qui est répétitif ;
8. valider le résultat sur le vrai programme ;
9. conserver des preuves reproductibles.

Pour ce projet, les compétences centrales sont :

- utiliser GDB sans dépendre d’une interface graphique ;
- comprendre registres, mémoire, pile et branches ;
- observer les interactions avec le système ;
- reconnaître et bypasser une protection anti-debugging en laboratoire ;
- récupérer du code ou des données modifiés à l’exécution ;
- traduire des calculs assembleur en contraintes Z3 ;
- utiliser Angr lorsque le nombre de chemins rend l’analyse manuelle inefficace ;
- automatiser une grande série de binaires par familles ;
- documenter clairement les observations et conclusions.

La règle la plus importante est la suivante :

> Le debugger ne remplace pas la compréhension. Il te donne les preuves nécessaires pour vérifier ta compréhension.
