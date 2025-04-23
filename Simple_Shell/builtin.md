# Explication détaillée de `builtin.c`

Ce fichier gère les **commandes internes** du shell (`exit` et `env`), ainsi qu'une fonction utilitaire pour vérifier si une chaîne est un nombre.

---

## `_is_number`

```c
int _is_number(char *str)
```

- Vérifie si une chaîne représente un nombre entier valide (positif ou négatif).
- Retourne `1` si c’est un nombre, `0` sinon.

### Détail ligne par ligne :

```c
if (!str)
    return (0);
```
Vérifie que la chaîne existe. Si `NULL`, ce n’est pas un nombre.

```c
if (str[0] == '-' || str[0] == '+')
    i++;
```
On saute le signe si présent (ex : `-12`, `+34`).

```c
for (; str[i]; i++)
{
    if (str[i] < '0' || str[i] > '9')
        return (0);
}
```
Vérifie caractère par caractère si chaque lettre est un chiffre.

---

## `builtin_exit`

```c
int builtin_exit(char **argv, char **env, char *line, int last_status)
```

- Traite la commande `exit`.
- Si un argument numérique est passé (`exit 3`), on quitte avec ce code.
- Sinon, on quitte avec le dernier code de retour (`last_status`).

### Détail :

```c
int exit_status = last_status;
(void)env;
```
Initialise `exit_status` au dernier code connu. `env` est ignoré ici.

```c
if (argv[1])
```
Si un argument est fourni après `exit`, on l’examine.

```c
if (_is_number(argv[1]))
    exit_status = atoi(argv[1]);
```
Si c’est bien un nombre, on le convertit et on l’utilise comme code de sortie.

```c
else {
    fprintf(stderr, "./hsh: 1: exit: Illegal number: %s\n", argv[1]);
    free_argv(argv);
    free(line);
    exit(2);
}
```
Si ce n’est pas un nombre, on affiche une erreur, libère la mémoire, et sort avec le code 2.

Enfin :

```c
free_argv(argv);
free(line);
exit(exit_status);
```
Libère la mémoire et termine le programme.

---

## `builtin_env`

```c
int builtin_env(char **argv, char **env, char *line, int last_status)
```

- Affiche toutes les variables d’environnement si aucun argument n’est fourni.

### Détail :

```c
(void)line;
(void)last_status;
```
Non utilisés ici.

```c
if (argv[1])
    return (0);
```
Si un argument est donné après `env`, la commande n'est pas traitée.

```c
if (!env)
    return (1);
```
Sécurité : si `env` est `NULL`, on retourne une erreur.

```c
for (i = 0; env[i]; i++)
    printf("%s\n", env[i]);
```
Affiche toutes les variables d’environnement.

---

## `handle_builtin`

```c
int handle_builtin(char **argv, char **env, char *line, int last_status)
```

- Parcourt un tableau de `builtin_t` (structure avec un nom et une fonction).
- Si `argv[0]` correspond à un nom de commande builtin, exécute la fonction correspondante.

### Détail :

```c
builtin_t builtins[] = {
    {"exit", builtin_exit},
    {"env", builtin_env},
    {NULL, NULL}
};
```
Tableau de paires nom/fonction.

```c
if (!argv || !argv[0])
    return (0);
```
Validation des arguments.

```c
for (i = 0; builtins[i].name; i++)
{
    if (strcmp(argv[0], builtins[i].name) == 0)
    {
        ret = builtins[i].func(argv, env, line, last_status);
        return (ret);
    }
}
```
Compare `argv[0]` à chaque nom builtin. Si match, exécute la fonction associée.

```c
return (0);
```
Si aucune commande builtin n’est trouvée, on retourne 0.

