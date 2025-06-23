# 📚 Cours Ultra Complet - SQL & MySQL

---

## 🔰 Introduction Générale

### Qu'est-ce qu'une Base de Données (Database) ?

Une **base de données** est un conteneur structuré permettant de stocker, organiser, manipuler et interroger des informations. On peut la comparer à un classeur numérique où chaque page représente des données spécifiques.

### Qu'est-ce qu'une Base de Données Relationnelle (Relational Database) ?

C'est un système où les données sont organisées en **tables** interconnectées par des relations. Chaque table contient des lignes (**enregistrements**) et des colonnes (**attributs**).

**Exemple schéma relationnel :**

```
Table: Clients
+----+----------+-------------+
| id | nom      | email       |
+----+----------+-------------+
| 1  | Alice    | a@ex.com    |
| 2  | Bob      | b@ex.com    |
+----+----------+-------------+

Table: Commandes
+----+-----------+---------+
| id | client_id | montant |
+----+-----------+---------+
| 1  | 1         | 100.00  |
| 2  | 2         | 250.00  |
+----+-----------+---------+
```

**Relation :** `client_id` dans Commandes fait référence à `id` dans Clients.

### SQL - Structured Query Language

Langage normalisé permettant d'interagir avec les bases de données relationnelles :

* **Créer** des tables et bases de données
* **Manipuler** les données (INSERT, UPDATE, DELETE)
* **Interroger** les données (SELECT)

### Qu'est-ce que MySQL ?

Un des systèmes de gestion de base de données relationnelle (SGBDR) les plus populaires.

---

## 🛠️ Installation et Démarrage de MySQL

**Sous Ubuntu 22.04 / 20.04 :**

```bash
sudo apt update
sudo apt install mysql-server
mysql --version
```

Lancer le serveur :

```bash
sudo service mysql start
```

Accéder au terminal MySQL :

```bash
sudo mysql -uroot
```

---

## 🗂️ Types d'Instructions SQL

### DDL - Data Definition Language (Définition de structure)

* `CREATE` : Créer tables/bases
* `ALTER` : Modifier structure
* `DROP` : Supprimer

### DML - Data Manipulation Language (Manipulation de données)

* `INSERT` : Ajouter données
* `UPDATE` : Modifier données
* `DELETE` : Supprimer données
* `SELECT` : Requête de lecture

---

## ⚒️ Création et Modification

### Créer une base de données

```sql
CREATE DATABASE magasin;
```

### Utiliser une base

```sql
USE magasin;
```

### Créer une table

```sql
CREATE TABLE produits (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    prix DECIMAL(6,2) NOT NULL
);
```

### Modifier une table (Exemple : ajout d'une colonne)

```sql
ALTER TABLE produits ADD stock INT DEFAULT 0;
```

---

## 🔎 Requêtes de Lecture - SELECT

### Lire toutes les données

```sql
SELECT * FROM produits;
```

### Sélection avec condition

```sql
SELECT nom, prix FROM produits WHERE prix > 20.00;
```

### Trier les résultats

```sql
SELECT * FROM produits ORDER BY prix DESC;
```

### Limiter le nombre de résultats

```sql
SELECT * FROM produits LIMIT 5;
```

---

## ✍️ Manipulation des Données

### Insérer des données

```sql
INSERT INTO produits (nom, prix) VALUES ('Café', 2.50);
```

### Mettre à jour des données

```sql
UPDATE produits SET prix = 3.00 WHERE nom = 'Café';
```

### Supprimer des données

```sql
DELETE FROM produits WHERE nom = 'Café';
```

---

## 🧩 Sous-Requêtes (Subqueries)

Permettent d'imbriquer des requêtes.

**Exemple : Trouver les produits plus chers que la moyenne :**

```sql
SELECT * FROM produits
WHERE prix > (SELECT AVG(prix) FROM produits);
```

---

## 🎛️ Fonctions MySQL Utiles

* **AGREGATS :** `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`
* **CHAÎNES :** `CONCAT()`, `UPPER()`, `LOWER()`, `LENGTH()`
* **DATES :** `NOW()`, `CURDATE()`, `YEAR()`

**Exemple :**

```sql
SELECT COUNT(*) FROM produits;
SELECT CONCAT(nom, ' - ', prix) FROM produits;
```

---

## ⚡ Astuce : Backticks vs Apostrophes

* **Backticks** `` `table` `` : encadrent noms de tables/colonnes
* **Apostrophes** `'valeur'` : délimitent des valeurs textuelles

---

## 📃 Règles & Bonnes Pratiques

✅ Tous les mots-clés SQL en MAJUSCULE
✅ Chaque requête précédée d'un commentaire
✅ Fichiers SQL terminés par une nouvelle ligne
✅ Utilisation de `vi`, `vim` ou `emacs`

---

## 🗒️ Exemple de Fichier SQL avec Commentaires

```sql
-- Création de la base de données
CREATE DATABASE IF NOT EXISTS testdb;

-- Utilisation de la base
USE testdb;

-- Création de la table utilisateurs
CREATE TABLE utilisateurs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(50),
    email VARCHAR(100)
);

-- Insertion d'un utilisateur
INSERT INTO utilisateurs (nom, email) VALUES ('Alice', 'alice@exemple.com');
```

---

## 📌 Ressources complémentaires

* [MySQL Cheat Sheet](https://devhints.io/mysql)
* [MySQL 8.0 Documentation](https://dev.mysql.com/doc/refman/8.0/en/)

---

## 🎯 Objectifs à Maîtriser en Fin de Cours

✅ Comprendre ce qu'est SQL et une base relationnelle
✅ Créer et modifier des tables et bases avec DDL
✅ Manipuler les données avec DML
✅ Écrire des requêtes SELECT simples et avancées
✅ Utiliser des sous-requêtes et des fonctions
✅ Respecter les standards et la syntaxe MySQL

---

**Fin du Cours Ultra Complet SQL & MySQL ✅**

---

Prêt à t'entraîner avec des requêtes réelles ! 💻
