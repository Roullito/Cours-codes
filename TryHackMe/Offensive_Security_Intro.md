# TryHackMe – Offensive Security Intro

> "To outsmart a hacker, you need to think like one."

Ce premier module TryHackMe t’introduit au monde de l’**offensive security** : apprendre à penser comme un attaquant pour mieux défendre les systèmes.

Ce cours récapitule et approfondit les notions vues dans la room *Offensive Security Intro* (FakeBank) en ajoutant des explications, du vocabulaire et une petite méthodo de pentest.

---

## Objectifs du cours

À la fin, tu dois être capable de :

- Expliquer ce qu’est l’**offensive security** et le rôle d’un **ethical hacker**.
- Comprendre pourquoi on utilise des **machines virtuelles** pour s’entraîner.
- Manipuler un **terminal** (ligne de commande) de base.
- Comprendre le principe de la **découverte de pages cachées** sur un site web.
- Utiliser un outil comme **`dirb`** pour brute‑forcer des répertoires et interpréter son output.
- Faire le lien entre **découverte** d’une page cachée et **exploitation** logique (FakeBank / page de dépôt).

---

## 1. Qu’est‑ce que l’Offensive Security ?

### 1.1 Défense vs attaque

En cybersécurité, on distingue deux grandes approches :

- **Defensive security (blue team)**
  Protéger, surveiller, détecter les attaques (firewalls, antivirus, monitoring, logs, etc.).

- **Offensive security (red team / pentest)**
  Attaquer un système *comme un hacker* pour trouver ses faiblesses avant les “vrais” attaquants.

L’idée centrale du module :

> Pour bien défendre, il faut comprendre comment on attaque.

### 1.2 Ethical hacking

Un **ethical hacker** (ou pentester) :

- A **l’autorisation** d’attaquer un système (contrat, scope défini).
- Simule les comportements d’un vrai pirate (scans, exploitation, escalade de privilèges…).
- **Rapporte** ses découvertes (rapport, recommandations de sécurité).
- N’a pas pour but de voler ou détruire, mais de **renforcer la sécurité**.

⚠️ **Règle d’or légale :**
Ne teste JAMAIS un site / réseau sans autorisation explicite.
TryHackMe fournit des environnements **faits pour être attaqués** → tu peux expérimenter sans risque légal.

---

## 2. Environnement de labo : les machines virtuelles TryHackMe

### 2.1 Qu’est‑ce qu’une machine virtuelle (VM) ?

Une **machine virtuelle** est un ordinateur “simulé” à l’intérieur d’un autre.
Elle a :

- Son propre système (souvent Linux),
- Ses propres programmes,
- Son propre réseau virtuel.

TryHackMe lance pour toi une VM déjà configurée :

- Avec les outils de hacking (comme `dirb`),
- Avec une appli vulnérable (FakeBank) à attaquer.

### 2.2 Avantages des labs

- Tu peux **tout casser** → on peut **revert** la machine à son état initial.
- Tu ne touches pas à ton OS principal → pas de risque de le mettre en vrac.
- Tu t’entraînes dans un environnement proche de la **réalité** (vrais services, vraie stack web).

---

## 3. Hacking web : structure d’un site & pages cachées

### 3.1 Rappel rapide : qu’est‑ce qu’une application web ?

Une appli web est un programme accessible via un navigateur.
Tu y accèdes via une **URL**, par exemple :

```text
http://fakebank.thm/bank-deposit
          └────┬────┘ └───┬────┘
           domaine       chemin (page / fonctionnalité)
```

Une appli typique contient :

- Une partie visible pour l’utilisateur : pages liées dans les menus, boutons, formulaires.
- Une partie **non visible** (mais parfois accessible !) : pages d’administration, tests, anciennes pages, API, etc.

### 3.2 Pourquoi il existe des pages cachées ?

Les développeurs laissent parfois :

- Des **URL secrètes** pour tester rapidement des fonctionnalités.
- Des anciennes pages non supprimées.
- Des outils internes pas censés être publics.

Si ces pages :

- ne demandent pas d’authentification correcte,
- ou permettent des actions sensibles (modifier un solde, changer un rôle, etc.),

… alors elles deviennent des **portes dérobées logiques** pour un attaquant.

C’est exactement ce que tu exploites dans FakeBank.

---

## 4. Première étape d’une attaque : la découverte (reconnaissance)

Avant de “pirater” une appli web, tu dois la **connaître** :

