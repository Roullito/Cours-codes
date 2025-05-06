
# 🐍 Python – Conditions, Boucles et Fonctions

---

## 🔹 1. Les conditions (`if / elif / else`)

### 🧠 Règles importantes

- L’indentation (4 espaces) est **obligatoire**.
- Les blocs ne sont **pas délimités par `{}`** comme en C/C++ : Python utilise l’indentation.

### 🔍 Opérateurs de comparaison

| Opérateur | Signification     |
|----------|--------------------|
| `==`     | Égal à             |
| `!=`     | Différent de       |
| `>`      | Supérieur à        |
| `<`      | Inférieur à        |
| `>=`     | Supérieur ou égal  |
| `<=`     | Inférieur ou égal  |

### 🔗 Opérateurs logiques

| Opérateur | Utilisation                     |
|----------|----------------------------------|
| `and`    | Toutes les conditions vraies     |
| `or`     | Au moins une condition vraie     |
| `not`    | Négation d’une condition         |

---

## 🔹 2. Les boucles (`for`, `while`)

### 🔁 Boucle `for`

```python
for i in range(5):
    print(i)
```

> Affiche : `0 1 2 3 4`

### ⚙️ Paramètres de `range(start, stop, step)`

```python
for i in range(10, 0, -2):
    print(i)  # 10, 8, 6, 4, 2
```

### 🔄 Boucle `while`

```python
i = 0
while i < 5:
    print(i)
    i += 1
```

### 🛑 Contrôle de boucle

| Mot-clé    | Effet                                                   |
|------------|----------------------------------------------------------|
| `break`    | Sort immédiatement de la boucle                          |
| `continue` | Saute le reste du bloc et passe à l’itération suivante   |
| `else`     | S'exécute si la boucle n'est **pas** interrompue par `break` |

---

## 🔹 3. Fonctions en Python

### 🧾 Syntaxe de base

```python
def saluer(nom):
    print(f"Bonjour, {nom} !")
```

### 📤 Valeur de retour

```python
def carre(x):
    return x * x

resultat = carre(5)  # 25
```

### 📌 Paramètres par défaut

```python
def bonjour(nom="utilisateur"):
    print(f"Salut {nom} !")

bonjour()         # Salut utilisateur !
bonjour("Alice")  # Salut Alice !
```

### 📚 Docstring

```python
def addition(a, b):
    """
    Additionne deux nombres entiers.
    """
    return a + b
```

---

## 🔒 4. Portée des variables (scope)

Une variable définie dans une fonction est **locale**.

Les variables **globales** sont accessibles mais **non modifiables** sans le mot-clé `global`.

```python
x = 10

def affiche():
    print(x)  # OK : lecture

def change():
    global x
    x = 5  # OK : modification avec global
```

---

## 🧠 5. Bonnes pratiques

✅ Toujours utiliser **4 espaces** pour l’indentation  
✅ Donner des noms clairs aux fonctions (`calcul_salaire`, pas `cs`)  
✅ Écrire une **docstring** si la fonction est complexe  
🚫 Éviter les variables globales sauf nécessité absolue  
🚫 Ne jamais ignorer les cas `None` / erreurs dans des fonctions  

---

## 📚 Récapitulatif visuel

| Élément   | Clé à retenir                            |
|-----------|-------------------------------------------|
| `if`      | Contrôle de flux                         |
| `for`     | Itération sur séquence                   |
| `while`   | Boucle conditionnelle                    |
| `break`   | Sortie immédiate                         |
| `continue`| Aller directement à l’itération suivante |
| `def`     | Déclaration d'une fonction               |
| `return`  | Renvoie une valeur depuis la fonction    |

---

## 🧪 Exemples combinés

```python
def est_pair(n):
    if n % 2 == 0:
        return True
    return False

for i in range(10):
    if est_pair(i):
        print(f"{i} est pair")
```

---

## ✅ À retenir

- Tu peux créer des fonctions simples **sans typage explicite**.
- Les blocs sont **définis uniquement par l’indentation**.
- Maîtriser les conditions et boucles est essentiel pour toute logique de programme.

---
