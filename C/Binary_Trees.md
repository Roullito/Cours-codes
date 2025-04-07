# 🌲 C - Binary Trees

## 📚 Ressources utiles

- [Binary tree - Wikipedia](https://en.wikipedia.org/wiki/Binary_tree)
- [Data Structure and Algorithms - Tree](https://www.tutorialspoint.com/data_structures_algorithms/tree_data_structure.htm)
- [Tree Traversal](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/)
- [Binary Search Tree](https://www.geeksforgeeks.org/binary-search-tree-data-structure/)
- [Binary Tree Explanation - YouTube](https://www.youtube.com/results?search_query=binary+tree+data+structure)

---

## 🎯 Objectifs d’apprentissage

À la fin de ce projet, vous serez capable d'expliquer :

- Ce qu'est un arbre binaire.
- La différence entre un **binary tree** et un **binary search tree (BST)**.
- Le gain en complexité temporelle par rapport à des listes chaînées.
- Les notions de **profondeur (depth)**, **hauteur (height)**, **taille (size)**.
- Les différents parcours d'un arbre.
- Ce qu'est un arbre **complet**, **plein**, **parfait**, **équilibré**.

---

## 🧠 Définition

### 🔸 Arbre binaire

Un **arbre binaire** est une structure de données composée de **nœuds** (nodes) où :

- Chaque nœud a **au plus deux enfants** : un gauche (`left`) et un droit (`right`).
- Chaque nœud contient :
  - Une valeur (ici un `int n`)
  - Un pointeur vers son parent (ou `NULL` s'il est la racine)
  - Deux pointeurs vers ses enfants gauche et droit

```c
typedef struct binary_tree_s
{
    int n;
    struct binary_tree_s *parent;
    struct binary_tree_s *left;
    struct binary_tree_s *right;
} binary_tree_t;
```

---

## 🆚 Binary Tree vs BST

| Critère         | Binary Tree               | Binary Search Tree (BST)          |
|-----------------|---------------------------|-----------------------------------|
| Organisation    | Aucun ordre particulier   | Sous-arbre gauche < nœud < droit  |
| Utilisation     | Hiérarchies, arbres syntaxiques | Recherche rapide, tri |
| Insertion       | Libre                     | Basée sur la valeur               |

---

## ⏱️ Complexité et Avantages

- **Tableaux** : insertion O(n), suppression O(n)
- **Listes chaînées** : insertion O(1), recherche O(n)
- **Arbres binaires équilibrés** : recherche, insertion, suppression O(log n)

---

## 📏 Propriétés des arbres

- **Hauteur (height)** : longueur du chemin le plus long vers une feuille
- **Profondeur (depth)** : distance entre un nœud et la racine
- **Taille (size)** : nombre total de nœuds

---

## 🔄 Parcours d’un arbre

### 🔹 Parcours Préfixé (Pre-order)

Ordre : racine → gauche → droite

### 🔹 Parcours Infixé (In-order)

Ordre : gauche → racine → droite

### 🔹 Parcours Postfixé (Post-order)

Ordre : gauche → droite → racine

---

## 🌳 Types d'arbres

- **Arbre complet** : tous les niveaux sont remplis sauf peut-être le dernier, de gauche à droite
- **Arbre plein** : chaque nœud a 0 ou 2 enfants
- **Arbre parfait** : arbre plein + toutes les feuilles au même niveau
- **Arbre équilibré** : hauteur gauche ≈ hauteur droite (±1)

---

## ⚙️ Création d’un nœud

```c
binary_tree_t *binary_tree_node(binary_tree_t *parent, int value)
```

- Crée un nœud avec un parent
- `left` et `right` sont initialisés à `NULL`

---

## ➕ Insertion

### À gauche :

```c
binary_tree_insert_left(parent, value);
```

- Si `parent->left` existe, il devient `new->left`

### À droite :

```c
binary_tree_insert_right(parent, value);
```

---

## 🧹 Suppression d’un arbre

```c
void binary_tree_delete(binary_tree_t *tree)
```

- Parcours récursif post-order pour libérer chaque nœud

---

## 📋 Fonctions courantes

| Fonction                         | Utilité                                      |
|----------------------------------|----------------------------------------------|
| `binary_tree_is_leaf`            | Vérifie si un nœud est une feuille           |
| `binary_tree_is_root`            | Vérifie si un nœud est la racine             |
| `binary_tree_height`             | Calcule la hauteur                           |
| `binary_tree_depth`              | Calcule la profondeur                        |
| `binary_tree_size`               | Compte les nœuds                             |
| `binary_tree_leaves`             | Compte les feuilles                          |
| `binary_tree_nodes`              | Compte les nœuds avec au moins 1 enfant      |
| `binary_tree_balance`            | Calcule le facteur d’équilibre               |
| `binary_tree_is_full`            | Vérifie si tous les nœuds ont 0 ou 2 enfants |
| `binary_tree_is_perfect`         | Vérifie si l’arbre est parfait               |
| `binary_tree_sibling`            | Renvoie le frère du nœud                     |
| `binary_tree_uncle`              | Renvoie l’oncle d’un nœud                    |

---

## 📐 Exemples visuels (simplifiés)

```
       10
      /  \
     5    20
    / \   /
   3   6 15
```

- **Hauteur de 10** : 2
- **Profondeur de 6** : 2
- **Taille** : 6 nœuds
- **Feuilles** : 3, 6, 15

---

## 🔒 Contraintes du projet

- Compilation :  
```sh
gcc -Wall -Werror -Wextra -pedantic -std=gnu89
```

- Aucune variable globale
- Pas plus de 5 fonctions par fichier
- Style **Betty** obligatoire
- Tous les `.h` doivent être protégés (`#ifndef` / `#define` / `#endif`)

---

## 🧠 Tips pour réussir

- **Bien comprendre les appels récursifs**
- Visualiser les arbres avec des dessins
- Valider avec `valgrind` pour éviter les fuites mémoire
- Utiliser la fonction `binary_tree_print` (non obligatoire dans le repo)

---

## 📁 Exemple de `binary_trees.h`

```c
#ifndef BINARY_TREES_H
#define BINARY_TREES_H

#include <stdlib.h>
#include <stdio.h>

struct binary_tree_s
{
    int n;
    struct binary_tree_s *parent;
    struct binary_tree_s *left;
    struct binary_tree_s *right;
};

typedef struct binary_tree_s binary_tree_t;
typedef struct binary_tree_s bst_t;
typedef struct binary_tree_s avl_t;
typedef struct binary_tree_s heap_t;

/* Prototypes */
binary_tree_t *binary_tree_node(binary_tree_t *parent, int value);
binary_tree_t *binary_tree_insert_left(binary_tree_t *parent, int value);
binary_tree_t *binary_tree_insert_right(binary_tree_t *parent, int value);
void binary_tree_delete(binary_tree_t *tree);
int binary_tree_is_leaf(const binary_tree_t *node);
int binary_tree_is_root(const binary_tree_t *node);
/* etc... */

#endif /* BINARY_TREES_H */
```

---

## 📌 Résumé

- Les **arbres binaires** sont une structure centrale en algorithmie.
- Ils permettent une organisation hiérarchique efficace des données.
- Leurs **parcours** et leurs propriétés (**hauteur**, **taille**, etc.) sont fondamentaux.
- La maîtrise de la **récursivité** est indispensable pour les manipuler.

---

🚀 **Bon courage pour ton projet !**
