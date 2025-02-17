# Guide des Options de GCC

## Introduction
GCC (GNU Compiler Collection) est un compilateur puissant pour le langage C et d'autres langages. Ce document détaille les principales options utilisées avec `gcc`.

---

## 1. Options Générales
| Option | Description |
|--------|------------|
| `-o <fichier>` | Spécifie le nom du fichier de sortie (exécutable ou autre). |
| `-c` | Compile sans édition de liens (génère un fichier `.o`). |
| `-v` | Affiche les étapes détaillées de la compilation. |
| `--version` | Affiche la version de `gcc`. |

---

## 2. Étapes du Processus de Compilation
| Étape | Option | Description |
|-------|--------|------------|
| **Prétraitement** | `-E` | Effectue uniquement le prétraitement (`#include`, `#define`). |
| **Compilation** | `-S` | Arrête après la compilation (génère un fichier `.s`). |
| **Assemblage** | `-c` | Arrête après l’assemblage (génère un fichier `.o`). |
| **Édition de liens** | (par défaut) | Crée l’exécutable final. |

---

## 3. Options de Débogage
| Option | Description |
|--------|------------|
| `-g` | Génère des informations de débogage pour `gdb`. |
| `-Wall` | Active les avertissements courants. |
| `-Werror` | Traite les avertissements comme des erreurs. |
| `-Wextra` | Active des avertissements supplémentaires. |
| `-pedantic` | Applique strictement la norme C. |

---

## 4. Options d’Optimisation
| Option | Description |
|--------|------------|
| `-O0` | Pas d’optimisation (par défaut). |
| `-O1` | Optimisation légère. |
| `-O2` | Optimisation standard (recommandée). |
| `-O3` | Optimisation agressive (peut ralentir la compilation). |
| `-Ofast` | Optimisation maximale (non standard). |
| `-Os` | Optimisation pour réduire la taille du fichier binaire. |

---

## 5. Options de Gestion des Standards du Langage
| Option | Description |
|--------|------------|
| `-std=c89` | Utilise la norme C89. |
| `-std=c99` | Utilise la norme C99. |
| `-std=c11` | Utilise la norme C11. |
| `-std=gnu11` | Utilise C11 avec extensions GNU. |
| `-ansi` | Active strictement ANSI C (équivalent à `-std=c89`). |

---

## 6. Options de Liens et Bibliothèques
| Option | Description |
|--------|------------|
| `-lm` | Lie avec la bibliothèque mathématique (`libm`). |
| `-lpthread` | Active le support des threads POSIX. |
| `-static` | Génère un exécutable statique. |
| `-shared` | Génère une bibliothèque dynamique (`.so`). |
| `-L<path>` | Spécifie un répertoire où chercher les bibliothèques. |
| `-l<nom>` | Lie avec une bibliothèque (`-lm` → `libm.so`). |

---

## 7. Options de Compilation pour l’Architecture
| Option | Description |
|--------|------------|
| `-m32` | Compile pour une architecture 32 bits. |
| `-m64` | Compile pour une architecture 64 bits. |
| `-march=native` | Utilise les optimisations du processeur actuel. |
| `-mtune=native` | Optimise le code pour le CPU actuel. |

---

## 8. Options de Génération de Code
| Option | Description |
|--------|------------|
| `-fPIC` | Génère du code indépendant de la position (nécessaire pour `.so`). |
| `-fstack-protector` | Ajoute une protection contre les dépassements de pile. |
| `-fno-builtin` | Désactive les fonctions internes optimisées de GCC. |

---

## 9. Compilation pour C++
Pour compiler un fichier **C++**, utilisez `g++` au lieu de `gcc` :
```bash
 g++ -std=c++11 -Wall -Wextra -o prog prog.cpp
```

---

## 10. Commande pour Voir Toutes les Options GCC
Si vous voulez voir **toutes les options disponibles** sur votre système :
```bash
 gcc --help
```
ou
```bash
 man gcc
```

---

## Exemples de Compilation
### Compilation Simple
```bash
gcc main.c -o main
```

### Compilation avec Warnings et Débogage
```bash
gcc -Wall -Wextra -g main.c -o main
```

### Compilation avec Optimisation
```bash
gcc -O2 main.c -o main
```

### Générer une Bibliothèque Dynamique (`.so`)
```bash
gcc -shared -fPIC lib.c -o libmylib.so
```

### Compiler pour 32 bits sur une machine 64 bits
```bash
gcc -m32 main.c -o main32
```

---

Ce guide couvre les options **les plus courantes et utiles** pour compiler du C avec `gcc`. 🚀

