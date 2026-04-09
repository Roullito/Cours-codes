# Web Application Forensics — Cours complet, pédagogique et ultra détaillé

## Table des matières

1. Introduction
2. Qu'est-ce que la Digital Forensics ?
3. Qu'est-ce que la Web Application Forensics ?
4. Positionnement dans la cybersécurité : IR, DFIR, SOC, Blue Team
5. Objectifs d’une investigation forensique web
6. Différence entre analyse défensive, réponse à incident et forensics
7. Principes fondamentaux de la forensics
8. Cadre légal, éthique et bonnes pratiques
9. Architecture d’une application web : ce qu’il faut comprendre avant d’enquêter
10. Surface d’attaque d’une application web
11. Sources de preuves dans une investigation web
12. Journaux applicatifs : types, formats, valeur probante
13. Comment analyser les logs d’application web
14. Comment tracer l’origine d’une attaque avec les logs
15. Corrélation de logs : méthode complète
16. Analyse réseau dans un contexte forensique web
17. Utilisation de Wireshark dans une investigation web
18. Utilisation de Burp Suite dans une investigation web
19. Rôle des firewalls Linux, iptables et firewalld
20. Auth.log et dmesg : ce qu’ils apportent à l’enquête
21. Méthodologie complète d’investigation web
22. Cas d’usage : reconstitution d’une attaque
23. Vulnérabilités web courantes et indicateurs forensiques
24. Conservation des preuves et chaîne de possession
25. Rédaction d’un rapport forensique clair et professionnel
26. Erreurs classiques à éviter
27. Checklist opérationnelle
28. Mini glossaire
29. Conclusion

---

# 1. Introduction

La **Web Application Forensics** est la branche de la forensics numérique qui cherche à **reconstituer, comprendre, documenter et prouver** ce qu’il s’est passé lorsqu’une application web a été ciblée, compromise, abusée ou utilisée de manière malveillante.

En pratique, cela revient à répondre à des questions comme :

- **Qui** a attaqué ou utilisé abusivement l’application ?
- **Depuis où** l’attaque semble-t-elle venir ?
- **Quand** l’attaque a-t-elle commencé ?
- **Comment** l’attaquant a-t-il procédé ?
- **Quelles pages, routes, API ou ressources** ont été touchées ?
- **Quelles données** ont été consultées, modifiées ou exfiltrées ?
- **Quel a été l’impact réel** ?
- **Quelles preuves** permettent d’étayer ces conclusions ?

La difficulté principale est qu’une application web génère énormément de données réparties dans plusieurs couches :

- navigateur / client
- reverse proxy
- serveur web
- application
- base de données
- système d’exploitation
- pare-feu
- traces réseau
- solutions de supervision ou de sécurité

L’investigateur forensique doit donc être capable de **corréler** toutes ces sources pour reconstruire une chronologie fiable.

---

# 2. Qu'est-ce que la Digital Forensics ?

La **Digital Forensics** (forensique numérique) est la discipline qui consiste à :

- identifier des preuves numériques,
- les préserver,
- les collecter,
- les analyser,
- les interpréter,
- puis les présenter de manière rigoureuse et défendable.

## 2.1 Objectif

Le but n’est pas seulement de “voir qu’il y a eu un problème”.

Le but est de produire des conclusions **fiables, traçables et justifiables**.

## 2.2 Grandes branches de la forensics

On distingue souvent :

### A. Computer Forensics
Analyse de postes, serveurs, systèmes de fichiers, disques, artefacts mémoire, comptes utilisateurs.

### B. Network Forensics
Analyse de flux réseau, paquets, sessions, métadonnées de communication, captures PCAP.

### C. Mobile Forensics
Analyse de smartphones, tablettes, applications mobiles, données locales et synchronisées.

### D. Malware Forensics / Reverse Engineering
Analyse de binaires malveillants, persistance, comportements, IOC.

### E. Cloud Forensics
Analyse d’environnements cloud, logs IAM, API, containers, stockages objets, événements de contrôle.

### F. Web Application Forensics
Analyse spécifique des incidents liés aux applications web, APIs, serveurs web, reverse proxies, traces HTTP et logs applicatifs.

---

# 3. Qu'est-ce que la Web Application Forensics ?

La **Web Application Forensics** est la discipline qui consiste à examiner toutes les traces laissées autour d’une application web pour comprendre un incident.

Elle s’intéresse notamment à :

- requêtes HTTP/HTTPS
- en-têtes
- cookies et sessions
- logs d’accès
- logs applicatifs
- logs d’authentification
- logs système
- événements du WAF / firewall
- base de données
- reverse proxy
- traces réseau
- erreurs côté serveur
- comportements anormaux côté utilisateur

## 3.1 But principal

Reconstituer :

- la **chronologie**
- la **technique utilisée**
- l’**origine apparente**
- la **portée**
- les **dommages**
- les **preuves**

## 3.2 Exemple concret

Supposons qu’un site web subisse une fuite de données.

