
# 🐍 Python - Sérialisation et Marshalling

## 📘 Introduction
La sérialisation et le marshalling sont des processus fondamentaux pour stocker, transmettre ou sauvegarder des objets Python. Ils permettent de transformer des structures de données complexes en formats simples (texte ou binaire) pour les enregistrer ou les envoyer à d'autres programmes ou machines.

## 🔄 Définitions clés

### 🧱 Marshalling
- **Définition** : Transformation d’un objet Python en une représentation portable en mémoire (souvent binaire), utilisée pour les appels de procédure distante (RPC) ou les communications inter-processus.
- **Utilité** : Transfert de données entre systèmes hétérogènes ou environnements différents.

### 📦 Sérialisation
- **Définition** : Conversion d’un objet Python en une chaîne de caractères ou un format binaire pour l’enregistrement ou la transmission.
- **Utilité** : Sauvegarde d'état, communication réseau, cache, API, fichiers de configuration, etc.

## 💡 Différences
| Critère         | Marshalling                     | Sérialisation                   |
|------------------|----------------------------------|----------------------------------|
| Objectif         | Transfert mémoire/plateformes   | Stockage ou transmission        |
| Portabilité      | Moins portable (format binaire) | Plus portable (ex: JSON)        |
| Usage courant    | RPC, IPC                        | Web, fichiers, base de données  |
| Exemple Python   | `marshal`                       | `json`, `pickle`, `csv`, `xml`  |

## 🧰 Modules Python de sérialisation

### 1. `json`
```python
import json

# Sérialiser un dictionnaire en JSON
data = {"nom": "Alice", "âge": 30}
json_str = json.dumps(data)

# Désérialiser depuis JSON
obj = json.loads(json_str)
```

### 2. `pickle`
```python
import pickle

# Sérialiser un objet en binaire
with open("data.pkl", "wb") as f:
    pickle.dump(data, f)

# Lire l'objet
with open("data.pkl", "rb") as f:
    data_restored = pickle.load(f)
```

### 3. `marshal`
```python
import marshal

code = compile('print("Bonjour")', '', 'exec')
with open("code.marshal", "wb") as f:
    marshal.dump(code, f)
```

### 4. `csv`
```python
import csv

# Écrire
with open("data.csv", "w", newline='') as f:
    writer = csv.writer(f)
    writer.writerow(["Nom", "Âge"])
    writer.writerow(["Alice", 30])

# Lire
with open("data.csv", newline='') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

### 5. `xml` avec `ElementTree`
```python
import xml.etree.ElementTree as ET

root = ET.Element("personne")
ET.SubElement(root, "nom").text = "Alice"
ET.SubElement(root, "age").text = "30"

tree = ET.ElementTree(root)
tree.write("personne.xml")
```

## 🚨 Problèmes fréquents

- ❌ Sérialiser des objets non supportés (ex: objets complexes avec des sockets)
- ⚠️ Risques de sécurité avec `pickle` (peut exécuter du code malveillant)
- ⚠️ Incompatibilités entre versions Python ou plateformes
- 🔐 Fichiers sérialisés peu lisibles ou non interopérables

## 🧪 Cas pratiques
- Enregistrer des sessions utilisateur
- Communiquer entre microservices (JSON via HTTP)
- Sauvegarder des modèles d’IA entraînés (`pickle`)
- Lire/écrire des configurations (`json` ou `.ini`)

## 🧠 Bonnes pratiques

- ✅ Privilégier `json` pour la portabilité et la lisibilité
- ✅ Utiliser `with` pour ouvrir les fichiers
- ❌ Éviter `pickle` pour les données non sécurisées
- ✅ Tester la désérialisation dans un environnement sûr
- ✅ Documenter les formats sérialisés (clé/valeur, structures)

## 📚 Ressources complémentaires

- [Documentation JSON Python](https://docs.python.org/3/library/json.html)
- [Pickle Python](https://docs.python.org/3/library/pickle.html)
- [Automate the Boring Stuff - Fichiers](https://automatetheboringstuff.com/)
- [ElementTree - XML](https://docs.python.org/3/library/xml.etree.elementtree.html)

## ✅ À retenir

- La sérialisation est essentielle pour la persistance et la communication.
- Plusieurs formats existent selon les besoins : lisibilité, performance, sécurité.
- Bien comprendre chaque module permet de choisir le bon outil.

