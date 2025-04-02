
# 📘 C - Doubly Linked Lists

---

## 📚 Ressources recommandées

- [What is a Doubly Linked List](https://www.geeksforgeeks.org/doubly-linked-list/)
- YouTube - Doubly Linked Lists (Recherchez "Doubly Linked List in C")

---

## 🎯 Objectifs pédagogiques

À la fin de ce projet, vous devez être capable d'expliquer :
- Ce qu’est une **liste doublement chaînée**
- Comment la construire, l’utiliser et la libérer
- Commencer à chercher de l’information seul, sans trop d’aide extérieure

---

## 🔹 Définition : Liste Doublement Chaînée

Une **liste doublement chaînée (doubly linked list)** est une structure de données composée de **nœuds** contenant :
- Une **valeur**
- Un **pointeur vers le nœud suivant**
- Un **pointeur vers le nœud précédent**

Cela permet un **parcours dans les deux sens**.

---

## 🧱 Structure imposée dans le projet

```c
/**
 * struct dlistint_s - doubly linked list
 * @n: integer
 * @prev: points to the previous node
 * @next: points to the next node
 */
typedef struct dlistint_s
{
    int n;
    struct dlistint_s *prev;
    struct dlistint_s *next;
} dlistint_t;
```

---

## 🆚 Liste simplement vs doublement chaînée

| Caractéristique          | Singly Linked List     | Doubly Linked List        |
|--------------------------|------------------------|----------------------------|
| Parcours                 | Sens unique            | Double sens               |
| Taille mémoire par nœud  | Faible                 | +1 pointeur (`prev`)      |
| Suppression d'un nœud    | Nécessite pointeur précedent | Facile avec `prev`     |
| Utilisation typique      | Simple structures      | Listes complexes, navigation rapide |

---

## ➕ Ajouter un nœud en tête

```c
dlistint_t *add_dnodeint(dlistint_t **head, const int n)
{
    dlistint_t *new_node = malloc(sizeof(dlistint_t));
    if (!new_node)
        return NULL;

    new_node->n = n;
    new_node->prev = NULL;
    new_node->next = *head;

    if (*head)
        (*head)->prev = new_node;

    *head = new_node;
    return new_node;
}
```

---

## 🔄 Parcours de la liste

### Parcours vers l’avant
```c
void print_forward(const dlistint_t *head)
{
    while (head)
    {
        printf("%d\n", head->n);
        head = head->next;
    }
}
```

### Parcours vers l’arrière
```c
void print_backward(const dlistint_t *tail)
{
    while (tail)
    {
        printf("%d\n", tail->n);
        tail = tail->prev;
    }
}
```

---

## 🗑 Libération mémoire

```c
void free_dlistint(dlistint_t *head)
{
    dlistint_t *tmp;
    while (head)
    {
        tmp = head->next;
        free(head);
        head = tmp;
    }
}
```

---

## 📁 Fichier `lists.h` recommandé

```c
#ifndef LISTS_H
#define LISTS_H

#include <stdio.h>
#include <stdlib.h>

/**
 * struct dlistint_s - doubly linked list
 * @n: integer
 * @prev: points to the previous node
 * @next: points to the next node
 */
typedef struct dlistint_s
{
    int n;
    struct dlistint_s *prev;
    struct dlistint_s *next;
} dlistint_t;

dlistint_t *add_dnodeint(dlistint_t **head, const int n);
void free_dlistint(dlistint_t *head);
void print_forward(const dlistint_t *head);
void print_backward(const dlistint_t *tail);

#endif
```

---

## 🛠 Contraintes techniques du projet

- Éditeurs autorisés : `vi`, `vim`, `emacs`
- Compilation :
```bash
gcc -Wall -Werror -Wextra -pedantic -std=gnu89 fichier.c -o programme
```
- Fichiers `.c` limités à 5 fonctions
- Aucun usage de variables globales
- Fonctions autorisées : `malloc`, `free`, `printf`, `exit`
- Tous les headers doivent être protégés (`#ifndef`, `#define`, `#endif`)

---

## 💡 Bonnes pratiques

- Toujours vérifier le retour de `malloc`
- Toujours libérer les listes
- Utiliser Valgrind pour détecter les fuites mémoire :
```bash
valgrind ./programme
```

---

## 🔁 Résumé

- Une liste doublement chaînée permet un **parcours fluide dans les deux sens**
- Facilite l’insertion/suppression au milieu
- Nécessite une bonne gestion de la mémoire et des pointeurs