L’investigation cherchera à savoir :

1. quel endpoint a été abusé,
2. si l’attaquant s’est authentifié ou non,
3. quelles IP ont accédé aux ressources,
4. si un dump massif a eu lieu,
5. si l’exfiltration est passée par l’application ou par un accès système,
6. quels comptes ont été touchés,
7. si l’incident est fini ou toujours en cours.

---

# 4. Positionnement dans la cybersécurité : IR, DFIR, SOC, Blue Team

## 4.1 Incident Response (IR)
Réponse rapide à un incident :
- containment
- eradication
- recovery
- communication

## 4.2 Digital Forensics and Incident Response (DFIR)
Le **DFIR** combine :
- l’investigation technique approfondie,
- la collecte de preuves,
- la réponse opérationnelle à l’incident.

## 4.3 SOC / Blue Team
Le SOC détecte, surveille, alerte.
La Blue Team défend l’infrastructure.
La forensics intervient souvent après détection pour comprendre précisément l’événement.

## 4.4 Relation entre ces disciplines

On peut résumer ainsi :

- **SOC** : “Quelque chose d’anormal se passe.”
- **IR** : “On contient l’incident.”
- **Forensics** : “On établit exactement ce qu’il s’est passé.”
- **DFIR** : “On fait les deux de manière structurée.”

---

# 5. Objectifs d’une investigation forensique web

Une investigation bien menée doit répondre au minimum à ces axes :

## 5.1 Détection
Identifier qu’un incident a eu lieu.

## 5.2 Qualification
Déterminer :
- type d’incident,
- niveau de gravité,
- périmètre,
- urgence.

## 5.3 Attribution technique
Identifier la source technique apparente :
- IP source
- user-agent
- session
- compte compromis
- infrastructure intermédiaire

Attention : **attribution technique** ne veut pas dire **attribution judiciaire certaine**.

## 5.4 Reconstitution
Reconstruire le scénario :
- première requête,
- phase de reconnaissance,
- exploitation,
- élévation,
- mouvement latéral,
- exfiltration,
- nettoyage éventuel.

## 5.5 Mesure d’impact
Évaluer :
- comptes concernés,
- données touchées,
- services affectés,
- durée de compromission.

## 5.6 Remédiation
Apporter des correctifs :
- patchs
- règles WAF
- durcissement
- nouvelles alertes
- amélioration des logs

---

# 6. Différence entre analyse défensive, réponse à incident et forensics

## 6.1 Analyse défensive
On surveille, on détecte, on cherche des anomalies.

## 6.2 Réponse à incident
On agit vite :
- isoler,
- bloquer,
- couper un accès,
- réinitialiser un secret,
- désactiver un compte.

## 6.3 Forensics
On agit avec une exigence de **préservation de la preuve** et de **traçabilité**.

Exemple :
- couper brutalement un serveur peut faire perdre des artefacts volatils,
- modifier des logs peut compromettre leur valeur probante.

---

# 7. Principes fondamentaux de la forensics

## 7.1 Intégrité
Les preuves doivent rester intactes ou toute modification doit être documentée.

## 7.2 Traçabilité
Chaque action doit être notée :
- qui a fait quoi,
- quand,
- comment,
- avec quel outil.

## 7.3 Répétabilité
Un autre analyste devrait pouvoir suivre la même méthode et retrouver les mêmes observations.

## 7.4 Minimisation de l’altération
On évite de modifier l’environnement plus que nécessaire.

## 7.5 Horodatage fiable
Les fuseaux horaires, dérives d’horloge et synchronisations NTP sont cruciaux.

## 7.6 Chaîne de possession
Les preuves doivent être suivies de bout en bout.

---

# 8. Cadre légal, éthique et bonnes pratiques

## 8.1 Autorisation
Une investigation doit être autorisée dans son périmètre :
- système interne,
- client,
- mandat,
- cadre pédagogique,
- politique interne.

## 8.2 Respect de la vie privée
Les logs peuvent contenir :
- identifiants,
- cookies,
- tokens,
- adresses IP,
- données personnelles.

Il faut appliquer le principe du **besoin d’en connaître**.

## 8.3 Conservation proportionnée
On ne conserve pas tout indéfiniment sans justification.

## 8.4 Valeur probante
Les procédures doivent être défendables devant :
- audit interne,
- conformité,
- assurance,
- juridique,
- éventuellement justice.

## 8.5 Documentation
Toute hypothèse doit être distinguée d’un fait observé.

### Bonne pratique
Toujours séparer :
- **faits**
- **interprétations**
- **hypothèses**
- **recommandations**

---

# 9. Architecture d’une application web : ce qu’il faut comprendre avant d’enquêter

On ne peut pas faire de bonne web forensics sans comprendre l’architecture.

## 9.1 Architecture simplifiée

Utilisateur → DNS → CDN / WAF → Reverse Proxy / Load Balancer → Serveur Web → Application → Base de données → Services externes

## 9.2 Composants fréquents

