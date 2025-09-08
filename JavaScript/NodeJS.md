markdown
# 🌐 NodeJS Basics – Cours Ultra Complet et Pédagogique

Ce document est un **cours détaillé** sur les bases de **NodeJS**.  
Il couvre les fondamentaux que tu dois maîtriser pour travailler avec NodeJS, Express, et l’écosystème moderne.

---

## 1. Introduction à NodeJS

### 🔹 Qu’est-ce que NodeJS ?
- **Node.js** est un environnement d’exécution JavaScript basé sur le moteur **V8** de Google Chrome.
- Il permet d’exécuter du JavaScript **côté serveur** (hors navigateur).
- Atouts principaux :
  - **Asynchrone** : il ne bloque pas le thread principal.
  - **Orienté événements** : chaque action déclenche un événement auquel tu peux réagir.
  - **Rapide** : conçu pour des applications réseau performantes.

👉 Exemple minimal :

**hello.js**
```js
console.log("Hello NodeJS!");
```

Exécution :

```bash
node hello.js
```

Résultat :
```
Hello NodeJS!
```

---

## 2. Les Modules dans NodeJS

### 🔹 Importer / Exporter un module
Chaque fichier est un **module**.  
Tu peux partager des fonctions/objets avec `module.exports` et les importer avec `require()`.

**export.js**
```js
function greet(name) {
  return `Hello, ${name}`;
}
module.exports = greet;
```

**import.js**
```js
const greet = require('./export');
console.log(greet("Holberton"));
```

Résultat :
```
Hello, Holberton
```

### 🔹 Les modules intégrés
NodeJS possède des modules intégrés très utiles :  
- `fs` → gérer les fichiers  
- `http` → créer des serveurs  
- `path` → manipuler les chemins  
- `process` → interagir avec l’environnement  

Exemple avec **path** :
```js
const path = require('path');

console.log(path.basename('/foo/bar/baz/asdf/quux.html')); // quux.html
```

---

## 3. Utiliser `process`

### 🔹 Accéder aux arguments de la ligne de commande
Le tableau `process.argv` contient les arguments passés au script.

**args.js**
```js
console.log(process.argv);
```

Exécution :
```bash
node args.js Hello World
```

Résultat :
```
[ '/usr/bin/node', '/path/args.js', 'Hello', 'World' ]
```

👉 On peut extraire les arguments utiles :
```js
const args = process.argv.slice(2);
console.log("Arguments:", args);
```

### 🔹 Variables d’environnement
Tu peux accéder aux variables système via `process.env` :

```js
console.log("Mon PATH :", process.env.PATH);
```

---

## 4. Lire des fichiers avec NodeJS

### 🔹 Lecture synchrone (bloquante)
```js
const fs = require('fs');

const data = fs.readFileSync('database.csv', 'utf8');
console.log(data);
```

### 🔹 Lecture asynchrone (non bloquante)
```js
const fs = require('fs');

fs.readFile('database.csv', 'utf8', (err, data) => {
  if (err) {
    console.error("Erreur :", err);
    return;
  }
  console.log(data);
});
```

👉 **Bonne pratique** : privilégier l’asynchrone pour ne pas bloquer l’application.

---

## 5. Créer un serveur HTTP avec Node

### 🔹 Exemple minimal
```js
const http = require('http');

const app = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello Holberton School!');
});

app.listen(1245, () => {
  console.log('Server running at http://localhost:1245/');
});
```

Résultat avec `curl localhost:1245` :
```
Hello Holberton School!
```

---

## 6. Express : framework pour NodeJS

### 🔹 Exemple simple avec Express
```js
const express = require('express');
const app = express();
const port = 1245;

app.get('/', (req, res) => {
  res.send('Hello Holberton School!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```

👉 Plus simple que `http` natif : Express gère les routes, middlewares, erreurs, etc.

---

## 7. Routage avancé avec Express

### 🔹 Exemple avec plusieurs routes
```js
app.get('/', (req, res) => res.send('Accueil'));
app.get('/students', (req, res) => res.send('Liste des étudiants'));
app.get('/students/:major', (req, res) => {
  const { major } = req.params;
  res.send(`Liste des étudiants en ${major}`);
});
```

Test :
```bash
curl localhost:1245/students/CS
```

Résultat :
```
Liste des étudiants en CS
```

---

## 8. Babel & ES6 dans Node

### 🔹 Pourquoi Babel ?
NodeJS ne supporte pas toujours toutes les nouvelles fonctionnalités ES6+.  
Avec **Babel**, on peut utiliser `import/export` au lieu de `require/module.exports`.

Exemple :
```js
// utils.js
export function sum(a, b) {
  return a + b;
}

// main.js
import { sum } from './utils.js';
console.log(sum(2, 3));
```

Configuration typique : `babel.config.js`

```js
module.exports = {
  presets: [
    ['@babel/preset-env', { targets: { node: 'current' } }],
  ],
};
```

---

## 9. Nodemon pour un dev plus rapide

### 🔹 Qu’est-ce que Nodemon ?
C’est un outil qui redémarre automatiquement ton serveur quand tu modifies ton code.

Installation :
```bash
npm install --save-dev nodemon
```

Ajoute dans `package.json` :
```json
"scripts": {
  "dev": "nodemon index.js"
}
```

Lance :
```bash
npm run dev
```

👉 Chaque changement relance ton serveur sans intervention manuelle.

---

## 10. Tests avec Mocha (ou Jest)

### 🔹 Exemple simple avec Mocha
**math.js**
```js
function add(a, b) {
  return a + b;
}
module.exports = add;
```

**test.js**
```js
const assert = require('assert');
const add = require('./math');

describe('Addition', () => {
  it('2 + 3 = 5', () => {
    assert.equal(add(2, 3), 5);
  });
});
```

Exécution :
```bash
npx mocha test.js
```

Résultat :
```
  Addition
    ✓ 2 + 3 = 5
```

---

# ✅ Conclusion

Avec ce cours tu sais maintenant :
- Exécuter du JavaScript avec NodeJS
- Utiliser les modules et `process`
- Lire des fichiers (sync/async)
- Créer un serveur HTTP (natif et Express)
- Gérer des routes avancées
- Utiliser Babel pour ES6+
- Développer plus vite avec Nodemon
- Écrire des tests avec Mocha/Jest

👉 C’est exactement la base que ton projet **Holberton - NodeJS Basics** attend de toi.
