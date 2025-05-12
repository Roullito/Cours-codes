
# 🐍 Python - Structures de Données : Listes et Tuples

## 🎯 Objectifs du cours

- Comprendre ce que sont les **listes** et **tuples** en Python
- Savoir **déclarer**, **accéder**, **modifier** et **parcourir** ces structures
- Connaître les différences fondamentales entre listes et tuples
- Maîtriser les méthodes associées aux listes
- Savoir choisir entre liste ou tuple selon le contexte
- Apprendre à gérer les **erreurs courantes**
- Être capable de résoudre des problèmes réels avec ces structures

---

## 🔹 1. Les listes en Python

### ✅ Définition
Une **liste** est une collection **ordonnée et modifiable**. Elle peut contenir des éléments de **types différents**.

```python
fruits = ["pomme", "banane", "cerise"]
nombres = [1, 2, 3, 4, 5]
melange = [1, "bonjour", 3.14, True]
```

---

### 🔍 Accès aux éléments
```python
print(fruits[0])      # "pomme"
print(fruits[-1])     # "cerise"
```

---

### ✏️ Modification d’un élément
```python
fruits[1] = "kiwi"
```

---

### ➕ Ajout d’éléments
```python
fruits.append("orange")        # à la fin
fruits.insert(1, "mangue")     # à l'index 1
```

---

### ➖ Suppression d’éléments
```python
fruits.remove("kiwi")          # supprime "kiwi"
del fruits[0]                  # supprime "pomme"
dernier = fruits.pop()         # supprime et retourne le dernier
```

---

### 🔁 Parcours
```python
for fruit in fruits:
    print(fruit)

for i in range(len(fruits)):
    print(i, fruits[i])
```

---

### 📚 Méthodes utiles
```python
fruits.sort()
fruits.reverse()
fruits.count("banane")
fruits.index("cerise")
```

---

### ⚠️ Erreurs fréquentes
```python
fruits[10]  # IndexError: list index out of range
fruits.remove("ananas")  # ValueError si l'élément n'existe pas
```

---

## 🔹 2. Les tuples en Python

### ✅ Définition
Un **tuple** est une collection **ordonnée mais IMMUTABLE**.

```python
coordonnees = (10, 20)
info = ("Nom", "Prénom", 25)
```

---

### 🧱 Syntaxe particulière
```python
x = (5,)    # tuple avec un seul élément (virgule obligatoire)
y = 5       # ceci est un entier, pas un tuple !
```

---

### 🔍 Accès aux éléments
```python
print(coordonnees[0])    # 10
```

---

### ⚠️ Immuabilité
```python
coordonnees[0] = 15  # ❌ TypeError
```

---

### 🔁 Parcours
```python
for val in info:
    print(val)
```

---

### 🎒 Déballage (unpacking)
```python
nom, prenom, age = info
```

---

## ⚖️ 3. Listes vs Tuples

| Caractéristique        | Liste             | Tuple              |
|------------------------|-------------------|--------------------|
| Modifiable             | ✅ Oui            | ❌ Non             |
| Définition             | []                | ()                 |
| Mémoire                | Plus lourde       | Plus légère        |
| Performances           | Moins rapide      | Plus rapide        |
| Utilisation            | Données dynamiques| Données constantes |

---

## 🧠 4. Bonnes pratiques

- ✅ Utiliser les **tuples** pour des **données constantes**
- ✅ Utiliser les **listes** si besoin de modifier les éléments
- ❌ Éviter d'accéder à des indices qui n'existent pas
- ✅ Utiliser `enumerate()` pour parcourir une liste avec l'index

---

## 💥 5. Cas concrets

### 📌 Exemple 1 : Inverser une liste
```python
nombres = [1, 2, 3, 4]
nombres.reverse()
```

---

### 📌 Exemple 2 : Trier une liste sans la modifier
```python
notes = [10, 15, 12]
nouvelles_notes = sorted(notes)
```

---

### 📌 Exemple 3 : Créer une liste de tuples
```python
personnes = [("Alice", 25), ("Bob", 30)]
```

---

### 📌 Exemple 4 : Utiliser zip()
```python
prenoms = ["Alice", "Bob"]
ages = [25, 30]
liste = list(zip(prenoms, ages))
```

---

## 📚 Récapitulatif

- Les **listes** sont **modifiables**, les **tuples** sont **immutables**
- Les **listes** offrent plus de méthodes intégrées
- Utiliser **les bonnes structures selon les besoins**
- Bien gérer les **erreurs d'index** et l’immutabilité
- Appliquer les **bonnes pratiques** pour écrire du code clair et robuste

---

🚀 **Tu es maintenant prêt(e) à manipuler efficacement les listes et tuples en Python !**
