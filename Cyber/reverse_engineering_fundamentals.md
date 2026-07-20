# Reverse Engineering Fundamentals

> **“Every program tells a story. Reverse engineering is how you learn to read it.”**

Cours complet de rétro-ingénierie logicielle, orienté Linux, ELF, architecture x86/x86-64 et analyse locale de `target_binary`.

---

## Table des matières

1. [Introduction au reverse engineering](#1-introduction-au-reverse-engineering)
2. [Pourquoi le reverse engineering est important](#2-pourquoi-le-reverse-engineering-est-important)
3. [Cadre légal et environnement sécurisé](#3-cadre-légal-et-environnement-sécurisé)
4. [Du code source au binaire](#4-du-code-source-au-binaire)
5. [Concepts fondamentaux du processeur](#5-concepts-fondamentaux-du-processeur)
6. [Registres x86-64](#6-registres-x86-64)
7. [Flags du processeur](#7-flags-du-processeur)
8. [Mémoire virtuelle d’un processus](#8-mémoire-virtuelle-dun-processus)
9. [La pile](#9-la-pile)
10. [Syntaxes Intel et AT&T](#10-syntaxes-intel-et-att)
11. [Instructions assembleur essentielles](#11-instructions-assembleur-essentielles)
12. [Conventions d’appel](#12-conventions-dappel)
13. [Reconnaître une fonction](#13-reconnaître-une-fonction)
14. [Désassemblage](#14-désassemblage)
15. [Décompilation](#15-décompilation)
16. [Analyse statique et dynamique](#16-analyse-statique-et-dynamique)
17. [Débogueurs](#17-débogueurs)
18. [Analyse du contrôle de flux](#18-analyse-du-contrôle-de-flux)
19. [Call graph](#19-call-graph)
20. [Cross-references](#20-cross-references)
21. [Formats exécutables](#21-formats-exécutables)
22. [Symboles](#22-symboles)
23. [Imports et comportement probable](#23-imports-et-comportement-probable)
24. [PLT, GOT et résolution dynamique](#24-plt-got-et-résolution-dynamique)
25. [Relocations](#25-relocations)
26. [Analyse des données](#26-analyse-des-données)
27. [Outils de reverse engineering](#27-outils-de-reverse-engineering)
28. [Décompilateurs](#28-décompilateurs)
29. [Outils statiques complémentaires](#29-outils-statiques-complémentaires)
30. [Protections modernes](#30-protections-modernes)
31. [Anti-reverse engineering](#31-anti-reverse-engineering)
32. [Méthodologie de contournement anti-RE](#32-méthodologie-de-contournement-anti-re)
33. [Workflow complet pour target_binary](#33-workflow-complet-pour-target_binary)
34. [Script d’analyse reproductible](#34-script-danalyse-reproductible)
35. [Cas pratiques](#35-cas-pratiques)
36. [Méthode de raisonnement](#36-méthode-de-raisonnement)
37. [Erreurs classiques](#37-erreurs-classiques)
38. [Modèle de rapport](#38-modèle-de-rapport)
39. [Checklist](#39-checklist)
40. [Résumé des commandes](#40-résumé-des-commandes)
41. [Questions de révision](#41-questions-de-révision)
42. [Glossaire](#42-glossaire)
43. [Parcours d’apprentissage](#43-parcours-dapprentissage)
44. [Conclusion](#44-conclusion)

---

# 1. Introduction au reverse engineering

## 1.1 Définition

Le **reverse engineering**, ou **rétro-ingénierie logicielle**, consiste à étudier un programme sans disposer nécessairement de son code source afin de comprendre :

- sa structure ;
- son fonctionnement interne ;
- les données qu’il manipule ;
- ses interactions avec le système ;
- ses communications réseau ;
- ses mécanismes de protection ;
- ses fonctionnalités visibles ou cachées ;
- ses vulnérabilités potentielles.

Lorsqu’un développeur écrit un programme, il utilise généralement un langage de haut niveau comme C, C++, Rust ou Go. Le code est ensuite transformé en instructions compréhensibles par le processeur.

Le reverse engineering suit approximativement le chemin inverse :

```text
Code source
    ↓ compilation
Code machine
    ↓ désassemblage
Code assembleur
    ↓ analyse et décompilation
Pseudo-code proche du C
    ↓ raisonnement humain
Compréhension du programme
```

Le résultat ne permet généralement pas de retrouver exactement le code source d’origine. Les noms des variables, les commentaires, certaines informations de types et la structure initiale peuvent être perdus pendant la compilation.

L’objectif est donc de reconstruire le **comportement logique** du programme.

---

# 2. Pourquoi le reverse engineering est important

## 2.1 Analyse de malware

Un malware est rarement accompagné de son code source.

L’analyste doit déterminer :

- comment il s’exécute ;
- comment il obtient de la persistance ;
- quels fichiers il crée ;
- quelles informations il collecte ;
- quels serveurs il contacte ;
- quelles commandes il accepte ;
- comment il chiffre ou encode ses données ;
- comment il évite la détection ;
- comment le neutraliser.

Exemple de comportement reconstruit :

```text
Démarrage
    ↓
Collecte du nom de la machine
    ↓
Collecte de l’utilisateur connecté
    ↓
Collecte d’informations système
    ↓
Création d’une connexion réseau
    ↓
Envoi des données vers un serveur externe
```

## 2.2 Recherche de vulnérabilités

Le reverse engineering permet de rechercher :

- des dépassements de tampon ;
- des erreurs de validation ;
- des mots de passe codés en dur ;
- des fonctions dangereuses ;
- des erreurs de gestion mémoire ;
- des contrôles d’autorisation insuffisants ;
- des chemins d’exécution non documentés ;
- des mécanismes cryptographiques faibles ;
- des fonctions administratives cachées.

## 2.3 Incident response

Dans une investigation, l’analyste peut devoir déterminer :

- si un fichier est légitime ;
- si un programme a été modifié ;
- quelle fonctionnalité a été utilisée par un attaquant ;
- quelles données ont potentiellement été compromises ;
- quels indicateurs de compromission rechercher.

## 2.4 Compatibilité logicielle

La rétro-ingénierie peut permettre :

- de recréer un format de fichier non documenté ;
- de rendre un ancien programme compatible avec un nouveau système ;
- de comprendre un protocole propriétaire ;
- de développer un logiciel interopérable ;
- d’analyser un pilote ou un firmware ancien.

## 2.5 CTF

Dans un challenge CTF, le reverse engineering sert souvent à retrouver :

- un mot de passe ;
- une clé ;
- un flag caché ;
- une fonction non appelée ;
- un algorithme de validation ;
- une valeur calculée ;
- une chaîne encodée ;
- une condition à contourner.

---

# 3. Cadre légal et environnement sécurisé

Le reverse engineering doit être effectué uniquement sur :

- un programme dont tu es propriétaire ;
- un programme fourni dans un laboratoire ;
- un challenge CTF ;
- un environnement scolaire autorisé ;
- un logiciel pour lequel tu disposes d’une autorisation explicite.

## 3.1 Pourquoi utiliser une VM ?

Un binaire inconnu peut :

- modifier des fichiers ;
- voler des données ;
- effectuer des connexions réseau ;
- créer des processus ;
- installer une persistance ;
- tenter une élévation de privilèges ;
- détecter l’environnement d’analyse ;
- exploiter une vulnérabilité du système.

Il faut donc privilégier :

- une VM Kali dédiée ;
- un snapshot avant l’analyse ;
- aucun dossier partagé avec l’hôte ;
- aucun presse-papiers partagé ;
- aucune donnée personnelle ;
- un réseau désactivé ou isolé ;
- un compte sans privilèges ;
- une copie de travail du fichier ;
- une conservation intacte de l’original.

## 3.2 Principe fondamental

> **Ne jamais exécuter directement un binaire inconnu simplement pour voir ce qu’il fait.**

L’analyse doit commencer par des techniques statiques.

## 3.3 Attention à `ldd`

`ldd` affiche les bibliothèques partagées nécessaires à un programme. Cependant, sur un binaire non fiable, son utilisation peut présenter un risque.

Pour examiner les dépendances directes sans lancer `ldd`, utiliser d’abord :

```bash
objdump -p ./target_binary
```

Puis rechercher les entrées :

```text
NEEDED
```

---

# 4. Du code source au binaire

## 4.1 Préprocesseur

Pour un programme C, le préprocesseur traite notamment :

```c
#include <stdio.h>
#define MAX_SIZE 100
```

Il développe :

- les macros ;
- les inclusions de fichiers ;
- les compilations conditionnelles.

## 4.2 Compilateur

Le compilateur transforme le code source en représentation intermédiaire puis en code assembleur.

Il peut appliquer des optimisations :

- suppression de code inutilisé ;
- intégration de fonctions ;
- réorganisation des conditions ;
- déroulement de boucles ;
- simplification des expressions ;
- suppression de variables ;
- propagation de constantes.

## 4.3 Assembleur

L’assembleur transforme les instructions textuelles en opcodes.

```asm
mov eax, 1
ret
```

peut devenir :

```text
b8 01 00 00 00 c3
```

## 4.4 Linker

Le linker assemble :

- les fichiers objets ;
- les bibliothèques ;
- les symboles ;
- les références externes ;
- les informations de relocation.

Exemple :

```c
printf("Hello\n");
```

L’implémentation de `printf` se trouve généralement dans une bibliothèque externe.

## 4.5 Loader

Au lancement, le chargeur :

1. lit le format du fichier ;
2. mappe ses segments en mémoire ;
3. charge les bibliothèques nécessaires ;
4. applique les relocations ;
5. prépare la pile ;
6. initialise l’environnement ;
7. transfère l’exécution au point d’entrée.

---

# 5. Concepts fondamentaux du processeur

## 5.1 Bits et octets

```text
1 byte  = 8 bits
1 word  = 16 bits
1 dword = 32 bits
1 qword = 64 bits
```

```asm
mov al, 1      ; 8 bits
mov ax, 1      ; 16 bits
mov eax, 1     ; 32 bits
mov rax, 1     ; 64 bits
```

## 5.2 Hexadécimal

```text
Décimal :      255
Hexadécimal :  0xff
Binaire :      11111111
```

Quelques valeurs importantes :

```text
0x10 = 16
0x20 = 32
0x40 = 64
0xff = 255
```

Une adresse comme `0x4011f6` désigne une position dans l’espace d’adressage virtuel.

## 5.3 Endianness

Les processeurs x86 et x86-64 utilisent généralement le **little endian**.

La valeur :

```text
0x12345678
```

est stockée en mémoire comme :

```text
78 56 34 12
```

---

# 6. Registres x86-64

Les registres sont de petites zones de stockage intégrées au processeur.

| Registre | Rôle fréquent |
|---|---|
| `RAX` | Accumulateur et valeur de retour |
| `RBX` | Registre général, souvent préservé |
| `RCX` | Compteur ou argument |
| `RDX` | Donnée ou argument |
| `RSI` | Source ou argument |
| `RDI` | Destination ou argument |
| `RBP` | Base de la stack frame |
| `RSP` | Pointeur de pile |
| `R8-R9` | Arguments ou registres généraux |
| `R10-R11` | Registres temporaires |
| `R12-R15` | Registres généralement préservés |

## 6.1 Sous-registres

```text
RAX   64 bits
EAX   32 bits
AX    16 bits
AH     8 bits
AL     8 bits
```

```asm
mov eax, 5
```

Sur x86-64, écrire dans `eax` remet généralement à zéro la partie haute de `rax`.

## 6.2 RIP

`RIP` contient l’adresse de la prochaine instruction.

```asm
lea rdi, [rip+0xe5a]
```

Cette instruction calcule une adresse relative à l’instruction courante.

## 6.3 RSP et RBP

```asm
push rbp
mov rbp, rsp
sub rsp, 0x20
```

Interprétation :

1. sauvegarder l’ancien `rbp` ;
2. créer une nouvelle stack frame ;
3. réserver 32 octets pour les variables locales.

---

# 7. Flags du processeur

Le registre `RFLAGS` contient notamment :

```text
ZF  Zero Flag
CF  Carry Flag
SF  Sign Flag
OF  Overflow Flag
PF  Parity Flag
```

Exemple :

```asm
cmp eax, 5
je equal
```

`cmp eax, 5` met à jour les flags comme si le processeur calculait :

```text
eax - 5
```

Si `eax == 5`, le résultat est zéro et `ZF = 1`.

---

# 8. Mémoire virtuelle d’un processus

Représentation simplifiée :

```text
Adresses hautes
+------------------------------+
| Stack                        |
| Variables locales            |
| Arguments                    |
+------------------------------+
| Bibliothèques partagées      |
+------------------------------+
| Heap                         |
| Allocations dynamiques       |
+------------------------------+
| .bss                         |
| Données non initialisées     |
+------------------------------+
| .data                        |
| Données modifiables          |
+------------------------------+
| .rodata                      |
| Constantes et chaînes        |
+------------------------------+
| .text                        |
| Code exécutable              |
+------------------------------+
Adresses basses
```

---

# 9. La pile

La pile fonctionne selon le principe **Last In, First Out**.

## 9.1 `push`

```asm
push rax
```

Conceptuellement :

```text
rsp = rsp - 8
[rsp] = rax
```

## 9.2 `pop`

```asm
pop rax
```

Conceptuellement :

```text
rax = [rsp]
rsp = rsp + 8
```

## 9.3 `call`

```asm
call function
```

L’instruction :

1. place l’adresse de retour sur la pile ;
2. transfère l’exécution à la fonction.

## 9.4 `ret`

```asm
ret
```

L’instruction récupère l’adresse de retour et y transfère l’exécution.

---

# 10. Syntaxes Intel et AT&T

## 10.1 Syntaxe AT&T

```asm
mov %rax, %rbx
```

La source est à gauche, la destination à droite.

## 10.2 Syntaxe Intel

```asm
mov rbx, rax
```

La destination est à gauche, la source à droite.

Pour afficher la syntaxe Intel :

```bash
objdump -d -M intel ./target_binary
```

---

# 11. Instructions assembleur essentielles

## 11.1 `mov`

```asm
mov eax, 5
mov rax, rbx
mov eax, DWORD PTR [rbp-4]
```

Les crochets représentent un accès mémoire.

```asm
mov eax, [rbp-4]
```

signifie : lire la valeur située à l’adresse `rbp - 4`.

## 11.2 `lea`

```asm
lea rax, [rbp-4]
```

calcule l’adresse `rbp - 4`.

```asm
lea eax, [rdi+rdi*4]
```

calcule :

```text
eax = rdi * 5
```

## 11.3 Arithmétique

```asm
add eax, 5
sub eax, 2
inc eax
dec eax
imul eax, eax, 4
```

## 11.4 XOR

```asm
xor eax, eax
```

met `eax` à zéro.

```asm
xor al, 0x41
```

applique un XOR avec `0x41`.

## 11.5 AND et OR

```asm
and eax, 0xff
or eax, 1
```

## 11.6 Décalages

```asm
shl eax, 1
shr eax, 1
```

Dans de nombreux cas :

```text
shl eax, 1 ≈ multiplication par 2
shr eax, 1 ≈ division non signée par 2
```

## 11.7 `cmp`

```asm
cmp eax, 10
```

met à jour les flags comme si `eax - 10` était calculé.

## 11.8 `test`

```asm
test eax, eax
```

teste souvent si `eax` vaut zéro.

```asm
call strcmp
test eax, eax
je password_correct
```

Pseudo-code :

```c
if (strcmp(input, expected) == 0) {
    password_correct();
}
```

## 11.9 Sauts

| Instruction | Signification |
|---|---|
| `jmp` | saut inconditionnel |
| `je` / `jz` | égal ou zéro |
| `jne` / `jnz` | différent ou non nul |
| `jg` | supérieur, signé |
| `jge` | supérieur ou égal, signé |
| `jl` | inférieur, signé |
| `jle` | inférieur ou égal, signé |
| `ja` | supérieur, non signé |
| `jae` | supérieur ou égal, non signé |
| `jb` | inférieur, non signé |
| `jbe` | inférieur ou égal, non signé |

## 11.10 Extensions de valeurs

```asm
movzx eax, byte ptr [rbp-1]
movsx eax, byte ptr [rbp-1]
```

`movzx` complète avec des zéros.

`movsx` propage le bit de signe.

---

# 12. Conventions d’appel

## 12.1 System V AMD64

Sous Linux x86-64 :

```text
1er argument : RDI
2e argument  : RSI
3e argument  : RDX
4e argument  : RCX
5e argument  : R8
6e argument  : R9
```

Valeur de retour :

```text
RAX
```

Exemple :

```c
result = compare(a, b, c);
```

peut correspondre à :

```asm
mov rdi, a
mov rsi, b
mov rdx, c
call compare
```

## 12.2 Windows x64

```text
1er argument : RCX
2e argument  : RDX
3e argument  : R8
4e argument  : R9
```

Le code appelant réserve généralement 32 octets de **shadow space**.

## 12.3 x86 32 bits

Exemple `cdecl` :

```asm
push second_argument
push first_argument
call function
add esp, 8
```

---

# 13. Reconnaître une fonction

## 13.1 Prologue classique

```asm
push rbp
mov rbp, rsp
sub rsp, 0x20
```

## 13.2 Épilogue classique

```asm
leave
ret
```

`leave` correspond approximativement à :

```asm
mov rsp, rbp
pop rbp
```

## 13.3 Limites

Une fonction optimisée peut être :

```asm
lea eax, [rdi+rdi]
ret
```

Elle ne contient ni prologue classique ni stack frame.

## 13.4 Techniques d’identification

Une fonction peut être identifiée grâce à :

1. la table des symboles ;
2. une cible de `call` ;
3. un point d’entrée ;
4. un prologue connu ;
5. des informations DWARF ;
6. des informations d’unwind ;
7. une relocation ;
8. une entrée dans la PLT ;
9. une table de pointeurs ;
10. une fonction d’initialisation ;
11. une fonction de finalisation ;
12. des heuristiques de contrôle de flux ;
13. un `ret` cohérent ;
14. l’alignement du code ;
15. les références croisées.

---

# 14. Désassemblage

Le désassemblage transforme le code machine en instructions assembleur.

Entrée :

```text
83 ff 2a 0f 94 c0 0f b6 c0 c3
```

Sortie :

```asm
cmp edi, 0x2a
sete al
movzx eax, al
ret
```

## 14.1 Désassemblage linéaire

Le désassembleur lit les octets les uns après les autres.

Avantage :

- simple ;
- couverture importante.

Limite :

- risque d’interpréter des données comme du code.

## 14.2 Désassemblage récursif

Le désassembleur part d’un point d’entrée et suit :

- les branches ;
- les appels ;
- les cibles connues ;
- les chemins accessibles.

Limites :

- appels indirects ;
- code non référencé ;
- obfuscation ;
- fonctions cachées.

## 14.3 Commandes `objdump`

```bash
objdump -f ./target_binary
objdump -h ./target_binary
objdump -p ./target_binary
objdump -d -M intel ./target_binary
objdump -D -M intel ./target_binary
objdump -d -M intel --disassemble=main ./target_binary
objdump -t ./target_binary
objdump -T ./target_binary
objdump -r ./target_binary
objdump -R ./target_binary
objdump -s -j .rodata ./target_binary
```

---

# 15. Décompilation

La décompilation transforme l’assembleur en pseudo-code.

Assembleur :

```asm
cmp edi, 0x2a
sete al
movzx eax, al
ret
```

Pseudo-code :

```c
return value == 42;
```

## 15.1 Désassemblage contre décompilation

| Désassemblage | Décompilation |
|---|---|
| Produit de l’assembleur | Produit du pseudo-code |
| Très proche du code machine | Plus proche du raisonnement humain |
| Montre les registres et flags | Reconstruit variables et conditions |
| Très précis sur les instructions | Peut faire des hypothèses |
| Plus difficile à lire | Plus accessible |

## 15.2 Limites

Un décompilateur ne retrouve généralement pas :

- les commentaires ;
- les noms originaux ;
- les macros ;
- les noms exacts des structures ;
- le code supprimé par optimisation ;
- la logique exacte du code source initial.

> Le pseudo-code est une interprétation plausible du binaire, pas le code source original.

---

# 16. Analyse statique et dynamique

## 16.1 Analyse statique

Elle examine le fichier sans exécuter sa logique.

Elle permet d’étudier :

- les en-têtes ;
- les sections ;
- les segments ;
- les symboles ;
- les chaînes ;
- les imports ;
- les exports ;
- les relocations ;
- le point d’entrée ;
- le code assembleur ;
- les protections ;
- le contrôle de flux.

Avantages :

- plus sûre ;
- reproductible ;
- permet une vue globale ;
- permet d’étudier plusieurs chemins.

Limites :

- code chiffré ou compressé ;
- code généré dynamiquement ;
- appels indirects ;
- absence des valeurs réelles.

## 16.2 Analyse dynamique

Elle observe le programme pendant son exécution.

Elle permet de voir :

- les registres ;
- la mémoire ;
- les arguments ;
- les valeurs calculées ;
- les fichiers ouverts ;
- les branches suivies ;
- le code décompressé en mémoire.

Limites :

- un seul chemin à la fois ;
- anti-debugging ;
- comportement dépendant de l’environnement ;
- risque d’exécution.

---

# 17. Débogueurs

## 17.1 Rôle d’un debugger

Un debugger permet de :

- suspendre le programme ;
- placer un breakpoint ;
- exécuter instruction par instruction ;
- inspecter les registres ;
- inspecter la mémoire ;
- modifier une valeur ;
- suivre les appels ;
- observer la pile ;
- surveiller une adresse.

## 17.2 GDB

GDB est largement utilisé sous Linux.

Il permet :

- les breakpoints ;
- le step-in ;
- le step-over ;
- l’examen mémoire ;
- l’examen des registres ;
- l’analyse de core dumps.

Dans ce projet, seuls `objdump`, `readelf` et `ldd` sont autorisés pour l’analyse pratique.

## 17.3 x64dbg

x64dbg est un debugger Windows adapté aux programmes :

- x86 ;
- x86-64 ;
- PE ;
- DLL Windows.

## 17.4 OllyDbg

OllyDbg est un debugger historique orienté Windows 32 bits et analyse assembleur.

---

# 18. Analyse du contrôle de flux

## 18.1 Basic block

Un **basic block** est une suite d’instructions :

- avec une seule entrée ;
- une seule sortie ;
- sans branche au milieu.

```asm
mov eax, edi
add eax, 5
cmp eax, 10
jne fail
```

## 18.2 Control Flow Graph

Un CFG représente :

- les basic blocks sous forme de nœuds ;
- les transitions sous forme d’arêtes.

```text
             +----------------+
             | input == 42 ?  |
             +-------+--------+
                     |
            +--------+--------+
            |                 |
           oui               non
            |                 |
   +--------v------+   +------v-------+
   | afficher flag |   | afficher fail|
   +--------+------+   +------+-------+
            |                 |
            +--------+--------+
                     |
                  +--v--+
                  | fin |
                  +-----+
```

## 18.3 Condition

```asm
cmp DWORD PTR [rbp-4], 0x2a
jne fail
```

Pseudo-code :

```c
if (value != 42) {
    goto fail;
}
```

## 18.4 Boucle

```asm
mov DWORD PTR [rbp-4], 0

loop_start:
cmp DWORD PTR [rbp-4], 10
jge loop_end

add DWORD PTR [rbp-4], 1
jmp loop_start

loop_end:
```

Pseudo-code :

```c
for (int i = 0; i < 10; i++) {
}
```

## 18.5 Switch et jump table

```asm
cmp eax, 4
ja default_case

lea rdx, [rip+jump_table]
movsxd rax, DWORD PTR [rdx+rax*4]
add rax, rdx
jmp rax
```

---

# 19. Call graph

Un call graph représente les relations entre fonctions.

```text
main
 ├── read_input
 ├── validate_input
 │    ├── strlen
 │    ├── transform
 │    └── memcmp
 └── print_result
```

Différence :

```text
CFG        : flux entre les blocs d’une fonction
Call graph : relations d’appel entre plusieurs fonctions
```

---

# 20. Cross-references

Une cross-reference, ou XREF, indique qu’un élément est référencé ailleurs.

## 20.1 Code XREF

```asm
call secret_function
```

## 20.2 Data XREF

```asm
mov eax, DWORD PTR [rip+global_counter]
```

## 20.3 String XREF

```asm
lea rdi, [rip+access_granted]
call puts
```

Méthode :

```text
Chaîne intéressante
    ↓
Référence vers la chaîne
    ↓
Fonction utilisant la chaîne
    ↓
Condition menant à cette fonction
    ↓
Algorithme de validation
```

## 20.4 Import XREF

```text
connect
   ↑
send_system_info
   ↑
collect_and_send
   ↑
main
```

---

# 21. Formats exécutables

## 21.1 ELF

ELF signifie **Executable and Linkable Format**.

Il est principalement utilisé sur Linux et plusieurs systèmes Unix.

Structure générale :

```text
+----------------------------+
| ELF Header                 |
+----------------------------+
| Program Header Table       |
+----------------------------+
| Sections                   |
| .text                      |
| .rodata                    |
| .data                      |
| .bss                       |
| .dynsym                    |
| .dynstr                    |
| .rela.plt                  |
| .got                       |
| .plt                       |
+----------------------------+
| Section Header Table       |
+----------------------------+
```

### ELF Header

```bash
readelf -hW ./target_binary
```

Champs importants :

```text
Class
Data
Type
Machine
Entry point address
Start of program headers
Start of section headers
Number of program headers
Number of section headers
```

Types importants :

```text
ET_REL   Fichier objet relogeable
ET_EXEC  Exécutable classique
ET_DYN   Bibliothèque partagée ou exécutable PIE
ET_CORE  Core dump
```

### Sections contre segments

Les sections sont principalement utiles au linker et aux outils d’analyse.

Les segments sont utilisés par le loader pour mapper le programme en mémoire.

```bash
readelf -lW ./target_binary
```

### Sections ELF importantes

| Section | Rôle |
|---|---|
| `.text` | code exécutable |
| `.rodata` | constantes et chaînes |
| `.data` | données modifiables initialisées |
| `.bss` | données non initialisées |
| `.plt` | trampolines vers fonctions importées |
| `.got` | adresses résolues dynamiquement |
| `.dynsym` | symboles dynamiques |
| `.dynstr` | chaînes des symboles dynamiques |
| `.rela.plt` | relocations de la PLT |
| `.rela.dyn` | autres relocations dynamiques |
| `.init_array` | fonctions exécutées avant `main` |
| `.fini_array` | fonctions exécutées à la terminaison |

Commandes :

```bash
readelf -SW ./target_binary
readelf -sW ./target_binary
readelf --dyn-syms -W ./target_binary
readelf -rW ./target_binary
readelf -dW ./target_binary
readelf -nW ./target_binary
readelf -p .rodata ./target_binary
```

## 21.2 PE

PE signifie **Portable Executable**.

Il est utilisé par Windows pour :

- les `.exe` ;
- les `.dll` ;
- les pilotes ;
- certains exécutables UEFI.

Structure simplifiée :

```text
+----------------------------+
| DOS Header                 |
| Signature MZ               |
+----------------------------+
| DOS Stub                   |
+----------------------------+
| PE Signature               |
| PE\0\0                     |
+----------------------------+
| COFF File Header           |
+----------------------------+
| Optional Header            |
+----------------------------+
| Data Directories           |
+----------------------------+
| Section Table              |
+----------------------------+
| .text                      |
| .rdata                     |
| .data                      |
| .rsrc                      |
| .reloc                     |
+----------------------------+
```

Éléments importants :

- DOS Header ;
- PE Signature ;
- COFF Header ;
- Optional Header ;
- Import Table ;
- Export Table ;
- Resource Table ;
- TLS callbacks ;
- Relocation Table.

## 21.3 Mach-O

Mach-O signifie **Mach Object**.

Il est utilisé sur les plateformes Apple.

Structure générale :

```text
Mach Header
Load Commands
Segments
Sections
Symbol information
Relocations
Code and data
```

Load commands fréquentes :

```text
LC_SEGMENT_64
LC_MAIN
LC_LOAD_DYLIB
LC_SYMTAB
LC_DYSYMTAB
LC_CODE_SIGNATURE
```

Un binaire universel peut contenir plusieurs architectures :

```text
x86-64
ARM64
```

---

# 22. Symboles

Un symbole associe une adresse à un nom.

```text
0000000000401136 main
0000000000401180 check_password
00000000004011e0 print_flag
```

## 22.1 Binaire non stripped

Il peut conserver :

- les noms de fonctions ;
- les noms de variables globales ;
- certains symboles locaux ;
- des informations de debug.

## 22.2 Binaire stripped

Les noms peuvent être remplacés par :

```text
sub_401136
FUN_00101136
fcn.00401136
```

Il faut reconstruire leur rôle :

```text
sub_401136
    ↓
validate_password
```

Les symboles dynamiques peuvent rester visibles :

```text
puts
strcmp
malloc
socket
connect
```

---

# 23. Imports et comportement probable

## Entrées utilisateur

```text
scanf
fgets
read
getchar
```

## Comparaisons

```text
strcmp
strncmp
memcmp
```

## Fichiers

```text
open
fopen
read
write
unlink
rename
```

## Mémoire

```text
malloc
calloc
realloc
free
mmap
mprotect
```

## Processus

```text
fork
execve
system
popen
```

## Réseau

```text
socket
connect
bind
listen
accept
send
recv
getaddrinfo
```

## Informations système

```text
gethostname
uname
getuid
getenv
```

Exemple d’hypothèse :

```text
Imports :
gethostname
socket
connect
send

Hypothèse :
le programme récupère le nom de la machine et peut l’envoyer sur le réseau.
```

Cette hypothèse doit être confirmée par le contrôle de flux et le suivi des données.

---

# 24. PLT, GOT et résolution dynamique

```asm
call puts@plt
```

Représentation simplifiée :

```text
Code du programme
      ↓
puts@plt
      ↓
Entrée dans la GOT
      ↓
Adresse réelle de puts dans libc
```

La PLT agit comme un trampoline.

La GOT contient les adresses résolues dynamiquement.

---

# 25. Relocations

Une relocation indique qu’une adresse doit être ajustée pendant le linking ou le chargement.

Elle peut révéler :

- les fonctions importées ;
- les variables globales ;
- les pointeurs de fonction ;
- les tables d’adresses ;
- les références vers la GOT.

Commandes :

```bash
readelf -rW ./target_binary
objdump -R ./target_binary
```

---

# 26. Analyse des données

## 26.1 Chaînes

Les chaînes peuvent révéler :

- des messages d’erreur ;
- des chemins ;
- des noms de fichiers ;
- des domaines ;
- des adresses IP ;
- des commandes ;
- des clés ;
- des messages de succès ;
- des fonctionnalités cachées.

Attention : une chaîne ne prouve pas qu’un comportement est exécuté.

## 26.2 Chaînes dans `.rodata`

```bash
readelf -p .rodata ./target_binary
```

ou :

```bash
objdump -s -j .rodata ./target_binary
```

## 26.3 Données encodées

Une valeur peut être stockée sous forme de :

- XOR ;
- Base64 ;
- hexadécimal ;
- tableau d’octets ;
- addition ou soustraction ;
- rotation de bits ;
- compression ;
- chiffrement.

---

# 27. Outils de reverse engineering

## 27.1 IDA Pro

Forces :

- analyse mature ;
- nombreuses architectures ;
- excellente navigation ;
- XREFs ;
- graphes ;
- système de types ;
- scripts et plugins ;
- intégration Hex-Rays.

Limites :

- coût ;
- certaines fonctions dépendent de la licence.

## 27.2 Ghidra

Forces :

- gratuit ;
- open source ;
- décompilateur intégré ;
- nombreuses architectures ;
- graphes ;
- types ;
- références croisées ;
- scripts ;
- analyse automatique.

Limites :

- interface dense ;
- consommation mémoire ;
- corrections manuelles fréquentes.

## 27.3 Radare2

Forces :

- libre ;
- ligne de commande ;
- très scriptable ;
- léger ;
- nombreuses architectures ;
- adapté à l’automatisation.

Limites :

- courbe d’apprentissage élevée ;
- syntaxe moins intuitive.

## 27.4 Comparaison

| Outil | Modèle | Point fort |
|---|---|---|
| IDA Pro | Commercial | Maturité et écosystème |
| Ghidra | Gratuit / open source | Décompilateur intégré |
| Radare2 | Gratuit / CLI | Scripts et automatisation |
| objdump | GNU CLI | Désassemblage rapide |
| readelf | GNU CLI | Analyse détaillée ELF |

---

# 28. Décompilateurs

## 28.1 Hex-Rays

Hex-Rays transforme le code désassemblé en pseudo-code proche du C.

L’analyste peut :

- renommer les variables ;
- modifier les types ;
- ajouter des commentaires ;
- reconstruire des structures ;
- naviguer entre assembleur et pseudo-code.

## 28.2 Ghidra Decompiler

Il reconstruit :

- les variables ;
- les expressions ;
- les conditions ;
- les boucles ;
- les appels ;
- certains types ;
- les paramètres.

## 28.3 RetDec

RetDec est un décompilateur basé sur LLVM et conçu pour supporter plusieurs architectures.

## 28.4 Principe simplifié

```text
Octets
  ↓
Instructions machine
  ↓
Représentation intermédiaire
  ↓
Analyse des registres et données
  ↓
Reconstruction du contrôle de flux
  ↓
Reconstruction des variables
  ↓
Reconnaissance des types
  ↓
Pseudo-code
```

---

# 29. Outils statiques complémentaires

## 29.1 Strings

Un outil de type `strings` recherche les séquences de caractères imprimables.

Il peut révéler :

- des messages ;
- des URLs ;
- des chemins ;
- des commandes ;
- des identifiants ;
- des noms de fonctions ;
- des flags.

Limites :

- faux positifs ;
- chaînes inutilisées ;
- données chiffrées invisibles ;
- absence de contexte ;
- chaînes leurres.

## 29.2 Binwalk

Binwalk est utilisé pour examiner :

- les firmwares ;
- les systèmes de fichiers intégrés ;
- les signatures de compression ;
- les archives incorporées ;
- les données concaténées.

```text
Firmware
 ├── Bootloader
 ├── Kernel
 ├── SquashFS
 ├── Configuration
 └── Certificats
```

---

# 30. Protections modernes

## 30.1 NX / DEP

Une zone mémoire non exécutable ne doit pas permettre l’exécution de code.

```bash
readelf -lW ./target_binary
```

Rechercher :

```text
GNU_STACK
```

Une pile `RW` mais non `E` est non exécutable.

## 30.2 PIE

PIE signifie **Position Independent Executable**.

Un ELF `ET_DYN` peut être :

- une bibliothèque partagée ;
- un exécutable PIE.

## 30.3 ASLR

ASLR randomise les adresses de :

- l’exécutable PIE ;
- la pile ;
- le heap ;
- les bibliothèques ;
- certaines mappings mémoire.

## 30.4 Stack canary

La présence de :

```asm
call __stack_chk_fail@plt
```

suggère une protection contre les corruptions de pile.

## 30.5 RELRO

On distingue :

- No RELRO ;
- Partial RELRO ;
- Full RELRO.

Examiner :

```bash
readelf -lW ./target_binary
readelf -dW ./target_binary
```

Rechercher :

```text
GNU_RELRO
BIND_NOW
FLAGS
FLAGS_1
```

## 30.6 Stripping

Le stripping retire des symboles et informations non indispensables.

Il complique :

- l’identification des fonctions ;
- la compréhension des variables ;
- la navigation ;
- le repérage de `main`.

Il ne chiffre pas le code.

---

# 31. Anti-reverse engineering

## 31.1 Obfuscation

Techniques :

- symboles supprimés ;
- calculs complexes ;
- conditions opaques ;
- contrôle de flux aplati ;
- blocs inutiles ;
- chaînes encodées ;
- appels indirects ;
- fonctions fragmentées ;
- code mort.

## 31.2 Opaque predicates

```c
if (((x * x + x) % 2) == 0) {
    real_code();
} else {
    fake_code();
}
```

Pour tout entier `x`, `x² + x` est pair. La branche `else` est donc impossible.

## 31.3 Control-flow flattening

```text
        +------------+
        | dispatcher |
        +------+-----+
               |
     +---------+---------+
     |         |         |
   état 1    état 2    état 3
     |         |         |
     +---------+---------+
               |
        retour dispatcher
```

## 31.4 Packing

Un packer compresse ou chiffre le programme original.

Signes possibles :

- point d’entrée inhabituel ;
- sections aux noms étranges ;
- très peu d’imports ;
- section modifiable et exécutable ;
- données de grande taille ;
- transfert vers une zone préparée en mémoire.

## 31.5 Anti-debugging

Techniques courantes :

- `ptrace` sous Linux ;
- `IsDebuggerPresent` sous Windows ;
- mesure du temps ;
- détection de breakpoints ;
- vérification des registres de debug ;
- exceptions ;
- détection de processus d’analyse.

## 31.6 Anti-disassembly

Techniques :

- mélange de code et de données ;
- instructions se chevauchant ;
- sauts vers le milieu d’une instruction ;
- faux opcodes ;
- blocs inaccessibles ;
- destinations calculées ;
- self-modifying code.

## 31.7 Anti-VM

Recherche possible de :

- pilotes VMware ou VirtualBox ;
- noms de périphériques virtuels ;
- adresses MAC spécifiques ;
- processus d’analyse ;
- faible quantité de RAM ;
- nombre réduit de processeurs ;
- artefacts matériels.

---

# 32. Méthodologie de contournement anti-RE

## 32.1 Identifier précisément la protection

Éviter les conclusions vagues.

Mauvais :

```text
Le binaire est obfusqué.
```

Meilleur :

```text
Le point d’entrée ne mène pas directement à main.
Le programme importe ptrace.
Les chaînes principales sont construites à l’exécution.
```

## 32.2 Localiser la décision

```asm
call anti_debug_check
test eax, eax
jne terminate
```

Il faut comprendre :

- la valeur retournée ;
- le flag testé ;
- la branche de succès ;
- la branche de terminaison.

## 32.3 Neutraliser dans une copie

Approches conceptuelles :

- modifier le résultat retourné ;
- inverser une branche ;
- ignorer un appel ;
- patcher la fonction ;
- arrêter l’exécution après le déchiffrement ;
- extraire la mémoire reconstruite.

L’original doit rester intact.

## 32.4 Contourner un packer

```text
Identifier le stub
    ↓
Trouver la boucle de décompression
    ↓
Observer la destination mémoire
    ↓
Attendre que le code soit reconstruit
    ↓
Identifier le vrai point d’entrée
    ↓
Extraire la représentation décompressée
    ↓
Recommencer l’analyse statique
```

## 32.5 Simplifier l’obfuscation

1. identifier les blocs réels ;
2. marquer le code mort ;
3. suivre les variables d’état ;
4. renommer les fonctions ;
5. reconstruire les conditions ;
6. dessiner le flux ;
7. valider chaque hypothèse ;
8. produire un pseudo-code simplifié.

---

# 33. Workflow complet pour `target_binary`

## Phase 0 — Préparation

```text
re_lab/
├── original/
│   └── target_binary
├── working/
│   └── target_binary
├── output/
├── notes/
└── analyze.sh
```

```bash
mkdir -p ./re_lab/original
mkdir -p ./re_lab/working
mkdir -p ./re_lab/output
mkdir -p ./re_lab/notes

cp ./target_binary ./re_lab/original/target_binary
cp ./re_lab/original/target_binary ./re_lab/working/target_binary

chmod a-x ./re_lab/original/target_binary
chmod a-x ./re_lab/working/target_binary
```

## Phase 1 — Intégrité

```bash
sha256sum ./re_lab/original/target_binary
sha256sum ./re_lab/working/target_binary
```

Les deux résultats doivent être identiques.

```bash
sha256sum ./re_lab/original/target_binary \
  > ./re_lab/notes/original.sha256
```

## Phase 2 — Identification du format

```bash
readelf -hW ./re_lab/working/target_binary
```

Questions :

- ELF valide ?
- 32 ou 64 bits ?
- little endian ou big endian ?
- quelle architecture ?
- `ET_EXEC`, `ET_DYN` ou autre ?
- quel point d’entrée ?
- section headers présents ?

## Phase 3 — Étude des segments

```bash
readelf -lW ./re_lab/working/target_binary
```

Rechercher :

- `LOAD` ;
- permissions `R`, `W`, `E` ;
- `INTERP` ;
- `DYNAMIC` ;
- `GNU_STACK` ;
- `GNU_RELRO`.

## Phase 4 — Étude des sections

```bash
readelf -SW ./re_lab/working/target_binary
```

Rechercher :

- `.text` ;
- `.rodata` ;
- `.data` ;
- `.bss` ;
- `.plt` ;
- `.got` ;
- `.dynsym` ;
- `.dynstr` ;
- `.rela.plt` ;
- `.init_array` ;
- `.fini_array`.

## Phase 5 — Symboles

```bash
readelf -sW ./re_lab/working/target_binary
readelf --dyn-syms -W ./re_lab/working/target_binary
```

Rechercher :

- `main` ;
- fonctions personnalisées ;
- `puts`, `printf`, `strcmp`, `memcmp` ;
- fonctions réseau ;
- fonctions fichiers ;
- fonctions processus ;
- `__stack_chk_fail`.

Si `main` existe :

```bash
objdump -d -M intel \
  --disassemble=main \
  ./re_lab/working/target_binary
```

## Phase 6 — Dépendances

Pour un binaire non fiable :

```bash
objdump -p ./re_lab/working/target_binary
```

Rechercher :

```text
NEEDED
```

`ldd` ne doit être utilisé que dans une VM isolée :

```bash
ldd ./re_lab/working/target_binary
```

## Phase 7 — Relocations

```bash
readelf -rW ./re_lab/working/target_binary
objdump -R ./re_lab/working/target_binary
```

## Phase 8 — Données et chaînes

```bash
readelf -p .rodata ./re_lab/working/target_binary
objdump -s -j .rodata ./re_lab/working/target_binary
```

## Phase 9 — Désassemblage global

```bash
objdump -d -M intel \
  ./re_lab/working/target_binary \
  > ./re_lab/output/disassembly.txt
```

Avec offsets de fichier :

```bash
objdump -d -M intel -F \
  ./re_lab/working/target_binary \
  > ./re_lab/output/disassembly_offsets.txt
```

## Phase 10 — Point d’entrée

```bash
readelf -hW ./re_lab/working/target_binary
```

Puis :

```bash
objdump -d -M intel \
  --start-address=0xADRESSE_DEBUT \
  --stop-address=0xADRESSE_FIN \
  ./re_lab/working/target_binary
```

## Phase 11 — Localiser `main` dans un binaire stripped

Méthode :

1. récupérer le point d’entrée ;
2. désassembler `_start` ;
3. trouver l’appel vers le runtime ;
4. identifier l’adresse passée comme fonction principale ;
5. désassembler cette adresse ;
6. reconstruire les appels depuis cette fonction.

## Phase 12 — Reconstruire le comportement

Pour chaque fonction :

```text
Adresse :
Nom initial :
Nom proposé :
Appelée depuis :
Fonctions appelées :
Arguments probables :
Valeur de retour :
Variables globales utilisées :
Chaînes référencées :
Comportement supposé :
Niveau de confiance :
```

Exemple :

```text
Adresse : 0x4011a0
Nom initial : sub_4011a0
Nom proposé : validate_password
Appelée depuis : main
Fonctions appelées : strlen, memcmp
Arguments probables :
    RDI = entrée utilisateur
Valeur de retour :
    EAX = 1 si valide, 0 sinon
Chaînes référencées :
    "Access denied"
Comportement supposé :
    vérifie la taille puis compare une transformation de l’entrée
Confiance :
    élevée
```

---

# 34. Script d’analyse reproductible

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

target="${1:-./target_binary}"
output_dir="${2:-./analysis_output}"

if [[ "${target}" = /* ]]; then
    printf 'Error: use a relative path for the target.\n' >&2
    exit 1
fi

if [[ ! -f "${target}" ]]; then
    printf 'Error: target does not exist: %s\n' "${target}" >&2
    exit 1
fi

mkdir -p "${output_dir}"

readelf -hW "${target}" \
    > "${output_dir}/01_elf_header.txt"

readelf -lW "${target}" \
    > "${output_dir}/02_program_headers.txt"

readelf -SW "${target}" \
    > "${output_dir}/03_section_headers.txt"

readelf -sW "${target}" \
    > "${output_dir}/04_symbols.txt"

readelf --dyn-syms -W "${target}" \
    > "${output_dir}/05_dynamic_symbols.txt"

readelf -rW "${target}" \
    > "${output_dir}/06_relocations.txt"

readelf -dW "${target}" \
    > "${output_dir}/07_dynamic_section.txt"

readelf -nW "${target}" \
    > "${output_dir}/08_notes.txt"

objdump -f "${target}" \
    > "${output_dir}/09_file_header.txt"

objdump -h "${target}" \
    > "${output_dir}/10_sections.txt"

objdump -p "${target}" \
    > "${output_dir}/11_private_headers.txt"

objdump -t "${target}" \
    > "${output_dir}/12_static_symbols.txt"

objdump -T "${target}" \
    > "${output_dir}/13_dynamic_symbols_objdump.txt"

objdump -r "${target}" \
    > "${output_dir}/14_static_relocations.txt"

objdump -R "${target}" \
    > "${output_dir}/15_dynamic_relocations.txt"

objdump -d -M intel -F "${target}" \
    > "${output_dir}/16_disassembly.txt"

printf 'Static analysis completed.\n'
printf 'Results written to: %s\n' "${output_dir}"
printf 'ldd was intentionally not executed.\n'
```

Rendre le script exécutable :

```bash
chmod +x ./analyze.sh
```

Utilisation :

```bash
./analyze.sh ./target_binary ./analysis_output
```

---

# 35. Cas pratiques

## 35.1 Vérification de mot de passe

```asm
lea rax, [rbp-0x40]
mov rsi, rax

lea rax, [rip+0xe32]
mov rdi, rax

call strcmp@plt
test eax, eax
jne fail
```

Sous System V AMD64 :

```text
RDI = premier argument
RSI = deuxième argument
```

Comme `strcmp` retourne zéro lorsque les chaînes sont identiques :

```c
if (strcmp(expected_password, user_input) != 0) {
    fail();
}
```

## 35.2 Vérification avec XOR

```asm
movzx eax, BYTE PTR [rbp-0x20]
xor eax, 0x23
cmp al, 0x62
jne fail
```

Équation :

```text
input_byte XOR 0x23 == 0x62
```

Donc :

```text
input_byte = 0x62 XOR 0x23
input_byte = 0x41
```

`0x41` correspond à `A`.

## 35.3 Fonction cachée

Supposons les symboles :

```text
main
validate
print_error
win
```

mais `main` n’appelle jamais `win`.

```bash
objdump -d -M intel \
  --disassemble=win \
  ./target_binary
```

Il faut analyser :

- les chaînes ;
- les fichiers ouverts ;
- les fonctions appelées ;
- les conditions.

## 35.4 Réseau suspect

Imports :

```text
gethostname
socket
connect
send
close
```

Chaînes :

```text
198.51.100.15
System information collected
```

Méthode :

1. trouver l’appel à `gethostname` ;
2. identifier le buffer ;
3. trouver l’appel à `socket` ;
4. suivre la construction de l’adresse ;
5. trouver l’appel à `send` ;
6. vérifier si le même buffer est transmis.

## 35.5 Boucle de transformation

```asm
mov DWORD PTR [rbp-4], 0
jmp check

loop:
mov eax, DWORD PTR [rbp-4]
cdqe

movzx edx, BYTE PTR [rbp+rax-0x30]
xor edx, 0x55

mov eax, DWORD PTR [rbp-4]
cdqe

mov BYTE PTR [rbp+rax-0x30], dl
add DWORD PTR [rbp-4], 1

check:
cmp DWORD PTR [rbp-4], 15
jle loop
```

Pseudo-code :

```c
for (int i = 0; i <= 15; i++) {
    buffer[i] ^= 0x55;
}
```

---

# 36. Méthode de raisonnement

Pour chaque bloc assembleur, poser les questions suivantes.

## Entrées

- quels registres sont lus ?
- quelles variables mémoire sont lues ?
- quelles valeurs viennent de l’appelant ?
- quelles chaînes sont référencées ?

## Transformation

- quelles opérations sont effectuées ?
- existe-t-il une boucle ?
- une valeur est-elle encodée ?
- la comparaison est-elle signée ?
- un pointeur est-il calculé ?

## Sorties

- quelle valeur reste dans `RAX` ?
- quelles zones mémoire sont modifiées ?
- quelle fonction est appelée ensuite ?
- quelle branche est suivie ?

## Effets secondaires

- fichier créé ?
- donnée envoyée ?
- processus lancé ?
- mémoire allouée ?
- variable globale modifiée ?

---

# 37. Erreurs classiques

## 37.1 Croire aveuglément le décompilateur

Toujours revenir à l’assembleur lorsqu’une information semble incohérente.

## 37.2 Confondre adresse et valeur

```asm
lea rax, [rbp-0x20]
```

charge une adresse.

```asm
mov rax, [rbp-0x20]
```

charge une valeur.

## 37.3 Ignorer la convention d’appel

Sans convention d’appel, les arguments ne peuvent pas être reconstruits correctement.

## 37.4 Confondre signé et non signé

```text
jg ≠ ja
jl ≠ jb
```

## 37.5 Considérer toutes les chaînes comme actives

Une chaîne peut être inutilisée ou servir de leurre.

## 37.6 Utiliser `ldd` sur un fichier inconnu

Utiliser d’abord :

```bash
objdump -p ./target_binary
```

## 37.7 Modifier l’original

Toujours travailler sur une copie.

## 37.8 Commencer instruction par instruction

Commencer par :

- le format ;
- l’architecture ;
- les imports ;
- les symboles ;
- les sections ;
- le point d’entrée ;
- les chaînes ;
- les protections.

## 37.9 Renommer trop tôt

Préférer des noms prudents :

```text
read_input_candidate
network_send_candidate
decode_buffer_probable
```

---

# 38. Modèle de rapport

## 38.1 Informations générales

```text
Nom du fichier :
Chemin relatif :
Taille :
Hash SHA-256 :
Date de l’analyse :
Analyste :
Environnement :
```

## 38.2 Classification

```text
Format :
Architecture :
Endianness :
Type ELF :
Point d’entrée :
Linking dynamique ou statique :
Stripped :
PIE :
NX :
RELRO :
Stack canary :
```

## 38.3 Structure

```text
Segments importants :
Sections importantes :
Sections inhabituelles :
Permissions suspectes :
Interpréteur ELF :
```

## 38.4 Dépendances

```text
Bibliothèques :
Fonctions importées :
Fonctions réseau :
Fonctions fichiers :
Fonctions processus :
Fonctions de comparaison :
```

## 38.5 Fonctions importantes

| Adresse | Nom proposé | Description | Confiance |
|---|---|---|---|
| `0x...` | `main` | Point de départ logique | Élevée |
| `0x...` | `read_input` | Lit l’entrée utilisateur | Élevée |
| `0x...` | `validate_input` | Vérifie et transforme l’entrée | Moyenne |
| `0x...` | `send_data` | Envoie un buffer sur le réseau | Élevée |

## 38.6 Comportement confirmé, probable et inconnu

```text
Confirmé :
- Le programme importe connect et send.
- La fonction 0x... appelle connect.
- Le buffer envoyé provient de gethostname.

Probable :
- Le programme semble envoyer l’identité de la machine.

Non confirmé :
- Aucun mécanisme de persistance n’a été identifié.
```

## 38.7 Conclusion

```text
L’analyse statique montre que target_binary collecte le nom d’hôte,
construit une connexion TCP et transmet cette information à une adresse
distante. Le flux est confirmé par les appels successifs à gethostname,
socket, connect et send ainsi que par le suivi du même buffer entre ces
fonctions.
```

## 38.8 Limites

```text
- Aucune exécution dynamique n’a été réalisée.
- Certaines fonctions sont indirectes.
- Le binaire est stripped.
- Une partie des données peut être reconstruite à l’exécution.
- Les conclusions concernent uniquement les chemins visibles.
```

---

# 39. Checklist

```text
[ ] Travailler dans une VM ou sandbox
[ ] Conserver une copie originale
[ ] Calculer et enregistrer le hash
[ ] Retirer le droit d’exécution
[ ] Identifier le format ELF
[ ] Identifier l’architecture
[ ] Identifier l’endianness
[ ] Identifier le type ET_EXEC ou ET_DYN
[ ] Noter le point d’entrée
[ ] Examiner les program headers
[ ] Examiner les section headers
[ ] Examiner GNU_STACK
[ ] Examiner GNU_RELRO
[ ] Examiner les symboles
[ ] Examiner les symboles dynamiques
[ ] Examiner les relocations
[ ] Examiner la section dynamique
[ ] Examiner les notes
[ ] Examiner .rodata
[ ] Examiner les dépendances avec objdump -p
[ ] Éviter ldd si le binaire n’est pas fiable
[ ] Désassembler le point d’entrée
[ ] Désassembler main si disponible
[ ] Identifier les fonctions importantes
[ ] Identifier les conditions
[ ] Identifier les boucles
[ ] Identifier les appels externes
[ ] Reconstruire les arguments
[ ] Suivre les données sensibles
[ ] Rechercher les fonctions cachées
[ ] Rechercher les fonctions d’initialisation
[ ] Rechercher des signes de packing
[ ] Rechercher des signes d’anti-debugging
[ ] Produire un call graph logique
[ ] Produire un CFG des fonctions principales
[ ] Distinguer faits, hypothèses et inconnues
[ ] Rédiger le rapport final
```

---

# 40. Résumé des commandes

## `readelf`

```bash
readelf -hW ./target_binary
readelf -lW ./target_binary
readelf -SW ./target_binary
readelf -sW ./target_binary
readelf --dyn-syms -W ./target_binary
readelf -rW ./target_binary
readelf -dW ./target_binary
readelf -nW ./target_binary
readelf -p .rodata ./target_binary
```

## `objdump`

```bash
objdump -f ./target_binary
objdump -h ./target_binary
objdump -p ./target_binary
objdump -t ./target_binary
objdump -T ./target_binary
objdump -r ./target_binary
objdump -R ./target_binary
objdump -d -M intel ./target_binary
objdump -D -M intel ./target_binary
objdump -d -M intel -F ./target_binary
objdump -d -M intel --disassemble=main ./target_binary
objdump -s -j .rodata ./target_binary
```

## `ldd`

Uniquement dans un environnement jetable et contrôlé :

```bash
ldd ./target_binary
ldd -v ./target_binary
```

Pour un fichier non fiable :

```bash
objdump -p ./target_binary
```

---

# 41. Questions de révision

## Question 1

Qu’est-ce que le reverse engineering logiciel ?

**Réponse :** l’analyse d’un programme compilé afin de reconstruire sa structure, son fonctionnement et ses comportements sans disposer nécessairement de son code source.

## Question 2

Quelle différence existe entre désassemblage et décompilation ?

**Réponse :** le désassemblage traduit le code machine en assembleur. La décompilation reconstruit un pseudo-code de plus haut niveau.

## Question 3

Pourquoi le pseudo-code n’est-il pas le code source original ?

**Réponse :** la compilation supprime ou transforme les noms, commentaires, types, structures, macros et intentions originales.

## Question 4

Qu’est-ce qu’un basic block ?

**Réponse :** une suite d’instructions possédant une seule entrée, une seule sortie et aucune branche interne.

## Question 5

Quelle différence existe entre CFG et call graph ?

**Réponse :** le CFG représente le contrôle de flux entre les blocs d’une fonction. Le call graph représente les appels entre fonctions.

## Question 6

Qu’est-ce qu’une XREF ?

**Réponse :** une référence indiquant qu’un élément est utilisé depuis un autre emplacement.

## Question 7

Quels sont les six premiers registres d’arguments sous System V AMD64 ?

```text
RDI, RSI, RDX, RCX, R8, R9
```

## Question 8

Quel registre contient généralement la valeur de retour ?

```text
RAX
```

## Question 9

Quelle différence existe entre `mov` et `lea` ?

**Réponse :** `mov` transfère généralement une valeur. `lea` calcule une adresse effective.

## Question 10

Que signifie :

```asm
test eax, eax
je success
```

**Réponse :** le programme teste si `eax` vaut zéro et va vers `success` si c’est le cas.

## Question 11

Que contient `.text` ?

**Réponse :** principalement le code exécutable.

## Question 12

Que contient `.rodata` ?

**Réponse :** principalement les constantes et les chaînes.

## Question 13

Quelle différence existe entre section et segment ELF ?

**Réponse :** les sections servent principalement au linker et aux outils d’analyse. Les segments servent au loader pour mapper le programme en mémoire.

## Question 14

À quoi servent la PLT et la GOT ?

**Réponse :** elles participent à la résolution et à l’appel des fonctions dynamiques importées.

## Question 15

Pourquoi un binaire stripped est-il plus difficile à analyser ?

**Réponse :** parce qu’une grande partie des noms de fonctions et informations de symboles a été retirée.

## Question 16

Que fait un packer ?

**Réponse :** il compresse ou chiffre le programme original et ajoute un stub chargé de le reconstruire en mémoire.

## Question 17

Donne trois techniques anti-debugging.

**Réponse :**

- détection d’un debugger ;
- mesure du temps ;
- vérification de breakpoints ou registres de debug.

## Question 18

Pourquoi éviter `ldd` sur un exécutable non fiable ?

**Réponse :** son utilisation peut présenter un risque d’exécution selon le binaire et l’environnement.

## Question 19

Comment identifier une fonction dans un binaire stripped ?

**Réponse :** à partir des cibles de `call`, du contrôle de flux, des relocations, des tables de pointeurs, des informations d’unwind et des heuristiques.

## Question 20

Quelle règle doit guider toute conclusion ?

```text
Ce qui est confirmé
Ce qui est probable
Ce qui reste inconnu
```

---

# 42. Glossaire

| Terme | Définition |
|---|---|
| ABI | Interface binaire entre programme, bibliothèques et système |
| ASLR | Randomisation des adresses mémoire |
| Basic block | Suite linéaire d’instructions sans branche interne |
| Binaire | Fichier contenant du code machine ou des données exécutables |
| Call graph | Graphe représentant les appels entre fonctions |
| Calling convention | Règles de transmission des arguments et retours |
| CFG | Graphe du contrôle de flux |
| Code machine | Instructions encodées sous forme d’octets |
| Decompiler | Outil transformant le code bas niveau en pseudo-code |
| Disassembler | Outil transformant le code machine en assembleur |
| ELF | Format exécutable principalement utilisé sous Linux |
| Entry point | Adresse à laquelle le loader transfère l’exécution |
| GOT | Global Offset Table |
| Import | Fonction ou donnée utilisée depuis une bibliothèque externe |
| Mach-O | Format exécutable des plateformes Apple |
| Opcode | Encodage binaire d’une instruction |
| Packer | Programme compressant ou chiffrant un exécutable |
| PE | Portable Executable, format principal de Windows |
| PIE | Position Independent Executable |
| PLT | Procedure Linkage Table |
| Relocation | Information permettant d’ajuster une adresse |
| Section | Zone logique d’un fichier objet |
| Segment | Zone mappée en mémoire par le loader |
| Stack frame | Zone de pile utilisée par une fonction |
| Stripped binary | Binaire dont une partie des symboles a été supprimée |
| Symbol | Association entre un nom et une adresse |
| XREF | Référence croisée |

---

# 43. Parcours d’apprentissage

## Niveau 1 — Architecture

Maîtriser :

- hexadécimal ;
- little endian ;
- registres ;
- mémoire ;
- pile ;
- flags ;
- instructions essentielles ;
- conventions d’appel.

## Niveau 2 — ELF

Maîtriser :

- header ;
- sections ;
- segments ;
- symboles ;
- relocations ;
- PLT/GOT ;
- linking dynamique ;
- point d’entrée.

## Niveau 3 — Assembleur

Savoir reconnaître :

- conditions ;
- boucles ;
- tableaux ;
- chaînes ;
- appels ;
- arguments ;
- valeurs de retour ;
- variables locales ;
- variables globales.

## Niveau 4 — Analyse logique

Savoir produire :

- pseudo-code ;
- CFG ;
- call graph ;
- XREFs ;
- description de fonction ;
- suivi de données.

## Niveau 5 — Protections

Comprendre :

- stripping ;
- PIE ;
- ASLR ;
- NX ;
- canaries ;
- RELRO ;
- packing ;
- obfuscation ;
- anti-debugging.

## Niveau 6 — CTF

S’entraîner sur :

- comparaison de mot de passe ;
- XOR ;
- tableaux ;
- fonctions cachées ;
- checksums ;
- encodages ;
- validations multi-étapes.

---

# 44. Conclusion

Le reverse engineering n’est pas principalement une question d’outil.

Un outil peut afficher :

- des instructions ;
- des graphes ;
- des chaînes ;
- des imports ;
- du pseudo-code.

Mais l’analyste doit transformer ces éléments en une explication fiable.

Méthode générale :

```text
Identifier le format
    ↓
Comprendre l’architecture
    ↓
Cartographier les sections et segments
    ↓
Examiner les symboles et imports
    ↓
Localiser les fonctions importantes
    ↓
Reconstruire le contrôle de flux
    ↓
Suivre le déplacement des données
    ↓
Formuler une hypothèse
    ↓
Chercher des preuves supplémentaires
    ↓
Documenter le niveau de confiance
```

Pour `target_binary`, l’objectif final est de pouvoir expliquer :

1. ce qu’est le fichier ;
2. comment il est chargé ;
3. quelles fonctions il contient ;
4. quelles entrées il reçoit ;
5. quelles décisions il prend ;
6. quelles données il manipule ;
7. quelles actions il réalise ;
8. quelles protections il utilise ;
9. quelles fonctionnalités sont cachées ;
10. quelles preuves soutiennent chaque conclusion.

Un bon reverse engineer ne dit pas seulement :

> « Cette fonction semble suspecte. »

Il explique :

> « Cette fonction récupère le hostname dans un buffer local, transmet le même buffer comme argument à `send`, puis ferme le socket. Cette conclusion est soutenue par les appels, la convention System V AMD64, les références croisées et le suivi du buffer dans le désassemblage. »

---

## Ressources recommandées

- OpenSecurityTraining — Intro to x86
- Reverse Engineering for Beginners
- Practical Reverse Engineering
- The IDA Pro Book
- Ghidra Tutorial Series
- Documentation GNU Binutils pour `objdump` et `readelf`
- Documentation officielle ELF, PE et Mach-O

---

## Outils étudiés

- IDA Pro
- Ghidra
- Radare2
- GDB
- x64dbg
- OllyDbg
- Hex-Rays
- RetDec
- Binwalk
- Strings
- objdump
- readelf
- ldd
