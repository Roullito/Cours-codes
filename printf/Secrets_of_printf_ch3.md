## 3. Spécifications de format

La vraie puissance de `printf` se révèle lorsqu’on affiche le contenu de variables.

Exemple :

```c
int age = 25;
printf("J'ai %d ans\n", age); // Affiche : J'ai 25 ans
```

Dans cet exemple, `printf` a deux arguments :
- Une chaîne avec un spécificateur (`%d`)
- Une variable (`age`)

---

### 3.1 Liste des arguments

`printf` lit caractère par caractère. Lorsqu’il rencontre un `%`, il sait qu’un format est à venir.

Exemple :

```c
int x = 5, y = 10;
printf("x vaut %d et y vaut %d\n", x, y);
// Affiche : x vaut 5 et y vaut 10
```

---

### 3.2 Le symbole `%`

Tous les spécificateurs de format commencent par `%` et finissent par une lettre.  
Voici quelques lettres et leur signification :

| Spécificateur | Description                           |
|---------------|----------------------------------------|
| `%c`          | Affiche un caractère                   |
| `%d` / `%i`   | Affiche un entier (base 10)            |
| `%e`          | Affiche un float en notation scientifique |
| `%f`          | Affiche un float                       |
| `%g`          | Format général pour les floats         |
| `%o`          | Affiche un entier en octal             |
| `%s`          | Affiche une chaîne de caractères       |
| `%u`          | Affiche un entier non signé            |
| `%x`          | Affiche un entier en hexadécimal       |
| `%%`          | Affiche le caractère `%`               |

Exemples :

```c
printf("%d", 0);           // 0
printf("%d", -7);          // -7
printf("%d", 1560133635);  // 1560133635
```

---

### 3.3 Option de largeur (`width`)

Permet de forcer un minimum de largeur, en ajoutant des espaces devant.

```c
printf("%5d", 27);   // "   27"
printf("%5d", -7);   // "   -7"
```

Si la valeur dépasse la largeur demandée, elle s'affiche en entier.

---

### 3.4 Remplissage des espaces

Que faire des espaces vides ?

On peut les remplir par défaut avec des espaces, ou bien utiliser :

- Zéros : `%05d` → `00027`
- Étoiles, `$`, `=` → Non supporté par `printf` mais possible manuellement

Ce remplissage est parfois appelé **protection de chèque**.

---

### 3.5 Alignement (Justify)

Aligner à gauche ou à droite ?

| Spécificateur | Résultat                       |
|---------------|--------------------------------|
| `%5d`         | Aligné à droite (par défaut)   |
| `%-5d`        | Aligné à gauche (rempli à droite) |

```c
printf("%-5d", 42); // "42   "
```
