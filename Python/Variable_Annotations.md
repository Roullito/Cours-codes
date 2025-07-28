
# 🐍 Python – Variable Annotations

---

## 🎯 Objectifs pédagogiques

À la fin de ce cours, tu sauras :

- ✅ Comprendre le rôle des **annotations de types** en Python.
- ✅ Annoter **fonctions**, **variables** et **structures complexes**.
- ✅ Distinguer les types **simples**, **composés**, **génériques**.
- ✅ Valider ton code avec `mypy`.
- ✅ Comprendre le concept de **duck typing**.

---

## 🧠 1. Qu'est-ce que les Variable Annotations ?

En Python, le typage est **dynamique** : une variable peut changer de type à tout moment.  
Mais depuis Python 3.5+, on peut **annoter** les types pour **documenter le code**, faciliter la **relecture**, et utiliser des outils comme `mypy`.

```python
def add(a: int, b: int) -> int:
    return a + b
```

➡️ Ici, on **annote** les types attendus. Cela ne change rien à l'exécution, mais aide :

- les développeurs à comprendre le contrat
- les outils de validation statique à détecter des erreurs

---

## 🔍 2. Pourquoi utiliser les annotations ?

| Avantage                  | Description |
|--------------------------|-------------|
| 📘 Documentation          | Le code devient auto-documenté |
| 🧪 Linting / CI           | Les outils peuvent vérifier les types (`mypy`) |
| 💡 IDE intelligent        | Autocomplétion + vérification |
| 🐞 Moins de bugs          | Les erreurs sont visibles avant l'exécution |

---

## 🧬 3. Syntaxe des annotations simples

| Type Python | Annotation |
|-------------|------------|
| `int`       | `: int`    |
| `float`     | `: float`  |
| `str`       | `: str`    |
| `bool`      | `: bool`   |

```python
name: str = "Alice"
pi: float = 3.14
active: bool = True
```

---

## ⚙️ 4. Fonctions avec annotations

### ➕ Exemple basique

```python
def multiply(a: float, b: float) -> float:
    return a * b
```

### 🔄 Conversion de type

```python
def to_str(n: float) -> str:
    return str(n)
```

📌 Tu peux vérifier les annotations avec `.__annotations__` :

```python
print(to_str.__annotations__)
```

---

## 🧰 5. Types avancés avec `typing`

```python
from typing import List, Union, Tuple, Callable, Iterable, Sequence
```

### 📜 Liste de float

```python
def sum_list(input_list: List[float]) -> float:
    return sum(input_list)
```

### 🔀 Liste mixte (int + float)

```python
def sum_mixed_list(mxd_lst: List[Union[int, float]]) -> float:
    return sum(mxd_lst)
```

### 📦 Tuple (str, carré d'un nombre)

```python
def to_kv(k: str, v: Union[int, float]) -> Tuple[str, float]:
    return (k, v * v)
```

---

## 🧪 6. Fonctions retournant une fonction

Utilisation de `Callable`

```python
from typing import Callable

def make_multiplier(multiplier: float) -> Callable[[float], float]:
    def multiply(n: float) -> float:
        return n * multiplier
    return multiply
```

---

## 🦆 7. Duck Typing et Iterable

💡 Le duck typing permet de ne pas spécifier de types concrets mais des **comportements attendus**.

```python
from typing import Iterable, Sequence, Tuple, List

def element_length(lst: Iterable[Sequence]) -> List[Tuple[Sequence, int]]:
    return [(i, len(i)) for i in lst]
```

---

## 🧪 8. Validation avec `mypy`

Installe-le avec :

```bash
pip install mypy
```

Puis exécute :

```bash
mypy my_script.py
```

🔍 Il affichera les **erreurs de type** potentielles, sans exécuter le script.

---

## ✅ Bonnes pratiques

✅ Annote **toutes tes fonctions publiques**  
✅ Utilise `typing` pour les types composés  
✅ Ne remplace pas les tests unitaires, mais les complète  
✅ Combine avec un linter (`flake8`, `pylint`) pour plus de rigueur

---

## 📚 Références utiles

- [📖 Python Type Hints Guide (PEP 484)](https://peps.python.org/pep-0484/)
- [📘 Mypy Cheat Sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
- [📚 Python `typing` module](https://docs.python.org/3/library/typing.html)

---

## 👨‍💻 Exemple final récapitulatif

```python
from typing import List, Union, Callable

def process(values: List[Union[int, float]]) -> Callable[[float], float]:
    total: float = sum(values)
    return lambda x: total * x
```

---

## 🚀 Résumé

| Concept | Résumé rapide |
|--------|----------------|
| Type hint | Indication du type attendu |
| Typing | Module pour gérer les types complexes |
| mypy | Outil pour valider les types statiquement |
| Duck typing | Typage basé sur le comportement |
| Callable | Type d'une fonction comme objet retourné |

---

## 🧠 À retenir

- Les annotations n'affectent pas l'exécution.
- Elles rendent ton code plus **clair**, **robuste**, **maintenable**.
- Utilise-les **partout**, surtout dans les fonctions.
