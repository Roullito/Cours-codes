markdown
# 🟨 JavaScript - Warm up

## 🎯 Objectifs pédagogiques

À la fin de ce cours, vous serez capable d'expliquer et d'appliquer les concepts fondamentaux suivants :

- Pourquoi JavaScript est un langage puissant
- Comment exécuter un script JavaScript
- Comment déclarer des variables avec `var`, `let` et `const`
- Comprendre les types de données en JavaScript
- Utiliser les instructions conditionnelles `if`, `else if`, `else`
- Ajouter des commentaires dans son code
- Affecter des valeurs aux variables
- Utiliser les boucles `while` et `for`
- Comprendre les instructions `break` et `continue`
- Déclarer et appeler des fonctions
- Comprendre le scope (portée) des variables
- Utiliser les opérateurs arithmétiques
- Manipuler des objets et tableaux
- Importer des fichiers ou modules

---

## 🚀 Pourquoi JavaScript ?

JavaScript est un langage de programmation **interprété**, **multi-paradigme** et **orienté objet**. Il est :
- Léger, rapide à exécuter
- Largement utilisé pour le développement **frontend** (navigateur) et **backend** (Node.js)
- Compatible avec la majorité des navigateurs

---

## 🧰 Exécution d’un script

Pour exécuter un fichier `.js` :

```bash
$ node fichier.js
```

---

## 🔤 Déclaration de variables

### `var`, `let`, `const`

| Mot-clé | Scope          | Re-déclarable | Re-assignable |
|--------:|----------------|---------------|----------------|
| `var`   | fonction        | ✅ Oui         | ✅ Oui          |
| `let`   | bloc (`{}`)     | ❌ Non         | ✅ Oui          |
| `const` | bloc (`{}`)     | ❌ Non         | ❌ Non          |

Exemples :
```js
let age = 25;
const nom = "Alice";
```

---

## 🔢 Types de données

- `Number`
- `String`
- `Boolean`
- `Object` (incl. Arrays)
- `null`
- `undefined`
- `Symbol` (avancé)
- `BigInt`

---

## 📐 Opérateurs et priorités

```js
+  -  *  /  %   // arithmétiques
== === != !==   // comparaison
&& || !         // logiques
```

L’ordre des opérations suit une **précédence** comme en mathématiques. Utilisez des **parenthèses** pour forcer une priorité.

---

## 🧭 Contrôle du flux

### `if`, `else if`, `else`

```js
if (age > 18) {
  console.log("Majeur");
} else {
  console.log("Mineur");
}
```

### `while` et `for`

```js
let i = 0;
while (i < 3) {
  console.log(i);
  i++;
}

for (let j = 0; j < 3; j++) {
  console.log(j);
}
```

### `break` et `continue`

- `break` arrête totalement la boucle
- `continue` passe à l’itération suivante

---

## 🧩 Fonctions

```js
function saluer(nom) {
  console.log("Bonjour " + nom);
}

saluer("Luc");
```

Une fonction sans `return` retourne `undefined`.

---

## 📦 Objets et tableaux

### Objet

```js
let personne = {
  nom: "Alice",
  age: 30
};
console.log(personne.nom);
```

### Tableau

```js
let fruits = ["pomme", "banane"];
console.log(fruits[0]);
```

---

## 🧠 Scope des variables

- `var` a un **scope de fonction**
- `let` et `const` ont un **scope de bloc**

```js
{
  let local = "ici";
}
console.log(local); // Erreur !
```

---

## 🧭 Modules

Importation de fichiers avec `require()` (CommonJS) :

```js
const monModule = require('./module.js');
```

---

## ✅ Règles de style

Utilisez `semistandard` (StandardJS + point-virgules) :

```bash
$ sudo npm install semistandard --global
$ semistandard fichier.js
```

---

## 🖥️ Environnement recommandé

- Node.js 14 :
```bash
$ curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
$ sudo apt install -y nodejs
```

- Éditeurs recommandés : `vim`, `emacs`, `VSCode`

---

## 📚 Ressources utiles

- [JavaScript.info](https://javascript.info)
- [MDN - JavaScript](https://developer.mozilla.org/fr/docs/Web/JavaScript)
- [Modern JS - ES6+](https://exploringjs.com/)
- [AirBnB JavaScript Style Guide](https://github.com/airbnb/javascript)

---

## 🔚 Conclusion

Ce "warm-up" JavaScript vous permettra de maîtriser les bases pour :

- Automatiser des scripts comme avec Python
- Manipuler le DOM et créer des interactions frontend
- Construire des composants JS dans votre projet AirBnB

Pratiquez et amusez-vous bien !
