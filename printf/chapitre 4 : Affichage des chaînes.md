## 4. Affichage de chaînes (`%s`)

Le spécificateur `%s` permet d'afficher une **chaîne de caractères** dans une autre chaîne.

### Exemple :

```c
char *grade;
grade = "junior";
printf("%s est en %s\n", "Fred", grade);
// Affiche : Fred est en junior
```

---

### 4.1 Alignement des chaînes

L'alignement à gauche ou à droite fonctionne aussi pour `%s` :

```c
printf("%5s", "a");     // "    a"
printf("%-5s", "a");    // "a    "
```

| Format     | Résultat         |
|------------|------------------|
| `"%5s"`    | Aligne à droite  |
| `"%-5s"`   | Aligne à gauche  |

### 4.2 Cas particuliers

```c
printf("%5s", "");        // "     "
printf("%5s", "ab");      // "   ab"
printf("%5s", "abcdefg"); // "abcdefg"
```

Si la chaîne dépasse la largeur minimale, elle s'affiche entièrement **sans être tronquée**.

---

### 4.3 Flags inutiles avec `%s`

Les flags suivants **n'ont aucun effet** avec les chaînes :
- `0` (zéro-fill)
- `+` (affichage de signe)
- `' '` (plus invisible)

Seul l’alignement (`-`) est pertinent avec `%s`.
