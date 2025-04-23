# Décomposition ligne par ligne de `find.c`

Ce fichier a pour but de retrouver le chemin absolu d'une commande passée par l'utilisateur en utilisant la variable d'environnement `PATH`.

---

## Fonction : `_which`
```c
char *_which(char *filename, char **env)
```
### But :
Cherche si la commande `filename` est un chemin direct vers un exécutable ou bien la localise en la recherchant dans les répertoires contenus dans `PATH`.

### Détail ligne par ligne :
```c
char *env_path, *found_path;
```
- Déclaration de deux pointeurs de chaîne :
  - `env_path` stockera une copie de `PATH` sans "PATH=".
  - `found_path` contiendra le chemin final si la commande est trouvée.

```c
if (filename == NULL || env == NULL)
    return (NULL);
```
- Si aucun nom de fichier ou d'environnement fourni, on retourne `NULL`.

```c
if (_check_direct_path(filename))
    return (_strdup(filename));
```
- Si `filename` est déjà un chemin direct valide (ex: `/bin/ls`), on retourne une copie de ce chemin.

```c
env_path = _extract_path(env);
```
- Récupère le contenu de `PATH` sans le préfixe `PATH=` (via fonction définie plus bas).

```c
if (!env_path)
    return (NULL);
```
- Si l'extraction a échoué, on retourne `NULL`.

```c
found_path = _search_in_path(env_path, filename);
free(env_path);
```
- Recherche dans chaque dossier de `PATH` si la commande y est présente.
- Libère `env_path` (plus utile).

```c
return (found_path);
```
- Retourne le chemin trouvé ou `NULL` si rien n'a été trouvé.

---

## Fonction : `_check_direct_path`
```c
int _check_direct_path(char *filename)
```
### But :
Vérifie si `filename` est un chemin absolu ou relatif vers un exécutable.

### Détail :
```c
if (filename[0] == '/' || filename[0] == '.')
```
- Si le nom commence par `/` (absolu) ou `.` (relatif).

```c
if (access(filename, X_OK) == 0)
    return (1);
```
- On teste s'il est exécutable.

```c
return (0);
```
- Sinon, ce n'est pas un chemin valide.

---

## Fonction : `_extract_path`
```c
char *_extract_path(char **env)
```
### But :
Extraire le contenu de `PATH` des variables d'environnement.

### Détail :
```c
for (i = 0; env[i]; i++)
```
- Parcours ligne par ligne le tableau `env`.

```c
if (_strncmp(env[i], "PATH=", 5) == 0)
```
- Si une ligne commence par "PATH="...

```c
copy = _strdup(env[i]);
```
- Copie de la ligne entière (précaution pour ne pas altérer `env`).

```c
_strtok(copy, "=");
result = _strdup(_strtok(NULL, ""));
```
- On isole la valeur de PATH (après le `=`).

```c
free(copy);
return (result);
```
- Libère `copy` et retourne une nouvelle chaîne contenant uniquement le contenu de `PATH`.

---

## Fonction : `_search_in_path`
```c
char *_search_in_path(char *path_str, char *filename)
```
### But :
Parcourt chaque dossier de `PATH` pour y chercher la commande.

### Détail :
```c
path_copy = _strdup(path_str);
```
- On travaille sur une copie de `PATH`.

```c
file_len = strlen(filename);
dir = _strtok(path_copy, ":");
```
- On récupère le premier dossier de `PATH`.

```c
while (dir != NULL)
```
- Boucle sur tous les dossiers de `PATH`.

```c
path_len = strlen(dir);
full_path = malloc(path_len + file_len + 2);
```
- Alloue une chaîne pour stocker "dossier/commande" + `\0`.

```c
_strcpy(full_path, dir);
full_path[path_len] = '/';
_strcpy(full_path + path_len + 1, filename);
full_path[path_len + file_len + 1] = '\0';
```
- Construit le chemin complet.

```c
if (access(full_path, X_OK) == 0)
```
- Si ce fichier est exécutable, on le retourne.

```c
free(full_path);
dir = _strtok(NULL, ":");
```
- Sinon, on libère et on teste le prochain dossier.

```c
free(path_copy);
return (NULL);
```
- Aucun chemin valide trouvé : on libère la copie et retourne `NULL`.

---

Prêt à passer au prochain fichier quand tu veux !

