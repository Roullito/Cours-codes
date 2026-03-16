# Cours ultra complet — Insecure Direct Object Reference (IDOR)

## Contexte du module

Ce cours est conçu pour le module **Cyber – WebSec 0x06**. Son objectif est de te donner une compréhension **solide, complète, opérationnelle et pédagogique** d’**Insecure Direct Object Reference (IDOR)**, afin que tu puisses :

- reconnaître rapidement une IDOR dans une application web ou une API ;
- comprendre **pourquoi** elle existe ;
- la distinguer d’autres vulnérabilités ;
- la tester méthodiquement dans un lab ou un audit autorisé ;
- proposer des **mesures de remédiation robustes** côté architecture, backend, API et processus de développement.

---

# 1. Objectifs pédagogiques

À la fin de ce cours, tu dois pouvoir expliquer clairement :

- ce qu’est une **IDOR** ;
- ce que signifie exactement **Insecure Direct Object Reference** ;
- comment une IDOR fonctionne techniquement ;
- pourquoi une IDOR est avant tout un **problème d’autorisation** ;
- comment une attaque IDOR se produit ;
- quelles sont les **différentes formes** d’IDOR ;
- quel est son **impact technique et métier** ;
- comment **détecter** une IDOR ;
- comment **prévenir** une IDOR ;
- quelles sont les **bonnes pratiques de mitigation**.

---

# 2. Définition simple

## 2.1 Définition courte

Une **IDOR** est une vulnérabilité d’**accès non autorisé** qui apparaît lorsqu’une application utilise une **référence directe à un objet interne** (identifiant, nom de fichier, clé, UUID, slug, etc.) fournie ou contrôlée par l’utilisateur **sans vérifier correctement** si cet utilisateur a le droit d’accéder à cet objet.

En pratique :

- l’application reçoit un identifiant ;
- elle s’en sert pour charger une ressource ;
- elle oublie de vérifier si cette ressource appartient bien à l’utilisateur courant, ou s’il est autorisé à agir dessus.

Exemple très simple :

```http
GET /profile?id=123
```

Si l’utilisateur change `123` par `124` et voit le profil d’une autre personne, il y a une **IDOR**.

---

## 2.2 Que signifie “Insecure Direct Object Reference” ?

Décomposons le terme :

### Insecure
Le mécanisme est **non sécurisé**, car il fait confiance à une donnée contrôlée par le client sans contrôle d’autorisation suffisant.

### Direct
La référence pointe **directement** vers l’objet réel utilisé en backend.

Exemples de références directes :

- `user_id=42`
- `/invoice/2024-00091`
- `/download?file=salary.xlsx`
- `{ "orderId": 8871 }`
- `/api/v1/customers/ab12cd34-...`

### Object Reference
L’application manipule un **objet métier ou technique** :

- un utilisateur ;
- une commande ;
- une facture ;
- un fichier ;
- un message privé ;
- un ticket ;
- une image ;
- un document RH ;
- une ressource API.

Donc, **IDOR = référence directe à un objet interne, utilisable de manière non sécurisée**.

---

# 3. L’idée fondamentale : l’IDOR est un problème d’autorisation

C’est un point central.

Beaucoup de débutants pensent qu’une IDOR est un problème “d’ID dans l’URL”. En réalité, le vrai problème est ailleurs.

## 3.1 Le vrai bug n’est pas “l’identifiant visible”

Le vrai bug, c’est :

> **le serveur ne vérifie pas correctement si l’utilisateur a le droit d’accéder à l’objet demandé.**

Avoir un identifiant dans l’URL **n’est pas forcément vulnérable**.

Ceci peut être totalement sûr :

```http
GET /api/orders/91827
```

... **si** le serveur vérifie correctement :

- qui est l’utilisateur authentifié ;
- à quel tenant / compte / organisation il appartient ;
- s’il possède cette commande ;
- s’il a le droit de la lire, modifier ou supprimer.

## 3.2 Authentification ≠ Autorisation

Il faut distinguer deux notions :

### Authentification
Elle répond à la question : **qui es-tu ?**

Exemples :

- login + mot de passe ;
- session ;
- JWT ;
- cookie d’authentification ;
- OAuth.

### Autorisation
Elle répond à la question : **qu’as-tu le droit de faire ?**

Exemples :

- lire ta facture, mais pas celle d’un autre ;
- modifier ton profil, mais pas celui d’un autre utilisateur ;
- accéder au dashboard admin seulement si tu es admin.

Une application peut avoir une **authentification parfaite** et être **vulnérable à l’IDOR** si l’autorisation objet-par-objet est défaillante.

---

# 4. Comment fonctionne une IDOR ?

## 4.1 Mécanisme général

Une IDOR naît souvent selon cette séquence :

1. l’utilisateur est connecté ;
2. l’application lui montre ou utilise un identifiant d’objet ;
3. cet identifiant est transmis dans l’URL, le body, un champ caché, un en-tête, un cookie, un paramètre GraphQL, etc. ;
4. le serveur lit cet identifiant ;
5. le serveur charge l’objet demandé ;
6. **mais il ne vérifie pas correctement que cet objet est bien autorisé pour l’utilisateur courant**.

Résultat : l’attaquant peut remplacer la référence par une autre valeur valide.

---

## 4.2 Schéma mental

Imagine un hôtel.

- L’authentification = montrer que tu es bien un client de l’hôtel.
- L’autorisation = vérifier que tu peux entrer **dans ta chambre**, pas dans toutes les chambres.
- L’IDOR = le réceptionniste te laisse entrer dans n’importe quelle chambre juste parce que tu fournis un numéro de porte valide.

Tu es bien client de l’hôtel, donc **authentifié**.
Mais tu n’as pas le droit d’entrer dans la chambre 208 si la tienne est la 203.

