# input.c - Explication détaillée en français

## Fonction : `_getline`
Cette fonction lit une ligne depuis un descripteur de fichier (par défaut, `stdin`) caractère par caractère, et la stocke dans un buffer dynamique, réalloué si besoin.

```c
ssize_t _getline(char **lineptr, size_t *n, int fd)
```

### Paramètres :
- `char **lineptr` : Pointeur vers le buffer de la ligne à remplir. Ce buffer sera alloué ou réalloué par la fonction.
- `size_t *n` : Pointeur vers la taille actuelle du buffer.
- `int fd` : Descripteur de fichier d'où lire (par exemple, 0 pour `stdin`).

### Retour :
- Nombre de caractères lus (hors `\0`).
- `-1` si erreur ou fin de fichier sans lecture.

---

### Ligne 1 :
```c
size_t i = 0, new_size;
```
Initialise un compteur `i` pour suivre le nombre de caractères lus. `new_size` servira en cas de réallocation du buffer.

### Ligne 2 :
```c
char *temp, c;
```
`temp` sera utilisé pour la réallocation, `c` contient chaque caractère lu.

### Ligne 3 :
```c
ssize_t r;
```
`r` contient le retour de `read()` pour vérifier succès ou erreur.

---

### Vérification des pointeurs :
```c
if (lineptr == NULL || n == NULL)
    return (-1);
```
Sécurité : si les pointeurs sont invalides, on ne peut pas continuer.

---

### Allocation initiale :
```c
if (*lineptr == NULL || *n == 0)
```
Si aucun buffer n'est fourni, ou s'il est vide :
```c
*n = 128;
*lineptr = malloc(*n);
```
On alloue un buffer de 128 caractères. Si échec :
```c
if (*lineptr == NULL)
    return (-1);
```

---

### Lecture boucle infinie :
```c
while (1)
```
On lit caractère par caractère dans une boucle infinie jusqu'à `\n` ou `EOF`.

---

### Lecture d’un caractère :
```c
r = read(fd, &c, 1);
```
Lecture d’un seul caractère dans `c`.
- Si `r < 0` : erreur, on retourne `-1`
- Si `r == 0` (EOF) :
```c
return (i > 0 ? (ssize_t)i : -1);
```
Retourne le nombre de caractères lus ou `-1` si aucun.

---

### Réallocation si nécessaire :
```c
if (i >= (*n - 1))
```
Si le buffer est plein, on double sa taille :
```c
new_size = ((*n) * 2);
temp = malloc(new_size);
```
Si allocation échoue, on retourne `-1`.

Sinon, on copie les données, libère l’ancien buffer, et met à jour :
```c
_memcpy(temp, *lineptr, *n);
free(*lineptr);
*lineptr = temp;
*n = new_size;
```

---

### Ajout du caractère lu :
```c
(*lineptr)[i++] = c;
```

### Fin de ligne ?
```c
if (c == '\n')
    break;
```
On sort si on atteint la fin de ligne.

---

### Null-termination et retour :
```c
(*lineptr)[i] = '\0';
return (i);
```
On termine la chaîne et on retourne la longueur lue.

---

## Résumé :
Cette fonction personnalise `getline()` pour une implémentation maison : 
- Elle lit caractère par caractère,
- Gère dynamiquement la mémoire,
- Gère EOF intelligemment,
- Peut être utilisée comme base pour un shell.

Très utile dans le contexte de votre shell Holberton pour interagir ligne par ligne avec l’utilisateur.

