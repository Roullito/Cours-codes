# 🐚 Everything You Need to Know to Start Coding Your Own Shell

## 📚 Table of Contents

- [1. PID & PPID](#1-pid--ppid)
- [2. Command Line Arguments](#2-command-line-arguments)
- [3. Read Line Input](#3-read-line-input)
- [4. Splitting Input into Arguments](#4-splitting-input-into-arguments)
- [5. Executing Programs (execve)](#5-executing-programs-execve)
- [6. Process Creation (fork)](#6-process-creation-fork)
- [7. Synchronizing with wait](#7-synchronizing-with-wait)
- [8. File Information (stat)](#8-file-information-stat)
- [9. The Environment](#9-the-environment)

---

## 1. PID & PPID

### 🔍 Qu'est-ce qu'un processus ?
Un **processus** est une instance en cours d'exécution d'un programme. Chaque processus possède un identifiant unique appelé **PID** (*Process ID*). Quand un programme est lancé, un nouveau processus est créé et obtient un PID distinct.

### 📌 PID (Process ID)
La fonction `getpid()` permet d'obtenir l'identifiant du processus courant. C'est utile pour comprendre qui exécute quoi dans un système.

### 📌 PPID (Parent Process ID)
Chaque processus est créé par un autre processus, qu'on appelle **le parent**. On peut obtenir le PID du parent avec `getppid()`.

### 🧪 Exemple :
```c
#include <stdio.h>
#include <unistd.h>

int main(void)
{
    printf("PID: %d\n", getpid());
    printf("PPID: %d\n", getppid());
    return 0;
}
```
À chaque exécution, `getpid()` retourne un numéro différent, mais si on exécute plusieurs fois le programme dans le même terminal, `getppid()` donne toujours la même valeur (le shell parent).

### 🧠 Analyse pratique
- `echo $$` affiche le PID du shell courant.
- Comparez ce PID avec le résultat de `getppid()` dans votre programme.

---

## 2. Command Line Arguments

### 📥 Qu'est-ce qu'un argument ?
Quand on exécute un programme depuis le terminal, on peut lui passer des **arguments**. Ces arguments sont récupérés dans `main()` via :
```c
int main(int ac, char **av);
```
- `ac` (argument count) : nombre d'arguments
- `av` (argument values) : tableau contenant les arguments sous forme de chaînes

### 🔍 Détails
- `av[0]` contient le nom du programme lui-même
- `av[1]`, `av[2]`, ... contiennent les arguments fournis

### 🧪 Exemple :
```bash
./mon_programme hello world
```
```c
av[0] = "./mon_programme"
av[1] = "hello"
av[2] = "world"
```

---

## 3. Read Line Input

### 🧾 Objectif
Créer un prompt simple :
- Affiche un `$ `
- Lit ce que tape l'utilisateur
- Affiche ce texte ensuite

### 📌 Fonction `getline()`
Permet de lire une ligne entière depuis `stdin`.
```c
ssize_t getline(char **lineptr, size_t *n, FILE *stream);
```
- Alloue automatiquement la mémoire
- Gère les tailles dynamiquement

### 💡 Attention :
- L’utilisateur peut quitter avec **Ctrl+D**, ce qui génère EOF (end-of-file).

---

## 4. Splitting Input into Arguments

### 🎯 Objectif
Une fois qu'une ligne est lue, il faut la découper pour en extraire les arguments.

### 🛠 Fonction `strtok()`
Elle découpe une chaîne en tokens (mots) selon un délimiteur :
```c
char *strtok(char *str, const char *delim);
```
- Modifie la chaîne d'origine
- Nécessite plusieurs appels pour parcourir tous les tokens

### 🧠 Exemple pratique
```c
char str[] = "ls -l /home";
char *token = strtok(str, " ");
while (token != NULL) {
    printf("%s\n", token);
    token = strtok(NULL, " ");
}
```

---

## 5. Executing Programs (execve)

### 🚀 Exécuter un autre programme
Avec `execve()`, on peut remplacer le processus courant par un autre.
```c
int execve(const char *pathname, char *const argv[], char *const envp[]);
```
- `pathname` : chemin vers le binaire à exécuter
- `argv` : arguments à passer au programme
- `envp` : environnement

### ⚠️ Important
- Si `execve()` réussit, le code après n’est **jamais exécuté** !
- En cas d’échec, il retourne `-1`

### 🔍 Exemple visuel
```
Avant execve
/bin/ls -l /tmp est exécuté...
=> Plus rien après n’est affiché (si execve réussit)
```

---

## 6. Process Creation (fork)

### 👨‍👦 Créer un nouveau processus
La fonction `fork()` permet de créer un **clone** du processus courant.
```c
pid_t fork(void);
```

### 🧠 Résultat
- **Dans le parent** : `fork()` retourne le PID du fils
- **Dans le fils** : `fork()` retourne `0`

### 📌 Conséquence
Les deux processus continuent leur exécution indépendamment. Chacun a sa propre mémoire (copie).

### 🧪 Test simple
- Affiche des messages différents dans le parent et dans le fils
- Compare les PIDs

---

## 7. Synchronizing with wait

### 🛑 Pourquoi attendre ?
Quand un processus père crée un fils, il peut souhaiter **attendre sa fin** avant de continuer.

### 🧰 Fonction `wait()`
```c
pid_t wait(int *status);
```
- Suspends le père jusqu'à la fin d’un de ses fils
- Remplit `status` avec des informations sur la fin du fils

### 🔁 Cas pratique
- Le père crée un fils
- Le fils dort 3 secondes, puis termine
- Le père attend la fin du fils et affiche un message

---

## 8. File Information (stat)

### 📦 Objectif
Vérifier si un fichier existe, ou obtenir ses **métadonnées** (taille, permissions, etc.)

### 📌 Fonction `stat()`
```c
int stat(const char *pathname, struct stat *statbuf);
```

- Si le fichier existe, retourne 0
- Sinon, retourne -1

### 🧪 Exemple d'utilisation
- Lire plusieurs chemins passés en argument
- Afficher s’ils existent ou non avec `stat()`

---

## 9. The Environment

### 🌍 Qu’est-ce que l’environnement ?
Chaque processus possède un **tableau de chaînes** représentant son environnement. Exemple : `PATH`, `HOME`, `USER`, etc.

### 📥 Accès possible :
- Troisième paramètre de `main()` : `char **env`
- Variable globale `extern char **environ;`

### 📌 Exemple de lecture :
```c
int main(int ac, char **av, char **env)
{
    for (int i = 0; env[i] != NULL; i++)
        printf("%s\n", env[i]);
    return 0;
}
```

### 🔬 Travaux pratiques :
- Créer une fonction `_getenv` qui retourne la valeur d’une variable
- Créer `_setenv` et `_unsetenv` pour modifier ou supprimer une variable
- Lire la variable `PATH` et afficher chaque dossier
- Créer une **liste chaînée** contenant chaque dossier du PATH

---

🎯 **Prêt à passer à la pratique ?**
Dès que tu es chaud, on attaque les exercices un par un, avec schémas, étapes logiques et tests guidés 👨‍🏫