---

# 5. Exemples concrets d’IDOR

## 5.1 Lecture d’un profil utilisateur

```http
GET /user?id=17
```

Si l’utilisateur remplace `17` par `18` et obtient le profil d’un autre compte, il y a une IDOR.

## 5.2 Téléchargement de facture

```http
GET /download/invoice/2025-00421
```

Si n’importe quel client peut télécharger la facture d’un autre simplement en changeant le numéro, c’est une IDOR.

## 5.3 Modification de profil via champ caché

```html
<input type="hidden" name="user_id" value="17">
```

Si l’application met à jour l’utilisateur indiqué par `user_id` sans vérifier qu’il s’agit bien du compte connecté, l’attaquant peut envoyer `user_id=18`.

## 5.4 API REST

```http
PATCH /api/orders/91827
```

Body :

```json
{
  "status": "cancelled"
}
```

Si un utilisateur peut annuler la commande d’un autre en changeant l’identifiant, c’est une IDOR.

## 5.5 Fichier interne

```http
GET /files?name=contract_2025_jean_dupont.pdf
```

Si le serveur sert ce fichier sans vérifier qu’il appartient bien à l’utilisateur, il y a une IDOR.

---

# 6. Où peut se cacher une référence directe ?

Les débutants regardent surtout les paramètres URL. C’est une erreur fréquente.

Une référence directe peut se trouver dans :

- le **path** : `/users/42`
- la **query string** : `?id=42`
- le **body POST/PUT/PATCH**
- un **JSON**
- un **champ hidden** dans un formulaire
- un **cookie**
- un **header HTTP**
- une **valeur GraphQL**
- un **nom de fichier**
- une **clé d’objet cloud storage**
- un **slug** (`/company/acme-inc/reports/q1`)
- un **UUID**
- un **hash pseudo-aléatoire**
- un **identifiant de message** dans un WebSocket
- un **identifiant interne** dans une application mobile.

Conclusion :

> une IDOR ne dépend pas de l’endroit où se trouve l’identifiant, mais du fait qu’un utilisateur puisse manipuler une référence vers un objet sans contrôle d’autorisation adéquat.

---

# 7. Pourquoi les IDOR arrivent-elles ?

## 7.1 Cause principale : l’objet est chargé sans filtrage par le contexte utilisateur

Exemple conceptuel vulnérable :

```python
order = Order.get(id=request.params["order_id"])
return order
```

Ici, le serveur charge une commande à partir d’un identifiant brut.

Version saine :

```python
order = current_user.orders.get(id=request.params["order_id"])
return order
```

La différence est énorme.

- version vulnérable : “charge l’objet demandé” ;
- version saine : “charge l’objet demandé **dans l’ensemble des objets autorisés pour cet utilisateur**”.

---

## 7.2 Causes fréquentes

### a) Confiance excessive dans le frontend
Le développeur suppose que l’utilisateur ne modifiera pas :

- l’URL ;
- les champs cachés ;
- le JSON ;
- les requêtes XHR/fetch.

C’est faux.

### b) Contrôle présent dans l’interface, absent côté serveur
Exemple :

- le bouton “Edit” n’apparaît que sur les objets de l’utilisateur ;
- mais l’endpoint `/api/edit-profile` n’effectue aucun contrôle serveur.

### c) Confusion entre authentification et autorisation
“L’utilisateur est connecté, donc c’est bon.”

Non. Être connecté ne suffit pas.

### d) Contrôle de rôle sans contrôle d’objet
Le système vérifie que l’utilisateur a le rôle `customer`, mais pas que **cette facture précise** lui appartient.

### e) Mauvaise modélisation multi-tenant
Dans un SaaS B2B, un utilisateur du tenant A peut accéder à des objets du tenant B si les requêtes ne sont pas systématiquement filtrées par tenant.

### f) Réutilisation dangereuse d’ID côté client
Le client envoie :

```json
{ "user_id": 44, "email": "new@example.com" }
```

Le backend se contente d’appliquer la mise à jour au `user_id` fourni.

### g) Absence de tests d’autorisation
Les tests vérifient la fonctionnalité, mais pas les accès croisés entre utilisateurs.

---

# 8. Différence entre IDOR et autres vulnérabilités

Comprendre les différences est essentiel.

## 8.1 IDOR vs Broken Access Control

**Broken Access Control** est la **catégorie large**.

**IDOR** est une **forme particulière** de broken access control :

- l’utilisateur manipule une référence d’objet ;
- le serveur n’applique pas correctement l’autorisation.

Donc :

- **tout IDOR est un broken access control** ;
- **tout broken access control n’est pas forcément une IDOR**.

---

## 8.2 IDOR vs BOLA

Dans le monde API, on parle souvent de **BOLA** :

- **Broken Object Level Authorization**.

Dans la pratique, BOLA est le cousin API d’IDOR, souvent considéré comme le même problème sous un vocabulaire plus moderne.

Nuance utile :

- **IDOR** est le terme historique le plus courant côté web ;
- **BOLA** insiste sur le fait qu’il s’agit d’un problème d’**autorisation au niveau de l’objet**, notamment dans les APIs.

---

## 8.3 IDOR vs BFLA

**BFLA** = Broken Function Level Authorization.

### IDOR / BOLA
Tu as accès à l’endpoint, mais **pas au bon objet**.

Exemple :

```http
GET /api/invoices/1001
GET /api/invoices/1002
```

L’endpoint est autorisé, mais l’objet 1002 ne devrait pas l’être.

### BFLA
Tu n’as pas le droit d’accéder à la **fonction elle-même**.

Exemple :

```http
POST /api/admin/delete-user
```

Un simple utilisateur peut appeler une fonction admin.

En résumé :

