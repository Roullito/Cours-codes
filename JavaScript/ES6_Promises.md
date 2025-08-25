# ES6 Promises - Cours Ultra Complet

## Introduction

En JavaScript moderne (ES6 et versions ultérieures), les **Promises**
sont un mécanisme puissant pour gérer les opérations **asynchrones**.\
Elles permettent d'écrire du code plus lisible et plus maintenable
qu'avec les **callbacks** traditionnels.

------------------------------------------------------------------------

## 1. Qu'est-ce qu'une Promise ?

Une **Promise** est un objet représentant l'achèvement (ou l'échec)
éventuel d'une opération asynchrone.

-   **En attente (pending)** → L'opération n'est pas encore terminée.
-   **Résolue (fulfilled)** → L'opération a réussi et renvoie une
    valeur.
-   **Rejetée (rejected)** → L'opération a échoué et renvoie une raison
    (erreur).

### Schéma :

``` text
Pending ---> Fulfilled (résultat)
        \--> Rejected (erreur)
```

------------------------------------------------------------------------

## 2. Création d'une Promise

Une Promise est créée avec le constructeur `new Promise`, qui prend en
paramètre une fonction **executor** avec deux arguments :

-   `resolve` : appelé quand l'opération réussit
-   `reject` : appelé quand l'opération échoue

### Exemple :

``` javascript
const maPromesse = new Promise((resolve, reject) => {
  let succes = true;

  if (succes) {
    resolve("Opération réussie !");
  } else {
    reject("Erreur lors de l’opération");
  }
});
```

------------------------------------------------------------------------

## 3. Consommer une Promise

Une fois créée, une Promise se consomme avec les méthodes :

-   `.then()` → pour gérer la réussite
-   `.catch()` → pour gérer les erreurs
-   `.finally()` → exécuté quoi qu'il arrive

### Exemple :

``` javascript
maPromesse
  .then(resultat => {
    console.log("Succès :", resultat);
  })
  .catch(erreur => {
    console.error("Échec :", erreur);
  })
  .finally(() => {
    console.log("Fin de l'opération.");
  });
```

------------------------------------------------------------------------

## 4. Promises chaînées

Les Promises permettent de **chaîner les opérations** asynchrones.

### Exemple :

``` javascript
new Promise((resolve, reject) => {
  resolve(2);
})
.then(result => {
  console.log(result); // 2
  return result * 2;
})
.then(result => {
  console.log(result); // 4
  return result * 2;
})
.then(result => {
  console.log(result); // 8
});
```

------------------------------------------------------------------------

## 5. Promise.all, Promise.race, Promise.allSettled, Promise.any

### Promise.all()

Attend que **toutes** les Promises soient résolues (ou rejette si une
échoue).

``` javascript
Promise.all([
  Promise.resolve(1),
  Promise.resolve(2),
  Promise.resolve(3)
]).then(values => console.log(values)); // [1, 2, 3]
```

### Promise.race()

Renvoie la première Promise qui se résout ou se rejette.

``` javascript
Promise.race([
  new Promise(resolve => setTimeout(resolve, 100, "Rapide")),
  new Promise(resolve => setTimeout(resolve, 200, "Lent"))
]).then(value => console.log(value)); // "Rapide"
```

### Promise.allSettled()

Renvoie un tableau avec l'état de toutes les Promises (succès ou échec).

``` javascript
Promise.allSettled([
  Promise.resolve("OK"),
  Promise.reject("Erreur")
]).then(results => console.log(results));
```

### Promise.any()

Renvoie la première Promise **résolue** (ignore les rejets, sauf si
toutes échouent).

``` javascript
Promise.any([
  Promise.reject("Erreur"),
  Promise.resolve("Succès !")
]).then(value => console.log(value)); // "Succès !"
```

------------------------------------------------------------------------

## 6. Exemple concret : Requête réseau simulée

``` javascript
function requeteServeur(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url === "https://api.example.com") {
        resolve({ data: "Réponse du serveur" });
      } else {
        reject("404 - Not Found");
      }
    }, 1000);
  });
}

requeteServeur("https://api.example.com")
  .then(response => console.log(response.data))
  .catch(error => console.error(error));
```

------------------------------------------------------------------------

## 7. Comparaison avec async/await

Les Promises sont la base de `async/await` (introduit en ES8).\
Elles permettent d'écrire du code asynchrone **comme du code
synchrone**.

### Exemple avec async/await :

``` javascript
async function getData() {
  try {
    const response = await requeteServeur("https://api.example.com");
    console.log(response.data);
  } catch (error) {
    console.error(error);
  }
}
getData();
```

------------------------------------------------------------------------

## 8. Bonnes pratiques avec les Promises

✅ Toujours gérer les erreurs avec `.catch()` ou `try/catch`\
✅ Utiliser `Promise.all` pour paralléliser des tâches\
✅ Éviter les pyramides de callbacks (callback hell)\
✅ Favoriser `async/await` pour plus de lisibilité

------------------------------------------------------------------------

## Conclusion

Les **Promises** sont un outil central en JavaScript moderne pour gérer
l'asynchrone.\
Elles simplifient énormément le code par rapport aux callbacks et
permettent une meilleure organisation avec `.then()`, `.catch()`,
`.finally()`, ainsi que les méthodes statiques comme `Promise.all`.

------------------------------------------------------------------------

## Ressources complémentaires

-   [MDN -
    Promise](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Promise)
-   [JavaScript.info - Promises](https://javascript.info/promise-basics)
-   [Async/Await](https://developer.mozilla.org/fr/docs/Learn/JavaScript/Asynchronous/Promises)
