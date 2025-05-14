# 🧪 Python - Test-Driven Development (TDD)

## 📚 Objectifs pédagogiques

À la fin de ce cours, tu seras capable de :
- Comprendre l'intérêt du TDD (Test-Driven Development)
- Écrire des docstrings de test avec `doctest`
- Écrire des tests unitaires avec `unittest`
- Organiser proprement un projet Python avec des fichiers de tests
- Trouver des cas limites et les tester avant de coder
- Lancer automatiquement les tests avec `doctest`

---

## 🚀 Qu'est-ce que le TDD ?

> **Test-Driven Development** est une méthode de développement où **les tests sont écrits avant le code**.

### 🔁 Le cycle TDD (Red → Green → Refactor)

1. **Red** : Écris un test qui échoue.
2. **Green** : Implémente le code minimal pour que le test passe.
3. **Refactor** : Réécris ton code proprement, les tests doivent toujours passer.

---

## 🔍 Pourquoi écrire les tests en premier ?

✅ Penser à tous les cas possibles, **y compris les cas limites**.  
✅ Éviter les effets de bord non anticipés.  
✅ Avoir une **documentation vivante** sous forme de tests.  
✅ Faciliter la relecture et la collaboration.  
✅ Détection rapide des bugs.  

---

## 📖 Écriture de tests avec `doctest`

### 🧾 Exemple de docstring testable

```python
def add(a, b):
    """Additionne deux nombres.

    >>> add(2, 3)
    5
    >>> add(-1, 1)
    0
    """
    return a + b
```

### ▶️ Exécution des tests

```bash
$ python3 -m doctest ./tests/*
```

🧠 **Remarque :** Les tests doivent être dans un dossier `tests/` au format `.txt`.

---

## 📦 Structure d’un projet avec TDD

```
mon_projet/
├── my_module.py
├── tests/
│   ├── test_my_module.txt
├── README.md
```

---

## 🛡️ Bonnes pratiques de documentation

- Chaque **module** et **fonction** doit avoir une **docstring claire et complète**.
- La docstring doit expliquer **le but** et non juste redire le nom.
- Exemple incorrect : `"Addition"`  
- Exemple correct : `"Retourne la somme de deux entiers"`

---

## ⚠️ Détection des cas limites

Pose-toi toujours ces questions :
- Que se passe-t-il si je passe un `None` ?
- Que se passe-t-il avec des chaînes vides ?
- Que se passe-t-il avec des entiers négatifs ou des très grands nombres ?
- La fonction est-elle sensible à la casse ?

🧠 **Astuce :** Écrire des tests en binôme pour ne **rien oublier**.

---

## 🧪 Écrire un test unitaire avec `unittest`

```python
import unittest
from my_module import add

class TestAdd(unittest.TestCase):
    def test_add_positive(self):
        self.assertEqual(add(1, 2), 3)

    def test_add_zero(self):
        self.assertEqual(add(0, 0), 0)
```

Puis exécuter :
```bash
python3 -m unittest test_module.py
```

---

## ⚙️ Commandes utiles

| Commande                              | Description                        |
|---------------------------------------|------------------------------------|
| `python3 -m doctest tests/*.txt`      | Lance les tests `doctest`          |
| `python3 -m unittest`                 | Lance tous les tests `unittest`    |
| `python3 -c 'print(__import__("x").__doc__)'` | Affiche la doc du module     |
| `python3 -c 'print(__import__("x").y.__doc__)'` | Affiche la doc d'une fonction |

---

## 📌 Règles du projet (Holberton)

- Dossier `tests/` obligatoire.
- Tous les tests sont en `.txt` (pour doctest).
- Exécution obligatoire via `python3 -m doctest ./tests/*`.
- Aucune ligne de code ne doit être écrite avant les tests.
- Tous les modules et fonctions doivent être documentés.
- Tous les fichiers `.py` doivent être exécutables.
- Respect de la norme `pycodestyle`.

---

## ✅ Résumé visuel

| Élément             | Clé à retenir                         |
|---------------------|---------------------------------------|
| TDD                 | Tests d'abord, code ensuite           |
| doctest             | Tests dans la docstring               |
| unittest            | Tests plus complets et structurés     |
| docstring           | Doit être descriptive et testable     |
| tests/              | Dossier obligatoire pour les fichiers |
| edge cases          | Ne jamais les ignorer                 |

---

## 🔚 Pour aller plus loin

- Couplage de `TDD` avec `CI/CD`
- Intégration dans GitHub Actions ou GitLab CI
- Usage de `coverage` pour vérifier les tests manquants

🚀 Bon code, bons tests, peu de bugs !