- IDOR/BOLA = problème sur **quel objet** ;
- BFLA = problème sur **quelle fonction**.

---

## 8.4 IDOR vs SQL Injection

### SQL Injection
L’attaquant injecte du SQL pour manipuler la requête en base.

### IDOR
L’attaquant envoie un identifiant valide et profite d’un manque de contrôle d’autorisation.

Une IDOR n’a pas besoin d’injection.

---

## 8.5 IDOR vs Path Traversal

Path Traversal consiste à manipuler un chemin de fichier (`../`) pour sortir d’un répertoire autorisé.

Une IDOR sur fichiers peut lui ressembler, mais ce n’est pas exactement la même logique :

- **Path Traversal** = résolution de chemin non sécurisée ;
- **IDOR fichier** = accès à un fichier non autorisé via une référence directe mal contrôlée.

Exemple IDOR fichier :

```http
GET /download?file=clientB/payroll.pdf
```

Exemple traversal :

```http
GET /download?file=../../etc/passwd
```

---

## 8.6 IDOR vs CSRF

### CSRF
Force un utilisateur authentifié à envoyer une requête à son insu.

### IDOR
Permet à un utilisateur de manipuler une référence pour viser un objet non autorisé.

Les deux peuvent se combiner, mais ce n’est pas la même vulnérabilité.

---

# 9. Types d’IDOR

Il existe plusieurs façons utiles de classer les IDOR.

## 9.1 IDOR de lecture
L’attaquant **lit** des données qu’il ne devrait pas voir.

Exemples :

- profil d’un autre utilisateur ;
- facture d’un autre client ;
- document RH ;
- message privé ;
- historique de commandes ;
- données médicales.

Impact principal : **confidentialité**.

---

## 9.2 IDOR d’écriture / modification
L’attaquant **modifie** une ressource qui ne lui appartient pas.

Exemples :

- changer l’adresse e-mail d’un autre compte ;
- modifier l’état d’une commande ;
- éditer un ticket ;
- remplacer un document.

Impact principal : **intégrité**.

---

## 9.3 IDOR de suppression
L’attaquant **supprime** un objet non autorisé.

Exemples :

- suppression d’un fichier ;
- suppression d’un message ;
- suppression d’une commande ;
- suppression d’un utilisateur.

Impact principal : **intégrité** et parfois **disponibilité**.

---

## 9.4 IDOR horizontale
L’attaquant reste au **même niveau de privilège**, mais accède aux objets d’un autre utilisateur.

Exemple :

- Alice lit la facture de Bob.

C’est la forme la plus classique.

---

## 9.5 IDOR verticale
L’attaquant accède à des objets ou fonctions normalement réservés à un niveau de privilège supérieur.

Exemple :

- un utilisateur standard modifie un objet réservé aux managers ou admins.

---

## 9.6 IDOR sur fichiers
La référence directe pointe vers un fichier :

- PDF ;
- image ;
- export CSV ;
- contrat ;
- sauvegarde ;
- rapport interne.

---

## 9.7 IDOR sur API
Très fréquente dans les architectures modernes :

- SPA + backend API ;
- mobile app + API ;
- microservices ;
- GraphQL ;
- SaaS multi-tenant.

---

## 9.8 Blind IDOR
L’application ne renvoie pas forcément les données directement, mais l’attaquant peut quand même provoquer une action visible indirectement.

Exemples :

- changer l’adresse e-mail d’un autre utilisateur ;
- déclencher un reset ou un workflow ;
- modifier un statut ;
- générer un effet observable ailleurs.

C’est plus subtil, donc souvent plus difficile à repérer.

---

## 9.9 IDOR prévisible vs IDOR non prévisible
### Prévisible
Les identifiants sont faciles à deviner :

- `1`, `2`, `3`, `4`...
- `invoice-1001`, `invoice-1002`...

### Non prévisible
Les identifiants sont moins devinables :

- UUID ;
- chaînes pseudo-aléatoires ;
- identifiants opaques.

Important :

> un identifiant complexe réduit l’énumération, mais **ne remplace jamais** un contrôle d’autorisation.

---

# 10. Comment une attaque IDOR se déroule

## 10.1 Scénario typique

1. l’attaquant crée un compte ou utilise un compte légitime ;
2. il navigue dans l’application ;
3. il repère un identifiant d’objet ;
4. il intercepte la requête ;
5. il remplace l’identifiant ;
6. il observe la réponse ;
7. il confirme un accès non autorisé.

---

## 10.2 Étapes mentales de l’attaquant

L’attaquant se demande :

- où sont les identifiants ?
- quelles ressources ont de la valeur ?
- les IDs sont-ils séquentiels ?
- les objets sont-ils filtrés par utilisateur ou tenant ?
- quelles actions sont possibles : lire, modifier, supprimer, approuver, exporter ?
- le contrôle dépend-il seulement du frontend ?
- les UUID sont-ils réellement protégés par autorisation ?

---

## 10.3 Pourquoi les attaquants aiment les IDOR

Parce qu’elles sont souvent :

- **simples à tester** ;
- **très impactantes** ;
- **discrètes** ;
- **faciles à automatiser** si les IDs sont prévisibles ;
- **présentes dans les APIs modernes**.

---

# 11. Impact d’une IDOR

L’impact dépend du type de données et de l’action possible.

## 11.1 Confidentialité
Exposition de :

- données personnelles ;
- informations financières ;
- documents internes ;
- PII ;
- données médicales ;
- secrets métier ;
- données clients B2B.

## 11.2 Intégrité
Modification non autorisée :

- profils ;
- statuts ;
- montants ;
- commandes ;
- droits ;
- documents ;
- configurations.

## 11.3 Disponibilité
Suppression ou altération de ressources critiques.

