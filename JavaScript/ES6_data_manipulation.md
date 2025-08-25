# 📘 Cours Complet : ES6 - Data Manipulation

## 🎯 Objectifs d'apprentissage

À la fin de ce cours, vous serez capable de : - Comprendre les
différentes structures de données en JavaScript (objets, tableaux, maps,
sets). - Manipuler les données efficacement avec les fonctionnalités ES6
(et +). - Utiliser les méthodes modernes pour transformer, filtrer et
agréger des données. - Écrire du code plus concis, lisible et performant
grâce aux nouvelles fonctionnalités.

------------------------------------------------------------------------

## 🚀 Introduction à ES6 et la manipulation de données

ECMAScript 6 (ES6), aussi appelé ES2015, a introduit de nombreuses
fonctionnalités puissantes pour manipuler les données en JavaScript.\
Ce cours va couvrir pas à pas :

1.  Les **Tableaux** (Arrays)
2.  Les **Objets**
3.  Les **Maps** et **Sets**
4.  Les **Opérateurs modernes** (spread, rest, destructuring)
5.  Les **Méthodes de manipulation** (`map`, `filter`, `reduce`, etc.)
6.  Les **Cas pratiques** : transformation, regroupement, recherche.

------------------------------------------------------------------------

## 📚 1. Manipulation des Tableaux (Arrays)

### Création de tableaux

``` js
const fruits = ["pomme", "banane", "orange"];
const nombres = [1, 2, 3, 4, 5];
```

### Boucles modernes

-   **for...of**

``` js
for (const fruit of fruits) {
  console.log(fruit);
}
```

-   **forEach**

``` js
fruits.forEach((fruit, index) => console.log(index, fruit));
```

### Méthodes utiles

-   **map()** → transformation

``` js
const doubles = nombres.map(n => n * 2);
```

-   **filter()** → filtrage

``` js
const pairs = nombres.filter(n => n % 2 === 0);
```

-   **reduce()** → accumulation

``` js
const somme = nombres.reduce((acc, n) => acc + n, 0);
```

-   **find()**

``` js
const fruit = fruits.find(f => f.startsWith("b"));
```

-   **some() / every()**

``` js
const auMoinsUnPair = nombres.some(n => n % 2 === 0);
const tousPairs = nombres.every(n => n % 2 === 0);
```

------------------------------------------------------------------------

## 📚 2. Manipulation des Objets

### Création et accès

``` js
const utilisateur = {
  nom: "Alice",
  age: 25,
  ville: "Paris"
};

console.log(utilisateur.nom); // Dot notation
console.log(utilisateur["age"]); // Bracket notation
```

### Parcourir un objet

-   **Object.keys()**

``` js
Object.keys(utilisateur).forEach(key => console.log(key));
```

-   **Object.values()**

``` js
Object.values(utilisateur).forEach(value => console.log(value));
```

-   **Object.entries()**

``` js
Object.entries(utilisateur).forEach(([k, v]) => console.log(k, v));
```

### Fusion d'objets avec le spread operator

``` js
const adresse = { ville: "Paris", pays: "France" };
const profil = { ...utilisateur, ...adresse };
```

------------------------------------------------------------------------

## 📚 3. Maps et Sets

### Map (clé/valeur)

``` js
const map = new Map();
map.set("nom", "Alice");
map.set("age", 25);

console.log(map.get("nom")); // Alice
console.log(map.has("age")); // true
```

### Set (valeurs uniques)

``` js
const set = new Set([1, 2, 3, 3, 4]);
console.log(set); // {1, 2, 3, 4}
```

------------------------------------------------------------------------

## 📚 4. Opérateurs modernes

### Destructuring

``` js
const [a, b] = [1, 2];
const { nom, age } = utilisateur;
```

### Spread operator

``` js
const nombres2 = [...nombres, 6, 7];
```

### Rest parameter

``` js
function somme(...args) {
  return args.reduce((a, b) => a + b, 0);
}
```

------------------------------------------------------------------------

## 📚 5. Cas pratiques

### Exemple 1 : Transformer un tableau d'objets

``` js
const users = [
  { nom: "Alice", age: 25 },
  { nom: "Bob", age: 30 }
];

const noms = users.map(u => u.nom);
```

### Exemple 2 : Grouper par propriété

``` js
const groupByAge = users.reduce((acc, u) => {
  acc[u.age] = acc[u.age] || [];
  acc[u.age].push(u.nom);
  return acc;
}, {});
```

### Exemple 3 : Chaînage de méthodes

``` js
const resultat = nombres
  .filter(n => n > 2)
  .map(n => n * 10)
  .reduce((a, b) => a + b, 0);
```

------------------------------------------------------------------------

## ✅ Conclusion

-   ES6 offre un arsenal puissant pour manipuler les données.\
-   Les **tableaux** et **objets** restent centraux, mais **Maps** et
    **Sets** sont idéaux pour des cas particuliers.\
-   Les opérateurs **spread/rest/destructuring** simplifient énormément
    le code.\
-   Les méthodes comme **map, filter, reduce** rendent le code plus
    expressif et concis.

👉 La pratique est essentielle : essayez d'appliquer chaque concept dans
de petits projets (gestion de liste, transformation de données API,
etc.).

------------------------------------------------------------------------

## 📖 Ressources

-   [MDN - Array
    methods](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array)
-   [MDN -
    Object](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Object)
-   [MDN -
    Map](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Map)
-   [MDN -
    Set](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Set)
-   [ECMAScript 6 Guide](https://262.ecma-international.org/6.0/)
