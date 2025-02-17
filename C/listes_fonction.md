# Liste des Fonctions en C

## 📌 Introduction
Ce document répertorie les principales fonctions en langage C, organisées par catégories. Chaque fonction est un lien vers sa documentation détaillée.

---

## 🔹 1. Fonctions d'Entrée/Sortie (I/O)
| Fonction | Description |
|----------|------------|
| [`printf`](https://man7.org/linux/man-pages/man3/printf.3.html) | Affiche du texte formaté sur la sortie standard. Exemples de format : `%d` (entier), `%f` (flottant), `%s` (chaîne). |
| [`scanf`](https://man7.org/linux/man-pages/man3/scanf.3.html) | Lit des entrées avec un format spécifique. Exemples : `%d` (entier), `%f` (flottant), `%s` (chaîne). |
| [`puts`](https://man7.org/linux/man-pages/man3/puts.3.html) | Affiche une chaîne de caractères suivie d'un retour à la ligne. |
| [`gets`](https://man7.org/linux/man-pages/man3/gets.3.html) | Lit une ligne de texte (⚠️ dangereuse, ne pas utiliser). |
| [`fgets`](https://man7.org/linux/man-pages/man3/fgets.3.html) | Lit une ligne de texte en spécifiant la taille maximale. |
| [`putchar`](https://man7.org/linux/man-pages/man3/putchar.3.html) | Affiche un caractère unique sur la sortie standard. |
| [`getchar`](https://man7.org/linux/man-pages/man3/getchar.3.html) | Lit un caractère depuis l'entrée standard. |
| [`fprintf`](https://man7.org/linux/man-pages/man3/fprintf.3.html) | Écrit du texte formaté dans un fichier. |
| [`fscanf`](https://man7.org/linux/man-pages/man3/fscanf.3.html) | Lit des données depuis un fichier avec un format spécifique. |
| [`fopen`](https://man7.org/linux/man-pages/man3/fopen.3.html) | Ouvre un fichier. |
| [`fclose`](https://man7.org/linux/man-pages/man3/fclose.3.html) | Ferme un fichier ouvert. |
| [`fwrite`](https://man7.org/linux/man-pages/man3/fwrite.3.html) | Écrit des données binaires dans un fichier. |
| [`fread`](https://man7.org/linux/man-pages/man3/fread.3.html) | Lit des données binaires depuis un fichier. |

---

## 🔹 2. Fonctions de Manipulation de Chaînes
| Fonction | Description |
|----------|------------|
| [`strlen`](https://man7.org/linux/man-pages/man3/strlen.3.html) | Retourne la longueur d'une chaîne de caractères. |
| [`strcpy`](https://man7.org/linux/man-pages/man3/strcpy.3.html) | Copie une chaîne dans une autre. |
| [`strncpy`](https://man7.org/linux/man-pages/man3/strncpy.3.html) | Copie une chaîne avec une limite de taille. |
| [`strcat`](https://man7.org/linux/man-pages/man3/strcat.3.html) | Concatène deux chaînes de caractères. |
| [`strcmp`](https://man7.org/linux/man-pages/man3/strcmp.3.html) | Compare deux chaînes de caractères. |
| [`strchr`](https://man7.org/linux/man-pages/man3/strchr.3.html) | Cherche un caractère dans une chaîne. |
| [`strstr`](https://man7.org/linux/man-pages/man3/strstr.3.html) | Cherche une sous-chaîne dans une chaîne. |

---

## 🔹 3. Fonctions Mathématiques
| Fonction | Description |
|----------|------------|
| [`sizeof`](https://en.cppreference.com/w/c/language/sizeof) | Retourne la taille en octets d'un type ou d'une variable. |
| [`sqrt`](https://man7.org/linux/man-pages/man3/sqrt.3.html) | Calcule la racine carrée. |
| [`pow`](https://man7.org/linux/man-pages/man3/pow.3.html) | Calcule la puissance. |
| [`abs`](https://man7.org/linux/man-pages/man3/abs.3.html) | Retourne la valeur absolue d'un entier. |
| [`ceil`](https://man7.org/linux/man-pages/man3/ceil.3.html) | Arrondit un nombre vers le haut. |
| [`floor`](https://man7.org/linux/man-pages/man3/floor.3.html) | Arrondit un nombre vers le bas. |

---

## 🔹 4. Fonctions de Gestion de Mémoire
| Fonction | Description |
|----------|------------|
| [`malloc`](https://man7.org/linux/man-pages/man3/malloc.3.html) | Alloue dynamiquement de la mémoire. |
| [`free`](https://man7.org/linux/man-pages/man3/free.3.html) | Libère de la mémoire allouée dynamiquement. |

---

## 🔹 5. Fonctions de Gestion de Processus
| Fonction | Description |
|----------|------------|
| [`exit`](https://man7.org/linux/man-pages/man3/exit.3.html) | Termine le programme immédiatement. |
| [`system`](https://man7.org/linux/man-pages/man3/system.3.html) | Exécute une commande système. |
| [`fork`](https://man7.org/linux/man-pages/man3/fork.3.html) | Crée un processus fils. |

---

## 🔹 6. Fonctions de Gestion du Temps
| Fonction | Description |
|----------|------------|
| [`time`](https://man7.org/linux/man-pages/man3/time.3.html) | Retourne l'heure actuelle en secondes depuis l'époque. |
| [`clock`](https://man7.org/linux/man-pages/man3/clock.3.html) | Retourne le temps CPU écoulé. |

---

## 📌 Conclusion
Ce document regroupe les fonctions C les plus utilisées. Pour plus d’informations, consultez la documentation officielle ou utilisez `man <fonction>` sur un terminal Linux/macOS.

