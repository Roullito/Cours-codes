
# 🧠 Python – More Data Structures: Set, Dictionary

## 📚 Objectifs pédagogiques

À la fin de ce cours, tu seras capable de :

- Comprendre les structures de données `set` (ensemble) et `dict` (dictionnaire) en Python.
- Manipuler efficacement des ensembles et des dictionnaires.
- Éviter les erreurs courantes en situation réelle.
- Choisir la bonne structure de données selon le problème.

---

## 🔹 SET (ENSEMBLE)

### ✅ Définition

Un `set` est une **collection non ordonnée, non indexée et sans doublon**.

```python
fruits = {"pomme", "banane", "pomme", "orange"}
print(fruits)  # {'banane', 'pomme', 'orange'} — pas de doublon
```

### ⚙️ Création

```python
mon_set = set()
autre_set = set(["a", "b", "c"])
```

### 📌 Propriétés

- Pas d’indexation (`set[0]` ❌)
- Pas de doublons
- Non ordonné (l’ordre peut changer à chaque exécution)

### 🔧 Méthodes courantes

```python
s = {1, 2, 3}
s.add(4)           # ajoute un élément
s.remove(2)        # supprime l'élément (KeyError si absent)
s.discard(5)       # supprime l'élément si présent, sinon ne fait rien
s.clear()          # vide le set
```

### 🔄 Opérations ensemblistes

```python
a = {1, 2, 3}
b = {3, 4, 5}

a | b     # union -> {1, 2, 3, 4, 5}
a & b     # intersection -> {3}
a - b     # différence -> {1, 2}
a ^ b     # différence symétrique -> {1, 2, 4, 5}
```

### ⚠️ Erreurs fréquentes

- Tenter d’accéder à un `set` avec un index
- Oublier que `add()` ajoute UN élément, et non une séquence

```python
s = set()
s.add([1, 2])  # ❌ TypeError: unhashable type: 'list'
```

---

## 🔹 DICTIONNAIRE (`dict`)

### ✅ Définition

Un dictionnaire est une **collection de paires clé/valeur**, indexée, modifiable et sans doublons de clés.

```python
personne = {
    "nom": "Alice",
    "âge": 25,
    "ville": "Paris"
}
```

### ⚙️ Création

```python
d = dict()
d2 = {"a": 1, "b": 2}
d3 = dict(nom="Bob", age=30)
```

### 🔍 Accès aux valeurs

```python
print(personne["nom"])  # Alice
print(personne.get("email"))  # None (ou valeur par défaut)
```

### ✍️ Modification

```python
personne["âge"] = 26
personne["email"] = "alice@email.com"
```

### ❌ Suppression

```python
del personne["ville"]
email = personne.pop("email", None)
personne.clear()
```

### 🔄 Parcours d’un dictionnaire

```python
for cle in personne:
    print(cle, personne[cle])

for k, v in personne.items():
    print(k, v)
```

### 📌 Méthodes utiles

```python
d.keys()       # retourne une vue des clés
d.values()     # retourne une vue des valeurs
d.items()      # retourne une vue des paires
d.update(d2)   # fusionne deux dictionnaires
```

### ⚠️ Problèmes courants

- Accès direct à une clé inexistante → `KeyError`
- Attention à la **mutabilité** : deux dictionnaires peuvent référencer le même objet.

```python
d1 = {"a": [1, 2]}
d2 = d1.copy()
d2["a"].append(3)
print(d1)  # {'a': [1, 2, 3]} — effet de bord !
```

### ✅ Solution : copie profonde

```python
import copy
d2 = copy.deepcopy(d1)
```

---

## 🧠 Comparatif : Set vs Dict

| Critère        | set                          | dict                                 |
|----------------|-------------------------------|--------------------------------------|
| Doublons       | Non                          | Clés uniques, valeurs possibles en doublon |
| Accès direct   | ❌ (non indexé)              | ✅ via clé                            |
| Valeurs        | Uniques uniquement            | Paire clé/valeur                     |
| Performance    | Très rapide (hashé)           | Très rapide (hashé)                  |

---

## 🚧 Cas réels et bonnes pratiques

- 🔍 Tester l’existence d’un élément :
```python
if "clé" in mon_dict:
```

- 🎯 Choisir `set` pour vérifier appartenance, supprimer doublons, opérations mathématiques.
- 🎯 Choisir `dict` pour stocker des relations (clé/valeur), des objets mappés, des index nommés.

---

## 📚 Récapitulatif

| Élément     | Type      | Ordonné | Indexé | Mutable | Doublons |
|-------------|-----------|---------|--------|---------|----------|
| set         | Ensemble  | Non     | Non    | Oui     | Non      |
| dict        | Mapping   | Oui¹    | Oui    | Oui     | Clés : Non / Valeurs : Oui |

¹ Depuis Python 3.7, les dicts conservent l’ordre d’insertion.

---

## 🧪 À toi de jouer

- Crée un `set` avec des valeurs dupliquées. Que se passe-t-il ?
- Manipule un dictionnaire imbriqué (ex : `{"user": {"name": "Bob"}}`).
- Crée une fonction qui prend un dictionnaire et retourne la clé avec la valeur maximale.

---

🚀 Tu maîtrises maintenant les ensembles (`set`) et dictionnaires (`dict`) en Python.