1. Quel est le domaine ? Sous‑domaines ?
2. Quelles pages sont visibles ?
3. Quelles pages sont cachées mais accessibles ?
4. Quels formulaires, paramètres, cookies, headers sont utilisés ?

Dans FakeBank, ton objectif initial est simple :

> Trouver des **URLs cachées** qui donnent accès à des fonctionnalités bancaires sensibles.

Pour ça, on utilise une technique appelée **directory/file brute forcing**.

---

## 5. Le terminal : ton interface avec les outils de hacking

### 5.1 Qu’est‑ce qu’un terminal ?

Le **terminal** (ou *command line*, *shell*) est un programme qui te permet de :

- Taper des **commandes en texte**,
- Lancer des outils (`dirb`, `nmap`, `ssh`, etc.),
- Lire les réponses du système directement dans la console.

Sur la VM TryHackMe :

- Tu cliques sur l’icône **Terminal**,
- Tu obtiens une fenêtre avec une ligne de type :

```text
ubuntu@tryhackme:~/Desktop$
```

C’est le **prompt** : il attend tes commandes.

### 5.2 Structure d’une commande

En général :

```bash
commande [options] [arguments]
```

Exemples :

- `ls` → affiche les fichiers du dossier courant.
- `cd /tmp` → change de dossier.
- `dirb http://fakebank.thm` → lance `dirb` sur l’URL donnée.

---

## 6. Brute‑forcer des pages cachées avec `dirb`

### 6.1 Principe de `dirb`

`dirb` est un outil de **brute force de répertoires/fichiers web** :

- Il prend une **liste de mots** (wordlist) comme `admin`, `login`, `images`, `backup`…
- Il teste pour chaque mot si `http://site.tld/mot` **existe**.
- Il regarde la **réponse HTTP** :

  - `200` → page trouvée (OK)
  - `301/302` → redirection (ex: vers un répertoire)
  - `403` → interdit (la ressource existe mais tu n’y as pas accès)
  - `404` → pas trouvé

C’est utile car **les humains sont prévisibles** : ils appellent souvent leurs pages `admin`, `backup`, `dev`, `old`, etc.

### 6.2 Lancer `dirb` sur FakeBank

Dans le terminal de la VM :

```bash
dirb http://fakebank.thm
```

- `dirb` → le programme
- `http://fakebank.thm` → l’URL de base à scanner

Le tool utilise par défaut une wordlist située ici :

```text
/usr/share/dirb/wordlists/common.txt
```

Tu peux ouvrir ce fichier pour voir les mots testés.

### 6.3 Lire la sortie de `dirb`

Exemple d’output (simplifié) :

```text
START_TIME: Thu Apr 17 16:29:52 2025
URL_BASE: http://fakebank.thm/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

---- Scanning URL: http://fakebank.thm/ ----
+ http://fakebank.thm/bank-deposit (CODE:200|SIZE:4663)
+ http://fakebank.thm/images      (CODE:301|SIZE:179)

END_TIME: Thu Apr 17 16:29:59 2025
DOWNLOADED: 4610 - FOUND: 2
```

Décryptage :

- `URL_BASE` → l’adresse scannée.
- `WORDLIST_FILES` → la liste de mots utilisée.
- `DOWNLOADED` → nombre total d’URLs testées.
- `FOUND` → nombre de résultats intéressants.

Les lignes qui commencent par `+` sont les **pages découvertes**.
Ici, tu vois :

- `/images` → certainement un répertoire d’images (peu intéressant).
- `/bank-deposit` → **très intéressant** pour une banque.

### 6.4 Que faire des URLs trouvées ?

Étape suivante : **les ouvrir dans le navigateur** de la VM, par exemple :

```text
http://fakebank.thm/bank-deposit
```

Tu observens alors :

- Quelles actions sont possibles ?
- Y a‑t‑il une authentification ?
- Peux‑tu interagir avec des paramètres (numéro de compte, montant, etc.) ?

C’est là qu’on passe de l’**enumeration** (découverte) à l’**exploitation**.

---

## 7. De la découverte à l’exploitation : l’exemple FakeBank

Dans FakeBank :

- Tu commences comme **utilisateur normal**, avec un compte bancaire (ex : numéro `8881`).
- En temps normal, tu devrais seulement voir ton solde et une interface classique.

Grâce à `dirb`, tu trouves une page comme :

```text
http://fakebank.thm/bank-deposit
```

Cette page permet :

- D’ajouter de l’argent sur **un compte donné**,
- En saisissant un **numéro de compte** + **montant**.

### 7.1 Le problème de sécurité

Si cette page :