- navigateur client
- CDN
- WAF
- Nginx / Apache
- backend (Flask, Django, Node, PHP, Java, etc.)
- Redis / cache
- base SQL / NoSQL
- fournisseur d’identité
- API tierces
- système Linux
- observabilité (SIEM, APM, logs)

## 9.3 Pourquoi c’est essentiel

Une requête malveillante peut laisser des traces à plusieurs niveaux :

- dans le WAF
- dans le reverse proxy
- dans l’access log
- dans le log applicatif
- dans la base
- dans auth.log
- dans le trafic réseau

Si tu ne connais pas l’architecture, tu risques :
- de manquer une source utile,
- de mal interpréter un log,
- de rater la chronologie réelle.

---

# 10. Surface d’attaque d’une application web

Une surface d’attaque web peut inclure :

- pages publiques
- formulaires
- endpoints d’API
- authentification
- upload de fichiers
- paramètres GET/POST
- cookies
- sessions
- WebSockets
- endpoints admin
- interfaces internes exposées par erreur
- mécanismes de reset de mot de passe
- intégrations tierces
- tâches de fond accessibles via HTTP
- dashboards oubliés

## 10.1 Vulnérabilités courantes à connaître

- SQL Injection
- Command Injection
- File Inclusion
- Path Traversal
- XSS
- CSRF
- SSRF
- IDOR / BOLA
- Auth bypass
- Session fixation
- Broken access control
- Upload malveillant
- Deserialization
- Template Injection
- Open redirect
- RCE

Chaque vulnérabilité a des **indicateurs forensiques** différents.

---

# 11. Sources de preuves dans une investigation web

## 11.1 Access logs
Exemple :
- IP source
- date/heure
- méthode HTTP
- URL
- code réponse
- taille
- referer
- user-agent

## 11.2 Error logs
Très utiles pour :
- exceptions
- stack traces
- erreurs backend
- comportements anormaux
- chemins de fichiers exposés
- commandes système échouées

## 11.3 Logs applicatifs
Ils racontent souvent le mieux la logique métier :
- connexion réussie / échouée
- création d’objet
- modification de compte
- génération d’export
- tentative de suppression
- appel d’API interne

## 11.4 Logs d’authentification
`auth.log` sur Linux peut révéler :
- connexions SSH
- élévations de privilèges
- sudo
- accès système liés à l’incident

## 11.5 Logs système
`dmesg`, `syslog`, `journalctl` peuvent révéler :
- crashs
- OOM kills
- chargement de modules
- erreurs noyau
- événements liés aux interfaces réseau

## 11.6 Firewall / WAF logs
Ils montrent :
- connexions bloquées
- ports ciblés
- patterns d’attaque
- décisions de filtrage

## 11.7 Reverse proxy / load balancer logs
Indispensables si l’IP source est réécrite ou si des en-têtes comme `X-Forwarded-For` sont utilisés.

## 11.8 Base de données
Peut révéler :
- comptes modifiés
- dumps
- requêtes anormales
- volumes d’extraction
- timestamps métier

## 11.9 Captures réseau
PCAP, NetFlow, métadonnées TLS, DNS logs, transactions HTTP.

---

# 12. Journaux applicatifs : types, formats, valeur probante

## 12.1 Logs structurés vs non structurés

### Logs non structurés
Exemple :
`Login failed for user admin from 192.0.2.10`

Lisibles, mais difficiles à parser à grande échelle.

### Logs structurés
Exemple JSON :
```json
{
  "timestamp": "2026-04-09T08:13:12Z",
  "level": "warning",
  "event": "login_failed",
  "user": "admin",
  "ip": "192.0.2.10",
  "path": "/login"
}
```

Bien meilleurs pour :
- recherche
- corrélation
- détection
- reporting

## 12.2 Champs à rechercher dans les logs

- timestamp
- timezone
- IP source
- port source
- méthode HTTP
- URI
- code statut
- taille réponse
- temps de réponse
- user-agent
- referer
- identifiant de session
- utilisateur authentifié
- request ID / correlation ID
- host / vhost
- backend cible
- résultat applicatif

## 12.3 Valeur probante d’un log

Un log n’est pas automatiquement “la vérité absolue”.

Il faut se demander :
- Peut-il être falsifié ?
- Est-il local ou centralisé ?
- Est-il horodaté proprement ?
- Est-il complet ?
- A-t-il été altéré par rotation, truncation, redémarrage, anti-forensics ?

---

# 13. Comment analyser les logs d’application web

## 13.1 Approche générale

Toujours procéder en plusieurs passes :

### Passe 1 : vue d’ensemble
- volume global
- période suspecte
- anomalies visibles
- pics de trafic
- codes 4xx / 5xx inhabituels

### Passe 2 : focalisation
- IP suspectes
- endpoints sensibles
- comptes ciblés
- user-agents inhabituels

### Passe 3 : reconstruction
- ordre exact des requêtes
- enchaînement logique
- impact

## 13.2 Questions à se poser

