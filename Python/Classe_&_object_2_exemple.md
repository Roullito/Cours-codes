"""
Cours complet sur les classes en Python
Avec comme exemple l'exercice Holberton : classe Rectangle
"""

# 🧠 Qu'est-ce qu'une classe en Python ?

Une **classe** est un plan de construction pour créer des objets. Elle permet de :
- regrouper des **données** (appelées attributs)
- regrouper des **comportements** (appelés méthodes)

On dit que Python est un **langage orienté objet**, et les classes sont la base de cette orientation.

---

## 🔧 Définir une classe

```python
class NomDeClasse:
    def __init__(self):
        pass
```

Le mot-clé `class` permet de créer une classe. La méthode `__init__` est spéciale : elle est automatiquement appelée à la création de l'objet (c'est le **constructeur**).

---

## 🟩 Exemple : La classe Rectangle

On va créer une classe `Rectangle` qui modélise un rectangle géométrique, avec :
- Une **largeur** et une **hauteur**
- Un calcul d'aire et de périmètre
- Un affichage graphique en `#`

---

## 🔐 Encapsulation : attributs privés

```python
self.__width
self.__height
```

Les attributs sont précédés de `__` pour indiquer qu'ils sont **privés** (non accessibles directement de l'extérieur).

---

## ✅ Getters et Setters (via @property)

Ils permettent de **contrôler l'accès** aux attributs, tout en gardant une syntaxe simple :

```python
@property
 def width(self):
     return self.__width

@width.setter
 def width(self, value):
     if not isinstance(value, int):
         raise TypeError("width must be an integer")
     if value < 0:
         raise ValueError("width must be >= 0")
     self.__width = value
```

> ⚠️ C'est ici qu'on applique les règles métier (ex : "la largeur doit être un entier positif")

---

## 🧮 Méthodes d'instance : comportement lié à l'objet

```python
def area(self):
    return self.__width * self.__height

def perimeter(self):
    if self.__width == 0 or self.__height == 0:
        return 0
    return 2 * (self.__width + self.__height)
```

---

## 🖨 Représentation : str vs repr

### `__str__`  → pour l'humain (`print()`)
```python
def __str__(self):
    return "#" * self.__width
```

### `__repr__` → pour le programme (`eval()`)
```python
def __repr__(self):
    return f"Rectangle({self.__width}, {self.__height})"
```

---

## 🧼 Méthode `__del__()`

Appelée automatiquement quand un objet est supprimé.
```python
def __del__(self):
    print("Bye rectangle...")
```

---

## 🧮 Attributs de classe

- `Rectangle.number_of_instances` : compteur global
- `Rectangle.print_symbol` : symbole utilisé dans l'affichage

> Ces attributs sont partagés entre **toutes les instances**.

```python
Rectangle.print_symbol = "#"
``` 

Mais chaque objet peut aussi surcharger ce symbole :
```python
mon_rectangle.print_symbol = "@"
```

---

## 🆚 Méthode statique : `@staticmethod`

Méthode liée à la **classe**, qui ne dépend pas de l'objet.

```python
@staticmethod
def bigger_or_equal(rect_1, rect_2):
    if rect_1.area() >= rect_2.area():
        return rect_1
    return rect_2
```

> On l'appelle comme `Rectangle.bigger_or_equal(r1, r2)`

---

## 🏗 Méthode de classe : `@classmethod`

Elle reçoit la classe elle-même (`cls`) en premier paramètre.
Utilisée ici pour fabriquer un **carré** spécial :

```python
@classmethod
def square(cls, size=0):
    return cls(size, size)
```

> On l'appelle comme `Rectangle.square(5)`

---

## 🔁 Résumé : qu'avons-nous appris avec `Rectangle`

| Concept Python OOP         | Appliqué dans `Rectangle`                          |
|----------------------------|----------------------------------------------------|
| Attributs privés           | `__width`, `__height`                             |
| Getters / Setters          | `@property` et validations                         |
| Méthodes d'instance        | `area()`, `perimeter()`, `__str__()`              |
| Méthodes spéciales         | `__init__`, `__repr__`, `__del__`                 |
| Attributs de classe        | `number_of_instances`, `print_symbol`             |
| Méthode statique           | `bigger_or_equal()`                               |
| Méthode de classe          | `square()`                                        |

> Cette classe Rectangle est un **exercice complet** pour comprendre les bases de la programmation orientée objet en Python.
