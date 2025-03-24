## 7. Astuces pour réussir les tests `printf`

Les tests `printf` peuvent être **trompeurs** : ils jouent sur l’alignement, les espaces, les signes, etc.  
Voici une méthode stratégique pour les réussir **facilement**.

---

### 7.1 Le pouvoir de l’élimination

> 🔍 Commence par repérer **les éléments visibles** dans la sortie :
- Zéros devant ? → il y a probablement un flag `0`
- Signe positif sur les nombres ? → il y a un `+`
- Espace avant les positifs ? → il y a un `' '` (plus invisible)
- Alignement à gauche ? → il y a un `-`

💡 **Chaque détail te donne un indice** sur le format utilisé !

---

### 7.2 Avant, entre, après : bien lire la sortie

Prenons un exemple :

```txt
  -2035065302 
```

Ici :
- 2 espaces **avant** → il y a 2 caractères **hors format** avant `%...`
- 1 espace **après** → probablement **après le format**

Analyse :
```c
printf("  %-12d ", val); 
```

---

### 7.3 L’invisible plus (`' '`)

Compare une valeur **positive** et **négative**.

Si la **négative** a un `-` et la **positive** a un **espace** → flag `' '` utilisé.

Exemple :

```txt
 203
-204
```

→ `% d`

---

### 7.4 Astuce pour l’alignement

Compare les espaces autour du nombre :
- S’il y en a **devant** → alignement **à droite**
- S’il y en a **derrière** → alignement **à gauche**
- S’il y en a **devant et derrière** → ❌ erreur probable

---

### 7.5 Exemple d’analyse complète

Sortie observée :
```txt
  +27 
```

Déduction :
- Signe positif → `+`
- Espace avant → probablement `width` ≥ 4
- Espace après → alignement à gauche `-`

Format probable :
```c
printf("%-+5d", 27);
```

---

> ✔️ Résous les tests comme une énigme visuelle.  
> Chaque caractère imprimé est un indice sur le format exact utilisé.
