# 📝 CSS Cheat Sheet – Detailed

## Table of Contents
- [Selectors](#selectors)
- [Colors & Units](#colors--units)
- [Text & Fonts](#text--fonts)
- [Box Model (Margin, Padding, Border)](#box-model)
- [Backgrounds](#backgrounds)
- [Display & Positioning](#display--positioning)
- [Flexbox](#flexbox)
- [CSS Grid](#css-grid)
- [Responsive Design](#responsive-design)
- [Transitions & Animations](#transitions--animations)
- [Pseudo-classes & Pseudo-elements](#pseudo-classes--pseudo-elements)
- [Advanced Properties](#advanced-properties)
- [Variables (Custom Properties)](#variables)
- [Useful Shorthands](#useful-shorthands)
- [Debugging Tips](#debugging-tips)
- [References](#references)

---

## Selectors

```css
/* Element */
h1 { ... }

/* Class */
.my-class { ... }

/* ID */
#my-id { ... }

/* Multiple Selectors */
h1, h2, .title { ... }

/* Descendant */
article p { ... }

/* Child */
nav > ul { ... }

/* Attribute */
input[type="text"] { ... }

/* Universal */
* { ... }

```
---

## Colors & Units

```css
color: #ff3300;      /* Hex */
color: rgb(255,0,0); /* RGB */
color: rgba(255,0,0,0.8); /* RGBA */
color: hsl(0,100%,50%);   /* HSL */
color: var(--main-color); /* Variable */

/* Units */
width: 50px;
width: 80%;
width: 2rem; /* Relative to root */
width: 1.5em; /* Relative to parent font-size */
```
---

## Text & Fonts

```css
font-family: 'Roboto', Arial, sans-serif;
font-size: 1.2rem;
font-weight: bold;   /* or 400, 700 */
font-style: italic;
text-align: center;
text-transform: uppercase;
letter-spacing: 2px;
line-height: 1.5;
text-shadow: 1px 1px 4px #000;
text-decoration: underline;
overflow-wrap: break-word; /* or word-break */
```
---

## Box Model

```css
margin: 20px; /* all sides */
margin: 10px 20px; /* vertical | horizontal */
margin: 10px 15px 20px 5px; /* top right bottom left */

padding: 1rem 2rem;

border: 1px solid #222;
border-radius: 8px;
box-sizing: border-box; /* recommended! */

width: 250px;
height: 100px;
box-shadow: 0 2px 8px rgba(0,0,0,0.15);
```
---

## Backgrounds

```css
background-color: #fafafa;
background-image: url('image.jpg');
background-repeat: no-repeat;
background-size: cover;
background-position: center;
background: linear-gradient(90deg, #f00, #00f);
background-attachment: fixed;
```
---

## Display & Positioning

```css
display: block | inline | inline-block | flex | grid | none;
visibility: visible | hidden;

position: static | relative | absolute | fixed | sticky;
top: 20px;
left: 30px;
z-index: 10;
overflow: auto | hidden | scroll;

float: left | right;
clear: both;

cursor: pointer;
```
---

## Flexbox

```css
display: flex;
flex-direction: row | column | row-reverse | column-reverse;
justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
align-items: stretch | flex-start | flex-end | center | baseline;
gap: 1rem;

/* Child */
flex: 1; /* grow/shrink */
align-self: flex-end;
order: 2;
```
---

### Example:

```css
.container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
}
```
---

## CSS Grid

```css
display: grid;
grid-template-columns: 1fr 2fr 1fr;
grid-template-rows: 100px auto;
grid-gap: 10px;

grid-column: 1 / 3;
grid-row: 2 / span 2;
justify-items: center;
align-items: stretch;
place-items: center;

/* Shorthand */
grid: auto-flow / 1fr 1fr 1fr;
```
---

## Responsive Design

```css
/* Media Query */
@media (max-width: 600px) {
  .sidebar {
    display: none;
  }
}

/* Viewport Units */
width: 100vw;
height: 100vh;
```
---

## Transitions & Animations

```css
/* Transition */
transition: all 0.3s ease-in-out;
transition-property: background, color;
transition-delay: 0.2s;
transition-timing-function: cubic-bezier(.25,.8,.25,1);

/* Animation */
@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

.element {
  animation: fadeIn 2s forwards;
  animation-delay: 0.5s;
}
```
---

## Pseudo-classes & Pseudo-elements

```css
/* Pseudo-classes */
a:hover { color: #007bff; }
input:focus { border-color: #333; }
li:first-child { font-weight: bold; }
p:last-of-type { margin-bottom: 0; }

/* Pseudo-elements */
p::first-line { color: red; }
p::after { content: " ✔️"; color: green; }
```
---

## Advanced Properties

```css
object-fit: cover | contain;
object-position: center;
filter: blur(3px) brightness(0.8);
backdrop-filter: blur(10px);

clip-path: circle(50% at 50% 50%);
box-shadow: 0 4px 12px rgba(0,0,0,0.2);

scroll-behavior: smooth;
user-select: none | text | all | auto;
pointer-events: none | auto;
```
---

## Variables (Custom Properties)

```css
:root {
  --main-color: #007bff;
  --spacing: 1.5rem;
}

.box {
  color: var(--main-color);
  margin-bottom: var(--spacing);
}
```
---

## Useful Shorthands

```css
margin: 10px 20px;          /* top/bottom left/right */
padding: 1em 2em 1em 0;     /* top right bottom left */
border: 2px dashed #333;    /* width style color */
background: #fff url('bg.jpg') no-repeat center/cover;
font: italic small-caps bold 18px/1.4 'Arial', sans-serif;
transition: background 0.5s;
```
---

## Debugging Tips
*outline: 2px solid red;* pour voir les bordures des éléments.

*{ box-sizing: border-box; }* pour éviter les surprises sur la largeur/hauteur.

Utilise l’inspecteur du navigateur (F12).

Ajoute *background-color: rgba(0,255,0,0.1);* temporairement pour débugger.

## References
MDN CSS Reference

CSS Tricks – Complete Guide to Flexbox

CSS Tricks – Complete Guide to Grid
