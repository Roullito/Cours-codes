# Commandes Shell

## 📌 Introduction
Ce document répertorie les principales commandes Shell utilisées sous Linux, classées par catégories avec leurs options et des exemples.

---

## 🔹 1. Navigation dans le système de fichiers
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `pwd` | Affiche le répertoire actuel | Aucun | `pwd` |
| `ls` | Liste les fichiers d'un répertoire | `-l` pour le format détaillé, `-a` pour afficher les fichiers cachés | `ls -la` |
| `cd` | Change de répertoire | `..` pour monter d'un niveau | `cd /home/user` |
| `mkdir` | Crée un nouveau dossier | Aucun | `mkdir mon_dossier` |
| `rmdir` | Supprime un dossier vide | Aucun | `rmdir mon_dossier` |
| `rm -r` | Supprime un dossier et son contenu | `-f` pour forcer la suppression | `rm -rf mon_dossier` |

---

## 🔹 2. Gestion des fichiers
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `touch` | Crée un fichier vide | Aucun | `touch fichier.txt` |
| `cp` | Copie un fichier ou un dossier | `-r` pour copier un dossier | `cp fichier.txt copie.txt` |
| `mv` | Déplace ou renomme un fichier | Aucun | `mv fichier.txt nouveau_nom.txt` |
| `rm` | Supprime un fichier | `-f` pour forcer, `-r` pour récursif | `rm fichier.txt` |
| `find` | Recherche des fichiers selon des critères | `-name` pour filtrer par nom | `find /home -name fichier.txt` |

---

## 🔹 3. Manipulation de texte
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `cat` | Affiche le contenu d'un fichier | Aucun | `cat fichier.txt` |
| `head` | Affiche les premières lignes d'un fichier | `-n` pour spécifier le nombre de lignes | `head -5 fichier.txt` |
| `tail` | Affiche les dernières lignes d'un fichier | `-f` pour suivre un fichier en temps réel | `tail -f fichier.log` |
| `grep` | Recherche un motif dans un fichier | `-i` pour ignorer la casse, `-r` pour récursif | `grep -i erreur fichier.log` |
| `awk` | Manipule des colonnes de texte | Aucun | `awk '{print $1}' fichier.txt` |
| `sed` | Modifie du texte en ligne | Aucun | `sed 's/ancien/nouveau/g' fichier.txt` |

---

## 🔹 4. Gestion des processus
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `ps` | Affiche les processus en cours | `aux` pour voir tous les processus | `ps aux` |
| `top` | Affiche les processus actifs en temps réel | Aucun | `top` |
| `kill` | Termine un processus | `-9` pour forcer l'arrêt | `kill 1234` |
| `pkill` | Termine un processus par nom | Aucun | `pkill firefox` |
| `bg` | Remet un processus en arrière-plan | Aucun | `bg %1` |
| `fg` | Remet un processus en avant-plan | Aucun | `fg %1` |

---

## 🔹 5. Gestion des utilisateurs et permissions
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `whoami` | Affiche l'utilisateur actuel | Aucun | `whoami` |
| `id` | Affiche les informations de l'utilisateur | Aucun | `id user` |
| `chmod` | Change les permissions d'un fichier | `+x` pour ajouter l'exécution | `chmod +x script.sh` |
| `chown` | Change le propriétaire d'un fichier | `user:group` pour définir propriétaire et groupe | `chown user:group fichier.txt` |
| `su` | Change d'utilisateur | Aucun | `su root` |
| `sudo` | Exécute une commande en tant que super-utilisateur | Aucun | `sudo apt update` |

---

## 🔹 6. Réseau et Connexions
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `ping` | Vérifie la connectivité vers une adresse IP | `-c` pour limiter le nombre de paquets | `ping -c 4 google.com` |
| `wget` | Télécharge un fichier depuis une URL | Aucun | `wget https://example.com/fichier.zip` |
| `curl` | Effectue des requêtes HTTP | Aucun | `curl -I https://example.com` |
| `netstat` | Affiche les connexions réseau | `-tulnp` pour voir les ports écoutés | `netstat -tulnp` |
| `ss` | Affiche les sockets réseau actives | `-l` pour voir les ports ouverts | `ss -l` |

---

## 📌 Conclusion
Ce document regroupe les commandes Shell les plus utilisées. Pour plus de détails, utilisez `man <commande>` dans un terminal Linux/macOS.

