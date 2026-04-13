# Server-Side Request Forgery (SSRF)

## Cours ultra complet, détaillé et pédagogique

> Module : **Server-Side Request Forgery**  
> Contexte projet : **Cyber - WebSec 0x08**  
> Note importante : **les applications sont port-forwarded**. En labo, il faut donc toujours faire attention **au port réellement exposé** quand on teste une redirection, un callback, un fetch distant ou une cible interne.

---

## Table des matières

1. [Introduction](#1-introduction)
2. [Définition de la SSRF](#2-définition-de-la-ssrf)
3. [Pourquoi cette vulnérabilité existe](#3-pourquoi-cette-vulnérabilité-existe)
4. [Comment fonctionne une attaque SSRF](#4-comment-fonctionne-une-attaque-ssrf)
5. [Cycle complet d’une exploitation SSRF](#5-cycle-complet-dune-exploitation-ssrf)
6. [Types de SSRF](#6-types-de-ssrf)
7. [Scénarios d’attaque courants](#7-scénarios-dattaque-courants)
8. [Vecteurs d’entrée typiques](#8-vecteurs-dentrée-typiques)
9. [Protocoles impliqués dans les SSRF](#9-protocoles-impliqués-dans-les-ssrf)
10. [Cibles visées par une SSRF](#10-cibles-visées-par-une-ssrf)
11. [Impacts et risques](#11-impacts-et-risques)
12. [SSRF en environnement cloud](#12-ssrf-en-environnement-cloud)
13. [SSRF dans les API et microservices](#13-ssrf-dans-les-api-et-microservices)
14. [Techniques d’identification d’une SSRF](#14-techniques-didentification-dune-ssrf)
15. [Méthodologie de test en laboratoire autorisé](#15-méthodologie-de-test-en-laboratoire-autorisé)
16. [Bypass fréquents des protections](#16-bypass-fréquents-des-protections)
17. [Erreurs de défense les plus courantes](#17-erreurs-de-défense-les-plus-courantes)
18. [Prévention et mitigation](#18-prévention-et-mitigation)
19. [Design sécurisé côté développement](#19-design-sécurisé-côté-développement)
20. [Détection et monitoring](#20-détection-et-monitoring)
21. [Checklist pentest SSRF](#21-checklist-pentest-ssrf)
22. [Checklist développeur / défense](#22-checklist-développeur--défense)
23. [Exemples pédagogiques](#23-exemples-pédagogiques)
24. [Pièges classiques à l’examen ou en projet](#24-pièges-classiques-à-lexamen-ou-en-projet)
25. [Résumé final à retenir](#25-résumé-final-à-retenir)
26. [Mini glossaire](#26-mini-glossaire)
27. [Ressources de référence](#27-ressources-de-référence)

---

## 1. Introduction

La **Server-Side Request Forgery (SSRF)** est une vulnérabilité dans laquelle une application permet à un attaquant de **forcer le serveur à envoyer une requête vers une destination choisie par l’attaquant**.

L’idée essentielle est simple :

- l’attaquant **ne contacte pas directement** la cible finale ;
- il **demande au serveur vulnérable** de le faire à sa place ;
- la requête part donc **depuis le serveur**, avec **sa position réseau**, **ses droits**, **sa visibilité interne** et parfois **ses credentials**.

C’est précisément ce qui rend la SSRF dangereuse : le serveur peut atteindre des ressources que l’attaquant ne peut pas atteindre depuis Internet.

Exemples classiques :

- accès à `127.0.0.1` ou `localhost` ;
- accès à des IP privées comme `10.0.0.5`, `172.16.x.x`, `192.168.x.x` ;
- accès à des services internes non exposés ;
- accès à des métadonnées cloud ;
- rebond pour scanner un réseau interne ;
- enchaînement vers fuite de secrets, compromission de services, voire RCE selon l’architecture.

---

## 2. Définition de la SSRF

Une SSRF apparaît lorsqu’une application :

1. accepte une **URL**, un **host**, une **IP**, un **nom de domaine**, ou une **destination réseau** contrôlable par l’utilisateur ;
2. effectue ensuite une requête **côté serveur** vers cette destination ;
3. sans **validation stricte**, **normalisation correcte**, **restriction réseau**, **allowlist**, ou **contrôle du protocole**.

Autrement dit :

> **La SSRF est une faille de confiance sur la destination d’une requête émise par le serveur.**

L’application pense effectuer un fetch légitime, mais elle exécute en réalité une instruction réseau pilotée par l’attaquant.

---

## 3. Pourquoi cette vulnérabilité existe

La SSRF naît très souvent d’une fonctionnalité légitime. Par exemple :

- import d’image depuis une URL ;
- aperçu d’un site web ;
- génération de miniatures ;
- webhook ;
- callback serveur ;
- conversion PDF depuis une URL ;
- fetch d’un flux RSS ;
- validation d’un endpoint distant ;
- proxy interne ;
- connecteur API ;
- outil d’administration qui “teste une URL”.

Le problème n’est donc pas la fonctionnalité en elle-même, mais le fait de **laisser l’utilisateur choisir une destination réseau insuffisamment encadrée**.

Beaucoup de développeurs pensent :

- “ce n’est qu’une URL” ;
- “le serveur va juste télécharger un fichier” ;
- “on a bloqué `localhost`, donc c’est bon” ;
- “on a mis une regex, donc c’est sécurisé”.

En pratique, la sécurité SSRF est difficile parce qu’elle implique à la fois :

- parsing d’URL,
- résolution DNS,
- validation IP,
- gestion des redirections,
- contrôle des protocoles,
- segmentation réseau,
- protection cloud,
- authentification inter-services.

---

## 4. Comment fonctionne une attaque SSRF

Schéma mental :

```text
Attaquant -> Application vulnérable -> Ressource cible atteinte par le serveur
```

Exemple simple :

1. une application propose un champ `image_url` ;
2. le backend récupère l’image à cette URL ;
3. l’attaquant soumet `http://127.0.0.1:8080/admin` ;
4. le serveur envoie la requête vers son propre service local ;
5. la réponse peut être renvoyée à l’attaquant, ou utilisée en interne.

Le point clé :

- **la requête est émise par le serveur** ;
- donc les ACL, firewall, filtrages IP ou règles “interne uniquement” peuvent être contournés ;
- le serveur devient un **pivot**.

---

## 5. Cycle complet d’une exploitation SSRF

Une exploitation SSRF suit souvent ce déroulé :

### 5.1 Trouver un point d’entrée

L’attaquant repère un paramètre qui ressemble à :

- `url=`
- `image=`
- `feed=`
- `endpoint=`
- `callback=`
- `avatar=`
- `proxy=`
- `dest=`
- `path=`
- `link=`

### 5.2 Vérifier si le serveur fait réellement la requête

Il fournit une URL vers un domaine qu’il contrôle ou un serveur d’observation, puis vérifie si un hit est reçu.

### 5.3 Identifier le comportement

La SSRF peut être :

- **non blind** : la réponse est visible ;
- **blind** : seule une interaction externe prouve l’appel ;
- **semi-blind** : on observe des différences de temps, de statut HTTP ou d’erreur.

### 5.4 Cartographier ce qui est accessible

Ensuite, l’attaquant teste :

- `127.0.0.1`
- `localhost`
- IP privées
- ports internes
- services d’administration
- métadonnées cloud
- protocoles alternatifs

### 5.5 Extraire, pivoter, escalader

Selon l’architecture, il peut :

- lire des données ;
- scanner le réseau ;
- toucher un admin panel ;
- voler des credentials ;
- invoquer une API interne ;
- déclencher un job ;
- exploiter un second service ;
- obtenir du RCE indirect.

---

## 6. Types de SSRF

### 6.1 SSRF classique

La réponse du serveur cible revient à l’attaquant.

Exemple :

- l’application télécharge l’URL et affiche le contenu ;
- l’attaquant lit directement la page interne.

C’est le cas le plus confortable pour l’exploitation.

### 6.2 SSRF blind

La réponse n’est pas affichée, mais la requête part bien.

L’attaquant doit s’appuyer sur :

- logs externes ;
- interactions DNS/HTTP ;
- variations temporelles ;
- codes de retour ;
- changements observables côté application.

### 6.3 SSRF semi-blind

La réponse complète n’est pas visible, mais des signaux partiels existent :

- “connection refused” ;
- “timeout” ;
- “invalid content type” ;
- différence entre port ouvert et fermé ;
- temps de traitement variable.

Cela suffit souvent pour :

- faire de la reconnaissance interne ;
- distinguer des hôtes atteignables ;
- détecter des ports ouverts.

### 6.4 SSRF avec rebond par redirection

L’application n’autorise peut-être qu’un domaine “de confiance”, mais elle **suit ensuite une redirection** vers une cible interdite.

Exemple :

- URL autorisée : `https://trusted.example/resource`
- le serveur suit une redirection `302` vers `http://127.0.0.1:8080/admin`

### 6.5 SSRF second-order

La donnée n’est pas exploitée immédiatement.

Exemple :

- un utilisateur enregistre une webhook URL ;
- plus tard, un job serveur appelle cette URL ;
- la SSRF se déclenche au moment du traitement différé.

### 6.6 SSRF via parser confusion

Le problème vient parfois d’un écart entre :

- ce que la validation pense lire ;
- ce que la bibliothèque réseau interprète réellement.

C’est fréquent avec :

- userinfo dans l’URL,
- encodages,
- forme décimale/hexadécimale des IP,
- IPv6,
- mélange de slash et backslash,
- doubles résolutions DNS,
- URL mal normalisées.

---

## 7. Scénarios d’attaque courants

### 7.1 Lecture d’un service local

L’application permet de fetcher une URL. L’attaquant vise :

- `http://127.0.0.1:8000/`
- `http://localhost/admin`

Objectif : atteindre un panneau non exposé publiquement.

### 7.2 Accès à un service interne sur IP privée

Exemple :

- `http://10.0.0.12:8080/health`
- `http://192.168.1.50:5000/internal-api`

Objectif : contourner l’isolement réseau.

### 7.3 Accès aux métadonnées cloud

Exemple historique en cloud :

- `http://169.254.169.254/`

Objectif : récupérer des informations d’instance, credentials temporaires, rôles IAM, tokens, configuration, identité du workload.

### 7.4 SSRF vers un service d’administration

Exemples :

- interface de monitoring ;
- tableau de bord interne ;
- API de debug ;
- endpoint d’orchestration ;
- console de queue ;
- panneau d’admin non authentifié car “interne seulement”.

### 7.5 Port scan interne via SSRF

L’attaquant mesure :

- port ouvert = réponse différente ;
- port fermé = refus immédiat ;
- hôte inaccessible = timeout.

Même sans voir les réponses, cela permet de cartographier le réseau.

### 7.6 Exploitation d’un protocole non HTTP

Certaines bibliothèques acceptent d’autres schémas :

- `file://`
- `gopher://`
- `ftp://`
- `dict://`
- `ldap://`

Cela peut ouvrir des scénarios plus graves :

- lecture de fichiers locaux ;
- émission de charges brutes vers un service TCP ;
- interactions avec Redis, SMTP, Memcached, etc. selon le contexte.

### 7.7 SSRF dans une API

Une API backend-to-backend permet par exemple :

- de valider une URL partenaire ;
- de récupérer un document distant ;
- de configurer un webhook.

L’attaquant injecte une destination interne à la place d’un partenaire externe.

---

## 8. Vecteurs d’entrée typiques

La SSRF ne se cache pas uniquement derrière un paramètre nommé `url`.

### 8.1 Paramètres HTTP évidents

- `url`
- `uri`
- `dest`
- `redirect`
- `target`
- `endpoint`
- `domain`
- `feed`
- `proxy`

### 8.2 Fonctionnalités orientées contenu externe

- import d’avatar depuis URL ;
- import de PDF ;
- capture screenshot d’un site ;
- génération de preview ;
- unfurling de lien ;
- parser RSS ;
- scraper ;
- convertisseur d’URL en image ;
- moteur de webhook.

### 8.3 Paramètres indirects

- XML avec référence externe ;
- JSON contenant une URL ;
- YAML ;
- template ;
- champ de configuration ;
- fichier importé contenant une URL qui sera traitée plus tard.

### 8.4 En-têtes ou valeurs moins évidentes

- `Referer`
- `Host`
- `X-Forwarded-Host`
- URL de callback enregistrée dans un profil
- endpoint d’intégration stocké en base

---

## 9. Protocoles impliqués dans les SSRF

Quand on entend SSRF, on pense souvent à HTTP. Pourtant, le danger dépend aussi du **schéma accepté** par la bibliothèque côté serveur.

### 9.1 `http://` et `https://`

Ce sont les plus fréquents. Ils suffisent déjà à :

- contacter le réseau interne ;
- atteindre localhost ;
- appeler des API d’administration ;
- lire des endpoints internes.

### 9.2 `file://`

Selon la stack et la bibliothèque, ce schéma peut permettre de lire un fichier local.

Exemple conceptuel :

```text
file:///etc/passwd
```

Toutes les applications ne l’acceptent pas, mais quand c’est le cas, l’impact peut être majeur.

### 9.3 `gopher://`

Très intéressant en exploitation avancée, car il peut permettre d’envoyer des **payloads bruts** à un service TCP.

En laboratoire, cela peut servir à interagir avec des services comme :

- Redis ;
- Memcached ;
- SMTP ;
- autres démons textuels.

### 9.4 `ftp://`, `dict://`, `ldap://`

Moins fréquents, mais parfois acceptés par certaines bibliothèques ou wrappers.

### 9.5 Schémas custom ou handlers applicatifs

Certaines applications enregistrent leurs propres schémas ou font transiter des requêtes via des composants spécifiques.

---

## 10. Cibles visées par une SSRF

### 10.1 Le serveur lui-même

- `127.0.0.1`
- `localhost`
- `[::1]`

### 10.2 Le réseau privé

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

### 10.3 Les adresses spéciales

- `169.254.0.0/16` (link-local)
- `169.254.169.254` (métadonnées cloud selon fournisseur)

### 10.4 Les interfaces d’administration

- dashboards
- `/admin`
- `/metrics`
- `/debug`
- `/actuator`
- `/health`
- outils internes non authentifiés

### 10.5 Les services backend

- base de données HTTPisées
- moteurs de recherche
- caches exposés localement
- file d’attente
- orchestrateurs
- registries

### 10.6 Les services tiers atteignables par le serveur

Une SSRF peut aussi être utilisée pour :

- attaquer un tiers ;
- contourner une restriction IP ;
- faire du spam ou des callbacks depuis une adresse “de confiance”.

---

## 11. Impacts et risques

La SSRF n’est pas “juste” une lecture d’URL. Son impact réel dépend du contexte réseau et applicatif.

### 11.1 Contournement des contrôles d’accès réseau

Le serveur possède souvent une visibilité interne inaccessible depuis Internet.

### 11.2 Lecture de ressources sensibles

- pages internes ;
- tokens ;
- secrets ;
- configuration ;
- endpoints de debug ;
- métadonnées cloud.

### 11.3 Reconnaissance interne

- découverte d’hôtes ;
- découverte de ports ;
- identification de technologies ;
- compréhension de l’architecture.

### 11.4 Mouvement latéral logique

Même sans shell, l’attaquant peut utiliser le serveur comme pivot applicatif.

### 11.5 Compromission cloud

Si l’accès aux métadonnées permet de voler des credentials temporaires, la SSRF peut devenir une porte d’entrée vers le compte cloud.

### 11.6 Exécution de commandes indirecte

La SSRF n’est pas elle-même du RCE, mais elle peut mener à un RCE si elle atteint un service interne vulnérable ou un composant d’administration capable d’exécuter des actions dangereuses.

### 11.7 Impact métier

- fuite de données ;
- compromission d’environnement ;
- destruction d’intégrité ;
- indisponibilité ;
- pivot vers d’autres systèmes ;
- impact réglementaire et réputationnel.

---

## 12. SSRF en environnement cloud

Le cloud a rendu la SSRF encore plus critique.

### 12.1 Pourquoi

Les workloads cloud accèdent souvent à un service local de métadonnées, utile pour :

- connaître l’identité de l’instance ;
- récupérer des tokens temporaires ;
- lire la configuration ;
- découvrir des rôles associés.

Si l’application est vulnérable à une SSRF, l’attaquant peut tenter d’appeler ce service.

### 12.2 Adresse souvent ciblée

L’exemple le plus connu est l’adresse link-local :

```text
169.254.169.254
```

### 12.3 Risque réel

En fonction du fournisseur et de la configuration :

- lecture de métadonnées ;
- récupération de credentials temporaires ;
- accès à des API cloud ;
- compromission plus large de l’environnement.

### 12.4 Défenses cloud importantes

- IMDSv2 ou mécanisme équivalent quand disponible ;
- limitation du hop count ;
- egress filtering ;
- segmentation réseau ;
- moindre privilège IAM ;
- suppression des accès metadata inutiles ;
- authentification forte entre services.

---

## 13. SSRF dans les API et microservices

La SSRF moderne apparaît souvent dans des architectures API.

### 13.1 Cas typiques

- webhook registration ;
- “test connectivity” ;
- import depuis une URL ;
- proxy d’intégration ;
- connecteur vers service externe ;
- fetch d’un document utilisateur.

### 13.2 Pourquoi c’est dangereux dans les microservices

Parce que les services internes se font souvent confiance.

Exemples :

- service A peut appeler service B sans auth forte ;
- certains endpoints sont “protégés” uniquement par réseau interne ;
- l’API gateway n’est pas présente sur les chemins est-ouest.

Une SSRF dans un service apparemment mineur peut donc ouvrir l’accès à tout le maillage interne.

---

## 14. Techniques d’identification d’une SSRF

### 14.1 Indices dans l’application

Chercher des fonctionnalités qui :

- récupèrent une ressource distante ;
- affichent un aperçu d’URL ;
- téléchargent une image ;
- déclenchent un callback ;
- valident un endpoint ;
- consomment une URL saisie par l’utilisateur.

### 14.2 Indices dans les erreurs

Exemples :

- `connection refused`
- `timeout`
- `could not resolve host`
- `unsupported protocol`
- `invalid URL`
- `ECONNREFUSED`
- `dial tcp`
- `socket hang up`

Ces messages révèlent souvent que le backend tente réellement une connexion.

### 14.3 Out-of-band testing

Utiliser un domaine ou un serveur de journalisation contrôlé pour détecter :

- une requête HTTP ;
- une résolution DNS ;
- l’User-Agent du backend ;
- l’adresse IP de sortie du serveur.

### 14.4 Time-based testing

En blind SSRF, on peut différencier :

- hôte atteint mais lent ;
- port fermé ;
- hôte non joignable ;
- redirection suivie ou non.

### 14.5 Variation de ports

Tester différents ports permet de repérer :

- service web interne ;
- panel admin ;
- service de monitoring ;
- API technique.

### 14.6 Analyse du comportement de redirection

Vérifier si l’application :

- suit les 30x ;
- limite le nombre de redirects ;
- revalide la destination après redirection ;
- conserve la politique de sécurité au second hop.

---

## 15. Méthodologie de test en laboratoire autorisé

Dans un lab comme **Cyber - WebSec 0x08**, il faut raisonner proprement.

### Étape 1 — Identifier le sink SSRF

Repérer où l’application effectue un fetch serveur.

### Étape 2 — Prouver la requête serveur

Faire pointer la valeur vers une ressource observable.

### Étape 3 — Déterminer la nature de la SSRF

- réponse visible ?
- blind ?
- semi-blind ?
- redirections suivies ?
- protocole limité ?

### Étape 4 — Tester localhost et IP privées

Toujours dans le périmètre autorisé du lab.

### Étape 5 — Cartographier les comportements

Noter :

- codes de réponse ;
- temps ;
- messages d’erreur ;
- ports accessibles ;
- chemins intéressants.

### Étape 6 — Vérifier les métadonnées cloud ou services locaux si le scénario s’y prête

Uniquement si cela correspond au lab.

### Étape 7 — Documenter précisément

- point d’entrée ;
- payloads ;
- observations ;
- impacts ;
- remédiations.

### Point important sur ce projet

Le rappel “**All App are Port forwarded**” signifie qu’il ne faut pas se tromper entre :

- le port réel du service dans l’infrastructure ;
- le port exposé au joueur ;
- le port utilisé par une redirection interne.

En pratique, quand tu vois une URL, une redirection ou un service annexe, **le port fait partie intégrante de l’analyse**.

---

## 16. Bypass fréquents des protections

Les défenses SSRF faibles tombent souvent à cause d’un mauvais filtrage.

### 16.1 Blocage naïf de `localhost`

Si seule la chaîne `localhost` est bloquée, l’attaquant peut essayer :

- `127.0.0.1`
- `127.1`
- `0.0.0.0`
- `[::1]`
- représentation décimale d’IP
- représentation hexadécimale ou octale selon le parseur
- DNS résolvant vers localhost

### 16.2 Blacklist textuelle

Bloquer des mots-clés (`localhost`, `127.0.0.1`, `file`) n’est pas suffisant.

### 16.3 Redirections non revalidées

L’URL initiale est autorisée, mais pas la destination finale.

### 16.4 DNS rebinding / DNS pinning insuffisant

Le domaine paraît légitime au moment du contrôle, puis résout ensuite vers une IP interdite.

### 16.5 Validation avant normalisation

Si on valide l’URL brute sans normalisation canonique, le parseur réseau peut l’interpréter différemment.

### 16.6 Confusion userinfo dans les URL

Exemple conceptuel :

```text
http://allowed.example@127.0.0.1/
```

À l’œil, on croit voir `allowed.example`, mais l’hôte réel peut être `127.0.0.1` selon l’interpréteur.

### 16.7 IPv6 oubliée

Beaucoup de défenses filtrent IPv4 mais oublient `[::1]` et autres formes IPv6.

### 16.8 Schémas non bloqués

L’application bloque `http://` vers IP privées mais accepte `gopher://` ou `file://`.

---

## 17. Erreurs de défense les plus courantes

### 17.1 “On a une regex”

Une regex n’est pas une politique de sécurité réseau.

### 17.2 “On a blacklisté localhost”

Insuffisant.

### 17.3 “Le service interne n’est pas exposé à Internet”

C’est précisément ce qu’exploite la SSRF.

### 17.4 “Le réseau interne est fiable”

En architecture moderne, cette hypothèse est dangereuse.

### 17.5 “On fait confiance à ce microservice”

Une SSRF dans un microservice peut briser cette confiance.

### 17.6 “Le serveur ne renvoie pas la réponse, donc ce n’est pas grave”

Blind SSRF reste exploitable pour :

- scan interne ;
- callbacks ;
- interactions cloud ;
- déclenchement d’actions serveur.

---

## 18. Prévention et mitigation

La vraie défense SSRF repose sur **plusieurs couches**.

## 18.1 Première règle : éviter de laisser l’utilisateur choisir librement une destination réseau

La meilleure défense est architecturale :

- ne pas accepter d’URL arbitraire ;
- utiliser des identifiants métiers à la place ;
- sélectionner une ressource via une clé interne ;
- faire correspondre un alias à une destination connue.

Exemple :

- mauvais : l’utilisateur fournit `https://nimporte-quoi` ;
- meilleur : l’utilisateur choisit un fournisseur dans une liste serveur.

## 18.2 Allowlist stricte

Si une URL externe est vraiment nécessaire :

- autoriser seulement des domaines précis ;
- idéalement des hôtes exacts ;
- éventuellement des IP précises ;
- contrôler le port autorisé ;
- contrôler le schéma autorisé.

La logique doit être :

> **Tout est interdit sauf ce qui est explicitement permis.**

## 18.3 Normaliser avant de valider

Toujours :

1. parser l’URL proprement ;
2. normaliser ;
3. résoudre le nom ;
4. vérifier l’IP finale ;
5. vérifier que l’IP n’est pas locale, privée, loopback, link-local, multicast, etc. ;
6. n’autoriser ensuite la connexion que si tout est conforme.

## 18.4 Vérifier toutes les résolutions DNS

Le domaine peut changer ou résoudre vers plusieurs IP.

Il faut vérifier :

- la résolution initiale ;
- les IP réellement utilisées ;
- les redirections ;
- les éventuels changements de résolution.

## 18.5 Bloquer les redirections non sûres

Options :

- désactiver complètement le suivi automatique des redirections ;
- ou revalider **chaque hop**.

## 18.6 Restreindre les schémas

Souvent, seules `http` et `https` devraient être autorisées.

Bloquer ou désactiver :

- `file`
- `gopher`
- `ftp`
- `dict`
- `ldap`
- tout schéma non indispensable.

## 18.7 Bloquer les destinations sensibles

Refuser les IP ou plages suivantes :

- loopback ;
- privées ;
- link-local ;
- metadata endpoints ;
- adresses non routables ;
- interfaces locales ;
- services d’administration.

## 18.8 Segmenter le réseau et filtrer l’egress

Même si une SSRF existe, le serveur ne devrait pas pouvoir parler librement à tout le réseau.

Défenses réseau :

- firewall de sortie ;
- proxy de sortie ;
- ACL ;
- segmentation ;
- zéro trust interne.

## 18.9 Authentifier les services internes

Un endpoint interne ne doit pas être “sécurisé” uniquement parce qu’il est interne.

Il faut :

- authentification service-to-service ;
- autorisation ;
- signatures ;
- tokens courts ;
- mTLS si pertinent.

## 18.10 Protéger les métadonnées cloud

- utiliser les protections modernes du fournisseur ;
- exiger des jetons/session metadata quand possible ;
- limiter le hop count ;
- empêcher les workloads d’accéder inutilement aux metadata ;
- appliquer le moindre privilège IAM.

## 18.11 Timeouts, taille de réponse, méthodes HTTP

Réduire aussi la surface opérationnelle :

- timeout court ;
- taille max de réponse ;
- méthodes limitées ;
- pas de forwarding arbitraire d’en-têtes ;
- pas de cookies ni secrets transmis à des cibles non prévues.

---

## 19. Design sécurisé côté développement

### 19.1 Modèle dangereux

```text
Utilisateur -> fournit URL arbitraire -> backend fetch -> réponse renvoyée
```

### 19.2 Modèle plus sûr

```text
Utilisateur -> choisit une ressource logique -> backend mappe vers une destination autorisée -> fetch contrôlé
```

### 19.3 Bonnes pratiques de conception

- éviter les URL arbitraires ;
- créer des clients dédiés par intégration ;
- configurer les destinations côté serveur ;
- ne jamais réutiliser un “proxy générique” pour tout ;
- centraliser la logique de validation/fetch ;
- journaliser toute tentative refusée ;
- tester les cas limites d’URL.

### 19.4 Défense en profondeur

Même si la validation applicative échoue, il faut encore :

- restriction egress ;
- segmentation ;
- auth inter-services ;
- protection cloud ;
- monitoring.

---

## 20. Détection et monitoring

La prévention est prioritaire, mais la détection compte aussi.

### 20.1 Logs applicatifs utiles

Journaliser :

- destination demandée ;
- hôte résolu ;
- IP finale ;
- schéma ;
- port ;
- méthode ;
- redirects suivies ;
- taille de réponse ;
- erreurs réseau.

### 20.2 Signaux suspects

- tentatives vers `127.0.0.1` ;
- tentatives vers IP privées ;
- tentatives vers `169.254.169.254` ;
- scan de ports séquentiel ;
- schémas inhabituels ;
- échecs multiples de résolution ;
- URLs avec encodages anormaux.

### 20.3 Contrôles SOC / réseau

- détection d’egress inhabituel ;
- trafic vers metadata endpoints ;
- pics de connexions vers adresses internes ;
- activité inter-services inhabituelle.

---

## 21. Checklist pentest SSRF

Quand tu testes une SSRF en laboratoire autorisé, pense à :

- identifier le paramètre contrôlé ;
- confirmer qu’une requête serveur part ;
- déterminer si la réponse est visible ;
- tester localhost ;
- tester IP privées ;
- tester différents ports ;
- observer les erreurs et timings ;
- vérifier les redirections ;
- vérifier les schémas autorisés ;
- penser aux métadonnées cloud ;
- documenter précisément le comportement ;
- rester strictement dans le périmètre autorisé.

---

## 22. Checklist développeur / défense

Avant mise en production :

- l’utilisateur peut-il fournir une URL arbitraire ?
- peut-on remplacer cela par un identifiant logique ?
- y a-t-il une allowlist stricte ?
- la normalisation est-elle correcte ?
- les IP privées / loopback / link-local sont-elles bloquées ?
- les redirections sont-elles désactivées ou revalidées ?
- seuls `http/https` sont-ils autorisés ?
- les services internes exigent-ils une auth forte ?
- l’egress réseau est-il filtré ?
- les metadata cloud sont-elles protégées ?
- les logs remontent-ils les destinations refusées ?
- des tests SSRF existent-ils dans la QA / sécurité ?

---

## 23. Exemples pédagogiques

## 23.1 Exemple 1 — Import d’image vulnérable

Fonctionnalité :

- l’utilisateur soumet une URL d’image ;
- le backend télécharge l’image ;
- aucune restriction sérieuse n’est appliquée.

Risque :

- au lieu d’une image publique, l’attaquant vise une ressource interne.

Leçon :

- toute fonctionnalité de téléchargement serveur est potentiellement un sink SSRF.

## 23.2 Exemple 2 — Validation de webhook

Fonctionnalité :

- l’utilisateur enregistre une URL de webhook ;
- l’application envoie une requête de test.

Risque :

- l’attaquant fournit une cible interne ;
- l’application révèle si elle est joignable.

Leçon :

- une simple “vérification de connectivité” peut suffire à créer une SSRF.

## 23.3 Exemple 3 — Preview de lien

Fonctionnalité :

- un chat interne génère un aperçu du lien saisi.

Risque :

- l’application visite elle-même la cible ;
- un lien interne ou piégé peut provoquer une SSRF.

Leçon :

- les systèmes d’unfurling sont des candidats classiques.

## 23.4 Exemple 4 — API proxy générique

Fonctionnalité :

- une route backend appelle “n’importe quelle URL” pour contourner le CORS côté navigateur.

Risque :

- le backend devient un proxy SSRF.

Leçon :

- contourner le CORS avec un proxy générique est souvent une très mauvaise idée.

---

## 24. Pièges classiques à l’examen ou en projet

### Piège 1

Confondre SSRF et redirection ouverte.

- **Open redirect** : on fait rediriger le navigateur de la victime.
- **SSRF** : on force **le serveur** à effectuer une requête.

### Piège 2

Croire que SSRF = uniquement HTTP.

Faux. Le danger peut inclure plusieurs schémas.

### Piège 3

Croire que si la réponse n’est pas visible, la faille est mineure.

Faux. Une blind SSRF peut suffire à compromettre un environnement.

### Piège 4

Oublier le contexte cloud.

Aujourd’hui, SSRF + cloud = scénario critique très fréquent.

### Piège 5

Ne regarder que `localhost`.

Il faut penser aussi à :

- IP privées ;
- loopback alternatives ;
- IPv6 ;
- link-local ;
- redirections ;
- DNS.

### Piège 6

Bloquer par blacklist textuelle.

C’est l’une des pires approches.

---

## 25. Résumé final à retenir

Si tu dois expliquer la SSRF simplement à quelqu’un :

> Une SSRF, c’est quand une application accepte une destination contrôlée par l’utilisateur et envoie une requête côté serveur vers cette destination. L’attaquant exploite alors la position réseau et les privilèges du serveur pour atteindre des ressources qu’il ne pourrait pas joindre directement.

À retenir absolument :

- la requête part **depuis le serveur** ;
- cela peut donner accès à **localhost**, au **réseau interne**, aux **métadonnées cloud** et à des **services d’administration** ;
- la SSRF peut être **classique**, **blind** ou **semi-blind** ;
- l’impact va de la **reconnaissance interne** jusqu’à la **compromission cloud** ou à une **chaîne menant au RCE** ;
- la défense efficace repose sur **l’allowlist**, la **normalisation**, le **blocage des IP sensibles**, la **restriction des schémas**, la **revalidation des redirections**, l’**egress filtering** et l’**authentification inter-services**.

---

## 26. Mini glossaire

### SSRF

Server-Side Request Forgery : falsification de requête côté serveur.

### Blind SSRF

SSRF où la réponse n’est pas directement renvoyée à l’attaquant.

### Sink

Point du code où une donnée utilisateur est utilisée dans une action sensible, ici un fetch réseau.

### Loopback

Adresse qui désigne la machine elle-même, comme `127.0.0.1`.

### Link-local

Plage d’adresses locale non routée globalement, souvent utilisée pour certains services système ou cloud.

### Egress filtering

Filtrage des connexions sortantes d’un serveur.

### Allowlist

Liste explicite de ce qui est autorisé.

### Metadata service

Service local exposant des informations sur l’instance ou le workload en environnement cloud.

---

## 27. Ressources de référence

### Références fondamentales

- OWASP Cheat Sheet — Server-Side Request Forgery Prevention Cheat Sheet
- OWASP — Server Side Request Forgery
- OWASP Top 10 2021 — A10: Server-Side Request Forgery
- PortSwigger Web Security Academy — Server-side request forgery (SSRF)
- PortSwigger — Testing for SSRF vulnerabilities with Burp Suite
- MITRE CWE-918 — Server-Side Request Forgery (SSRF)
- MDN — Server-Side Request Forgery (SSRF)
- OWASP WSTG — Testing for Server-Side Request Forgery

### Conseils de travail pour ton module

Pour ce projet, tu peux structurer ton apprentissage comme suit :

1. comprendre le concept théorique ;
2. repérer les sinks SSRF dans une application ;
3. distinguer SSRF classique / blind / semi-blind ;
4. tester localhost, IP privées, ports et redirects ;
5. prendre en compte le contexte **port-forwarded** de **Cyber - WebSec 0x08** ;
6. documenter proprement les impacts et les remédiations.

---

## Conclusion

La SSRF fait partie des vulnérabilités serveur les plus importantes à comprendre aujourd’hui, car elle se situe à l’intersection de plusieurs mondes :

- sécurité applicative,
- réseau,
- cloud,
- microservices,
- architecture.

Un bon pentester doit savoir :

- reconnaître rapidement un sink SSRF,
- confirmer la requête serveur,
- caractériser l’impact réel,
- penser à localhost, IP privées, cloud metadata, redirects et schémas,
- proposer une remédiation solide en profondeur.

Un bon développeur, lui, doit retenir une règle simple :

> **Ne laisse jamais un utilisateur piloter librement les destinations réseau de ton serveur.**

