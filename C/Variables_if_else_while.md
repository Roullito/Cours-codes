# C - Variables, if, else, while

## 📌 Introduction
Ce document répertorie toutes les commandes possibles en C pour les variables, les structures conditionnelles (`if`, `else`) et les boucles (`while`). Un tableau récapitulatif des types de données est également inclus.

---

## 🔹 1. Types de Données en C
| Type | Description | Taille (octets) | Valeurs possibles |
|------|------------|-----------------|-------------------|
| `char` | Caractère | 1 | -128 à 127 (signé) ou 0 à 255 (non signé) |
| `int` | Entier | 4 | -2,147,483,648 à 2,147,483,647 |
| `short` | Entier court | 2 | -32,768 à 32,767 |
| `long` | Entier long | 4 ou 8 | Dépend du système |
| `float` | Flottant simple précision | 4 | 1.2E-38 à 3.4E+38 |
| `double` | Flottant double précision | 8 | 2.3E-308 à 1.7E+308 |
| `long double` | Flottant haute précision | 16 | Dépend du système |
| `_Bool` | Booléen (0 ou 1) | 1 | 0 (faux), 1 (vrai) |

---

## 🔹 2. Déclaration et Initialisation des Variables
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `int x;` | Déclare une variable entière | Aucune | `int age;` |
| `int x = 10;` | Déclare et initialise une variable | Aucune | `int score = 100;` |
| `const int x = 5;` | Déclare une constante | Impossible de modifier `x` | `const float pi = 3.14;` |
| `volatile int x;` | Empêche l'optimisation de la variable | Aucune | `volatile int compteur;` |
| `extern int x;` | Déclare une variable globale | Définition ailleurs | `extern int globalVar;` |

---

## 🔹 3. Structures Conditionnelles
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `if (condition)` | Exécute un bloc si la condition est vraie | `==, !=, >, <, >=, <=` | `if (x > 10) {}` |
| `else` | Exécute un bloc si `if` est faux | Aucune | `else {}` |
| `else if (condition)` | Ajoute une condition intermédiaire | Aucune | `else if (x == 5) {}` |
| `switch (variable)` | Teste plusieurs valeurs possibles | `case, break, default` | `switch (choix) { case 1: ... }` |
| `case` | Spécifie une valeur dans `switch` | Nécessite `break` pour éviter l'exécution en cascade | `case 1: printf("Un"); break;` |
| `default` | Cas par défaut dans un `switch` | Optionnel | `default: printf("Autre"); break;` |

---

## 🔹 4. Boucles
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `while (condition)` | Répète tant que la condition est vraie | Aucune | `while (x < 10) { x++; }` |
| `do { } while (condition);` | Exécute au moins une fois avant de tester la condition | Aucune | `do { x++; } while (x < 10);` |
| `for (init; cond; incr)` | Boucle avec compteur | `break, continue` | `for (int i = 0; i < 10; i++) {}` |
| `break;` | Sort immédiatement d'une boucle | `if (x == 5) break;` | `while (1) { if (x == 5) break; }` |
| `continue;` | Saute l'itération en cours et passe à la suivante | `if (x == 5) continue;` | `for (int i = 0; i < 10; i++) { if (i % 2 == 0) continue; }` |

---

## 📌 Conclusion
Ce document regroupe toutes les commandes possibles pour les variables, structures conditionnelles et boucles en C. Pour plus de détails, utilisez `man <commande>` dans un terminal Linux/macOS.

