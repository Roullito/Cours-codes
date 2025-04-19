### File: `get_input_line.c`

```c
ssize_t get_input_line(char **line, size_t *len)
{
    ssize_t r = _getline(line, len, 0);

    if (r == -1)
    {
        if (is_interactive())
            write(1, "\n", 1);
        return (-1);
    }

    if (r > 0 && (*line)[r - 1] == '\n')
        (*line)[r - 1] = '\0';

    return (r);
}
```

---

### 🔍 Détail ligne par ligne

#### `ssize_t r = _getline(line, len, 0);`
- Appel de notre fonction personnalisée `_getline()`.
- Elle lit l'entrée utilisateur depuis l'entrée standard (fd = 0),
- Elle stocke la ligne dans le pointeur `*line`, et gère automatiquement la taille via `*len`.
- Elle retourne le **nombre de caractères lus** (incluant potentiellement le `\n`).

#### `if (r == -1)`
- Si `_getline` retourne -1, cela signifie une **erreur** ou que l'utilisateur a fait `Ctrl+D` (EOF).

#### `if (is_interactive()) write(1, "\n", 1);`
- Si l'utilisateur est dans un shell interactif, on affiche un `\n` pour que le prompt Linux suivant n'apparaisse pas à côté du dernier caractère.

#### `if (r > 0 && (*line)[r - 1] == '\n')`
- On supprime le `\n` final de la commande.
- Cela évite que `argv[0]` soit par exemple "ls\n", ce qui rendrait l'exécution impossible.

#### `return (r);`
- On retourne le nombre de caractères lus (utile si on veut détecter une ligne vide dans la shell loop).

---

### 🔁 Lien avec le reste du code
- Appelé dans `shell_loop()` pour chaque nouvelle commande utilisateur.
- Permet une intégration transparente avec `stock_args(line)` ensuite.
- Évite d'utiliser la version standard `getline()` pour un meilleur contrôle et apprentissage interne.

---

Souhaites-tu que je crée un fichier à part pour `is_interactive()` maintenant ?