- Quel est le premier signe d’activité malveillante ?
- Y a-t-il eu une phase de scan ?
- Quels endpoints reviennent souvent ?
- Y a-t-il des patterns de fuzzing ?
- Une authentification a-t-elle précédé l’action ?
- Y a-t-il des erreurs applicatives révélatrices ?
- Quel compte a été utilisé ?
- Quel volume de données a été transféré ?

## 13.3 Exemples de patterns suspects

### Énumération
- nombreuses requêtes sur plusieurs endpoints
- séries de 404
- chemins inhabituels

### Bruteforce
- nombreuses tentatives de login
- répétition sur `/login`, `/auth`, `/api/token`
- statuts répétés 401/403/429

### Injection
- caractères spéciaux dans les paramètres
- payloads avec `'`, `"`, `--`, `<script>`, `${`, `../`, `;`, `|`

### Exfiltration
- gros volumes de réponse
- nombreuses pages d’export
- accès répétitifs à des ressources de téléchargement

## 13.4 Chronologie

L’un des livrables les plus importants est une **timeline**.

Exemple :

- 08:11:03 — scan de `/admin`
- 08:11:14 — requête anormale sur `/search?q=' OR 1=1`
- 08:11:15 — erreur SQL dans error log
- 08:11:18 — réponse HTTP 200 sur endpoint d’export
- 08:12:02 — téléchargement massif
- 08:13:40 — connexion SSH suspecte observée dans `auth.log`

---

# 14. Comment tracer l’origine d’une attaque avec les logs

## 14.1 Ce que signifie “origine”

L’origine peut vouloir dire :

- IP source immédiate,
- proxy intermédiaire,
- VPN,
- TOR exit node,
- machine compromise,
- compte applicatif,
- session utilisateur,
- API key utilisée.

## 14.2 Champs clés

Dans les logs, on regarde souvent :

- IP source
- `X-Forwarded-For`
- `X-Real-IP`
- `Forwarded`
- cookie de session
- token API
- identifiant utilisateur
- user-agent
- heure
- enchaînement des requêtes

## 14.3 Limites

L’IP peut être :
- usurpée dans certains contextes réseau,
- réécrite par proxy,
- partagée,
- masquée par NAT,
- issue d’un VPN,
- l’hôte d’un relais.

Donc :
**une IP seule ne suffit jamais à conclure définitivement**.

## 14.4 Corrélation utile

Une attribution technique devient plus crédible si plusieurs éléments convergent :

- même IP
- même user-agent
- même session
- même séquence de navigation
- même fenêtre temporelle
- même compte compromis
- même pattern d’attaque

---

# 15. Corrélation de logs : méthode complète

La corrélation, c’est le cœur de la web forensics.

## 15.1 Pourquoi corréler ?

Un seul log donne une vision partielle.

Exemple :
- l’access log montre une requête,
- l’error log montre une exception,
- l’app log montre le compte ciblé,
- le firewall log montre la même IP quelques minutes avant,
- `auth.log` montre une connexion système juste après.

## 15.2 Exemple de corrélation

### Access log
`203.0.113.25 - - [09/Apr/2026:08:11:14 +0000] "GET /search?q=' OR 1=1 HTTP/1.1" 500 842`

### Error log
`SQL syntax error near OR 1=1`

### App log
`report_export triggered by user=admin`

### Firewall log
`ACCEPT IN=eth0 SRC=203.0.113.25 DST=198.51.100.10 PROTO=TCP SPT=53122 DPT=443`

Tu peux déjà reconstruire :
1. l’IP source,
2. le moment exact,
3. la tentative d’injection,
4. l’erreur SQL,
5. un effet métier associé.

## 15.3 Problèmes fréquents

- fuseaux horaires différents
- horloges désynchronisées
- formats de dates différents
- rotation de logs
- logs incomplets
- reverse proxy qui masque la vraie IP

---

# 16. Analyse réseau dans un contexte forensique web

La web forensics ne se limite pas aux fichiers de logs.

Le réseau peut révéler :
- scans,
- C2,
- exfiltration,
- échanges DNS suspects,
- destinations inhabituelles,
- pics de trafic,
- patterns répétés.

## 16.1 Données réseau utiles

- PCAP
- NetFlow / IPFIX
- journaux firewall
- journaux proxy
- logs DNS
- captures TLS metadata
- statistiques de bande passante

## 16.2 Ce qu’on cherche

- connexions sortantes anormales
- uploads massifs
- répétition sur un même endpoint
- communication vers infrastructure externe rare
- changement soudain de pattern

## 16.3 Exemple

Si l’app génère normalement peu de trafic sortant, mais qu’après une compromission elle contacte :
- plusieurs domaines rares,
- une IP unique à l’étranger,
- avec volume inhabituel,

cela peut indiquer :
- exfiltration,
- téléchargement d’outils,
- callback de malware,
- tunnel applicatif.

---

# 17. Utilisation de Wireshark dans une investigation web

