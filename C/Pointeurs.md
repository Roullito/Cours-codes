# Les Pointeurs en C : Guide Complet

## Table des matières
1. [Introduction](#introduction)
2. [Qu'est-ce qu'un pointeur ?](#quest-ce-quun-pointeur)
3. [Déclaration et initialisation](#déclaration-et-initialisation)
4. [Opérations sur les pointeurs](#opérations-sur-les-pointeurs)
5. [Pointeurs et tableaux](#pointeurs-et-tableaux)
6. [Pointeurs et chaînes de caractères](#pointeurs-et-chaînes-de-caractères)
7. [Pointeurs de fonctions](#pointeurs-de-fonctions)
8. [Pointeurs et structures](#pointeurs-et-structures)
9. [Allocation dynamique de mémoire](#allocation-dynamique-de-mémoire)
10. [Bonnes pratiques et erreurs courantes](#bonnes-pratiques-et-erreurs-courantes)
11. [Concepts Clés](#concepts-clés)
12. [Exercices pratiques](#exercices-pratiques)
13. [Conclusion](#conclusion)
14. [Notes perso](#notes-perso)

---

## Introduction

Les pointeurs sont des variables contenant des adresses mémoire. Ils permettent de manipuler directement les données stockées en mémoire. Comprendre les pointeurs est essentiel pour maîtriser la programmation en C, car ils sont souvent utilisés pour les tableaux, les chaînes de caractères, la gestion dynamique de mémoire et les structures de données avancées.

Ce guide vous expliquera en détail comment déclarer, initialiser et utiliser les pointeurs en C, avec des exemples clairs et des explications approfondies.

---

## Qu'est-ce qu'un pointeur ?

Un pointeur est une variable qui stocke l'adresse mémoire d'une autre variable. Il permet d'accéder directement à la mémoire et de modifier les valeurs sans passer par leur nom de variable.

### Exemple simple
```c
#include <stdio.h>

int main() {
    int x = 10;  // Variable entière
    int *p = &x; // Pointeur vers x

    printf("Valeur de x : %d\n", x);      // Affiche la valeur de x
    printf("Adresse de x : %p\n", &x);    // Affiche l'adresse de x
    printf("Valeur du pointeur p : %p\n", p);  // Adresse pointée par p
    printf("Valeur pointée par p : %d\n", *p); // Valeur à cette adresse

    return 0;
}
```

**Sortie :**
```
Valeur de x : 10
Adresse de x : 0x7ffee1c1a6ac
Valeur du pointeur p : 0x7ffee1c1a6ac
Valeur pointée par p : 10
```

Explication :
1. `int *p = &x;` crée un pointeur `p` pointant vers `x`.
2. `&x` donne l'adresse de la variable `x`.
3. `*p` déréférence le pointeur pour obtenir la valeur à l'adresse pointée.

---

## Déclaration et initialisation

### 1. Déclaration
La déclaration d'un pointeur utilise l'opérateur `*` :
```c
int *p;  // Pointeur vers un int
```
Cela signifie que `p` stockera l'adresse d'une variable entière.

### 2. Initialisation
Un pointeur doit être initialisé avec l'adresse d'une variable existante :
```c
int x = 5;
int *p = &x;  // p pointe vers x
```

### 3. Pointeur nul
Un pointeur peut être initialisé à `NULL` pour indiquer qu'il ne pointe vers rien :
```c
int *p = NULL;
```
Cela évite des comportements imprévisibles lors du déréférencement.

---

## Opérations sur les pointeurs

1. **Déréférencement (`*`)** : Permet d'accéder à la valeur pointée par un pointeur :
```c
int x = 10;
int *p = &x;
printf("Valeur pointée par p : %d\n", *p);  // Affiche 10
```

2. **Adresse (`&`)** : Renvoie l'adresse d'une variable :
```c
int y = 20;
printf("Adresse de y : %p\n", &y);
```

3. **Arithmétique des pointeurs :**
Les pointeurs peuvent être incrémentés et décrémentés :
```c
int arr[] = {10, 20, 30};
int *p = arr;
p++;  // Avance de la taille d'un int (4 octets)
printf("Deuxième élément : %d\n", *p);  // Affiche 20
```

---

## Les Tableaux et les Pointeurs en C

## Introduction
En langage C, les **tableaux** et les **pointeurs** sont étroitement liés. Un tableau est une structure qui stocke des éléments de même type en mémoire de façon contiguë. Un pointeur, quant à lui, est une variable qui stocke l'adresse mémoire d'une autre variable.

Lorsque l'on travaille avec des tableaux et des pointeurs, il est crucial de comprendre leur relation afin d'optimiser l'utilisation de la mémoire et d'écrire un code efficace.

---

## 1. Déclaration et Accès aux Tableaux

Un tableau est déclaré comme suit :
```c
int tab[5] = {10, 20, 30, 40, 50};
```
### Accès aux éléments
Chaque élément du tableau est accessible via son **indice** :
```c
printf("%d\n", tab[0]); // Affiche 10
printf("%d\n", tab[2]); // Affiche 30
```

### Relation entre Tableaux et Pointeurs
Le nom du tableau **tab** représente l'adresse du premier élément du tableau.
```c
printf("%p\n", tab); // Affiche l'adresse du premier élément
printf("%p\n", &tab[0]); // Même adresse
```
En réalité, `tab` est un **pointeur constant** sur le premier élément du tableau.

---

## 2. Utilisation des Pointeurs pour Accéder aux Éléments

Un tableau peut être parcouru à l'aide d'un pointeur :
```c
int *p = tab; // p pointe vers le premier élément du tableau
printf("%d\n", *p);   // Affiche 10
p++;  // Avance d'une case
printf("%d\n", *p);   // Affiche 20
```

On peut aussi accéder aux éléments d'un tableau en utilisant l'arithmétique des pointeurs :
```c
printf("%d\n", *(tab + 1)); // Équivaut à tab[1]
```

---

## 3. Modification des Valeurs via un Pointeur

Un pointeur permet également de modifier les valeurs d'un tableau :
```c
int tab[3] = {5, 10, 15};
int *p = tab; // p pointe sur tab[0]

*p = 20;  // Modifie tab[0]
p++;      // Déplace le pointeur sur tab[1]
*p = 25;  // Modifie tab[1]

// Résultat : tab = {20, 25, 15}
```

---

## 4. Passer un Tableau à une Fonction

Lorsqu'on passe un tableau à une fonction, on passe **l'adresse du premier élément**, donc un pointeur :
```c
void afficher_tableau(int *t, int taille) {
    for (int i = 0; i < taille; i++) {
        printf("%d ", *(t + i));
    }
    printf("\n");
}

int main() {
    int tab[5] = {1, 2, 3, 4, 5};
    afficher_tableau(tab, 5);
    return 0;
}
```

---

## 5. Différence entre `tab[]` et `*tab`
Dans une fonction, les deux écritures suivantes sont équivalentes :
```c
void fonction1(int tab[]);
void fonction2(int *tab);
```
Dans les deux cas, `tab` est en réalité un **pointeur sur le premier élément du tableau**, et non une copie complète du tableau.

---

## 6. Allocation Dynamique de Tableaux
Avec `malloc`, on peut allouer un tableau dynamiquement :
```c
int *tab = malloc(5 * sizeof(int));
if (tab == NULL) {
    printf("Allocation échouée\n");
    return 1;
}

for (int i = 0; i < 5; i++) {
    tab[i] = i * 10;
}

free(tab); // Libération de la mémoire
```

---

## 7. Tableau de Pointeurs
On peut aussi créer un tableau qui stocke des **pointeurs** :
```c
int a = 10, b = 20, c = 30;
int *tab[3] = {&a, &b, &c};

printf("%d\n", *tab[0]); // Affiche 10
printf("%d\n", *tab[1]); // Affiche 20
```

---

## Conclusion
- Un **tableau** est une suite de données stockées de manière contiguë en mémoire.
- Un **pointeur** permet d'accéder aux éléments d'un tableau via des adresses mémoire.
- L'arithmétique des pointeurs permet de naviguer efficacement dans un tableau.
- Lorsqu'un tableau est passé à une fonction, seul l'adresse du premier élément est transmise.
- L'utilisation de l'allocation dynamique (`malloc`) permet de créer des tableaux de taille variable.

Ces notions sont essentielles pour optimiser la gestion de la mémoire en C. 🚀


## Pointeurs et chaînes de caractères

Les chaînes de caractères sont des tableaux de `char` terminés par un caractère nul (`\0`).

```c
char str[] = "Bonjour";
char *p = str;

while (*p != '\0') {
    printf("%c", *p);  // Affiche chaque caractère
    p++;               // Avance vers le caractère suivant
}
```

---

## Pointeurs de fonctions

Un pointeur peut également pointer vers une fonction, ce qui permet de passer des fonctions comme arguments.

### Exemple :
```c
#include <stdio.h>

void afficherMessage() {
    printf("Bonjour !\n");
}

int main() {
    void (*fptr)() = afficherMessage;  // Pointeur vers la fonction
    fptr();  // Appelle la fonction

    return 0;
}
```

---

## Pointeurs et structures

Les pointeurs peuvent être utilisés pour manipuler des structures.

```c
#include <stdio.h>

struct Point {
    int x;
    int y;
};

int main() {
    struct Point p1 = {10, 20};
    struct Point *ptr = &p1;

    printf("x = %d, y = %d\n", ptr->x, ptr->y);
    return 0;
}
```

Explication : `ptr->x` est équivalent à `(*ptr).x`.

---

## Allocation dynamique de mémoire

L'allocation dynamique utilise `malloc` et `free` pour gérer la mémoire manuellement.

### Exemple :
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int *)malloc(sizeof(int));
    if (p == NULL) {
        printf("Allocation échouée\n");
        return 1;
    }

    *p = 42;
    printf("Valeur : %d\n", *p);

    free(p);  // Libération de la mémoire
    return 0;
}
```

---

## Bonnes pratiques et erreurs courantes

1. **Initialisation :** Toujours initialiser les pointeurs à `NULL` :
```c
int *p = NULL;
```

2. **Libération :** Libérer la mémoire allouée avec `free` :
```c
free(p);
p = NULL;
```

3. **Vérification :** Toujours vérifier si l'allocation a réussi :
```c
if (p == NULL) {
    printf("Allocation échouée\n");
}
```

4. **Éviter les dépassements de tableau :**
```c
int arr[5];
for (int i = 0; i < 5; i++) {
    arr[i] = i;
}
```

---

## Concepts Clés

Voici les concepts essentiels pour bien comprendre les exercices :

1. **Différences entre pointeurs et tableaux :**
   - Un tableau est une zone de mémoire contiguë.
   - Un pointeur est une variable contenant une adresse mémoire.

2. **Passage de paramètres par adresse :** Permet de modifier une variable depuis une fonction.
```c
void reset_to_98(int *n) {
    *n = 98;
}
```

3. **Chaînes de caractères :** Tableau de `char` terminé par `\0`.

4. **Portée des variables :** Locale (fonction), globale (programme entier), statique (persiste après la fonction).

5. **Arithmétique des pointeurs :** Incrémentation et décrémentation pour naviguer dans les tableaux.

---

## Exercices pratiques

Voici les exercices recommandés :
1. **reset_to_98 :** Modifier un entier via un pointeur.
2. **swap_int :** Échanger deux entiers à l'aide de pointeurs.
3. **_strlen :** Calculer la longueur d'une chaîne.
4. **_puts :** Afficher une chaîne avec un saut de ligne.
5. **print_rev :** Afficher une chaîne en sens inverse.
6. **rev_string :** Inverser une chaîne en place.
7. **puts2 :** Afficher un caractère sur deux d'une chaîne.
8. **puts_half :** Afficher la deuxième moitié d'une chaîne.
9. **print_array :** Afficher les n premiers éléments d'un tableau.
10. **_strcpy :** Copier une chaîne vers un autre emplacement mémoire.
11. **_atoi :** Convertir une chaîne en entier.

---

## Conclusion

Les pointeurs sont essentiels pour gérer la mémoire et manipuler efficacement les données en C. En pratiquant, vous développerez une compréhension plus profonde des structures et algorithmes. N'hésitez pas à expérimenter avec les exemples et à approfondir les concepts abordés ici.

## Notes Perso

### **Exemple :**
```c
char *str = "Hello";
```

| Terme   | Signification                                    | Valeur initiale     |
|--------|--------------------------------------------------|---------------------|
| `str`  | Adresse du premier caractère de la chaîne         | `0x1000` (adresse)  |
| `*str` | Valeur à l'adresse pointée par `str` (déréférencement) | `'H'`               |

---

### **Appel de la fonction `_strlen` :**
Dans le `main` :
```c
len = _strlen(str);
```

Dans `_strlen` :
```c
int _strlen(char *s)
```

Explication étape par étape :
1. **`s` reçoit une copie de l'adresse contenue dans `str` :**
   ```c
   s = str;
   ```
   - Ici, `s = 0x1000` (même adresse que `str`).

2. **Valeurs au début :**
   | Terme   | Adresse pointée | Valeur pointée |
   |--------|------------------|----------------|
   | `str`  | `0x1000`         | `'H'`          |
   | `s`    | `0x1000`         | `'H'`          |

3. **Après un `s++` :**
   - `s` avance à l'adresse suivante : `0x1001`
   - `*s` devient `'e'`.

4. **Évolution pendant la boucle :**
   | Étape | Adresse de `s` | `*s` (Valeur) |
   |-------|----------------|---------------|
   | 1     | `0x1000`       | `'H'`         |
   | 2     | `0x1001`       | `'e'`         |
   | 3     | `0x1002`       | `'l'`         |
   | 4     | `0x1003`       | `'l'`         |
   | 5     | `0x1004`       | `'o'`         |
   | 6     | `0x1005`       | `'
