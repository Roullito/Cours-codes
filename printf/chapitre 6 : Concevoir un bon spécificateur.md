## 6. Concevoir un bon spécificateur `printf`

Créer le bon format pour `printf` dépend du **type de donnée** que vous voulez afficher et de la **présentation souhaitée**.

---

### 6.1 Étape 1 : Identifier le type de donnée

Commencez par déterminer ce que vous imprimez :
- Un **entier** → `%d`
- Un **flottant** → `%f`
- Une **chaîne de caractères** → `%s`
- Un **caractère** → `%c`

---

### 6.2 Étape 2 : Choisir la largeur (`width`)

La largeur permet de réserver un **espace fixe** dans l'affichage, pour :
- Aligner les valeurs dans des tableaux
- Éviter des décalages quand les chiffres changent

Exemples :
```c
%5d   // 5 caractères de large, aligné à droite
%-5d  // 5 caractères de large, aligné à gauche
%05d  // 5 caractères avec zéro en padding
```

---

### 6.3 Étape 3 : Ajouter la précision (`precision`)

Valable pour :
- les **flottants** : nombre de chiffres après la virgule
- les **chaînes** : nombre max de caractères à afficher

Exemples :
```c
%.2f    // 2 chiffres après la virgule
%.4s    // affiche les 4 premiers caractères de la chaîne
%6.2f   // largeur 6, précision 2 (ex: " 12.34")
```

---

### 6.4 Étape 4 : Tester avec différents cas

Testez avec :
- Un **petit entier positif**
- Un **petit entier négatif**
- Un **entier très grand**
- Une **valeur flottante courte et longue**

Objectif : vérifier que tout s'aligne bien et que rien n’est tronqué.

---

### 6.5 Exemple de conception complète

Imaginons qu’on souhaite afficher un prix :

> 🔹 Format souhaité : 6 caractères de large, 2 chiffres après la virgule, toujours afficher le signe

```c
printf("%+6.2f", 12.5);   // " +12.50"
printf("%+6.2f", -7.0);   // "  -7.00"
```

> ✅ Résultat : valeurs lisibles, alignées, cohérentes

---

Prêt pour le **chapitre 7 : Astuces pour réussir les tests `printf`** ? 🧠