Wireshark sert à inspecter le trafic réseau paquet par paquet.

## 17.1 À quoi il sert en web forensics

- observer des échanges HTTP non chiffrés
- analyser des captures réseau historiques
- identifier des flux entre client et serveur
- retrouver des requêtes, réponses, cookies, erreurs
- étudier la chronologie réseau
- vérifier des hypothèses issues des logs

## 17.2 Limite importante

Avec HTTPS, le contenu est chiffré sauf si :
- on dispose des clés de déchiffrement,
- on a un MITM légal/autorisé,
- le trafic a été capturé dans un contexte contrôlé.

Même sans déchiffrer, Wireshark reste utile pour :
- IP source/destination
- ports
- SNI selon contexte
- taille et fréquence des échanges
- timings
- retransmissions
- anomalies TCP

## 17.3 Filtres Wireshark utiles

- `http`
- `tcp.port == 80`
- `tcp.port == 443`
- `ip.addr == 203.0.113.25`
- `dns`
- `tls`
- `tcp.flags.syn == 1`

## 17.4 Ce qu’on peut extraire

- méthodes HTTP
- URI
- hôtes
- cookies
- contenu de formulaires en HTTP clair
- erreurs de protocole
- uploads de fichiers
- séquences d’échanges

## 17.5 Exemple pédagogique

Un access log montre :
- `POST /login`
- puis `GET /admin/export`

Avec Wireshark tu peux parfois voir :
- le timing précis,
- si plusieurs tentatives de login ont eu lieu,
- si un token/cookie a circulé,
- si l’export a généré une réponse volumineuse.

---

# 18. Utilisation de Burp Suite dans une investigation web

Burp Suite est souvent vu comme un outil d’audit offensif, mais il peut aussi aider en **forensics web**.

## 18.1 Pourquoi Burp est utile

Il permet de :
- reproduire des requêtes observées dans les logs,
- comprendre la logique de l’application,
- vérifier comment un endpoint répond,
- examiner les en-têtes,
- rejouer des scénarios de manière contrôlée,
- comparer comportement attendu et observé.

## 18.2 Utilisations forensiques typiques

### A. Rejouer une requête observée
Tu vois dans un log un `POST /api/export`.
Tu peux reconstruire :
- méthode
- headers
- cookies
- body

Puis le rejouer dans Burp Repeater dans un environnement autorisé.

### B. Comprendre les flux d’authentification
- cookies de session
- redirections
- tokens CSRF
- appels API secondaires

### C. Valider une hypothèse
Exemple :
- un log suggère une IDOR,
- Burp permet de tester si la ressource est accessible avec un autre identifiant.

## 18.3 Limites

Burp ne remplace pas les preuves historiques.
Il sert à :
- **comprendre**,
- **simuler**,
- **reproduire**.

Il ne prouve pas à lui seul que la requête historique a bien eu exactement ce contenu si celui-ci n’a pas été loggé.

---

# 19. Rôle des firewalls Linux, iptables et firewalld

## 19.1 Pourquoi c’est important en forensics web

Le firewall peut fournir :
- la liste des connexions autorisées ou bloquées,
- les ports ciblés,
- l’IP source,
- l’interface réseau,
- des indices de scan ou d’attaque.

## 19.2 iptables

`iptables` est un outil historique de filtrage sous Linux.
Il permet de définir des règles sur :
- source,
- destination,
- ports,
- protocoles,
- états de connexion.

### Exemples de chaînes
- INPUT
- OUTPUT
- FORWARD

### Ce qu’un analyste peut en tirer
- quelles connexions étaient permises,
- quelles règles de logs existaient,
- si des paquets suspects ont été journalisés.

## 19.3 firewalld

`firewalld` est une couche de gestion plus dynamique, souvent utilisée sur certaines distributions modernes.
Il s’appuie sur des concepts comme :
- zones,
- services,
- règles riches.

## 19.4 Différence simplifiée

- **iptables** : niveau plus bas, plus direct, plus “manuel”.
- **firewalld** : gestion plus abstraite, plus dynamique, souvent plus simple à administrer.

## 19.5 Ce que ça change en enquête

Tu dois savoir :
- quel moteur est utilisé,
- où sont stockées les règles,
- où vont les logs,
- si les paquets rejetés sont enregistrés,
- si le firewall local ou en amont a filtré le trafic.

---

# 20. auth.log et dmesg : ce qu’ils apportent à l’enquête

Le projet mentionne deux fichiers : `auth.log` et `dmesg`.

Ils sont très pertinents pour compléter l’analyse d’une compromission web.

## 20.1 `auth.log`

Sous Linux, `auth.log` peut contenir :

- authentifications réussies/échouées,
- connexions SSH,
- `sudo`,
- escalades de privilèges,
- changements liés à PAM,
- comptes système utilisés.

### Pourquoi c’est utile
Une attaque web peut déboucher sur :
- un shell,
- une persistance,
- une connexion SSH,
- l’utilisation de `sudo`,
- la création d’un utilisateur.

