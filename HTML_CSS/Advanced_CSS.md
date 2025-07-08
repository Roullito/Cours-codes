# CSS Fundamentals and Advanced Concepts

## Overview
CSS (Cascading Style Sheets) are used to style HTML.

CSS works by:
- Selecting an HTML element
- Choosing a property to alter
- Applying a certain value

### Syntax
```css
element-selector {
  one-style-property: value;
  another-style-property: value;
}
```
A property + value is known as a **declaration**.

## Applying CSS to HTML

### External Stylesheet
Link a `.css` file:
```html
<link rel="stylesheet" href="styles.css">
```
Example:
```css
h1 {
  color: deepskyblue;
  text-align: center;
}
```

### Internal Stylesheet
Use `<style>` inside the `<head>`:
```html
<style>
  h1 {
    color: deepskyblue;
    text-align: center;
  }
</style>
```

### Inline Styles
Directly in the HTML tag:
```html
<h1 style="color: deepskyblue; text-align: center;">Hello CSS!</h1>
```

### Invalid CSS
Browsers ignore unknown properties or values. Use developer tools and validation tools to detect issues.

---

## Advanced CSS

### Selectors
```css
* {}                /* all elements */
section {}          /* section tags */
.my-class {}        /* class */
#my-id {}           /* id */
```
Prefer classes over IDs for reusability.

### Colors
Examples:
```css
p { color: red; }
p { color: #f00; }
p { color: rgb(255,0,0); }
p { color: hsl(0, 100%, 50%); }
```

### CSS Variables
```css
:root {
  --main-bg-color: blue;
}
body {
  color: var(--main-bg-color);
}
```

### Units and Values
- `px`: absolute
- `rem`: relative to `<html>`
```css
font-size: 2rem;
padding: 1.2rem 2rem;
```

### Line Height
```css
p {
  line-height: 1.5;
}
```

### Text Decoration
```css
a {
  text-decoration: line-through;
}
```

### Text Align
```css
p {
  text-align: center;
}
```

### Text Transform
```css
p {
  text-transform: lowercase;
}
```

### Letter Spacing
```css
a {
  letter-spacing: -0.4rem;
}
```

---

## Pseudo Classes
```css
a:link { color: green; }
a:visited { color: cadetblue; }
a:hover { text-decoration: underline; }
a:active { color: darkcyan; }
```

## CSS Reset / Normalize
Normalize.css and similar tools are used to ensure consistency across browsers.

---

## Box Model
```css
.box {
  box-sizing: border-box;
  width: 100px;
  height: 50px;
  padding: 30px;
  margin: 5px;
}
```

## Flow and Display
Block vs inline elements and flow layouts.

## Grid Systems
From traditional float-based layouts to CSS Grid.

---

## Pseudo Elements
```css
a::after {
  content: '→';
}
p::first-letter {
  font-size: 130%;
}
```

## Attribute Selectors
```css
a[href*="facebook"] { color: #3C5A99; }
a[href^="#"] { background-color: gold; }
a[href$=".org"] { color: red; }
```

## Backgrounds
```css
.box {
  background: linear-gradient(...);
}
```

## Borders
```css
.box {
  border: 2px double orange;
  border-radius: 12px;
}
```

## Positioning
```css
.component {
  position: sticky;
  top: 0;
}
```

## Transform
```css
transform: rotate(45deg);
transform: scale(2);
```

## Animation
```css
@keyframes example {
  from { background-color: blue; }
  to { background-color: red; }
}
.box {
  animation-name: example;
  animation-duration: 3s;
}
```

---

## Resources
- [MDN CSS Guide](https://developer.mozilla.org/en-US/docs/Web/CSS)
- [CSS Tricks](https://css-tricks.com/)
- [Animate.css](https://animate.style)
