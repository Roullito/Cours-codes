# 🌐 Cours Ultra Complet – Les API et les API RESTful en Python

## 🔰 Introduction aux API

### Qu’est-ce qu’une API ?
Une **API (Application Programming Interface)** est un ensemble de règles permettant à deux logiciels de communiquer entre eux. Elle agit comme un **pont** entre des systèmes.

**Exemples :**
- Une appli météo qui récupère la météo via une API.
- Une appli bancaire qui affiche ton solde en utilisant l’API de la banque.
- Une appli de livraison qui utilise une API de géolocalisation.

### Pourquoi utiliser une API ?
- **Réutilisation** de fonctionnalités tierces (paiement, authentification, géolocalisation…)
- **Modularité** du code
- **Interopérabilité** entre systèmes hétérogènes
- **Scalabilité** via microservices
- **Automatisation** des échanges machine-to-machine

---

## 🧱 REST – Representational State Transfer

### Définition
REST est un **style architectural** pour construire des services web interopérables. Proposé par Roy Fielding en 2000, il repose sur des **contraintes** garantissant performance, scalabilité, et simplicité.

### 6 Contraintes REST :
1. **Client-serveur**
2. **Stateless** (le serveur ne conserve pas l’état entre les appels)
3. **Cacheable**
4. **Interface uniforme** (URI + méthodes HTTP standard)
5. **Système en couches**
6. **Code à la demande (optionnel)**

---

## 📬 Protocole HTTP – Fondement des API REST

### Méthodes HTTP :
| Méthode | Usage                      | Idempotent | Sécurisé |
|---------|----------------------------|------------|----------|
| GET     | Lire une ressource         | ✅         | ✅       |
| POST    | Créer une ressource        | ❌         | ❌       |
| PUT     | Remplacer une ressource    | ✅         | ❌       |
| PATCH   | Modifier partiellement     | ❌         | ❌       |
| DELETE  | Supprimer une ressource    | ✅         | ❌       |

### Codes de statut :
| Code | Signification                   |
|------|----------------------------------|
| 200  | OK                              |
| 201  | Created                         |
| 204  | No Content                      |
| 400  | Bad Request                     |
| 401  | Unauthorized                    |
| 403  | Forbidden                       |
| 404  | Not Found                       |
| 500  | Internal Server Error           |

---

## 🧪 Consommer une API

### Avec `curl`
```bash
curl https://jsonplaceholder.typicode.com/posts
curl -X POST -d "title=test&body=hello" https://jsonplaceholder.typicode.com/posts
```

### Avec Python – `requests`
```python
import requests

response = requests.get("https://jsonplaceholder.typicode.com/posts")
if response.status_code == 200:
    for post in response.json():
        print(post["title"])
```

### JSON en Python
```python
import json
dict_data = {"name": "Alice", "age": 25}
json_string = json.dumps(dict_data)
parsed = json.loads(json_string)
```

---

## 🧑‍🍳 Créer une API

### Avec `http.server`
```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class Handler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'application/json')
        self.end_headers()
        self.wfile.write(b'{"message": "Hello from HTTP server"}')

httpd = HTTPServer(('localhost', 8000), Handler)
httpd.serve_forever()
```

### Avec `Flask`
```python
from flask import Flask, jsonify, request

app = Flask(__name__)
users = {}

@app.route("/add_user", methods=["POST"])
def add_user():
    data = request.get_json()
    username = data.get("username")
    if not username:
        return jsonify({"error": "Username is required"}), 400
    users[username] = data
    return jsonify({"message": "User added", "user": data}), 201
```

---

## 🔐 Sécuriser une API

### Authentification basique – Flask-HTTPAuth
```python
from flask_httpauth import HTTPBasicAuth
from werkzeug.security import generate_password_hash, check_password_hash

auth = HTTPBasicAuth()
users = {"admin": generate_password_hash("secret")}

@auth.verify_password
def verify(username, password):
    if username in users and check_password_hash(users.get(username), password):
        return username
```

### JWT – Flask-JWT-Extended
```python
from flask_jwt_extended import JWTManager, create_access_token, jwt_required

app.config["JWT_SECRET_KEY"] = "super-secret"
jwt = JWTManager(app)

@app.route("/login", methods=["POST"])
def login():
    return jsonify(access_token=create_access_token(identity="admin"))
```

---

## 📝 Documentation avec OpenAPI / Swagger

### Objectifs :
- Générer une doc interactive
- Fournir un contrat clair pour les consommateurs

### Outils :
- Swagger UI
- ReDoc
- Postman (collections auto-documentées)
- FastAPI (génère doc Swagger automatiquement)

---

## 🧠 Problèmes fréquents

| Problème                | Solution                            |
|------------------------|--------------------------------------|
| 401 Unauthorized       | Vérifie token / entête d’auth        |
| 403 Forbidden          | Vérifie les droits d’accès / rôles   |
| 415 Unsupported Type   | Mettre Content-Type: application/json|
| 500 Internal Server    | Lire les logs serveur                |
| CORS bloqué            | Ajouter headers Access-Control-Allow-* |

---

## 🧭 Concepts Avancés à connaître

- **Rate Limiting** (Limiter abus d’API)
- **Pagination** (Réduire taille réponse)
- **Versioning** (`/api/v1/`, ou via `Accept`)
- **HATEOAS** : liens dans les réponses JSON
- **Idempotence** : répétition d’une requête = même effet
- **Safety** : GET ne modifie pas, POST oui

---

## ✅ Résumé Clé

- Les **API REST** utilisent HTTP pour exposer des ressources
- **GET**, **POST**, **PUT**, **DELETE** → actions standards
- JSON est le format dominant
- `curl` ou `requests` permettent de consommer une API
- `Flask` permet d’en développer une simplement
- La sécurité (auth) est essentielle en production
- Swagger permet de **documenter proprement**
