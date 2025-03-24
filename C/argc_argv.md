# 📘 C - Arguments `argc` et `argv`

## 📚 Resources
Read or watch:
- [Arguments to main](#)
- [argc and argv](#)
- [What does argc and argv mean?](#)
- [How to compile with unused variables](#)

---

## 🎯 Learning Objectives
À la fin de ce projet, vous devriez être capable d’expliquer :
- **Comment utiliser les arguments passés à un programme.**
- **Quels sont les deux prototypes de `main` et quand les utiliser.**
- **Comment éviter les erreurs de compilation avec des variables inutilisées (`__attribute__((unused))` ou `(void)`).**

---

## 🔍 Comprendre `argc` et `argv`
En C, **`argc` et `argv`** permettent à un programme de recevoir des arguments depuis la ligne de commande.

### 🔹 Définition
- **`argc`** (*argument count*) : représente le **nombre d'arguments** passés au programme.
- **`argv`** (*argument vector*) : un tableau de **chaînes de caractères** contenant les arguments passés.

### 🔹 Les deux prototypes de `main`
```c
int main(int argc, char *argv[])
```
ou
```c
int main(int argc, char **argv)
```
`argv` est un **tableau de pointeurs vers des chaînes de caractères**.
- `char *argv[]` et `char **argv` sont **équivalents** (un tableau de pointeurs est similaire à un double pointeur).
- `argc` est toujours **supérieur ou égal à 1** car le **nom du programme** est toujours le premier argument (`argv[0]`).

### 🔹 Exemple simple d'affichage des arguments
```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    printf("Nombre d'arguments : %d\n", argc);
    for (int i = 0; i < argc; i++) {
        printf("Argument %d : %s\n", i, argv[i]);
    }
    return 0;
}
```
#### 🖥 Exécution en ligne de commande
```sh
$ ./a.out Hello World 42
Nombre d'arguments : 4
Argument 0 : ./a.out
Argument 1 : Hello
Argument 2 : World
Argument 3 : 42
```

### 🔹 Accéder aux arguments
Chaque argument est **une chaîne de caractères**. Si un argument représente un nombre, vous devez le **convertir**.
```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    if (argc != 2) {
        printf("Usage: %s <nombre>\n", argv[0]);
        return 1;
    }
    int nombre = atoi(argv[1]);
    printf("Nombre converti : %d\n", nombre);
    return 0;
}
```
#### 🖥 Exécution
```sh
$ ./a.out 10
Nombre converti : 10
```

---

## 🚀 Cas d’utilisation
### 🔹 Vérification d’arguments obligatoires
```c
if (argc < 2) {
    printf("Erreur: veuillez fournir un argument\n");
    return 1;
}
```

### 🔹 Traiter plusieurs arguments
```c
for (int i = 1; i < argc; i++) {
    printf("Argument %d: %s\n", i, argv[i]);
}
```

### 🔹 Concaténation de tous les arguments
```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    for (int i = 1; i < argc; i++) {
        printf("%s ", argv[i]);
    }
    printf("\n");
    return 0;
}
```
#### 🖥 Exécution
```sh
$ ./a.out Bonjour le monde
Bonjour le monde
```

---

## 🔥 Gestion des Variables Inutilisées
Lorsque vous ne voulez pas utiliser `argc` ou `argv`, vous pouvez les **ignorer proprement**.
### 🔹 Méthode 1 : Utilisation de `(void)`
```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    (void)argc;
    (void)argv;
    printf("Hello, World!\n");
    return 0;
}
```

### 🔹 Méthode 2 : `__attribute__((unused))`
```c
#include <stdio.h>

int main(int argc __attribute__((unused)), char *argv[] __attribute__((unused))) {
    printf("Hello, World!\n");
    return 0;
}
```

---

## 📜 Exigences du Projet
- **Éditeurs autorisés** : `vi`, `vim`, `emacs`
- **Compilation** : Ubuntu 20.04 avec `gcc -Wall -Werror -Wextra -pedantic -std=gnu89`
- **Respect des normes Betty**
- **Pas plus de 5 fonctions par fichier**
- **Les prototypes doivent être dans `main.h`**

---

## 🚀 Conclusion
- **`argc` et `argv` permettent de récupérer des arguments en ligne de commande.**
- **Il faut toujours gérer les cas où les arguments sont absents.**
- **Les variables inutilisées doivent être marquées avec `(void)` ou `__attribute__((unused))`**.

🎯 **Prochaines étapes :**
- Tester avec des arguments dynamiques
- Manipuler des fichiers en ligne de commande
- Expérimenter avec `getopt` pour la gestion d’options complexes

🚀 **Happy coding!**

