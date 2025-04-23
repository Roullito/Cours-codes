# Explication complète de `executor.c`

Ce fichier gère la création d'un processus enfant pour exécuter une commande externe via `execve`. C'est un élément central du fonctionnement d'un shell, qui permet de lancer les commandes saisies par l'utilisateur.

---

## 🔧 Fonction `execute`

```c
int execute(char **argv, char **env)
```

- `argv` : tableau de chaînes contenant la commande et ses arguments.
- `env` : tableau contenant les variables d'environnement.
- Retourne : le code de sortie de la commande ou `127` si `execve` échoue.

---

### Détail ligne par ligne

```c
if (argv == NULL || argv[0] == NULL || env == NULL)
	return (1);
```
- Vérifie que les arguments et l'environnement sont valides.

```c
pid = fork();
```
- Crée un nouveau processus. Si ça échoue, on retourne une erreur.

```c
if (pid == -1)
```
- Échec de `fork`, on affiche une erreur et retourne `1`.

```c
if (pid == 0)
```
- Code exécuté dans le **processus enfant**.

```c
if (execve(argv[0], argv, env) == -1)
```
- Tente d'exécuter la commande. Si ça échoue, on affiche une erreur et quitte le processus avec `127`.

```c
else
```
- Partie **parent** du processus : attend que le fils termine avec `waitpid`.

```c
if (waitpid(pid, &status, 0) == -1)
```
- Vérifie que l'attente s'est bien passée. Sinon, affiche une erreur.

```c
if(WIFEXITED(status))
	return (WEXITSTATUS(status));
```
- Si l'enfant s'est terminé normalement, on retourne son code de sortie (avec `WEXITSTATUS`).

```c
return (1);
```
- Si l'enfant ne s'est pas terminé normalement, on retourne une erreur générique `1`.

---

## 🧰 Schéma mémoire (simplifié)

```text
Processus parent
|
|-- fork()
|
|---> Processus enfant
      |
      |-- execve("/bin/ls", {"ls", NULL}, env)
      |     -> Remplace totalement le code de l'enfant par celui de la commande
```

---

## ✨ Intégration dans le shell
- Cette fonction est appelée **après avoir vérifié que la commande n'est pas un builtin**.
- Elle permet au shell de rester actif pendant que la commande s'exécute dans un processus séparé.

---

Si `execve` échoue, on retourne `127`, comme spécifié dans le projet.
Sinon, on relaie simplement le code de retour du processus enfant vers le shell parent.

