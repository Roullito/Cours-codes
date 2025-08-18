# 📘 Bible ES6+ (ECMAScript 2015 et extensions proches)

Ce document est une **référence complète et détaillée** sur ES6
(ECMAScript 2015), avec ajout des fonctionnalités essentielles des
versions proches (ES2016-2017) pour avoir un panorama complet.

------------------------------------------------------------------------

# 1. Déclarations de variables : `let`, `const`, `var`

### Avant ES6

``` js
var x = 10;
```

-   `var` → portée **fonctionnelle**, pas de bloc scope, redéclarable.

### ES6 : `let` et `const`

``` js
let a = 10;   // portée de bloc, réassignable
const b = 20; // portée de bloc, non réassignable
```

⚠️ **TDZ (Temporal Dead Zone)** : une variable `let` ou `const` ne peut
être utilisée avant sa déclaration.

``` js
console.log(a); // ReferenceError
let a = 5;
```

------------------------------------------------------------------------

# 2. Fonctions fléchées (Arrow Functions)

``` js
const add = (x, y) => x + y;
```

Caractéristiques : - Syntaxe concise - `this` **lexical** (hérite du
contexte parent) - Ne peuvent pas être utilisées comme constructeurs

------------------------------------------------------------------------

# 3. Template Literals

``` js
let name = "Alice";
console.log(`Hello ${name}`);
```

-   Support multi-lignes
-   Interpolation d'expressions
-   Tagged templates pour transformer la chaîne

------------------------------------------------------------------------

# 4. Paramètres par défaut et Rest/Spread

``` js
function greet(name = "inconnu") { return `Bonjour ${name}`; }
function sum(...nums) { return nums.reduce((a,b)=>a+b,0); }
let arr = [1,2]; let arr2 = [...arr,3,4];
```

------------------------------------------------------------------------

# 5. Destructuring

### Tableaux

``` js
let [x, y] = [1, 2];
```

### Objets

``` js
let {name, age} = {name:"Bob", age:25};
```

Avec valeurs par défaut et alias :

``` js
let {n: nom = "inconnu"} = {};
```

------------------------------------------------------------------------

# 6. Classes et héritage

``` js
class Person {
  constructor(name) { this.name = name; }
  sayHi() { console.log(`Hi ${this.name}`); }
}

class Student extends Person {
  constructor(name, level) { super(name); this.level = level; }
}
```

------------------------------------------------------------------------

# 7. Modules ES6

### export.js

``` js
export const pi = 3.14;
export default function hello(){ console.log("Hello"); }
```

### import.js

``` js
import hello, {pi} from "./export.js";
```

⚡ Possibilité d'aliasing :

``` js
import {pi as PI} from "./export.js";
```

Modules dynamiques (ES2020) :

``` js
const mod = await import("./export.js");
```

------------------------------------------------------------------------

# 8. Promises et Async/Await

``` js
let p = new Promise((res,rej)=> res(42));
p.then(console.log).catch(console.error);
```

Méthodes utilitaires : - `Promise.all([p1,p2])` -
`Promise.race([p1,p2])` - `Promise.resolve(val)` - `Promise.reject(err)`

Async/Await (ES2017) :

``` js
async function test() {
  try { let data = await p; console.log(data); }
  catch(e){ console.error(e); }
}
```

------------------------------------------------------------------------

# 9. Symboles et métaprogrammation

``` js
let id = Symbol("id");
let obj = {[id]: 123};
```

Registre global :

``` js
let s1 = Symbol.for("shared");
let s2 = Symbol.for("shared");
console.log(s1 === s2); // true
```

------------------------------------------------------------------------

# 10. Iterators et Generators

``` js
function* gen(){ yield 1; yield 2; }
let g = gen();
console.log(g.next()); // {value:1,done:false}
```

`yield*` permet la délégation :

``` js
function* g2(){ yield* [1,2,3]; }
```

------------------------------------------------------------------------

# 11. Collections : Set, Map, WeakMap, WeakSet

