# Documentation ultra détaillée : _strtok.c

---

## Fonction : `_strtok`

```c
char *_strtok(char *str, const char *delim)
{
    static char *next;
    char *token;

    if (str != NULL)
        next = str;

    if (next == NULL || *next == '\0')
        return (NULL);

    while (*next && _strchr(delim, *next))
        next++;

    if (*next == '\0')
        return (NULL);

    token = next;

    while (*next && !_strchr(delim, *next))
        next++;

    if (*next)
    {
        *next = '\0';
        next++;
    }

    return (token);
}
```

---

## 🔍 Explication ligne par ligne

### `static char *next;`
- Permet de garder la position entre deux appels successifs.
- Comme `strtok` standard, on ne passe la chaîne qu'une seule fois, puis on rappelle avec `NULL`.

### `if (str != NULL) next = str;`
- Si on passe une nouvelle chaîne, on initialise `next` avec cette chaîne.
- Sinon, on continue à partir de la position stockée.

### `if (next == NULL || *next == '\0') return (NULL);`
- Si `next` est vide ou NULL, on a terminé le parsing → on retourne `NULL`.

---

### `while (*next && _strchr(delim, *next)) next++;`
- On saute les délimiteurs (ex: espaces) au début.
- `_strchr(delim, *next)` : vérifie si le caractère courant fait partie des séparateurs.

### `if (*next == '\0') return (NULL);`
- Si on a atteint la fin de la chaîne après avoir sauté les délimiteurs, rien à tokeniser.

---

### `token = next;`
- On stocke le début du token courant.

### `while (*next && !_strchr(delim, *next)) next++;`
- On avance jusqu'au prochain délimiteur (fin du mot).

### `if (*next)`
- Si on n'est pas à la fin de la chaîne :
    - On coupe le mot en mettant `\0` à la fin.
    - On avance `next` pour le prochain appel.

---

## 🔎 Rôle de `_strtok` dans le projet
- Utilisé dans `stock_args()` (dans `parser.c`) pour splitter l'input utilisateur (ligne) en plusieurs arguments.
- Simule `strtok()` standard mais sans ses effets secondaires éventuels.

### Exemple :
Entrée : `ls -l /home`

1. Premier appel : `_strtok("ls -l /home", " ")` → `"ls"`
2. Deuxième appel : `_strtok(NULL, " ")` → `"-l"`
3. Troisième appel : `_strtok(NULL, " ")` → `"/home"`
4. Quatrième appel : `NULL`

---

Souhaites-tu que je continue avec `_strchr` ou une autre fonction de la liste de ton projet ?