### Exemples d’indices
- `Failed password`
- `Accepted password`
- `Accepted publickey`
- `sudo:`
- `session opened for user root`

## 20.2 `dmesg`

`dmesg` contient les messages du noyau.

Il peut révéler :
- événements liés au boot,
- détection matériel,
- erreurs réseau,
- crashs,
- kills mémoire,
- anomalies système,
- messages liés à certains modules.

### Pourquoi c’est utile en web forensics
Si une attaque a provoqué :
- surcharge,
- crash applicatif,
- OOM kill,
- comportement réseau étrange,

`dmesg` peut contenir des traces complémentaires.

### Exemple
Une exploitation déclenche un processus lourd ou un fork bomb :
- le noyau peut tuer un processus par manque de mémoire,
- `dmesg` peut montrer quel processus a été tué.

---

# 21. Méthodologie complète d’investigation web

Voici une méthode solide et professionnelle.

## 21.1 Phase 1 — Préparation

- définir le périmètre
- confirmer les autorisations
- identifier les systèmes concernés
- figer les objectifs
- noter date, heure, fuseau

## 21.2 Phase 2 — Préservation

- sauvegarder les logs
- calculer des empreintes si nécessaire
- noter l’emplacement exact des preuves
- éviter de redémarrer ou modifier inutilement

## 21.3 Phase 3 — Collecte

Récupérer :
- access logs
- error logs
- app logs
- auth.log
- dmesg
- firewall logs
- reverse proxy logs
- éventuels PCAP
- exports DB pertinents
- configuration du serveur

## 21.4 Phase 4 — Normalisation

- unifier les fuseaux horaires
- harmoniser les formats de timestamp
- trier chronologiquement
- isoler la fenêtre temporelle pertinente

## 21.5 Phase 5 — Analyse

- repérer les anomalies
- identifier l’IP ou les IP pertinentes
- suivre la séquence d’actions
- relever les comptes ciblés
- lister les ressources touchées
- mesurer l’impact

## 21.6 Phase 6 — Corrélation

Mettre en correspondance :
- réseau
- web
- application
- système
- authentification
- base

## 21.7 Phase 7 — Validation

- vérifier chaque hypothèse
- éliminer les faux positifs
- distinguer fait et supposition
- tester en environnement autorisé si nécessaire

## 21.8 Phase 8 — Rapport

Inclure :
- contexte
- périmètre
- méthode
- sources de preuves
- chronologie
- analyse
- impact
- limites
- recommandations

---

# 22. Cas d’usage : reconstitution d’une attaque

Prenons un scénario pédagogique.

## 22.1 Contexte

Une application web interne propose :
- `/login`
- `/search`
- `/admin/export`
- `/api/users`

Un pic inhabituel est détecté.

## 22.2 Indices dans l’access log

- nombreuses requêtes 404 sur `/admin`, `/backup`, `/old`
- puis accès à `/search?q=' OR 1=1`
- puis plusieurs requêtes réussies sur `/admin/export`

## 22.3 Indices dans l’error log

- erreur SQL syntax
- stack trace partielle
- mention d’une requête construite dynamiquement

## 22.4 Indices dans l’app log

- génération d’un export
- exécution pour un compte admin
- absence d’action utilisateur visible dans l’interface

## 22.5 Indices dans `auth.log`

- quelques minutes après, une connexion SSH depuis la même IP

## 22.6 Hypothèse

1. reconnaissance web
2. injection SQL
3. accès à une fonction d’export
4. possible récupération d’identifiants
5. accès système secondaire

## 22.7 Conclusion

L’incident ne concerne plus uniquement l’application :
il faut étendre l’enquête au système complet.

---

# 23. Vulnérabilités web courantes et indicateurs forensiques

## 23.1 SQL Injection

### Indices
- erreurs SQL
- paramètres suspects
- caractères `'`, `"`, `--`
- réponses 500
- export ou accès anormal à données

## 23.2 XSS

### Indices
- payloads HTML/JS
- valeurs stockées contenant `<script>`
- alertes CSP
- comportement anormal côté client

## 23.3 Path Traversal / File Inclusion

### Indices
- `../`
- `%2e%2e/`
- accès à fichiers système
- erreurs “file not found”
- chemins révélés dans les logs

## 23.4 Bruteforce

### Indices
- grand nombre de 401/403
- répétition d’un endpoint d’auth
- plusieurs noms d’utilisateur
- cadence régulière

## 23.5 IDOR / Broken Access Control

### Indices
- requêtes séquentielles sur des IDs
- même session sur plusieurs ressources d’autres comptes
- codes 200 là où un 403 serait attendu

## 23.6 Upload malveillant

### Indices
- upload de fichiers inhabituels
- extensions suspectes
- accès ultérieur au fichier uploadé
- exécution ou inclusion côté serveur

## 23.7 Command Injection / RCE

### Indices
- paramètres contenant opérateurs shell
- erreurs système
- processus enfants anormaux
- connexions sortantes inattendues
- traces dans `auth.log` ou `dmesg`

