# 🌐 Python - Server-Side Rendering (SSR)

## 📚 Introduction

Server-side rendering (SSR) est une technique où le serveur génère des pages HTML complètes **avant** de les envoyer au navigateur. Cela s’oppose au rendu côté client, où le navigateur reconstruit les pages via JavaScript à partir de données brutes.

Dans ce cours, tu apprendras à créer des applications web dynamiques et performantes avec **Python** et **Flask**, en utilisant le moteur de templates **Jinja2**.

---

## 🎯 Objectifs pédagogiques

À la fin de ce cours, tu seras capable de :

- Comprendre les différences entre SSR, client-side rendering et pre-rendering
- Mettre en œuvre SSR avec **Flask**
- Utiliser **Jinja2** pour générer du HTML dynamique
- Charger et afficher des données issues de formats divers : JSON, CSV, SQLite
- Gérer des formulaires et entrées utilisateur côté serveur
- Structurer une app Flask maintenable avec templates, static, routes, et logique métier

---

## 🧠 1. Comprendre le SSR

### 🔁 SSR vs CSR

| Aspect | SSR (Server-Side) | CSR (Client-Side) |
|-------|------------------|------------------|
| Rendu initial | Généré sur le serveur | Généré dans le navigateur |
| Performance | Rapide au chargement initial | Plus rapide ensuite |
| SEO | ✅ Excellente indexation | ❌ Peut poser problème |
| JavaScript | Optionnel | Nécessaire |

### Avantages du SSR

- Chargement initial plus rapide
- Meilleur SEO (le HTML est déjà prêt)
- Moins dépendant du JS côté client
- Simplifie le debugging

---

## 🧰 2. Flask et Jinja2

### Installation de Flask

```bash
pip install Flask
```

### Structure d’un projet Flask avec SSR

```
my_app/
├── app.py
├── templates/
│   ├── base.html
│   ├── index.html
├── static/
│   ├── style.css
├── data/
│   ├── data.json
│   ├── data.csv
└── database.db
```

### Exemple minimal

#### app.py

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

if __name__ == '__main__':
    app.run(debug=True)
```

#### templates/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Page d'accueil</title>
</head>
<body>
    <h1>Bienvenue sur le site !</h1>
</body>
</html>
```

---

## 🧩 3. Le moteur de template Jinja2

Jinja2 permet :

- d’injecter des variables Python dans le HTML
- de faire des boucles (`for`) et conditions (`if`)
- d’hériter de templates (`extends`, `block`)

### Exemple avec des données dynamiques

```python
@app.route('/users')
def users():
    data = ["Alice", "Bob", "Charlie"]
    return render_template('users.html', users=data)
```

```html
<!-- templates/users.html -->
<h2>Liste des utilisateurs</h2>
<ul>
    {% for user in users %}
        <li>{{ user }}</li>
    {% endfor %}
</ul>
```

---

## 🗂️ 4. Charger des données

### 🔸 JSON

```python
import json

@app.route('/from-json')
def json_data():
    with open('data/data.json') as f:
        infos = json.load(f)
    return render_template('infos.html', infos=infos)
```

### 🔸 CSV

```python
import csv

@app.route('/from-csv')
def csv_data():
    with open('data/data.csv') as f:
        reader = csv.DictReader(f)
        rows = list(reader)
    return render_template('table.html', rows=rows)
```

### 🔸 SQLite

```python
import sqlite3

@app.route('/from-db')
def from_db():
    conn = sqlite3.connect('database.db')
    c = conn.cursor()
    c.execute("SELECT * FROM users")
    users = c.fetchall()
    conn.close()
    return render_template('db_users.html', users=users)
```

---

## 🧾 5. Formulaires et entrées utilisateur

```python
from flask import request

@app.route('/form', methods=['GET', 'POST'])
def form_example():
    if request.method == 'POST':
        name = request.form['name']
        return f"Bonjour, {name} !"
    return render_template('form.html')
```

```html
<!-- form.html -->
<form method="POST">
    <input type="text" name="name" placeholder="Ton prénom" />
    <button type="submit">Envoyer</button>
</form>
```

---

## 📡 6. Différences avec Client-Side Rendering

| Fonction | SSR | CSR |
|----------|-----|-----|
| Affichage initial | Serveur | Navigateur |
| JavaScript requis | ❌ non | ✅ oui |
| Modifications DOM | Rechargement HTML | DOM dynamique via JS |
| Données dynamiques | Incluses dans le HTML | Récupérées par API |
| Framework typique | Flask, Django | React, Vue, Angular |

---

## 📚 Ressources complémentaires

- [📘 MDN - Développement Web côté serveur](https://developer.mozilla.org/fr/docs/Learn/Server-side)
- [📘 Flask Documentation](https://flask.palletsprojects.com/)
- [📘 Jinja2 Template Engine](https://jinja.palletsprojects.com/)
- [📘 JSON Python](https://docs.python.org/3/library/json.html)
- [📘 CSV Python](https://docs.python.org/3/library/csv.html)
- [📘 SQLite Python](https://docs.python.org/3/library/sqlite3.html)

---

## 🧠 À retenir

✅ Le SSR améliore l’accessibilité, les performances et le SEO.  
✅ Flask + Jinja2 = puissant et simple pour le rendu côté serveur.  
✅ Tu peux injecter des données dynamiques depuis divers formats.  
✅ Apprends à gérer les entrées utilisateur et les routes intelligemment.
