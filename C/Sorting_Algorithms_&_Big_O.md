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

**Complexité :** O(n²)

### 2. **Insertion Sort (tri par insertion)**
**Principe :** On insère chaque élément à sa bonne position dans la partie déjà triée.

**Complexité :** O(n²) dans le pire cas, O(n) dans le meilleur cas (tableau presque trié)

### 3. **Selection Sort (tri par sélection)**
**Principe :** On cherche le plus petit élément et on le place au début.

**Complexité :** O(n²)

### 4. **Quick Sort**
**Principe :**
- Choisir un **pivot**
- Diviser le tableau en deux sous-tableaux
- Appliquer le tri récursivement

**Complexité moyenne :** O(n log n), mais O(n²) dans le pire cas si mal choisi

### 5. **Merge Sort (tri fusion)**
**Principe :** Diviser pour régner. On divise en sous-tableaux jusqu'à n = 1, puis on fusionne en triant.

**Complexité :** O(n log n)

---

## 🤨 Stabilité des algorithmes de tri
Un algorithme de tri est **stable** s'il **conserve l'ordre relatif** des éléments égaux.

| Tri            | Stable ? |
|----------------|----------|
| Bubble Sort    | Oui      |
| Insertion Sort | Oui      |
| Selection Sort | Non      |
| Merge Sort     | Oui      |
| Quick Sort     | Non      |

---

## 🌍 Notation Big O

La **notation Big O** décrit la complexité temporelle ou spatiale d'un algorithme en fonction de la taille des données.

### ⏳ Exemples de complexité
| Notation | Interprétation             |
|----------|-----------------------------|
| O(1)     | Temps constant              |
| O(n)     | Temps linéaire             |
| O(n log n) | Temps quasi-linéaire    |
| O(n²)    | Temps quadratique          |
| O(2^n)   | Temps exponentiel           |

### ✅ Pour le tri :
- **Bubble / Insertion / Selection** : O(n²)
- **Merge / Quick Sort** : O(n log n)

---

## 🤯 Comment choisir le bon tri ?
| Situation                            | Algorithme recommandé  |
|--------------------------------------|--------------------------|
| Données petites / quasi triées       | Insertion Sort           |
| Performance optimale attendue        | Merge ou Quick Sort      |
| Stabilité requise                    | Merge Sort               |
| Mémoire limitée                      | Quick Sort (in-place)    |

---

## 🳱 Astuces de test
- Utilise `print_array()` pour afficher un tableau
- Utilise `print_list()` pour afficher une liste doublement chaînée
- Teste avec des gros tableaux de nombres aléatoires (random.org)

---

## 📚 Récapitulatif
- ✔ 4 tri à connaître : Bubble, Insertion, Merge, Quick
- ✔ Big O permet d'estimer les performances
- ✔ Merge Sort est stable et efficace
- ✔ Quick Sort est souvent très rapide, mais pas stable

---

## 📁 Structure à utiliser
```c
typedef struct listint_s
{
    const int n;
    struct listint_s *prev;
    struct listint_s *next;
} listint_t;
