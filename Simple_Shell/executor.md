# Fichier : `execute.c`

## Fonction : `void execute(char **argv, char **env)`

---

### Prototype :
```c
void execute(char **argv, char **env);
```

### Objectif :
Cette fonction crée un processus fils avec `fork()` pour exécuter une commande extérieure (non builtin) en utilisant `execve()`. Le parent attend que l'enfant termine avec `wait()`.

---

### Détail ligne par ligne :

```c
pid_t pid;
int status;
```
- `pid` : stocke le résultat du `fork()`. Permet de savoir si on est dans le processus parent ou enfant.
- `status` : sera utilisé par `wait()` pour récupérer le statut de retour de l'enfant.

---

```c
pid = fork();
```
- Crée un **nouveau processus**.
  - Si `pid < 0` ⇒ Erreur dans `fork()`.
  - Si `pid == 0` ⇒ On est dans le processus **enfant**.
  - Si `pid > 0` ⇒ On est dans le **parent**.

---

```c
if (pid == -1)
{
    perror("fork");
    return;
}
```
- Si `fork()` échoue, on affiche l'erreur avec `perror()` et on quitte la fonction.

---

```c
if (pid == 0)
{
    if (execve(argv[0], argv, env) == -1)
    {
        perror("execve");
        exit(EXIT_FAILURE);
    }
}
```
- **Dans le processus enfant** :
  - On tente d'exécuter la commande avec `execve()`.
    - `argv[0]` : chemin de la commande à exécuter (absolu ou déduit via `_which()`).
    - `argv` : tableau des arguments.
    - `env` : tableau des variables d'environnement.
  - Si `execve()` échoue, on affiche l'erreur, puis on **quitte l'enfant** avec `exit()` (important pour ne pas exécuter la suite du code parent).

---

```c
else
{
    wait(&status);
}
```
- **Dans le processus parent** :
  - `wait()` permet d'attendre la fin de l'exécution de l'enfant.
  - Le statut est stocké dans `status` (inutile ici, mais bon pour des futurs améliorations).

---

## Récapitulatif du fonctionnement :

1. Le shell lit une commande.
2. Si ce n'est pas un builtin, on passe à `execute(argv, env)`.
3. `fork()` crée un processus fils.
4. Le fils appelle `execve()` pour lancer la commande.
5. Le parent attend que l'enfant se termine.

---

## Pourquoi utiliser `fork()` et `execve()` ?
- `fork()` crée une copie du processus courant pour isoler l'exécution.
- `execve()` remplace l'image mémoire du fils par celle de la commande, sans affecter le shell principal.
- Cela garantit que même si la commande échoue ou se termine, notre shell continue de tourner.

