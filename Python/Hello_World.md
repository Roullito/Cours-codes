# Cours Complet : Introduction à Python (Holberton School)

## 📌 Objectifs pédagogiques

À la fin de ce cours, vous serez capable de :

* Utiliser l'interpréteur Python (REPL) en ligne de commande
* Comprendre les différences entre compilation et interprétation
* Manipuler des chaînes de caractères, des variables et le formatage
* Respecter les règles de style Python avec `pycodestyle` (PEP8)
* Lire et comprendre le bytecode Python grâce au module `dis`
* Comprendre la structure d'un interpréteur Python avec Byterun
* Visualiser les notions de stack, frame, et environnement d'exécution

---

## 🧭 Table des matières

* [1. Introduction à Python](#1-introduction-à-python)
* [2. Utiliser l'interpréteur Python](#2-utiliser-linterpréteur-python)
* [3. Affichage et formatage avec `print`](#3-affichage-et-formatage-avec-print)
* [4. Chaînes, indexation et slicing](#4-chaînes-indexation-et-slicing)
* [5. Style de code Python : `pycodestyle` et PEP8](#5-style-de-code-python-pycodestyle-et-pep8)
* [6. Compilation, Bytecode et Disassembleur `dis`](#6-compilation-bytecode-et-disassembleur-dis)
* [7. Byterun : Créer un interpréteur Python en Python](#7-byterun-créer-un-interpréteur-python-en-python)
* [8. Comprendre les frames et la pile d'appels](#8-comprendre-les-frames-et-la-pile-dappels)
* [9. Typage dynamique et limites de l'analyse statique](#9-typage-dynamique-et-limites-de-lanalyse-statique)
* [10. Ressources complémentaires](#10-ressources-complémentaires)

---

## 1. Introduction à Python

Python est un langage de programmation haut niveau, dynamique, interprété et multi-paradigme. Il est souvent utilisé pour :

* Le scripting et l’automatisation
* La data science
* Le développement web
* L’IA, la robotique ou la cybersécurité

Son grand avantage : **il privilégie la lisibilité et la simplicité**.

## 2. Utiliser l’interpréteur Python

L’interpréteur Python permet d’exécuter du code ligne par ligne :

```bash
$ python3
>>> print("Hello World")
Hello World
```

On l’appelle aussi REPL : **Read Eval Print Loop**.

On peut aussi exécuter un fichier `.py` :

```bash
$ chmod +x hello.py
$ ./hello.py
```

Contenu minimal du fichier :

```python
#!/usr/bin/python3
print("Hello World")
```

## 3. Affichage et formatage avec `print`

La fonction `print()` permet d’afficher du texte et des variables :

```python
name = "Jean"
age = 24
print("Bonjour", name, "! Tu as", age, "ans.")
```

### 🧪 Formatage avec f-strings (recommandé)

```python
print(f"Bonjour {name}, tu as {age} ans.")
```

### Autres méthodes :

```python
print("Bonjour {} ! Tu as {} ans.".format(name, age))
print("Bonjour %s ! Tu as %d ans." % (name, age))
```

→ Voir [realpython.com/python-f-strings](https://realpython.com/python-f-strings/)

## 4. Chaînes, indexation et slicing

Python permet d’accéder à une sous-partie d’une chaîne :

```python
s = "Python"
print(s[0])    # P
print(s[-1])   # n
print(s[1:4])  # yth
```

* `s[start:end]` → extrait du caractère à l’index `start` jusqu’à `end - 1`
* `s[:3]` → du début à l’index 2
* `s[::2]` → un caractère sur deux

## 5. Style de code Python : `pycodestyle` et PEP8

Python a un style officiel : **PEP8**. Pour l’appliquer automatiquement :

```bash
$ pip install pycodestyle
$ pycodestyle mon_script.py
```

Exemples de règles :

* Indentation : 4 espaces
* Longueur de ligne : 79 caractères
* Variables en snake\_case
* Fonctions séparées par deux lignes

🔗 Lien vers la doc : [pycodestyle](https://pycodestyle.pycqa.org)

## 6. Compilation, Bytecode et Disassembleur `dis`

Même si Python est interprété, **il compile d'abord en bytecode**.

Exemple :

```python
def somme(a, b):
    return a + b
```

```python
import dis
dis.dis(somme)
```

→ Affiche le bytecode généré (instructions exécutées par l’interpréteur CPython)

## 7. Byterun : Créer un interpréteur Python en Python

Byterun est une implémentation de l’interpréteur Python **en Python**. Elle permet de comprendre :

* Comment fonctionne une machine à pile (stack machine)
* Comment sont gérées les instructions : `LOAD_VALUE`, `ADD_TWO_VALUES`, etc.

Extrait minimal :

```python
class Interpreter:
    def __init__(self):
        self.stack = []

    def LOAD_VALUE(self, value):
        self.stack.append(value)

    def ADD_TWO_VALUES(self):
        b = self.stack.pop()
        a = self.stack.pop()
        self.stack.append(a + b)
```

On exécute ensuite un dictionnaire `what_to_execute` avec une suite d'instructions.

📘 Voir les détails dans le PDF **500 Lines or Less**.

## 8. Comprendre les frames et la pile d’appels

Chaque appel de fonction crée une **frame** (contexte d’exécution) :

* Elle contient ses propres variables locales
* Elle possède sa propre pile de données (data stack)

```python
def foo():
    a = 1
    b = 2
    return bar(a + b)

def bar(x):
    return x + 1
```

→ Appeler `foo()` empile deux frames : `foo`, puis `bar`.
→ La frame `bar` est supprimée après son `return`

## 9. Typage dynamique et analyse impossible statiquement

Le typage dynamique implique :

* Les types sont **connus uniquement à l’exécution**
* L’interpréteur doit vérifier les types à chaque opération

Exemple :

```python
def mod(a, b):
    return a % b

mod(19, 5)        # 4
mod("by%sde", "teco")  # 'bytecode'
```

→ `%` a un comportement différent selon les types (`int`, `str`, `obj`)
→ Impossible à savoir au moment de la compilation

## 10. Ressources complémentaires

### 📹 Playlist recommandée

* [Learn to Program - YouTube](https://www.youtube.com/playlist?list=PL1A2CSdiySGJQ5yl9ASBdf07fTHiG8WvJ)

### 📚 Documentation

* [Python 3.8.20 Docs](https://docs.python.org/3.8/)
* [PEP8 - Style Guide for Python Code](https://peps.python.org/pep-0008/)
* [PyCodeStyle](https://pycodestyle.pycqa.org/en/latest/)
* PDF : **500 Lines or Less** (cf. pièce jointe)
* Livre conseillé : "Software Design by Example"

---

*Ce document est un cours unifié couvrant l'ensemble des bases théoriques du projet Python « Hello World » de la Holberton School. Des cours séparés approfondiront chaque section.*
