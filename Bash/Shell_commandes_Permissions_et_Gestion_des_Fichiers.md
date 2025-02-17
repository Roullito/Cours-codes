# Commandes Shell - Permissions et Gestion des Fichiers

## 📌 Introduction
Ce document répertorie les principales commandes Shell utilisées pour la gestion des permissions et des fichiers sous Linux, classées par catégories avec leurs options et des exemples.

---

## 🔹 1. Gestion des permissions
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `ls -l` | Affiche les permissions des fichiers | `-h` pour un affichage lisible | `ls -lh` |
| `chmod` | Modifie les permissions d’un fichier ou dossier | `+x` (ajoute exécution), `-r` (retire lecture) | `chmod 755 script.sh` |
| `chmod u+x` | Ajoute l’exécution pour l’utilisateur | `u` = user, `g` = group, `o` = other | `chmod u+x mon_fichier` |
| `chmod g-w` | Retire l'écriture pour le groupe | Aucun | `chmod g-w fichier.txt` |
| `chmod 777` | Donne tous les droits à tout le monde | Aucun | `chmod 777 fichier.txt` |
| `umask` | Définit les permissions par défaut des nouveaux fichiers | Aucun | `umask 022` |

---

## 🔹 2. Gestion des propriétaires
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `chown` | Change le propriétaire d’un fichier ou dossier | `user:group` pour définir propriétaire et groupe | `chown user:group fichier.txt` |
| `chown user` | Change uniquement le propriétaire | Aucun | `chown alice fichier.txt` |
| `chown :group` | Change uniquement le groupe | Aucun | `chown :admin fichier.txt` |
| `chown -R user:group dossier/` | Change le propriétaire et groupe récursivement | `-R` pour inclure tout le dossier | `chown -R alice:admin mon_dossier/` |
| `chgrp` | Change le groupe d’un fichier ou dossier | Aucun | `chgrp admin fichier.txt` |

---

## 🔹 3. Gestion des liens
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `ln -s` | Crée un lien symbolique (raccourci) | Aucun | `ln -s /chemin/fichier lien_nom` |
| `ln` | Crée un lien physique | Aucun | `ln fichier1.txt lien1.txt` |
| `rm lien_nom` | Supprime un lien symbolique | Aucun | `rm mon_lien` |
| `readlink` | Affiche la destination d’un lien symbolique | Aucun | `readlink mon_lien` |

---

## 🔹 4. Gestion des ACL (Access Control Lists)
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `getfacl` | Affiche les ACL d’un fichier | Aucun | `getfacl fichier.txt` |
| `setfacl -m` | Ajoute une permission ACL à un utilisateur | `-m` pour modifier | `setfacl -m u:alice:rwx fichier.txt` |
| `setfacl -x` | Supprime une entrée ACL | `-x` pour supprimer | `setfacl -x u:alice fichier.txt` |
| `setfacl -b` | Supprime toutes les ACL d’un fichier | Aucun | `setfacl -b fichier.txt` |

---

## 🔹 5. Gestion des droits spéciaux
| Commande | Description | Options | Exemples |
|----------|------------|---------|----------|
| `chmod +s` | Active le **SUID** sur un fichier exécutable | Aucun | `chmod +s /usr/bin/script` |
| `chmod +g` | Active le **SGID** sur un dossier | Aucun | `chmod g+s mon_dossier/` |
| `chmod +t` | Active le **sticky bit** (verrouillage de suppression) | Aucun | `chmod +t mon_dossier/` |

---

## 📌 Conclusion
Ce document regroupe les commandes Shell essentielles pour la gestion des permissions, des propriétaires et des ACL sous Linux. Pour plus de détails, utilisez `man <commande>` dans un terminal Linux/macOS.

