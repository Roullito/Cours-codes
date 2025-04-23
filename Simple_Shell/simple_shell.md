# Projet Simple Shell - Fonctionnement global et architecture

Ce document propose une explication **générale et pédagogique** du fonctionnement complet du projet **Simple Shell**. Nous allons voir **comment les différents fichiers s'articulent ensemble**, le parcours d'une commande, les interactions entre les fonctions, ainsi que la gestion mémoire et les cas d'erreurs.

---

## 1. Vue d'ensemble du fonctionnement

Voici un schéma simplifié du cycle de vie d'une commande dans le shell :

```text
                      +--------------------------+
                      |         main()           |
                      +--------------------------+
                                  |
                                  v
                      +--------------------------+
                      |       shell_loop()       |
                      +--------------------------+
                                  |
                                  v
                      +--------------------------+
                      |     get_input_line()     | <--- Lecture de l'utilisateur
                      +--------------------------+
                                  |
                                  v
                      +--------------------------+
                      |       stock_args()       | <--- Tokenisation (parser)
                      +--------------------------+
                                  |
                                  v
              +-------------------------------------+
              | handle_builtin_or_execute()         |
              +-------------------------------------+
               |         |                        |
               |         |                        |
               v         v                        v
     builtin_env()   builtin_exit()          _which() (find.c)
                                           + execute() (executor.c)
```

---

## 2. main.c : point d'entrée et boucle principale

- `main()` appelle `shell_loop()`.
- `shell_loop()` lit les lignes entrées par l'utilisateur (avec `_getline()`), les découpe (avec `stock_args()`) puis appelle `handle_builtin_or_execute()`.
- Il s'agit de la **boucle infinie du shell** qui gère l'interaction avec l'utilisateur.

---

## 3. parser.c : tokenisation de la ligne

- `stock_args()` reçoit la ligne complète et utilise `_strtok()` pour extraire chaque mot.
- Retourne un tableau **argv** de type `char **` contenant chaque mot comme une commande ou un argument.

---

## 4. builtin.c : commandes internes

- `handle_builtin()` teste si la commande est `exit` ou `env`. Si oui, il les exécute avec :
  - `builtin_exit()` : libère la mémoire, appelle `exit()`.
  - `builtin_env()` : imprime l'environnement avec `printf()`.
- Si la commande **n'est pas un builtin**, on retourne la main au shell pour la chercher dans le `PATH`.

---

## 5. find.c : recherche de commande

- `_which()` cherche si la commande existe dans le `PATH`.
  - `_check_direct_path()` vérifie si c'est déjà un chemin valide (comme `./a.out`).
  - `_extract_path()` extrait la valeur de `PATH=` dans l'environnement.
  - `_search_in_path()` teste chaque dossier dans `PATH` pour voir si la commande s'y trouve.

---

## 6. executor.c : exécution de commande externe

- `execute()` crée un processus enfant avec `fork()`.
- Le fils appelle `execve()` avec le chemin trouvé et les arguments.
- Le parent attend la fin du fils avec `waitpid()`.
- On retourne le `WEXITSTATUS()` pour connaître le code retour de la commande.

---

## 7. input.c : lecture de la ligne utilisateur

- `_getline()` lit caractère par caractère.
- Alloue dynamiquement un buffer qu'elle redimensionne si besoin.
- Retourne une chaîne contenant la ligne terminée par `\n`.

---

## 8. free.c : gestion de la mémoire

- `free_argv()` libère tout le `argv` alloué dynamiquement par `stock_args()`.
- Appelé systématiquement après chaque commande.

---

## 9. Exemple complet d'exécution :

### Input utilisateur :
```bash
ls -l /tmp
```

### Traitement :
- `main()` appelle `shell_loop()`.
- `shell_loop()` affiche le prompt.
- `_getline()` lit "ls -l /tmp".
- `stock_args()` retourne argv = `["ls", "-l", "/tmp"]`.
- `handle_builtin_or_execute()` ne détecte pas de builtin.
- `_which()` retourne `/bin/ls`.
- `execute()` lance le processus enfant avec `/bin/ls`.
- Le parent attend et récupère le code retour.

---

## 10. Particularités importantes

- Si une commande est invalide :
  - Le shell ne fork PAS (conformément à l'exercice `0.3`).
- Si `exit 98` est tapé :
  - `_is_number()` valide "98", le shell quitte avec `exit(98)`.
- Si `exit qwe` est tapé :
  - Affiche une erreur : `Illegal number`, quitte avec `exit(2)`.

---

## Conclusion

Ce shell respecte les grandes étapes d'un shell Unix :
1. Lire une ligne.
2. Découper en arguments.
3. Vérifier les built-ins.
4. Chercher dans le PATH.
5. Fork + execve.
6. Attendre la fin.
7. Libérer la mémoire.

Le code est organisé, propre et chaque fichier a une responsabilité claire. À présent, tu peux naviguer ligne par ligne grâce aux fichiers `.md` pour chaque .c que tu m'as transmis !

