# 🧠 Python – Abstract Classes and Interfaces

## 🎯 Objectifs pédagogiques

À la fin de ce cours, vous serez capable de :

- Comprendre ce qu’est une **classe abstraite** en Python.
- Utiliser le module `abc` pour définir des classes abstraites.
- Créer des interfaces Python (même si Python n’a pas de mot-clé `interface`).
- Comprendre le **duck typing** et son rôle dans la conception orientée interface.
- Étendre les **classes de base standard** (`list`, `dict`, etc.) pour les adapter à vos besoins.
- Comprendre et utiliser **l’héritage multiple** et les **mixins**.
- Savoir **overrider** des méthodes dans une sous-classe.
- Déboguer et concevoir des architectures orientées objet robustes.

---

## 🔍 1. Qu’est-ce qu’une classe abstraite ?

Une classe abstraite est une classe qui **ne peut pas être instanciée** directement. Elle définit des **méthodes que les sous-classes doivent implémenter**.

### 📦 Exemple simple avec le module `abc`

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def parler(self):
        pass

class Chien(Animal):
    def parler(self):
        return "Woof"

class Chat(Animal):
    def parler(self):
        return "Meow"
```

```python
a = Animal()  # ❌ TypeError: Can't instantiate abstract class
```

---

## 🛠 2. Interfaces et Duck Typing

Python ne dispose pas de mot-clé `interface`. L’interface est **contractuelle** : si un objet a les méthodes attendues, il peut être utilisé.

### 🦆 Duck typing
> “If it walks like a duck and quacks like a duck, it’s a duck.”

```python
class Canard:
    def marcher(self):
        print("Le canard marche.")

    def cancaner(self):
        print("Coin coin !")

def tester_canard(animal):
    animal.marcher()
    animal.cancaner()

tester_canard(Canard())  # ✅ fonctionne car l'objet respecte l’interface attendue
```

---

## 🧬 3. Subclassing des classes natives

Vous pouvez hériter de `list`, `dict`, etc., pour créer des types personnalisés.

```python
class MonDict(dict):
    def __getitem__(self, key):
        print(f"Accès à la clé : {key}")
        return super().__getitem__(key)
```

---

## 🪓 4. Override de méthodes

Redéfinir une méthode permet d’en **changer le comportement**.

```python
class Chien(Animal):
    def parler(self):
        return "Grrrrr"
```

---

## 🎭 5. L’héritage multiple

Python permet l’héritage multiple (plusieurs parents).

```python
class A:
    def faire(self):
        print("A")

class B:
    def faire(self):
        print("B")

class C(A, B):
    pass

c = C()
c.faire()  # A (priorité donnée à A)
```

---

## 🧩 6. Mixins

Un **mixin** est une classe conçue pour **ajouter du comportement** à d’autres classes via héritage multiple.

```python
class JsonMixin:
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

class Utilisateur(JsonMixin):
    def __init__(self, nom):
        self.nom = nom

u = Utilisateur("Alice")
print(u.to_json())
```

---

## 📚 Ressources utiles

- [`abc` — Abstract Base Classes](https://docs.python.org/3/library/abc.html)
- [RealPython — OOP en Python](https://realpython.com/python3-object-oriented-programming/)
- [Corey Schafer — OOP YouTube Series](https://www.youtube.com/playlist?list=PL-osiE80TeTt2d9bfVyTiXJA-UTHn6WwU)

---

## 🚨 Problèmes fréquents rencontrés

| Problème | Cause fréquente | Solution |
|---------|----------------|----------|
| `TypeError: Can't instantiate abstract class` | Vous avez oublié d’implémenter toutes les méthodes abstraites | Vérifiez avec `@abstractmethod` |
| Méthode attendue non trouvée | Duck typing brisé | Implémentez toutes les méthodes nécessaires |
| Mauvais ordre de résolution de méthode (MRO) | Héritage multiple mal structuré | Utilisez `super()` et lisez `Class.__mro__` |
| Mélange entre attributs d’instance et de classe | Mauvaise compréhension du `self` | Soyez explicite avec `self` ou `ClassName.attr` |

---

## 🧪 Conseils pratiques pour vos projets

- 📐 Définissez toujours des interfaces claires pour vos objets.
- 🧪 Écrivez des **tests unitaires** sur l’implémentation ET le contrat.
- 🧼 Documentez toutes les classes avec des docstrings explicites.
- 🔍 Utilisez `isinstance(obj, Class)` ou `hasattr(obj, "méthode")` pour vérifier l’interface.
- 🤓 Utilisez `abc.ABC` + `@abstractmethod` pour forcer les sous-classes à respecter l’interface.

---

## 📁 Exemple de structure de projet

```
projet/
├── main.py
├── base.py          # Classe abstraite
├── user.py          # Implémentation concrète
├── mixins.py        # Mixin optionnels
└── tests/
    └── test_user.py
```

---

## ✅ À retenir

- `ABC` permet de créer des **interfaces obligatoires**.
- Python est très souple avec l’héritage et le duck typing.
- Utilisez `abstractmethod` pour éviter les implémentations incomplètes.
- `__str__`, `__repr__`, `__len__`, etc., sont aussi des interfaces implicites à respecter.

---

Bon codage orienté objet en Python ! 🐍
