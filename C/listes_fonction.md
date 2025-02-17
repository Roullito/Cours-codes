# Liste des Fonctions en C

## 📌 Introduction
Ce document répertorie les principales fonctions en langage C, organisées par catégories.

---

## 🔹 1. Fonctions d'Entrée/Sortie (I/O)
| Fonction | Description |
|----------|------------|
| `printf` | Affiche du texte formaté sur la sortie standard. |
| `scanf` | Lit des entrées depuis la console avec un format spécifique. |
| `puts` | Affiche une chaîne de caractères suivie d'un retour à la ligne. |
| `gets` | Lit une ligne de texte (⚠️ dangereuse, ne pas utiliser). |
| `fgets` | Lit une ligne de texte en spécifiant la taille maximale. |
| `putchar` | Affiche un caractère unique sur la sortie standard. |
| `getchar` | Lit un caractère depuis l'entrée standard. |
| `fprintf` | Écrit du texte formaté dans un fichier. |
| `fscanf` | Lit des données depuis un fichier avec un format spécifique. |
| `fopen` | Ouvre un fichier. |
| `fclose` | Ferme un fichier ouvert. |
| `fwrite` | Écrit des données binaires dans un fichier. |
| `fread` | Lit des données binaires depuis un fichier. |

---

## 🔹 2. Fonctions de Manipulation de Chaînes
| Fonction | Description |
|----------|------------|
| `strlen` | Retourne la longueur d'une chaîne de caractères. |
| `strcpy` | Copie une chaîne dans une autre (⚠️ peut causer des débordements). |
| `strncpy` | Copie une chaîne avec une limite de taille. |
| `strcat` | Concatène deux chaînes de caractères. |
| `strncat` | Concatène deux chaînes avec une limite de taille. |
| `strcmp` | Compare deux chaînes de caractères. |
| `strncmp` | Compare deux chaînes jusqu'à un certain nombre de caractères. |
| `strchr` | Cherche un caractère dans une chaîne. |
| `strrchr` | Cherche un caractère depuis la fin de la chaîne. |
| `strstr` | Cherche une sous-chaîne dans une chaîne. |
| `strtok` | Découpe une chaîne en tokens (⚠️ modifie la chaîne originale). |

---

## 🔹 3. Fonctions Mathématiques (math.h)
| Fonction | Description |
|----------|------------|
| `sqrt` | Calcule la racine carrée. |
| `pow` | Calcule la puissance. |
| `abs` | Retourne la valeur absolue d'un entier. |
| `fabs` | Retourne la valeur absolue d'un flottant. |
| `ceil` | Arrondit un nombre vers le haut. |
| `floor` | Arrondit un nombre vers le bas. |
| `round` | Arrondit un nombre au plus proche. |
| `sin` | Calcule le sinus d'un angle. |
| `cos` | Calcule le cosinus d'un angle. |
| `tan` | Calcule la tangente d'un angle. |

---

## 🔹 4. Fonctions de Gestion de Mémoire
| Fonction | Description |
|----------|------------|
| `malloc` | Alloue dynamiquement de la mémoire. |
| `calloc` | Alloue dynamiquement de la mémoire et l'initialise à zéro. |
| `realloc` | Redimensionne un bloc de mémoire alloué. |
| `free` | Libère de la mémoire allouée dynamiquement. |

---

## 🔹 5. Fonctions de Gestion de Processus
| Fonction | Description |
|----------|------------|
| `exit` | Termine le programme immédiatement. |
| `abort` | Termine le programme de manière anormale. |
| `system` | Exécute une commande système. |
| `getenv` | Récupère la valeur d'une variable d'environnement. |

---

## 🔹 6. Fonctions de Gestion du Temps
| Fonction | Description |
|----------|------------|
| `time` | Retourne l'heure actuelle en secondes depuis l'époque. |
| `clock` | Retourne le temps CPU écoulé. |
| `difftime` | Calcule la différence entre deux temps. |
| `strftime` | Formate une date en une chaîne de caractères. |

---

## 📌 Conclusion
Ce document regroupe les fonctions C les plus utilisées. Pour plus d’informations, consultez la documentation officielle ou utilisez `man <fonction>` sur un terminal Linux/macOS.

