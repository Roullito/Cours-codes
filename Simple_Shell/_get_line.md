# Documentation de la fonction _getline

---

## 🔷 Nom du fichier : `_getline.c`

```c
ssize_t _getline(char **lineptr, size_t *n, int fd)
```

---

## 🧠 But de la fonction :
Simuler le comportement de la fonction standard POSIX `getline()`, mais sans l’utiliser directement.
Permet de lire dynamiquement une ligne complète depuis un descripteur de fichier (souvent `STDIN_FILENO`, i.e. l'entrée standard).

Utilisée dans notre shell pour capter l'entrée utilisateur, même dans le cadre de redirections (`echo "ls" | ./simple_shell`).

---

## 🔢 Prototype
```c
ssize_t _getline(char **lineptr, size_t *n, int fd);
```
- `lineptr` : adresse d’un pointeur où la ligne lue sera stockée
- `n` : taille allouée du buffer pointé par `lineptr`
- `fd` : descripteur du fichier (souvent `0` pour `stdin`)

Retourne :
- Le **nombre de caractères lus** (incluant le `\n`, mais excluant le `\0`)
- `-1` en cas d’erreur ou d’EOF sans lecture

---

## 🧩 Étapes principales de la fonction

### 1. 🔒 Validation des paramètres
```c
if (lineptr == NULL || n == NULL || fd < 0)
    return (-1);
```
- On vérifie que les pointeurs sont valides et que le descripteur est acceptable.

---

### 2. 🆕 Allocation initiale (si nécessaire)
```c
if (*lineptr == NULL || *n == 0)
{
    *n = 128;
    *lineptr = malloc(*n);
    if (*lineptr == NULL)
        return (-1);
}
```
- Si aucun buffer n’est encore alloué, on en réserve un de 128 octets.
- Cette taille s’adaptera dynamiquement si besoin (voir étape 4).

---

### 3. 🔄 Lecture caractère par caractère
```c
while ((r = read(fd, &c, 1)) == 1)
{
    (*lineptr)[i++] = c;
```
- On lit **un caractère à la fois**.
- Chaque caractère est ajouté au buffer alloué.

---

### 4. 🔁 Réallocation dynamique si le buffer est plein
```c
if (i >= *n)
{
    *n *= 2;
    *lineptr = realloc(*lineptr, *n);
    if (*lineptr == NULL)
        return (-1);
}
```
- Si le buffer est trop petit, on le double (`*n *= 2`).
- `realloc()` ajuste la taille.

---

### 5. 🔚 Fin de ligne
```c
if (c == '\n')
    break;
```
- Si on lit un `\n`, on sort de la boucle (la ligne est complète).

---

### 6. 📌 Fin de fichier (EOF) ou erreur
```c
if (r == -1 || (r == 0 && i == 0))
    return (-1);
```
- `r == 0` signifie fin de fichier.
- Si aucun caractère n’a été lu avant EOF, on retourne `-1`.

---

### 7. 🔚 Terminaison de la chaîne
```c
(*lineptr)[i] = '\0';
```
- On termine manuellement la chaîne avec `\0` pour en faire une chaîne C classique.

---

### 8. 🔁 Retour du nombre de caractères lus
```c
return (i);
```
- On renvoie le nombre total de caractères (sans le `\0`, mais avec `\n` si présent).

---

## ✅ Résumé visuel :

```c
STDIN:         l    s     \n
               ↓     ↓      ↓
_buffer:      [l]  [s]   [\n] [\0]
```

---

Souhaites-tu que je génère maintenant la documentation détaillée de `_strtok()` ?

