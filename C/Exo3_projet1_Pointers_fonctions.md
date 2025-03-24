# Explication ultra détaillée du programme `calc`

Ce document explique **ligne par ligne** comment fonctionne le programme `calc`, en détaillant la syntaxe, le passage des arguments entre les fichiers, et ce qui se passe en mémoire.

---

## 📌 Objectif du programme
Le programme `calc` permet d'effectuer des **opérations mathématiques simples** à partir de la ligne de commande en utilisant des **pointeurs de fonction**.

### **Usage :**
```bash
./calc num1 operator num2
```
- `num1` : Premier nombre (entier)
- `operator` : Opération à effectuer (`+`, `-`, `*`, `/`, `%`)
- `num2` : Deuxième nombre (entier)

### **Exemples d'exécution :**
```bash
$ ./calc 10 + 5
15

$ ./calc 20 / 4
5

$ ./calc 10 % 3
1
```

### **Gestion des erreurs :**
- **Nombre d'arguments incorrect →** `Error` et `exit(98)`.
- **Opérateur invalide →** `Error` et `exit(99)`.
- **Division ou modulo par zéro →** `Error` et `exit(100)`.

---

## 📁 Passage des arguments et allocation mémoire
### 📌 Comment les arguments sont passés entre les fichiers ?

Lorsque le programme est exécuté avec :
```bash
./calc 1024 + 98
```
Voici comment les arguments sont **stockés en mémoire** :
```
argv[0] → "./calc"
argv[1] → "1024"
argv[2] → "+"
argv[3] → "98"
argv[4] → NULL (fin des arguments)
```

1. `main()` **récupère les arguments** depuis `argv`.
2. `atoi(argv[1])` **convertit** `"1024"` en `int` → `num1 = 1024`.
3. `atoi(argv[3])` **convertit** `"98"` en `int` → `num2 = 98`.
4. `get_op_func(argv[2])` recherche la fonction **associée à `"+"`**.
5. Le **pointeur de fonction** est retourné vers `op_add()`.
6. `op_add(1024, 98)` est exécuté.
7. Le résultat `1122` est affiché.

### 📌 Schéma mémoire après chargement des arguments
```
Stack:
┌──────────────┐
│ argv[0] = "./calc" │
│ argv[1] = "1024"  │ → num1 = 1024 (converti par atoi)
│ argv[2] = "+"    │ → Opérateur
│ argv[3] = "98"    │ → num2 = 98 (converti par atoi)
│ argv[4] = NULL   │ (Fin des arguments)
└──────────────┘
Heap:
┌──────────────┐
│ ops[]        │ → Table des opérations (`+`, `-`, etc.)
└──────────────┘
```
---

## 📌 Explication détaillée des fichiers (ligne par ligne)

### 📌 1. `3-calc.h` (Fichier d'en-tête)
```c
#ifndef CALC_H
#define CALC_H

/* Structure associant un opérateur à sa fonction */
typedef struct op
{
    char *op;               // Chaine contenant l'opérateur (+, -, *, /, %)
    int (*f)(int a, int b); // Pointeur vers une fonction prenant deux entiers
} op_t;

/* Déclaration des fonctions d'opérations */
int op_add(int a, int b);
int op_sub(int a, int b);
int op_mul(int a, int b);
int op_div(int a, int b);
int op_mod(int a, int b);

/* Déclaration de la fonction de sélection de l'opération */
int (*get_op_func(char *s))(int, int);

#endif /* CALC_H */
```
---

### 📌 2. `3-op_functions.c` (Implémentation des opérations)
```c
#include "3-calc.h"
#include <stdio.h>
#include <stdlib.h>

int op_add(int a, int b) { return (a + b); }
int op_sub(int a, int b) { return (a - b); }
int op_mul(int a, int b) { return (a * b); }

int op_div(int a, int b)
{
    if (b == 0)
    {
        printf("Error\n");
        exit(100);
    }
    return (a / b);
}

int op_mod(int a, int b)
{
    if (b == 0)
    {
        printf("Error\n");
        exit(100);
    }
    return (a % b);
}
```
---

### 📌 3. `3-get_op_func.c` (Sélection de l'opération)
```c
#include "3-calc.h"
#include <string.h>

int (*get_op_func(char *s))(int, int)
{
    op_t ops[] = {
        {"+", op_add},
        {"-", op_sub},
        {"*", op_mul},
        {"/", op_div},
        {"%", op_mod},
        {NULL, NULL}
    };
    int i = 0;

    while (ops[i].op != NULL)
    {
        if (strcmp(ops[i].op, s) == 0)
            return (ops[i].f);
        i++;
    }
    return (NULL);
}
```
---

### 📌 4. `3-main.c` (Programme principal)
```c
#include "3-calc.h"
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    int num1, num2, result;
    int (*operation)(int, int);

    if (argc != 4)
    {
        printf("Error\n");
        exit(98);
    }

    num1 = atoi(argv[1]);
    num2 = atoi(argv[3]);
    operation = get_op_func(argv[2]);

    if (operation == NULL)
    {
        printf("Error\n");
        exit(99);
    }

    if ((*argv[2] == '/' || *argv[2] == '%') && num2 == 0)
    {
        printf("Error\n");
        exit(100);
    }

    result = operation(num1, num2);
    printf("%d\n", result);

    return (0);
}
```
---

## 🎯 **Conclusion**
Ce programme **utilise les pointeurs de fonction** pour exécuter **dynamiquement** l’opération choisie. 🚀 Il gère les erreurs et affiche un résultat en utilisant une **structure bien organisée** et un **passage efficace des arguments en mémoire**.

