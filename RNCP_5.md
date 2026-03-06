# 🎓 RNCP 5 DWWM — COURS ULTIME (COMPLET & PÉDAGOGIQUE)  
### Tout ce qu’un candidat doit savoir + exemples concrets **Smart Trading Bot / HBnB / PSG Académie**

> **Objectif** : te rendre **inattaquable** à l’entretien RNCP 5 DWWM.  
> Ce cours couvre **TOUTES** les compétences attendues (front, back, BDD, sécurité, tests, déploiement, méthodo) + les transverses (résolution de problèmes, communication, veille).  
> **Chaque notion** est expliquée + illustrée par **tes projets** :  
> - **Smart Trading Bot** (dossier projet principal)  
> - **HBnB** (preuve BDD relationnelle + API sécurisée)  
> - **PSG Académie** (projet multi-services + Docker + React/FastAPI)

---

# 0) Comprendre l’examen RNCP 5 (ce que le jury fait vraiment)

## 0.1 Les supports
Le jury s’appuie sur :  
1) **Dossier projet** (preuve principale : ce que tu as construit)  
2) **Support oral** (slides)  
3) **Dossier professionnel** (preuves complémentaires)  
4) **Entretien technique** (questions sur tes choix + compétences non couvertes)  
5) **Entretien final** (vision globale du métier, posture, limites, améliorations)

## 0.2 La logique du jury
Le jury coche des compétences. Il cherche :
- Compréhension (tu sais expliquer, pas réciter)
- Argumentation (tu sais justifier des choix)
- Maîtrise minimale de la sécurité
- Capacité à relier UI ↔ API ↔ DB ↔ sécurité ↔ tests ↔ déploiement

## 0.3 Méthode de réponse “jury” (à utiliser partout)
Structure type :
1) **Définition** (1 phrase)  
2) **Pourquoi** (enjeu)  
3) **Comment dans mon projet** (preuve concrète)  
4) **Limite / amélioration** (1 phrase)

Exemple CORS :
> “CORS est un mécanisme de sécurité du navigateur qui contrôle les requêtes cross-origin. Dans PSG, le front et l’API n’ont pas le même origin, donc le navigateur bloque sans CORS. Je limite les origins autorisées. En prod, je restreins au domaine exact + HTTPS.”

---

# 1) Référentiel RNCP 5 DWWM : les 2 activités-types

## 1.1 Activité-type 1 — Développer la partie front-end d’une application web ou web mobile **sécurisée**
Tu dois savoir :
- Installer/configurer ton environnement (outils)
- Maquetter (web + mobile)
- Réaliser interfaces statiques (HTML/CSS)
- Développer interfaces dynamiques (JS/React + API)
- Appliquer recommandations sécurité front
- Tester (jeu d’essai fonctionnel)
- Documenter (au minimum ce que tu as fait)

### Exemples dans tes projets
- **Smart Trading Bot** : wireframes + responsive + design system + JS fetch/auto-refresh + statut + jeu d’essai front  
- **HBnB** : front vanilla (HTML/CSS/JS), fetch, affichage conditionnel si connecté (JWT)  
- **PSG** : React + formulaires + dashboards + consommation d’API

## 1.2 Activité-type 2 — Développer la partie back-end d’une application web ou web mobile **sécurisée**
Tu dois savoir :
- BDD relationnelle (modèle + contraintes + scripts + sauvegarde/restauration)
- Accès aux données (SQL + éventuellement NoSQL / services tiers)
- Composants métier serveur (logique)
- Sécurité back (auth, secrets, validation, logs, erreurs)
- Tests (jeu d’essai, unit/integration au minimum expliqué)
- Déploiement (documentation : comment le lancer en prod)

### Exemples dans tes projets
- **Smart Trading Bot** : client Bitget (HMAC), API interne Flask, Risk Manager, logs, erreurs, tests “entrée/attendu/obtenu”  
- **HBnB** : Flask RESTx + Swagger + JWT/Bcrypt + CRUD + SQLAlchemy + schema.sql  
- **PSG** : FastAPI + PostgreSQL + Docker Compose + Nginx reverse proxy

---

# 2) Installer et configurer son environnement (compétence transversale importante)

## 2.1 Ce que le jury attend
- Tu sais expliquer ton environnement de dev : OS, IDE, dépendances, virtualenv, Docker, Git
- Tu sais reproduire un setup sur une autre machine
- Tu sais gérer les variables d’environnement (.env non versionné)

