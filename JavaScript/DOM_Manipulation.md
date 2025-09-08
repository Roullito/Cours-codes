
# 🌐 JavaScript DOM Manipulation - Cours Ultra Complet

## 📚 Introduction

Le DOM (Document Object Model) est une interface de programmation pour les documents HTML et XML. Il représente la page de manière structurée afin que les langages de script comme JavaScript puissent accéder, manipuler, modifier ou supprimer les éléments du document.

Ce cours couvre **tous les aspects fondamentaux et avancés** de la manipulation du DOM avec JavaScript moderne.

---

## 🧠 Objectifs pédagogiques

À la fin de ce cours, tu sauras :
- Sélectionner des éléments HTML en JavaScript
- Différencier les sélecteurs ID, classe, et nom de balise
- Modifier le style d’un élément HTML via JavaScript
- Lire et modifier le contenu HTML ou textuel d’un élément
- Ajouter, modifier, supprimer des éléments du DOM
- Gérer des événements utilisateurs (clics, survols, etc.)
- Faire des requêtes réseau (XHR et Fetch API)
- Corriger les erreurs fréquentes de manipulation DOM

---

## 🏗️ Le DOM : c’est quoi ?

Le **DOM** est une **représentation arborescente** d’un document. Chaque balise HTML devient un **nœud** (node), que l’on peut parcourir, modifier, supprimer ou insérer.

```html
<body>
  <div id="container">
    <p class="text">Hello World</p>
  </div>
</body>
```

Ce code est représenté en JavaScript par une arborescence : `document.body -> div#container -> p.text`

---

## 🔍 Sélection des éléments DOM

### 1. `getElementById()`
```js
const title = document.getElementById("main-title");
```

### 2. `getElementsByClassName()`
```js
const items = document.getElementsByClassName("list-item");
```

### 3. `getElementsByTagName()`
```js
const paragraphs = document.getElementsByTagName("p");
```

### 4. `querySelector()` (sélecteur CSS - premier match)
```js
const firstParagraph = document.querySelector(".text");
```

### 5. `querySelectorAll()` (tous les matchs)
```js
const allItems = document.querySelectorAll("ul > li");
```

---

## 🎨 Modifier le style d’un élément

```js
const element = document.querySelector(".box");
element.style.backgroundColor = "blue";
element.style.fontSize = "18px";
```

---

## 📝 Modifier le contenu HTML ou textuel

```js
const el = document.querySelector("#msg");
el.textContent = "Bonjour !";      // Texte brut
el.innerHTML = "<strong>Salut</strong>"; // HTML interprété
```

---

## 🧱 Modifier la structure du DOM

### ➕ Ajouter un élément
```js
const newDiv = document.createElement("div");
newDiv.textContent = "Je suis nouveau !";
document.body.appendChild(newDiv);
```

### 🧹 Supprimer un élément
```js
const oldDiv = document.getElementById("to-remove");
oldDiv.remove();
```

### 🔁 Remplacer un élément
```js
parent.replaceChild(newElement, oldElement);
```

---

## 🎧 Écouter des événements

```js
const button = document.querySelector("button");
button.addEventListener("click", () => {
  alert("Clic détecté !");
});
```

Autres événements : `mouseover`, `keyup`, `change`, `submit`, `scroll`, etc.

---

## 🌐 Requêtes HTTP

### 1. Avec **XMLHttpRequest** (ancien)
```js
const xhr = new XMLHttpRequest();
xhr.open("GET", "https://api.example.com/data");
xhr.onload = () => {
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.responseText));
  }
};
xhr.send();
```

### 2. Avec **Fetch API** (moderne)
```js
fetch("https://api.example.com/data")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error("Erreur :", error));
```

---

## ⚠️ Déboguer le JavaScript DOM

- Vérifier que le script est chargé **après** les éléments (`defer`, ou placer le `<script>` en bas du HTML).
- Utiliser `console.log()` pour inspecter les nœuds
- S'assurer que les sélecteurs sont corrects (`#id`, `.class`, `tag`)
- Utiliser les outils développeur du navigateur

---

## 🛠️ Exemples avancés

### Exemple : Ajout dynamique d’un élément au clic

```js
const addButton = document.querySelector("#add");
const container = document.querySelector("#container");

addButton.addEventListener("click", () => {
  const newPara = document.createElement("p");
  newPara.textContent = "Nouveau paragraphe !";
  container.appendChild(newPara);
});
```

---

## 📖 Ressources complémentaires

- [MDN - Introduction au DOM](https://developer.mozilla.org/fr/docs/Web/API/Document_Object_Model/Introduction)
- [CSS Diner - Entraîne-toi aux sélecteurs](https://flukeout.github.io/)
- [Fetch API - MDN](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API)
- [DOM Events](https://developer.mozilla.org/fr/docs/Web/API/Event)

---

## ✅ Conclusion

La manipulation du DOM avec JavaScript est **l’un des piliers** du développement web. Maîtriser ces bases permet de rendre les pages **interactives, dynamiques, et réactives aux actions utilisateurs**.

➡️ À mesure que tu progresses, tu pourras combiner DOM, CSS, et API pour créer des applications front-end complètes !
