# Commandes Vi

## 📌 Introduction
Ce document répertorie les principales commandes de l'éditeur de texte Vi, classées par catégories avec leurs options et des exemples.

---

## 🔹 1. Modes de Vi
| Mode | Description | Activation |
|------|------------|------------|
| Mode Normal | Mode par défaut pour naviguer et exécuter des commandes | Appuyer sur `Esc` |
| Mode Insertion | Permet d’éditer du texte | `i`, `a`, `o` |
| Mode Commande | Permet d'exécuter des commandes | `:` en mode Normal |

---

## 🔹 2. Navigation dans un Fichier
| Commande | Description | Exemples |
|----------|------------|----------|
| `h`, `l` | Déplacement gauche/droite | `h` (gauche), `l` (droite) |
| `j`, `k` | Déplacement bas/haut | `j` (bas), `k` (haut) |
| `0`, `^`, `$` | Aller au début (`0`), premier caractère (`^`) ou fin (`$`) d’une ligne | `0` pour début, `$` pour fin |
| `gg`, `G` | Aller au début (`gg`) ou à la fin (`G`) d’un fichier | `gg` (début), `G` (fin) |
| `nG` | Aller à la ligne `n` | `10G` va à la ligne 10 |
| `Ctrl-d`, `Ctrl-u` | Descendre (`Ctrl-d`) ou remonter (`Ctrl-u`) d’un demi-écran | `Ctrl-d` pour descendre |
| `Ctrl-f`, `Ctrl-b` | Descendre (`Ctrl-f`) ou remonter (`Ctrl-b`) d’un écran | `Ctrl-f` (page suivante) |

---

## 🔹 3. Modes d'Insertion
| Commande | Description | Exemples |
|----------|------------|----------|
| `i` | Insérer avant le curseur | `iTexte` |
| `a` | Insérer après le curseur | `aTexte` |
| `o` | Nouvelle ligne en dessous | `oTexte` |
| `O` | Nouvelle ligne au-dessus | `OTexte` |
| `Esc` | Revenir en mode Normal | `Esc` |

---

## 🔹 4. Suppression et Remplacement
| Commande | Description | Exemples |
|----------|------------|----------|
| `x` | Supprime un caractère sous le curseur | `x` |
| `dd` | Supprime une ligne entière | `dd` |
| `D` | Supprime tout jusqu'à la fin de la ligne | `D` |
| `dw` | Supprime un mot | `dw` |
| `cw` | Remplace un mot | `cwNouveauTexte` |
| `r` | Remplace un caractère | `rX` (remplace par X) |

---

## 🔹 5. Copier, Coller et Annuler
| Commande | Description | Exemples |
|----------|------------|----------|
| `yy` | Copier une ligne | `yy` |
| `p` | Coller après le curseur | `p` |
| `P` | Coller avant le curseur | `P` |
| `u` | Annuler la dernière action | `u` |
| `Ctrl-r` | Rétablir une action annulée | `Ctrl-r` |

---

## 🔹 6. Recherche et Remplacement
| Commande | Description | Exemples |
|----------|------------|----------|
| `/mot` | Recherche `mot` dans le texte | `/erreur` |
| `?mot` | Recherche `mot` en arrière | `?erreur` |
| `n` | Répète la recherche | `n` |
| `:%s/ancien/nouveau/g` | Remplace `ancien` par `nouveau` dans tout le fichier | `:%s/erreur/correct/g` |

---

## 🔹 7. Sauvegarde et Fermeture
| Commande | Description | Exemples |
|----------|------------|----------|
| `:w` | Sauvegarde le fichier | `:w` |
| `:q` | Quitte (si aucune modification) | `:q` |
| `:wq` ou `ZZ` | Sauvegarde et quitte | `:wq` |
| `:q!` | Quitte sans sauvegarder | `:q!` |

---

## 📌 Conclusion
Ce document regroupe les principales commandes Vi utilisées pour l'édition de texte. Pour plus de détails, utilisez `:help` dans Vi.

