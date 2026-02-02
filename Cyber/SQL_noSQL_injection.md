# SQL & NoSQL Injection — Cours ultra complet (avec exemples ludiques)

> **Contexte** : contenu pédagogique destiné à l’apprentissage **en environnement légal** (labs, CTF, machines cibles autorisées) — notamment *cyber_websec_0x01* sur **Kali Linux**.  
> **⚠️ Important** : n’utilise jamais ces techniques sur un système sans autorisation explicite.

---

## Table des matières

1. [SQL vs NoSQL — la base qui change tout](#1-sql-vs-nosql--la-base-qui-change-tout)
2. [C’est quoi une injection ? (modèle mental)](#2-cest-quoi-une-injection--modèle-mental)
3. [SQL Injection (SQLi) — définition, formes, risques](#3-sql-injection-sqli--définition-formes-risques)
4. [UNION attack — comprendre l’idée](#4-union-attack--comprendre-lidée)
5. [Blind SQL Injection — quand tu n’as pas l’erreur ni la data](#5-blind-sql-injection--quand-tu-nas-pas-lerreur-ni-la-data)
6. [Pourquoi LIMIT (et OFFSET) comptent en SQLi](#6-pourquoi-limit-et-offset-comptent-en-sqli)
7. [Prévention SQLi — ce qui marche vraiment](#7-prévention-sqli--ce-qui-marche-vraiment)
8. [Parameterized Queries — la défense #1](#8-parameterized-queries--la-défense-1)
9. [Stored Procedures — utiles, mais pas magiques](#9-stored-procedures--utiles-mais-pas-magiques)
10. [Input validation + Regex — important, mais pas seul](#10-input-validation--regex--important-mais-pas-seul)
11. [Escaping — à comprendre (et ses limites)](#11-escaping--à-comprendre-et-ses-limites)
12. [ORMs — quel rôle dans la prévention ?](#12-orms--quel-rôle-dans-la-prévention-)
13. [NoSQL Injection — spécificités et différences](#13-nosql-injection--spécificités-et-différences)
14. [NoSQLi dans MongoDB — comment ça arrive](#14-nosqli-dans-mongodb--comment-ça-arrive)
15. [Prévenir NoSQLi (MongoDB & co)](#15-prévenir-nosqli-mongodb--co)
16. [Méthodo de test en lab (sans sqlmap)](#16-méthodo-de-test-en-lab-sans-sqlmap)
17. [Checklists, pièges classiques, mini-quiz](#17-checklists-pièges-classiques-mini-quiz)
18. [Références (ressources officielles & utiles)](#18-références-ressources-officielles--utiles)

---

## 1) SQL vs NoSQL — la base qui change tout

### 1.1 SQL (relationnel) en 30 secondes
- **Données** : tables (lignes/colonnes), schéma défini (types, contraintes).
- **Relations** : clés étrangères + `JOIN`.
- **Langage** : SQL (standardisé).
- **Forces** : cohérence forte, transactions ACID, requêtes complexes, intégrité.
- **Exemples** : MySQL, PostgreSQL, MariaDB, SQL Server, Oracle.

### 1.2 NoSQL en 30 secondes
NoSQL = “Not only SQL” (pas *forcément* “pas SQL”), et ça regroupe plusieurs familles :
- **Document** : JSON/BSON (MongoDB)
- **Key-value** : Redis
- **Column-family** : Cassandra
- **Graph** : Neo4j

Caractéristiques fréquentes :
- **Schéma plus flexible** (mais pas “sans structure”)
- **Scaling horizontal** plus simple dans certains cas
- **Langage de requête** variable : API + JSON, ou dialectes

### 1.3 Pourquoi c’est important pour les injections ?
Parce que l’attaque dépend de **la grammaire** que tu peux casser :
- En SQLi, tu casses **la grammaire SQL** (guillemets, commentaires, opérateurs…).
- En NoSQLi, tu casses **la grammaire de la requête NoSQL** (objets JSON, opérateurs comme `$ne`, ou parfois du JavaScript côté base).

> **Image mentale** :  
> SQL = “phrase” standardisée (verbe + sujet + compléments).  
> NoSQL = “objet” (JSON) qu’on envoie à une API de requête.

---

## 2) C’est quoi une injection ? (modèle mental)

### 2.1 Définition simple
Une injection arrive quand une application mélange :
- **le code de la requête** (structure, logique),
- avec **des données utilisateur** (input),
… sans séparation stricte.

Résultat : l’attaquant peut faire en sorte que son input soit interprété comme **du code** (ou un opérateur) et pas seulement comme **une donnée**.

### 2.2 Analogie ludique : “commande au restaurant”
- Tu veux commander : *“Je veux une pizza au fromage.”*
- L’appli envoie au cuisinier : `COMMANDE(type="pizza", garniture="fromage")`
- Si tu peux injecter : `garniture="fromage"); AJOUTER("10 pizzas gratuites"); //"`

Tu as réussi à faire passer une **instruction** en plus de ta garniture.

### 2.3 La règle d’or
✅ **Séparer** structure de requête et données (paramètres).  
✅ **Valider** et **contraindre** les entrées (type, taille, format).  
✅ **Limiter** l’impact (moindres privilèges, logs, erreurs).

---

## 3) SQL Injection (SQLi) — définition, formes, risques

### 3.1 C’est quoi SQL Injection ?
SQLi = vulnérabilité qui permet d’interférer avec les requêtes SQL exécutées par l’application via une entrée contrôlable.

### 3.2 Où ça arrive ?
Dès que tu as :
- Une requête construite par concaténation de chaînes,
- avec un input utilisateur dedans.

Exemple “vulnérable” (pseudo-code) :
~~~text
query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'"
~~~

### 3.3 Principales familles de SQLi (niveau compréhension)
1) **In-band (classique)**  
   - *Error-based* : l’erreur SQL “parle trop”
   - *UNION-based* : tu merges un second `SELECT` au résultat

2) **Blind SQLi** (inférence)  
   - *Boolean-based* : la page change selon vrai/faux
   - *Time-based* : la réponse est plus lente selon vrai/faux

3) **Out-of-band** (rare)  
   - l’exfiltration passe par un canal annexe (DNS, HTTP, etc.)

> Objectif pédagogique : comprendre “comment la logique est modifiée”, pas apprendre à casser des sites au hasard.

### 3.4 Les risques (très concrets)
- **Bypass auth** (connexion sans mot de passe)
- **Lecture** de données sensibles (users, emails, hash, secrets)
- **Modification / suppression** (altération de données)
- **Escalade** (droits DB) + parfois impact serveur (si fonctions dangereuses activées)
- **Persistance** (backdoor via données stockées, triggers, etc.)
- **Conformité** : fuite RGPD, incident, pertes financières

---

## 4) UNION attack — comprendre l’idée

### 4.1 Le concept
Une `UNION` permet de **concaténer les résultats** de deux requêtes `SELECT` en un seul résultat.

Si une appli fait :
~~~sql
SELECT title, body FROM posts WHERE id = <user_input>
~~~
…et que ton input arrive à transformer la requête pour ajouter :
~~~sql
UNION SELECT email, password_hash FROM users
~~~
…tu peux “mélanger” des colonnes d’une autre table dans la réponse.

### 4.2 Conditions pour qu’une UNION marche
- Le nombre de colonnes doit **matcher**
- Les types doivent être **compatibles**
- Il faut que la requête originale soit un `SELECT` exploitable

> Au niveau compréhension : l’attaquant cherche d’abord “combien de colonnes sortent” et “quelles colonnes sont affichées”.

### 4.3 À retenir
- UNION = attaque “directe” quand tu vois le résultat.
- Si tu ne vois rien → tu bascules souvent en **blind**.

---

## 5) Blind SQL Injection — quand tu n’as pas l’erreur ni la data

### 5.1 Définition
Blind SQLi = tu ne vois pas le résultat SQL directement, donc tu **déduis** l’info via :
- un changement de contenu (boolean),
- ou un changement de temps (time-based).

### 5.2 Boolean-based (inférence par différence)
L’idée :
- Si condition vraie → la page contient “Bienvenue”
- Si condition fausse → “Identifiants invalides”

Tu transformes l’application en “oracle” oui/non.

### 5.3 Time-based (inférence par délai)
Quand la page ne change jamais visuellement, tu forces un comportement :
- Si condition vraie → requête “attend” (délai)
- Sinon → répond vite

> En lab : le point clé est de **mesurer** proprement une baseline, puis de vérifier que le délai est stable/reproductible.

### 5.4 Erreurs fréquentes en blind
- Confondre “latence réseau” avec “délai DB”
- Ne pas répéter le test (faux positifs)
- Ne pas isoler une variable à la fois

---

## 6) Pourquoi LIMIT (et OFFSET) comptent en SQLi

### 6.1 Rôle “attaque”
`LIMIT` et `OFFSET` permettent de :
- **Réduire** la quantité de résultats (discrétion, rapidité)
- **Parcourir** des résultats “ligne par ligne” (utile en inférence)

Exemples de logique (pas une recette d’attaque, juste le concept) :
- `LIMIT 1` : ne renvoie qu’un item
- `LIMIT 1 OFFSET n` : “saute” les n premiers items

### 6.2 Rôle “défense”
Dans l’application, `LIMIT` peut aussi :
- réduire l’impact d’une requête trop large,
- limiter certaines exfiltrations “grosses”.

⚠️ Mais : **LIMIT ne corrige jamais une SQLi**.  
Ça peut juste limiter les dégâts, pas supprimer la vulnérabilité.

---

## 7) Prévention SQLi — ce qui marche vraiment

Voici la hiérarchie “pratique” (du plus efficace au moins fiable) :

1) ✅ **Parameterized queries / prepared statements** (meilleur)
2) ✅ **ORM** (souvent safe *si* tu évites le raw SQL)
3) ✅ **Stored procedures** (safe si pas de SQL dynamique)
4) ⚠️ **Escaping** (fragile / dépend du contexte)
5) ⚠️ **Validation** seule (insuffisante)
6) ⚠️ **WAF** seul (contournable) — utile en défense en profondeur

---

## 8) Parameterized Queries — la défense #1

### 8.1 Définition
Une parameterized query sépare :
- la **structure** de la requête (avec des placeholders),
- des **valeurs** (paramètres) envoyées séparément.

Même si l’utilisateur met des caractères spéciaux, ils restent des **données**.

### 8.2 Exemple en Python (DB-API style)
Vulnérable :
~~~python
sql = "SELECT * FROM users WHERE email = '%s'" % email
cursor.execute(sql)
~~~

Safe (paramètres) :
~~~python
sql = "SELECT * FROM users WHERE email = %s"
cursor.execute(sql, (email,))
~~~

### 8.3 Exemple Node.js (mysql2 / pg)
Vulnérable :
~~~js
const sql = "SELECT * FROM users WHERE id = " + req.query.id;
db.query(sql);
~~~

Safe :
~~~js
const sql = "SELECT * FROM users WHERE id = ?";
db.query(sql, [req.query.id]);
~~~

### 8.4 Attention : “paramétriser” ≠ “concaténer proprement”
Un anti-pattern courant :
~~~js
// Mauvais : tu concatènes quand même.
const sql = "SELECT * FROM users WHERE id = " + escape(id);
~~~
Escaper n’est pas paramétriser.

---

## 9) Stored Procedures — utiles, mais pas magiques

### 9.1 C’est quoi ?
Une stored procedure = logique stockée côté DB, appelée par l’appli.

### 9.2 Pourquoi ça peut aider ?
- Tu centralises la logique
- Tu peux imposer des paramètres typés
- Tu peux limiter les droits de l’utilisateur DB à “EXECUTE procedure”

### 9.3 Pourquoi ça peut être vulnérable quand même ?
Si la procédure construit du SQL dynamique :
~~~sql
-- pseudo
SET @q = CONCAT('SELECT * FROM users WHERE name = "', user_input, '"');
PREPARE stmt FROM @q; EXECUTE stmt;
~~~
Là, tu recrées le problème (concaténation).

---

## 10) Input validation + Regex — important, mais pas seul

### 10.1 Pourquoi valider ?
Même si tu paramètres :
- tu veux éviter les données absurdes,
- réduire la surface d’attaque,
- éviter bugs / DoS (inputs énormes),
- forcer des types (int, email, UUID…).

### 10.2 Validation : “denylist” vs “allowlist”
- ❌ Denylist : “je bloque certains caractères” → contournable
- ✅ Allowlist : “j’accepte uniquement ce format”

### 10.3 Exemples de regex (allowlist)
**UUID v4 (simple)** :
~~~text
^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$
~~~

**Identifiant (lettres, chiffres, underscore, 3-20)** :
~~~text
^[a-zA-Z0-9_]{3,20}$
~~~

**Entier positif** :
~~~text
^[0-9]{1,10}$
~~~

### 10.4 Pièges de la validation par regex
- Une regex peut être trop permissive
- Une regex complexe peut causer du **ReDoS**
- Valider un champ ne suffit pas si tu reconstruis une requête dangereuse

> Règle : **validation = ceinture**, **paramétrisation = airbag**.

---

## 11) Escaping — à comprendre (et ses limites)

### 11.1 Définition
Escaper = transformer certains caractères pour qu’ils soient traités comme données.

### 11.2 Pourquoi c’est fragile ?
- Ça dépend du **dialecte SQL** et de la config
- Ça peut casser sur des encodages / collations
- Ça ne protège pas si tu injectes dans un contexte non-string (ORDER BY, LIMIT, etc.)

### 11.3 Quand c’est acceptable ?
- Rarement, et seulement si :
  - paramétrisation impossible (legacy),
  - escape fourni par la lib DB,
  - et contexte parfaitement maîtrisé.

---

## 12) ORMs — quel rôle dans la prévention ?

### 12.1 Pourquoi les ORMs aident
- Ils génèrent des requêtes paramétrées
- Ils gèrent les types
- Ils t’encouragent à éviter la concaténation

### 12.2 Mais : ORM ≠ immunité
Tu peux redevenir vulnérable si tu fais :
- raw SQL (`.raw()`, `text()`, etc.)
- concaténation dans des filtres
- interpolation de fragments SQL (ORDER BY dynamique, noms de colonnes)

### 12.3 Bonne pratique
- Whitelist stricte pour les parties structurelles (colonnes triables, champs filtrables)
- Paramètres uniquement pour les valeurs

---

## 13) NoSQL Injection — spécificités et différences

### 13.1 Définition
NoSQLi = vulnérabilité où un attaquant interfère avec les requêtes vers une base NoSQL.

### 13.2 Différence clé avec SQLi
En NoSQL, tu n’injectes pas forcément des guillemets + SQL.
Tu peux :
- injecter un **opérateur** (ex : `$ne`, `$gt`) dans un objet,
- casser une chaîne JavaScript (cas `$where`),
- exploiter des parseurs JSON / querystring.

### 13.3 Un problème fréquent : “l’objet de requête est construit avec l’input”
Exemple vulnérable (Node/Express, MongoDB) :
~~~js
const query = { username: req.body.username, password: req.body.password };
const user = await users.findOne(query);
~~~
Si `req.body` n’est pas strictement typé et validé, `username` peut devenir un objet avec opérateurs.

---

## 14) NoSQLi dans MongoDB — comment ça arrive

### 14.1 Vector 1 : Operator Injection (JSON / body)
Exemple “input attendu” :
~~~json
{ "username": "alice", "password": "Secret123!" }
~~~

Exemple “input malveillant” (idée de bypass) :
~~~json
{ "username": { "$ne": null }, "password": { "$ne": null } }
~~~

**Pourquoi ça marche (si c’est vulnérable) ?**
- `{ "$ne": null }` signifie “différent de null”
- donc la condition peut matcher trop large

### 14.2 Vector 2 : Injection via querystring (Express)
Selon le parseur, une URL peut produire des objets :
~~~text
?username[$ne]=null&password[$ne]=null
~~~

### 14.3 Vector 3 : `$where` (JavaScript injection)
Exemple vulnérable (anti-pattern) :
~~~js
const query = { $where: `this.username == '${username}'` };
~~~
Bonne pratique : éviter `$where` avec input.

### 14.4 MongoDB peut-il être “injecté” ?
Oui, **au niveau de l’application** : si tu laisses passer des opérateurs, tu obtiens une NoSQLi.

---

## 15) Prévenir NoSQLi (MongoDB & co)

### 15.1 Règle #1 : typer et valider strictement
- Refuser si ce n’est pas une string
- Limiter longueur (ex: 1..64)

### 15.2 Règle #2 : bloquer les clés dangereuses (`$` et `.`)
- Refuser toute clé commençant par `$`
- Refuser les clés contenant `.` si non nécessaires

### 15.3 Règle #3 : construction de requêtes côté serveur (allowlist)
Ne jamais faire “je prends l’objet reçu et je le passe à find()”.
Construire explicitement :
~~~js
const username = String(req.body.username);
const password = String(req.body.password);
const user = await users.findOne({ username, password });
~~~

### 15.4 Règle #4 : éviter `$where`
- Pas d’exécution JS côté DB
- Filtres standards + index

### 15.5 Défense en profondeur
- Moindres privilèges
- Logs, alerting, rate limiting
- Messages d’erreur neutres
- Tests de sécurité automatisés

---

## 16) Méthodo de test en lab (sans sqlmap)

### 16.1 Cartographie des entrées
Lister :
- paramètres URL (GET)
- corps (POST/JSON)
- cookies
- headers
- champs de formulaire

### 16.2 Signaux faibles
- Erreurs DB (“syntax error”, “unknown column”…)
- Différences de statut / taille de réponse
- Différences de temps récurrentes

### 16.3 Boucle de test minimaliste
1) Baseline
2) Input “anormal”
3) Observer : HTTP, longueur, contenu, latence
4) Répéter pour confirmer

### 16.4 Burp Suite (en lab)
- Proxy : intercepter
- Repeater : comparer
- Intruder : varier un paramètre et observer

### 16.5 Contrainte “scripts 1 ligne”
Si ton projet impose des scripts one-liner :
- privilégie des commandes reproductibles
- garde la preuve d’observation (diff, grep, timings), pas de l’auto-exploit.

---

## 17) Checklists, pièges classiques, mini-quiz

### 17.1 Checklist prévention SQLi (dev)
- [ ] Aucune concaténation dans SQL
- [ ] Paramètres partout
- [ ] Whitelist pour ORDER BY / noms de colonnes
- [ ] Validation de type
- [ ] Moindres privilèges DB
- [ ] Erreurs DB non exposées
- [ ] Logs + monitoring

### 17.2 Checklist prévention NoSQLi (MongoDB)
- [ ] Validation schema (JSON Schema/Joi/Zod…)
- [ ] Rejet des objets inattendus
- [ ] Blocage des clés `$` et `.`
- [ ] Pas de `$where` avec input
- [ ] Requêtes construites explicitement

### 17.3 “Pourquoi tous les fichiers finissent par une newline ?”
Parce que beaucoup d’outils Unix considèrent qu’une “ligne” doit se terminer par `\n`. Sans newline, tu peux avoir des comportements bizarres avec `diff`, parsers, concat, etc.

### 17.4 Mini-quiz
1) Définis SQL injection en une phrase.  
2) Comment NoSQLi diffère de SQLi ?  
3) Cite 3 risques.  
4) Décris une UNION attack.  
5) Explique blind boolean vs time.  
6) Rôle de LIMIT/OFFSET.  
7) Parameterized query : définition.  
8) Stored procedure : quand c’est safe ?  
9) Pourquoi valider même si tu paramètres ?  
10) Regex allowlist : exemple et but.  
11) MongoDB : comment NoSQLi peut bypass login (concept) ?  
12) ORM : protège toujours ? Pourquoi ?