## 11.4 Impact business
- fuite de données ;
- perte de confiance ;
- incidents RGPD ;
- sanctions réglementaires ;
- fraude ;
- coût d’investigation ;
- atteinte à la réputation ;
- parfois **prise de contrôle de compte** via modification d’e-mail ou de paramètres sensibles.

---

# 12. Cas typiques où trouver des IDOR

## 12.1 E-commerce
- commandes ;
- paniers ;
- factures ;
- adresses ;
- retours ;
- remboursements.

## 12.2 Réseaux sociaux / messagerie
- profils ;
- messages privés ;
- pièces jointes ;
- brouillons ;
- médias.

## 12.3 RH / entreprise
- fiches salariés ;
- bulletins ;
- contrats ;
- notes de frais ;
- tickets IT.

## 12.4 SaaS multi-tenant
- organisations ;
- dashboards ;
- exports ;
- rapports ;
- clients ;
- projets.

## 12.5 Santé
- dossiers patients ;
- résultats ;
- ordonnances ;
- comptes-rendus.

## 12.6 Éducation
- notes ;
- copies ;
- profils étudiants ;
- paiements.

## 12.7 Applications mobiles
- endpoints JSON ;
- téléchargements ;
- synchronisation ;
- médias ;
- tickets ;
- historiques.

---

# 13. Signes qui doivent te faire penser “IDOR”

Quand tu testes une app, certains indices doivent déclencher un réflexe.

## 13.1 IDs dans les requêtes
Exemples :

- `id=17`
- `/users/17`
- `/api/orders/887`
- `document_id`
- `account_id`
- `tenant_id`
- `invoice_uuid`

## 13.2 IDs séquentiels
Si tu vois :

- `1001`
- `1002`
- `1003`

... pense immédiatement à une tentative de permutation dans un environnement autorisé.

## 13.3 Références en champ caché
Tout `hidden input` contenant un identifiant métier mérite de la suspicion.

## 13.4 Références dans le JSON
Exemple :

```json
{
  "userId": 42,
  "projectId": 991,
  "ticketId": 75
}
```

## 13.5 Applications multi-comptes
Si tu peux créer deux comptes, compare leurs objets et leurs identifiants.

## 13.6 Endpoints “My account” qui prennent un id
Exemple :

```http
GET /my-account?id=wiener
```

C’est un énorme signal d’alerte.

## 13.7 Export, download, preview, invoice, report, attachment
Les fonctionnalités de fichiers sont historiquement riches en IDOR.

---

# 14. Méthodologie de détection d’une IDOR

Ici on se place dans un **lab**, un audit autorisé ou un environnement de formation.

## 14.1 Étape 1 — Cartographier les objets
Liste les objets manipulés par l’application :

- users
- orders
- invoices
- files
- messages
- projects
- organizations
- tickets
- reviews
- payments

Pose-toi la question :

> quels objets ont un propriétaire, un tenant, un rôle d’accès ou une contrainte métier ?

---

## 14.2 Étape 2 — Relever toutes les références utilisateur-contrôlées
Cherche dans :

- URLs ;
- query params ;
- JSON ;
- formulaires ;
- champs cachés ;
- cookies ;
- headers ;
- WebSocket messages ;
- appels API ;
- GraphQL variables.

---

## 14.3 Étape 3 — Utiliser au moins deux comptes
C’est une règle d’or.

Un seul compte ne suffit souvent pas.

Il faut idéalement :

- **compte A**
- **compte B**
- parfois **compte admin** ou rôle différent

Objectif : comparer les objets possédés par chacun.

---

## 14.4 Étape 4 — Rejouer les requêtes en remplaçant les identifiants
Tu prends une requête légitime de A.
Puis tu remplaces l’identifiant par celui d’un objet appartenant à B.

Tu observes :

- `200 OK` avec données d’autrui ;
- `403 Forbidden` ;
- `404 Not Found` ;
- réponse vide ;
- comportement latéral ;
- effet différé.

---

## 14.5 Étape 5 — Tester toutes les actions, pas seulement GET
Beaucoup d’étudiants ne testent que la lecture.

Il faut tester :

- **GET** : lire
- **POST** : créer ou déclencher
- **PUT / PATCH** : modifier
- **DELETE** : supprimer
- actions métier : approve, refund, cancel, export, invite, attach, transfer

Une app peut protéger la lecture mais pas la modification.

---

## 14.6 Étape 6 — Tester les objets imbriqués
Exemple :

```http
GET /organizations/12/projects/998/reports/17
```

Il faut vérifier chaque niveau :

- organisation ;
- projet ;
- rapport.

Une seule vérification “organisation visible dans l’UI” n’est pas suffisante.

---

## 14.7 Étape 7 — Tester la version API et la version frontend
Souvent :

- le frontend masque l’accès ;
- mais l’API répond quand même.

Donc il faut toujours regarder les appels XHR/fetch.

---

## 14.8 Étape 8 — Tester les fichiers et médias
Regarde :

- download ;
- preview ;
- export ;
- attachment ;
- avatar ;
- PDF ;
- CSV ;
- ZIP ;
- image viewer.

---

## 14.9 Étape 9 — Tester les UUID aussi
Ne te laisse pas tromper.

Un UUID **n’empêche pas** une IDOR.
Il réduit seulement la facilité de deviner les objets.

Si tu récupères un UUID d’autrui via :

- historique ;
- partage ;
- logs ;
- e-mail ;
- page publique ;
- autre endpoint ;

... l’IDOR existe toujours si le contrôle serveur est absent.

---

## 14.10 Étape 10 — Chercher les “blind IDOR”
Parfois la réponse ne montre rien directement.

Mais une modification peut être confirmée via :

- un second compte ;
- une notification ;
- un changement d’état ;
- un effet visible dans l’interface ;
- un workflow déclenché.

---

