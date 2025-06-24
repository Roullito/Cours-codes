# 📚 SQL - More Queries — Cours Ultra Complet, Pédagogique et Détaillé

---

## 🛠️ 1. Création et gestion d'utilisateurs MySQL

### ➤ Pourquoi créer des utilisateurs ?
Pour sécuriser et organiser les accès à la base de données : chaque utilisateur peut avoir des droits différents.

### ➤ Créer un nouvel utilisateur :
```sql
CREATE USER 'new_user'@'localhost' IDENTIFIED BY 'password';
```
**localhost** signifie que l'utilisateur peut se connecter uniquement depuis la machine locale.

### ➤ Attribuer des privilèges à un utilisateur :
```sql
GRANT ALL PRIVILEGES ON database_name.* TO 'new_user'@'localhost';
FLUSH PRIVILEGES;
```
⚠️ Toujours exécuter `FLUSH PRIVILEGES` après modification des droits.

### ➤ Exemples de privilèges spécifiques :
```sql
GRANT SELECT, INSERT ON database_name.* TO 'new_user'@'localhost';
```

### ➤ Retirer des privilèges :
```sql
REVOKE ALL PRIVILEGES ON database_name.* FROM 'new_user'@'localhost';
```

### ➤ Supprimer un utilisateur :
```sql
DROP USER 'new_user'@'localhost';
```

---

## 🔑 2. Contraintes SQL (Garantir l'intégrité des données)

### ➤ PRIMARY KEY :
Identifie de manière unique chaque ligne d'une table. Obligatoire pour la clé d'identification principale.
```sql
id INT PRIMARY KEY
```

### ➤ FOREIGN KEY :
Crée un lien entre deux tables.
```sql
FOREIGN KEY (role_id) REFERENCES roles(id)
```
**Attention :** La valeur insérée doit exister dans la table référencée.

### ➤ NOT NULL :
La colonne doit obligatoirement contenir une valeur.

### ➤ UNIQUE :
La valeur dans cette colonne doit être unique pour chaque ligne.

**Exemple complet :**
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    role_id INT,
    FOREIGN KEY (role_id) REFERENCES roles(id)
);
```

---

## 🔄 3. JOINS — Lier les tables pour enrichir les résultats

Imagine 2 tables :
```sql
clients (id, nom)
commandes (id, client_id, montant)
```

### ➤ INNER JOIN (intersection des deux tables)
```sql
SELECT clients.nom, commandes.montant
FROM clients
INNER JOIN commandes ON clients.id = commandes.client_id;
```
**Affiche uniquement les clients ayant passé des commandes.**

### ➤ LEFT JOIN (tous les clients, commandes si existantes)
```sql
SELECT clients.nom, commandes.montant
FROM clients
LEFT JOIN commandes ON clients.id = commandes.client_id;
```
**Affiche tous les clients, même ceux sans commande (valeurs NULL si pas de commande).**

### ➤ RIGHT JOIN (l'inverse du LEFT JOIN)
Affiche toutes les commandes même si aucun client n'est associé (peu courant mais possible).

### ➤ FULL OUTER JOIN (MySQL ne le supporte pas directement)
Simulable avec `UNION` :
```sql
SELECT * FROM A LEFT JOIN B ON A.key = B.key
UNION
SELECT * FROM A RIGHT JOIN B ON A.key = B.key;
```

---

## 🧩 4. JOINs multiples

### ➤ Exemple avec 3 tables :
```sql
SELECT A.nom, B.nom, C.nom
FROM A
JOIN B ON A.b_id = B.id
JOIN C ON B.c_id = C.id;
```
Permet de naviguer dans plusieurs niveaux de relations.

---

## 🔍 5. Sous-requêtes (Subqueries)

**Une sous-requête permet d'insérer une requête dans une autre.**

### ➤ Dans le WHERE :
```sql
SELECT nom FROM clients
WHERE id IN (SELECT client_id FROM commandes WHERE montant > 100);
```
**Affiche les clients ayant passé une commande de plus de 100.**

### ➤ Avec EXISTS :
```sql
SELECT nom FROM clients
WHERE EXISTS (SELECT * FROM commandes WHERE clients.id = commandes.client_id);
```
**Affiche les clients ayant au moins une commande.**

---

## 🔀 6. UNION et différences

### ➤ UNION : Combine les résultats de deux requêtes sans doublons.
```sql
SELECT nom FROM clients
UNION
SELECT nom FROM fournisseurs;
```

### ➤ UNION ALL : Inclut les doublons.

### ➤ MINUS : Pas supporté en MySQL. À simuler avec `LEFT JOIN ... IS NULL`.

---

## 🏗️ 7. Design de base de données (UML et Normalisation)

### ➤ UML :
Diagramme représentant :
- Les tables (entités)
- Les relations (liens entre clés)

### ➤ Normalisation :
**But : Éliminer les redondances et assurer la cohérence.**

- **1NF** : Pas de champs multivalués ou de champs composites.
- **2NF** : Dépendance complète de la clé primaire.
- **3NF** : Pas de dépendance transitives (dépendances entre colonnes non clés).

### ➤ Exemple simple de modélisation :
**Table Clients :** id, nom  
**Table Commandes :** id, client_id, montant  
Relation : Commandes.client_id → Clients.id

---

## 🎨 8. Résumé visuel des types de JOIN (voir image associée au projet)

- INNER JOIN → Intersection (données communes)
- LEFT JOIN → Tout A + correspondances B
- RIGHT JOIN → Tout B + correspondances A
- FULL JOIN (via UNION) → Tout A + Tout B
- LEFT JOIN + IS NULL → Éléments de A sans correspondance dans B
- RIGHT JOIN + IS NULL → Éléments de B sans correspondance dans A

---

## 📝 9. Bonnes pratiques SQL obligatoires

✅ Commentaires en haut de chaque script  
✅ Tous les mots-clés SQL en majuscules  
✅ Respect des conventions (snake_case ou camelCase selon l'équipe)  
✅ Fichiers terminés par un saut de ligne  
✅ Attention aux performances :
- Utiliser les indexes
- Éviter les sous-requêtes inutiles
- Privilégier les JOIN clairs

---

## 📌 10. Récapitulatif express

✔ Créer des utilisateurs et gérer leurs droits  
✔ Maîtriser PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL  
✔ Faire des requêtes complexes avec JOINs et sous-requêtes  
✔ Utiliser UNION pour combiner les résultats  
✔ Appliquer la normalisation et modéliser avec UML  
✔ Écrire des scripts SQL propres et maintenables  

---

## 🔗 11. Ressources complémentaires

- [MySQL Cheat Sheet](https://devhints.io/mysql)
- [SQL Joins visuel interactif](https://www.sql-joins.lewagon.com/)
- [SQL Style Guide](https://www.sqlstyle.guide/)
- [Documentation officielle MySQL](https://dev.mysql.com/doc/)

---

**Ce cours couvre l'intégralité des concepts nécessaires pour réussir le projet SQL - More Queries et comprendre les bonnes pratiques du développement de bases relationnelles.**
