# 🔄 Flowcharts (Organigrammes de Flux)

## 📌 Qu’est-ce qu’un flowchart ?

Un **flowchart** est un organigramme qui **représente visuellement la logique d’un processus ou d’un algorithme**, grâce à des symboles reliés par des flèches.  
Il permet de **structurer, expliquer ou déboguer** un programme.

---

## ❓ Pourquoi utiliser un flowchart ?

- Structurer son raisonnement avant de coder
- Clarifier un processus complexe
- Communiquer efficacement (même avec des non-développeurs)
- Identifier rapidement les erreurs logiques
- Créer une documentation visuelle

---

## 🔣 Symboles de base (standard ISO 5807)

| Symbole          | Nom                        | Description |
|------------------|-----------------------------|-------------|
| ➡️               | **Flowline**               | Indique la direction du flux |
| 🔵               | **Terminal (Start/End)**    | Début ou fin du processus |
| 🟦               | **Process / Action**        | Étape d’action (ex: `Set n = 5`) |
| 🟪               | **Input / Output (I/O)**    | Entrée ou sortie de données |
| 🔷               | **Decision / Condition**    | Condition ou question (ex: `n > 0 ?`) |
| 📄               | **Document**                | Représente un fichier ou rapport |
| 🧩               | **Subroutine / Fonction**   | Processus externe appelé ici |
| 💬               | **Commentaire / Note**      | Info supplémentaire liée à une étape |
| 🔘               | **Connecteur sur la page**  | Évite les flèches croisées, repère local |
| 🔻               | **Connecteur hors page**    | Renvoie à un autre diagramme |
| 🥫               | **Base de données / Fichier** | Données stockées |

> 🔁 Les flèches montrent le chemin d’exécution entre les symboles.

---

## 🧪 Exemple : `print_line(int n)`

### 🎯 Objectif de la fonction

- Affiche `_` `n` fois, puis un retour à la ligne (`\n`)
- Si `n <= 0` → affiche seulement `\n`

---

### 🧠 Pseudo-code

```c
nCopy = n;
while (nCopy > 0)
{
  print '_';
  nCopy--;
}
print '\n';
