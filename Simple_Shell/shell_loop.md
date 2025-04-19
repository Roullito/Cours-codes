# Documentation : shell_loop.c

---

## Fonction : `shell_loop`

```c
void shell_loop(char **env)
```

### Description :
Cette fonction est le cœur du shell. Elle implémente la boucle principale du shell qui tourne indéfiniment jusqu'à ce que l'utilisateur quitte le programme (par exemple avec la commande `exit` ou un EOF via Ctrl+D).

---

## Détail ligne par ligne :

```c
char *line = NULL, **argv;
```
- `line` : un pointeur vers la ligne de commande saisie par l'utilisateur. Initialisé à `NULL` pour que `getline` puisse allouer dynamiquement la mémoire nécessaire.
- `argv` : tableau de chaînes de caractères contenant les arguments séparés (ex: `ls -l` devient argv[0] = "ls", argv[1] = "-l", etc).

```c
size_t len = 0;
```
- `len` : taille de la ligne (utile pour `getline`, qui peut la redimensionner).

```c
ssize_t r;
```
- `r` : valeur de retour de la lecture de ligne. Nombre de caractères lus ou -1 en cas d'erreur ou EOF.

---

## Boucle principale :

```c
while (1)
```
- Crée une boucle infinie, typique des shells. On en sort uniquement par un `break`.

### Prompt interactif :
```c
if (is_interactive())
    write(1, "$ ", 2);
```
- Affiche le prompt `"$ "` si le shell est en mode interactif (terminal ouvert).

---

## Lecture de la commande :
```c
r = get_input_line(&line, &len);
if (r == -1)
    break;
```
- Appelle une fonction personnalisée `get_input_line` (wrappant `_getline`).
- Si `-1` est retourné, cela signifie une erreur ou un Ctrl+D → le shell quitte.

---

## Parsing :
```c
argv = stock_args(line);
```
- Transforme `line` en un tableau `argv`, en séparant les mots par des espaces.

```c
if (argv != NULL && argv[0] != NULL)
```
- On vérifie qu'une commande a bien été saisie (non vide).

---

## Builtin ou exécutable :
```c
if (handle_builtin_or_execute(argv, env) == -1)
{
    free_argv(argv);
    break;
}
```
- Cette fonction vérifie si la commande est un **builtin** comme `exit`, `env`, etc.
- Si c'est un builtin `exit` → retourne `-1` → on libère `argv` et on quitte la boucle.
- Sinon, on exécute la commande normalement.

---

## Libération mémoire :
```c
free_argv(argv);
```
- Libère la mémoire allouée par `stock_args` (chaque argument et le tableau).

---

## Fin de la fonction :
```c
free(line);
```
- Libère la ligne lue pour éviter les fuites mémoires.

---

## Flux global :
1. Affiche un prompt si terminal interactif.
2. Lit la commande.
3. Parse en tableau.
4. Si c'est un builtin : on l'exécute, potentiellement on quitte.
5. Sinon : on exécute la commande avec `execve`.
6. On libère argv et recommence.

---

Souhaites-tu que je continue avec la fonction suivante (`get_input_line`, `_getline`, `stock_args`, etc.) ?
