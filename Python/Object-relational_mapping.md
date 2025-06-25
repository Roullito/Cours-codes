
# 🗄️ Cours Ultra Complet – Python : Object-Relational Mapping (ORM) avec SQLAlchemy et MySQLdb

---

## 🔑 Introduction : Pourquoi relier Python et les bases de données ?

- Les applications réelles manipulent des **données persistantes** : utilisateurs, produits, commandes...
- Ces données sont souvent stockées dans une **base relationnelle** (MySQL, PostgreSQL...)
- Python permet d'interagir avec ces bases de deux façons :
    - **Requêtes SQL brutes** avec le module `MySQLdb`
    - **ORM** avec SQLAlchemy : manipuler directement des objets Python sans écrire de SQL

---

## ⚙️ Partie 1 : Connexion et requêtes SQL avec `MySQLdb`

### ✅ Connexion simple

```python
import MySQLdb

conn = MySQLdb.connect(host="localhost", user="root", passwd="root", db="my_db")
cur = conn.cursor()
```

### ✅ Exemple : Récupérer des données

```python
cur.execute("SELECT * FROM states ORDER BY id ASC")
for row in cur.fetchall():
    print(row)
```

### ⚠️ Attention aux injections SQL !

Mauvaise pratique :

```python
name = input("Nom : ")
cur.execute("SELECT * FROM states WHERE name = '{}'".format(name))
```

Bonne pratique avec paramètres :

```python
cur.execute("SELECT * FROM states WHERE name = %s", (name,))
```

### ✅ Fermer la connexion

```python
cur.close()
conn.close()
```

---

## 🏗️ Partie 2 : ORM avec SQLAlchemy

### 🔧 Définition

- ORM = **Object Relational Mapper**
- Permet de manipuler des objets Python plutôt que du SQL
- Le stockage (MySQL, SQLite...) devient transparent

### ✅ Connexion avec SQLAlchemy

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import Session

engine = create_engine("mysql+mysqldb://root:root@localhost/my_db", pool_pre_ping=True)
session = Session(engine)
```

### ✅ Modèle Python lié à une table

```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String

Base = declarative_base()

class State(Base):
    __tablename__ = 'states'
    id = Column(Integer, primary_key=True)
    name = Column(String(128), nullable=False)
```

### ✅ Lire des données

```python
for state in session.query(State).order_by(State.id).all():
    print(state.id, state.name)
```

### ✅ Ajouter une donnée

```python
new_state = State(name="Louisiana")
session.add(new_state)
session.commit()
print(new_state.id)
```

### ✅ Modifier une donnée

```python
state = session.query(State).filter_by(id=2).first()
if state:
    state.name = "New Mexico"
    session.commit()
```

### ✅ Supprimer des données

```python
for state in session.query(State).filter(State.name.like("%a%")).all():
    session.delete(state)
session.commit()
```

---

## 📐 Visualisation simplifiée

```
+--------------------+      +------------------+
| Objet Python (ORM) | <--> | Ligne MySQL      |
+--------------------+      +------------------+
| State(id=1, name="CA") | | id | name         |
+--------------------+      +------------------+
```

---

## 🛠️ Concepts Avancés Indispensables

- Requêtes filtrées : `.filter_by()`, `.filter()`
- Opérateurs : `.like()`, `.ilike()`, `.contains()`
- Trier : `.order_by()`
- Limiter les résultats : `.first()`, `.all()`
- Relations entre tables : `relationship()`
- Jointures implicites via ORM

---

## 🚧 Bonnes pratiques et pièges à éviter

| Problème                | Cause fréquente                      | Solution                         |
|-------------------------|--------------------------------------|-----------------------------------|
| Injection SQL           | Chaînage de chaînes mal protégé     | Toujours utiliser les paramètres |
| Connexion non fermée    | Oubli de `session.close()` ou `conn.close()` | Utiliser le contexte `with` ou bien gérer les fermetures |
| Mauvaise performance    | Requêtes mal optimisées             | Préférer les filtres côté SQL, limiter les `.all()` |
| ORM mal compris         | Confusion entre objets Python et lignes SQL | Relire les bases du mapping ORM  |

---

## 📚 Ressources complémentaires conseillées

- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/en/14/orm/)
- [Cheatsheet SQLAlchemy](https://cheatography.com/jasonrigden/cheat-sheets/sqlalchemy/)
- [Tutoriel SQLAlchemy complet](https://overiq.com/sqlalchemy-101/)
- [Flask et SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/)

---

## 🧠 À retenir

- `MySQLdb` : contrôle total en SQL mais plus verbeux
- `SQLAlchemy ORM` : productivité, lisibilité, abstraction
- L'ORM n'est pas "magique" : comprendre le SQL reste indispensable
- Attention aux bonnes pratiques (sécurité, performance)
