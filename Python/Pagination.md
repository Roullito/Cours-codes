# 📚 Cours ultra détaillé : Pagination

## 🎯 Objectifs pédagogiques

À la fin de ce cours, vous serez capable d'expliquer et d'implémenter :
- Comment paginer un dataset avec de simples paramètres `page` et `page_size`
- Comment paginer un dataset avec des métadonnées hypermédia (HATEOAS)
- Comment rendre la pagination **résiliente aux suppressions** d’éléments

---

## 1️⃣ Introduction à la pagination

### 📌 Qu’est-ce que la pagination ?

La **pagination** est une technique utilisée pour diviser un grand ensemble de données en plusieurs pages plus petites.
- Elle permet de **limiter la quantité de données envoyées au client** à chaque requête.
- Elle améliore **les performances** et **l’expérience utilisateur**.
- Très courante dans les **API REST**, les sites e-commerce, les moteurs de recherche, etc.

**Exemple concret :**
Sur Google, vous ne voyez pas tous les résultats d’un coup, mais page par page.

### 📊 Avantages :
- Performance améliorée
- Réduction de la charge serveur et réseau
- Meilleure expérience utilisateur

---

## 2️⃣ Pagination simple avec `page` et `page_size`

### 🛠 Principe
- **page** : numéro de la page demandée (commence à 1)
- **page_size** : nombre d’éléments par page
- On calcule **l’index de départ** et **l’index de fin** dans le dataset.

### 📏 Formule
```
start_index = (page - 1) * page_size
end_index = start_index + page_size
```

**Exemple :**
```
page = 3
page_size = 15
start = (3 - 1) * 15 = 30
end = 30 + 15 = 45
```
On retournera les éléments 30 à 44 inclus.

### 💡 Helper function : `index_range`
Cette fonction renvoie un tuple `(start_index, end_index)`.

```python
def index_range(page: int, page_size: int) -> tuple[int, int]:
    return ((page - 1) * page_size, page * page_size)
```

---

## 3️⃣ Implémentation dans une classe `Server`

### 📂 Dataset
On utilise `Popular_Baby_Names.csv` :
- 19 418 lignes
- Colonnes : `Year of Birth, Gender, Ethnicity, Child's First Name, Count, Rank`

```python
def dataset(self) -> List[List]:
    if self.__dataset is None:
        with open(self.DATA_FILE) as f:
            reader = csv.reader(f)
            dataset = [row for row in reader]
        self.__dataset = dataset[1:]  # On enlève l'en-tête
    return self.__dataset
```

### 📜 Méthode `get_page`
- Vérifie que `page` et `page_size` sont **entiers positifs**
- Utilise `index_range` pour déterminer les bornes
- Retourne les lignes correspondantes

---

## 4️⃣ Pagination hypermédia (HATEOAS)

### 📌 Définition
HATEOAS (**Hypermedia as the Engine of Application State**) : une API REST renvoie **non seulement des données**, mais aussi des **liens ou métadonnées** pour naviguer.

### 🔍 Objectif
En plus de `data`, on retourne :
- `page_size`
- `page`
- `next_page`
- `prev_page`
- `total_pages`

### 📐 Calcul de `total_pages`
```
total_pages = ceil(len(dataset) / page_size)
```

### 📦 Exemple de retour :
```json
{
  "page_size": 2,
  "page": 1,
  "data": [["2016", "FEMALE", "Olivia", "172", "1"], ["2016", "FEMALE", "Chloe", "112", "2"]],
  "next_page": 2,
  "prev_page": null,
  "total_pages": 9709
}
```

---

## 5️⃣ Pagination résiliente aux suppressions

### 🚨 Problème
Si des éléments sont supprimés entre deux appels, on peut :
- Perdre des données
- Revoir des données déjà vues

### 💡 Solution
Utiliser un **dataset indexé** :
```python
def indexed_dataset(self) -> Dict[int, List]:
    if self.__indexed_dataset is None:
        dataset = self.dataset()
        self.__indexed_dataset = {i: dataset[i] for i in range(len(dataset))}
    return self.__indexed_dataset
```

### 📜 Méthode `get_hyper_index`
Retourne :
- `index` (début actuel)
- `next_index` (prochain début)
- `page_size`
- `data` (page)

💡 Ici, `next_index` est calculé en **sautant les trous** dus aux suppressions.

---

## 6️⃣ Schéma visuel

```text
Dataset : [0][1][2][3][4][5][6][7][8][9]...
Suppression :      X        X  X
Page 1 (size=3) → [0][1][2]
Page 2 (size=3) → [4][5][8]
```
Les index sautent pour éviter les doublons ou les manques.

---

## 7️⃣ Bonnes pratiques
- Toujours valider les paramètres (`assert` ou exceptions)
- Fournir **des métadonnées** (page, total_pages, next, prev)
- Prévoir la **résilience aux suppressions**
- Ne jamais charger tout le dataset en mémoire pour chaque requête (utiliser cache ou streaming)

---

## 8️⃣ Exemple de cas d’usage API REST
- **E-commerce** : produits par page
- **Réseaux sociaux** : flux infini avec `next_page` basé sur un curseur
- **Recherche** : résultats paginés avec navigation

---

## 📌 Conclusion
La pagination est essentielle pour :
- Performance
- Scalabilité
- Expérience utilisateur

Les 3 niveaux :
1. Pagination simple (`page` / `page_size`)
2. Pagination hypermédia avec métadonnées
3. Pagination résiliente aux suppressions
```
