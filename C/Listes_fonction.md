# Liste des Fonctions en C

## 📌 Introduction
Ce document répertorie les principales fonctions en langage C, organisées par catégories. Chaque fonction est accompagnée de sa description, de ses options et d'un exemple d'utilisation.

---

## 🔹 1. Fonctions d'Entrée/Sortie (I/O)
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `printf()` | Affiche du texte formaté sur la sortie standard | `%d` = entier, `%f` = flottant, `%s` = chaîne, `%c` = caractère `%lu` = unsigned long, `%ld` = long, `%llu` = unsigned long long| `printf("Valeur: %d", 10);` |
| `scanf()` | Lit des entrées formatées | `%d` = entier, `%f` = flottant, `%s` = chaîne `%lu` = unsigned long, `%ld` = long, `%llu` = unsigned long long | `scanf("%d", &valeur);` |
| `puts()` | Affiche une chaîne avec retour à la ligne | Aucune | `puts("Hello World");` |
| `fgets()` | Lit une ligne avec une taille maximale | `(char *str, int size, FILE *stream)` | `fgets(buffer, 50, stdin);` |
| `putchar()` | Affiche un caractère unique | `(char c)` | `putchar('A');` |
| `getchar()` | Lit un caractère depuis l'entrée standard | Aucun | `char c = getchar();` |
| `fprintf()` | Écrit du texte formaté dans un fichier | Identique à `printf()` avec un `FILE *stream` | `fprintf(fichier, "Nom: %s", nom);` |
| `fscanf()` | Lit des données formatées depuis un fichier | Identique à `scanf()` avec un `FILE *stream` | `fscanf(fichier, "%d", &age);` |
| `fopen()` | Ouvre un fichier | `"r"` = lecture, `"w"` = écriture, `"a"` = ajout | `FILE *f = fopen("data.txt", "r");` |
| `fclose()` | Ferme un fichier ouvert | `(FILE *stream)` | `fclose(f);` |
| `fwrite()` | Écrit des données binaires dans un fichier | `(void *ptr, size_t size, size_t count, FILE *stream)` | `fwrite(data, sizeof(char), 100, fichier);` |
| `fread()` | Lit des données binaires depuis un fichier | `(void *ptr, size_t size, size_t count, FILE *stream)` | `fread(buffer, sizeof(char), 100, fichier);` |

---

## 🔹 2. Fonctions de Manipulation de Chaînes
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `strlen()` | Retourne la longueur d'une chaîne | `(const char *str)` | `int len = strlen("Hello");` |
| `strcpy()` | Copie une chaîne dans une autre | `(char *dest, const char *src)` | `strcpy(dest, "Texte");` |
| `strncpy()` | Copie une chaîne avec une limite de taille | `(char *dest, const char *src, size_t n)` | `strncpy(dest, "Texte", 5);` |
| `strcat()` | Concatène deux chaînes | `(char *dest, const char *src)` | `strcat(dest, " suite");` |
| `strcmp()` | Compare deux chaînes | `(const char *str1, const char *str2)` | `if(strcmp(s1, s2) == 0) {}` |
| `strchr()` | Cherche un caractère dans une chaîne | `(const char *str, int c)` | `char *ptr = strchr(str, 'a');` |
| `strstr()` | Cherche une sous-chaîne dans une autre | `(const char *haystack, const char *needle)` | `char *ptr = strstr(texte, "mot");` |

---

## 🔹 3. Fonctions Mathématiques
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `sizeof()` | Retourne la taille en octets d'un type ou d'une variable | `(type)` ou `(variable)` | `printf("%lu", sizeof(int));` |
| `sqrt()` | Calcule la racine carrée | `(double x)` | `double r = sqrt(25.0);` |
| `pow()` | Calcule la puissance | `(double base, double exp)` | `double p = pow(2, 3);` |
| `abs()` | Retourne la valeur absolue d'un entier | `(int x)` | `int valeur = abs(-5);` |
| `ceil()` | Arrondit un nombre vers le haut | `(double x)` | `double r = ceil(2.3);` |
| `floor()` | Arrondit un nombre vers le bas | `(double x)` | `double r = floor(2.9);` |

---

## 🔹 4. Fonctions de Gestion de Mémoire
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `malloc()` | Alloue dynamiquement de la mémoire | `(size_t size)` | `int *ptr = malloc(10 * sizeof(int));` |
| `calloc()` | Alloue dynamiquement de la mémoire et l'initialise à zéro | `(size_t n, size_t size)` | `int *ptr = calloc(10, sizeof(int));` |
| `realloc()` | Redimensionne un bloc de mémoire alloué | `(void *ptr, size_t size)` | `ptr = realloc(ptr, 20 * sizeof(int));` |
| `free()` | Libère la mémoire allouée | `(void *ptr)` | `free(ptr);` |

---

## 🔹 5. Fonctions de Gestion de Processus
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `exit()` | Termine le programme immédiatement | `(int status)` | `exit(0);` |
| `system()` | Exécute une commande système | `(const char *command)` | `system("ls");` |
| `fork()` | Crée un processus fils | Aucun | `pid_t pid = fork();` |

---

## 🔹 6. Fonctions de Gestion du Temps
| Fonction  | Description | Options | Exemples |
|-----------|------------|---------|----------|
| `time()` | Retourne l'heure actuelle en secondes depuis l'époque | `(time_t *tloc)` | `time_t now = time(NULL);` |
| `clock()` | Retourne le temps CPU écoulé | Aucun | `clock_t t = clock();` |
| `difftime()` | Calcule la différence entre deux temps | `(time_t time1, time_t time2)` | `double diff = difftime(t1, t2);` |
| `strftime()` | Formate une date en chaîne de caractères | `(char *s, size_t max, const char *format, const struct tm *tm)` | `strftime(buffer, 80, "%Y-%m-%d", tm_info);` |

---

## 📌 Conclusion
Ce document regroupe les fonctions C les plus utilisées avec leur description, leurs options et des exemples concrets. Pour plus de détails, utilisez `man <fonction>` dans un terminal Linux/macOS.

