# 🧬 Python - Inheritance

## 🎯 Objectifs d'apprentissage

À la fin de ce cours, tu devrais être capable d'expliquer :

- Ce qu’est une classe parente (superclasse/baseclass)
- Ce qu’est une sous-classe (subclass)
- Comment hériter d’une classe
- Comment **surcharger** une méthode
- Ce que sont les fonctions `super()`, `isinstance()`, `issubclass()`, `type()`
- Ce que signifie l'héritage multiple
- Le rôle de la classe `object` dans Python
- Comment fonctionnent les **méthodes magiques** en lien avec l'héritage

---

## 📘 1. Introduction à l'héritage

L’héritage permet à une **classe enfant (subclass)** de **réutiliser** les attributs et méthodes d’une **classe parent (superclass)**.

```python
class Animal:
    def parler(self):
        print("L’animal fait un bruit")

class Chien(Animal):
    pass

rex = Chien()
rex.parler()  # L’animal fait un bruit
```

---

## 🧱 2. Héritage simple

### Déclaration
```python
class Parent:
    ...

class Enfant(Parent):
    ...
```

Une **subclass** hérite de **tout ce qui est public et protégé** dans la classe parent.

---

## 🔁 3. Surcharge de méthodes (`method overriding`)

Tu peux redéfinir une méthode héritée pour l’adapter :

```python
class Animal:
    def parler(self):
        print("L’animal parle")

class Chat(Animal):
    def parler(self):
        print("Le chat miaule")
```

---

## 🧰 4. La fonction `super()`

`super()` te permet d’appeler une méthode de la classe parente sans en connaître le nom.

```python
class Animal:
    def __init__(self, nom):
        self.nom = nom

class Chien(Animal):
    def __init__(self, nom, race):
        super().__init__(nom)
        self.race = race
```

---

## 👪 5. Héritage multiple

Une classe peut hériter de **plusieurs classes** :

```python
class Volant:
    def bouger(self):
        print("Je vole")

class Nageant:
    def bouger(self):
        print("Je nage")

class Canard(Volant, Nageant):
    pass

c = Canard()
c.bouger()  # MRO : method resolution order => Volant en premier
```

### ⚠️ Problèmes potentiels :
- Conflits de noms de méthodes
- Ordre de résolution complexe

---

## 🏛 6. La classe `object`

Toutes les classes héritent de `object` **par défaut** en Python 3 :

```python
class A:
    pass

print(issubclass(A, object))  # True
```

---

## 🔎 7. Fonctions utiles

| Fonction       | Description                                   |
|----------------|-----------------------------------------------|
| `isinstance(obj, Class)` | Vérifie si `obj` est une instance de `Class` ou d’une sous-classe |
| `issubclass(Sub, Super)` | Vérifie si `Sub` hérite de `Super` |
| `type(obj)`    | Donne le type exact (pas l’héritage)          |
| `super()`      | Accès à la classe parente                     |

---

## 📚 8. Méthodes magiques utiles

| Méthode       | Rôle                                    |
|---------------|------------------------------------------|
| `__init__`    | Constructeur                             |
| `__str__`     | Affichage lisible (`str()`)              |
| `__repr__`    | Représentation officielle (`repr()`)     |
| `__del__`     | Destructeur (à éviter sauf cas spécifiques) |

---

## 🔐 9. Attributs hérités

Une sous-classe hérite :
- des **attributs publics**
- des **attributs protégés** (préfixe `_`)
Mais **pas des attributs privés** (`__attr`)

---

## ⚙️ 10. Ajout d’attributs dynamiques

Tu peux ajouter dynamiquement des attributs à une instance :

```python
obj = Chien("Rex", "Labrador")
obj.age = 5
print(obj.age)
```

---

## 📁 11. Exploration : `__dict__` et introspection

```python
print(obj.__dict__)  # Attributs de l'objet
print(Chien.__dict__)  # Attributs/méthodes de la classe
```

---

## 📌 12. Exemples combinés

```python
class Personne:
    def __init__(self, nom):
        self.nom = nom

    def __str__(self):
        return f"Je suis {self.nom}"

class Étudiant(Personne):
    def __init__(self, nom, école):
        super().__init__(nom)
        self.école = école

    def __str__(self):
        return f"{super().__str__()} et j’étudie à {self.école}"

e = Étudiant("Jean", "Holberton")
print(e)
```

---

## 🧠 Problèmes fréquents

- 🔸 Surcharge non voulue (méthode parent écrasée)
- 🔸 Mauvais usage de `super()` (oubli des arguments)
- 🔸 Héritage multiple mal compris (MRO non maîtrisé)
- 🔸 Tentative d'accès à des attributs privés d’une classe parente
- 🔸 Oubli de `__init__` dans la sous-classe

---

## 📌 Résumé visuel

| Concept              | Exemple / Note                      |
|----------------------|-------------------------------------|
| Classe parente       | `class A:`                          |
| Classe enfant        | `class B(A):`                       |
| Méthode surchargée   | Redéfinition dans une sous-classe   |
| `super()`            | Appelle la version parente          |
| Héritage multiple    | `class C(A, B):`                    |
| Classe racine        | `object`                            |

---

## 🧪 Bonnes pratiques

✅ Toujours utiliser `super()` pour construire

✅ Respecter les conventions `_protegé` et `__privé`

✅ Documenter les relations de classes

✅ Tester avec `isinstance`, `issubclass`

❌ Ne jamais accéder à un `__attribut` depuis une sous-classe directement

---

## 📁 Exécution des tests

```bash
python3 -m doctest ./tests/*
```

---

## 🧩 À retenir

- ✅ L’héritage permet la réutilisation et l’extension de code
- ✅ Python permet **l’héritage multiple** mais il faut être prudent
- ✅ `super()` est indispensable pour éviter les répétitions
- ✅ Les fonctions d’introspection sont très utiles pour déboguer

🚀 Maîtriser l’héritage en Python est la clé pour écrire du code réutilisable, clair et extensible.
