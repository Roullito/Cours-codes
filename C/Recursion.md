# 📘 C - Recursion (Récursivité en C)

## 📚 Resources
Read or watch:
- [Recursion, introduction](#)
- [What on Earth is Recursion?](#)
- [C - Recursion](#)
- [C Programming Tutorial 85, Recursion pt.1](#)
- [C Programming Tutorial 86, Recursion pt.2](#)

## 🎯 Learning Objectives
By the end of this project, you should be able to explain:
- **Qu'est-ce que la récursivité ?**
- **Comment implémenter la récursivité en C ?**
- **Quand utiliser la récursivité ?**
- **Quand éviter la récursivité ?**

---

## 🔍 Qu'est-ce que la Récursivité ?
La **récursivité** est un concept en programmation où une fonction s'appelle elle-même pour résoudre un problème en le décomposant en sous-problèmes plus petits.

Un programme récursif repose sur deux éléments fondamentaux :
1. **Le cas de base** (base case) : une condition qui arrête la récursion.
2. **Le cas récursif** (recursive case) : où la fonction s'appelle elle-même avec un problème plus petit.

---

## 🏗 Structure d'une Fonction Récursive
Chaque fonction récursive suit un modèle général :
```c
retour function(parameters) {
    if (condition de base)
        return résultat_final;
    else
        return opération + function(nouvelle_valeur);
}
```
L'idée est de **réduire le problème progressivement** jusqu'à atteindre un cas trivial (base case).

### 🔹 Exemple 1 : Calcul Factoriel
Le **factoriel** d’un nombre `n` (noté `n!`) est défini comme :
```
n! = n × (n-1) × (n-2) × ... × 1
```
Voici une implémentation récursive en C :
```c
#include "main.h"

int factorial(int n) {
    if (n == 0 || n == 1)
        return 1; // Cas de base
    return n * factorial(n - 1); // Cas récursif
}
```
**Explication :**
1. Si `n` est `0` ou `1`, on retourne `1` (car `0! = 1! = 1`).
2. Sinon, on retourne `n × factorial(n - 1)`.
3. À chaque appel, `n` diminue jusqu'à atteindre `0`.

### 🔹 Exécution de `factorial(5)`
```
5! = 5 × 4 × 3 × 2 × 1
factorial(5)
-> 5 * factorial(4)
-> 5 * (4 * factorial(3))
-> 5 * (4 * (3 * factorial(2)))
-> 5 * (4 * (3 * (2 * factorial(1))))
-> 5 * (4 * (3 * (2 * 1)))
-> 120
```
Chaque appel crée une nouvelle **instance de la fonction** jusqu'à atteindre le cas de base (`1`).

---

## 📖 Autres Exemples de Récursivité

### 🔹 Exemple 2 : Somme des N premiers nombres
```c
int sum(int n) {
    if (n == 0)
        return 0;
    return n + sum(n - 1);
}
```
**Explication :**
- Cas de base : `sum(0) = 0`
- Cas récursif : `sum(n) = n + sum(n-1)`
- **Exécution :**
```
sum(4)
-> 4 + sum(3)
-> 4 + (3 + sum(2))
-> 4 + (3 + (2 + sum(1)))
-> 4 + (3 + (2 + (1 + sum(0))))
-> 4 + 3 + 2 + 1 + 0
-> 10
```

### 🔹 Exemple 3 : Suite de Fibonacci
La suite de Fibonacci suit la relation :
```
F(n) = F(n-1) + F(n-2) avec F(0) = 0 et F(1) = 1
```
Implémentation en C :
```c
int fibonacci(int n) {
    if (n == 0)
        return 0;
    if (n == 1)
        return 1;
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

---

## ✅ Quand Utiliser la Récursivité ?
La récursivité est idéale lorsque :
- Le problème **peut être décomposé en sous-problèmes identiques**.
- Un **parcours récursif** est naturel (ex. arbres, graphes).
- La solution **récursive est plus simple à lire** que l’itérative.

**Exemples où elle est utile :**
- **Parcours d’arbres (ex : BST, Trie).**
- **Recherche DFS/BFS en graphes.**
- **Backtracking (résolution de Sudoku, labyrinthe).**

---

## 🚨 Quand NE PAS Utiliser la Récursivité ?
La récursivité **n'est pas toujours efficace**. Elle doit être évitée si :
- **Trop d’appels récursifs** → risque de **dépassement de pile (stack overflow)**.
- Un **algorithme itératif** est plus performant et utilise moins de mémoire.
- Les appels récursifs **introduisent trop de duplication de calculs** (ex : Fibonacci sans mémoïsation est inefficace).

**Optimisation possible :**
1. **Mémoïsation** (stocker les résultats des appels précédents).
2. **Utilisation de la récursivité de queue** (`tail recursion`).

---

## 📜 Exigences du Projet
- **Pas de boucles** (`for`, `while`, `do...while` sont interdits).
- **Pas de variables globales.**
- **Respect des normes de codage Betty.**
- **Compiler avec `gcc -Wall -Werror -Wextra -pedantic -std=gnu89` sur Ubuntu 20.04.**

---

## 🚀 Conclusion
La récursivité est un outil puissant en programmation, mais elle doit être utilisée avec prudence. Il est crucial d’identifier **quand elle est adaptée** et **comment éviter ses pièges** (comme les appels redondants). En combinant **une bonne structuration des cas de base** et **des optimisations**, vous pouvez créer des programmes efficaces et élégants. 🔥

🎯 **Prochaines étapes :**
- Mettre en pratique ces concepts avec **des exercices de récursivité** !

🚀 **Happy coding!**
