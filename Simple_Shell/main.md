# Analyse détaillée du fichier `main.c`

Ce fichier est le cœur du programme de shell. Il contient la boucle principale (`shell_loop`), le point d'entrée (`main`), ainsi que la gestion des commandes et des commandes intégrées (built-ins).

---

## 1. `is_interactive`

```c
int is_interactive(void)
{
	return (isatty(STDIN_FILENO));
}
```

- Vérifie si le shell est en mode interactif (si l'entrée standard est un terminal).
- `isatty(STDIN_FILENO)` retourne 1 si le descripteur correspond à un terminal.
- Utilisé pour afficher un prompt uniquement en mode interactif.

---

## 2. `get_input_line`

```c
ssize_t get_input_line(char **line, size_t *len)
```

- Lit une ligne de l'entrée standard.
- Utilise `_getline` pour gérer dynamiquement la mémoire.
- Si une ligne se termine par un retour à la ligne, celui-ci est supprimé.
- En cas d'erreur, retourne -1. En mode interactif, affiche un retour à la ligne pour bien formater l'affichage.

### Ligne par ligne :
```c
if (!line || !len)
	return (-1);
```
- Vérifie que les pointeurs sont valides.

```c
r = _getline(line, len, 0);
```
- Appelle `_getline` avec le descripteur `0` (stdin).

```c
if (r == -1)
{
	if (is_interactive())
		write(1, "\n", 1);
	return (-1);
}
```
- En cas d'erreur ou EOF, retourne -1.
- Si le shell est interactif, ajoute une nouvelle ligne pour l'affichage.

```c
if (r > 0 && (*line)[r - 1] == '\n')
	(*line)[r - 1] = '\0';
```
- Supprime le retour à la ligne final.

---

## 3. `handle_builtin_or_execute`

```c
int handle_builtin_or_execute(char **argv, char **env, char *progname,
	char *line, int *exit_status)
```

- Gère l'exécution d'une commande : d'abord les commandes intégrées (`env`, `exit`), puis les commandes classiques trouvées via `PATH`.

### Étapes :
1. Vérifie les pointeurs.
2. Si `argv[0]` est `env`, appelle `builtin_env`.
3. Sinon, appelle `handle_builtin`.
4. Si ce n'est pas un builtin, cherche le chemin avec `_which`.
5. Si la commande est trouvée, exécute-la.
6. Retourne le code de sortie.

### Ligne par ligne :
```c
if (!argv || !argv[0] || !env || !progname)
	return (0);
```
- Vérifie la validité des arguments.

```c
if (strcmp(argv[0], "env") == 0)
	return (builtin_env(argv, env, line, *exit_status));
```
- Si la commande est `env`, on l'exécute directement.

```c
if (handle_builtin(argv, env, line, *exit_status) == -1)
	return (-1);
```
- Appelle `handle_builtin`, si c'est `exit`, retourne `-1`.

```c
cmd_path = _which(argv[0], env);
```
- Cherche le chemin de la commande dans le PATH.

```c
if (cmd_path == NULL)
{
	fprintf(stderr, "%s: 1: %s: not found\n", progname, argv[0]);
	return (127);
}
```
- Affiche une erreur si la commande est introuvable.

```c
if (cmd_path != argv[0])
{
	free(argv[0]);
	argv[0] = cmd_path;
}
```
- Remplace le nom de commande par son chemin complet (libère l'ancien si nécessaire).

```c
exec_status = execute(argv, env);
return (exec_status);
```
- Exécute la commande.

---

## 4. `shell_loop`

```c
int shell_loop(char **env, char *progname, int *exit_status)
```

- La boucle principale du shell.
- Affiche un prompt.
- Lit une ligne.
- Analyse les arguments.
- Exécute la commande.
- Libère la mémoire à chaque itération.

### Ligne par ligne :
```c
if (is_interactive())
	write(1, "#cisfun$ ", 9);
```
- Affiche le prompt en mode interactif.

```c
r = get_input_line(&line, &len);
```
- Lit une ligne depuis l’entrée utilisateur.

```c
if (r == -1)
	break;
```
- Sort de la boucle en cas d’erreur ou de EOF (Ctrl+D).

```c
if (r == 0 || line[0] == '\0')
	continue;
```
- Ignore les lignes vides.

```c
argv = stock_args(line);
```
- Découpe la ligne en tableau de chaînes.

```c
if (argv != NULL && argv[0] != NULL)
{
	cmd_status = handle_builtin_or_execute(argv, env, progname, line, exit_status);
```
- Exécute la commande (builtin ou externe).

```c
if (cmd_status == -1)
{
	free_argv(argv);
	break;
}
```
- Si la commande était `exit`, on libère et sort.

```c
else if (cmd_status != 0)
	*exit_status = cmd_status;
```
- Sinon, on met à jour le dernier code d’erreur.

```c
free_argv(argv);
```
- Libère la mémoire.

---

## 5. `main`

```c
int main(int ac, char **av, char **env)
```
- Point d’entrée du shell.
- Lance la boucle principale avec `shell_loop`.
- Gère le code de sortie du programme.

```c
int exit_status = 0;
```
- Initialise le code de sortie à 0.

```c
(void)ac;
```
- Supprime le warning du paramètre inutilisé.

```c
if (shell_loop(env, av[0], &exit_status) == -1)
	return (exit_status);
```
- Lance la boucle shell.

```c
return (exit_status);
```
- Retourne le dernier code de sortie.

---

## Résumé

Le fichier `main.c` orchestre toute l’exécution du shell : de l’affichage du prompt à l’exécution de la commande, tout en passant par la gestion des erreurs et des commandes intégrées. C’est le point central du fonctionnement.

### Articulation générale :
- `main()` ⟶ `shell_loop()` ⟶ `get_input_line()` & `handle_builtin_or_execute()`
- Builtins (env/exit) gérés directement sans passer par fork.
- Les autres commandes passent par `_which()` puis `execute()`.

---

Souhaite-tu qu'on génère une version visuelle (schéma/canvas) de cette explication ?