### 17.5 Réponses (résumé)
1) Interférence avec requêtes SQL via input.  
2) Souvent injection d’opérateurs JSON / logique de requête.  
3) Bypass, exfiltration, modification, escalade.  
4) Fusionner deux SELECT (mêmes colonnes/types).  
5) Boolean = diff contenu; Time = diff latence.  
6) Explorer/limiter résultats; ne corrige pas la faille.  
7) Séparation structure/valeurs via placeholders.  
8) Safe si pas de SQL dynamique.  
9) Types, format, taille, robustesse.  
10) Ex: `^[0-9]{1,10}$` pour entier positif.  
11) Match trop large si objets/opérateurs acceptés.  
12) Non; raw SQL/interpolation structurelle peut casser.

---

## 18) Références (ressources officielles & utiles)

- OWASP — SQL Injection Prevention Cheat Sheet  
  https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- OWASP — Injection Prevention Cheat Sheet  
  https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html
- OWASP — SQL Injection (community)  
  https://owasp.org/www-community/attacks/SQL_Injection
- PortSwigger Web Security Academy — SQL injection  
  https://portswigger.net/web-security/sql-injection
- PortSwigger Web Security Academy — NoSQL injection  
  https://portswigger.net/web-security/nosql-injection
- MITRE CWE-89 — SQL Injection  
  https://cwe.mitre.org/data/definitions/89.html
- MITRE CWE-943 — Query Logic Neutralization (NoSQL-related)  
  https://cwe.mitre.org/data/definitions/943.html
- MITRE CAPEC-676 — NoSQL Injection  
  https://capec.mitre.org/data/definitions/676.html
- HackTricks — NoSQL injection  
  https://blog.1nf1n1ty.team/hacktricks/pentesting-web/nosql-injection
- MongoDB — SQL vs NoSQL  
  https://www.mongodb.com/resources/basics/databases/nosql-explained/nosql-vs-sql

---