## 2.2 Exemple Smart Trading Bot
- Python + venv
- Flask pour API interne
- Dépendances crypto/API (requests, hmac, etc.)
- `.env` pour clés API (non commit)
- Logs + structure projet en modules

## 2.3 Exemple PSG
- Docker Compose orchestre DB + backends + frontend + Nginx
- Healthcheck DB + migrations/seed au démarrage

✅ **Question jury typique :** “Si je clone ton repo sur une machine vierge, je fais quoi ?”  
Réponse attendue : “installer X, créer venv, pip install, config .env, lancer flask/compose…”

---

# 3) AT1 — Maquetter une interface (web + mobile)

## 3.1 Concepts
- **Benchmark** : analyser des UI existantes proches du besoin
- **Wireframe** : plan d’écran sans style final
- **Hiérarchie d’info** : ce qui doit être vu “en 3 secondes”
- **Responsive** : adaptation à la taille d’écran
- **Design system** : variables + composants réutilisables

## 3.2 Exemple Smart Trading Bot (dossier projet)
- Benchmark d’interfaces de brokers (Bitget/Binance)
- Wireframes desktop + mobile
- Itérations via DevTools : empilement, tables scrollables, contrastes
- Design system CSS : variables (couleurs/espacements) + composants (cards, tables, badges)

✅ **Questions jury** :
- Pourquoi ce choix de structure d’écran ?
- Comment tu prouves l’adaptation mobile ?

---

# 4) AT1 — Réaliser une interface statique (HTML/CSS)

## 4.1 HTML : ce qu’il faut savoir
- Sémantique : header/main/section/footer
- Formulaires : label, input types, required
- Accessibilité : alt, labels, focus, contraste

## 4.2 CSS : ce qu’il faut savoir
- Flexbox / Grid (layout)
- Typographie, contrastes
- Responsive : breakpoints, fluidité, overflow, zones tactiles
- Design system : variables CSS `:root { --color... }`

## 4.3 Exemple HBnB (DP)
- Pages HTML (login, liste, détail)
- CSS simple mais lisible
- JS interagit avec DOM

✅ **Piège** : “responsive = meta viewport” → faux. Il faut décrire des décisions de layout.

---

# 5) AT1 — Développer la partie dynamique (JS/React)

## 5.1 JS Vanilla : notions indispensables
- `fetch()` + JSON
- DOM : `textContent` (préféré) vs `innerHTML` (risque XSS)
- Gestion d’erreurs : try/catch, status HTTP, timeouts
- Auto-refresh : setInterval + bouton refresh
- États UI : loading / error / connected

### Exemple Smart Trading Bot
- Le front appelle **ton API interne Flask** (pas Bitget directement)
- L’API interne appelle Bitget et renvoie une réponse normalisée
- Le front met à jour KPI + tables sans reload
- En cas d’erreur : statut “Disconnected”, UI reste stable

## 5.2 React : notions indispensables
- Composants, props, state
- `useState`, `useEffect`
- État local vs global (Context/Redux)
- Gestion loading/error
- Formulaires contrôlés (validation)
- Séparation : UI / services API

### Exemple PSG
- UI React (formulaire métriques + dashboard)
- Axios pour appels API
- Affichage dynamique des données

✅ **Question jury** : “Quelle différence entre état local et global ?”  
- Local : au composant (useState)  
- Global : partagé (Context/store) pour auth/données globales

---

# 6) Sécurité front-end (obligatoire RNCP)

## 6.1 XSS (Cross-Site Scripting)
### Définition
XSS = injection permettant d’exécuter du JavaScript dans le navigateur de la victime.

### Types
- Stored (stocké en base)
- Reflected (renvoyé immédiatement)
- DOM-based (manipulation DOM dangereuse)

### Prévention
- Échapper les sorties (render texte, pas HTML)
- Utiliser `textContent`, éviter `innerHTML`
- En React : éviter `dangerouslySetInnerHTML`
- CSP en production
- Cookies HttpOnly (si cookies)

### Exemple HBnB
- Une zone “review” peut être une surface d’attaque si elle affiche du contenu non échappé.
- Tu dois dire : “j’affiche en texte, pas en HTML, et je valide côté serveur.”

