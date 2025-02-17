# Liste des Fonctions en C

## 📌 Introduction
Ce document répertorie les principales fonctions en langage C, organisées par catégories. Chaque fonction est accompagnée de sa description, de ses options et d'un exemple d'utilisation.

---

## 🔹 1. Fonctions d'Entrée/Sortie (I/O)
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `printf()` | Affiche du texte formaté sur la sortie standard | `%d` = entier, `%f` = flottant, `%s` = chaîne, `%c` = caractère | `printf("Valeur: %d", 10);` |
| `scanf()` | Lit des entrées formatées | `%d` = entier, `%f` = flottant, `%s` = chaîne | `scanf("%d", &valeur);` |
| `puts()` | Affiche une chaîne avec retour à la ligne | Aucune | `puts("Hello World");` |
| `fgets()` | Lit une ligne avec une taille maximale | `(char *str, int size, FILE *stream)` | `fgets(buffer, 50, stdin);` |
| `putchar()` | Affiche un caractère unique | `(char c)` | `putchar('A');` |
| `getchar()` | Lit un caractère depuis l'entrée standard | Aucun | `char c = getchar();` |

---

## 🔹 2. Fonctions de Manipulation de Chaînes
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `strlen()` | Retourne la longueur d'une chaîne | `(const char *str)` | `int len = strlen("Hello");` |
| `strcpy()` | Copie une chaîne dans une autre | `(char *dest, const char *src)` | `strcpy(dest, "Texte");` |
| `strcat()` | Concatène deux chaînes | `(char *dest, const char *src)` | `strcat(dest, " suite");` |

---

## 🔹 3. Fonctions Mathématiques
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `sizeof()` | Retourne la taille en octets d'un type ou d'une variable | `(type)` ou `(variable)` | `printf("%lu", sizeof(int));` |
| `sqrt()` | Calcule la racine carrée | `(double x)` | `double r = sqrt(25.0);` |
| `pow()` | Calcule la puissance | `(double base, double exp)` | `double p = pow(2, 3);` |

---

## 🔹 4. Fonctions de Gestion de Mémoire
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `malloc()` | Alloue dynamiquement de la mémoire | `(size_t size)` | `int *ptr = malloc(10 * sizeof(int));` |
| `free()` | Libère la mémoire allouée | `(void *ptr)` | `free(ptr);` |

---

## 🔹 5. Fonctions de Gestion de Processus
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `exit()` | Termine le programme immédiatement | `(int status)` | `exit(0);` |
| `system()` | Exécute une commande système | `(const char *command)` | `system("ls");` |

---

## 🔹 6. Fonctions de Gestion du Temps
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `time()` | Retourne l'heure actuelle en secondes depuis l'époque | `(time_t *tloc)` | `time_t now = time(NULL);` |
| `clock()` | Retourne le temps CPU écoulé | Aucun | `clock_t t = clock();` |

---

## 📌 Conclusion
Ce document regroupe les fonctions C les plus utilisées avec leur description, leurs options et des exemples concrets. Pour plus de détails, utilisez `man <fonction>` dans un terminal Linux/macOS.

