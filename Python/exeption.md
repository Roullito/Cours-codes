# 📘 Python - Exceptions

## 🎯 Objectifs d'apprentissage
À la fin de ce cours, tu sauras :
- Ce qu'est une exception en Python
- Pourquoi et comment les exceptions sont utilisées
- Comment gérer les erreurs avec `try`, `except`, `else`, `finally`
- Créer ses propres exceptions
- Les bonnes pratiques pour un code robuste
- Identifier et résoudre les erreurs les plus fréquentes

---

## 🧨 Qu'est-ce qu'une exception ?

Une **exception** est une erreur détectée pendant l'exécution d’un programme. Elle interrompt le flux normal du programme si elle n’est pas traitée.

### 🔎 Exemple
```python
print(10 / 0)  # ZeroDivisionError
```

---

## 🛠️ Gérer les exceptions : try / except

### 🔹 Syntaxe de base
```python
try:
    # Code potentiellement dangereux
except NomDeLErreur:
    # Code exécuté en cas d'erreur
```

### 🔹 Exemple concret
```python
try:
    num = int(input("Entrez un nombre : "))
except ValueError:
    print("Ce n’est pas un nombre valide.")
```

---

## ⚙️ Utiliser else et finally

### 🔸 else
Exécuté **seulement si aucune exception** n’a été levée.

```python
try:
    x = 10 / 2
except ZeroDivisionError:
    print("Erreur division par zéro")
else:
    print("Aucune erreur !")
```

### 🔸 finally
Exécuté **quoi qu’il arrive** (erreur ou pas).

```python
try:
    x = 10 / 0
except ZeroDivisionError:
    print("Division par zéro")
finally:
    print("Bloc finally exécuté")
```

---

## 🧱 Gérer plusieurs exceptions

```python
try:
    f = open("fichier.txt")
    data = int(f.read())
except FileNotFoundError:
    print("Fichier non trouvé")
except ValueError:
    print("Contenu invalide")
```

---

## 🧑‍💻 Créer ses propres exceptions

### 🔹 Exemple
```python
class ErreurAge(Exception):
    pass

def valider_age(age):
    if age < 0:
        raise ErreurAge("L’âge ne peut pas être négatif.")
```

---

## 🔍 Exceptions courantes

| Exception            | Description                            |
|----------------------|----------------------------------------|
| `ZeroDivisionError`  | Division par zéro                      |
| `ValueError`         | Mauvais type de valeur                 |
| `IndexError`         | Index hors de portée                   |
| `KeyError`           | Clé absente dans un dictionnaire       |
| `TypeError`          | Type d’argument incorrect              |
| `FileNotFoundError`  | Fichier introuvable                    |
| `ImportError`        | Erreur lors d’un import                |

---

## 🧠 Bonnes pratiques

- ✅ Ne pas attraper toutes les exceptions avec `except:`
- ✅ Utiliser des blocs `try` courts
- ✅ Spécifier les types d’exception précis
- ✅ Documenter les exceptions levées par une fonction
- ✅ Toujours tester les cas limites

---

## 🔥 Problèmes fréquents rencontrés

| Problème                         | Cause possible                                     | Solution                         |
|----------------------------------|----------------------------------------------------|----------------------------------|
| `except:` capture trop large     | Mauvaise pratique, peut masquer les bugs critiques | Utiliser des exceptions spécifiques |
| Oubli du `finally`               | Ressources non libérées                            | Toujours fermer les fichiers     |
| Mauvais type d'exception levé   | Ex: `raise "Erreur"`                               | Utiliser une classe dérivée de `Exception` |
| Exception silencieuse           | Pas de `print` ou `logging` dans le bloc `except`  | Toujours logger les erreurs      |

---

## 📚 Références utiles

- [Python Official Docs - Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html)
- [Real Python - Python Exceptions](https://realpython.com/python-exceptions/)
- [PEP 8 - Handling Errors](https://peps.python.org/pep-0008/#programming-recommendations)

---

## ✅ À retenir

- Les exceptions permettent de rendre le code plus **robuste** et **fiable**
- Le bloc `try/except` intercepte les erreurs sans arrêter le programme
- On peut lever (`raise`) des exceptions personnalisées pour mieux décrire un problème
- Toujours penser à libérer les ressources dans `finally`
- Un bon code **prévoit** les cas d'erreur et les **gère proprement**

Bon codage ! 🐍
