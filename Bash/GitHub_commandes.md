# Commandes GitHub

## 📌 Introduction
Ce document répertorie les principales commandes GitHub, classées par catégories avec leurs options et des exemples.

---

## 🔹 1. Configuration de GitHub
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git config` | Configure les paramètres globaux ou locaux de Git | `--global`, `--local` | `git config --global user.name "Nom"` |
| `git config --list` | Affiche la configuration actuelle | Aucun | `git config --list` |
| `git config --global user.email` | Définit l'email de l'utilisateur | Aucun | `git config --global user.email "email@example.com"` |

---

## 🔹 2. Gestion des Dépôts
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git init` | Initialise un nouveau dépôt Git | Aucun | `git init mon_projet` |
| `git clone` | Clone un dépôt distant | `--depth` pour limiter l'historique | `git clone https://github.com/user/repo.git` |
| `git status` | Affiche l'état actuel du dépôt | Aucun | `git status` |

---

## 🔹 3. Gestion des Fichiers
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git add` | Ajoute un fichier à l'index | `.` pour tous les fichiers | `git add fichier.txt` |
| `git rm` | Supprime un fichier du dépôt | `--cached` pour ne pas supprimer physiquement | `git rm fichier.txt` |
| `git mv` | Renomme ou déplace un fichier | Aucun | `git mv ancien.txt nouveau.txt` |

---

## 🔹 4. Validation et Historique
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git commit` | Sauvegarde les modifications | `-m` pour ajouter un message | `git commit -m "Initial commit"` |
| `git commit --amend` | Modifie le dernier commit | Aucun | `git commit --amend -m "Nouveau message"` |
| `git log` | Affiche l'historique des commits | `--oneline` pour un affichage compact | `git log --oneline` |
| `git diff` | Affiche les différences entre les commits | `--staged` pour voir les changements indexés | `git diff` |

---

## 🔹 5. Gestion des Branches
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git branch` | Liste ou crée des branches | `-d` pour supprimer | `git branch nouvelle_branche` |
| `git checkout` | Change de branche | `-b` pour créer et changer | `git checkout -b dev` |
| `git switch` | Change de branche (alternative à `checkout`) | `-c` pour créer une nouvelle branche | `git switch main` |
| `git branch -m` | Renomme une branche | Aucun | `git branch -m ancienne_nouvelle` |

---

## 🔹 6. Synchronisation avec GitHub
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git remote` | Liste ou ajoute un dépôt distant | `add origin` pour ajouter une origine | `git remote add origin https://github.com/user/repo.git` |
| `git push` | Envoie des commits au dépôt distant | `-u` pour suivre la branche | `git push origin main` |
| `git pull` | Récupère et fusionne les modifications d'un dépôt distant | Aucun | `git pull origin main` |
| `git fetch` | Récupère les mises à jour sans fusionner | Aucun | `git fetch origin` |

---

## 🔹 7. Gestion des Merges et Rebase
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git merge` | Fusionne une branche dans la branche active | `--no-ff` pour un merge non-fast-forward | `git merge feature-branch` |
| `git rebase` | Applique les commits d'une branche sur une autre | `--interactive` pour éditer les commits | `git rebase main` |
| `git cherry-pick` | Applique un commit spécifique d'une autre branche | Aucun | `git cherry-pick abc123` |

---

## 🔹 8. Annulation et Récupération
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `git reset` | Annule des commits en modifiant l'historique | `--soft`, `--mixed`, `--hard` | `git reset --hard HEAD~1` |
| `git revert` | Annule un commit en créant un nouveau commit | Aucun | `git revert abc123` |
| `git stash` | Met de côté les modifications en cours | `pop` pour restaurer | `git stash` |

---

## 📌 Conclusion
Ce document regroupe les commandes GitHub les plus utiles. Pour plus de détails, utilisez `git help <commande>` dans un terminal.