## 6.2 Exposition de secrets
- Jamais de clé API dans le front
- Tokens : attention au stockage (localStorage vulnérable XSS)
- En prod : préférer cookies HttpOnly + Secure (selon architecture)

### Exemple Smart Trading Bot
- Clés Bitget uniquement côté serveur + `.env` non versionné
- Le front ne parle qu’à l’API interne

---

# 7) AT2 — API REST (fondamentaux indispensables)

## 7.1 REST : définitions
- API REST = ressources accessibles via endpoints + méthodes HTTP
- Stateless : chaque requête contient ce qu’il faut (auth via token)

## 7.2 Méthodes
- GET : lire
- POST : créer
- PUT : remplacer entièrement
- PATCH : modifier partiellement
- DELETE : supprimer

## 7.3 Idempotence
- GET idempotent
- PUT idempotent (si même payload)
- POST souvent non idempotent

## 7.4 Codes HTTP (à savoir sans hésiter)
- 200 OK
- 201 Created
- 400 Bad Request
- 401 Unauthorized (pas authentifié / token invalide)
- 403 Forbidden (auth mais pas autorisé)
- 404 Not Found
- 409 Conflict
- 500 Server Error

### Exemple HBnB
- Routes CRUD documentées Swagger
- JWT pour protéger création/modification

---

# 8) AT2 — Base de données relationnelle (cours complet)

## 8.1 Concepts à maîtriser
- Table, colonne, ligne
- PK (clé primaire)
- FK (clé étrangère)
- UNIQUE (unicité)
- CHECK (règle de validité)
- Index (accélère recherche, coût à l’écriture)

## 8.2 Relations
- 1–N : un user possède plusieurs places
- N–N : places ↔ amenities via table pivot

### Exemple HBnB
- `place_amenity` = table d’association pour N–N

## 8.3 Intégrité référentielle vs métier
- Référentielle : FK pointe vers une ligne existante
- Métier : règle business (rating 1..5, pas d’auto-review)

## 8.4 ON DELETE CASCADE
- Supprime les enfants quand parent supprimé
- Alternatives : RESTRICT / SET NULL selon le besoin

✅ **Question jury fréquente :** “Si je supprime un user, que deviennent ses places/reviews ?”  
Réponse : dépend des FK et cascades définies (tu expliques l’effet et pourquoi).

## 8.5 Sauvegarde / restauration (attendu RNCP)
- Backup : `pg_dump` (Postgres) / dump SQLite
- Restore : `psql` / import
- Pourquoi : sécurité et continuité

### Exemple PSG
- DB Postgres avec volumes Docker → persistance
- Sauvegarde possible via dump du volume

---

# 9) AT2 — Accès aux données (SQL / NoSQL / services tiers)

## 9.1 Accès SQL (HBnB)
- ORM SQLAlchemy : requêtes paramétrées
- Repositories / couche d’accès (si architecture)
- Gestion des exceptions

## 9.2 Accès via service tiers (Smart Trading Bot)
- API Bitget : endpoints publics (non signés) + privés (signés)
- Signature HMAC : intégrité/authentification requête
- Normalisation des réponses pour le front

## 9.3 SQL Injection (à expliquer parfaitement)
### Définition
SQLi = injection de SQL via une entrée non contrôlée pour lire/modifier des données.

### Prévention (3 mesures)
- requêtes paramétrées / ORM (pas de concat)
- validation serveur (types, bornes, whitelist)
- moindre privilège DB + messages d’erreurs non bavards

⚠️ **À ne jamais dire** : “ORM = impossible d’être vulnérable”.  
À dire : “ORM réduit fortement le risque si utilisé correctement ; le SQL brut concaténé reste dangereux.”

---

# 10) AT2 — Composants métier côté serveur (logique)

## 10.1 Ce que le jury attend
- Une logique métier claire, testable, justifiée
- Règles explicites
- Gestion des cas limites
- Traces/logs

## 10.2 Exemple Smart Trading Bot : Risk Manager
Rôle : garde-fou avant exécution
- max positions
- exposition par symbole
- notional minimum
- levier maximum
- risk/trade (budget de risque)
- SL/TP imposés
- refus motivé (raison claire)

✅ **Question jury** : “Pourquoi ce composant est côté serveur ?”  
Réponse : parce que c’est une règle de sécurité, et le client ne doit pas être de confiance.

---

# 11) Sécurité back-end (obligatoire RNCP)

