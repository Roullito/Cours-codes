# Python - Compréhensions Asynchrones

## Introduction

Les compréhensions asynchrones sont un outil puissant en Python, combinant les avantages des expressions génératrices avec la programmation asynchrone. Ces constructions sont utiles lorsqu'on travaille avec des générateurs ou itérateurs asynchrones.

## Générateur Asynchrone

Un générateur asynchrone est une fonction qui retourne un itérateur asynchrone. Il peut utiliser `await` et `yield` pour produire des valeurs de manière asynchrone.

```python
import asyncio
import random

async def async_generator():
    for _ in range(10):
        await asyncio.sleep(1)
        yield random.uniform(0, 10)
```

Ce générateur va produire 10 nombres aléatoires entre 0 et 10, avec une pause de 1 seconde entre chaque.

## Compréhension Asynchrone

En utilisant ce générateur asynchrone, on peut utiliser des compréhensions asynchrones pour collecter des données de manière efficace.

```python
async def async_comprehension():
    return [i async for i in async_generator()]
```

Cette compréhension s'exécute de façon asynchrone et rassemble les résultats dans une liste.

## Exécution parallèle et mesure du temps

Pour accélérer l'exécution, on peut lancer plusieurs compréhensions en parallèle.

```python
import asyncio
import time

async def measure_runtime():
    start = time.time()
    await asyncio.gather(*(async_comprehension() for _ in range(4)))
    return time.time() - start
```

Cela va exécuter quatre compréhensions en parallèle. Comme chaque itération comprend un délai de 1 seconde, le temps total est d’environ 10 secondes, et non 40.

## Conclusion

Les compréhensions asynchrones permettent de collecter efficacement des données issues de générateurs asynchrones. Elles sont particulièrement utiles lorsqu'on traite des tâches liées à l’I/O comme des appels API ou des flux en temps réel.
