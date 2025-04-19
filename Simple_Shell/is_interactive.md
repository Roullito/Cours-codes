## Fichier : `is_interactive.c`

### Fonction : `is_interactive`
```c
int is_interactive(void)
{
    return (isatty(STDIN_FILENO));
}
```

---

### ❌ Description :
Cette fonction est très courte mais **extrêmement importante** dans la logique du shell. Elle permet de savoir **dans quel mode le shell s'exécute** :

- Mode **interactif** : quand l'utilisateur utilise directement le terminal (lancement manuel du shell)
- Mode **non-interactif** : quand une commande est pipée ou que l'entrée est redirigée (ex: `echo "ls" | ./simple_shell`)

---

### ✅ Détail ligne par ligne :

```c
return (isatty(STDIN_FILENO));
```
- `isatty(int fd)` est une fonction standard de `<unistd.h>`.
- Elle retourne `1` si le descripteur de fichier `fd` correspond à un terminal (TTY), `0` sinon.
- `STDIN_FILENO` est une constante prédéfinie égal à `0`, représentant l'entrée standard (le clavier en mode interactif).
- Donc :
  - Si l'utilisateur tape directement dans le terminal, `isatty(0)` retourne `1` → shell interactif.
  - Si le shell lit via une redirection ou un pipe, `isatty(0)` retourne `0` → shell non interactif.

---

### 🔗 Comment elle s'intègre au reste du shell ?
- Dans `shell_loop()` :
```c
if (is_interactive())
    write(1, "$ ", 2);
```
- Cela permet **d'afficher le prompt `$` uniquement si on est en mode interactif**.
- En mode non-interactif (scripts ou redirections), le prompt n'est pas affiché pour ne pas polluer la sortie.

---

### 🌐 Exemple :
#### Mode interactif
```bash
$ ./simple_shell
$ ls
```
- `isatty(0)` retourne `1`, on affiche le prompt `$ `.

#### Mode non-interactif
```bash
echo "ls" | ./simple_shell
```
- `isatty(0)` retourne `0`, pas de prompt.

---

### 🔹 Pourquoi ce test est indispensable ?
- Il rend le shell **comportementalement correct** selon son mode d'utilisation.
- Il évite des sorties inutiles dans un script automatisé.
- Il permet également à certaines fonctions comme `get_input_line()` d'afficher un saut de ligne à la fin **seulement si besoin**.
