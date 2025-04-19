### Documentation de la fonction `handle_builtin`

```c
int handle_builtin(char **argv)
{
    if (argv[0] && strcmp(argv[0], "exit") == 0)
    {
        free_argv(argv);
        return (-1);
    }
    return (0);
}
```

---

### ✨ Fonction : `handle_builtin`

#### ▶ Prototype :
```c
int handle_builtin(char **argv);
```

#### ▶ But :
Cette fonction détecte si la commande entrée par l'utilisateur est un **built-in** reconnu (dans ce cas uniquement `exit`), et agit en conséquence.

---

#### 🔎 Explication ligne par ligne :

```c
if (argv[0] && strcmp(argv[0], "exit") == 0)
```
- On vérifie que la commande existe (`argv[0] != NULL`) **ET** qu'elle correspond exactement à la chaîne "exit".
- Utilisation de `strcmp` (chaîne = chaîne) car on ne veut pas de correspondance partielle ("exitnow" ou "ex" doivent être ignorés).

```c
free_argv(argv);
```
- On libère tout le tableau `argv` (y compris chaque argument individuellement).
- Cela évite une **fuite mémoire** avant de sortir du shell.
- Appelle une fonction spécifique que nous avons définie (voir plus bas).

```c
return (-1);
```
- Renvoie `-1` pour signaler à `shell_loop()` que l'utilisateur souhaite **quitter le shell**.
- Cela permet de briser la boucle dans `shell_loop()` avec :
```c
if (handle_builtin_or_execute(argv, env) == -1)
```

```c
return (0);
```
- Si ce n'est pas une commande reconnue, on retourne `0`.
- Cela permet à `handle_builtin_or_execute()` de savoir qu'il doit **continuer avec une commande externe** (non builtin).

---

### Documentation de la fonction `free_argv`

```c
void free_argv(char **argv)
{
    int i;
    for (i = 0; argv[i]; i++)
        free(argv[i]);
    free(argv);
}
```

---

### ✨ Fonction : `free_argv`

#### ▶ Prototype :
```c
void free_argv(char **argv);
```

#### ▶ But :
Libérer proprement tout le tableau contenant les arguments découpés de la commande utilisateur (ex: `ls -l /tmp`).

---

#### 🔎 Explication ligne par ligne :

```c
for (i = 0; argv[i]; i++)
    free(argv[i]);
```
- On libère **chaque argument** un par un.
- `argv` est un tableau de `char *`, donc il faut libérer **chaque chaîne**.
- Exemple : pour `ls -l /tmp`, il y a 3 `malloc` pour chaque `strdup` dans `stock_args()`.

```c
free(argv);
```
- Enfin, on libère le **tableau de pointeurs lui-même**.
- Cela évite toute fuite mémoire sur les allocations créées dans `stock_args()`.

---

Souhaites-tu maintenant que je passe à la documentation de `execute()`, `_which()`, ou des fonctions personnalisées comme `_getenv()` ou `_getline()` ?