## 11.1 Authentification vs autorisation
- Authentification : qui es-tu ? (login, token)
- Autorisation : as-tu le droit ? (roles, ownership)

### Exemple HBnB
- JWT protège la création de reviews
- Ownership : seul l’auteur peut modifier/supprimer sa review, sinon 403

## 11.2 JWT (expliquer sans se tromper)
- Format : `header.payload.signature`
- Souvent **signé**, pas chiffré (lisible, mais infalsifiable)
- Expiration + claims

## 11.3 Hash de mots de passe (Bcrypt)
- Hash adaptatif
- Non réversible
- Protège contre brute force (plus lent)

### Exemple HBnB
- passwords hashés avec Bcrypt, jamais en clair

## 11.4 Gestion d’erreurs API externes (Bitget)
Cas à savoir :
- 401 : stop (auth invalide), log, UI disconnected
- 429 : backoff + retry limité
- timeout : retry limité
- 500 : fallback + log
**Jamais de retry infini.**

## 11.5 Logs (sécurité + debug)
- Loguer : statuts, ids, raisons, timestamps
- Ne jamais loguer : clés, signatures, headers sensibles

---

# 12) Docker / Nginx / Architecture (RNCP moderne)

## 12.1 Docker : notions indispensables
- Image vs container
- Volume DB = persistance
- Network Docker
- Healthcheck = service prêt
- depends_on service_healthy = attendre DB prête

### Exemple PSG
- 2 DB Postgres + 2 backends + front + Nginx

## 12.2 Nginx : réponse jury parfaite (3 fonctions)
1) Point d’entrée unique (80/443)  
2) Routage vers les services (proxy_pass)  
   - `/api/physical` → backend catapult  
   - `/api/tactical` → backend veo  
   - `/` → frontend  
3) Couche production : HTTPS, headers sécurité, compression, rate limit (optionnel)

⚠️ Ne dis pas “priorise le flux” : dis “reverse proxy + routage + terminaison TLS”.

---

# 13) Tests & jeux d’essai (attendu RNCP)

## 13.1 Pourquoi le format “Entrée / Attendu / Obtenu” est excellent
- Lisible pour jury
- Couvre nominal + limites + erreurs
- Montre une démarche de résolution de problème

## 13.2 Exemples à savoir présenter à l’oral
### Smart Trading Bot — Front
- Entrée : API renvoie []  
- Attendu : pas de crash, tableau vide, statut connected  
- Obtenu : conforme

### Smart Trading Bot — Back (Risk Manager)
- Entrée : 5 positions ouvertes (limite atteinte)  
- Attendu : ordre refusé + raison  
- Obtenu : refus loggué

## 13.3 Tests unitaires vs intégration (à connaître)
- Unitaire : teste une fonction/classe isolée (Risk Manager)
- Intégration : teste interaction composants (API → client Bitget → réponse)

---

# 14) Déploiement & documentation (compétence RNCP)

## 14.1 Déploiement VPS (réponse en 4 étapes)
1) Préparer serveur : firewall + SSH clés  
2) Déployer code : git clone/scp + `.env` serveur  
3) Lancer : docker compose up -d (restart policy)  
4) Exposer proprement : Nginx + HTTPS (Let’s Encrypt)

## 14.2 Points sécurité indispensables
- Secrets hors repo (env/secrets manager)
- HTTPS
- logs propres
- restrictions CORS
- principe moindre privilège

---

# 15) Veille (la vraie, pas juste monitoring)

## 15.1 Définition
Veille = suivre les vulnérabilités/bonnes pratiques (OWASP, CVE, dépendances), et appliquer.

## 15.2 Ce que tu dois citer à l’oral
- OWASP Top 10 (XSS, Injection, Auth…)
- alertes dépendances (GitHub advisories, changelogs)
- bonnes pratiques secrets/logs

## 15.3 Exemple d’application dans tes projets
- Bot : secrets côté serveur + pas de leak dans logs
- PSG : limiter origins CORS en prod
- HBnB : JWT + Bcrypt + ownership

---

# 16) Scalabilité / performances (réponses réalistes)

## Question : “10 000 utilisateurs arrivent, que se passe-t-il ?”
Réponse pro :
- Aujourd’hui : risque saturation (CPU, DB, workers)
- Améliorations : scaling horizontal, workers (gunicorn/uvicorn), cache (Redis), index DB, CDN, load balancing