---

# 24. Conservation des preuves et chaîne de possession

## 24.1 Pourquoi c’est critique

Sans chaîne de possession claire, une preuve peut être contestée.

## 24.2 Ce qu’il faut documenter

- quoi a été collecté
- où
- quand
- par qui
- méthode de collecte
- hash éventuel
- emplacement de stockage
- accès accordés

## 24.3 Exemples

- copie de `/var/log/nginx/access.log`
- export de `journalctl`
- capture réseau `incident_2026-04-09.pcap`
- archive des logs applicatifs

## 24.4 Bonnes pratiques

- travailler sur des copies
- limiter les accès
- horodater les actions
- éviter les manipulations inutiles
- utiliser des noms de fichiers explicites

---

# 25. Rédaction d’un rapport forensique clair et professionnel

Un bon rapport ne doit pas être seulement technique.
Il doit être **compréhensible, structuré et défendable**.

## 25.1 Structure recommandée

### A. Résumé exécutif
Pour management / non-technique :
- incident
- impact
- gravité
- actions urgentes

### B. Contexte
- système concerné
- date de détection
- périmètre

### C. Sources de preuves
- logs
- captures
- systèmes
- fichiers analysés

### D. Méthodologie
- comment l’analyse a été menée
- outils utilisés
- limites

### E. Chronologie
- suite ordonnée des événements

### F. Résultats techniques
- IP
- endpoints
- comptes
- actions observées
- exfiltration / modifications

### G. Impact
- données touchées
- services affectés
- durée

### H. Recommandations
- techniques
- organisationnelles
- détection
- prévention

## 25.2 Règles de rédaction

- écrire clairement
- séparer faits et hypothèses
- éviter les affirmations non prouvées
- fournir extraits utiles sans noyer le lecteur
- garder la chronologie cohérente

## 25.3 Exemple de formulation rigoureuse

Bonne formulation :
> Les logs d’accès montrent 47 requêtes depuis l’IP 203.0.113.25 entre 08:11:03 et 08:14:52, incluant plusieurs tentatives sur `/admin` et une requête contenant un motif compatible avec une tentative d’injection SQL.

Mauvaise formulation :
> Un hacker a piraté le serveur via une SQLi.

Pourquoi la première est meilleure :
- elle décrit des faits,
- elle reste prudente,
- elle ne sur-interprète pas.

---

# 26. Erreurs classiques à éviter

## 26.1 Se focaliser sur un seul log
Toujours croiser les sources.

## 26.2 Oublier les fuseaux horaires
Erreur très fréquente.

## 26.3 Confondre corrélation et preuve absolue
Même IP ≠ même personne certaine.

## 26.4 Modifier l’environnement trop tôt
Contenir oui, détruire des preuves non.

## 26.5 Ne pas distinguer fait et hypothèse
C’est l’erreur la plus dangereuse dans un rapport.

## 26.6 Négliger les logs “secondaires”
`auth.log`, `dmesg`, firewall, DNS peuvent être décisifs.

## 26.7 Oublier l’architecture
Sans savoir où passent les requêtes, on interprète mal les traces.

---

# 27. Checklist opérationnelle

## Avant l’analyse
- [ ] périmètre validé
- [ ] autorisation confirmée
- [ ] fuseau horaire noté
- [ ] système(s) identifiés

## Collecte
- [ ] access logs
- [ ] error logs
- [ ] logs applicatifs
- [ ] `auth.log`
- [ ] `dmesg`
- [ ] firewall / WAF logs
- [ ] reverse proxy logs
- [ ] captures réseau si disponibles
- [ ] exports DB utiles

## Analyse
- [ ] fenêtre temporelle définie
- [ ] IP suspectes identifiées
- [ ] endpoints sensibles isolés
- [ ] comptes utilisateurs corrélés
- [ ] erreurs techniques relevées
- [ ] timeline construite
- [ ] impact estimé

## Rapport
- [ ] faits séparés des hypothèses
- [ ] preuves citées
- [ ] limites précisées
- [ ] recommandations réalistes

---

# 28. Mini glossaire

## Access Log
Journal des requêtes reçues par le serveur web.

## Error Log
Journal des erreurs du serveur ou de l’application.

## Artifact
Élément observable utile à l’enquête.

## Chain of Custody
Suivi documenté de la preuve.

## DFIR
Digital Forensics and Incident Response.

## IOC
Indicator of Compromise.

## Timeline
Chronologie reconstituée des événements.

## WAF
Web Application Firewall.

## PCAP
Fichier de capture de paquets réseau.

## Correlation ID
Identifiant permettant de relier plusieurs événements ou logs.

---

# 29. Conseils pratiques pour ton projet avec `auth.log` et `dmesg`

Comme ton projet mentionne explicitement ces fichiers, voici comment les exploiter intelligemment.

## 29.1 Avec `auth.log`
Cherche :