# 15. Outils utiles pour tester des IDOR

## 15.1 Navigateur + DevTools
Indispensable pour :

- observer les requêtes XHR/fetch ;
- inspecter formulaires et champs cachés ;
- comprendre le flux métier.

## 15.2 Burp Suite
Très utile pour :

- Proxy ;
- HTTP history ;
- Repeater ;
- Intruder ;
- comparaison de requêtes.

## 15.3 Deux comptes de test
Outil le plus sous-estimé.

## 15.4 Scripts maison dans un lab
Pour rejouer des requêtes autorisées à différentes valeurs d’identifiants.

## 15.5 Extensions / aides d’autorisation
Des outils comme **Autorize** peuvent aider à comparer automatiquement le comportement de requêtes autorisées vs non autorisées, mais ils ne remplacent pas l’analyse manuelle.

---

# 16. Comment raisonner pendant le test

Quand tu vois une requête, pose-toi toujours ces questions :

1. **Quel objet est visé ?**
2. **Qui est censé avoir accès à cet objet ?**
3. **L’objet appartient-il à l’utilisateur courant ?**
4. **Le rôle suffit-il, ou faut-il aussi vérifier la relation métier ?**
5. **Que se passe-t-il si je remplace l’ID ?**
6. **Que se passe-t-il avec une autre méthode HTTP ?**
7. **Le contrôle existe-t-il sur tous les endpoints liés ?**
8. **Le système protège-t-il l’UI mais oublie-t-il l’API ?**
9. **Le contrôle est-il par objet, par tenant, par organisation, par statut ?**
10. **Puis-je provoquer une action sans voir directement la donnée ?**

---

# 17. Exemples d’erreurs de conception qui mènent aux IDOR

## 17.1 Lookup direct sur clé primaire globale
Mauvais pattern :

```python
invoice = Invoice.get(id=invoice_id)
```

Bon pattern :

```python
invoice = current_user.invoices.get(id=invoice_id)
```

Ou, en multi-tenant :

```python
invoice = Invoice.get(id=invoice_id, tenant_id=current_user.tenant_id)
```

---

## 17.2 “Le bouton n’apparaît pas”
Erreur classique :

- le frontend cache le bouton “Delete” ;
- mais l’endpoint `/api/delete-file?id=...` reste accessible.

L’absence de bouton n’est **jamais** un contrôle de sécurité.

---

## 17.3 Confiance dans un champ hidden

```html
<input type="hidden" name="owner_id" value="17">
```

Tout ce qui vient du client peut être modifié.

---

## 17.4 Vérification partielle du rôle
Exemple :

- tous les `customer` peuvent accéder à `/api/orders/:id` ;
- mais on oublie de vérifier que l’order appartient bien à **ce** customer.

---

## 17.5 Autorisation au mauvais endroit
Le contrôleur vérifie le rôle, puis le service lit l’objet globalement.

Ou pire :

- un endpoint appelle plusieurs services ;
- un sous-service ne réapplique pas le filtre d’autorisation.

---

## 17.6 Références indirectes mal conçues
Le développeur remplace l’ID numérique par une valeur “opaque”, mais l’objet reste accessible sans contrôle d’autorisation.

Ce n’est pas une vraie correction.

---

# 18. Pourquoi les UUID ne suffisent pas

C’est une idée très répandue.

> “On a mis des UUID, donc plus d’IDOR.”

Faux.

## 18.1 Ce que les UUID améliorent
- ils rendent l’énumération massive plus difficile ;
- ils empêchent souvent les séquences triviales ;
- ils ajoutent de l’opacité.

## 18.2 Ce qu’ils ne corrigent pas
- l’absence de contrôle d’autorisation ;
- la fuite d’un UUID via une autre page ou API ;
- les accès croisés si l’UUID est connu ;
- les liens partagés ;
- les historiques ;
- les journaux ;
- les notifications ;
- les listes publiques ;
- les endpoints de recherche.

## 18.3 Bonne conclusion
Les UUID sont une **mesure de défense en profondeur**, pas une solution principale.

---

# 19. Prévention : principes essentiels

Voici la partie la plus importante côté défense.

## 19.1 Toujours vérifier l’autorisation côté serveur
Jamais dans le frontend uniquement.

Le client ne doit jamais décider seul quel objet il peut lire, modifier ou supprimer.

---

## 19.2 Vérifier l’autorisation pour chaque objet, sur chaque requête
Le contrôle doit être :

- systématique ;
- centralisé autant que possible ;
- cohérent pour tous les verbes HTTP ;
- réutilisable ;
- testé.

---

## 19.3 Filtrer les objets à partir du contexte utilisateur
Très bonne stratégie :

- ne pas charger “n’importe quel objet puis vérifier après” ;
- charger directement l’objet dans le **scope autorisé**.

Exemple logique :

- pas `Project.find(project_id)`
- mais `current_user.projects.find(project_id)`

Cela réduit fortement le risque d’oubli.

---

## 19.4 Appliquer le principe du moindre privilège
Un utilisateur ne doit avoir accès qu’au strict nécessaire.

Ce principe vaut :

- horizontalement ;
- verticalement ;
- par tenant ;
- par relation métier ;
- par environnement.

---

## 19.5 Deny by default
Si la règle n’est pas explicitement autorisée, l’accès doit être refusé.

---

## 19.6 Ne pas faire confiance aux références client
Tout identifiant venant du client doit être considéré comme :

- potentiellement falsifié ;
- potentiellement rejoué ;
- potentiellement observé chez un autre utilisateur.

---

## 19.7 Utiliser des références indirectes ou opaques si pertinent
C’est utile en défense en profondeur, mais **jamais suffisant seul**.

---

## 19.8 Éviter d’exposer inutilement les IDs sensibles
Quand c’est possible :