⚠️ “Grâce à Nginx ça va” = faux. Nginx aide, mais pas suffisant.

---

# 17) Banque de questions “jury” (100% RNCP) + réponses attendues (modèles)

## Architecture (10)
1) Présente ton projet en 2 min (besoin → solution → flux → sécurité)  
2) API externe vs API interne : différence + pourquoi  
3) Rôle du Risk Manager (garde-fou)  
4) Rôle de Nginx (3 fonctions)  
5) Limites actuelles + amélioration prioritaire  
6) Pourquoi logs ? quoi loguer / ne pas loguer  
7) Qu’est-ce qu’un reverse proxy ?  
8) CORS : définition correcte  
9) Pourquoi pas Figma ? démarche alternative  
10) Comment tu passerais en prod ?

## REST/HTTP (10)
11) PUT vs PATCH  
12) 401 vs 403  
13) Idempotence + exemple  
14) 409 conflict + exemple  
15) 400 vs 422 (si on te le demande)  
16) Pagination : pourquoi  
17) Stateless : définition  
18) Versioning d’API : pourquoi  
19) Timeout : gestion  
20) Rate limiting : gestion

## BDD (10)
21) PK/FK/UNIQUE/CHECK (ex HBnB)  
22) Table pivot : pourquoi  
23) Intégrité référentielle vs métier  
24) ON DELETE CASCADE : effets  
25) Index : intérêt  
26) Migration vs seed  
27) Backup/restore : pourquoi  
28) Normalisation : idée générale  
29) Transactions : idée générale  
30) Schéma conceptuel vs physique

## Sécurité (15)
31) SQLi : définition + 3 protections  
32) XSS : définition + 2 protections  
33) JWT : structure + signature  
34) Stockage JWT : risques localStorage  
35) Bcrypt : pourquoi  
36) Secrets : comment en prod  
37) Logs : éviter fuites  
38) OWASP Top 10 : citer 3 items  
39) CSRF : définition simple  
40) Principe moindre privilège  
41) Validation serveur : pourquoi  
42) Erreurs : pourquoi éviter détails  
43) Headers sécurité : CSP, HSTS (si on te le demande)  
44) CORS : limiter origins  
45) Rate limiting brute force

## Docker/DevOps (10)
46) Image vs container  
47) Volume Postgres : pourquoi  
48) Healthcheck : pourquoi  
49) depends_on healthy : pourquoi  
50) Réseau docker : idée  
51) Environnement dev vs prod  
52) Nginx TLS : rôle  
53) Logs centralisés : idée  
54) CI/CD : idée  
55) Monitoring : idée

---

# 18) Plan d’entraînement (30 minutes/jour, 10 jours)

- J1 : pitch 2 min + architecture bot  
- J2 : BDD HBnB (relations + contraintes + cascade)  
- J3 : REST (PUT/PATCH, 401/403, idempotence)  
- J4 : sécurité (SQLi/XSS/JWT)  
- J5 : Docker + Nginx (PSG)  
- J6 : erreurs API externes (401/429/timeout/500)  
- J7 : tests (entrée/attendu/obtenu)  
- J8 : déploiement VPS + HTTPS + secrets  
- J9 : veille OWASP + dépendances  
- J10 : simulation jury (30 questions)

---

# ✅ Check-list finale “je suis prêt” (si tu coches tout → tu passes)

- [ ] Pitch 2 minutes fluide (besoin → solution → flux → sécurité)  
- [ ] Explique Risk Manager + 1 exemple (clamp/refus)  
- [ ] Explique HBnB BDD : PK/FK/UNIQUE/CHECK + N-N + cascade  
- [ ] Explique PUT vs PATCH + idempotence  
- [ ] Explique 401 vs 403  
- [ ] Explique CORS correctement  
- [ ] Explique Nginx (3 fonctions)  
- [ ] Explique Docker volume + healthcheck  
- [ ] Explique SQLi + XSS (sans “impossible”)  
- [ ] Donne 2 sujets de veille + 1 application concrète  
- [ ] Donne un plan de déploiement VPS en 4 étapes  
- [ ] Sais proposer 1 limite + 1 amélioration réaliste

---

## Si tu veux, on reprend la simulation “une question à la fois”
Je te pose une question, tu réponds, je coche ✅/⚠️/❌ jusqu’à ce que tu dises stop — et à la fin je te donne une note “prêt / pas prêt” + plan de rattrapage.
