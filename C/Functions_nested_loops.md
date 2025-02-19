# 📘 C - Functions and Nested Loops

## 📖 Introduction
Ce cours détaillé couvre les concepts essentiels liés aux fonctions et aux boucles imbriquées en C. Vous apprendrez à définir, utiliser et gérer efficacement les fonctions tout en comprenant les implications des boucles imbriquées. Il inclut également des explications sur les drapeaux de compilation `gcc` et les fichiers d'en-tête.

---

## 🔍 **1. Fonctions en C**

### ➤ **1.1 Définition d'une Fonction**
Une fonction en C est un bloc de code réutilisable qui effectue une tâche spécifique. Elle évite la répétition du code et améliore la lisibilité.

**Syntaxe générale :**
```c
retour_type nom_fonction(paramètres) {
    // Corps de la fonction
    return valeur;
}
```

**Exemple :**
```c
#include "main.h"

int addition(int a, int b) {
    return a + b;
}
```

### ➤ **1.2 Appel d'une Fonction**
Pour exécuter une fonction, il suffit de l'appeler en passant les arguments nécessaires.

**Exemple :**
```c
#include "main.h"
#include <stdio.h>

int addition(int a, int b);

int main(void) {
    int resultat = addition(5, 3);
    _putchar('0' + resultat); // Affiche '8'
    return (0);
}
```

### ➤ **1.3 Déclaration vs Définition**
- **Déclaration :** Annonce la fonction au compilateur (prototype).
- **Définition :** Implémente le corps de la fonction.

**Exemple de déclaration :**
```c
int addition(int a, int b);
```

**Exemple de définition :**
```c
int addition(int a, int b) {
    return a + b;
}
```

### ➤ **1.4 Prototypes de Fonction**
Le prototype indique au compilateur le nom, le type de retour et les paramètres d'une fonction avant sa définition.

**Exemple :**
```c
int addition(int a, int b);
```
Cela évite les erreurs lors de la compilation si la fonction est définie après son utilisation.

### ➤ **1.5 Portée des Variables**
- **Globale :** Définie en dehors des fonctions, accessible partout.
- **Locale :** Définie à l'intérieur d'une fonction, accessible uniquement dans cette fonction.

**Exemple :**
```c
int global = 10;  // Variable globale

void afficher(void) {
    int local = 5;  // Variable locale
    _putchar('0' + local);
}
```

---

## 🔄 **2. Boucles Imbriquées (Nested Loops)**

### ➤ **2.1 Définition**
Les boucles imbriquées sont des boucles à l'intérieur d'autres boucles. Chaque itération de la boucle extérieure déclenche plusieurs itérations de la boucle intérieure.

**Exemple :**
```c
#include "main.h"

void table_multiplication(void) {
    int i, j;

    for (i = 1; i <= 3; i++) {
        for (j = 1; j <= 3; j++) {
            _putchar('0' + (i * j));
            _putchar(' ');
        }
        _putchar('\n');
    }
}
```
**Sortie :**
```
1 2 3
2 4 6
3 6 9
```

### ➤ **2.2 Boucles While Imbriquées**

**Exemple :**
```c
#include "main.h"

void compteur(void) {
    int i = 0;
    while (i < 3) {
        int j = 0;
        while (j < 3) {
            _putchar('0' + j);
            j++;
        }
        _putchar('\n');
        i++;
    }
}
```

---

## ⚙️ **3. Compilation avec `gcc`**
Pour compiler un programme C, utilisez les options suivantes :

| Drapeau               | Description |
|-----------------------|-------------|
| `-Wall`               | Affiche tous les avertissements. |
| `-Werror`             | Traite les avertissements comme des erreurs. |
| `-pedantic`           | Enforce strict C standards. |
| `-Wextra`             | Affiche des avertissements supplémentaires. |
| `-std=gnu89`          | Utilise la norme C89 avec les extensions GNU. |

**Exemple de compilation :**
```bash
gcc -Wall -Werror -Wextra -pedantic -std=gnu89 main.c fonctions.c -o programme
```

---

## 📄 **4. Fichiers d'En-tête (Header Files)**
Les fichiers d'en-tête (.h) contiennent les prototypes de fonctions, les macros et les définitions de types partagés entre plusieurs fichiers.

**Exemple : `main.h`**
```c
#ifndef MAIN_H
#define MAIN_H

int addition(int a, int b);
void table_multiplication(void);
int _putchar(char c);

#endif /* MAIN_H */
```

**Inclusion dans un fichier C :**
```c
#include "main.h"
```

---

## 🔑 **5. Bonnes Pratiques**
1. **Modularité :** Découpez le code en petites fonctions réutilisables.
2. **Nommez clairement :** Utilisez des noms explicites pour les fonctions et variables.
3. **Vérifiez les erreurs :** Testez les retours des fonctions.
4. **Respectez la norme Betty :** Utilisez `betty-style.pl` pour formater votre code.

---

## 📝 **6. Exemple Complet**
### **Fichier `main.c` :**
```c
#include "main.h"

int main(void) {
    int resultat = addition(10, 5);
    _putchar('0' + resultat);
    _putchar('\n');
    table_multiplication();
    return (0);
}
```

### **Fichier `fonctions.c` :**
```c
#include "main.h"

int addition(int a, int b) {
    return a + b;
}

void table_multiplication(void) {
    int i, j;
    for (i = 1; i <= 3; i++) {
        for (j = 1; j <= 3; j++) {
            _putchar('0' + (i * j));
            _putchar(' ');
        }
        _putchar('\n');
    }
}
```

### **Fichier `main.h` :**
```c
#ifndef MAIN_H
#define MAIN_H

int addition(int a, int b);
void table_multiplication(void);
int _putchar(char c);

#endif /* MAIN_H */
```

---

## 🎯 **7. Conclusion**
Vous maîtrisez maintenant les concepts de fonctions et de boucles imbriquées en C. En appliquant les bonnes pratiques, en utilisant les prototypes et en comprenant les drapeaux de compilation, vous pourrez écrire un code structuré, lisible et performant.

