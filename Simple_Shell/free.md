# Explication complète de `free.c`

Ce fichier contient une seule fonction utilitaire : `free_argv`.  
Elle est essentielle pour **libérer correctement la mémoire allouée dynamiquement** à un tableau de chaînes de caractères (souvent `argv`), afin d’éviter les **fuites mémoire** dans notre shell.

---

## 🔧 `free_argv`

```c
void free_argv(char **argv)
```

### Fonction :
Libère un tableau de chaînes de caractères (`char **`) dont chaque élément a été alloué par `malloc` ou `strdup`, **et qui se termine par `NULL`**.

---

### 📘 Ligne par ligne

```c
int i;
```
- Déclaration d’un compteur `i` utilisé pour parcourir le tableau `argv`.

---

```c
if (!argv)
	return;
```
- Vérifie si le tableau est `NULL` (par sécurité).
- Si `argv` est `NULL`, la fonction quitte immédiatement sans rien faire.
- 🧠 **Pourquoi ?** Tenter de parcourir un tableau `NULL` provoquerait un crash (segfault).

---

```c
for (i = 0; argv[i]; i++)
	free(argv[i]);
```
- Boucle à travers chaque élément du tableau (tant que `argv[i] != NULL`).
- Libère **chaque chaîne de caractères** individuelle avec `free(argv[i])`.
- 🧠 **Pourquoi ?** Chaque élément a été `malloc` ou `strdup`, donc doit être libéré individuellement.

---

```c
free(argv);
```
- Après avoir libéré chaque élément, on libère le **tableau lui-même**.
- 🧠 **Pourquoi ?** Le tableau (type `char **`) a également été alloué dynamiquement avec `malloc`.

---

### 📌 Résumé

- `free_argv` est indispensable pour **éviter les fuites mémoire** lorsqu’un tableau de chaînes est utilisé.
- Elle suit la bonne pratique de :
  1. Libérer chaque élément
  2. Puis libérer le tableau lui-même

---

### 💡 Schéma visuel (mémoire)

Imaginons une exécution de :  
```c
argv = malloc(3 * sizeof(char *));
argv[0] = strdup("ls");
argv[1] = strdup("-l");
argv[2] = NULL;
```

Mémoire (vue simplifiée) :
```
argv ---> [ * ] ---> "ls\0"
          [ * ] ---> "-l\0"
          [NULL]
```

🔁 La fonction va :
- `free("ls")`
- `free("-l")`
- `free(argv)`

Tout est proprement libéré ✅

---

### 🧹 Intégration dans le projet `simple_shell`

- `free_argv` est appelée **après chaque exécution de commande** ou **en cas d’erreur**, pour nettoyer proprement `argv` alloué via `stock_args`.

---

Si tu veux, je peux enchaîner avec `input.c`, `parser.c`, `main.c`, etc. Dis-moi juste le prochain !
