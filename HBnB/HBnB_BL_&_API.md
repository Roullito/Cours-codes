# 🧱️ Projet HBnB – Partie 2 : Business Logic & API

## 🗭 Objectif général

Créer l’architecture et les fonctionnalités principales de l’API HBnB avec une séparation claire entre :

* **Présentation (API)** avec Flask et `flask-restx`
* **Logique métier** (Business Logic Layer - BLL)
* **Persistance temporaire** en mémoire (repository mock)
* Intégration du **Facade Pattern** pour connecter proprement l’API à la logique métier

---

## 📁 1. Structure du Projet – Architecture Modulaire

### 🎯 But

Organiser ton code en modules clairs et indépendants :

```bash
part2/
├── api/                # Présentation (endpoints REST)
│   └── v1/
│       ├── __init__.py
│       └── users.py, places.py, ...
├── business/           # Logique métier
│   ├── __init__.py
│   ├── user.py
│   ├── place.py
│   └── ...
├── persistence/        # Stockage temporaire (in-memory)
│   ├── __init__.py
│   └── repository.py
├── facade/             # Pattern façade
│   └── __init__.py
├── app.py              # Point d’entrée Flask
└── requirements.txt    # Dépendances Python
```

### 📘 Ressources

* [Python Project Structure](https://docs.python-guide.org/writing/structure/)
* [Modular Programming in Python](https://realpython.com/python-application-layouts/)

---

## 🧠 2. Design Pattern : Le **Facade Pattern**

### 🧩️ Définition

Le **Facade Pattern** est un patron de conception qui fournit une **interface simplifiée** à un système complexe. Dans HBnB, il sert d’intermédiaire entre :

* L’API (Presentation Layer)
* La logique métier (BLL)

### ✅ Avantages

* Réduction du couplage entre les couches
* Meilleure organisation et testabilité
* Masque la complexité interne

### 💡 Exemple simple (sans code)

Au lieu que l’API appelle directement les classes User, Place, etc., elle appelle un **FaçadeService** qui centralise les appels métiers.

### 📘 Ressources

* [Facade Pattern en Python](https://refactoring.guru/design-patterns/facade/python/example)

---

## 🔧 3. Flask & flask-restx : Créer une API RESTful

### 🌐 Flask

Un microframework web Python très léger, idéal pour construire une API rapidement.

### 📚 Flask-RestX

Extension de Flask pour créer des **API REST documentées**, avec :

* **Namespaces** (`api = Namespace('users', ...)`)
* **Models** pour la documentation Swagger (OpenAPI)
* **Decorators** comme `@api.route`, `@api.expect`, etc.

### 📘 Ressources

* [Flask](https://flask.palletsprojects.com/en/stable/)
* [flask-restx](https://flask-restx.readthedocs.io/en/latest/)

---

## 📁 4. Implémentation des **Classes Métiers** (Business Logic)

### 🧬 Entités à coder

* `User`
* `Place`
* `Amenity`
* `Review`

### 🔄 Concepts clés à maîtriser

* Encapsulation (attributs privés)
* Héritage (ex : `BaseModel`)
* Relations entre entités :

  * `Place` lié à `User` (owner)
  * `Review` lié à `Place` et `User`
  * `Amenity` lié à `Place` via une liste

### ⚙️ Validation à inclure

* UUID comme identifiant (`uuid4`)
* Attributs obligatoires (ex : email pour User)
* Timestamps (`created_at`, `updated_at`)

### 📘 Ressources

* [Python OOP Basics – Real Python](https://realpython.com/python3-object-oriented-programming/)
* [UUIDs en Python](https://docs.python.org/3/library/uuid.html)

---

## 🗉 5. CRUD Endpoints pour chaque ressource

### Pour chaque entité :

* **POST** `/api/v1/{entity}` → création
* **GET** `/api/v1/{entity}/{id}` → lecture par ID
* **GET** `/api/v1/{entity}` → liste
* **PUT** `/api/v1/{entity}/{id}` → mise à jour
* (**DELETE** seulement pour Review)

### ✅ Attention

* Ne jamais retourner le mot de passe dans les réponses
* Valider les champs côté API (types, formats, obligatoires)
* Retourner les bons codes HTTP (`200`, `201`, `404`, `400`, etc.)
* Swagger se met à jour automatiquement avec `@api.doc`

---

## 🧬 6. Sérialisation et Composition

### ↺ Pourquoi ?

Quand tu récupères un `Place`, tu dois aussi renvoyer dans la réponse :

* Le `User` propriétaire (`first_name`, `last_name`)
* La liste des `Amenities` associées

### 🛠️ Outils

* Méthodes `.to_dict()` dans les classes métiers
* Embedding manuel des objets liés (pas de ORM ici)

---

## 🧪 7. Tests et Validation

### 💫 2 types de tests

* **Tests manuels** avec `cURL` ou Postman
* **Tests unitaires** avec `unittest` ou `pytest`

### 🔍 Que tester ?

* Création avec bons et mauvais inputs
* Récupération avec ID invalide
* Mise à jour partielle
* Champs obligatoires
* Liens entre entités

### 🛠️ Outils

* `curl` :

```bash
curl -X POST http://localhost:5000/api/v1/users/ \
  -H "Content-Type: application/json" \
  -d '{"email": "test@test.com"}'
```

* Swagger UI de `flask-restx`

---

## ✅ Étapes Résumées

| Étape | Action                      | Concepts à maîtriser            |
| ----- | --------------------------- | ------------------------------- |
| 1     | Créer l’architecture        | Modularité, packages Python     |
| 2     | Implémenter classes métiers | OOP, UUID, timestamps           |
| 3     | Façade entre API et logique | Design patterns                 |
| 4     | API Users                   | flask-restx, validation, routes |
| 5     | API Amenities               | Idem                            |
| 6     | API Places (avec relations) | Composition, nested data        |
| 7     | API Reviews (avec DELETE)   | Validation croisée              |
| 8     | Tests                       | cURL, Swagger, unittest         |
| 9     | Swagger & doc auto          | OpenAPI / Swagger UI            |
| 10    | Préparer intégration DB     | Repository Pattern              |

---

## 🧠 Tips de dev efficaces

* Commence simple : endpoints sans logique trop complexe
* Utilise des fichiers `schemas.py` si tu veux centraliser la structure des payloads
* Garde ton `app.py` propre, tout doit être modulé
* Teste chaque couche séparément : logique métier d’abord, API ensuite
* Documente ton Swagger **au fur et à mesure**
