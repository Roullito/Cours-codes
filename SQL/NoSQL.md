# Cours complet NoSQL & MongoDB

## 📖 Introduction à NoSQL
**NoSQL** signifie **"Not Only SQL"**.  
C'est une approche flexible pour le stockage et la gestion des données, souvent utilisée pour :
- Les applications **Big Data**
- Les systèmes nécessitant une **scalabilité horizontale**
- Les données **non structurées ou semi-structurées**
- Les environnements temps réel (réseaux sociaux, IoT, analytics…)

---

## ⚔️ SQL vs NoSQL

| Critère            | SQL (Relationnel)          | NoSQL                          |
|--------------------|----------------------------|---------------------------------|
| **Structure**      | Tables (lignes, colonnes)  | Collections (documents)        |
| **Schéma**         | Fixe, défini à l’avance    | Flexible, dynamique            |
| **Langage**        | SQL                        | API ou langage spécifique      |
| **Scalabilité**    | Verticale                  | Horizontale                    |
| **Transactions**   | ACID                       | BASE                           |
| **Cas d’usage**    | Schéma stable              | Données massives, temps réel   |

---

## 🧩 ACID vs BASE

### ACID (SQL)
- **Atomicité** : Tout ou rien
- **Cohérence** : Données valides
- **Isolation** : Transactions indépendantes
- **Durabilité** : Données persistantes

### BASE (NoSQL)
- **Basically Available** : Toujours disponible
- **Soft state** : État temporairement incohérent possible
- **Eventual consistency** : Cohérence atteinte avec le temps

---

## 🗂 Types de bases NoSQL
1. **Clé-valeur** → (Redis, DynamoDB) : Simple et rapide
2. **Colonnes** → (Cassandra, HBase) : Pour l’analytique massive
3. **Documents** → (MongoDB, CouchDB) : Stockage JSON/BSON
4. **Graphes** → (Neo4j, ArangoDB) : Relations complexes

---

## 🍃 MongoDB - Concepts clés
- **Base de données** : Ensemble de collections
- **Collection** : Ensemble de documents (équivalent table)
- **Document** : Objet JSON/BSON
- **_id** : Identifiant unique

---

## ⚙️ Installation (Ubuntu 20.04)
```bash
sudo apt update
sudo apt install -y mongodb
sudo systemctl start mongodb
sudo systemctl enable mongodb
```

---

## 💻 Commandes Mongo Shell

### Lister les bases
```javascript
show dbs
```

### Utiliser / créer une base
```javascript
use my_db
```

### Insérer un document
```javascript
db.school.insertOne({ name: "Holberton school" })
```

### Lire tous les documents
```javascript
db.school.find()
```

### Filtrer
```javascript
db.school.find({ name: "Holberton school" })
```

### Compter
```javascript
db.school.countDocuments()
```

### Mettre à jour
```javascript
db.school.updateMany(
  { name: "Holberton school" },
  { $set: { address: "972 Mission street" } }
)
```

### Supprimer
```javascript
db.school.deleteMany({ name: "Holberton school" })
```

---

## 🐍 PyMongo (MongoDB avec Python)

### Installation
```bash
pip install pymongo
```

### Connexion
```python
from pymongo import MongoClient
client = MongoClient("mongodb://127.0.0.1:27017")
db = client.my_db
```

### Lister
```python
def list_all(mongo_collection):
    return list(mongo_collection.find())
```

### Insérer
```python
def insert_school(mongo_collection, **kwargs):
    return mongo_collection.insert_one(kwargs).inserted_id
```

### Mettre à jour
```python
def update_topics(mongo_collection, name, topics):
    mongo_collection.update_many(
        { "name": name },
        { "$set": { "topics": topics } }
    )
```

### Rechercher par topic
```python
def schools_by_topic(mongo_collection, topic):
    return list(mongo_collection.find({ "topics": topic }))
```

---

## 🔍 Agrégation
```javascript
db.collection.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$category", total: { $sum: 1 } } }
])
```

---

## 🚀 Avantages NoSQL
✅ Flexibilité du schéma  
✅ Haute performance et scalabilité  
✅ Parfait pour données massives  
✅ Haute disponibilité

---

## 📚 Ressources utiles
- [NoSQL Databases Explained](https://www.mongodb.com/nosql-explained)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [PyMongo Docs](https://pymongo.readthedocs.io/en/stable/)