- déduire l’utilisateur depuis la session ;
- utiliser le contexte serveur ;
- stocker les étapes intermédiaires côté session pour les workflows multi-étapes.

---

## 19.9 Journaliser les accès refusés et les comportements suspects
Les tentatives répétées de changement d’identifiants peuvent révéler :

- un test offensif ;
- un compte compromis ;
- un utilisateur malveillant ;
- un bug d’intégration.

---

# 20. Mitigation best practices

## 20.1 Contrôles d’autorisation centralisés
Évite de disperser des `if user.id == ...` partout.

Préférer :

- policy layer ;
- decorators / middleware ;
- guards ;
- services d’autorisation ;
- moteurs de policy ;
- helpers réutilisables.

---

## 20.2 Contrôle objet + rôle + relation + tenant
Une bonne autorisation ne se résume pas à “est-il connecté ?”.

Elle doit souvent tenir compte de :

- l’identité ;
- le rôle ;
- la propriété ;
- le tenant ;
- l’état métier ;
- la relation entre acteurs et objets.

Exemple :

- un manager peut voir les tickets de son équipe, mais pas ceux d’une autre équipe ;
- un comptable peut lire une facture sans pouvoir l’annuler ;
- un client peut lire sa commande seulement si elle appartient à son compte.

---

## 20.3 Contrôle sur tous les verbes et toutes les variantes d’endpoint
Il faut protéger :

- `GET /orders/1`
- `PATCH /orders/1`
- `DELETE /orders/1`
- `POST /orders/1/cancel`
- `GET /orders/1/export`
- `GET /api/v2/orders/1`

Souvent, une seule variante est oubliée.

---

## 20.4 Contrôle sur les ressources statiques et fichiers
Ne sécurise pas seulement les pages HTML.

Protège aussi :

- exports ;
- images ;
- PDF ;
- pièces jointes ;
- téléchargements ;
- documents générés.

---

## 20.5 Créer des tests d’autorisation automatiques
Tu dois avoir des tests qui vérifient :

- utilisateur A peut accéder à son objet ;
- utilisateur A ne peut pas accéder à l’objet de B ;
- utilisateur A ne peut pas modifier l’objet de B ;
- rôle standard ne peut pas accéder aux objets admin ;
- tenant A n’accède jamais aux objets tenant B.

---

## 20.6 Vérifier les contrôles à la bonne couche
Le contrôle doit être effectué **avant** l’action sensible, et dans une couche fiable.

Il ne doit pas dépendre uniquement :

- du frontend ;
- d’un flag JavaScript ;
- d’une condition d’affichage ;
- d’une convention implicite.

---

## 20.7 Gérer proprement les refus d’accès
Quand l’accès est refusé :

- ne pas exécuter l’action quand même ;
- ne pas laisser l’objet partiellement modifié ;
- retourner un comportement cohérent ;
- ne pas divulguer trop d’informations dans les erreurs.

---

## 20.8 Revue des frameworks et bibliothèques
Ne suppose jamais qu’un framework te protège “par magie”.

Tu dois savoir :

- où se fait l’autorisation ;
- comment elle se propage ;
- quelles routes l’utilisent ;
- quels cas elle ne couvre pas.

---

# 21. Modèles d’autorisation utiles contre l’IDOR

## 21.1 RBAC — Role-Based Access Control
Accès basé sur le rôle.

Exemple :

- `admin`
- `manager`
- `user`

Limite : parfois trop grossier pour les règles objet-par-objet.

---

## 21.2 ABAC — Attribute-Based Access Control
Décision basée sur des attributs :

- rôle ;
- département ;
- pays ;
- propriétaire ;
- état ;
- type d’objet ;
- heure ;
- contexte.

Très utile pour les règles fines.

---

## 21.3 ReBAC — Relationship-Based Access Control
L’accès dépend de la relation entre sujet et objet.

Exemples :

- “cet utilisateur est membre de ce projet” ;
- “ce manager supervise ce salarié” ;
- “cet utilisateur appartient à ce tenant” ;
- “ce reviewer est assigné à ce dossier”.

C’est souvent extrêmement pertinent contre les IDOR complexes.

---

# 22. IDOR dans les APIs modernes

Les APIs sont particulièrement exposées.

## 22.1 Pourquoi ?
Parce qu’elles manipulent directement :

- des IDs d’objets ;
- des routes REST ;
- des payloads JSON ;
- des clients variés (web, mobile, partenaires).

Le serveur garde souvent moins d’état de navigation, donc il se repose davantage sur les paramètres envoyés par le client.

---

## 22.2 Endroits typiques en API REST
- `/api/users/42`
- `/api/orders/91827`
- `/api/files/8a2f.../download`
- `{"ticketId": 17}`
- `tenant_id` dans le body

---

## 22.3 GraphQL
Dans GraphQL, les références d’objets apparaissent souvent dans :

- arguments de queries ;
- variables ;
- mutations ;
- edges/nodes ;
- IDs relay/global IDs.

Exemple conceptuel :

```graphql
query {
  invoice(id: "inv_123") {
    total
    customerEmail
  }
}
```

Si l’ID est modifiable sans contrôle d’autorisation adapté, le risque existe aussi.

---

## 22.4 Mobile
Les applications mobiles sont souvent des clients API :

- l’ID peut être dans le JSON ;
- les objets peuvent être énumérés ;
- le backend doit appliquer les mêmes protections que pour le web.

---

# 23. Exemples pédagogiques de code vulnérable vs sain

## 23.1 Exemple Flask conceptuel
### Vulnérable

```python
@app.get("/orders/<int:order_id>")
def get_order(order_id):
    order = Order.query.get(order_id)
    return jsonify(order.to_dict())
```

Problème :

