markdown
# 🎨 Cours ULTRA COMPLET – CSS (Cascading Style Sheets)

## 🔰 Introduction à CSS

CSS (Cascading Style Sheets) est un **langage de style** qui permet de **contrôler la présentation** d’un document HTML. Il définit comment les éléments HTML doivent être affichés à l'écran, sur papier ou sur d'autres supports.

### Pourquoi CSS ?
- Séparation du **contenu** (HTML) et de la **présentation** (CSS)
- Réutilisabilité des styles
- Meilleure maintenabilité
- Responsive design (adapté aux mobiles/tablettes)

---

## 📄 Syntaxe de base

```css
selector {
  property: value;
}
```

Exemple :
```css
p {
  color: blue;
  font-size: 16px;
}
```

---

## 🧱 Les Selecteurs CSS

### Sélecteurs de base
- `*` : tous les éléments
- `element` : tous les `<p>`, `<h1>`, etc.
- `.class` : tous les éléments ayant cette classe
- `#id` : un élément unique avec cet id

### Sélecteurs combinés
- `div p` : tous les `<p>` dans un `<div>`
- `div > p` : `<p>` enfants directs de `<div>`
- `div + p` : `<p>` juste après un `<div>`
- `div ~ p` : tous les `<p>` après un `<div>`

### Sélecteurs d'attribut
```css
input[type="text"] { ... }
a[href^="https"] { ... }
```

---

## 🎨 Propriétés CSS importantes

### Couleurs
- `color`, `background-color`
- `rgba()`, `hex`, `hsl()`

### Texte
- `font-family`, `font-size`, `font-weight`
- `text-align`, `text-transform`, `line-height`

### Boîte (Box Model)
- `width`, `height`
- `padding`, `margin`, `border`
- `box-sizing: border-box`

### Display
- `display: block | inline | inline-block | flex | grid | none`
- `visibility: hidden`

---

## 🧭 Flexbox

```css
display: flex;
```

### Propriétés de container
- `flex-direction`, `justify-content`, `align-items`
- `gap`, `flex-wrap`

### Propriétés d’éléments enfants
- `flex-grow`, `flex-shrink`, `flex-basis`, `align-self`

---

## 🔲 Grid

```css
display: grid;
```

- `grid-template-columns`, `grid-template-rows`
- `grid-column`, `grid-row`
- `gap`, `place-items`

---

## 📱 Responsive Design

### Media Queries
```css
@media (max-width: 768px) {
  body {
    background-color: lightblue;
  }
}
```

---

## 🔁 Pseudo-classes & Pseudo-éléments

### Pseudo-classes
- `:hover`, `:focus`, `:nth-child(n)`
- `:first-child`, `:last-child`

### Pseudo-éléments
- `::before`, `::after`
- `::placeholder`, `::selection`

---

## 🧙‍♂️ Transitions & Animations

### Transitions
```css
transition: all 0.3s ease-in-out;
```

### Animations
```css
@keyframes slidein {
  from { transform: translateX(-100%); }
  to { transform: translateX(0); }
}
```

---

## 📦 Autres Concepts

- `z-index` (superposition)
- `position` (`static`, `relative`, `absolute`, `fixed`, `sticky`)
- `overflow`, `object-fit`
- `filter`, `backdrop-filter`

---

## 🧠 Bonnes pratiques

- Utiliser les classes (éviter les IDs pour le style)
- Externaliser le CSS dans un fichier `.css`
- Nommer les classes de manière explicite
- Utiliser des variables CSS (`--primary-color`)
- Préférer le `rem` ou `%` au `px` pour le responsive

---

## ✅ À retenir

- CSS est **essentiel pour styliser** vos pages HTML
- Flexbox et Grid sont **les piliers modernes** du layout
- Le responsive est **obligatoire** aujourd’hui
- La **lisibilité** et **modularité** du code CSS est clé pour maintenir un bon projet
