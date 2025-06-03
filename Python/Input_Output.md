# 📘 Python - Input / Output (I/O) Ultra Complet

## 🧠 Objectifs pédagogiques

À la fin de ce cours, tu seras capable d'expliquer (sans Google !) :

- Comment ouvrir, lire, écrire et fermer un fichier en Python
- Comment utiliser `with` pour la gestion automatique des fichiers
- Comment lire un fichier ligne par ligne
- Comment déplacer le curseur dans un fichier (`seek`, `tell`)
- Ce qu’est le JSON et comment le manipuler (sérialisation / désérialisation)
- Comment utiliser les arguments passés en ligne de commande (`sys.argv`)

---

## 🔹 1. Ouvrir un fichier en Python

```python
f = open("fichier.txt", "r")  # modes possibles : 'r', 'w', 'a', 'b', etc.
```

| Mode | Description |
|------|-------------|
| 'r'  | Lecture (erreur si le fichier n’existe pas) |
| 'w'  | Écriture (crée un nouveau fichier ou écrase) |
| 'a'  | Ajout à la fin |
| 'b'  | Binaire |
| 'x'  | Crée un nouveau fichier, échoue s’il existe |
| 't'  | Texte (par défaut) |

---

## 🔹 2. Lire un fichier

```python
f = open("example.txt", "r")
content = f.read()
f.close()
```

### Lire ligne par ligne

```python
with open("example.txt", "r") as f:
    for line in f:
        print(line.strip())
```

### Lire N caractères

```python
f = open("example.txt", "r")
print(f.read(10))  # lit les 10 premiers caractères
f.close()
```

---

## 🔹 3. Écrire dans un fichier

```python
with open("output.txt", "w") as f:
    f.write("Hello, World!")
```

### Ajouter à la fin du fichier

```python
with open("output.txt", "a") as f:
    f.write("Nouvelle ligne")
```

---

## 🔹 4. Se déplacer dans un fichier

```python
f = open("example.txt", "r")
print(f.tell())     # position actuelle
f.seek(0)           # revient au début
print(f.read(5))
f.close()
```

---

## 🔹 5. Gestion automatique : le mot-clé `with`

```python
with open("file.txt", "r") as f:
    data = f.read()
# plus besoin de f.close()
```

---

## 🔹 6. Manipulation de JSON

### Sérialisation (Python -> JSON)

```python
import json

data = {"name": "Alice", "age": 25}
json_string = json.dumps(data)
```

### Écriture dans un fichier

```python
with open("data.json", "w") as f:
    json.dump(data, f)
```

### Désérialisation (JSON -> Python)

```python
with open("data.json", "r") as f:
    data = json.load(f)
```

### Depuis une string

```python
data = json.loads(json_string)
```

---

## 🔹 7. Accès aux arguments de la ligne de commande

```python
import sys

print("Nom du script :", sys.argv[0])
print("Arguments :", sys.argv[1:])
```

```bash
$ python mon_script.py param1 param2
```

---

## 🔍 Problèmes fréquents

| Problème | Solution |
|---------|----------|
| `ValueError: I/O operation on closed file` | Vérifie que tu n'as pas fermé le fichier avant la lecture/écriture |
| Encodage erroné | Ajoute `encoding="utf-8"` à `open()` |
| Fichier non trouvé | Vérifie le chemin relatif/absolu |
| Données JSON mal formatées | Utilise `try...except json.JSONDecodeError` |

---

## ✅ Bonnes pratiques

- Toujours utiliser `with` pour ouvrir les fichiers
- Penser à `.strip()` pour nettoyer les lignes lues
- Toujours valider les types avant la sérialisation JSON
- Ne jamais écrire ou lire en dur des chemins de fichiers : utiliser `os.path`

---

## 📚 Liens utiles

- [Lecture / écriture de fichiers – Python docs](https://docs.python.org/3/tutorial/inputoutput.html)
- [JSON – Python docs](https://docs.python.org/3/library/json.html)
- [Automate the Boring Stuff - Fichiers](https://automatetheboringstuff.com/)
