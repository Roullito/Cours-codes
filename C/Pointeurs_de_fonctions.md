# 📘 C - Pointeurs de Fonctions

## 📚 Ressources
À lire ou regarder :
- [Pointeur de fonction en C](#)
- [Pointeurs sur fonctions](#)
- [Pointeurs de fonction en C / C++](#)
- [Pourquoi utiliser des pointeurs de fonction ?](#)
- [Tout ce que vous devez savoir sur les pointeurs en C](#)

---

## 🎯 Objectifs d'Apprentissage
À la fin de ce cours, vous devriez être capable d'expliquer :
- Ce que sont les pointeurs de fonction et comment les utiliser.
- Ce que contient exactement un pointeur de fonction.
- Où un pointeur de fonction pointe dans la mémoire virtuelle.

---

## 🔹 Qu'est-ce qu'un Pointeur de Fonction ?
Un **pointeur de fonction** est un pointeur qui stocke l'adresse d'une fonction au lieu d'une variable.

💡 **Pourquoi utiliser les pointeurs de fonction ?**
- Ils permettent **d'appeler dynamiquement une fonction** à l'exécution.
- Ils sont utiles pour les **fonctions de rappel** (callbacks), comme dans `qsort`.
- Ils facilitent la création de **structures de programme flexibles**, comme les machines à états.

---

## 🏗 Déclaration des Pointeurs de Fonction
### **Syntaxe de Base**
```c
return_type (*nom_du_pointeur)(types_des_paramètres);
```
Par exemple :
```c
int (*func_ptr)(int, int);
```
Cela signifie que `func_ptr` est un pointeur vers une fonction qui :
- Prend **deux paramètres de type `int`**
- Retourne un **`int`**

---

## 🔹 Assigner une Fonction à un Pointeur
### **Exemple : Utilisation d'un Pointeur de Fonction**
```c
#include <stdio.h>

// Fonction d'addition
int add(int a, int b) {
    return a + b;
}

int main() {
    int (*func_ptr)(int, int); // Déclaration du pointeur de fonction
    func_ptr = add; // Assignation de l'adresse de la fonction

    int resultat = func_ptr(10, 20); // Appel de la fonction via le pointeur
    printf("Résultat : %d\n", resultat); // Sortie : 30
    return 0;
}
```
✅ **Points clés :**
- On assigne le nom de la fonction (`add`) **sans parenthèses**.
- On appelle la fonction en utilisant `func_ptr(arguments)`.

---

## 🔹 Utilisation des Pointeurs de Fonction dans des Tableaux
Nous pouvons stocker plusieurs pointeurs de fonction dans un **tableau**.

### **Exemple : Tableau de Pointeurs de Fonction**
```c
#include <stdio.h>

int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }
int multiply(int a, int b) { return a * b; }

int main() {
    int (*operations[3])(int, int) = {add, subtract, multiply};
    
    int choix = 2; // Sélectionne la multiplication
    int resultat = operations[choix](10, 5);
    printf("Résultat : %d\n", resultat); // Sortie : 50
    return 0;
}
```
✅ **Points clés :**
- Stocke plusieurs pointeurs de fonction dans un tableau.
- Utilise un index (`operations[choix]`) pour appeler dynamiquement une fonction.

---

## 🔹 Pointeurs de Fonction comme Arguments (Callbacks)
Une **fonction de rappel** (callback) est une fonction passée en argument à une autre fonction.

### **Exemple : Utilisation de Pointeurs de Fonction pour un Tri**
```c
#include <stdio.h>

void execute(int (*operation)(int, int), int x, int y) {
    int resultat = operation(x, y);
    printf("Résultat : %d\n", resultat);
}

int add(int a, int b) { return a + b; }

int main() {
    execute(add, 10, 20); // Sortie : 30
    return 0;
}
```
✅ **Points clés :**
- `execute` prend un pointeur de fonction en paramètre.
- On passe `add` à `execute`, permettant une exécution flexible des fonctions.

---

## 🔹 Où les Pointeurs de Fonction sont Stockés en Mémoire
En C, **les fonctions sont stockées dans le segment de texte** de la mémoire, et les pointeurs de fonction contiennent **l'adresse de ces fonctions**.

💡 **Disposition de la Mémoire :**
- **Segment Code (Texte)** → Stocke le code compilé des fonctions.
- **Segment Données** → Stocke les variables globales/statique.
- **Tas (Heap)** → Stocke la mémoire allouée dynamiquement (`malloc`).
- **Pile (Stack)** → Stocke les variables locales et les informations d'appel de fonction.

Quand vous déclarez un pointeur de fonction :
```c
int (*func_ptr)(int, int) = add;
```
`func_ptr` contient **l'adresse mémoire** de la fonction `add` dans le **segment de code**.

---

## 🚀 Utilisation Avancée : Sélection Dynamique de Fonction
Les pointeurs de fonction permettent un **code flexible et modulaire**.

### **Exemple : Menu Dynamique**
```c
#include <stdio.h>

void option1() { printf("Vous avez choisi l'option 1 !\n"); }
void option2() { printf("Vous avez choisi l'option 2 !\n"); }
void option3() { printf("Vous avez choisi l'option 3 !\n"); }

int main() {
    void (*menu[3])() = {option1, option2, option3};
    
    int choix;
    printf("Entrez votre choix (1-3) : ");
    scanf("%d", &choix);
    
    if (choix >= 1 && choix <= 3)
        menu[choix - 1]();
    else
        printf("Choix invalide !\n");
    
    return 0;
}
```
✅ **Points clés :**
- Utilise **un tableau de pointeurs de fonction** pour exécuter dynamiquement des options.
- Réduit le besoin d'**instructions `if-else` ou `switch` longues**.

---

## 📜 Exigences du Projet
- **Éditeurs autorisés** : `vi`, `vim`, `emacs`
- **Compilation** : Ubuntu 20.04 LTS avec :
  ```sh
  gcc -Wall -Werror -Wextra -pedantic -std=gnu89 fichier.c -o fichier
  ```
- **Chaque fichier doit se terminer par une nouvelle ligne.**
- **Respecter le style de codage Betty (`betty-style.pl`, `betty-doc.pl`).**
- **Aucune variable globale autorisée.**
- **Pas plus de 5 fonctions par fichier.**
- **Fonctions autorisées** : `malloc`, `free`, `exit`.
- **Tous les fichiers d'en-tête doivent être protégés par `#ifndef #define #endif`.**

---

## 🎯 Résumé
✅ **Les Pointeurs de Fonction permettent :**
- L'exécution dynamique des fonctions.
- L'implémentation de **fonctions de rappel (callbacks)**.
- La création de **code modulaire et flexible**.
- La gestion **d'une sélection dynamique de fonctions**.

🚀 **Bon codage !**

