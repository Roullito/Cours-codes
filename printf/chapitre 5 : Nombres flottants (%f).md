## 5. Affichage des nombres flottants (`%f`)

Les **nombres flottants** (comme `3.1415`) sont gérés avec les mêmes principes que les entiers, mais avec des options supplémentaires liées à la **précision** après la virgule.

---

### 5.1 La précision (`precision`)

Elle détermine le **nombre de chiffres après la virgule**.  
On l’indique avec un point suivi d’un chiffre, par exemple `%.2f`.

```c
float e = 2.718281828;

printf("%.0f", e);   // 3
printf("%.1f", e);   // 2.7
printf("%.2f", e);   // 2.72
printf("%.6f", e);   // 2.718282
```

💡 **Par défaut**, `printf` utilise une précision de **6 chiffres** pour `%f`.

---

### 5.2 Largeur et précision combinées

On peut combiner largeur (`width`) et précision (`precision`) :

```c
printf("%5.2f", e);   // " 2.72"
printf("%8.3f", e);   // "  2.718"
```

🚨 Attention : `%5.2f` signifie :
- Largeur **totale** : 5
- **2 chiffres** après la virgule
> Ce n’est **pas** "5 chiffres avant la virgule et 2 après".

---

### 5.3 Flags avec `%f`

Tous les flags vus précédemment fonctionnent avec `%f` :
- `+` → ajoute le signe
- `-` → aligne à gauche
- `0` → ajoute des zéros à gauche
- `' '` → ajoute un espace pour les positifs

#### Exemples :

```c
printf("%5.1f", e);     // "  2.7"
printf("%-5.1f", e);    // "2.7  "
printf("%+5.1f", e);    // " +2.7"
printf("%+-5.1f", e);   // "+2.7 "
printf("%05.1f", e);    // "002.7"
printf("%+05.1f", e);   // "+02.7"
```

✅ Ces options permettent un affichage **aligné**, **lisible**, et **adapté à tous les cas d'usage**.

---

Prêt pour le **chapitre 6 : Comment concevoir le bon spécificateur de format** ? 🎯