- ne vérifie pas que tu es un **employé**,
- ne limite pas l’accès à certains rôles,
- ne protège pas l’action de dépôt,

… alors **tout utilisateur** qui connaît l’URL peut s’en servir.

C’est une **faille logique** : la fonctionnalité est correcte pour un employé, mais accessible à la mauvaise personne.

On peut assimiler ça à :

- Une erreur d’**autorisations** (contrôle d’accès),
- Un cas de **“forced browsing”** (accès à une URL non liée dans l’interface, mais toujours accessible).

### 7.2 Exploitation logique

Exemple de scénario :

1. Tu te connectes avec ton compte (8881).
2. Tu découvres `/bank-deposit` avec `dirb`.
3. Tu ouvres cette page dans le navigateur.
4. Tu entres :
   - Numéro de compte : `8881`
   - Montant : `2000`
5. Tu valides → ton solde est crédité illégalement.

Tu n’as pas “cassé” de mot de passe ni exploité un bug mémoire :
tu as exploité une **mauvaise conception de l’application** (contrôle d’accès manquant).

C’est typique des vulnérabilités web réelles :
beaucoup d’attaques sont basées sur la **logique métier** et l’oubli de règles de sécurité côté serveur.

---

## 8. Méthodologie offensive à retenir

Même sur un premier module simple, tu peux déjà te caler une **méthodo** :

1. **Reconnaissance**
   - Ouvrir le site dans le navigateur.
   - Regarder les pages visibles, formulaires, liens, messages d’erreur.
   - Identifier le domaine, les chemins intéressants.

2. **Enumeration**
   - Utiliser des outils comme `dirb` pour trouver des pages cachées.
   - Noter les URLs qui sortent de l’ordinaire (`/admin`, `/backup`, `/test`, `/bank-deposit`, etc.).

3. **Analyse**
   - Pour chaque page trouvée, te demander :
     - Qui est censé y avoir accès ?
     - Quelles actions ça permet ?
     - Quels paramètres sont manipulables (ID, montant, rôle…) ?

4. **Exploitation**
   - Tester des scénarios abusifs :
     - Crédits non autorisés (comme FakeBank),
     - Modification du compte d’un autre utilisateur,
     - Accès à des données confidentielles.

5. **Validation & nettoyage (en vrai pentest)**
   - Confirmer que la faille est exploitable,
   - Documenter proprement,
   - Éviter de laisser l’environnement dans un état critique.

Sur TryHackMe, tu peux casser, tester, rejouer → c’est fait pour ça.

---

## 9. Fiche mémo rapide

### Commandes utiles

```bash
# Scanner un site avec la wordlist par défaut
dirb http://fakebank.thm

# Scanner un site avec une wordlist perso
dirb http://fakebank.thm /chemin/vers/ma_wordlist.txt
```

### À vérifier quand tu trouves une URL

- L’URL est‑elle visible normalement dans les menus ?
- Faut‑il être connecté pour y accéder ?
- Le rôle de l’utilisateur est‑il vérifié ?
- Peux‑tu :
  - changer des paramètres sensibles (ID, montant, rôle…) ?
  - accéder aux données d’un autre utilisateur ?

---

## 10. Glossaire

- **Offensive security** : approche qui consiste à attaquer les systèmes pour trouver des failles avant les vrais attaquants.
- **Ethical hacker / pentester** : personne autorisée à tester la sécurité d’un système.
- **VM (Virtual Machine)** : ordinateur virtuel isolé dans lequel on peut faire des tests sans impacter la machine réelle.
- **Terminal / Shell** : interface en ligne de commande pour lancer des outils via du texte.
- **URL** : adresse d’une ressource sur le web (domaine + chemin).
- **Brute‑force de répertoires** : technique consistant à tester automatiquement une liste de chemins possibles sur un site.
- **Wordlist** : fichier contenant une liste de mots utilisés pour des attaques automatisées (mots de passe, chemins, etc.).
- **Forced browsing** : accès direct à des pages non liées dans le site, mais encore accessibles et parfois sensibles.
- **Contrôle d’accès** : mécanisme qui détermine qui a le droit de faire quoi (ex : seul un employé peut créditer un compte).
- **HTTP status code** : code de retour d’une requête web (200 OK, 404 Not Found, 403 Forbidden, 301 Moved Permanently, etc.).

---

Si tu veux, on peut maintenant enchaîner avec un **deuxième module** TryHackMe : tu m’envoies le contenu, et on te construit un autre cours dans le même style, pour te faire une vraie “mini‑formation cybersécurité” maison.
