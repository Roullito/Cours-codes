```markdown
# Décomposition du fichier `parser.c` ligne par ligne

Ce fichier contient les fonctions responsables du **découpage (tokenization)** d'une ligne de commande saisie dans le shell, afin de la transformer en tableau d'arguments (type `argv`).

---

## 1. `is_delim`
```c
int is_delim(char c, const char *delim)
```
- **But :** Vérifie si un caractère `c` fait partie d'une chaîne de délimiteurs `delim`.
- **Utilité :** Utilisée pour ignorer les espaces ou autres séparateurs entre les arguments.

```c
while (*delim != '\0')
```
- On parcourt la chaîne `delim`.

```c
if (*delim == c)
    return (1);
```
- Si `c` correspond à un des caractères dans `delim`, on retourne 1.

```c
return (0);
```
- Sinon, `c` n'est pas un délimiteur.

---

## 2. `_strtok`
```c
char *_strtok(char *str, const char *delim)
```
- **But :** Fonction de tokenisation maison similaire à `strtok`.
- **Comportement :** Elle conserve son contexte entre deux appels grâce à la variable statique `current`.

### 1er appel
```c
if (str != NULL)
    current = str;
```
- Le premier appel initialise `current` à la chaîne `str`.

### Ignorer les délimiteurs en début de ligne
```c
while (*current != '\0' && is_delim(*current, delim))
    current++;
```

### Fin de chaîne atteinte ?
```c
if (*current == '\0')
    return (NULL);
```

### Début du token
```c
token = current;
```

### Avancer jusqu'au prochain délimiteur
```c
while (*current != '\0' && !is_delim(*current, delim))
    current++;
```

### Finir le token avec `\0` si possible
```c
if (*current != '\0')
{
    *current = '\0';
    current++;
}
```

### Retour du token trouvé
```c
return (token);
```

---

## 3. `stock_args`
```c
char **stock_args(char *line)
```
- **But :** Crée un tableau dynamique `argv` à partir d'une ligne de commande.
- **Rôle clé :** C'est ici qu'on transforme la ligne de texte (ex : `ls -l /tmp`) en un tableau `argv = {"ls", "-l", "/tmp", NULL}`.

### 1. Compter les tokens
```c
cp_line = _strdup(line);
token = _strtok(cp_line, " ");
while (token != NULL)
{
    count++;
    token = _strtok(NULL, " ");
}
free(cp_line);
```
- On duplique la ligne pour ne pas modifier l'originale.
- On compte combien de tokens on a (pour savoir combien allouer).

### 2. Allocation de `argv`
```c
argv = malloc(sizeof(char *) * (count + 1));
```
- On alloue de quoi stocker tous les tokens **+1** pour le `NULL` final.

### 3. Deuxième passage pour remplir `argv`
```c
cp_line2 = _strdup(line);
token = _strtok(cp_line2, " ");
```
- On re-tokenise une nouvelle copie pour cette fois remplir le tableau `argv`.

### 4. Remplissage et duplication des tokens
```c
argv[i] = _strdup(token);
```
- Chaque token est dupliqué pour éviter dépendre de `cp_line2`.

### 5. Gestion d'erreur si une allocation échoue
```c
if (argv[i] == NULL)
{
    while (--i >= 0)
        free(argv[i]);
    free(argv);
    free(cp_line2);
    return (NULL);
}
```
- Bonne gestion mémoire en cas de `malloc` raté.

### 6. Fin de tableau
```c
argv[i] = NULL;
```
- Fin du tableau `argv`.

### 7. Nettoyage
```c
free(cp_line2);
```

### 8. Retour du tableau d'arguments
```c
return (argv);
```

---

## Conclusion
Le fichier `parser.c` est essentiel car il découpe les lignes de commande entrées par l'utilisateur en morceaux exploitables par `execve`. Son code est optimisé pour être robuste, dynamique, et économe en mémoire.
```

