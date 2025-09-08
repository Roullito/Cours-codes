
# 🌀 Python - Exécution Asynchrone : Cours Complet

## 🎯 Introduction
La programmation asynchrone est un paradigme qui permet d’exécuter plusieurs tâches de manière concurrente sans bloquer le programme principal. En Python, le module `asyncio` ainsi que les mots-clés `async` et `await` forment le socle de cette exécution. Ce modèle est essentiel pour créer des applications efficaces centrées sur l’I/O comme des scrapers web, des clients API ou des applications en temps réel.

---

## 🧠 Concepts Fondamentaux

### ✅ Coroutine
Une coroutine est une fonction spéciale qui peut suspendre son exécution puis la reprendre plus tard. Elle est définie avec `async def`.

```python
async def greet():
    print("Bonjour")
```

### ✅ Boucle d’événement (Event Loop)
La boucle d’événement est chargée d’exécuter les tâches asynchrones. Elle gère la planification et garantit que les tâches rendent le contrôle efficacement.

```python
import asyncio
asyncio.run(greet())
```

### ✅ async et await
- `async` : Définit une fonction asynchrone.
- `await` : Suspend l’exécution jusqu’à la fin d’une autre coroutine.

```python
async def exemple():
    await asyncio.sleep(1)
    print("Fini de dormir")
```

---

## ⏱ Exécution Séquentielle vs Concurrente

### Exemple Séquentiel

```python
import asyncio

async def dire_bonjour():
    await asyncio.sleep(1)
    print("Bonjour")

async def dire_monde():
    await asyncio.sleep(1)
    print("Monde")

async def main():
    await dire_bonjour()
    await dire_monde()

asyncio.run(main())  # Prend ~2 secondes
```

### Exemple Concurrent

```python
async def main():
    await asyncio.gather(dire_bonjour(), dire_monde())

asyncio.run(main())  # Prend ~1 seconde
```

---

## 🌐 Exemple Réel avec aiohttp

```python
import aiohttp
import asyncio

async def fetch_url(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()

async def main():
    urls = ["https://example.com", "https://example.org"]
    tasks = [fetch_url(url) for url in urls]
    results = await asyncio.gather(*tasks)
    for url, content in zip(urls, results):
        print(f"{url} : {len(content)} octets")

asyncio.run(main())
```

---

## ⚖️ Coroutines vs Threads

| Caractéristique     | Coroutines                            | Threads                                 |
|---------------------|---------------------------------------|------------------------------------------|
| Contrôle            | Coopératif (`await`)                  | Prédictif (géré par l’OS)                |
| Optimal pour        | Tâches I/O                            | Tâches CPU                               |
| Consommation mémoire| Très légère                           | Élevée (changement de contexte)          |
| Problèmes de concurrence | Très rares                     | Conditions de course, deadlocks possibles|
| Parallélisme        | Monothreadé (pas de parallélisme réel)| Multicœur (parallélisme réel)            |

---

## 🧪 Mesurer les performances

```python
import time

def measure_time(n, max_delay):
    start = time.time()
    asyncio.run(wait_n(n, max_delay))
    end = time.time()
    return (end - start) / n
```

---

## 🧱 Création et Planification des Tâches

### Créer une tâche

```python
async def retarde():
    await asyncio.sleep(1)
    return 1

task = asyncio.create_task(retarde())
await task  # Attente de la complétion
```

---

## 📦 Modules Utilisés

- `asyncio` : Bibliothèque centrale pour la programmation asynchrone.
- `aiohttp` : Client HTTP asynchrone pour le scraping web ou les APIs.
- `random` : Pour simuler des délais ou comportements variables.

---

## 🧰 Cas d’utilisation

- Applications de chat
- Dashboards en temps réel
- Pipelines de données
- Crawlers web
- Serveurs de jeux en ligne

---

## 🧵 Résumé

| Concept         | Description                                                      |
|------------------|------------------------------------------------------------------|
| Coroutine        | Fonction async suspendable/reprenante                           |
| Event Loop       | Planificateur principal des tâches asynchrones                  |
| await            | Met en pause jusqu’à fin d’une coroutine                        |
| asyncio.gather   | Exécute plusieurs coroutines en parallèle                       |
| create_task      | Lance une coroutine comme tâche indépendante                    |

---

## 📚 Ressources
- [Documentation asyncio](https://docs.python.org/3/library/asyncio.html)
- [Real Python - Async en Python](https://realpython.com/async-io-python/)
- [Projet Holberton - Async Execution](https://github.com/holbertonschool-web_back_end)

---

## ✅ Rappels pour Holberton
- Python 3.9 sur Ubuntu 20.04
- Fichiers exécutables et avec annotations de types obligatoires
- Première ligne : `#!/usr/bin/env python3`
- Respect du style PEP8 via `pycodestyle 2.5.x`

---

**🧠 Astuce** : Utilise la programmation asynchrone pour les charges I/O importantes afin de gagner en efficacité, scalabilité et réactivité !
