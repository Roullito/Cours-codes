
# 📦 Python – Import & Modules

## 🎯 Objectifs pédagogiques

À la fin de ce cours, tu sauras :
- Ce qu’est un **module** et un **package** en Python
- Comment importer un module (`import`, `from ... import`, `as`)
- Créer tes propres modules
- Gérer les chemins d’accès (`sys.path`, `PYTHONPATH`)
- Résoudre les problèmes courants liés aux imports

---

## 🧱 1. Qu’est-ce qu’un module ?

Un **module** est un **fichier `.py`** qui contient du code Python (fonctions, classes, variables, etc.).

### 🔍 Exemple de module : `math`
```python
import math
print(math.sqrt(16))  # 4.0
```

---

## 🗂 2. Qu’est-ce qu’un package ?

Un **package** est un **dossier contenant des modules** et un fichier `__init__.py` (même vide). Il permet de structurer ton code.

Structure :
```
mon_package/
├── __init__.py
├── module1.py
└── module2.py
```

---

## 🛠 3. Les différentes syntaxes d'import

### ✅ `import module`
```python
import random
print(random.randint(1, 10))
```

### ✅ `from module import element`
```python
from random import randint
print(randint(1, 10))
```

### ✅ `import module as alias`
```python
import numpy as np
print(np.array([1, 2, 3]))
```

### ✅ `from module import *` (⚠ à éviter)
```python
from math import *
print(sqrt(25))  # Peu lisible
```

---

## ✍️ 4. Créer son propre module

Fichier : `mon_module.py`
```python
def saluer(nom):
    return f"Bonjour, {nom} !"
```

Fichier : `main.py`
```python
import mon_module
print(mon_module.saluer("Alice"))
```

---

## 🧭 5. Où Python cherche-t-il les modules ?

Python recherche dans les chemins de `sys.path`.

```python
import sys
print(sys.path)
```

Tu peux :
- Ajouter un chemin dans `sys.path`
- Utiliser la variable d’environnement `PYTHONPATH`
- Créer un package proprement

---

## 💥 6. Erreurs courantes

| Erreur                            | Cause fréquente                              | Solution                          |
|----------------------------------|-----------------------------------------------|-----------------------------------|
| `ModuleNotFoundError`            | Le fichier n’est pas trouvé                   | Vérifie le chemin ou `sys.path`   |
| `ImportError`                    | Mauvais nom ou problème dans le module        | Vérifie le nom / syntaxe          |
| `AttributeError`                 | Mauvais usage d’un nom dans un module         | Vérifie les fonctions disponibles |

---

## 🔁 7. Cas réels et bonnes pratiques

### ❓ *J’ai un dossier `utils/` avec `string_tools.py` mais Python ne trouve pas le fichier ?*
✅ Ajoute un `__init__.py` dans le dossier `utils/` pour le rendre importable comme package.

---

### ❓ *Je veux appeler une fonction d’un fichier frère dans un sous-dossier ?*
✅ Utilise l’import **relatif** ou ajoute manuellement le chemin à `sys.path`.

---

### ❓ *Je ne veux pas réexécuter le code d’un module à chaque import ?*
✅ Python **ne recharge pas** un module s’il a déjà été importé une fois.

---

## 📚 Récapitulatif

| Type d'import            | Exemple                      |
|--------------------------|------------------------------|
| Importer un module       | `import math`                |
| Importer une fonction    | `from math import sqrt`      |
| Importer avec alias      | `import pandas as pd`        |
| Importer tout (⚠)        | `from math import *`         |
| Module personnalisé      | `import mon_module`          |

---

## 🧪 Exemple final combiné

Fichier : `math_tools.py`
```python
def carre(x):
    return x * x
```

Fichier : `main.py`
```python
from math_tools import carre

for i in range(5):
    print(f"{i}² = {carre(i)}")
```

---

## ✅ À retenir

- Un module = fichier `.py`
- Un package = dossier avec `__init__.py`
- Tu peux créer et importer tes propres modules
- Vérifie toujours les chemins si un import échoue

---

© Holberton-style Python Foundation Course
