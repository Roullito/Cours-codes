# 📘 C - Fonctions Variadiques

## 📚 Ressources
À lire ou regarder :
- [stdarg.h](#)
- [Fonctions Variadiques](#)
- [Const Keyword](#)
- **man pages** : `man stdarg`

---

## 🎯 Objectifs d'Apprentissage
À la fin de ce cours, vous devriez être capable d'expliquer :
- Ce que sont les fonctions variadiques et comment elles fonctionnent.
- Comment utiliser les macros `va_start`, `va_arg` et `va_end`.
- Pourquoi et comment utiliser le qualificateur `const`.

---

## 🔹 Qu'est-ce qu'une Fonction Variadique ?
Une **fonction variadique** est une fonction qui peut accepter un nombre variable d'arguments. Contrairement aux fonctions classiques qui ont un nombre fixe de paramètres, une fonction variadique peut prendre un nombre illimité d’arguments après ses paramètres fixes.

💡 **Pourquoi utiliser des fonctions variadiques ?**
- Elles permettent de créer des fonctions **flexibles et génériques**.
- Elles sont utiles pour des **fonctions comme `printf`**.
- Elles permettent de traiter **un nombre inconnu de paramètres** à l'exécution.

---

## 🏗 Déclaration d'une Fonction Variadique
### **Syntaxe de Base**
```c
#include <stdarg.h>

return_type function_name(fixed_params, ...);
```
Le `...` indique que la fonction peut accepter un nombre variable d’arguments.

### **Exemple : Déclaration d'une Fonction Variadique**
```c
#include <stdarg.h>
#include <stdio.h>

// Fonction qui affiche les entiers passés en argument
void print_numbers(int n, ...);
```

---

## 🔹 Utilisation des Macros `stdarg.h`
C fournit des **macros spéciales** pour manipuler les arguments variables.

### **Les Macros Importantes**
| Macro | Description |
|----------------|--------------------------------|
| `va_list` | Type utilisé pour stocker la liste des arguments. |
| `va_start(ap, last_fixed_param)` | Initialise `ap` pour accéder aux arguments variables. |
| `va_arg(ap, type)` | Récupère l’argument suivant de type `type`. |
| `va_end(ap)` | Termine l’utilisation de `va_list`. |

### **Exemple d'Utilisation**
```c
#include <stdarg.h>
#include <stdio.h>

void print_numbers(int n, ...) {
    va_list args;
    va_start(args, n);
    
    for (int i = 0; i < n; i++) {
        int num = va_arg(args, int);
        printf("%d ", num);
    }
    
    va_end(args);
    printf("\n");
}

int main() {
    print_numbers(3, 10, 20, 30);
    return 0;
}
```
✅ **Points clés :**
- `va_list args;` → Initialise la liste des arguments.
- `va_start(args, n);` → Initialise `args` après `n`.
- `va_arg(args, int);` → Récupère l’argument suivant.
- `va_end(args);` → Termine la liste.

---

## 🔹 Pourquoi Utiliser `const` ?
Le mot-clé `const` empêche la modification des valeurs passées en paramètre.

### **Utilisation de `const` pour Sécuriser les Données**
```c
void display_message(const char *message) {
    printf("%s\n", message);
}
```
- Empêche la modification de `message` dans `display_message`.
- Utile pour **éviter les erreurs de programmation accidentelles**.

---

## 🚀 Utilisation Avancée des Fonctions Variadiques

### **Exemple : Fonction Générique de Somme**
```c
#include <stdarg.h>
#include <stdio.h>

int sum_them_all(int count, ...) {
    int sum = 0;
    va_list args;
    
    va_start(args, count);
    for (int i = 0; i < count; i++) {
        sum += va_arg(args, int);
    }
    va_end(args);
    
    return sum;
}

int main() {
    printf("Somme : %d\n", sum_them_all(3, 5, 10, 15)); // Sortie : 30
    return 0;
}
```
✅ **Points clés :**
- La fonction peut additionner **n'importe quel nombre d'entiers**.
- Utilise `va_arg` pour **récupérer chaque argument**.
- La liste est **fermée avec `va_end`** pour éviter des comportements indéfinis.

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
- **Macros autorisées** : `va_start`, `va_arg`, `va_end`.
- **Tous les fichiers d'en-tête doivent être protégés par `#ifndef #define #endif`.**

---

## 🎯 Résumé
✅ **Les Fonctions Variadiques permettent :**
- D’écrire des fonctions flexibles acceptant un nombre variable d’arguments.
- De simplifier des fonctions qui nécessitent plusieurs paramètres facultatifs.
- D’éviter d’écrire plusieurs versions d’une même fonction.

🎯 **Prochaines Étapes :**
- Expérimenter avec **d'autres types d'arguments variadiques** (flottants, chaînes de caractères).
- Comprendre comment fonctionne **`printf`** en interne.
- Utiliser des **tableaux et structures avec `va_list`**.

🚀 **Bon codage !**

