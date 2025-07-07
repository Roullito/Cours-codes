
# 🛠️ Cours Ultra Complet et Pédagogique : **Advanced HTML**

Bienvenue dans ce guide détaillé qui vous apprendra les bases solides **et avancées** de la structuration d'une page HTML propre, sémantique et accessible.

---

## 🔥 Objectifs pédagogiques

À la fin de ce cours, vous saurez :
✅ Structurer un document HTML5 complet et valide.
✅ Comprendre et utiliser les balises sémantiques.
✅ Différencier `div` et `span`.
✅ Manipuler correctement les titres (`h1-h6`).
✅ Créer des listes (ordonnées, non ordonnées, définitions).
✅ Utiliser et optimiser les images.
✅ Intégrer des tableaux accessibles.
✅ Embellir vos pages avec des vidéos, audios, iframes.
✅ Produire un HTML clair, propre et compréhensible.

---

## 🧩 1. Structure de base d'une page HTML

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Votre description en moins de 150 caractères.">
    <title>Page titre</title>
    <link rel="icon" href="favicon.ico">
</head>
<body>
    <!-- Contenu de la page -->
</body>
</html>
```

---

## 🗂️ 2. Les balises de structure sémantique

| Balise    | Rôle                          |
|------------|-------------------------------|
| `<header>` | Entête de la page ou d'une section |
| `<nav>`    | Menu de navigation            |
| `<main>`   | Contenu principal unique      |
| `<section>`| Regroupement thématique       |
| `<article>`| Contenu autonome réutilisable |
| `<aside>`  | Informations annexes          |
| `<footer>` | Pied de page ou de section    |

### Exemple minimal :
```html
<body>
    <header>Mon entête</header>
    <nav><a href="#">Accueil</a></nav>
    <main>
        <section>
            <article>Un article autonome</article>
        </section>
    </main>
    <footer>© 2024 MonSite</footer>
</body>
```

---

## 🎯 3. Hiérarchie des titres

Respectez l'ordre logique : `h1` pour le titre principal, puis `h2`, `h3`, etc.

```html
<h1>Page principale</h1>
<h2>Section</h2>
<h3>Sous-section</h3>
```

⚠️ Ne sautez jamais un niveau sans logique !

---

## 📑 4. Listes HTML

### Listes ordonnées :
```html
<ol>
    <li>Premier</li>
    <li>Deuxième</li>
</ol>
```

### Listes non ordonnées :
```html
<ul>
    <li>Élément</li>
    <li>Élément</li>
</ul>
```

### Listes de définitions :
```html
<dl>
    <dt>HTML</dt>
    <dd>Langage de structure de contenu</dd>
</dl>
```

---

## 🖼️ 5. Gestion des images

```html
<img src="img/photo.jpg" alt="Description de l'image" width="300" height="200">
```

✅ L'attribut `alt` est **obligatoire** pour l'accessibilité.
✅ Toujours optimiser la taille et le poids.
✅ Format recommandé : WebP ou PNG pour le web.

### Balise `<picture>` pour formats alternatifs :
```html
<picture>
    <source srcset="img/photo.webp" type="image/webp">
    <img src="img/photo.jpg" alt="Image fallback">
</picture>
```

---

## 📊 6. Tableaux accessibles

```html
<table>
    <caption>Données de contact</caption>
    <thead>
        <tr><th>Nom</th><th>Email</th></tr>
    </thead>
    <tbody>
        <tr><td>Jean</td><td>jean@mail.com</td></tr>
    </tbody>
</table>
```

✅ Utilisez toujours `<caption>` et `<th>` avec `scope` pour clarifier.

---

## 🎥 7. Médias : vidéo et audio

### Vidéo :
```html
<video src="video.mp4" controls>
    Votre navigateur ne supporte pas la vidéo.
</video>
```

### Audio :
```html
<audio src="audio.mp3" controls>
    Audio non supporté.
</audio>
```

---

## 🌐 8. Iframe (intégration de contenu externe)

```html
<iframe src="https://www.example.com" width="600" height="400" title="Exemple intégré"></iframe>
```

⚠️ À utiliser avec parcimonie pour des raisons de performance.

---

## 🖋️ 9. Autres éléments essentiels

- `<a href="#">Lien</a>` — Lien hypertexte.
- `<em>` — Mise en emphase.
- `<strong>` — Mise en importance.
- `<abbr title="HyperText Markup Language">HTML</abbr>` — Abréviation.
- `<code>` — Code informatique inline.
- `<blockquote>` — Citation longue.
- `<q>` — Citation courte inline.
- `<hr>` — Séparateur horizontal.
- `<br>` — Saut de ligne (à éviter pour la mise en page).

---

## 🎨 10. Meilleures pratiques

✅ Structure claire et hiérarchique.
✅ HTML sémantique avant tout.
✅ Accessibilité systématique.
✅ Poids des images optimisé.
✅ Validez votre code : [W3C Validator](https://validator.w3.org).

---

## ✅ Conclusion

HTML est le **squelette** de vos pages web. Une bonne structure garantit un site :
- Compréhensible pour les navigateurs.
- Accessible pour tous.
- Facile à maintenir et à styliser.

**Prochaine étape : CSS et mise en forme !**

---

## 📚 Ressources complémentaires

- [MDN HTML](https://developer.mozilla.org/fr/docs/Web/HTML)
- [HTML5 Cheat Sheet](https://websitesetup.org/html5-cheat-sheet)
- [HTML Reference](https://htmlreference.io)
- [Can I use - HTML Elements](https://caniuse.com)

---

*Techium* — Formation professionnelle au web, 2024.