- tentatives de connexion SSH
- connexions réussies suspectes
- utilisation de `sudo`
- sessions ouvertes pour des comptes privilégiés
- heures proches de l’incident web

Questions à poser :
- Y a-t-il une connexion système juste après l’attaque web ?
- L’IP est-elle la même que celle vue dans les access logs ?
- Un compte inattendu a-t-il été utilisé ?

## 29.2 Avec `dmesg`
Cherche :

- erreurs mémoire
- crashs
- messages liés au réseau
- événements système inhabituels
- processus tués

Questions à poser :
- L’incident a-t-il dégradé le système ?
- Une surcharge a-t-elle provoqué des kills ?
- Un comportement anormal a-t-il été observé au niveau noyau ?

## 29.3 Corrélation simple à faire
Construis une petite timeline :

1. requête web suspecte
2. erreur serveur
3. événement applicatif
4. éventuelle connexion SSH
5. éventuel message noyau

C’est souvent là que l’incident “prend forme”.

---

# 30. Outils utiles en pratique

Même si le projet cite surtout Wireshark, Burp Suite, iptables et firewalld, voici une boîte à outils utile.

## Analyse de logs
- `grep`
- `awk`
- `sed`
- `sort`
- `uniq`
- `cut`
- `less`
- `zgrep`
- `journalctl`

## Réseau
- Wireshark
- tcpdump
- tshark
- ss
- netstat

## Web
- Burp Suite
- navigateur + DevTools
- curl

## Intégrité
- `sha256sum`
- `md5sum` (moins recommandé pour l’intégrité moderne, mais parfois encore rencontré)

---

# 31. Exemple de démarche analytique simple

Imaginons que tu reçoives :

- `auth.log`
- `dmesg`
- un access log web

Tu peux raisonner ainsi :

## Étape 1
Identifier la fenêtre temporelle suspecte.

## Étape 2
Dans l’access log :
- repérer IP suspecte,
- repérer endpoints,
- repérer erreurs.

## Étape 3
Dans `auth.log` :
- chercher la même IP,
- ou des événements proches en temps.

## Étape 4
Dans `dmesg` :
- vérifier s’il y a eu crash, OOM, surcharge, anomalie.

## Étape 5
Créer une chronologie.

## Étape 6
Conclure avec prudence :
- ce qui est prouvé,
- ce qui est probable,
- ce qui reste inconnu.

---

# 32. Ce qu’il faut savoir expliquer sans Google

À la fin du module, tu dois pouvoir expliquer clairement :

## Qu’est-ce que la Digital Forensics ?
La discipline qui permet de collecter, préserver, analyser et présenter des preuves numériques de manière fiable.

## Quels sont les concepts clés de la Web Application Forensics ?
Logs, traces HTTP, corrélation, chronologie, intégrité des preuves, analyse réseau, logs système, contexte applicatif.

## Comment analyser des logs web ?
En identifiant les anomalies, les IP, les endpoints, les codes de statut, les erreurs, puis en reconstruisant une timeline.

## Comment tracer l’origine d’une attaque ?
En corrélant IP, en-têtes, sessions, comptes, user-agents, logs réseau et événements système.

## Comment utiliser Wireshark et Burp Suite ?
Wireshark pour analyser le trafic réseau, Burp pour comprendre et reproduire des requêtes HTTP de manière contrôlée.

## Quels sont les cadres légaux et bonnes pratiques ?
Autorisation, minimisation, traçabilité, chaîne de possession, confidentialité, distinction entre faits et hypothèses.

## Pourquoi comprendre l’architecture web est indispensable ?
Parce qu’une requête traverse plusieurs couches et laisse des traces différentes dans chacune.

---

# 33. Conclusion

La **Web Application Forensics** n’est pas juste une lecture de logs.
C’est une discipline de **reconstruction méthodique**.

Un bon analyste doit savoir :

- comprendre l’architecture d’une application,
- identifier les bonnes sources de preuves,
- corréler plusieurs couches techniques,
- reconstituer une chronologie,
- mesurer l’impact,
- rédiger un rapport clair,
- rester rigoureux et prudent dans ses conclusions.

Le plus important à retenir est simple :

> Un log seul raconte rarement toute l’histoire.  
> Une investigation réussie repose sur la corrélation, la méthode et la rigueur.

---
## Bonus — Plan de révision ultra rapide

### À retenir absolument
- définition de la digital forensics
- différence entre IR et forensics
- rôle des access logs, error logs, app logs
- intérêt de `auth.log` et `dmesg`
- importance de la chronologie
- corrélation multi-sources
- usage de Wireshark et Burp
- rôle du firewall
- chaîne de possession
- rapport structuré

### Réponse courte type examen
La Web Application Forensics consiste à enquêter sur un incident touchant une application web en analysant les traces laissées dans les logs HTTP, applicatifs, système et réseau afin de reconstituer la chronologie, identifier l’origine apparente, comprendre la technique utilisée, mesurer l’impact et documenter les preuves de façon rigoureuse.

---
```
