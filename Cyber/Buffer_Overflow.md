# Cours ultra complet — Buffer Overflow

> **Module :** Buffer Overflow  
> **Format :** cours pédagogique, structuré, détaillé, orienté compréhension + bonnes pratiques défensives  
> **Langue :** français  
> **Public visé :** étudiant en cybersécurité / développement système / audit applicatif  
> **Contexte :** compréhension technique, analyse de vulnérabilités, prévention, détection et mitigation

---

# Table des matières

1. [Introduction](#1-introduction)
2. [Objectifs pédagogiques](#2-objectifs-pédagogiques)
3. [Qu’est-ce qu’un buffer ?](#3-quest-ce-quun-buffer-)
4. [Rappels indispensables sur la mémoire d’un processus](#4-rappels-indispensables-sur-la-mémoire-dun-processus)
5. [Qu’est-ce qu’un buffer overflow ?](#5-quest-ce-quun-buffer-overflow-)
6. [Buffer overflow vs buffer overflow attack](#6-buffer-overflow-vs-buffer-overflow-attack)
7. [Pourquoi les buffer overflows existent-ils ?](#7-pourquoi-les-buffer-overflows-existent-ils-)
8. [Comment un attaquant orchestre un buffer overflow ?](#8-comment-un-attaquant-orchestre-un-buffer-overflow-)
9. [Les différents types de buffer overflow](#9-les-différents-types-de-buffer-overflow)
10. [Fonctions C historiquement dangereuses](#10-fonctions-c-historiquement-dangereuses)
11. [Exemples pédagogiques de code vulnérable et corrigé](#11-exemples-pédagogiques-de-code-vulnérable-et-corrigé)
12. [Conséquences d’un buffer overflow](#12-conséquences-dun-buffer-overflow)
13. [Détection des buffer overflows](#13-détection-des-buffer-overflows)
14. [Mitigations et protections modernes](#14-mitigations-et-protections-modernes)
15. [Le rôle du système Linux et du système de fichiers `/proc`](#15-le-rôle-du-système-linux-et-du-système-de-fichiers-proc)
16. [Méthodologie d’audit et de revue de code](#16-méthodologie-daudit-et-de-revue-de-code)
17. [Checklist de prévention pour développeur C/C++](#17-checklist-de-prévention-pour-développeur-cc)
18. [Pièges fréquents et fausses bonnes idées](#18-pièges-fréquents-et-fausses-bonnes-idées)
19. [Synthèse ultra courte à mémoriser](#19-synthèse-ultra-courte-à-mémoriser)
20. [Questions/réponses de révision](#20-questionsréponses-de-révision)
21. [Conclusion](#21-conclusion)
22. [Références et ressources](#22-références-et-ressources)

---

# 1. Introduction

Le **buffer overflow** fait partie des vulnérabilités mémoire les plus classiques de l’histoire de la sécurité logicielle. C’est un sujet central parce qu’il mélange plusieurs disciplines à la fois :

- programmation bas niveau ;
- architecture mémoire ;
- sécurité logicielle ;
- compilation ;
- systèmes d’exploitation ;
- audit et reverse engineering ;
- défense applicative.

Un buffer overflow n’est pas seulement un “bug qui fait planter”. C’est une **écriture hors limites** : un programme essaie d’écrire plus de données qu’un buffer ne peut en contenir, ou écrit en dehors de la zone mémoire qui lui a été allouée. Selon le contexte, cela peut :

- corrompre des données ;
- provoquer un crash ;
- altérer le flux de contrôle ;
- ouvrir une voie vers l’exécution de code arbitraire ;
- permettre une élévation de privilèges ;
- transformer un simple bug en vulnérabilité critique.

Historiquement, ce type de faille a marqué la sécurité offensive et défensive. En réaction, les systèmes modernes ont introduit des mécanismes comme les **stack canaries**, l’**ASLR**, la **non-executable memory (NX/DEP)**, la **fortification de bibliothèques**, les **sanitizers**, et des protections de contrôle de flux. Malgré cela, les buffer overflows restent importants car :

1. ils existent encore dans du code C/C++, du code legacy, des drivers, des appliances, des parsers binaires et de nombreux composants système ;
2. ils servent d’excellent cas d’étude pour comprendre **comment une simple erreur de programmation devient un problème de sécurité** ;
3. leur étude permet de mieux écrire du code sûr, mieux auditer, et mieux configurer un build sécurisé.

Ce cours vise à te donner une compréhension **de fond**, pas juste une définition.

---

# 2. Objectifs pédagogiques

À la fin de ce module, tu dois être capable d’expliquer clairement, sans Google :

- ce qu’est un **buffer** ;
- ce qu’est un **buffer overflow** ;
- ce qu’est une **attaque par buffer overflow** ;
- pourquoi ces vulnérabilités apparaissent ;
- comment un attaquant peut les exploiter à haut niveau ;
- quelles sont les différentes familles de débordements ;
- comment les détecter ;
- quelles sont leurs conséquences ;
- comment les prévenir ;
- comment les mitiger côté compilation, exécution et architecture logicielle ;
- comment le système Linux, notamment **`/proc`**, aide à observer l’espace mémoire et certaines protections.

---

# 3. Qu’est-ce qu’un buffer ?

## 3.1 Définition simple

Un **buffer** est une zone de mémoire réservée pour stocker temporairement des données.

Exemples classiques :

- une chaîne de caractères ;
- un tableau d’octets ;
- un tableau d’entiers ;
- un bloc lu depuis un fichier ;
- un paquet réseau ;
- un bloc de données avant sérialisation.

En C, un buffer est très souvent un tableau :

```c
char name[32];
int numbers[10];
unsigned char packet[1500];
```

Chaque buffer possède implicitement ou explicitement une **capacité** :

- `name[32]` peut contenir au maximum 32 octets ;
- `numbers[10]` peut contenir 10 entiers ;
- `packet[1500]` peut contenir 1500 octets.

## 3.2 Analogie simple

Imagine un carton de 10 bouteilles.

- Le carton = le buffer
- La capacité maximale = 10
- Si tu essaies d’en mettre 12, les deux bouteilles “débordent”

En mémoire, le débordement ne “tombe pas par terre” : il **écrase autre chose**. Et c’est justement ce qui rend la situation dangereuse.

## 3.3 Buffers et chaînes de caractères

En C, les chaînes sont représentées par des tableaux de `char` terminés par le caractère nul `\0`.

Exemple :

```c
char s[6] = "hello";
```

Mémoire réelle :

- `h`
- `e`
- `l`
- `l`
- `o`
- `\0`

Donc une chaîne de 5 caractères demande en réalité **6 octets**. C’est un détail fondamental : énormément de bugs viennent d’un oubli du **null terminator**.

## 3.4 Taille logique vs taille physique

Il faut distinguer :

- la **taille logique** des données ;
- la **taille physique** du buffer.

Exemple :

```c
char username[16];
```

- taille physique du buffer : 16 octets ;
- taille logique d’une entrée utilisateur : variable.

Si l’utilisateur envoie 40 caractères et que le programme copie tout sans vérifier, le buffer est dépassé.

---

# 4. Rappels indispensables sur la mémoire d’un processus

Pour comprendre le buffer overflow, il faut comprendre **où vivent les buffers**.

## 4.1 Vue simplifiée de la mémoire

Un processus possède plusieurs zones mémoire, notamment :

- **text/code** : instructions exécutables ;
- **data** : variables globales/statics initialisées ;
- **bss** : variables globales/statics non initialisées ;
- **heap** : mémoire dynamique (`malloc`, `calloc`, `realloc`) ;
- **stack** : appels de fonctions, variables locales, adresses de retour.

Schéma simplifié :

```text
+---------------------------+
| Code / text               |
+---------------------------+
| Data / BSS                |
+---------------------------+
| Heap   (grandit souvent ↑)|
|                           |
|                           |
+---------------------------+
|                           |
|        espace libre       |
|                           |
+---------------------------+
| Stack  (grandit souvent ↓)|
+---------------------------+
```

## 4.2 La stack

La **stack** contient notamment :

- les variables locales ;
- les paramètres (selon ABI / convention d’appel) ;
- l’adresse de retour ;
- des informations de cadre de pile (frame).

Exemple conceptuel :

```c
void greet(void) {
    char buf[16];
}
```

Ici `buf` est typiquement sur la stack.

Pourquoi c’est sensible ? Parce que si l’écriture sort de `buf`, elle peut atteindre d’autres éléments du frame, dont dans certains cas :

- variables adjacentes ;
- pointeurs ;
- sauvegarde du frame pointer ;
- adresse de retour.

## 4.3 Le heap

Le **heap** contient la mémoire allouée dynamiquement :

```c
char *buf = malloc(32);
```

Ici `buf` pointe vers une zone de 32 octets dans le heap.

Un débordement heap n’écrase pas la stack, mais peut corrompre :

- des données applicatives ;
- des métadonnées d’allocateur ;
- des pointeurs ;
- des objets voisins ;
- des structures de contrôle.

## 4.4 Buffers globaux et statiques

Un buffer peut aussi être global :

```c
char global_buffer[64];
```

ou statique :

```c
static char cache[128];
```

Ces cas existent aussi et peuvent produire des corruptions mémoire sérieuses.

---

# 5. Qu’est-ce qu’un buffer overflow ?

## 5.1 Définition

Un **buffer overflow** se produit lorsqu’un programme :

- écrit plus de données que la capacité du buffer ;
- ou écrit en dehors des bornes prévues de ce buffer.

Autrement dit : il y a **écriture hors limites**.

Le cœur du problème n’est pas “beaucoup de données”.  
Le cœur du problème, c’est : **absence de contrôle des bornes**.

## 5.2 Formulation rigoureuse

On parle de buffer overflow lorsqu’une opération de copie, concaténation, lecture, formatage, indexation ou calcul de taille amène le programme à :

- dépasser la mémoire réservée ;
- ou accéder à une zone mémoire qui ne lui appartient pas dans le cadre de cet objet.

## 5.3 Ce n’est pas forcément visible immédiatement

Un buffer overflow ne provoque pas toujours un crash instantané.

Parfois :

- le programme continue ;
- la corruption ne se manifeste que plus tard ;
- l’effet est aléatoire ;
- le bug est reproductible seulement avec certaines tailles d’entrée.

C’est pour cela que les bugs mémoire sont pénibles à diagnostiquer.

---

# 6. Buffer overflow vs buffer overflow attack

## 6.1 Le bug

Le **buffer overflow** est d’abord un **défaut logiciel** : le programme écrit hors limites.

## 6.2 L’attaque

La **buffer overflow attack** est l’exploitation volontaire de ce défaut par un attaquant pour obtenir un effet intéressant, par exemple :

- faire tomber un service ;
- modifier des données ;
- contourner des contrôles ;
- détourner le flux d’exécution ;
- exécuter du code arbitraire ;
- élever des privilèges.

## 6.3 Différence essentielle

- **Overflow** = faiblesse / bug mémoire
- **Attack** = utilisation malveillante et contrôlée de cette faiblesse

Tous les overflows ne sont pas exploitables de façon utile.  
Mais tout overflow est au minimum un **signe grave de défaut de sûreté mémoire**.

---

# 7. Pourquoi les buffer overflows existent-ils ?

Les causes sont multiples.

## 7.1 Absence de vérification de taille

Exemple typique :

```c
char buf[16];
strcpy(buf, user_input);
```

Si `user_input` fait 100 caractères, `strcpy()` continue jusqu’au `\0`, sans connaître la capacité de `buf`.

## 7.2 Fonctions non bornées ou mal utilisées

Des fonctions historiques de C sont dangereuses si on les utilise sans rigueur :

- `gets()`
- `strcpy()`
- `strcat()`
- `sprintf()`
- `scanf("%s", ...)`
- `memcpy()` avec taille incorrecte

Même des fonctions “plus sûres” peuvent être mal utilisées si :

- la taille passée est fausse ;
- le buffer destination n’est pas réellement de cette taille ;
- la terminaison nulle n’est pas gérée ;
- la valeur de retour n’est pas vérifiée.

## 7.3 Off-by-one

On dépasse d’un seul octet seulement.  
Ça a l’air petit, mais un seul octet peut suffire à :

- casser une terminaison nulle ;
- corrompre un champ de structure ;
- modifier un flag ;
- changer un pointeur partiellement ;
- déstabiliser un allocateur.

## 7.4 Mauvais calcul de taille

Exemples fréquents :

- confusion entre nombre d’éléments et nombre d’octets ;
- oubli de `\0` ;
- oubli de multiplier par `sizeof(type)` ;
- utilisation d’une taille issue d’un entier tronqué ;
- addition / multiplication qui déborde avant l’allocation.

## 7.5 Integer overflow menant à un buffer overflow

Exemple logique :

1. on calcule une taille avec un entier ;
2. le calcul déborde ;
3. on alloue trop petit ;
4. on copie plus que la zone réellement allouée.

Ce n’est pas un buffer overflow “direct” au départ, mais une chaîne classique :

**integer overflow → allocation trop petite → buffer overflow**

## 7.6 Entrées non fiables

Les sources de données dangereuses sont nombreuses :

- saisie utilisateur ;
- paramètres CLI ;
- variables d’environnement ;
- paquets réseau ;
- fichiers ;
- métadonnées d’images, archives, PDF, audio, etc. ;
- IPC ;
- données désérialisées ;
- réponses de services externes.

## 7.7 Erreurs d’indexation

Exemple :

```c
for (i = 0; i <= 10; i++)
    buf[i] = 0;
```

Si `buf` a 10 cases (`buf[10]`), les indices valides vont de `0` à `9`.  
Le `<= 10` déborde.

## 7.8 Hypothèses implicites fausses

Le développeur “suppose” que :

- les noms ne dépasseront jamais 20 caractères ;
- les entêtes réseau seront “bien formés” ;
- le fichier sera toujours “correct” ;
- l’utilisateur sera “honnête”.

En sécurité, une hypothèse non vérifiée est un risque.

---

# 8. Comment un attaquant orchestre un buffer overflow ?

> Cette section reste **pédagogique et défensive** : on explique la logique générale d’exploitation sans fournir de procédure offensive détaillée.

## 8.1 Étape 1 : repérer une surface d’entrée

L’attaquant cherche une entrée contrôlable :

- un paramètre ;
- une chaîne lue au clavier ;
- un champ réseau ;
- un nom de fichier ;
- un en-tête ;
- un format binaire.

## 8.2 Étape 2 : trouver la zone vulnérable

Il cherche ensuite une opération risquée :

- copie non bornée ;
- concaténation aveugle ;
- formatage sans contrôle ;
- calcul de longueur défectueux ;
- indexation mal vérifiée ;
- allocation trop petite.

## 8.3 Étape 3 : provoquer une écriture hors limites

L’entrée est adaptée pour dépasser exactement ou approximativement la capacité visée.

L’objectif peut être :

- faire planter ;
- observer le comportement ;
- comprendre la structure mémoire ;
- vérifier qu’il y a bien corruption.

## 8.4 Étape 4 : transformer le crash en effet utile

Une exploitation plus avancée essaie de remplacer le simple crash par un effet contrôlé :

- modification d’un flag logique ;
- corruption d’un pointeur ;
- détournement d’un appel indirect ;
- changement du flux d’exécution ;
- contournement d’un contrôle de sécurité.

## 8.5 Étape 5 : lutter contre les mitigations

Les systèmes modernes compliquent l’exploitation :

- stack canary ;
- ASLR ;
- NX/DEP ;
- PIE ;
- RELRO ;
- CFI/CFG ;
- fortification ;
- sanitizers en test ;
- séparation des privilèges.

Donc un bug mémoire moderne n’est pas automatiquement “RCE triviale”. Mais il reste grave.

## 8.6 Le point clé

Un attaquant n’a pas besoin de “beaucoup de magie”.  
Il a besoin de trois choses :

1. une entrée contrôlée ;
2. une absence de vérification de bornes ;
3. une cible mémoire intéressante ou un effet observable.

---

# 9. Les différents types de buffer overflow

## 9.1 Stack-based buffer overflow

Le buffer débordé est sur la **stack**.

Exemple typique :

```c
void f(const char *input) {
    char buf[32];
    strcpy(buf, input);
}
```

C’est l’image classique du “stack smashing”.

### Risques typiques

- corruption de variables locales ;
- corruption du frame ;
- écrasement de l’adresse de retour ;
- crash ;
- détournement de contrôle dans certains contextes.

## 9.2 Heap-based buffer overflow

Le buffer débordé est dans le **heap**.

Exemple :

```c
char *buf = malloc(32);
memcpy(buf, input, input_len);   /* si input_len > 32 => overflow */
```

### Risques typiques

- corruption d’objets voisins ;
- corruption de structures applicatives ;
- corruption de métadonnées d’allocateur ;
- altération de pointeurs ;
- exécution indirecte via pointeurs de fonction ou vtables selon contexte.

## 9.3 Global / static buffer overflow

Le buffer est global ou statique.

Exemple :

```c
static char cache[64];
```

Déborder ce type de buffer peut corrompre d’autres objets statiques ou globaux.

## 9.4 Off-by-one overflow

Le dépassement est minime, souvent d’un seul octet.

Exemple :

```c
char buf[8];
for (int i = 0; i <= 8; i++)
    buf[i] = 'A';
```

Écriture de 9 octets dans 8.

### Pourquoi c’est dangereux ?

Parce qu’un seul octet peut suffire à :

- supprimer la terminaison nulle ;
- corrompre un flag ;
- altérer partiellement un pointeur ;
- changer un champ de longueur.

## 9.5 Out-of-bounds write

Concept plus large : écriture en dehors des limites d’un objet mémoire.

Le buffer overflow classique en fait partie, mais la famille plus générale couvre aussi des erreurs d’indexation et autres corruptions hors bornes.

## 9.6 Overread vs overwrite

Il faut distinguer :

- **overflow / overwrite** : on écrit trop loin ;
- **overread** : on lit trop loin.

Les deux sont graves :

- lecture hors borne → fuite d’information ;
- écriture hors borne → corruption, crash, exécution, etc.

## 9.7 Stack overflow au sens “épuisement de pile”

Attention au vocabulaire :

- **stack-based buffer overflow** = corruption mémoire due à un buffer sur la pile ;
- **stack overflow** au sens courant = pile épuisée par récursion profonde, allocations trop grandes sur la pile, etc.

Ce ne sont pas exactement les mêmes problèmes.

---

# 10. Fonctions C historiquement dangereuses

## 10.1 `gets()`

`gets()` est l’exemple emblématique.

Pourquoi c’est dangereux ?  
Parce que la fonction ne sait pas combien le buffer destination peut contenir.

```c
char buf[16];
gets(buf);   /* dangereux */
```

Elle continue jusqu’au retour ligne / EOF.  
C’est pour cela que cette fonction a été largement bannie et retirée du standard C moderne.

## 10.2 `strcpy()`

Copie jusqu’au `\0` source, sans tenir compte de la taille destination.

```c
char dst[16];
strcpy(dst, src);
```

Si `src` > 15 caractères + `\0`, overflow.

## 10.3 `strcat()`

Concatène sans vérifier si la destination a encore assez de place.

## 10.4 `sprintf()`

Écrit dans un buffer selon un format, sans borne intrinsèque.

```c
char buf[32];
sprintf(buf, "Hello %s", name);
```

Si `name` est trop long, overflow.

## 10.5 `scanf("%s", ...)`

Le `%s` sans largeur maximale lit une chaîne arbitrairement longue jusqu’à l’espace.

## 10.6 `memcpy()`

`memcpy()` n’est pas “dangereux par nature”, mais il est **aveugle** :

```c
memcpy(dst, src, n);
```

Si `n` est faux, la fonction obéit quand même.

## 10.7 Même les alternatives demandent de la rigueur

Exemples :

- `fgets()` est plus sûr, mais il faut comprendre sa sémantique ;
- `snprintf()` est préférable à `sprintf()`, mais il faut vérifier la troncature ;
- `strncpy()` n’est pas une solution magique, notamment à cause de la terminaison nulle non garantie si la source est trop longue.

---

# 11. Exemples pédagogiques de code vulnérable et corrigé

## 11.1 Copie naïve sur la stack

### Vulnérable

```c
#include <stdio.h>
#include <string.h>

void greet(const char *name) {
    char buf[16];
    strcpy(buf, name);
    printf("Bonjour %s\n", buf);
}
```

### Problème

- `buf` fait 16 octets
- `strcpy()` ne connaît pas la taille de `buf`
- une entrée trop longue provoque une écriture hors bornes

### Corrigé

```c
#include <stdio.h>
#include <string.h>

void greet(const char *name) {
    char buf[16];
    int written = snprintf(buf, sizeof(buf), "%s", name);

    if (written < 0) {
        return;
    }

    if ((size_t)written >= sizeof(buf)) {
        fprintf(stderr, "Nom trop long\n");
        return;
    }

    printf("Bonjour %s\n", buf);
}
```

### Pourquoi c’est mieux ?

- la taille du buffer est explicitement fournie ;
- on vérifie si le résultat a été tronqué ;
- on ne suppose pas que l’entrée est “raisonnable”.

---

## 11.2 Lecture depuis stdin

### Vulnérable

```c
#include <stdio.h>

int main(void) {
    char buf[8];
    gets(buf);
    puts(buf);
    return 0;
}
```

### Corrigé

```c
#include <stdio.h>
#include <string.h>

int main(void) {
    char buf[8];

    if (fgets(buf, sizeof(buf), stdin) == NULL) {
        return 1;
    }

    buf[strcspn(buf, "\n")] = '\0';
    puts(buf);
    return 0;
}
```

### Pourquoi c’est mieux ?

- `fgets()` connaît la taille du buffer ;
- la lecture ne dépasse pas la capacité ;
- on retire proprement le retour ligne.

---

## 11.3 Erreur d’allocation sur le heap

### Vulnérable

```c
#include <stdlib.h>
#include <string.h>

char *copy_name(const char *name) {
    char *p = malloc(strlen(name));
    if (!p) return NULL;
    strcpy(p, name);
    return p;
}
```

### Problème

`strlen(name)` ne compte pas le `\0`.  
Il faut allouer `strlen(name) + 1`.

### Corrigé

```c
#include <stdlib.h>
#include <string.h>

char *copy_name(const char *name) {
    size_t len = strlen(name);
    char *p = malloc(len + 1);
    if (!p) return NULL;

    memcpy(p, name, len + 1);
    return p;
}
```

---

## 11.4 Erreur de borne dans une boucle

### Vulnérable

```c
void zero_buffer(char *buf, size_t len) {
    for (size_t i = 0; i <= len; i++) {
        buf[i] = 0;
    }
}
```

### Problème

On écrit `len + 1` octets au lieu de `len`.

### Corrigé

```c
void zero_buffer(char *buf, size_t len) {
    for (size_t i = 0; i < len; i++) {
        buf[i] = 0;
    }
}
```

---

## 11.5 `scanf("%s")` non borné

### Vulnérable

```c
char username[16];
scanf("%s", username);
```

### Corrigé minimal

```c
char username[16];
scanf("%15s", username);
```

### Encore mieux

Préférer souvent `fgets()` + validation explicite.

---

## 11.6 Utilisation défensive de tailles

Une bonne habitude consiste à toujours lier la taille à l’objet réel :

```c
char buf[64];
fgets(buf, sizeof(buf), stdin);
```

et non à une constante “magique” séparée :

```c
char buf[64];
fgets(buf, 128, stdin);   /* catastrophe */
```

---

# 12. Conséquences d’un buffer overflow

## 12.1 Crash du programme

C’est la conséquence la plus visible :

- segmentation fault ;
- abort ;
- corruption détectée par runtime ;
- terminaison anormale.

## 12.2 Corruption silencieuse de données

Plus dangereux encore : le programme continue, mais avec des données corrompues.

Exemples :

- champ d’utilisateur modifié ;
- booléen de sécurité changé ;
- taille incorrecte ;
- état interne incohérent.

## 12.3 Déni de service

Un service vulnérable peut être forcé à planter à répétition.

## 12.4 Exécution de code arbitraire

Dans certains cas, la corruption mémoire permet à un attaquant de détourner le flux de contrôle et d’obtenir une exécution arbitraire. C’est la conséquence “historique” la plus redoutée, même si les systèmes modernes la rendent plus difficile.

## 12.5 Élévation de privilèges

Si le binaire vulnérable tourne avec des privilèges élevés, la faille peut devenir un vecteur d’élévation.

## 12.6 Fuite d’informations

Certains bugs mémoire associés peuvent provoquer des lectures hors bornes ou exposer l’état mémoire, ce qui aide à contourner d’autres protections.

## 12.7 Compromission en chaîne

Un buffer overflow peut n’être qu’un maillon d’une chaîne :

- fuite mémoire ;
- contournement de mitigation ;
- écriture hors borne ;
- pivot vers autre primitive ;
- compromission plus large.

---

# 13. Détection des buffer overflows

Détecter les buffer overflows demande plusieurs couches.

## 13.1 Revue de code

La revue manuelle cherche notamment :

- `strcpy`, `strcat`, `sprintf`, `gets`, `%s` non borné ;
- boucles `<=` au lieu de `<` ;
- calculs de taille suspects ;
- `malloc(strlen(x))` sans `+1` ;
- conversions de types dangereuses ;
- index basés sur des entrées non validées ;
- `memcpy` / `memmove` avec longueur non fiable.

## 13.2 Warnings du compilateur

Il faut compiler **avec beaucoup d’avertissements** :

```bash
gcc -Wall -Wextra -Wpedantic -Wformat=2 -Wconversion -Wshadow -Wundef -Wstack-protector
```

Les warnings ne suffisent pas, mais ils éliminent beaucoup d’erreurs simples.

## 13.3 Stack protector

Compiler avec un protecteur de pile :

```bash
gcc -fstack-protector-strong ...
```

Le protecteur ne “prévoit” pas le bug ; il aide à le **détecter à l’exécution** ou à rendre son exploitation plus difficile.

## 13.4 `_FORTIFY_SOURCE`

La fortification ajoute des vérifications supplémentaires à certaines fonctions quand le compilateur peut connaître la taille des objets.

Exemple d’usage courant :

```bash
gcc -O2 -D_FORTIFY_SOURCE=2 ...
```

Cela ne remplace pas un code sûr, mais ajoute une défense utile.

## 13.5 AddressSanitizer (ASan)

Excellent outil de détection en développement et tests.

Compilation typique :

```bash
clang -fsanitize=address -g -O1 file.c -o testprog
```

ou avec GCC/Clang selon environnement.

ASan détecte notamment :

- stack buffer overflow ;
- heap buffer overflow ;
- use-after-free ;
- certaines corruptions de redzones.

## 13.6 UndefinedBehaviorSanitizer (UBSan)

Utile pour attraper divers comportements indéfinis pouvant contribuer à des corruptions mémoire.

## 13.7 Fuzzing

Le fuzzing envoie énormément d’entrées variées ou mutées pour faire émerger les plantages et les comportements inattendus.

Très efficace pour :

- parsers ;
- formats binaires ;
- protocoles ;
- logiciels qui traitent de la donnée externe.

## 13.8 Analyse statique

Des outils d’analyse statique repèrent de nombreuses classes de bugs :

- dépassement de bornes ;
- tailles incohérentes ;
- flux de données non sûrs ;
- fonctions dangereuses ;
- index non validés.

## 13.9 Analyse dynamique et debugging

Avec un debugger, des crash dumps, des logs et des sanitizers, on peut comprendre :

- quel buffer a débordé ;
- avec quelle taille ;
- quelle zone mémoire a été touchée ;
- si le crash est reproductible.

## 13.10 Indices concrets qu’il faut suspecter un overflow

- crash sur entrée longue ;
- comportement bizarre dépendant de la taille d’entrée ;
- corruption aléatoire ;
- messages du type `stack smashing detected` ;
- crash dans `malloc`, `free`, `printf`, `strlen`, alors que le vrai bug est plus tôt ;
- différences entre debug et release ;
- fonctionnement correct sur petites entrées, cassé sur grosses entrées.

---

# 14. Mitigations et protections modernes

Important : une mitigation **réduit le risque**, elle ne remplace jamais la correction du code.

## 14.1 Validation stricte des tailles

Toujours connaître :

- la capacité du buffer destination ;
- la taille réelle des données source ;
- la marge pour `\0` si nécessaire.

## 14.2 APIs plus sûres

Préférer selon le contexte :

- `fgets()` plutôt que `gets()`
- `snprintf()` plutôt que `sprintf()`
- validation explicite avant `memcpy()`
- calculs de tailles avec `size_t`
- wrappers internes de copie vérifiée

## 14.3 Éviter les longueurs implicites

Une API qui reçoit juste `char *buf` est souvent fragile.  
Une API plus sûre reçoit :

```c
int read_name(char *buf, size_t buf_size);
```

## 14.4 Stack canaries

Un “canary” est placé entre certaines variables de pile et les données de contrôle.  
Si l’overflow l’écrase, le programme détecte la corruption avant le retour normal.

Compilation courante :

```bash
-fstack-protector-strong
```

## 14.5 NX / DEP

La mémoire de données n’est pas censée être exécutable.  
Cela complique certaines formes d’exploitation.

## 14.6 ASLR

L’**Address Space Layout Randomization** randomise l’emplacement de zones mémoire pour rendre plus difficile la prédiction des adresses.

Sous Linux, cette politique est liée notamment à :

```text
/proc/sys/kernel/randomize_va_space
```

Valeurs classiques :

- `0` : pas d’ASLR
- `1` : randomisation partielle
- `2` : randomisation plus complète

## 14.7 PIE

Un exécutable compilé en **PIE** (Position Independent Executable) bénéficie mieux de l’ASLR, car son image peut être chargée à des adresses variables.

## 14.8 RELRO

Protège certaines structures de relocation ELF contre des modifications tardives.

## 14.9 Fortification glibc (`_FORTIFY_SOURCE`)

Ajoute des vérifications supplémentaires quand la taille des objets est connue ou déductible.

## 14.10 CFI / CFG

Les protections de contrôle de flux rendent plus difficile le détournement d’appels indirects.

Sur Windows, par exemple, **Control Flow Guard (CFG)** est une mitigation importante contre des vulnérabilités de corruption mémoire.

## 14.11 SafeStack / protections avancées

Certains compilateurs ou environnements ajoutent des stratégies plus avancées pour séparer les données sensibles de pile ou renforcer les vérifications.

## 14.12 Séparation des privilèges

Même si un composant a un bug mémoire, il doit idéalement :

- tourner avec peu de droits ;
- être isolé ;
- ne pas exposer directement les secrets ;
- être compartimenté.

## 14.13 Langages à sûreté mémoire

Quand c’est possible, utiliser ou intégrer des composants dans des langages à meilleure sûreté mémoire réduit drastiquement cette classe de bugs.

Le point n’est pas de “remplacer tout C demain”, mais de comprendre qu’un design mémoire plus sûr réduit structurellement le risque.

---

# 15. Le rôle du système Linux et du système de fichiers `/proc`

Les références fournies mentionnent **`/proc`**, et c’est très pertinent.

## 15.1 Qu’est-ce que `/proc` ?

`/proc` est un **pseudo-système de fichiers** qui expose des informations noyau et processus.

Il ne contient pas des “vrais fichiers disque” classiques : il fournit une vue dynamique du système.

## 15.2 Pourquoi c’est utile pour ce module ?

Parce qu’on peut observer :

- l’espace mémoire d’un processus ;
- certaines protections du noyau ;
- des paramètres liés à l’ASLR ;
- le layout mémoire général.

## 15.3 `/proc/<pid>/maps`

Ce fichier montre les mappings mémoire d’un processus :

- plages d’adresses ;
- permissions (`rwx`) ;
- bibliothèques ;
- pile ;
- heap ;
- mappings anonymes.

Exemple de lecture :

```bash
cat /proc/$$/maps
```

Sur un vrai binaire :

```bash
cat /proc/<pid>/maps
```

Cela permet de visualiser :

- où se trouve le heap ;
- où se trouve la stack ;
- quelles régions sont exécutables ;
- quels segments sont mappés.

## 15.4 `/proc/<pid>/smaps`

Version plus détaillée des mappings, utile pour des informations de consommation mémoire.

## 15.5 `/proc/sys/kernel/randomize_va_space`

Paramètre noyau important pour l’ASLR.

Lecture :

```bash
cat /proc/sys/kernel/randomize_va_space
```

Cela permet de vérifier si la randomisation d’adresses est active.

## 15.6 Ce qu’il faut comprendre pédagogiquement

`/proc` te permet de faire le lien entre la théorie et la réalité :

- la stack n’est pas qu’un concept de cours ;
- le heap n’est pas abstrait ;
- l’ASLR n’est pas magique : c’est un comportement observable ;
- les permissions mémoire (`r`, `w`, `x`) ont un impact concret sur l’exploitation.

## 15.7 Ce que `/proc` ne fait pas

`/proc` ne “répare” pas les buffer overflows.  
Il aide à **observer** le système, diagnostiquer, comprendre les protections et analyser le comportement d’un processus.

---

# 16. Méthodologie d’audit et de revue de code

Quand tu audites du code C/C++ pour les buffer overflows, procède méthodiquement.

## 16.1 Étape 1 : cartographier les entrées

Chercher toutes les données non fiables :

- stdin ;
- argv ;
- variables d’environnement ;
- sockets ;
- fichiers ;
- IPC ;
- décompression / parsing.

## 16.2 Étape 2 : repérer les sinks dangereux

Fonctions et patterns à haut risque :

- `gets`
- `strcpy`
- `strcat`
- `sprintf`
- `%s`
- `memcpy`, `memmove`, `memset`
- `read`, `recv` avec longueur mal gérée
- boucles d’indexation manuelle
- arithmétique de pointeurs

## 16.3 Étape 3 : vérifier les contrats de taille

Pour chaque buffer :

- qui l’alloue ?
- quelle est sa capacité exacte ?
- cette capacité est-elle transmise à la fonction ?
- la taille copiée est-elle vérifiée ?
- la terminaison nulle est-elle garantie si nécessaire ?

## 16.4 Étape 4 : vérifier les conversions de types

Les bugs apparaissent souvent quand on mélange :

- `int`
- `unsigned`
- `size_t`
- `ssize_t`
- troncatures
- cast implicites

## 16.5 Étape 5 : examiner les boucles

Chercher :

- `<=` au lieu de `<`
- index signés / non signés
- bornes issues de l’utilisateur
- calculs `len - 1` quand `len == 0`
- double incrément involontaire

## 16.6 Étape 6 : vérifier les allocations

Toujours revoir :

- `malloc(strlen(x))` au lieu de `strlen(x) + 1`
- oubli de `sizeof(*ptr)`
- multiplication potentiellement débordante
- taille calculée depuis une source non fiable

## 16.7 Étape 7 : tester avec entrées limites

- chaîne vide ;
- taille maximale ;
- taille maximale + 1 ;
- très grande taille ;
- caractères spéciaux ;
- données binaires ;
- absence de `\0` là où le code en suppose un.

## 16.8 Étape 8 : compiler en mode durci

En dev/test :

```bash
gcc -Wall -Wextra -Wpedantic -g -O1 \
    -fsanitize=address,undefined \
    -fno-omit-frame-pointer \
    file.c -o file
```

En release durcie selon environnement :

```bash
gcc -O2 -D_FORTIFY_SOURCE=2 \
    -fstack-protector-strong \
    -fPIE -pie \
    -Wl,-z,relro,-z,now \
    file.c -o file
```

> Les options exactes dépendent de la toolchain, de la distro et du contexte, mais cette philosophie est bonne :  
> **warnings + durcissement + tests dynamiques**.

---

# 17. Checklist de prévention pour développeur C/C++

## 17.1 Règles de base

- Toujours connaître la taille du buffer destination.
- Toujours valider la taille des données source.
- Toujours réserver la place pour `\0` quand on manipule des chaînes C.
- Toujours préférer des APIs prenant explicitement la taille.
- Toujours vérifier les retours des fonctions de formatage/copie/lecture.
- Toujours se méfier des conversions de type.

## 17.2 Règles de design

- Passer `buffer + buffer_size` dans les fonctions.
- Éviter les tailles “magiques” codées en dur à plusieurs endroits.
- Centraliser les wrappers sûrs.
- Isoler le parsing de données non fiables.
- Réduire les privilèges du processus.

## 17.3 Règles de build

- Warnings élevés
- Stack protector
- FORTIFY
- PIE
- RELRO
- ASan/UBSan en CI ou tests
- revue statique régulière

## 17.4 Règles de tests

- tests de limites ;
- fuzzing ;
- gros inputs ;
- entrées malformées ;
- variations de format ;
- corpus de régression.

---

# 18. Pièges fréquents et fausses bonnes idées

## 18.1 “J’utilise `strncpy`, donc c’est sécurisé”

Non.  
`strncpy()` n’est pas une solution miracle. Elle peut :

- ne pas null-terminer ;
- remplir de zéros inutilement ;
- être mal comprise ;
- masquer des bugs logiques.

## 18.2 “Ça ne crash pas, donc c’est bon”

Faux.  
La corruption silencieuse est parfois pire qu’un crash.

## 18.3 “L’utilisateur n’enverra jamais ça”

En sécurité, il faut raisonner comme si quelqu’un **essaiera exprès** d’envoyer exactement ce qui casse ton hypothèse.

## 18.4 “On a l’ASLR, donc c’est réglé”

Non.  
L’ASLR complique l’exploitation. Il ne corrige pas le bug.

## 18.5 “On a un canary, donc on est protégés”

Le canary aide, mais :

- pas contre toutes les corruptions ;
- pas contre toutes les primitives ;
- pas contre toute logique applicative brisée.

## 18.6 “C’est juste un off-by-one”

Un seul octet peut suffire à transformer un programme correct en programme vulnérable.

## 18.7 “Le bug est seulement dans un outil interne”

Les outils internes traitent parfois :

- des fichiers externes ;
- des archives ;
- des imports ;
- des logs ;
- des données fournies par d’autres systèmes.

“Interne” ne veut pas dire “sans risque”.

---

# 19. Synthèse ultra courte à mémoriser

## 19.1 Définition

Un buffer overflow, c’est lorsqu’un programme écrit au-delà de la capacité prévue d’un buffer.

## 19.2 Cause principale

Absence de vérification correcte des bornes.

## 19.3 Conséquences

- crash ;
- corruption mémoire ;
- fuite d’informations ;
- déni de service ;
- exécution de code ;
- élévation de privilèges.

## 19.4 Types majeurs

- stack-based ;
- heap-based ;
- global/static ;
- off-by-one ;
- plus largement : out-of-bounds write.

## 19.5 Prévention

- APIs bornées ;
- tailles explicites ;
- revue de code ;
- warnings ;
- sanitizers ;
- mitigations de compilation et OS.

## 19.6 Idée clé

Les mitigations réduisent l’impact et compliquent l’exploitation, mais **le vrai remède est de ne jamais écrire hors bornes**.

---

# 20. Questions/réponses de révision

## Q1. Qu’est-ce qu’un buffer ?

Une zone de mémoire allouée pour stocker temporairement des données.

## Q2. Qu’est-ce qu’un buffer overflow ?

Une écriture qui dépasse la capacité d’un buffer ou sort de ses bornes.

## Q3. Quelle différence entre bug et attaque ?

Le bug est l’erreur logicielle ; l’attaque est l’exploitation malveillante de cette erreur.

## Q4. Pourquoi C est-il particulièrement concerné ?

Parce que C laisse beaucoup de contrôle mémoire au développeur et n’impose pas de vérification automatique des bornes.

## Q5. `gets()` est-elle dangereuse ?

Oui, extrêmement. Elle ne connaît pas la taille du buffer destination.

## Q6. `memcpy()` est-elle sûre ?

Seulement si la taille passée est correcte. Sinon elle copie aveuglément.

## Q7. Quelle différence entre stack overflow et stack-based buffer overflow ?

Le premier peut désigner l’épuisement de pile ; le second est une corruption d’un buffer situé sur la pile.

## Q8. Pourquoi un off-by-one est-il sérieux ?

Parce qu’un seul octet peut corrompre une structure sensible.

## Q9. Que fait l’ASLR ?

Elle randomise l’emplacement de régions mémoire pour compliquer les attaques basées sur des adresses prévisibles.

## Q10. Où vérifier l’ASLR sous Linux ?

Notamment via :

```bash
cat /proc/sys/kernel/randomize_va_space
```

## Q11. À quoi sert `/proc/<pid>/maps` ?

À visualiser les mappings mémoire d’un processus et leurs permissions.

## Q12. Que fait AddressSanitizer ?

Il détecte dynamiquement de nombreuses corruptions mémoire, dont des buffer overflows stack et heap.

## Q13. Les mitigations remplacent-elles la correction ?

Jamais. Elles réduisent le risque, elles ne suppriment pas le bug.

## Q14. Quelle règle simple faut-il retenir ?

Toujours lier les données à une taille fiable et vérifier les bornes avant copie/écriture.

---

# 21. Conclusion

Le buffer overflow est un sujet fondamental parce qu’il enseigne une vérité très importante en cybersécurité :

> **une petite erreur de logique ou de taille peut devenir une faille majeure.**

Pour vraiment maîtriser ce sujet, il faut savoir relier :

- le code source ;
- le modèle mémoire ;
- le comportement du compilateur ;
- les protections de l’OS ;
- la méthodologie d’audit ;
- les conséquences sécurité.

Retenir uniquement “`strcpy` est dangereux” n’est pas suffisant.

Ce qu’il faut comprendre en profondeur, c’est :

1. **où vivent les buffers** ;
2. **quelles hypothèses ton code fait sur leur taille** ;
3. **quelles hypothèses sont réellement garanties** ;
4. **comment une écriture hors bornes peut corrompre l’état interne** ;
5. **comment concevoir, tester et compiler pour réduire ce risque**.

Si tu veux progresser vite sur ce thème, entraîne-toi à faire trois choses sur chaque morceau de code C :

- identifier le buffer ;
- identifier sa capacité réelle ;
- identifier qui garantit que l’écriture ne la dépasse jamais.

Si tu ne peux pas répondre proprement à ces trois questions, il y a un risque.

---

# 22. Références et ressources

## Références techniques principales

1. **MITRE CWE-120 — Classic Buffer Overflow**  
   https://cwe.mitre.org/data/definitions/120.html

2. **MITRE CWE-121 — Stack-based Buffer Overflow**  
   https://cwe.mitre.org/data/definitions/121.html

3. **MITRE CWE-122 — Heap-based Buffer Overflow**  
   https://cwe.mitre.org/data/definitions/122.html

4. **MITRE CWE-787 — Out-of-bounds Write**  
   https://cwe.mitre.org/data/definitions/787.html

5. **OWASP — Buffer Overflow**  
   https://owasp.org/www-community/vulnerabilities/Buffer_Overflow

6. **OWASP — Buffer Overflow Attack**  
   https://owasp.org/www-community/attacks/Buffer_overflow_attack

7. **OWASP WSTG — Testing for Buffer Overflow**  
   https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/13-Testing_for_Buffer_Overflow

8. **Linux man-pages — `proc(5)`**  
   https://man7.org/linux/man-pages/man5/proc.5.html

9. **Linux man-pages — `/proc/<pid>/maps`**  
   https://man7.org/linux/man-pages/man5/proc_pid_maps.5.html

10. **Linux man-pages — `/proc/sys/kernel/randomize_va_space`**  
    https://man7.org/linux/man-pages/man5/proc_sys_kernel.5.html

11. **Kernel documentation — The `/proc` filesystem**  
    https://docs.kernel.org/filesystems/proc.html

12. **GCC documentation — warning options**  
    https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html

13. **Clang/LLVM — AddressSanitizer documentation**  
    https://clang.llvm.org/docs/AddressSanitizer.html

14. **Clang/LLVM — UndefinedBehaviorSanitizer documentation**  
    https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html

15. **Red Hat — compiler flags for stack protection**  
    https://developers.redhat.com/articles/2022/06/02/use-compiler-flags-stack-protection-gcc-and-clang

16. **Red Hat — `_FORTIFY_SOURCE` secure coding guide**  
    https://developers.redhat.com/articles/2023/07/04/developers-guide-secure-coding-fortifysource

17. **man7 — `gets(3)`**  
    https://man7.org/linux/man-pages/man3/gets.3.html

18. **Microsoft — Control Flow Guard**  
    https://learn.microsoft.com/en-us/windows/win32/secbp/control-flow-guard

19. **NIST — Safer Languages**  
    https://www.nist.gov/itl/ssd/software-quality-group/safer-languages

20. **NIST — Source Code Security Analyzers**  
    https://www.nist.gov/itl/ssd/software-quality-group/source-code-security-analyzers

## Ressources complémentaires à lire / regarder

- What is buffer overflow
- What Is a Buffer Overflow Attack
- buffer overflow attack types and prevention methods
- How Buffer Overflow Attacks Work
- Buffer Overflow in Cyber Security - What is, Types, & Consequences
- Overflow Vulnerabilities
- Consequences of Buffer Overflow
- Mitigation Strategies
- Common C Code Vulnerabilities and Mitigations relate to buffer overflows
- The `/proc` filesystem

## Conseils de travail

Pour transformer ce cours en vraie compétence :

1. relis les exemples de code et réécris-les toi-même ;
2. compile en mode warning élevé ;
3. teste avec des tailles limites ;
4. active ASan sur de petits programmes ;
5. observe `/proc/<pid>/maps` sur un programme simple ;
6. entraîne-toi à repérer les erreurs de taille à l’œil nu.

---

# Annexe A — Petit aide-mémoire pratique

## Build “dev / détection de bugs”

```bash
gcc -Wall -Wextra -Wpedantic -Wformat=2 -Wconversion \
    -g -O1 -fno-omit-frame-pointer \
    -fsanitize=address,undefined \
    main.c -o app
```

## Build “release durci” (exemple générique)

```bash
gcc -O2 -D_FORTIFY_SOURCE=2 \
    -fstack-protector-strong \
    -fPIE -pie \
    -Wl,-z,relro,-z,now \
    main.c -o app
```

## Vérifier l’ASLR

```bash
cat /proc/sys/kernel/randomize_va_space
```

## Visualiser les mappings d’un processus

```bash
cat /proc/<pid>/maps
```

ou pour le shell courant :

```bash
cat /proc/$$/maps
```

---

# Annexe B — Tableau de synthèse

| Concept | Idée clé |
|---|---|
| Buffer | Zone mémoire de taille finie |
| Buffer overflow | Écriture au-delà de cette taille |
| Cause classique | Absence de vérification de bornes |
| Zones touchées | Stack, heap, global/static |
| Impact minimal | Crash / corruption |
| Impact maximal | Exécution arbitraire / élévation |
| Détection | Revue, warnings, ASan, fuzzing, analyse statique |
| Prévention | APIs bornées, tailles explicites, design sûr |
| Mitigations | Canary, ASLR, NX, PIE, RELRO, FORTIFY, CFG/CFI |
| Observation Linux | `/proc/<pid>/maps`, `/proc/sys/kernel/randomize_va_space` |

---

# Annexe C — Formulations prêtes pour l’oral

## Définition courte

> Un buffer overflow est une écriture hors limites dans une zone mémoire allouée de taille finie.

## Définition pédagogique

> Un programme réserve une boîte de taille fixe pour y stocker des données. Si on y écrit plus que la boîte ne peut contenir, on écrase la mémoire voisine : c’est un buffer overflow.

## Définition sécurité

> Un buffer overflow est une vulnérabilité mémoire qui peut conduire à une corruption de données, un crash, ou, selon le contexte, à un détournement du flux de contrôle et à une compromission plus grave.

## Différence bug / attaque

> Le buffer overflow est le défaut logiciel ; l’attaque par buffer overflow est l’exploitation malveillante de ce défaut.

---

# Fin
