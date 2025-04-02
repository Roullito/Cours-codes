
# 📘 C - File I/O (Input/Output)

---

## 🎯 Objectifs pédagogiques

À la fin de ce cours, vous devez être capable de :

- Créer, ouvrir, fermer, lire et écrire dans un fichier.
- Expliquer ce qu’est un **file descriptor** (descripteur de fichier).
- Connaître les 3 **descripteurs standards** (stdin, stdout, stderr).
- Utiliser les appels systèmes `open`, `read`, `write`, `close`.
- Comprendre les **droits (permissions)** lors de la création de fichiers.
- Faire la différence entre une **fonction** et un **appel système**.

---

## 🧠 Qu’est-ce qu’un File Descriptor ?

Un **file descriptor** est un **entier** utilisé par le noyau Linux pour représenter un fichier ouvert. 

| Nom POSIX     | Valeur | Signification       |
|---------------|--------|---------------------|
| `STDIN_FILENO`  | 0      | Entrée standard      |
| `STDOUT_FILENO` | 1      | Sortie standard      |
| `STDERR_FILENO` | 2      | Sortie d'erreur      |

---

## 🛠 Les appels système autorisés

| Appel    | Utilité                          |
|----------|----------------------------------|
| `open()` | Ouvrir/créer un fichier          |
| `close()`| Fermer un fichier                |
| `read()` | Lire depuis un fichier           |
| `write()`| Écrire dans un fichier           |

---

## 📂 Ouvrir un fichier : `open`

### Prototype :
```c
int open(const char *pathname, int flags, mode_t mode);
```

### Flags courants :
- `O_RDONLY` : lecture seule
- `O_WRONLY` : écriture seule
- `O_RDWR`   : lecture et écriture
- `O_CREAT`  : créer le fichier s'il n'existe pas
- `O_APPEND` : écrire à la fin du fichier
- `O_TRUNC`  : tronquer le contenu

### Exemple :
```c
int fd = open("fichier.txt", O_CREAT | O_WRONLY | O_TRUNC, 0600);
```

---

## 📖 Lire un fichier : `read`

```c
ssize_t read(int fd, void *buf, size_t count);
```

```c
char buffer[1024];
ssize_t bytes_read = read(fd, buffer, sizeof(buffer));
```

---

## 🖊 Écrire dans un fichier : `write`

```c
ssize_t write(int fd, const void *buf, size_t count);
```

```c
write(STDOUT_FILENO, "Hello!\n", 7);
```

---

## ❌ Fermer un fichier : `close`

```c
int close(int fd);
```

```c
close(fd);
```

---

## 🧾 Permissions lors de la création

```c
open("fichier.txt", O_CREAT | O_WRONLY, 0644);
```

- `0644` = `rw-r--r--`

---

## 💡 Appel système vs Fonction

- `write()` = appel système
- `printf()` = fonction de bibliothèque (utilise `write()`)

---

## 🧨 Utiliser `dprintf`

```c
dprintf(fd, "Nom : %s\n", nom);
```

---

## ⚠️ Contraintes du projet

- Appels système autorisés : `read`, `write`, `open`, `close`
- Pas de `printf`, `puts`, `calloc`, `realloc`
- 5 fonctions max/fichier, pas de variables globales
- Headers protégés : `#ifndef/#define`
- Compilation :
```bash
gcc -Wall -Werror -Wextra -pedantic -std=gnu89
```

---

## 📁 Exemple de `main.h`

```c
#ifndef MAIN_H
#define MAIN_H

#include <unistd.h>
#include <fcntl.h>
#include <stdlib.h>

int create_file(const char *filename, char *text_content);
int append_text_to_file(const char *filename, char *text_content);
ssize_t read_textfile(const char *filename, size_t letters);

#endif
```

---

## ✅ Résumé

- `open()` → ouvrir fichier
- `read()` / `write()` → lire / écrire
- `close()` → fermer
- Aucun usage de `printf`, `puts`…
