# 📘 C - Sorting Algorithms & Big O

## 🎯 Objectifs d'apprentissage

✅ À la fin de ce cours, tu devrais être capable d'expliquer :
- Qu'est-ce qu'un algorithme de tri
- Au moins **4 algorithmes de tri différents**
- Comment fonctionne la **notation Big O**
- Quelle est la complexité temporelle d'un algorithme
- Ce qu'est un **tri stable**
- Comment choisir le **meilleur algorithme** selon les cas

---

## 👁️‍🗨️ Qu'est-ce qu'un algorithme de tri ?
Un algorithme de tri est une méthode permettant de **réorganiser les éléments d'une liste** (ou tableau) selon un ordre donné : croissant, décroissant, alphabétique, etc.

---

## 🌟 Algorithmes de tri fondamentaux

### 1. **Bubble Sort (tri à bulles)**
**Principe :** On compare deux à deux les éléments et on les échange si besoin.

**Complexité :**  
- Meilleur cas : O(n)  
- Moyen cas : O(n²)  
- Pire cas : O(n²)

### 2. **Insertion Sort (tri par insertion)**
**Principe :** On insère chaque élément à sa bonne position dans la partie déjà triée.

**Complexité :**  
- Meilleur cas : O(n)  
- Moyen cas : O(n²)  
- Pire cas : O(n²)

### 3. **Selection Sort (tri par sélection)**
**Principe :** On cherche le plus petit élément et on le place au début.

**Complexité :**  
- Tous les cas : O(n²)

### 4. **Quick Sort**
**Principe :**
- Choisir un **pivot**
- Diviser le tableau en deux sous-tableaux
- Appliquer le tri récursivement

**Complexité :**  
- Meilleur / moyen cas : O(n log n)  
- Pire cas (pivot mal choisi) : O(n²)

### 5. **Merge Sort (tri fusion)**
**Principe :** Diviser pour régner. On divise en sous-tableaux jusqu'à n = 1, puis on fusionne en triant.

**Complexité :**  
- Tous les cas : O(n log n)

---

## 🤨 Stabilité des algorithmes de tri
Un algorithme de tri est **stable** s'il **conserve l'ordre relatif** des éléments égaux.

| Algorithme       | Stable ? | In-place ? |
|------------------|----------|------------|
| Bubble Sort      | ✅       | ✅         |
| Insertion Sort   | ✅       | ✅         |
| Selection Sort   | ❌       | ✅         |
| Merge Sort       | ✅       | ❌         |
| Quick Sort       | ❌       | ✅         |
| Counting Sort    | ✅       | ❌         |
| Radix Sort       | ✅       | ❌         |
| Heap Sort        | ❌       | ✅         |

---

## 🌍 Notation Big O

La **notation Big O** décrit la complexité **temporelle** ou **spatiale** d’un algorithme selon la taille des données d’entrée.

### ⏳ Table de complexité temporelle

| Notation        | Nom                          | Interprétation                            | Exemple(s) typique(s)                     |
|-----------------|------------------------------|--------------------------------------------|-------------------------------------------|
| O(1)            | Temps constant                | Même durée, peu importe la taille des données | Accès direct à un tableau                |
| O(log n)        | Logarithmique                 | Très efficace, doublement de données → +1 op | Recherche binaire, arbre équilibré       |
| O(n)            | Linéaire                      | Proportionnel à la taille d’entrée         | Boucle simple, recherche dans une liste   |
| O(n log n)      | Quasi-linéaire                | Très efficace pour le tri                  | Merge Sort, Quick Sort                    |
| O(n²)           | Quadratique                   | Boucles imbriquées                         | Bubble / Insertion / Selection Sort       |
| O(n³)           | Cubique                       | 3 boucles imbriquées                       | Calculs de matrice naïfs                  |
| O(2ⁿ)           | Exponentielle                 | Explosion rapide du temps de calcul       | Problèmes de décision récursifs           |
| O(n!)           | Factorielle                   | Très lent, souvent inutilisable            | Génération de permutations                |

---

## 🧠 Complexité spatiale (mémoire utilisée)

| Notation  | Nom                    | Exemple d'utilisation                     |
|-----------|------------------------|-------------------------------------------|
| O(1)      | Constante              | Algo "in-place", sans mémoire supplémentaire |
| O(n)      | Linéaire               | Stockage de tableau intermédiaire         |
| O(log n)  | Logarithmique          | Pile d’appels récursifs                   |
| O(n log n)| Quasi-linéaire         | Merge Sort (pile + fusion)                |

---

## ⚖️ Comparatif des algorithmes de tri

| Algorithme       | Meilleur cas | Moyen cas   | Pire cas    | Stable | In-place |
|------------------|--------------|-------------|-------------|--------|----------|
| Bubble Sort      | O(n)         | O(n²)       | O(n²)       | ✅     | ✅       |
| Insertion Sort   | O(n)         | O(n²)       | O(n²)       | ✅     | ✅       |
| Selection Sort   | O(n²)        | O(n²)       | O(n²)       | ❌     | ✅       |
| Merge Sort       | O(n log n)   | O(n log n)  | O(n log n)  | ✅     | ❌       |
| Quick Sort       | O(n log n)   | O(n log n)  | O(n²)       | ❌     | ✅       |
| Heap Sort        | O(n log n)   | O(n log n)  | O(n log n)  | ❌     | ✅       |
| Counting Sort    | O(n + k)     | O(n + k)    | O(n + k)    | ✅     | ❌       |
| Radix Sort       | O(nk)        | O(nk)       | O(nk)       | ✅     | ❌       |

---

## 🤯 Comment choisir le bon tri ?

| Situation spécifique                  | Algorithme recommandé     |
|--------------------------------------|----------------------------|
| Liste petite ou déjà triée           | Insertion Sort             |
| Performance optimale attendue        | Quick Sort (en général)    |
| Stabilité nécessaire                 | Merge Sort, Counting Sort  |
| Très grands ensembles de données     | Quick Sort ou Heap Sort    |
| Trie d'entiers sur plage limitée     | Counting Sort              |
| Trie de chaînes de caractères (par caractère) | Radix Sort         |

---

## 🳱 Astuces de test
- Utilise `print_array()` pour afficher un tableau
- Utilise `print_list()` pour afficher une liste doublement chaînée
- Teste avec de gros tableaux de nombres aléatoires (ex: [random.org](https://www.random.org/))

---

## 📚 Récapitulatif
- ✔ Connaître : Bubble, Insertion, Merge, Quick
- ✔ Notation Big O = mesurer la performance
- ✔ Merge Sort = efficace ET stable
- ✔ Quick Sort = ultra rapide en pratique (moyenne)
- ✔ Savoir adapter l'algorithme à ton besoin

---

## 📁 Structure à utiliser

```c
typedef struct listint_s
{
    const int n;
    struct listint_s *prev;
    struct listint_s *next;
} listint_t;
