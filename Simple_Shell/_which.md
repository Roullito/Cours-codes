# Documentation : _which.c

## Fonction : `_which`

```c
char *_which(char *filename, char **env)
```

### 🔍 Rôle principal
Trouver le **chemin complet d'une commande** (par exemple, convertir `ls` en `/bin/ls`) en utilisant la variable d'environnement `PATH`.

Si l'utilisateur tape un chemin absolu (ex : `/bin/ls`), la fonction le retourne directement s'il existe. Sinon, elle parcourt tous les répertoires définis dans `PATH` pour y chercher la commande.

---

## Détail ligne par ligne

```c
if (filename[0] == '/') {
    if (stat(filename, &st) == 0)
        return (_strdup(filename));
    else
        return (NULL);
}
```
- **filename[0] == '/'** : Si le chemin est absolu.
- **stat(filename, &st)** : Vérifie si le fichier existe et est accessible.
- **_strdup(filename)** : Si le fichier existe, on renvoie une copie de la chaîne (jamais l'original).

---

```c
for (i = 0; env[i]; i++) {
    if (_strncmp(env[i], "PATH=", 5) == 0) {
        env_path = _strdup(env[i]);
```
- On cherche dans `env[]` la variable qui commence par `PATH=`.
- `_strdup` pour faire une copie de la ligne pour ne pas modifier `environ`.

```c
_strtok(env_path, "=");
path = _strtok(NULL, ":");
```
- On sépare "PATH=/bin:/usr/bin" en "PATH" puis on récupère `/bin`, `/usr/bin`, etc.

---

### 🔁 Boucle sur les chemins

```c
while (path != NULL) {
    full_path = malloc(1024);
    sprintf(full_path, "%s/%s", path, filename);
    if (stat(full_path, &st) == 0) {
        free(env_path);
        return (full_path);
    }
    free(full_path);
    path = _strtok(NULL, ":");
}
```
- On génère un chemin complet pour chaque répertoire : `/bin/ls`, `/usr/bin/ls`, etc.
- On teste l'existence avec `stat()`.
- Si trouvé, on renvoie la copie de ce chemin.
- Sinon, on continue à tester les autres chemins.

---

```c
free(env_path);
return (NULL);
```
- Si aucun chemin n’est valide, on libère la mémoire et retourne NULL.

---

## ✅ Ce que la fonction retourne
- **Un pointeur vers une nouvelle chaîne allouée dynamiquement** contenant le chemin complet de la commande.
- NULL si la commande n'existe pas.

## ⚠️ Points importants
- Ne jamais retourner un pointeur vers une variable statique ou vers un champ de `environ[i]`, toujours dupliqué !
- La mémoire allouée par `_which` doit être **free()** après utilisation.

---

## Exemple d'utilisation

```c
char *cmd = _which("ls", environ);
if (cmd)
{
    printf("Path trouvé : %s\n", cmd);
    free(cmd);
}
else
    printf("Commande introuvable\n");
```

---

Souhaites-tu qu'on documente ensuite la fonction `execute()` ?

