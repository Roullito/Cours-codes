# Static Analysis & Reverse Engineering — Cours ultra complet

> **Projet Holberton :** `holbertonschool-reverse_engineering/static_analysis`  
> **Cible principale :** `target_binary` / `target-binary`  
> **Environnement recommandé :** Kali Linux dans une machine virtuelle isolée  
> **Langue :** français, avec le vocabulaire technique anglais utilisé dans les outils

---

## Table des matières

1. [Introduction générale](#1-introduction-générale)
2. [Static analysis, dynamic analysis et reverse engineering](#2-static-analysis-dynamic-analysis-et-reverse-engineering)
3. [Pourquoi l’analyse statique est indispensable](#3-pourquoi-lanalyse-statique-est-indispensable)
4. [Environnement de travail sécurisé](#4-environnement-de-travail-sécurisé)
5. [Validation et préservation du binaire](#5-validation-et-préservation-du-binaire)
6. [Anatomie générale d’un binaire](#6-anatomie-générale-dun-binaire)
7. [Formats exécutables : ELF, PE et Mach-O](#7-formats-exécutables--elf-pe-et-mach-o)
8. [Workflow complet d’analyse statique](#8-workflow-complet-danalyse-statique)
9. [Analyse des chaînes de caractères](#9-analyse-des-chaînes-de-caractères)
10. [Analyse des en-têtes et des sections](#10-analyse-des-en-têtes-et-des-sections)
11. [Symboles, imports, exports et bibliothèques](#11-symboles-imports-exports-et-bibliothèques)
12. [Disassembly : comprendre le code machine](#12-disassembly--comprendre-le-code-machine)
13. [Decompilation : reconstruire une logique de haut niveau](#13-decompilation--reconstruire-une-logique-de-haut-niveau)
14. [Bases x86 et x86-64 pour le reverse engineering](#14-bases-x86-et-x86-64-pour-le-reverse-engineering)
15. [Stack, heap, registres et conventions d’appel](#15-stack-heap-registres-et-conventions-dappel)
16. [Control Flow Graphs](#16-control-flow-graphs)
17. [Cross-references](#17-cross-references)
18. [Reconnaissance de motifs et signatures](#18-reconnaissance-de-motifs-et-signatures)
19. [Recherche de vulnérabilités dans un binaire](#19-recherche-de-vulnérabilités-dans-un-binaire)
20. [Mathématiques utiles : arithmétique modulaire](#20-mathématiques-utiles--arithmétique-modulaire)
21. [Exponentiation by squaring](#21-exponentiation-by-squaring)
22. [Cryptographie et transformations fréquentes](#22-cryptographie-et-transformations-fréquentes)
23. [Obfuscation et anti-reverse engineering](#23-obfuscation-et-anti-reverse-engineering)
24. [Présentation des outils](#24-présentation-des-outils)
25. [Ghidra : guide pratique](#25-ghidra--guide-pratique)
26. [Radare2 : guide pratique](#26-radare2--guide-pratique)
27. [IDA Pro : guide pratique](#27-ida-pro--guide-pratique)
28. [Objdump, readelf, nm et autres outils CLI](#28-objdump-readelf-nm-et-autres-outils-cli)
29. [GDB pour confirmer une analyse](#29-gdb-pour-confirmer-une-analyse)
30. [Task 0 — Extracting and Analyzing Strings](#30-task-0--extracting-and-analyzing-strings)
31. [Task 1 — Static Analysis of a Security-Critical C Program](#31-task-1--static-analysis-of-a-security-critical-c-program)
32. [Task 2 — Optimizing a Decryption Algorithm](#32-task-2--optimizing-a-decryption-algorithm)
33. [Task 3 — Reverse Engineering an Obfuscated Flag](#33-task-3--reverse-engineering-an-obfuscated-flag)
34. [Task 4 — Understanding Raw Assembly Code](#34-task-4--understanding-raw-assembly-code)
35. [Scripts Kali Linux réutilisables](#35-scripts-kali-linux-réutilisables)
36. [Modèle de rapport d’analyse](#36-modèle-de-rapport-danalyse)
37. [Cheat sheet](#37-cheat-sheet)
38. [Erreurs fréquentes](#38-erreurs-fréquentes)
39. [Questions d’entretien et de soutenance](#39-questions-dentretien-et-de-soutenance)
40. [Glossaire](#40-glossaire)
41. [Checklist finale du projet](#41-checklist-finale-du-projet)

---

# 1. Introduction générale

> “You cannot defend what you do not understand. Static analysis is the art of understanding a program without ever running it.”

L’analyse statique consiste à étudier un programme **sans exécuter directement son code**. Le programme est traité comme un ensemble structuré de données : en-têtes, sections, instructions machine, chaînes de caractères, symboles, bibliothèques importées, tables de relocation et métadonnées.

Un exécutable inconnu n’est pas une boîte magique. C’est un fichier binaire respectant généralement un format documenté. Le compilateur et le linker y laissent de nombreux indices :

- le format du fichier ;
- l’architecture ciblée ;
- le point d’entrée ;
- les sections contenant le code et les données ;
- les fonctions importées ;
- les chaînes de caractères ;
- les constantes ;
- la structure du contrôle de flux ;
- parfois les noms de fonctions ou de variables ;
- les mécanismes de protection activés ;
- les comportements probables du programme.

Le travail d’un reverse engineer est de transformer ces indices en une représentation compréhensible du fonctionnement du programme.

## Objectif du module

À la fin de ce cours, tu dois être capable de :

- définir précisément l’analyse statique ;
- distinguer analyse statique, analyse dynamique et reverse engineering ;
- identifier le format et l’architecture d’un binaire ;
- examiner ses chaînes, sections, imports et symboles ;
- lire du désassemblage x86/x64 ;
- utiliser un décompilateur sans lui faire aveuglément confiance ;
- reconstruire le contrôle de flux d’une fonction ;
- utiliser les cross-references pour naviguer dans un programme ;
- reconnaître des motifs de vulnérabilités ;
- analyser des transformations mathématiques ou cryptographiques simples ;
- produire une documentation claire et reproductible.

---

# 2. Static analysis, dynamic analysis et reverse engineering

## 2.1 Analyse statique

L’analyse statique étudie un fichier sans laisser son comportement normal s’exécuter.

Exemples :

```bash
file target_binary
sha256sum target_binary
strings target_binary
readelf -h target_binary
objdump -d target_binary
r2 -A target_binary
```

Elle permet de répondre à des questions comme :

- Quel est le format du fichier ?
- Quelle architecture utilise-t-il ?
- Quelles bibliothèques charge-t-il ?
- Existe-t-il une chaîne ressemblant à un mot de passe ?
- Quelle fonction compare l’entrée utilisateur ?
- Le programme appelle-t-il `strcpy`, `system`, `execve` ou `memcmp` ?
- Quelle logique mène au message “Access granted” ?

## 2.2 Analyse dynamique

L’analyse dynamique observe un programme pendant son exécution.

Outils typiques :

- GDB ;
- x64dbg ;
- strace ;
- ltrace ;
- Process Monitor ;
- sandbox ;
- debugger intégré à Ghidra ou IDA.

Exemples de questions :

- Quelle valeur est contenue dans `RAX` à cette instruction ?
- Quel fichier est réellement ouvert ?
- Quelle clé est produite après déchiffrement ?
- Quel chemin est pris lorsque l’utilisateur entre une valeur précise ?

## 2.3 Reverse engineering

Le reverse engineering est la discipline générale consistant à reconstruire le fonctionnement interne d’un programme à partir du produit compilé.

Il peut combiner :

- analyse statique ;
- analyse dynamique ;
- instrumentation ;
- comparaison de versions ;
- reconstruction d’algorithmes ;
- émulation ;
- analyse de protocoles ;
- extraction de données.

## 2.4 Différence fondamentale

| Approche | Le programme s’exécute ? | Avantages | Limites |
|---|---:|---|---|
| Analyse statique | Non | Sûre, reproductible, vision globale | Obfuscation, code indirect, informations runtime absentes |
| Analyse dynamique | Oui | Valeurs réelles, comportement observable | Risque, couverture partielle, anti-debug |
| Reverse engineering | Pas nécessairement | Combine toutes les techniques | Demande du temps et de l’expérience |

---

# 3. Pourquoi l’analyse statique est indispensable

## 3.1 Malware analysis

Exécuter immédiatement un binaire inconnu peut :

- chiffrer les fichiers ;
- établir une persistance ;
- voler des identifiants ;
- contacter un serveur de commande et contrôle ;
- exploiter une vulnérabilité ;
- se propager sur le réseau ;
- détecter l’environnement d’analyse ;
- effacer des preuves.

L’analyse statique permet une première évaluation du risque avant toute exécution.

On peut rechercher :

- domaines et adresses IP ;
- noms de mutex ;
- chemins de fichiers ;
- clés de registre ;
- fonctions réseau ;
- fonctions de chiffrement ;
- commandes shell ;
- mécanismes de persistance ;
- techniques d’évasion.

## 3.2 Security auditing

Lors d’un audit, le code source peut être indisponible. Il peut également différer du binaire réellement déployé.

L’analyse statique permet de vérifier :

- les fonctions dangereuses utilisées ;
- la présence de protections de compilation ;
- l’exposition de secrets ;
- les vérifications d’authentification ;
- les contrôles de taille ;
- la validation des entrées ;
- les erreurs de logique ;
- les dépendances vulnérables ;
- les comportements non documentés.

## 3.3 Software debugging

Même avec le code source, le comportement compilé peut différer de l’intention du développeur à cause :

- des optimisations ;
- d’un comportement indéfini en C/C++ ;
- de l’inlining ;
- de la suppression de fonctions ;
- d’erreurs de compilation ;
- d’un mauvais usage de types signés/non signés ;
- d’un overflow entier.

Le désassemblage montre ce que le processeur exécutera réellement.

## 3.4 CTF et challenges académiques

Dans un CTF, le flag est souvent :

- présent en clair dans `.rodata` ;
- reconstruit caractère par caractère ;
- XORé avec une clé ;
- comparé à l’entrée utilisateur ;
- généré par une fonction mathématique ;
- caché derrière une branche conditionnelle ;
- encodé en Base64 ou hexadécimal ;
- stocké dans une ressource ;
- déchiffré seulement à l’exécution.

L’objectif est rarement de “deviner”. Il faut reconstruire la logique.

---

# 4. Environnement de travail sécurisé

## 4.1 Utiliser une VM dédiée

Une configuration minimale recommandée :

- Kali Linux à jour ;
- réseau désactivé ou limité si le fichier est inconnu ;
- snapshot avant analyse ;
- dossier partagé désactivé pour un malware réel ;
- presse-papiers bidirectionnel désactivé pour les échantillons dangereux ;
- aucun compte personnel connecté ;
- aucun secret présent dans la VM.

## 4.2 Structure de dossier recommandée

```text
static_analysis/
├── samples/
│   └── target_binary
├── hashes/
│   └── target_binary.sha256
├── output/
│   ├── strings.txt
│   ├── headers.txt
│   ├── sections.txt
│   ├── imports.txt
│   └── disassembly.txt
├── scripts/
│   ├── triage.sh
│   └── extract_strings.sh
├── notes/
│   └── analysis.md
└── README.md
```

## 4.3 Travailler sur une copie

Ne modifie pas l’original.

```bash
mkdir -p samples working output hashes notes scripts
cp target_binary samples/
cp samples/target_binary working/target_binary.analysis
chmod -x working/target_binary.analysis
```

Retirer le bit d’exécution ne garantit pas qu’un fichier ne sera jamais lancé, mais réduit le risque d’une exécution accidentelle.

## 4.4 Ne pas utiliser de chemins codés en dur

Mauvais :

```bash
strings /home/roullito/project/target_binary
```

Meilleur :

```bash
strings ./target_binary
```

Encore mieux dans un script :

```bash
#!/usr/bin/env bash
set -euo pipefail

binary="${1:-./target_binary}"
strings "$binary"
```

---

# 5. Validation et préservation du binaire

## 5.1 Pourquoi calculer un hash ?

Un hash cryptographique sert à vérifier que le fichier analysé reste identique.

```bash
sha256sum target_binary
```

Exemple de sortie :

```text
8c7f...d1a2  target_binary
```

Enregistrer le résultat :

```bash
sha256sum target_binary > hashes/target_binary.sha256
```

Vérifier plus tard :

```bash
sha256sum -c hashes/target_binary.sha256
```

## 5.2 Autres hashes

```bash
md5sum target_binary
sha1sum target_binary
sha256sum target_binary
sha512sum target_binary
```

Pour l’intégrité, SHA-256 est généralement suffisant. MD5 et SHA-1 restent utiles comme identifiants dans certains contextes, mais ne doivent plus être considérés comme résistants aux collisions.

## 5.3 Métadonnées du fichier

```bash
stat target_binary
ls -l target_binary
file target_binary
```

Points à documenter :

- nom ;
- taille ;
- type ;
- architecture ;
- hash SHA-256 ;
- date d’obtention ;
- source ;
- permissions ;
- analyste ;
- environnement.

---

# 6. Anatomie générale d’un binaire

Un exécutable contient généralement :

1. **Un header principal** : décrit le format, l’architecture et le point d’entrée.
2. **Des sections** : code, données, constantes, symboles, relocations.
3. **Des tables d’import/export** : fonctions externes nécessaires.
4. **Des instructions machine** : exécutées par le processeur.
5. **Des données** : chaînes, nombres, tableaux, clés.
6. **Des métadonnées** : symboles, debug, informations du compilateur.

## 6.1 Sections ELF courantes

| Section | Rôle |
|---|---|
| `.text` | Code exécutable |
| `.data` | Données globales initialisées et modifiables |
| `.bss` | Données globales non initialisées ou initialisées à zéro |
| `.rodata` | Données en lecture seule, souvent les chaînes |
| `.plt` | Stubs d’appels de fonctions externes |
| `.got` / `.got.plt` | Adresses résolues dynamiquement |
| `.symtab` | Table complète des symboles, souvent retirée dans un binaire stripped |
| `.dynsym` | Symboles nécessaires au chargement dynamique |
| `.strtab` | Chaînes associées aux symboles |
| `.dynstr` | Chaînes associées aux symboles dynamiques |
| `.rela.*` / `.rel.*` | Informations de relocation |
| `.init` / `.fini` | Code exécuté lors de l’initialisation ou de la terminaison |
| `.init_array` | Liste de fonctions appelées avant `main` |

## 6.2 Segments et sections

Une section est principalement une vue destinée au linker et aux outils d’analyse.

Un segment est une vue destinée au loader du système d’exploitation.

Pour voir les sections :

```bash
readelf -S target_binary
```

Pour voir les segments :

```bash
readelf -l target_binary
```

La correspondance sections/segments est visible en bas de `readelf -l`.

---

# 7. Formats exécutables : ELF, PE et Mach-O

## 7.1 ELF — Linux et systèmes Unix

ELF signifie **Executable and Linkable Format**.

Il est utilisé pour :

- exécutables ;
- bibliothèques partagées `.so` ;
- fichiers objets `.o` ;
- core dumps.

### Éléments importants

- ELF Header ;
- Program Header Table ;
- Section Header Table ;
- sections `.text`, `.data`, `.rodata`, etc. ;
- symboles ;
- relocations ;
- Dynamic Section.

Commandes :

```bash
file target_binary
readelf -h target_binary
readelf -S target_binary
readelf -l target_binary
readelf -d target_binary
readelf -s target_binary
```

## 7.2 PE — Windows

PE signifie **Portable Executable**.

Il est utilisé pour :

- `.exe` ;
- `.dll` ;
- `.sys` ;
- `.scr` ;
- certains objets Windows.

### Éléments importants

- en-tête DOS, commençant généralement par `MZ` ;
- champ pointant vers l’en-tête PE ;
- signature `PE\0\0` ;
- COFF Header ;
- Optional Header ;
- Data Directories ;
- Section Table ;
- Import Address Table ;
- Export Table ;
- ressources ;
- relocations ;
- TLS callbacks.

### Sections PE courantes

| Section | Rôle |
|---|---|
| `.text` | Code |
| `.rdata` | Données en lecture seule, imports |
| `.data` | Données modifiables |
| `.rsrc` | Ressources |
| `.reloc` | Relocations |
| `.pdata` | Informations d’exception x64 |

Outils locaux possibles :

```bash
file sample.exe
objdump -x sample.exe
rabin2 -I sample.exe
rabin2 -i sample.exe
rabin2 -S sample.exe
```

## 7.3 Mach-O — macOS et iOS

Mach-O signifie **Mach Object**.

Il est utilisé pour :

- exécutables macOS ;
- bibliothèques ;
- frameworks ;
- fichiers objets ;
- binaires universels ou “fat binaries”.

Éléments :

- Mach Header ;
- Load Commands ;
- segments `__TEXT`, `__DATA`, etc. ;
- sections `__text`, `__cstring`, etc. ;
- symbol table ;
- informations de signature de code.

## 7.4 Comparaison synthétique

| Élément | ELF | PE | Mach-O |
|---|---|---|---|
| Plateforme principale | Linux/Unix | Windows | macOS/iOS |
| Magic typique | `7f 45 4c 46` | `4d 5a` puis `PE` | Plusieurs magics possibles |
| Code | `.text` | `.text` | `__TEXT,__text` |
| Constantes | `.rodata` | `.rdata` | `__TEXT,__const` ou `__cstring` |
| Imports | Dynamic section, GOT/PLT | Import Table/IAT | Load Commands, symbol stubs |
| Ressources | Pas de format unique central | `.rsrc` | Bundles/sections spécifiques |
| Loader | Linux dynamic linker | Windows loader | dyld |

## 7.5 Pourquoi connaître le format ?

Le format détermine :

- comment trouver le point d’entrée ;
- comment localiser le code ;
- comment interpréter les adresses ;
- comment retrouver les imports ;
- quelles protections sont possibles ;
- quels outils et commandes utiliser.

---

# 8. Workflow complet d’analyse statique

Un bon analyste évite de commencer directement par le désassemblage complet. Il avance du plus général vers le plus précis.

## Phase 1 — Préservation

```bash
sha256sum target_binary
cp target_binary target_binary.analysis
chmod -x target_binary.analysis
```

## Phase 2 — Identification

```bash
file target_binary.analysis
xxd -l 64 target_binary.analysis
```

Questions :

- ELF, PE, Mach-O, archive ou données ?
- 32 ou 64 bits ?
- little-endian ou big-endian ?
- architecture x86, ARM, MIPS ?
- stripped ou non ?
- lié dynamiquement ou statiquement ?

## Phase 3 — Triage rapide

```bash
strings -a -n 4 target_binary.analysis | less
strings -a -t x target_binary.analysis > output/strings.txt
```

Puis :

```bash
readelf -h target_binary.analysis
readelf -S target_binary.analysis
readelf -l target_binary.analysis
```

## Phase 4 — Imports et protections

```bash
readelf -d target_binary.analysis
objdump -T target_binary.analysis
checksec --file=target_binary.analysis
```

## Phase 5 — Désassemblage ciblé

```bash
objdump -d -M intel target_binary.analysis | less
objdump -d -M intel target_binary.analysis > output/disassembly.txt
```

Si les symboles existent :

```bash
objdump -d -M intel target_binary.analysis | sed -n '/<main>:/,/^$/p'
```

## Phase 6 — Analyse dans Ghidra ou Radare2

- identifier `main` ;
- suivre les chaînes importantes ;
- afficher les xrefs ;
- renommer les fonctions et variables ;
- reconstruire les branches ;
- documenter les hypothèses ;
- comparer désassemblage et décompilation.

## Phase 7 — Reconstruction

Écrire en pseudo-code ce que fait le programme.

Exemple :

```c
read_input(buffer);
transform(buffer);
if (memcmp(buffer, expected, 16) == 0) {
    print_success();
} else {
    print_failure();
}
```

## Phase 8 — Validation contrôlée

Si autorisé et nécessaire :

- confirmer avec GDB ;
- tracer une valeur ;
- poser un breakpoint ;
- exécuter uniquement dans la VM ;
- ne jamais utiliser un environnement de production.

## Phase 9 — Documentation

Le rapport doit distinguer :

- faits observés ;
- hypothèses ;
- interprétations ;
- éléments non confirmés ;
- commandes utilisées ;
- preuves.

---

# 9. Analyse des chaînes de caractères

## 9.1 Qu’est-ce qu’une string dans un binaire ?

Une chaîne de caractères est une suite d’octets interprétable comme du texte.

Exemples :

- messages d’erreur ;
- chemins ;
- URL ;
- adresses IP ;
- noms de fichiers ;
- commandes ;
- credentials ;
- formats `printf` ;
- clés ;
- fragments de flag ;
- noms de fonctions ;
- informations de compilation.

## 9.2 Commande de base

```bash
strings target_binary
```

Par défaut, `strings` affiche généralement les suites d’au moins quatre caractères imprimables.

## 9.3 Scanner tout le fichier

```bash
strings -a target_binary
```

`-a` demande de scanner l’ensemble du fichier plutôt que certaines zones seulement.

## 9.4 Modifier la longueur minimale

```bash
strings -a -n 6 target_binary
```

Plus la valeur est faible, plus il y aura de bruit.

## 9.5 Afficher les offsets

```bash
strings -a -t x target_binary
```

Exemple :

```text
   2048 Access granted
   2057 Access denied
```

L’offset permet de retrouver la chaîne dans un hex editor ou un outil de reverse engineering.

Formats d’offset :

```bash
strings -t d target_binary   # décimal
strings -t x target_binary   # hexadécimal
strings -t o target_binary   # octal
```

## 9.6 Rechercher des motifs intéressants

```bash
strings -a target_binary | grep -iE 'flag|pass|secret|token|key|admin|root'
```

Réseau :

```bash
strings -a target_binary | grep -iE 'https?://|ftp://|[0-9]{1,3}(\.[0-9]{1,3}){3}'
```

Fichiers et commandes :

```bash
strings -a target_binary | grep -iE '/etc/|/tmp/|/home/|cmd\.exe|powershell|/bin/sh|bash'
```

Fonctions ou messages liés à la sécurité :

```bash
strings -a target_binary | grep -iE 'login|auth|decrypt|encrypt|compare|denied|granted|invalid|success|failed'
```

## 9.7 Chaînes UTF-16

Les binaires Windows contiennent souvent des chaînes UTF-16 little-endian.

```bash
strings -a -e l sample.exe
```

Options d’encodage utiles selon la version :

- `-e s` : single-byte ;
- `-e l` : 16-bit little-endian ;
- `-e b` : 16-bit big-endian ;
- `-e L` : 32-bit little-endian ;
- `-e B` : 32-bit big-endian.

## 9.8 Limites de `strings`

`strings` ne trouvera pas directement :

- une chaîne XORée ;
- une chaîne compressée ;
- une chaîne chiffrée ;
- une chaîne construite sur la stack ;
- une chaîne générée caractère par caractère ;
- une chaîne stockée sous forme d’entiers ;
- une chaîne encodée dans une ressource inhabituelle.

Exemple de construction cachée :

```c
char flag[5];
flag[0] = 'F';
flag[1] = 'L';
flag[2] = 'A';
flag[3] = 'G';
flag[4] = '\0';
```

Le mot `FLAG` peut ne pas apparaître comme une chaîne continue dans le fichier.

## 9.9 Comment interpréter les résultats

Une chaîne seule ne prouve pas un comportement.

La présence de `/bin/sh` ne prouve pas que le programme l’exécute. Il faut trouver :

- où la chaîne est référencée ;
- dans quelle fonction ;
- sous quelles conditions ;
- à quel appel elle est transmise.

La méthode correcte est :

```text
string intéressante
        ↓
cross-reference
        ↓
fonction utilisatrice
        ↓
contrôle de flux
        ↓
comportement confirmé ou infirmé
```

---

# 10. Analyse des en-têtes et des sections

## 10.1 Identification initiale

```bash
file target_binary
```

Exemple :

```text
ELF 64-bit LSB pie executable, x86-64, dynamically linked, stripped
```

Interprétation :

- `ELF` : format Linux/Unix ;
- `64-bit` : adresses et registres 64 bits ;
- `LSB` : little-endian ;
- `PIE` : position-independent executable ;
- `x86-64` : architecture AMD64 ;
- `dynamically linked` : bibliothèques partagées ;
- `stripped` : symboles de debug retirés.

## 10.2 ELF header

```bash
readelf -h target_binary
```

Champs importants :

- Magic ;
- Class ;
- Data ;
- OS/ABI ;
- Type ;
- Machine ;
- Entry point address ;
- Start of program headers ;
- Start of section headers ;
- Number of sections.

## 10.3 Point d’entrée

Le point d’entrée n’est pas toujours `main`.

Sur Linux, l’exécution commence souvent dans `_start`, fourni par le runtime C. `_start` prépare l’environnement puis appelle `__libc_start_main`, auquel l’adresse de `main` est transmise.

## 10.4 Sections

```bash
readelf -SW target_binary
```

`-W` évite la troncature des lignes.

Points suspects :

- section exécutable et modifiable (`WAX`) ;
- noms inhabituels ;
- section très grande ;
- entropie élevée ;
- point d’entrée hors `.text` ;
- chevauchements ;
- tailles incohérentes ;
- absence de sections attendues ;
- contenu exécutable dans une section de données.

## 10.5 Dump d’une section

```bash
readelf -x .rodata target_binary
objdump -s -j .rodata target_binary
```

Pour `.data` :

```bash
objdump -s -j .data target_binary
```

## 10.6 Analyse hexadécimale

```bash
xxd target_binary | less
hexdump -C target_binary | less
```

Lire les 64 premiers octets :

```bash
xxd -l 64 target_binary
```

---

# 11. Symboles, imports, exports et bibliothèques

## 11.1 Symboles

Un symbole associe un nom à une adresse ou à un objet.

```bash
nm target_binary
nm -D target_binary
readelf -s target_binary
```

Types courants avec `nm` :

- `T` / `t` : symbole dans la section code ;
- `D` / `d` : données initialisées ;
- `B` / `b` : BSS ;
- `U` : symbole externe non défini dans le binaire ;
- `R` / `r` : données en lecture seule.

## 11.2 Binaire stripped

Un binaire stripped a perdu une grande partie de ses symboles non nécessaires à l’exécution.

Conséquences :

- les fonctions peuvent s’appeler `FUN_00101234` dans Ghidra ;
- `main` peut ne pas être nommé ;
- les variables locales n’ont plus de nom ;
- il faut reconstruire davantage de contexte.

Les imports dynamiques peuvent toutefois rester visibles.

## 11.3 Bibliothèques dynamiques

```bash
ldd target_binary
```

Attention : pour un binaire totalement inconnu, certains analystes évitent `ldd` car son comportement historique peut dépendre du loader. Dans un projet académique contrôlé, il est généralement acceptable, mais `readelf -d` ou `objdump -p` sont des alternatives purement statiques.

```bash
readelf -d target_binary | grep NEEDED
objdump -p target_binary | grep NEEDED
```

## 11.4 Imports révélateurs

| Import | Indice possible |
|---|---|
| `strcmp`, `strncmp`, `memcmp` | Comparaison de mot de passe, clé ou flag |
| `strcpy`, `strcat`, `gets` | Risque de buffer overflow |
| `sprintf` | Risque de débordement si taille non contrôlée |
| `printf` | Affichage ; format string si format contrôlé par l’utilisateur |
| `malloc`, `free` | Gestion dynamique de mémoire |
| `socket`, `connect`, `send`, `recv` | Communication réseau |
| `open`, `read`, `write` | Accès fichiers ou descripteurs |
| `system`, `popen`, `execve` | Exécution de commandes |
| `dlopen`, `dlsym` | Chargement dynamique, résolution indirecte |
| `ptrace` | Anti-debug ou debugging |
| `mprotect` | Changement de permissions mémoire, unpacking, JIT |
| `crypt`, `EVP_*`, `AES_*` | Cryptographie |

Un import est un indice, pas une preuve. Il faut suivre ses appels.

---

# 12. Disassembly : comprendre le code machine

## 12.1 Définition

Le désassemblage transforme les octets machine en instructions assembleur.

Exemple d’octets :

```text
48 89 e5
```

Interprétation x86-64 :

```asm
mov rbp, rsp
```

## 12.2 Objdump

```bash
objdump -d target_binary
```

Syntaxe Intel :

```bash
objdump -d -M intel target_binary
```

Désassembler toutes les sections possibles :

```bash
objdump -D -M intel target_binary
```

Différence :

- `-d` désassemble les sections attendues comme exécutables ;
- `-D` tente de désassembler davantage de contenu.

## 12.3 Syntaxe Intel et AT&T

### Intel

```asm
mov rax, rbx
```

Destination à gauche, source à droite.

### AT&T

```asm
mov %rbx, %rax
```

Source à gauche, destination à droite.

Dans ce cours, on privilégie la syntaxe Intel.

## 12.4 Limites du désassemblage

Un désassembleur peut se tromper lorsque :

- du code et des données sont mélangés ;
- le programme utilise des sauts indirects ;
- le binaire est obfusqué ;
- les instructions se chevauchent ;
- les sections sont volontairement incorrectes ;
- du code est généré à l’exécution ;
- le binaire est packé.

Le résultat doit toujours être interprété, pas seulement lu.

---

# 13. Decompilation : reconstruire une logique de haut niveau

## 13.1 Définition

La décompilation tente de convertir l’assembleur en pseudo-code ressemblant à du C.

Exemple assembleur :

```asm
cmp eax, 0
jne fail
lea rdi, [rip + success_message]
call puts
```

Pseudo-code possible :

```c
if (result == 0) {
    puts("Success");
} else {
    goto fail;
}
```

## 13.2 Différence avec le désassemblage

| Désassemblage | Décompilation |
|---|---|
| Représente les instructions CPU | Reconstruit une logique de haut niveau |
| Très fidèle aux opérations | Plus lisible mais approximatif |
| Montre registres et adresses | Invente variables et types |
| Plus difficile à lire | Plus rapide pour comprendre la logique |

## 13.3 Pourquoi un décompilateur peut se tromper

Le code compilé ne contient généralement plus :

- les noms originaux des variables locales ;
- les commentaires ;
- les structures de contrôle exactes ;
- les types complets ;
- les macros ;
- les noms de certains symboles ;
- la séparation exacte des fonctions après certaines optimisations.

Le décompilateur doit donc inférer.

Exemple :

```c
undefined8 FUN_00101210(long param_1)
```

Après analyse, l’analyste peut renommer :

```c
bool validate_password(const char *input)
```

## 13.4 Bon usage

1. Lire le pseudo-code.
2. Identifier les zones importantes.
3. Vérifier les comparaisons et tailles dans l’assembleur.
4. Corriger les types.
5. Renommer les fonctions.
6. Ajouter des commentaires.
7. Ne jamais présenter le pseudo-code comme le code source original.

---

# 14. Bases x86 et x86-64 pour le reverse engineering

## 14.1 Registres généraux x86-64

| 64 bits | 32 bits | 16 bits | 8 bits bas | Rôle fréquent |
|---|---|---|---|---|
| `RAX` | `EAX` | `AX` | `AL` | Valeur de retour, calculs |
| `RBX` | `EBX` | `BX` | `BL` | Registre conservé |
| `RCX` | `ECX` | `CX` | `CL` | Compteur, 4e argument Windows x64 |
| `RDX` | `EDX` | `DX` | `DL` | 3e argument SysV, 2e Windows |
| `RSI` | `ESI` | `SI` | `SIL` | 2e argument SysV, source |
| `RDI` | `EDI` | `DI` | `DIL` | 1er argument SysV, destination |
| `RBP` | `EBP` | `BP` | `BPL` | Base de stack frame |
| `RSP` | `ESP` | `SP` | `SPL` | Pointeur de pile |
| `R8`–`R15` | `R8D`–`R15D` | etc. | etc. | Arguments et temporaires |
| `RIP` | `EIP` | — | — | Instruction suivante |

Écrire dans un registre 32 bits met généralement à zéro les 32 bits supérieurs du registre 64 bits correspondant.

```asm
mov eax, 1
```

produit `RAX = 1`, pas seulement `EAX = 1`.

## 14.2 Instructions essentielles

### Déplacement

```asm
mov rax, rbx
lea rax, [rbp-0x20]
```

`lea` calcule une adresse ou une expression arithmétique, sans lire nécessairement la mémoire.

### Arithmétique

```asm
add eax, 5
sub eax, 2
imul eax, ecx
inc eax
dec eax
```

### Bitwise

```asm
xor eax, eax
and eax, 0xff
or eax, 1
shl eax, 3
shr eax, 1
rol eax, 4
ror eax, 4
```

`xor eax, eax` met `EAX` à zéro.

### Comparaison

```asm
cmp eax, ebx
test eax, eax
```

`cmp a, b` effectue conceptuellement `a - b` et met à jour les flags sans stocker le résultat.

`test eax, eax` vérifie notamment si `eax` vaut zéro.

### Contrôle de flux

```asm
jmp label
je label
jne label
jg label
jl label
ja label
jb label
call function
ret
```

## 14.3 Flags importants

| Flag | Signification |
|---|---|
| ZF | Zero Flag |
| CF | Carry Flag |
| SF | Sign Flag |
| OF | Overflow Flag |

## 14.4 Sauts signés et non signés

Après `cmp a, b` :

### Comparaisons signées

- `jg` : greater ;
- `jge` : greater or equal ;
- `jl` : less ;
- `jle` : less or equal.

### Comparaisons non signées

- `ja` : above ;
- `jae` : above or equal ;
- `jb` : below ;
- `jbe` : below or equal.

Confondre les deux peut mener à une mauvaise reconstruction du programme.

## 14.5 Accès mémoire

```asm
mov eax, DWORD PTR [rbp-0x4]
mov BYTE PTR [rax], 0x41
mov rdx, QWORD PTR [rdi+8]
```

Tailles :

- `BYTE PTR` : 1 octet ;
- `WORD PTR` : 2 octets ;
- `DWORD PTR` : 4 octets ;
- `QWORD PTR` : 8 octets.

---

# 15. Stack, heap, registres et conventions d’appel

## 15.1 La stack

La pile stocke notamment :

- adresses de retour ;
- variables locales ;
- registres sauvegardés ;
- arguments supplémentaires ;
- données temporaires.

Elle grandit généralement vers les adresses basses.

Prologue classique :

```asm
push rbp
mov rbp, rsp
sub rsp, 0x40
```

Épilogue :

```asm
leave
ret
```

ou :

```asm
mov rsp, rbp
pop rbp
ret
```

## 15.2 La heap

La heap est utilisée pour les allocations dynamiques.

```c
char *buffer = malloc(128);
free(buffer);
```

Imports associés :

- `malloc` ;
- `calloc` ;
- `realloc` ;
- `free` ;
- `new` / `delete` en C++.

## 15.3 Convention System V AMD64

Utilisée principalement sous Linux x86-64.

Arguments entiers/pointeurs :

1. `RDI`
2. `RSI`
3. `RDX`
4. `RCX`
5. `R8`
6. `R9`

Valeur de retour : `RAX`.

Exemple :

```c
memcmp(input, expected, 16);
```

Avant l’appel, on s’attend à :

```text
RDI = input
RSI = expected
RDX = 16
```

## 15.4 Convention Windows x64

Arguments entiers/pointeurs :

1. `RCX`
2. `RDX`
3. `R8`
4. `R9`

Valeur de retour : `RAX`.

## 15.5 Caller-saved et callee-saved

Certains registres peuvent être détruits par une fonction appelée. D’autres doivent être restaurés.

Sous System V AMD64, les registres typiquement conservés par la fonction appelée incluent :

- `RBX` ;
- `RBP` ;
- `R12` à `R15`.

Cette information aide à suivre les valeurs entre les appels.

---

# 16. Control Flow Graphs

## 16.1 Définition

Un Control Flow Graph ou CFG représente les chemins d’exécution possibles d’une fonction.

Il est composé de :

- **basic blocks** : séquences d’instructions sans branche interne ;
- **edges** : transitions entre blocs ;
- branches conditionnelles ;
- boucles ;
- appels ;
- retours.

## 16.2 Basic block

Exemple :

```asm
mov eax, [rbp-4]
cmp eax, 5
jne fail
```

Ces instructions forment un bloc qui se termine par une branche.

## 16.3 Pourquoi le CFG est utile

Il permet de repérer :

- branches succès/échec ;
- boucles de déchiffrement ;
- conditions imbriquées ;
- chemins morts ;
- logique d’authentification ;
- sorties d’erreur ;
- états d’un programme ;
- obfuscation par aplatissement du contrôle de flux.

## 16.4 Exemple logique

```c
if (length != 16) {
    fail();
}
if (check_key(input)) {
    success();
} else {
    fail();
}
```

CFG simplifié :

```text
        [Entry]
           |
    [length == 16 ?]
       /         \
     non         oui
      |           |
    [Fail]   [check_key ?]
                 /    \
               non    oui
                |      |
              [Fail] [Success]
```

## 16.5 Backward slicing mental

Lorsque tu vois un bloc `Success`, remonte vers les conditions nécessaires pour l’atteindre.

Questions :

1. Quel saut mène au succès ?
2. Quel `cmp` ou `test` contrôle ce saut ?
3. D’où vient la valeur comparée ?
4. Quelle fonction la calcule ?
5. Quelles entrées influencent cette fonction ?

Cette méthode est très efficace pour les CTF.

---

# 17. Cross-references

## 17.1 Définition

Une cross-reference ou xref indique où un élément est utilisé.

Types :

- référence de code vers une fonction ;
- référence de code vers une chaîne ;
- référence de données vers une adresse ;
- appel entrant vers une fonction ;
- appel sortant vers une autre fonction.

## 17.2 Exemple avec une chaîne

Tu trouves :

```text
Access granted
```

En affichant ses xrefs, tu trouves une fonction qui appelle `puts` après une comparaison.

Cela donne immédiatement un point d’entrée logique dans l’analyse.

## 17.3 Exemple avec `strcmp`

Trouver les xrefs vers `strcmp` permet de localiser toutes les comparaisons de chaînes.

Chaque appel doit être étudié :

- arguments ;
- valeur de retour ;
- saut conditionnel suivant ;
- destination succès/échec.

## 17.4 Call graph

Un call graph représente les relations entre fonctions.

Exemple :

```text
main
├── read_user_input
├── validate_length
├── decode_expected_value
├── compare_result
└── print_result
```

Il aide à prioriser les fonctions importantes.

---

# 18. Reconnaissance de motifs et signatures

## 18.1 Pattern recognition

L’expérience permet de reconnaître des constructions courantes.

### Mise à zéro

```asm
xor eax, eax
```

### Boucle

```asm
mov ecx, 0
loop_start:
    ...
    inc ecx
    cmp ecx, 16
    jl loop_start
```

### Comparaison de chaîne

```asm
call strcmp
test eax, eax
jne failure
```

### Vérification d’un bit

```asm
test eax, 1
jz even_case
```

### Multiplication par puissance de deux

```asm
shl eax, 3
```

équivaut généralement à multiplier un entier non débordant par 8.

## 18.2 Signature matching

Une signature est un motif connu associé à :

- une bibliothèque ;
- une fonction ;
- un compilateur ;
- un packer ;
- un algorithme ;
- un malware ;
- une vulnérabilité.

Exemples de signatures :

- constantes AES ;
- tables CRC32 ;
- séquences de prologue ;
- noms de sections UPX ;
- appels connus d’un runtime ;
- motifs de bibliothèques statiquement liées.

## 18.3 Limites des signatures

Une signature peut produire :

- faux positif ;
- faux négatif ;
- confusion entre implémentations ;
- échec après modification ou obfuscation.

Elle doit orienter l’analyse, pas la remplacer.

---

# 19. Recherche de vulnérabilités dans un binaire

## 19.1 Fonctions dangereuses

### `gets`

Ne connaît pas la taille du buffer.

```c
char buffer[32];
gets(buffer);
```

Vulnérabilité probable : stack buffer overflow.

### `strcpy`

```c
strcpy(destination, source);
```

Danger si `destination` est trop petite.

### `strcat`

Peut dépasser la taille après concaténation.

### `sprintf`

```c
sprintf(buffer, "%s", user_input);
```

Pas de limite de taille.

### `printf(user_input)`

Format string vulnerability potentielle.

Correct :

```c
printf("%s", user_input);
```

### `system`

```c
system(user_controlled_string);
```

Risque de command injection.

## 19.2 Vérifier les tailles

Exemple assembleur :

```asm
lea rax, [rbp-0x20]   ; buffer de 32 octets
mov rdi, rax
call gets
```

L’appel à `gets` avec un buffer local de 32 octets est critique.

## 19.3 Integer overflow

```c
size_t total = count * element_size;
buffer = malloc(total);
```

Si la multiplication déborde, une allocation trop petite peut être créée.

Indices assembleur :

- multiplication sans vérification ;
- comparaison signée incohérente ;
- conversion 64 → 32 bits ;
- addition avant allocation.

## 19.4 Signedness bug

```c
int length = read(...);
if (length < MAX) {
    memcpy(dst, src, length);
}
```

Une valeur négative convertie en `size_t` peut devenir très grande.

## 19.5 Use-after-free

Séquence suspecte :

```text
malloc
...
free(ptr)
...
lecture ou écriture via ptr
```

## 19.6 Double free

Deux chemins de contrôle appellent `free(ptr)` sans remettre le pointeur à `NULL` ni empêcher le second appel.

## 19.7 Path traversal

Fonctions d’ouverture de fichiers combinées à une entrée non filtrée :

```c
snprintf(path, sizeof(path), "/srv/data/%s", input);
fopen(path, "r");
```

Rechercher la validation de `..`, `/`, `\` et des chemins absolus.

## 19.8 Mauvaise cryptographie

Indices :

- clé codée en dur ;
- nonce réutilisé ;
- XOR avec une clé courte répétée ;
- PRNG non cryptographique ;
- comparaison de secret non constante ;
- hash sans sel pour mots de passe ;
- ECB ;
- IV fixe.

## 19.9 Protections de compilation

```bash
checksec --file=target_binary
```

Protections typiques :

- NX ;
- PIE ;
- stack canary ;
- RELRO ;
- fortification.

### NX

Empêche normalement l’exécution de la stack et d’autres pages de données.

### Stack canary

Détecte certains débordements de pile avant le retour de fonction.

Indices :

- appel à `__stack_chk_fail` ;
- lecture depuis `fs:0x28` sur Linux x86-64.

### PIE

Permet de charger l’exécutable à une adresse variable avec ASLR.

### RELRO

Protège les structures de relocation, notamment la GOT selon le niveau.

---

# 20. Mathématiques utiles : arithmétique modulaire

## 20.1 Définition

On écrit :

```text
a ≡ b mod n
```

si `a` et `b` ont le même reste dans la division par `n`.

Exemple :

```text
17 ≡ 5 mod 12
```

car :

```text
17 % 12 = 5
```

## 20.2 Applications en reverse engineering

- rotation de caractères ;
- index cyclique dans une clé ;
- chiffrement de César ;
- calculs cryptographiques ;
- PRNG ;
- hash ;
- boucles sur tableaux ;
- réduction d’entiers.

## 20.3 Modulo par puissance de deux

Pour un entier non signé :

```c
x % 256
```

peut devenir :

```asm
and eax, 0xff
```

Le compilateur remplace souvent les opérations coûteuses par des opérations bitwise.

## 20.4 Inverse modulaire

L’inverse de `a mod n` est une valeur `x` telle que :

```text
a × x ≡ 1 mod n
```

Il existe seulement si `gcd(a, n) = 1`.

Cette notion apparaît dans RSA et certaines transformations réversibles.

---

# 21. Exponentiation by squaring

## 21.1 Problème naïf

Calculer :

```text
base^exponent
```

avec une multiplication répétée demande `O(exponent)` multiplications.

## 21.2 Principe

Si l’exposant est pair :

```text
base^n = (base^(n/2))^2
```

S’il est impair :

```text
base^n = base × base^(n-1)
```

On réduit ainsi la complexité à `O(log n)`.

## 21.3 Version itérative

```c
uint64_t fast_pow(uint64_t base, uint64_t exponent) {
    uint64_t result = 1;

    while (exponent > 0) {
        if (exponent & 1) {
            result *= base;
        }

        base *= base;
        exponent >>= 1;
    }

    return result;
}
```

## 21.4 Version modulaire

```c
uint64_t mod_pow(uint64_t base, uint64_t exponent, uint64_t modulus) {
    uint64_t result = 1 % modulus;
    base %= modulus;

    while (exponent > 0) {
        if (exponent & 1) {
            result = (result * base) % modulus;
        }

        base = (base * base) % modulus;
        exponent >>= 1;
    }

    return result;
}
```

## 21.5 Motifs assembleur reconnaissables

- `test exponent, 1` : exposant impair ?
- `imul base, base` : mise au carré ;
- `shr exponent, 1` : division de l’exposant par deux ;
- boucle jusqu’à exposant nul ;
- réduction par modulo.

## 21.6 Pourquoi cette partie apparaît dans le projet ?

Une tâche d’optimisation de déchiffrement peut contenir un calcul de puissance inefficace. Le reverse engineer doit :

1. comprendre l’algorithme actuel ;
2. déterminer sa complexité ;
3. conserver exactement le même résultat ;
4. remplacer les opérations répétitives par une méthode logarithmique ;
5. gérer correctement les overflows et le modulo.

---

# 22. Cryptographie et transformations fréquentes

## 22.1 XOR

Propriété essentielle :

```text
A XOR K XOR K = A
```

Exemple :

```python
cipher = bytes([0x12, 0x34, 0x56])
key = 0x20
plain = bytes(byte ^ key for byte in cipher)
print(plain)
```

Motif assembleur :

```asm
movzx eax, BYTE PTR [buffer+index]
xor eax, key
mov BYTE PTR [buffer+index], al
```

## 22.2 XOR avec clé répétée

```python
def repeating_xor(data: bytes, key: bytes) -> bytes:
    return bytes(value ^ key[index % len(key)] for index, value in enumerate(data))
```

Indices :

- index modulo longueur de clé ;
- accès cyclique ;
- `xor` dans une boucle.

## 22.3 César

```text
plaintext_char = (cipher_char - shift) mod 26
```

Le code peut comporter :

- soustraction de `'A'` ou `'a'` ;
- modulo 26 ;
- ajout du décalage ;
- remise dans l’intervalle ASCII.

## 22.4 Base64

Alphabet reconnaissable :

```text
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
```

Une chaîne finissant par `=` ou `==` peut être du Base64, sans que cela soit garanti.

Décodage local :

```bash
printf '%s' 'SGVsbG8=' | base64 -d
```

## 22.5 Hexadécimal

```bash
printf '%s' '666c6167' | xxd -r -p
```

## 22.6 ROT13

```bash
printf '%s' 'synt' | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

## 22.7 Add/subtract transform

```c
output[i] = input[i] - 3;
```

La transformation inverse est :

```c
input[i] = output[i] + 3;
```

## 22.8 Permutation

Le programme peut réordonner les caractères :

```c
output[i] = input[index_table[i]];
```

Il faut reconstituer la permutation ou son inverse.

## 22.9 Comparaison directe vs validation transformée

### Comparaison directe

```c
strcmp(input, "secret")
```

### Validation transformée

```c
for (i = 0; i < length; i++) {
    if ((input[i] ^ key[i]) != expected[i]) {
        return false;
    }
}
```

Dans le second cas, le flag n’est pas présent en clair mais reste mathématiquement récupérable.

---

# 23. Obfuscation et anti-reverse engineering

## 23.1 Obfuscation de chaînes

- XOR ;
- addition/soustraction ;
- chiffrement ;
- compression ;
- reconstruction sur stack ;
- fragmentation ;
- encodage personnalisé.

## 23.2 Control flow flattening

Le programme utilise un dispatcher et une variable d’état.

```c
while (1) {
    switch (state) {
        case 10: ...; state = 42; break;
        case 42: ...; state = 7; break;
        case 7: return result;
    }
}
```

Le CFG devient artificiellement complexe.

## 23.3 Dead code

Code jamais exécuté, ajouté pour distraire l’analyste.

Indices :

- branche impossible ;
- valeur constante contrôlant un `if` ;
- fonction sans xref ;
- bloc non atteignable.

## 23.4 Opaque predicates

Condition compliquée mais toujours vraie ou toujours fausse.

Exemple conceptuel :

```c
if ((x * x + x) % 2 == 0) {
    real_code();
} else {
    fake_code();
}
```

Pour tout entier `x`, `x² + x` est pair.

## 23.5 Packing

Un packer compresse ou chiffre le vrai code, puis le décompresse en mémoire.

Indices :

- peu d’imports ;
- sections à entropie élevée ;
- noms comme `UPX0`, `UPX1` ;
- point d’entrée inhabituel ;
- code initial très court ;
- appel à `mprotect`, `VirtualAlloc`, `VirtualProtect` ;
- grands blocs de données non interprétables.

## 23.6 Anti-debug

Techniques :

- `ptrace(PTRACE_TRACEME)` ;
- lecture de `/proc/self/status` ;
- vérification de temps ;
- exceptions ;
- `IsDebuggerPresent` ;
- détection de breakpoints ;
- vérification de processus.

## 23.7 Anti-disassembly

- octets ambiguës ;
- sauts au milieu d’instructions ;
- données insérées dans le flot ;
- instructions invalides sur chemins morts ;
- tables de sauts indirectes.

## 23.8 Méthode de résolution

1. Identifier les blocs réellement atteignables.
2. Utiliser les xrefs.
3. Renommer la variable d’état.
4. Reconstituer les transitions.
5. Simplifier les expressions.
6. Écrire un script reproduisant uniquement la transformation utile.
7. Confirmer avec une exécution contrôlée si autorisé.

---

# 24. Présentation des outils

| Outil | Type | Points forts | Limites |
|---|---|---|---|
| Ghidra | GUI, désassembleur, décompilateur | Gratuit, puissant, multi-architecture | Interface dense, décompilation parfois approximative |
| Radare2 | CLI/framework | Scriptable, léger, très complet | Courbe d’apprentissage forte |
| Cutter | GUI pour Radare2/Rizin | Visualisation plus accessible | Peut masquer certains détails CLI |
| IDA Pro | GUI, désassembleur | Standard historique, excellent graphe | Version complète coûteuse |
| Hex-Rays | Décompilateur IDA | Pseudo-code très performant | Commercial |
| Binary Ninja | GUI/API | UX moderne, excellente API | Commercial |
| Objdump | CLI | Rapide, disponible, fiable pour triage | Navigation limitée |
| Readelf | CLI | Très bon pour ELF | Pas de décompilation |
| Strings | CLI | Triage immédiat | Beaucoup de faux positifs et chaînes cachées |
| Binwalk | CLI | Firmware, données embarquées | Pas un désassembleur général |
| GDB | Debugger | Valeurs runtime, breakpoints | Exécute le programme |

---

# 25. Ghidra : guide pratique

## 25.1 Créer un projet

1. Lancer Ghidra.
2. `File` → `New Project`.
3. Choisir `Non-Shared Project`.
4. Créer un projet dans le dossier de travail.
5. Importer `target_binary`.
6. Vérifier le format et le langage détectés.
7. Lancer `Auto Analyze`.

## 25.2 Options d’analyse utiles

- Function ID ;
- Decompiler Parameter ID ;
- Stack ;
- References ;
- ELF Scalar Operand References ;
- demangler C++ si nécessaire.

## 25.3 Fenêtres principales

- **Listing** : instructions et données ;
- **Decompiler** : pseudo-code ;
- **Symbol Tree** : fonctions, labels, imports ;
- **Defined Strings** : chaînes ;
- **Function Graph** : CFG ;
- **Data Type Manager** : types et structures.

## 25.4 Trouver `main`

Possibilités :

- symbole `main` présent ;
- recherche dans `Symbol Tree` ;
- suivre `_start` ;
- suivre l’argument transmis à `__libc_start_main` ;
- utiliser la fonction de découverte automatique.

## 25.5 Utiliser les strings

1. `Window` → `Defined Strings`.
2. Rechercher `flag`, `success`, `error`, `password`.
3. Double-cliquer sur la chaîne.
4. Appuyer sur la touche ou commande de xrefs selon la configuration.
5. Naviguer vers la fonction utilisatrice.

## 25.6 Renommer

Noms utiles :

```text
FUN_00101210 → validate_input
local_38 → input_buffer
iVar2 → comparison_result
DAT_00104020 → encoded_flag
```

Le renommage transforme progressivement un binaire illisible en modèle compréhensible.

## 25.7 Corriger les types

Exemple initial :

```c
long FUN_00101210(long param_1)
```

Après analyse :

```c
bool validate_flag(const char *input)
```

Une bonne signature améliore toute la décompilation.

## 25.8 Patch et export

Dans un projet académique, on peut annoter ou patcher pour comprendre, mais il faut conserver l’original et documenter chaque changement.

---

# 26. Radare2 : guide pratique

## 26.1 Ouvrir le fichier

```bash
r2 target_binary
```

Analyse automatique :

```bash
r2 -A target_binary
```

Dans l’interface :

```text
aaa
```

## 26.2 Informations générales

```text
iI      informations binaires
ih      headers
is      symboles
ii      imports
iz      strings dans les sections de données
izz     recherche plus large de strings
iS      sections
ie      entry points
```

En CLI directe :

```bash
rabin2 -I target_binary
rabin2 -S target_binary
rabin2 -i target_binary
rabin2 -z target_binary
```

## 26.3 Lister les fonctions

```text
afl
```

Filtrer :

```text
afl~main
```

## 26.4 Aller à une fonction

```text
s main
pdf
```

Si `main` n’est pas nommé :

```text
s entry0
pdf
```

## 26.5 Désassembler

```text
pd 30
pdf
```

- `pd 30` : 30 instructions ;
- `pdf` : fonction courante.

## 26.6 Xrefs

Références vers l’adresse courante :

```text
axt
```

Références depuis l’adresse courante :

```text
afx
```

## 26.7 Graph mode

```text
VV
```

Navigation visuelle du CFG dans le terminal.

## 26.8 Renommer

```text
afvn old_name new_name
```

Renommer une fonction :

```text
af new_function_name @ address
```

Selon le contexte, utiliser :

```text
fr old_name new_name
```

## 26.9 Pseudo-code

Selon les plugins installés :

```text
pdc
pdg
```

`pdc` fournit une représentation pseudo-C interne. `pdg` peut utiliser le décompilateur Ghidra si le plugin correspondant est installé.

## 26.10 Recherche

Chaîne :

```text
/ flag
```

Octets :

```text
/x 7f454c46
```

---

# 27. IDA Pro : guide pratique

## 27.1 Chargement

1. Ouvrir le binaire.
2. Vérifier l’architecture.
3. Autoriser l’auto-analysis.
4. Ouvrir la liste des fonctions.
5. Rechercher les imports et strings.

## 27.2 Vues principales

- Text View ;
- Graph View ;
- Functions ;
- Imports ;
- Exports ;
- Strings ;
- Hex View ;
- Pseudocode avec Hex-Rays.

## 27.3 Navigation typique

1. Ouvrir Strings.
2. Trouver une chaîne de succès.
3. Afficher ses xrefs.
4. Aller dans la fonction.
5. Lire le graphe.
6. Ouvrir le pseudo-code.
7. Renommer fonctions et variables.

## 27.4 IDA Free et IDA Pro

Les fonctionnalités varient selon l’édition et les architectures. Pour un projet ELF x86-64 académique, Ghidra suffit souvent largement.

---

# 28. Objdump, readelf, nm et autres outils CLI

## 28.1 `file`

```bash
file target_binary
```

## 28.2 `readelf`

```bash
readelf -h target_binary       # header
readelf -S target_binary       # sections
readelf -l target_binary       # segments
readelf -s target_binary       # symboles
readelf -d target_binary       # dynamique
readelf -r target_binary       # relocations
readelf -x .rodata target_binary
```

## 28.3 `objdump`

```bash
objdump -f target_binary
objdump -h target_binary
objdump -x target_binary
objdump -d -M intel target_binary
objdump -s -j .rodata target_binary
objdump -T target_binary
```

## 28.4 `nm`

```bash
nm target_binary
nm -D target_binary
nm -C target_binary
```

`-C` démangle les noms C++.

## 28.5 `strings`

```bash
strings -a -n 4 -t x target_binary
```

## 28.6 `rabin2`

```bash
rabin2 -I target_binary
rabin2 -S target_binary
rabin2 -i target_binary
rabin2 -E target_binary
rabin2 -z target_binary
rabin2 -zz target_binary
```

## 28.7 `binwalk`

```bash
binwalk target_binary
binwalk -e target_binary
```

À utiliser surtout pour :

- firmware ;
- images disque ;
- données compressées ;
- fichiers embarqués ;
- signatures internes.

L’extraction doit se faire dans un dossier contrôlé.

## 28.8 `checksec`

```bash
checksec --file=target_binary
```

## 28.9 `xxd`

```bash
xxd -l 128 target_binary
xxd -s 0x2000 -l 64 target_binary
```

---

# 29. GDB pour confirmer une analyse

Même si le projet porte sur l’analyse statique, GDB peut servir à confirmer une hypothèse dans une VM.

## 29.1 Ouvrir

```bash
gdb ./target_binary
```

Syntaxe Intel :

```gdb
set disassembly-flavor intel
```

## 29.2 Fonctions principales

```gdb
info functions
disassemble main
break main
run
nexti
stepi
continue
info registers
x/16gx $rsp
x/s $rdi
```

## 29.3 Breakpoint sur une fonction importée

```gdb
break strcmp
run
```

Sur Linux x86-64, avant l’appel :

```gdb
x/s $rdi
x/s $rsi
```

Cela peut révéler les deux chaînes comparées.

## 29.4 Attention

- GDB exécute le binaire ;
- ne jamais l’utiliser hors sandbox pour un échantillon dangereux ;
- documenter que la confirmation est dynamique ;
- ne pas modifier le résultat attendu du projet si l’analyse doit rester strictement statique.

---

# 30. Task 0 — Extracting and Analyzing Strings

## 30.1 Objectif

Extraire les chaînes lisibles de `target-binary` ou `target_binary`, trouver les informations utiles et enregistrer le flag demandé dans :

```text
holbertonschool-reverse_engineering/static_analysis/0-flag.txt
```

Le nom exact du binaire doit être vérifié dans le projet. Les consignes utilisent parfois `target-binary` et parfois `target_binary`.

## 30.2 Étape 1 — Se placer dans le bon dossier

```bash
cd holbertonschool-reverse_engineering/static_analysis
ls -la
```

## 30.3 Étape 2 — Identifier le binaire

```bash
file ./target-binary
sha256sum ./target-binary
```

ou :

```bash
file ./target_binary
sha256sum ./target_binary
```

## 30.4 Étape 3 — Extraire les chaînes

```bash
strings -a ./target-binary | less
```

Avec offsets :

```bash
strings -a -t x ./target-binary | less
```

Enregistrer :

```bash
strings -a -n 4 -t x ./target-binary > strings_output.txt
```

## 30.5 Étape 4 — Filtrer intelligemment

```bash
grep -iE 'flag|secret|password|token|key|success|correct' strings_output.txt
```

Ne cherche pas seulement le mot `flag`. Le résultat peut ressembler à :

- un token ;
- une phrase ;
- une chaîne hexadécimale ;
- un fragment ;
- une chaîne située près d’un message de succès.

## 30.6 Étape 5 — Examiner le contexte

```bash
grep -in -C 3 'flag' strings_output.txt
```

`-C 3` affiche trois lignes avant et après.

## 30.7 Étape 6 — Si la chaîne n’est pas visible

### Chaînes plus courtes

```bash
strings -a -n 3 ./target-binary | less
```

### UTF-16

```bash
strings -a -e l ./target-binary | less
```

### Section `.rodata`

```bash
objdump -s -j .rodata ./target-binary | less
```

### Radare2

```bash
rabin2 -zz ./target-binary | less
```

### Ghidra

- ouvrir Defined Strings ;
- rechercher les messages ;
- suivre les xrefs ;
- identifier la fonction qui les utilise.

## 30.8 Étape 7 — Écrire uniquement la valeur attendue

```bash
printf '%s\n' 'VALEUR_TROUVEE' > 0-flag.txt
```

Vérifier :

```bash
cat -A 0-flag.txt
```

`cat -A` permet de repérer les espaces et caractères invisibles.

## 30.9 Étape 8 — Git

```bash
git status
git add 0-flag.txt
git commit -m "Add flag extracted through static string analysis"
git push
```

## 30.10 Ce qu’il faut expliquer à l’oral

> J’ai commencé par vérifier le type et l’intégrité du binaire. J’ai ensuite utilisé `strings` avec l’option `-a` pour scanner tout le fichier et `-t x` pour conserver les offsets. J’ai filtré les résultats avec `grep`, puis vérifié le contexte de la chaîne. Enfin, j’ai enregistré uniquement la valeur attendue dans `0-flag.txt`.

## 30.11 Pièges

- exécuter le fichier alors que ce n’est pas nécessaire ;
- copier un message complet au lieu du flag ;
- inclure des guillemets ;
- écrire une commande dans `0-flag.txt` au lieu de la réponse ;
- ajouter un espace final ;
- analyser le mauvais fichier ;
- faire confiance à la première chaîne ressemblant à un flag sans suivre sa xref.

---

# 31. Task 1 — Static Analysis of a Security-Critical C Program

Le détail exact de la tâche doit être lu dans l’énoncé du repository. La méthode ci-dessous couvre les attentes habituelles.

## 31.1 Objectifs probables

- comprendre une fonction de sécurité ;
- identifier une erreur logique ou mémoire ;
- déterminer comment une entrée est validée ;
- analyser des appels dangereux ;
- expliquer l’impact ;
- proposer une correction.

## 31.2 Méthode

### Étape A — Identifier les imports sensibles

```bash
objdump -T target_binary | grep -E 'strcpy|strcat|gets|scanf|sprintf|printf|system|memcmp|strcmp'
```

### Étape B — Trouver les xrefs

Dans Ghidra ou Radare2, suivre chaque appel.

### Étape C — Reconstituer la taille des buffers

Exemple :

```asm
sub rsp, 0x40
lea rax, [rbp-0x30]
```

Cela suggère un buffer local autour de `rbp-0x30`, mais il faut regarder l’ensemble de la stack frame.

### Étape D — Identifier la source de l’entrée

- `argv` ;
- `scanf` ;
- `fgets` ;
- `read` ;
- fichier ;
- socket ;
- variable d’environnement.

### Étape E — Vérifier les contrôles

Questions :

- la taille est-elle vérifiée avant la copie ?
- la vérification porte-t-elle sur la bonne variable ?
- existe-t-il une erreur off-by-one ?
- le retour d’une fonction est-il vérifié ?
- la comparaison est-elle inversée ?
- un type signé est-il utilisé pour une taille ?
- le code nettoie-t-il réellement un secret ?

## 31.3 Exemple : erreur de comparaison

```c
if (strcmp(input, expected)) {
    grant_access();
}
```

`strcmp` retourne zéro lorsque les chaînes sont égales. Le code ci-dessus accorde donc l’accès lorsque les chaînes sont différentes.

Assembleur typique :

```asm
call strcmp
test eax, eax
je denied
call grant_access
```

## 31.4 Exemple : overflow

```c
char password[16];
scanf("%s", password);
```

La conversion `%s` sans largeur peut écrire plus de 15 caractères plus le terminateur nul.

Correction :

```c
scanf("%15s", password);
```

ou mieux, selon le contexte :

```c
fgets(password, sizeof(password), stdin);
```

## 31.5 Rapport attendu

Pour chaque problème :

1. emplacement ;
2. instructions ou pseudo-code ;
3. entrée contrôlable ;
4. condition d’exploitation ;
5. impact ;
6. protections existantes ;
7. correction recommandée ;
8. niveau de confiance.

---

# 32. Task 2 — Optimizing a Decryption Algorithm

## 32.1 But

Comprendre un algorithme de déchiffrement ou de transformation, puis réduire son coût sans modifier son résultat.

## 32.2 Étapes d’analyse

1. Identifier les entrées : ciphertext, clé, modulus, exponent, longueur.
2. Identifier la boucle principale.
3. Traduire le code en pseudo-code.
4. Déterminer la complexité.
5. Repérer les calculs répétés.
6. Trouver une transformation mathématiquement équivalente.
7. Implémenter avec des types sûrs.
8. Tester localement sur plusieurs cas.

## 32.3 Exemple inefficace

```c
uint64_t power(uint64_t base, uint64_t exponent) {
    uint64_t result = 1;

    for (uint64_t i = 0; i < exponent; i++) {
        result *= base;
    }

    return result;
}
```

Complexité : `O(exponent)`.

## 32.4 Version optimisée

```c
uint64_t power(uint64_t base, uint64_t exponent) {
    uint64_t result = 1;

    while (exponent > 0) {
        if ((exponent & 1U) != 0U) {
            result *= base;
        }

        exponent >>= 1U;

        if (exponent > 0) {
            base *= base;
        }
    }

    return result;
}
```

Complexité : `O(log exponent)`.

## 32.5 Avec modulo

Attention aux overflows de multiplication.

Une implémentation simple en C peut déborder avant l’application du modulo :

```c
result = (result * base) % modulus;
```

Selon les contraintes, il peut être nécessaire d’utiliser :

- un type entier plus large ;
- `unsigned __int128` sur GCC/Clang ;
- une multiplication modulaire sûre ;
- une bibliothèque big integer.

Exemple avec `unsigned __int128` :

```c
static uint64_t mul_mod(uint64_t a, uint64_t b, uint64_t modulus) {
    return (uint64_t)(((unsigned __int128)a * b) % modulus);
}
```

## 32.6 Reconnaître l’algorithme dans un binaire

Chercher :

- boucle contrôlée par l’exposant ;
- test du bit faible ;
- mise au carré ;
- décalage à droite ;
- modulo ;
- accumulation dans un résultat.

## 32.7 Validation

Écrire un petit test comparant l’ancien et le nouvel algorithme sur :

- exposant 0 ;
- base 0 ;
- base 1 ;
- exposant pair ;
- exposant impair ;
- grandes valeurs ;
- modulus 1 ;
- valeurs proches des limites de type.

---

# 33. Task 3 — Reverse Engineering an Obfuscated Flag

## 33.1 Objectif

Retrouver une valeur cachée qui n’apparaît pas nécessairement en clair.

## 33.2 Workflow

### Étape 1 — Chercher les sorties

- message de succès ;
- message d’échec ;
- affichage final ;
- fonction `puts` ou `printf`.

### Étape 2 — Suivre les xrefs

Trouver la fonction qui décide entre succès et échec.

### Étape 3 — Identifier la validation

Possibilités :

- comparaison directe ;
- boucle caractère par caractère ;
- hash ;
- XOR ;
- permutation ;
- calcul modulaire ;
- table de substitution.

### Étape 4 — Reconstituer les constantes

Les constantes peuvent être :

- dans `.rodata` ;
- dans `.data` ;
- chargées par `movabs` ;
- réparties sur plusieurs instructions ;
- copiées sur la stack.

### Étape 5 — Inverser la transformation

Si :

```text
encoded[i] = flag[i] XOR key[i]
```

alors :

```text
flag[i] = encoded[i] XOR key[i]
```

Si :

```text
encoded[i] = (flag[i] + 7) mod 256
```

alors :

```text
flag[i] = (encoded[i] - 7) mod 256
```

### Étape 6 — Écrire un solveur local

Exemple :

```python
#!/usr/bin/env python3

encoded = bytes([
    0x12, 0x34, 0x56,
])
key = 0x20

flag = bytes(value ^ key for value in encoded)
print(flag.decode("utf-8", errors="replace"))
```

### Étape 7 — Vérifier

- longueur correcte ;
- caractères imprimables ;
- préfixe attendu ;
- pas de byte nul inattendu ;
- transformation reproduite exactement.

## 33.3 Construction de chaîne sur stack

Exemple assembleur :

```asm
mov BYTE PTR [rbp-0x20], 0x48
mov BYTE PTR [rbp-0x1f], 0x6f
mov BYTE PTR [rbp-0x1e], 0x6c
mov BYTE PTR [rbp-0x1d], 0x62
```

Conversion ASCII :

```text
0x48 = H
0x6f = o
0x6c = l
0x62 = b
```

## 33.4 Constantes 64 bits et endianness

```asm
movabs rax, 0x7d67616c667b4148
mov QWORD PTR [rbp-0x20], rax
```

En little-endian, les octets en mémoire seront inversés par rapport à l’écriture hexadécimale immédiate.

Valeur immédiate :

```text
7d 67 61 6c 66 7b 41 48
```

En mémoire little-endian :

```text
48 41 7b 66 6c 61 67 7d
```

Il faut toujours tenir compte de l’endianness.

---

# 34. Task 4 — Understanding Raw Assembly Code

## 34.1 Méthode générale

Pour une fonction en assembleur brut :

1. identifier l’architecture ;
2. identifier la syntaxe ;
3. découper en basic blocks ;
4. annoter chaque instruction ;
5. suivre les registres ;
6. reconstruire les variables ;
7. identifier les boucles ;
8. traduire en pseudo-code ;
9. calculer la sortie pour l’entrée donnée.

## 34.2 Tableau de suivi de registres

Exemple :

```asm
mov eax, 3
mov ebx, 5
add eax, ebx
imul eax, eax, 2
```

| Instruction | EAX | EBX |
|---|---:|---:|
| initial | ? | ? |
| `mov eax, 3` | 3 | ? |
| `mov ebx, 5` | 3 | 5 |
| `add eax, ebx` | 8 | 5 |
| `imul eax, eax, 2` | 16 | 5 |

## 34.3 Exemple avec boucle

```asm
xor eax, eax
xor ecx, ecx
loop_start:
    add eax, ecx
    inc ecx
    cmp ecx, 5
    jl loop_start
ret
```

Pseudo-code :

```c
int result = 0;
int i = 0;

while (i < 5) {
    result += i;
    i++;
}

return result;
```

Résultat :

```text
0 + 1 + 2 + 3 + 4 = 10
```

## 34.4 Exemple avec tableau

```asm
movzx eax, BYTE PTR [rdi+rcx]
xor eax, 0x20
mov BYTE PTR [rsi+rcx], al
```

Interprétation probable :

```c
output[i] = input[i] ^ 0x20;
```

## 34.5 Questions à se poser

- Quels registres contiennent les arguments ?
- Quelle est la valeur de retour ?
- Les opérations sont-elles signées ?
- Quelle taille est manipulée ?
- Où commence et finit la boucle ?
- Le saut est-il pris lorsque la comparaison est vraie ou fausse ?
- Une instruction `lea` calcule-t-elle une adresse ou une multiplication ?
- Un accès mémoire est-il relatif à `RBP`, `RSP`, `RIP` ou un argument ?

---

# 35. Scripts Kali Linux réutilisables

Tous les scripts utilisent des chemins relatifs et doivent être rendus exécutables.

## 35.1 Script de triage complet

Fichier : `scripts/triage.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

usage() {
    printf 'Usage: %s <binary> [output-directory]\n' "$0" >&2
}

if [[ $# -lt 1 || $# -gt 2 ]]; then
    usage
    exit 1
fi

binary="$1"
output_dir="${2:-./output}"

if [[ ! -f "$binary" ]]; then
    printf 'Error: file not found: %s\n' "$binary" >&2
    exit 1
fi

mkdir -p "$output_dir"

base_name="$(basename -- "$binary")"

sha256sum "$binary" > "$output_dir/${base_name}.sha256"
file "$binary" > "$output_dir/${base_name}.file.txt"
stat "$binary" > "$output_dir/${base_name}.stat.txt"
strings -a -n 4 -t x "$binary" > "$output_dir/${base_name}.strings.txt"
xxd -l 256 "$binary" > "$output_dir/${base_name}.header.hex.txt"

if readelf -h "$binary" >/dev/null 2>&1; then
    readelf -h "$binary" > "$output_dir/${base_name}.elf-header.txt"
    readelf -SW "$binary" > "$output_dir/${base_name}.sections.txt"
    readelf -lW "$binary" > "$output_dir/${base_name}.segments.txt"
    readelf -dW "$binary" > "$output_dir/${base_name}.dynamic.txt" || true
    readelf -sW "$binary" > "$output_dir/${base_name}.symbols.txt" || true
    objdump -d -M intel "$binary" > "$output_dir/${base_name}.disassembly.txt" || true
fi

if command -v checksec >/dev/null 2>&1; then
    checksec --file="$binary" > "$output_dir/${base_name}.checksec.txt" || true
fi

if command -v rabin2 >/dev/null 2>&1; then
    rabin2 -I "$binary" > "$output_dir/${base_name}.rabin2-info.txt" || true
    rabin2 -S "$binary" > "$output_dir/${base_name}.rabin2-sections.txt" || true
    rabin2 -i "$binary" > "$output_dir/${base_name}.rabin2-imports.txt" || true
fi

printf 'Triage complete. Results written to %s\n' "$output_dir"
```

Rendre exécutable :

```bash
chmod +x scripts/triage.sh
```

Utilisation :

```bash
./scripts/triage.sh ./target_binary
```

## 35.2 Script d’extraction de strings

Fichier : `scripts/extract_strings.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

if [[ $# -ne 1 ]]; then
    printf 'Usage: %s <binary>\n' "$0" >&2
    exit 1
fi

binary="$1"

if [[ ! -f "$binary" ]]; then
    printf 'Error: file not found: %s\n' "$binary" >&2
    exit 1
fi

output="$(basename -- "$binary").strings.txt"

strings -a -n 4 -t x "$binary" > "$output"
printf 'Strings written to %s\n' "$output"
```

## 35.3 Script de recherche d’indicateurs

Fichier : `scripts/find_indicators.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

if [[ $# -ne 1 ]]; then
    printf 'Usage: %s <strings-file>\n' "$0" >&2
    exit 1
fi

strings_file="$1"

if [[ ! -f "$strings_file" ]]; then
    printf 'Error: file not found: %s\n' "$strings_file" >&2
    exit 1
fi

pattern='flag|secret|password|passwd|token|api[_-]?key|admin|root|https?://|/bin/sh|cmd\.exe|powershell|access granted|access denied|invalid|success|failed'

grep -inE "$pattern" "$strings_file" || true
```

## 35.4 Script Python de XOR

Fichier : `scripts/xor_decode.py`

```python
#!/usr/bin/env python3
"""Decode hexadecimal bytes with a single-byte XOR key."""

from __future__ import annotations

import argparse


def parse_arguments() -> argparse.Namespace:
    parser = argparse.ArgumentParser(
        description="Decode hexadecimal data with a single-byte XOR key."
    )
    parser.add_argument("hex_data", help="Hexadecimal bytes, for example: 123456")
    parser.add_argument(
        "key",
        type=lambda value: int(value, 0),
        help="XOR key as decimal or prefixed integer, for example 32 or 0x20",
    )
    return parser.parse_args()


def main() -> None:
    args = parse_arguments()

    if not 0 <= args.key <= 0xFF:
        raise SystemExit("The key must fit in one byte (0..255).")

    try:
        encoded = bytes.fromhex(args.hex_data)
    except ValueError as exc:
        raise SystemExit(f"Invalid hexadecimal data: {exc}") from exc

    decoded = bytes(value ^ args.key for value in encoded)
    print(decoded.decode("utf-8", errors="replace"))


if __name__ == "__main__":
    main()
```

Rendre exécutable :

```bash
chmod +x scripts/xor_decode.py
```

## 35.5 Pourquoi `set -euo pipefail` ?

- `-e` : arrêter en cas d’erreur ;
- `-u` : erreur sur variable non définie ;
- `pipefail` : faire échouer un pipeline si une commande interne échoue.

Cela rend les scripts plus fiables.

---

# 36. Modèle de rapport d’analyse

```markdown
# Static Analysis Report — target_binary

## 1. Executive Summary

Résumé du rôle probable du binaire et des résultats principaux.

## 2. Scope

- File: `target_binary`
- SHA-256: `...`
- Analysis type: static analysis
- Environment: Kali Linux VM
- Online services used: none

## 3. File Identification

- Format:
- Architecture:
- Endianness:
- Bitness:
- Linking:
- Stripped:
- Entry point:

## 4. Security Protections

- NX:
- PIE:
- Canary:
- RELRO:
- Fortify:

## 5. Strings Analysis

| Offset | String | Interpretation |
|---:|---|---|
| `0x...` | `...` | `...` |

## 6. Sections and Headers

Description des sections importantes et anomalies.

## 7. Imports and External Libraries

| Function/Library | Possible purpose | Confirmed usage |
|---|---|---|

## 8. Important Functions

### `main`

Rôle, paramètres, appels et contrôle de flux.

### `validate_input`

Pseudo-code reconstruit.

## 9. Control Flow

Description des chemins succès et échec.

## 10. Findings

### Finding 1 — Title

- Evidence:
- Impact:
- Confidence:
- Recommendation:

## 11. Reconstructed Logic

```c
/* pseudo-code */
```

## 12. Commands Used

```bash
file target_binary
sha256sum target_binary
strings -a -t x target_binary
...
```

## 13. Conclusion

Résumé des faits confirmés et limites de l’analyse.
```

---

# 37. Cheat sheet

## Identification

```bash
file target_binary
stat target_binary
sha256sum target_binary
xxd -l 64 target_binary
```

## Strings

```bash
strings -a target_binary
strings -a -n 6 -t x target_binary
strings -a -e l target_binary
grep -iE 'flag|secret|pass|token|key' strings.txt
```

## ELF

```bash
readelf -h target_binary
readelf -SW target_binary
readelf -lW target_binary
readelf -dW target_binary
readelf -sW target_binary
readelf -rW target_binary
```

## Objdump

```bash
objdump -f target_binary
objdump -h target_binary
objdump -d -M intel target_binary
objdump -s -j .rodata target_binary
objdump -T target_binary
```

## Symboles et bibliothèques

```bash
nm target_binary
nm -D target_binary
ldd target_binary
```

## Sécurité

```bash
checksec --file=target_binary
```

## Radare2

```bash
r2 -A target_binary
```

Puis :

```text
iI
iz
izz
afl
s main
pdf
axt
VV
```

## GDB

```bash
gdb ./target_binary
```

Puis :

```gdb
set disassembly-flavor intel
break main
run
disassemble main
info registers
x/s $rdi
x/16gx $rsp
```

---

# 38. Erreurs fréquentes

## 38.1 Exécuter avant de comprendre

Le premier réflexe doit être l’identification et le triage, pas `./target_binary`.

## 38.2 Faire confiance aveuglément au pseudo-code

Le décompilateur peut inventer de mauvais types ou mal reconstruire une boucle.

## 38.3 Ignorer l’endianness

Les constantes multi-octets peuvent apparaître inversées en mémoire.

## 38.4 Confondre adresse virtuelle et offset fichier

Une adresse affichée dans le désassemblage n’est pas forcément l’offset brut dans le fichier.

## 38.5 Confondre `strcmp == 0`

Zéro signifie égalité.

## 38.6 Confondre `je` et `jne`

- `je` / `jz` : saut si égal/zéro ;
- `jne` / `jnz` : saut si différent/non nul.

## 38.7 Confondre signé et non signé

`jg` et `ja` ne signifient pas exactement la même chose.

## 38.8 Oublier les appels avant `main`

Des fonctions peuvent être exécutées via `.init_array`, TLS callbacks ou constructeurs.

## 38.9 Croire qu’une string prouve un comportement

Il faut suivre les xrefs.

## 38.10 Ne pas renommer les éléments

Sans renommage, l’analyse devient rapidement confuse.

## 38.11 Ne pas documenter les commandes

Une analyse doit être reproductible.

## 38.12 Modifier l’unique copie

Toujours conserver le hash et une copie originale.

---

# 39. Questions d’entretien et de soutenance

## 39.1 What is static analysis in reverse engineering?

Static analysis is the examination of a binary without executing its normal code. It includes studying headers, sections, strings, symbols, imports, instructions, data and control flow to infer the program’s behavior.

## 39.2 Why is it important for malware analysis?

It allows an analyst to collect initial indicators and understand risky capabilities without immediately triggering the sample. It reduces risk and guides later sandboxed dynamic analysis.

## 39.3 Disassembly vs decompilation?

Disassembly converts machine code into assembly instructions. Decompilation attempts to reconstruct higher-level pseudo-code. Disassembly is lower-level and usually more faithful; decompilation is easier to read but based on inference.

## 39.4 PE vs ELF vs Mach-O?

They are executable formats used mainly by Windows, Linux/Unix and macOS/iOS. They differ in headers, section organization, import resolution, metadata and loader behavior.

## 39.5 Why use a CFG?

A CFG maps basic blocks and possible execution paths. It helps identify conditions, loops, success paths, error paths and obfuscation.

## 39.6 What is a cross-reference?

A cross-reference shows where a function, address, symbol or string is used. It connects interesting data to the code responsible for using it.

## 39.7 What is header analysis?

Header analysis extracts structural information such as file type, architecture, entry point, section layout, permissions and loader metadata.

## 39.8 How can vulnerabilities be identified statically?

By locating dangerous functions, analyzing data flow, checking buffer sizes, reconstructing validation logic, inspecting signedness and integer operations, and matching known vulnerable patterns.

## 39.9 Typical static analysis workflow?

1. Preserve and hash the file.
2. Identify type and architecture.
3. Extract strings and metadata.
4. Inspect headers, sections and protections.
5. Enumerate imports, exports and symbols.
6. Locate important functions.
7. Analyze disassembly, decompilation, CFG and xrefs.
8. Reconstruct algorithms.
9. Validate carefully if necessary.
10. Document findings.

## 39.10 Why should online analysis services be avoided in this project?

Because the requirements explicitly demand local analysis. Uploading a binary can also leak confidential material and exposes the sample to third parties.

## 39.11 What does `xor eax, eax` do?

It sets `EAX` to zero, which also clears the upper 32 bits of `RAX` on x86-64.

## 39.12 What does `test eax, eax` followed by `je` mean?

It checks whether `EAX` is zero and jumps if it is.

## 39.13 Why is `strcmp` often important?

It is frequently used to compare user input with a password, key, command or expected value. A return value of zero means equality.

## 39.14 Why is exponentiation by squaring efficient?

It reduces the number of multiplications from linear in the exponent to logarithmic by processing the exponent bit by bit.

---

# 40. Glossaire

**Address Space Layout Randomization — ASLR**  
Randomisation des adresses de chargement pour compliquer l’exploitation.

**Basic block**  
Suite d’instructions exécutées linéairement, terminée par une branche, un appel ou un retour.

**Binary**  
Fichier contenant du code machine et des données.

**Calling convention**  
Règles définissant le passage des arguments, le retour de valeurs et la préservation des registres.

**CFG**  
Control Flow Graph, représentation des chemins d’exécution.

**Cross-reference / xref**  
Référence reliant une adresse, fonction ou donnée à ses utilisations.

**Decompiler**  
Outil reconstruisant du pseudo-code à partir du code machine.

**Disassembler**  
Outil convertissant les octets machine en assembleur.

**ELF**  
Executable and Linkable Format, format principal sous Linux.

**Endianness**  
Ordre des octets d’une valeur multi-octets en mémoire.

**Entry point**  
Adresse de la première instruction exécutée par le loader.

**GOT**  
Global Offset Table, table utilisée pour résoudre des adresses globales et externes.

**Heap**  
Zone de mémoire dynamique.

**Import**  
Fonction ou objet externe utilisé par le binaire.

**Instruction pointer**  
Registre contenant l’adresse de l’instruction suivante, `RIP` en x86-64.

**Linker**  
Outil combinant objets et bibliothèques pour produire un exécutable.

**Loader**  
Composant du système qui mappe le programme en mémoire et prépare son exécution.

**Mach-O**  
Format d’exécutable Apple.

**Opcode**  
Partie de l’instruction machine indiquant l’opération.

**Packing**  
Compression ou chiffrement d’un programme avec un stub de décompression.

**PE**  
Portable Executable, format Windows.

**PIE**  
Position-Independent Executable.

**PLT**  
Procedure Linkage Table, mécanisme d’appel des fonctions externes sous ELF.

**Relocation**  
Ajustement d’adresses lors du chargement ou de l’édition de liens.

**Section**  
Division logique d’un fichier objet ou exécutable.

**Segment**  
Zone mappée en mémoire par le loader.

**Signature**  
Motif connu permettant d’identifier une fonction, bibliothèque ou famille de code.

**Stack**  
Pile contenant notamment variables locales, adresses de retour et registres sauvegardés.

**Static analysis**  
Analyse d’un programme sans exécution normale.

**Stripped binary**  
Binaire dont une partie des symboles a été retirée.

**Symbol**  
Nom associé à une adresse ou à un objet.

---

# 41. Checklist finale du projet

## Avant l’analyse

- [ ] Je travaille dans une VM contrôlée.
- [ ] Je conserve une copie originale.
- [ ] J’ai calculé le SHA-256.
- [ ] Je connais le nom exact du fichier cible.
- [ ] Je n’utilise aucun service en ligne.

## Triage

- [ ] J’ai exécuté `file`.
- [ ] J’ai identifié format, architecture et endianness.
- [ ] J’ai extrait les strings avec offsets.
- [ ] J’ai inspecté les headers.
- [ ] J’ai inspecté les sections et segments.
- [ ] J’ai vérifié les imports et bibliothèques.
- [ ] J’ai vérifié les protections.

## Analyse approfondie

- [ ] J’ai identifié le point d’entrée.
- [ ] J’ai identifié ou reconstruit `main`.
- [ ] J’ai suivi les xrefs des strings intéressantes.
- [ ] J’ai étudié les fonctions de comparaison.
- [ ] J’ai reconstruit le CFG important.
- [ ] J’ai renommé les fonctions et variables.
- [ ] J’ai vérifié le pseudo-code avec l’assembleur.
- [ ] J’ai distingué faits et hypothèses.

## Tasks

- [ ] Task 0 : `0-flag.txt` contient uniquement la valeur attendue.
- [ ] Task 1 : la logique ou vulnérabilité est expliquée précisément.
- [ ] Task 2 : l’algorithme optimisé conserve le résultat et réduit la complexité.
- [ ] Task 3 : la transformation d’obfuscation est inversée localement.
- [ ] Task 4 : l’assembleur brut est traduit et expliqué instruction par instruction.

## Scripts

- [ ] Shebang correct.
- [ ] Scripts exécutables avec `chmod +x`.
- [ ] Aucun chemin absolu codé en dur.
- [ ] Entrées validées.
- [ ] Erreurs gérées.
- [ ] Fonctionnement testé sur Kali Linux.

## Documentation et Git

- [ ] Commandes documentées.
- [ ] Résultats organisés.
- [ ] Aucune donnée temporaire inutile committée.
- [ ] `git status` vérifié.
- [ ] Commit clair.
- [ ] Push effectué dans le bon repository.

---

# Conclusion

L’analyse statique ne consiste pas uniquement à ouvrir un binaire dans Ghidra et à lire du pseudo-code. C’est une méthode structurée : préserver le fichier, identifier son format, examiner ses données, comprendre ses dépendances, reconstruire ses fonctions, cartographier son contrôle de flux et documenter chaque conclusion.

Les outils automatisent une partie du travail, mais ils ne remplacent pas le raisonnement. `strings` peut révéler une piste, les xrefs montrent où elle est utilisée, le CFG explique sous quelles conditions elle est atteinte, le désassemblage confirme les opérations réelles et le décompilateur fournit une représentation plus lisible. La compétence centrale est de relier ces vues pour reconstruire fidèlement le comportement du programme.

Pour le projet `static_analysis`, la stratégie la plus efficace est donc :

```text
préserver → identifier → extraire → filtrer → suivre les xrefs
→ reconstruire le contrôle de flux → inverser les transformations
→ vérifier → documenter
```