- on charge l’objet globalement ;
- aucun contrôle n’assure que l’utilisateur connecté est propriétaire de cette commande.

### Plus sain

```python
@app.get("/orders/<int:order_id>")
def get_order(order_id):
    order = Order.query.filter_by(id=order_id, user_id=current_user.id).first_or_404()
    return jsonify(order.to_dict())
```

Ici, la requête est **scopée** à l’utilisateur connecté.

---

## 23.2 Exemple Django conceptuel
### Vulnérable

```python
order = Order.objects.get(pk=order_id)
```

### Plus sain

```python
order = get_object_or_404(Order, pk=order_id, owner=request.user)
```

---

## 23.3 Exemple Node/Express conceptuel
### Vulnérable

```javascript
const order = await Order.findById(req.params.id);
res.json(order);
```

### Plus sain

```javascript
const order = await Order.findOne({ _id: req.params.id, owner: req.user.id });
if (!order) return res.sendStatus(404);
res.json(order);
```

---

# 24. Erreurs de correction fréquentes

## 24.1 Remplacer les IDs numériques par des UUID et s’arrêter là
C’est insuffisant.

## 24.2 Cacher les boutons dans l’UI
Insuffisant.

## 24.3 Vérifier seulement côté frontend
Insuffisant.

## 24.4 Vérifier uniquement le rôle
Insuffisant si la relation à l’objet n’est pas validée.

## 24.5 Corriger une route mais oublier les variantes
Exemple :

- correction sur `/api/orders/:id`
- oubli sur `/api/orders/:id/export`
- oubli sur `/api/v2/orders/:id`

## 24.6 Corriger la lecture mais pas l’écriture
Très fréquent.

## 24.7 Protéger le web mais pas l’API mobile
Très fréquent aussi.

---

# 25. Réponses HTTP et interprétation

## 25.1 200 OK
Très mauvais signe si l’objet appartient à un autre utilisateur.

## 25.2 403 Forbidden
En général bon signe : le serveur refuse explicitement.

## 25.3 404 Not Found
Peut être volontaire pour ne pas révéler l’existence de la ressource.

Ce n’est pas forcément une preuve de sécurité parfaite, mais souvent un choix défensif acceptable.

## 25.4 302 Redirect ou comportement ambigu
Il faut vérifier si l’action a tout de même été réalisée.

## 25.5 Réponse vide mais side effect présent
Possible blind IDOR.

---

# 26. Cas particuliers importants

## 26.1 Multi-tenant
Très critique.

Il faut vérifier qu’un utilisateur de l’organisation A ne puisse jamais accéder aux objets de B, même s’il connaît leurs IDs.

---

## 26.2 Endpoints bulk / batch
Exemple :

```json
{
  "orderIds": [101, 102, 103]
}
```

Le serveur doit vérifier **chaque objet** de la liste.

---

## 26.3 Recherche et filtrage
Une recherche peut divulguer des objets d’autres utilisateurs si les filtres d’autorisation sont absents.

---

## 26.4 Imports / exports
Les exports CSV/PDF/ZIP sont souvent des cibles idéales.

---

## 26.5 Webhooks / liens temporaires / pré-signés
Même avec des mécanismes spéciaux, il faut vérifier :

- la portée ;
- la durée ;
- le propriétaire ;
- le contexte ;
- la révocation éventuelle.

---

# 27. Comment rapporter proprement une IDOR en audit ou bug bounty

Un bon rapport doit montrer :

1. **la ressource visée** ;
2. **le contexte d’autorisation attendu** ;
3. **la requête légitime** ;
4. **la modification minimale de l’identifiant** ;
5. **la preuve d’accès ou de modification non autorisée** ;
6. **l’impact** ;
7. **la recommandation de remédiation**.

Exemple de structure :

- Titre : IDOR permettant l’accès aux factures d’autres clients
- Endpoint : `GET /api/invoices/{id}`
- Rôle affecté : utilisateur authentifié standard
- Impact : fuite de données financières et personnelles
- Cause : absence de contrôle d’autorisation objet-par-objet
- Remédiation : filtrer par propriétaire / tenant et centraliser les policies.

---

# 28. Ce qu’un développeur doit retenir absolument

## 28.1 La règle d’or

> Ne jamais récupérer un objet à partir d’un identifiant client sans vérifier que l’utilisateur courant est autorisé sur cet objet précis.

## 28.2 Formule mentale simple
Toujours penser :

- **Qui est l’utilisateur ?**
- **Quel objet demande-t-il ?**
- **Pourquoi aurait-il le droit sur cet objet ?**

Si la réponse n’est pas claire dans le code, il y a un risque.

---

# 29. Ce qu’un pentester / étudiant cyber doit retenir absolument

## 29.1 Réflexe n°1
Dès qu’un objet a un identifiant, pense **IDOR/BOLA**.

## 29.2 Réflexe n°2
Teste avec **deux comptes**.

## 29.3 Réflexe n°3
Teste toutes les méthodes : lecture, modification, suppression, export, actions métier.

## 29.4 Réflexe n°4
Teste l’API, pas seulement l’interface.

## 29.5 Réflexe n°5
UUID ≠ sécurité.

---

# 30. Checklist pratique de test IDOR

## 30.1 Reconnaissance
- [ ] Identifier les objets métier sensibles
- [ ] Repérer tous les IDs visibles
- [ ] Repérer les flux API/XHR
- [ ] Créer au moins deux comptes

## 30.2 Tests de lecture
- [ ] Changer un ID dans le path
- [ ] Changer un ID en query param
- [ ] Changer un ID dans le body JSON
- [ ] Changer un hidden field
- [ ] Tester les téléchargements / exports

