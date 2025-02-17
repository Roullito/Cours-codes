# Shell - Redirections d'Entrée/Sortie et Filtres

## 📌 Introduction
Ce document répertorie les principales commandes et concepts liés aux redirections d'entrée/sortie (IO) et aux filtres dans un shell Linux.

---

## 🔹 1. Redirections d'Entrée/Sortie (IO)
| Commande | Description | Syntaxe | Exemples |
|----------|------------|---------|----------|
| `>` | Redirige la sortie vers un fichier (écrase) | `commande > fichier` | `ls > liste.txt` |
| `>>` | Redirige la sortie vers un fichier (ajoute) | `commande >> fichier` | `echo "Ajout" >> log.txt` |
| `<` | Redirige l’entrée d’un fichier | `commande < fichier` | `sort < noms.txt` |
| `2>` | Redirige les erreurs vers un fichier | `commande 2> fichier` | `ls /root 2> erreurs.txt` |
| `2>>` | Ajoute les erreurs à un fichier | `commande 2>> fichier` | `grep root /etc/passwd 2>> erreurs.log` |
| `&>` | Redirige sortie standard et erreurs vers un fichier | `commande &> fichier` | `make &> build.log` |
| `/dev/null` | Ignore la sortie ou les erreurs | `commande > /dev/null` | `ping -c 5 google.com > /dev/null` |

---

## 🔹 2. Pipes et Chaînage de Commandes
| Commande | Description | Syntaxe | Exemples |
|----------|------------|---------|----------|
| `|` | Envoie la sortie d'une commande vers une autre | `commande1 | commande2` | `ls -l | grep .txt` |
| `tee` | Affiche la sortie et l'écrit dans un fichier | `commande | tee fichier` | `ls | tee fichier.txt` |
| `xargs` | Transforme l'entrée standard en arguments | `commande | xargs autre_commande` | `ls | xargs rm` |

---

## 🔹 3. Filtres et Manipulation de Texte
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `grep` | Recherche un motif dans un fichier | `-i` (ignore la casse), `-r` (récursif) | `grep -i erreur log.txt` |
| `awk` | Manipule des colonnes de texte | `{print $n}` pour afficher une colonne | `awk '{print $1}' fichier.txt` |
| `sed` | Modifie du texte en flux | `s/ancien/nouveau/g` pour remplacer | `sed 's/foo/bar/g' fichier.txt` |
| `cut` | Extrait des parties de texte | `-d` (délimiteur), `-f` (colonne) | `cut -d: -f1 /etc/passwd` |
| `sort` | Trie les lignes d’un fichier | `-r` pour inverser l’ordre | `sort noms.txt` |
| `uniq` | Supprime les lignes en double | `-c` pour compter | `uniq -c noms.txt` |
| `tr` | Transforme des caractères | `-d` pour supprimer | `echo "Hello" | tr 'a-z' 'A-Z'` |
| `wc` | Compte les lignes, mots, caractères | `-l` (lignes), `-w` (mots) | `wc -l fichier.txt` |

---

## 📌 Conclusion
Ce document regroupe les principales commandes de redirection d'entrée/sortie et de filtrage sous Linux. Pour plus de détails, utilisez `man <commande>` dans un terminal Linux/macOS.

