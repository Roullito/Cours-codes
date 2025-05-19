# 📘 Python - More Classes and Objects

## 🎯 Objectifs d'apprentissage

À la fin de ce cours, tu devrais être capable d'expliquer :
- Ce qu'est la Programmation Orientée Objet (OOP)
- Les différences entre classes, objets et instances
- L'intérêt et l'utilisation de `self`
- Les attributs d'objets vs attributs de classe
- L'encapsulation, l'abstraction, et l'information hiding
- Les méthodes spéciales : `__init__`, `__str__`, `__repr__`, `__del__`
- Les méthodes de classe (`@classmethod`) et les méthodes statiques (`@staticmethod`)
- Les propriétés (`@property`) vs getters/setters classiques
- Le rôle de `__dict__` et de `getattr`

---

## 🧱 Rappels sur les classes Python

Une classe est un modèle qui permet de créer des objets.

```python
class Person:
    def __init__(self, name):
        self.name = name
```

`self` représente l'instance actuelle de la classe.

---

## 🔍 Attributs : objet vs classe

### 🔸 Attribut d’instance

Spécifique à un objet.

```python
class Dog:
    def __init__(self, name):
        self.name = name
```

### 🔸 Attribut de classe

Partagé par toutes les instances.

```python
class Dog:
    species = "Canis lupus"

    def __init__(self, name):
        self.name = name
```

---

## ⚙️ Méthodes spéciales

### 🔸 `__str__` vs `__repr__`

```python
class Book:
    def __init__(self, title):
        self.title = title

    def __str__(self):
        return f"Book title: {self.title}"

    def __repr__(self):
        return f"Book('{self.title}')"
```

- `__str__` : version lisible pour l’utilisateur
- `__repr__` : version explicite pour les développeurs (souvent réutilisable)

---

## 🏭 Méthodes de classe et statiques

### 🔹 Méthode de classe

Accède à la classe (`cls`)

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    @classmethod
    def margherita(cls):
        return cls(['cheese', 'tomato'])
```

### 🔹 Méthode statique

Ni accès à la classe, ni à l’instance.

```python
class Math:
    @staticmethod
    def add(x, y):
        return x + y
```

---

## 🧠 Propriétés vs getters/setters

### 🔹 Classique

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    def get_radius(self):
        return self._radius

    def set_radius(self, value):
        self._radius = value
```

### 🔹 Pythonique

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        self._radius = value
```

---

## 🧬 Création dynamique d’attributs

```python
obj = type("Dynamic", (), {})()
obj.x = 42
```

---

## 📦 Le dictionnaire `__dict__`

```python
class Person:
    def __init__(self, name):
        self.name = name

p = Person("Alice")
print(p.__dict__)  # {'name': 'Alice'}
```

---

## 🧪 Recherche d’attributs

Utilise `getattr` pour accéder dynamiquement :

```python
class Car:
    def __init__(self):
        self.speed = 100

c = Car()
print(getattr(c, 'speed', 0))       # 100
print(getattr(c, 'color', 'unknown'))  # 'unknown'
```

---

## 🧹 Destructor

```python
class Resource:
    def __del__(self):
        print("Nettoyage...")
```

---

## ⚠️ Problèmes courants

| Problème                            | Cause possible                          | Solution                               |
|-------------------------------------|------------------------------------------|----------------------------------------|
| L'attribut est partagé entre objets | Utilisation d’attribut de classe         | Utiliser `self.attr` dans `__init__`   |
| Impossible d’accéder à un attribut  | Attribut privé (`__attr`)                | Renommer ou utiliser getter            |
| `__str__` affiche mal               | Non défini                               | Ajouter une méthode `__str__()`        |
| Accès interdit à une méthode        | Mauvaise portée (manque `self`)          | Ne pas oublier `self.` dans les defs   |

---

## ✅ Récapitulatif

- `__init__`, `__str__`, `__repr__`, `__del__` = comportements spéciaux
- `@property`, `@classmethod`, `@staticmethod` facilitent l'encapsulation
- `getattr`, `__dict__` offrent introspection et accès dynamique
- `self` = instance courante
- `cls` = la classe elle-même

---

Bon code orienté objet ! 🐍
