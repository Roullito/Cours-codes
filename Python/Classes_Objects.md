# 📘 Python - Classes and Objects (OOP)

## 🎯 Objectifs d'apprentissage
À la fin de ce cours, tu seras capable d'expliquer :
- Ce qu'est la Programmation Orientée Objet (POO)
- Ce qu'est une classe, un objet, une instance
- La différence entre une classe et un objet
- Ce que sont les attributs (public, protégé, privé)
- Ce que signifie `self` en Python
- Ce qu'est une méthode et le rôle de `__init__`
- L'encapsulation, l'abstraction, le masquage d'information
- Les propriétés (`@property`), getters et setters
- Comment manipuler dynamiquement les attributs
- Comment fonctionne `__dict__`, `getattr`, etc.

---

## 🧠 Qu’est-ce que la Programmation Orientée Objet (OOP) ?
La POO est un paradigme de programmation basé sur le concept **d’objets** contenant **des données (attributs)** et **des comportements (méthodes)**.

### 🔹 Concepts clés :
- **Classe** : Plan de construction (modèle) pour les objets
- **Objet** : Instance concrète d’une classe
- **Instance** : Autre nom pour un objet

---

## 🔧 Définir une classe et créer un objet

```python
class Voiture:
    def __init__(self, marque, modele):
        self.marque = marque
        self.modele = modele

ma_voiture = Voiture("Renault", "Clio")
print(ma_voiture.marque)  # Renault
```

- `__init__` est le constructeur : il initialise chaque objet
- `self` représente l'objet en cours

---

## 🧱 Attributs et Visibilité

### 🔸 Attributs publics
Accessibles depuis n’importe où.

```python
self.nom = "Python"
```

### 🔸 Attributs protégés (_)
Convention : à ne pas modifier directement

```python
self._secret = "protégé"
```

### 🔸 Attributs privés (__)
Mécanisme de name mangling (nom brouillé)

```python
self.__tres_secret = "privé"
```

---

## 📦 Méthodes

### 🔹 Définition de méthode
```python
class Chien:
    def aboie(self):
        print("Wouf !")
```

### 🔹 Appel :
```python
medor = Chien()
medor.aboie()  # Wouf !
```

---

## ⚙️ Encapsulation, Abstraction et Hiding

- **Encapsulation** : Protéger les données en regroupant attributs et méthodes.
- **Abstraction** : Masquer les détails internes inutiles.
- **Information hiding** : Rendre invisibles certaines infos via `__attribut` ou des propriétés.

---

## 🧩 Propriétés : @property

### 🔸 Syntaxe moderne Pythonique :
```python
class Personne:
    def __init__(self, nom):
        self._nom = nom

    @property
    def nom(self):
        return self._nom

    @nom.setter
    def nom(self, valeur):
        if valeur:
            self._nom = valeur

p = Personne("Alice")
p.nom = "Bob"
```

- Permet de protéger l’accès tout en gardant une syntaxe simple.

---

## 🪄 Ajouter dynamiquement des attributs

```python
p = Personne("Alice")
p.age = 30  # Ajout dynamique
```

⚠️ Non recommandé pour des attributs critiques. Utiliser `__slots__` pour restreindre.

---

## 📚 `__dict__` et introspection

```python
print(p.__dict__)
# {'_nom': 'Alice', 'age': 30}
```

- Contient tous les attributs définis dynamiquement de l’instance.

---

## 🔍 `getattr`, `hasattr`, `setattr`

```python
getattr(p, "nom")      # Accès
setattr(p, "ville", "Paris")
hasattr(p, "age")      # True
```

Très utile pour accéder dynamiquement à des attributs.

---

## 📌 Liens attributs / classes

```python
class Exemple:
    version = 1.0

print(Exemple.version)          # Attribut de classe
inst = Exemple()
print(inst.version)             # OK (hérité)
inst.version = 2.0              # Attribut d’instance (masque celui de classe)
```

---

## 📜 Docstrings obligatoires

```python
class Voiture:
    """Classe représentant une voiture."""
    def __init__(self, marque):
        """Initialise la voiture avec une marque."""
        self.marque = marque
```

- Respecter la norme [Google docstring](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings)

---

## 🛠 Problèmes fréquents

| Problème | Explication / Solution |
|----------|--------------------------|
| `AttributeError` | L’attribut n’existe pas (orthographe ? `getattr` ?) |
| Confusion `self` | Oublier `self` en paramètre dans les méthodes |
| Utilisation incorrecte de `__init__` | Ne retourne jamais rien ! (`return` interdit) |
| Mauvais masquage d’attribut | `_attribut` ≠ `__attribut` (name mangling) |
| Répétition de code | Utiliser des méthodes privées ou `@property` |

---

## 🧪 Tester une classe

```python
import unittest

class TestVoiture(unittest.TestCase):
    def test_marque(self):
        v = Voiture("Peugeot")
        self.assertEqual(v.marque, "Peugeot")

if __name__ == "__main__":
    unittest.main()
```

---

## 🧠 À retenir

| Élément | Rôle |
|---------|------|
| `class` | Déclaration de classe |
| `self` | Référence à l'instance |
| `__init__` | Constructeur |
| `@property` | Encapsulation pythonique |
| `__dict__` | Dictionnaire des attributs |
| `getattr/setattr` | Accès dynamique |

---

## 🚀 À toi de jouer
- Crée une classe `Livre` avec titre, auteur, pages (avec @property)
- Ajoute un test unitaire vérifiant que `pages` est positif
- Expérimente l'ajout d'attributs dynamiques

Bon code ! 🎯
