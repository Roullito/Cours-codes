# Shell - Fichiers d'Initialisation, Variables et Expansions

## 📌 Introduction
Ce document répertorie les principales commandes et concepts liés aux fichiers d'initialisation, aux variables et aux expansions dans un shell Linux.

---

## 🔹 1. Fichiers d'Initialisation du Shell
| Fichier | Description | Chargé par | Exemples |
|---------|------------|------------|----------|
| `~/.bashrc` | Script de démarrage du shell interactif non connecté | Bash | `source ~/.bashrc` |
| `~/.bash_profile` | Script exécuté lors de la connexion d’un utilisateur | Bash | `source ~/.bash_profile` |
| `~/.profile` | Script de configuration pour l'environnement | Bourne, Bash, Dash | `source ~/.profile` |
| `/etc/profile` | Script de configuration global du système | Tous les utilisateurs | `source /etc/profile` |
| `/etc/bash.bashrc` | Script global exécuté pour chaque shell interactif | Bash | `source /etc/bash.bashrc` |

---

## 🔹 2. Variables d'Environnement
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `export VAR=valeur` | Définit une variable d’environnement | Aucun | `export PATH=$PATH:/opt/bin` |
| `echo $VAR` | Affiche la valeur d’une variable | Aucun | `echo $HOME` |
| `unset VAR` | Supprime une variable d’environnement | Aucun | `unset JAVA_HOME` |
| `env` | Affiche les variables d’environnement | Aucun | `env | grep USER` |
| `set` | Affiche toutes les variables et fonctions shell | Aucun | `set | grep SHELL` |

---

## 🔹 3. Expansions Shell
| Type | Description | Syntaxe | Exemples |
|------|------------|---------|----------|
| Expansion de variable | Remplace une variable par sa valeur | `$VAR` | `echo $HOME` |
| Substitution de commande | Exécute une commande et remplace la sortie | `` `cmd` `` ou `$(cmd)` | `echo $(date)` |
| Expansion d’accolades | Génère plusieurs valeurs | `{val1,val2,...}` | `echo {a,b,c}` |
| Expansion d’astérisque | Correspond à tous les fichiers d’un dossier | `*` | `ls *.txt` |
| Expansion numérique | Génère une séquence de nombres | `{start..end[..step]}` | `echo {1..10..2}` |

---

## 🔹 4. Gestion des Alias
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `alias` | Définit un alias temporaire | Aucun | `alias ll='ls -la'` |
| `unalias` | Supprime un alias | Aucun | `unalias ll` |
| `alias -p` | Affiche la liste des alias | Aucun | `alias -p` |

---

## 📌 Conclusion
Ce document regroupe les principales commandes et concepts liés aux fichiers d'initialisation, aux variables et aux expansions du Shell. Pour plus de détails, utilisez `man <commande>` dans un terminal Linux/macOS.