## 30.3 Tests d’écriture
- [ ] Modifier un objet d’un autre utilisateur
- [ ] Annuler / approuver / clôturer un objet d’autrui
- [ ] Changer des paramètres sensibles
- [ ] Tester PUT/PATCH/POST/DELETE

## 30.4 Tests avancés
- [ ] Tester les UUID
- [ ] Tester les ressources imbriquées
- [ ] Tester les listes bulk
- [ ] Tester les rôles différents
- [ ] Tester les tenants différents
- [ ] Chercher des blind IDOR

## 30.5 Validation défensive
- [ ] Les refus d’accès sont cohérents
- [ ] Les objets sont filtrés par owner/tenant
- [ ] Les policies sont centralisées
- [ ] Des tests automatiques existent
- [ ] Les logs d’accès refusés existent

---

# 31. Pièges pédagogiques fréquents chez les étudiants

## 31.1 “Je suis connecté donc ce n’est pas grave”
Si, justement. Beaucoup d’IDOR nécessitent un compte authentifié.

## 31.2 “L’ID est un UUID donc c’est sécurisé”
Non.

## 31.3 “Le bouton n’apparaît pas”
Ce n’est pas un contrôle de sécurité.

## 31.4 “Le backend vérifie que l’utilisateur existe”
Ça ne suffit pas. Il faut vérifier qu’il a le droit sur **cet objet précis**.

## 31.5 “On a une middleware auth globale”
Très bien pour l’authentification. Pas suffisant pour l’autorisation objet-par-objet.

---

# 32. Synthèse ultra courte

Une **IDOR** apparaît quand :

- une application expose ou accepte une **référence directe** à un objet interne ;
- un utilisateur peut manipuler cette référence ;
- le serveur **n’applique pas correctement l’autorisation** sur cet objet.

Conséquences possibles :

- lecture de données d’autrui ;
- modification d’objets d’autrui ;
- suppression ;
- fraude ;
- fuite massive ;
- account takeover indirect.

La vraie défense :

- **contrôles d’autorisation côté serveur** ;
- **vérification sur chaque requête et chaque objet** ;
- **scope des requêtes par owner/tenant/relation** ;
- **least privilege** ;
- **deny by default** ;
- **tests d’autorisation automatiques**.

---

# 33. Résumé final à apprendre presque par cœur

> Une IDOR n’est pas juste une “ID dans l’URL”. C’est une faille d’autorisation où le serveur utilise une référence d’objet contrôlée par l’utilisateur sans vérifier correctement si cet utilisateur a le droit d’accéder à l’objet visé. Le correctif principal n’est pas de masquer l’identifiant, mais de faire un contrôle d’autorisation fiable, systématique et objet-par-objet côté serveur.

---

# 34. Références de travail recommandées

Les ressources ci-dessous ont servi de base pour structurer ce cours, complétées et synthétisées dans une logique pédagogique orientée WebSec 0x06.

## Références principales

- OWASP Web Security Testing Guide — *Testing for Insecure Direct Object References*  
  https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References

- OWASP Cheat Sheet — *Insecure Direct Object Reference Prevention Cheat Sheet*  
  https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html

- OWASP Cheat Sheet — *Authorization Cheat Sheet*  
  https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html

- OWASP API Security Top 10 2023 — *API1:2023 Broken Object Level Authorization*  
  https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/

- OWASP Top 10 — *Broken Access Control*  
  https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/

- PortSwigger Web Security Academy — *Insecure direct object references (IDOR)*  
  https://portswigger.net/web-security/access-control/idor

- PortSwigger — *Testing for IDORs*  
  https://portswigger.net/burp/documentation/desktop/testing-workflow/vulnerabilities/access-controls/testing-for-idors

- MITRE CWE-639 — *Authorization Bypass Through User-Controlled Key*  
  https://cwe.mitre.org/data/definitions/639.html

## Pour aller plus loin

- OWASP Top 10 2025 — *Broken Access Control*  
  https://owasp.org/Top10/2025/A01_2025-Broken_Access_Control/

- OWASP 2017 — *A5: Broken Access Control*  
  https://owasp.org/www-project-top-ten/2017/A5_2017-Broken_Access_Control

- PortSwigger Web Security Academy — section complète Access Control  
  https://portswigger.net/web-security/access-control

- OWASP API Security Project  
  https://owasp.org/www-project-api-security/

---

# 35. Conseils pour ton module WebSec 0x06

Pour progresser vite sur ce sujet, entraîne-toi à faire systématiquement ceci dans les labs :

1. identifier **l’objet** ;
2. identifier **la référence** ;
3. identifier **la règle d’accès attendue** ;
4. comparer **compte A vs compte B** ;
5. tester **read / write / delete / export** ;
6. vérifier si la faille est **horizontale, verticale ou blind** ;
7. proposer une remédiation backend propre.

Si tu veux devenir vraiment bon, ne te contente pas de dire “j’ai changé l’ID”. Explique toujours :

- **où l’autorisation aurait dû être faite** ;
- **pourquoi le contrôle actuel échoue** ;
- **comment corriger proprement dans l’architecture**.

---

# 36. Mini glossaire

## Objet
Ressource interne manipulée par l’application : utilisateur, facture, document, commande, fichier, etc.

## Référence directe
Valeur utilisée directement pour retrouver l’objet : ID, UUID, nom, slug, clé.

## Autorisation
Vérification du droit d’un sujet à agir sur une ressource.

## Ownership
Relation de propriété entre un utilisateur et un objet.

## Tenant
Espace logique isolé dans une application multi-tenant.

## BOLA
Broken Object Level Authorization, vocabulaire courant dans les APIs.

## BFLA
Broken Function Level Authorization, accès non autorisé à une fonction plutôt qu’à un objet.

## Defense in depth
Mesures complémentaires qui renforcent la sécurité sans remplacer le contrôle principal.

---

Fin du cours.
