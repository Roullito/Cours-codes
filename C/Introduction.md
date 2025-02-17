# C - Hello World

## Table des matières
- [Introduction](#introduction)
- [1. Structure d'un programme en C](#1-structure-dun-programme-en-c)
- [2. Compilation et exécution](#2-compilation-et-execution)
  - [a) Compilation avec `gcc`](#a-compilation-avec-gcc)
  - [b) Exécution du programme](#b-execution-du-programme)
- [3. Gestion des erreurs](#3-gestion-des-erreurs)
- [4. Formatage et bonnes pratiques](#4-formatage-et-bonnes-pratiques)
- [5. Exercices](#5-exercices)

## Introduction
Le langage C est un langage de programmation bas niveau, utilisé pour développer des systèmes d'exploitation, des logiciels embarqués et des applications performantes. Cette première étape couvre les bases pour écrire, compiler et exécuter un programme en C.

## 1. Structure d'un programme en C
Un programme en C est généralement structuré comme suit :

```c
#include <stdio.h>

int main(void)
{
    printf("Hello, World!\n");
    return 0;
}
```

### Explication :
- `#include <stdio.h>` : Inclut la bibliothèque standard pour l'affichage.
- `int main(void)` : Point d'entrée du programme.
- `printf("Hello, World!\n");` : Affiche un texte à l'écran.
- `return 0;` : Indique que le programme s'est exécuté correctement.

## 2. Compilation et exécution

### a) Compilation avec `gcc`
GCC (GNU Compiler Collection) est un compilateur utilisé pour transformer le code source en exécutable.

```bash
gcc hello.c -o hello
```

- `gcc` : Commande du compilateur.
- `hello.c` : Fichier source en C.
- `-o hello` : Génère un fichier exécutable nommé `hello`.

### b) Exécution du programme
Après la compilation, exécute le programme avec :

```bash
./hello
```

## 3. Gestion des erreurs
Si une erreur de compilation se produit, GCC affichera des messages d’erreur. Quelques erreurs classiques :

- **Erreur de syntaxe** : Oubli d'un `;`, parenthèse incorrecte, etc.
- **Problème d'inclusion de bibliothèques** : Assurez-vous que `#include <stdio.h>` est bien présent.
- **Nom de fichier incorrect** : Vérifiez que vous compilez bien le fichier voulu.
- **Permissions insuffisantes** : Ajoutez les permissions d'exécution avec `chmod +x hello` si nécessaire.

Utilisez `-Wall` pour voir plus d’avertissements et améliorer la lisibilité des erreurs :

```bash
gcc -Wall hello.c -o hello
```

## 4. Formatage et bonnes pratiques
- Indentation correcte avec 4 espaces ou une tabulation.
- Utilisation de commentaires (`// Commentaire` ou `/* Commentaire */`).
- Utilisation de noms de variables explicites et descriptifs.
- Ajout d'une documentation minimale avec des commentaires pour expliquer le code.
- Séparer le code en plusieurs fichiers si nécessaire pour améliorer la lisibilité et la maintenabilité.

## 5. Exercices
1. Modifier le programme pour afficher votre prénom à la place de "Hello, World!".
2. Compiler et exécuter le programme.
3. Introduire une erreur volontaire (ex: oublier `;`) et observer l'erreur retournée.
4. Ajouter un deuxième message avec `printf` et tester son affichage.
5. Tester l'exécution du programme sur un autre ordinateur ou un environnement différent.

---

Ce document constitue votre premier support pour bien démarrer en C. La prochaine étape abordera les variables, les conditions et les boucles `while`. 🚀