``` js
let set = new Set([1,2,2]); // {1,2}
let map = new Map([["a",1]]);
```

Méthodes : - `set.has(v)`, `set.add(v)`, `set.delete(v)` -
`map.set(k,v)`, `map.get(k)`, `map.keys()`, `map.values()`

WeakMap/WeakSet → clés objets uniquement, non énumérables → gestion
mémoire.

------------------------------------------------------------------------

# 12. Boucle `for...of`

``` js
for (let val of [10,20]) console.log(val);
```

------------------------------------------------------------------------

# 13. Nouveaux outils pour objets

``` js
Object.assign({}, {a:1}, {b:2});
Object.is(NaN, NaN); // true (contrairement à ===)
Object.keys(obj); Object.values(obj); Object.entries(obj);
Object.getOwnPropertySymbols(obj);
```

Améliorations littéraux :

``` js
let x=1; let obj={x, say(){console.log("hi")}};
```

------------------------------------------------------------------------

# 14. Nouveaux outils String

``` js
"abc".includes("a");    // true
"abc".startsWith("a"); // true
"abc".endsWith("c");   // true
"hi".repeat(3);        // "hihihi"
"5".padStart(3,"0");   // "005"
```

------------------------------------------------------------------------

# 15. Nouveaux outils Array

``` js
[1,2,3].find(x=>x>1);       // 2
[1,2,3].findIndex(x=>x>1);  // 1
[1,2,3].fill(0,1);          // [1,0,0]
[1,2,3].copyWithin(1,0);    // [1,1,2]
Array.from("abc");          // ["a","b","c"]
Array.of(1,2);              // [1,2]
```

------------------------------------------------------------------------

# 16. Nouveaux outils Number/Math

``` js
Number.isNaN(NaN);       // true
Number.isInteger(10.5);  // false
Math.trunc(4.9);         // 4
Math.sign(-5);           // -1
Math.cbrt(27);           // 3
Math.hypot(3,4);         // 5
```

------------------------------------------------------------------------

# 17. Proxies et Reflect API

``` js
let obj = {a:1};
let proxy = new Proxy(obj, {
  get(t,p){ console.log("get",p); return t[p]; }
});
console.log(proxy.a);
```

Reflect → méthodes natives pour métaprogrammation :

``` js
Reflect.get(obj,"a");
Reflect.set(obj,"a",42);
```

------------------------------------------------------------------------

# 18. Tail Call Optimization (TCO)

ES6 spécifie l'optimisation des appels de fonction en récursion
terminale, mais peu supportée en pratique.

``` js
"use strict";
function fact(n, acc=1){
  if(n<=1) return acc;
  return fact(n-1, n*acc); // TCO possible
}
```

------------------------------------------------------------------------

# 19. Iterables personnalisés

``` js
let range = {
  from:1, to:3,
  [Symbol.iterator](){
    let i=this.from;
    return { next:()=> ({value: i<=this.to ? i++ : undefined, done: i>this.to}) };
  }
};
for(let v of range) console.log(v);
```

------------------------------------------------------------------------

# 20. Différences ES6 vs ES2016+

-   **ES2016** → `Array.prototype.includes`, `**` (exponentiation).\
-   **ES2017** → `Object.entries`, `Object.values`, `async/await`.

------------------------------------------------------------------------

# ✅ Conclusion

ES6 a révolutionné JavaScript :\
- Il a apporté **portée de bloc, classes, modules, Promises**.\
- Il a introduit la **métaprogrammation** avec Proxy et Reflect.\
- Il a enrichi **String, Array, Math, Object**.

Maîtriser ES6 est **indispensable** pour le développement moderne
(React, Vue, Angular, Node.js).

------------------------------------------------------------------------

# 📚 Ressources

-   [MDN ES6](https://developer.mozilla.org/fr/docs/Web/JavaScript)
-   [ECMAScript 2015 Spec](https://262.ecma-international.org/6.0/)
-   [Exploring ES6 par Axel Rauschmayer](https://exploringjs.com/es6/)
