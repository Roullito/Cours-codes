
# 📘 C - Listes Simples Chaînées (Singly Linked Lists)

---

## 📚 Ressources recommandées

- [Linked Lists (GeeksForGeeks)](https://www.geeksforgeeks.org/data-structures/linked-list/)
- [YouTube - Singly Linked List](https://www.youtube.com/results?search_query=singly+linked+list+in+c)

---

## 🎯 Objectifs pédagogiques

Tu dois être capable d'expliquer **sans Google** :
- Ce qu'est une liste simplement chaînée
- Comment construire et utiliser une liste chaînée
- Pourquoi et quand préférer une liste chaînée à un tableau

---

## 🔹 Qu'est-ce qu'une liste simplement chaînée ?

Une **liste simplement chaînée** est une structure de données dynamique composée de **nœuds**. Chaque nœud contient :
- Une valeur (`str`)
- Un entier (`len`)
- Un pointeur vers le nœud suivant (`next`)

Elle ne permet qu’un parcours **dans un seul sens** (vers l’avant).

### Exemple visuel :

```
["Hello"] -> ["World"] -> ["Holberton"] -> NULL
```

---

## 🧱 Structure imposée dans le projet

```c
/**
 * struct list_s - nœud d'une liste simplement chaînée
 * @str: chaîne de caractères allouée dynamiquement
 * @len: longueur de la chaîne
 * @next: pointeur vers le nœud suivant
 */
typedef struct list_s
{
    char *str;
    unsigned int len;
    struct list_s *next;
} list_t;
```

---

## 🔸 Listes chaînées vs Tableaux

| Critère                | Tableaux           | Listes chaînées         |
|------------------------|--------------------|--------------------------|
| Taille                 | Fixe               | Dynamique                |
| Accès à un élément     | Direct (`arr[i]`)  | Séquentiel               |
| Insertion/Suppression  | Difficile          | Facile en tête ou milieu |
| Allocation mémoire     | Statique           | Dynamique (`malloc`)     |
| Utilisation mémoire    | Optimisée          | Surcharge (pointeur)     |

---

## ⚙️ Création d'un nœud

```c
list_t *new_node = malloc(sizeof(list_t));
if (!new_node)
    return NULL;

new_node->str = strdup("Hello");
new_node->len = strlen("Hello");
new_node->next = NULL;
```

---

## ➕ Ajouter un nœud en tête

```c
list_t *add_node(list_t **head, const char *str)
{
    list_t *new = malloc(sizeof(list_t));
    if (!new)
        return NULL;

    new->str = strdup(str);
    new->len = strlen(str);
    new->next = *head;
    *head = new;

    return new;
}
```

---

## 📄 Parcourir et afficher la liste

```c
size_t print_list(const list_t *h)
{
    size_t count = 0;
    while (h)
    {
        if (h->str)
            printf("[%u] %s\n", h->len, h->str);
        else
            printf("[0] (nil)\n");
        h = h->next;
        count++;
    }
    return count;
}
```

---

## 📉 Libérer la mémoire de la liste

```c
void free_list(list_t *head)
{
    list_t *tmp;
    while (head)
    {
        tmp = head->next;
        free(head->str);
        free(head);
        head = tmp;
    }
}
```

---

## 🔌 Contraintes techniques du projet

- Compilation :
```bash
gcc -Wall -Werror -Wextra -pedantic -std=gnu89 fichier.c -o programme
```
- Aucune utilisation de : `printf`, `puts`, `calloc`, `realloc`, etc.
- Seules fonctions autorisées : `malloc`, `free`, `exit`, `_putchar`
- Maximum **5 fonctions par fichier**
- **Aucune variable globale**
- Tous les `.h` doivent être protégés avec `#ifndef`

---

## 📁 Exemple de fichier `lists.h`

```c
#ifndef LISTS_H
#define LISTS_H

#include <stdlib.h>
#include <string.h>
#include <stdio.h>

typedef struct list_s
{
    char *str;
    unsigned int len;
    struct list_s *next;
} list_t;

size_t print_list(const list_t *h);
list_t *add_node(list_t **head, const char *str);
void free_list(list_t *head);

#endif
```

---

## 🚀 Astuces et bonnes pratiques

- Toujours vérifier le retour de `malloc`
- Toujours `free` les chaînes **et** les nœuds
- Utiliser Valgrind pour détecter les fuites mémoire :

```bash
valgrind ./ton_programme
```

---

## 🔁 Récapitulatif rapide

- Liste simplement chaînée = ensemble de nœuds liés via `next`
- Meilleure flexibilité qu’un tableau
- Allocation dynamique avec `malloc`, libération avec `free`
