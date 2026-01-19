# OWASP Top 10 (2021) — Cours ultra complet & détaillé (WebSec 0x01 / Kali)

> **But** : être capable d’expliquer **sans Google** ce qu’est l’OWASP Top 10, et **pour chaque risque** :  
> 1) **comment ça arrive**, 2) **pourquoi c’est grave**, 3) **comment le repérer en audit/lab**, 4) **comment le prévenir en dev**.  
>
> ⚠️ Toute partie “pentest” doit être faite **uniquement** sur des environnements autorisés (lab Holberton, machines d’entraînement, Juice Shop local, etc.).

---

## Table des matières

- [1. OWASP Top 10 : définition, utilité, limites](#1-owasp-top-10--définition-utilité-limites)
- [2. Fondations indispensables (HTTP, cookies, sessions, SOP/CORS)](#2-fondations-indispensables-http-cookies-sessions-sopcors)
- [3. Lire un risque OWASP comme un pro (impact, probabilité, chaîne d’attaque)](#3-lire-un-risque-owasp-comme-un-pro-impact-probabilité-chaîne-dattaque)
- [4. La liste OWASP Top 10:2021](#4-la-liste-owasp-top-102021)
- [A01 Broken Access Control](#a01-broken-access-control)
- [A02 Cryptographic Failures (ex Sensitive Data Exposure)](#a02-cryptographic-failures-ex-sensitive-data-exposure)
- [A03 Injection](#a03-injection)
- [A04 Insecure Design](#a04-insecure-design)
- [A05 Security Misconfiguration (incl. XXE)](#a05-security-misconfiguration-incl-xxe)
- [A06 Vulnerable and Outdated Components](#a06-vulnerable-and-outdated-components)
- [A07 Identification and Authentication Failures](#a07-identification-and-authentication-failures)
- [A08 Software and Data Integrity Failures (incl. Insecure Deserialization)](#a08-software-and-data-integrity-failures-incl-insecure-deserialization)
- [A09 Security Logging and Monitoring Failures](#a09-security-logging-and-monitoring-failures)
- [A10 Server-Side Request Forgery (SSRF)](#a10-server-side-request-forgery-ssrf)
- [5. Focus ultra détaillé : Injection](#5-focus-ultra-détaillé--injection)
- [6. Focus ultra détaillé : XSS](#6-focus-ultra-détaillé--xss)
- [7. Focus : Authentification, mots de passe, sessions](#7-focus--authentification-mots-de-passe-sessions)
- [8. Focus : APIs — pourquoi ça augmente le risque ?](#8-focus--apis--pourquoi-ça-augmente-le-risque-)
- [9. Mini-labs Kali + outils OWASP (ZAP, Amass, Juice Shop, Dependency-Check)](#9-mini-labs-kali--outils-owasp-zap-amass-juice-shop-dependency-check)
- [10. Contraintes de scripts (Kali 2023.2 + règles Holberton)](#10-contraintes-de-scripts-kali-20232--règles-holberton)
- [11. Checklists finales (dev & audit)](#11-checklists-finales-dev--audit)
- [12. Quiz (révision rapide) + corrigé](#12-quiz-révision-rapide--corrigé)
- [Annexe : 2021 vs 2025 (culture générale)](#annexe--2021-vs-2025-culture-générale)
- [Références (liens)](#références-liens)

---

# 1. OWASP Top 10 : définition, utilité, limites

## C’est quoi ?
Le **OWASP Top 10** est un document de référence qui rassemble les **10 catégories de risques** les plus critiques pour la sécurité des applications web (édition 2021 ici).

**Important** : ce sont des **catégories de risques**, pas “10 bugs”.  
Ex : “Injection” regroupe SQLi, NoSQLi, command injection, etc.

## À quoi ça sert ?
- **Sensibilisation** : donner un langage commun dev/sécu/management.
- **Priorisation** : corriger ces classes de problèmes réduit fortement le risque global.
- **Formation** : apprendre les mécanismes d’attaque et les réflexes de prévention.
- **Audit** : structurer un pentest / code review / checklist.

## Ce que ce n’est pas
- Pas une checklist exhaustive.
- Pas un standard de conformité “magique”.
- Pas une excuse pour ignorer le reste (ex : logique métier, supply chain, etc.).

---

# 2. Fondations indispensables (HTTP, cookies, sessions, SOP/CORS)

Tu peux comprendre 80% des failles web avec 6 concepts :

## 2.1 Requête / réponse HTTP
- Le client envoie : **méthode**, **URL**, **headers**, **body**
- Le serveur répond : **status code**, **headers**, **body**

Zones où un attaquant peut injecter/altérer :
- URL / paramètres / body / headers
- fichiers uploadés
- ordre des requêtes (workflow)
- timing (race condition)

## 2.2 Cookies
Un cookie = une petite donnée stockée côté navigateur, renvoyée au serveur.

Attributs importants :
- `HttpOnly` : empêche JS de lire le cookie (limite l’impact XSS sur session)
- `Secure` : cookie envoyé seulement en HTTPS
- `SameSite` : aide à limiter CSRF (Lax/Strict/None)

## 2.3 Sessions
La **session** = mécanisme serveur pour “se rappeler” de l’utilisateur (stateful).

Risque : si l’ID de session est volé/deviné, l’attaquant devient toi.

## 2.4 SOP (Same Origin Policy)
Le navigateur isole les sites entre eux : un site ne peut pas lire les données d’un autre par défaut.

Mais :
- XSS “se passe” dans ton origin → alors il peut lire/agir comme toi.
- CORS peut ouvrir des accès cross-origin.

## 2.5 CORS
CORS est une **politique** que le serveur renvoie pour autoriser certains cross-origin.

Mauvaise config CORS = fuite de données ou actions non voulues.

## 2.6 Entrée vs sortie
- **Input validation** : “j’accepte uniquement ce que j’attends” (allowlist)
- **Output encoding** : “je rends inoffensif ce que j’affiche”

Beaucoup de dev font l’erreur “je valide l’entrée donc je suis safe”.  
Non : l’encodage de sortie est indispensable (XSS).

---

# 3. Lire un risque OWASP comme un pro (impact, probabilité, chaîne d’attaque)

Quand tu lis un risque, pense toujours à :

## 3.1 Le scénario d’attaque (la chaîne)
1) **Point d’entrée** (où l’attaquant contrôle un input)  
2) **Vulnérabilité** (erreur de design, code, config, dépendance…)  
3) **Impact** (data leak, takeover, RCE, fraude…)  
4) **Pivot** (utiliser l’accès pour aller plus loin)

## 3.2 La différence entre AuthN et AuthZ
- AuthN : *qui es-tu ?* (login, token)
- AuthZ : *as-tu le droit ?* (permissions, rôles, ownership)

Une grande partie d’A01/A07/API Security se résume à ça.

## 3.3 Le piège “ça marche dans l’UI”
L’UI (front) n’est **jamais** une garantie.
Le serveur doit toujours vérifier.

---

# 4. La liste OWASP Top 10:2021

1. **A01: Broken Access Control**  
2. **A02: Cryptographic Failures**  
3. **A03: Injection**  
4. **A04: Insecure Design**  
5. **A05: Security Misconfiguration**  
6. **A06: Vulnerable and Outdated Components**  
7. **A07: Identification and Authentication Failures**  
8. **A08: Software and Data Integrity Failures**  
9. **A09: Security Logging and Monitoring Failures**  
10. **A10: Server‑Side Request Forgery (SSRF)**

---

# A01 Broken Access Control

## Définition
Le contrôle d’accès empêche un utilisateur d’accéder à une ressource ou action non autorisée.  
Quand il est cassé : **lecture/modif/suppression** de données, **escalade de privilèges**, fraude, etc.

## Symptômes / erreurs classiques
- Autorisation uniquement côté front (boutons cachés)
- Checks “partiels” : on vérifie l’authentification, pas l’autorisation
- Vérification du rôle, mais pas du **propriétaire** (“ownership”)
- Endpoints admin exposés
- Multi‑tenant mal isolé (entreprises, organisations, “teams”)

## Exemples (conceptuels)
- **IDOR** : accéder à `/user/12/orders` alors qu’on est l’utilisateur 11.
- **Forced browsing** : route admin accessible si on connaît l’URL.
- **Privilege escalation** : un champ `is_admin` modifiable via API.
- **Bypass workflow** : appeler directement “ship order” sans paiement.

## Comment tester (audit/lab)
- Changer les IDs (path, query, body)
- Tester différentes méthodes HTTP
- Tester un compte “user” contre des routes “admin”
- Vérifier la séparation organisationnelle (tenant)
- Tester les objets indirects : fichiers, factures, exports, images…

## Prévention (dev)
- **Deny by default** : tout est refusé par défaut.
- Vérifier l’authz **à chaque requête** côté serveur.
- Implémenter un modèle clair :
  - **RBAC** (roles) : simple, mais parfois trop grossier
  - **ABAC** (attributes) : plus fin (ownership, tenant, scopes)
- Écrire des tests d’autorisation (unit + integration).
- Ne jamais “faire confiance” aux IDs/roles envoyés par le client.

### Ce que tu dois savoir expliquer à l’oral
> “Broken Access Control = l’utilisateur peut faire quelque chose qu’il ne devrait pas pouvoir faire, parce que le serveur ne vérifie pas correctement ses permissions.”

---

# A02 Cryptographic Failures (ex Sensitive Data Exposure)

## Définition
Données sensibles mal protégées :
- en transit (TLS/HTTPS),
- au repos (stockage),
- secrets et clés mal gérés,
- mots de passe mal stockés,
- crypto obsolète ou mal utilisée.

## Exemples courants
- HTTP au lieu de HTTPS
- TLS faible / mauvaise config
- mot de passe hashé en MD5/SHA1
- tokens/clé API dans les logs
- secret dans Git (commit)
- chiffrement “maison” (custom algorithm)

## Impact
- fuite massive de données (PII, finance, santé)
- account takeover (passwords/tokens)
- fraude et usurpation
- incident légal (RGPD), réputation

## Prévention (règles d’or)
- Forcer **HTTPS** (et HSTS si possible).
- Stocker les mots de passe avec **Argon2id** ou **bcrypt** (jamais MD5/SHA1).
- Protéger les secrets :
  - variables d’environnement sécurisées
  - coffre-fort (vault) si besoin
  - rotation régulière
- Chiffrer au repos si nécessaire (selon risques).
- Ne jamais logger : passwords, tokens, secrets.

---

# A03 Injection

## Définition
L’application mélange **données non fiables** avec un **langage interprété** (SQL, shell, LDAP, template…), ce qui permet de modifier la structure de la requête.

## Où ça arrive souvent
- SQL construit à la main
- commandes système (`ping`, `nslookup`, `convert`, `ffmpeg`…)
- filtres de recherche (NoSQL, Elastic)
- templating côté serveur

## Prévention (résumé)
- **Paramétrer** (prepared statements) / ORM safe
- Validation allowlist (types, formats)
- Éviter les shells : utiliser des APIs
- Moindre privilège DB

> Pour un deep-dive complet : voir [Focus Injection](#5-focus-ultra-détaillé--injection)

---

# A04 Insecure Design

## Définition
Le système est conçu d’une façon qui rend les abus probables, même si le code est “propre”.

## Exemples
- Aucun rate limiting (login, OTP, reset)
- Workflow paiement modifiable depuis le client
- Pas de séparation des rôles dès le départ
- Pas de contrôle anti-fraude sur actions critiques
- Pas de threat model, pas d’abuse cases

## Prévention
- Threat modeling léger : “comment un attaquant abuserait de ce flow ?”
- Ajout de contrôles au niveau design :
  - quotas, rate limits, step-up auth
  - validation serveur des étapes du workflow
  - audit trail (logs)
  - idempotence (anti double click, anti replay)

---

# A05 Security Misconfiguration (incl. XXE)

## Définition
Mauvaise configuration d’un composant :
- serveur, framework, headers, CORS
- debug activé en prod
- permissions trop larges
- services inutiles exposés
- stockage cloud public
- parsers XML permissifs (XXE)

## Misconfig fréquentes (checklist)
- Debug on / stack traces exposées
- Directory listing
- Headers sécurité manquants (CSP, HSTS…)
- CORS `*` sur endpoints sensibles
- Default credentials
- Ports admin ouverts au monde
- Trop d’infos dans messages d’erreur

## XXE : explication claire
Un document XML peut déclarer des “entités externes”.  
Si le parser les résout, l’attaquant peut forcer l’app à :
- lire un fichier local,
- appeler un service interne (SSRF),
- provoquer un DoS.

## Prévention
- “Secure by default” : config de prod stricte.
- Désactiver debug, fermer ports, supprimer services inutiles.
- Mettre des headers de sécurité.
- Pour XXE : désactiver DTD/external entities, préférer JSON quand possible.

---

# A06 Vulnerable and Outdated Components

## Définition
Libs/frameworks/dépendances vulnérables ou non maintenues.

## Pourquoi c’est terrible ?
- Exploit “prêt à l’emploi” (CVE public)
- Compromission sans bug dans ton code
- Chaîne d’attaque supply chain

## Prévention
- Inventaire dépendances + versions (SBOM si possible)
- Patch management (process régulier)
- SCA (Dependency-Check, Dependabot…)
- Pin versions + lockfiles
- Images Docker maintenues (base images à jour)

---

# A07 Identification and Authentication Failures

## Définition
Failles dans :
- login
- gestion sessions/tokens
- MFA/OTP
- reset password
- “remember me”
- protection contre brute force / credential stuffing

## Exemples
- pas de rate limit sur login
- sessions qui n’expirent pas
- token JWT trop long, secret faible
- reset password facile à abuser
- erreurs de login trop bavardes (user enumeration)

## Prévention (résumé)
- MFA sur comptes sensibles
- rate limiting et détection de credential stuffing
- sessions : rotation, expiration, invalidation
- cookies : HttpOnly/Secure/SameSite
- stockage password : Argon2id/bcrypt

---

# A08 Software and Data Integrity Failures (incl. Insecure Deserialization)

## Définition
Problèmes d’intégrité liés à :
- code distribué (artefacts CI/CD, mises à jour)
- dépendances et supply chain
- plugins non signés
- désérialisation non sûre

## Insecure Deserialization (simple)
Désérialiser un objet “complexe” non fiable (Java serialization, Python pickle…) peut :
- manipuler la logique,
- exécuter du code (dans certains cas),
- provoquer des corruptions d’état.

## Prévention
- Éviter la désérialisation d’objets arbitraires : préférer JSON + schéma strict
- Signer/valider les artefacts (hash, signatures)
- Sécuriser CI/CD (secrets, permissions minimales)
- Vérifier l’intégrité des téléchargements et plugins

---

# A09 Security Logging and Monitoring Failures

## Définition
Si tu ne logs pas (bien) et ne surveilles pas, tu ne peux pas :
- détecter,
- enquêter,
- répondre.

## Ce qu’il faut logger (sans secrets)
- succès/échecs login
- reset password / MFA
- changements de rôle/permissions
- accès à ressources sensibles
- erreurs serveur (5xx) + anomalies

## Prévention
- logs structurés (JSON), `request_id`
- alerting (seuils simples)
- intégrité des logs (accès limité, rotation, retention)
- préparation incident response (IR)

---

# A10 Server-Side Request Forgery (SSRF)

## Définition
L’app fetch une URL contrôlée par l’utilisateur sans validation.  
L’attaquant fait faire au serveur des requêtes vers :
- `localhost`
- réseaux privés (intranet)
- services cloud metadata
- endpoints internes derrière firewall

## Prévention
- allowlist strict des destinations
- bloquer IP privées/localhost/metadata
- egress filtering (proxy sortant)
- pas de “follow redirects” aveugle
- timeouts, size limits

---

# 5. Focus ultra détaillé : Injection

## 5.1 Le “pattern mental” de l’injection
> *Si un input utilisateur peut influencer la structure d’un langage interprété, il y a un risque d’injection.*

Langages typiques :
- SQL
- Shell/OS commands
- LDAP
- XPath/XQuery
- Template engines (SSTI)
- NoSQL queries

## 5.2 SQL Injection (SQLi)

### Comment ça arrive (concept)
Tu concatènes l’input dans la requête au lieu de la paramétrer.

### Impacts (exemples)
- lire des données sensibles (users, hashes, cartes…)
- modifier/supprimer des données
- bypass login
- parfois RCE selon DB + config

### Prévention (vraiment efficace)
- Prepared statements / requêtes paramétrées (toujours)
- ORM, query builder sécurisé
- Validation strict type (ex: `id` doit être un int)
- Compte DB en moindre privilège

## 5.3 Command Injection

### Où ça arrive
- features “ping”, “traceroute”, “convert image”, “zip”
- scripts shell appelés avec paramètres utilisateur

### Prévention
- éviter le shell : utiliser une librairie native
- si nécessaire : arguments séparés (pas de string)
- allowlist stricte sur les valeurs (ex: hostname validé)
- sandbox (container), permissions minimales

## 5.4 NoSQL Injection (ex Mongo)

### Problème typique
L’API accepte des opérateurs spéciaux (`$ne`, `$gt`, etc.) dans l’input JSON.

### Prévention
- schéma strict (types attendus)
- “désactiver” les opérateurs non attendus
- utiliser des libs qui protègent contre operator injection

## 5.5 SSTI (Server-Side Template Injection)

### Comment ça arrive
Tu passes une string contrôlée par l’utilisateur à un moteur de template côté serveur, qui interprète des expressions.

### Prévention
- ne jamais compiler/évaluer un template fourni par l’utilisateur
- utiliser des templates “logic-less”
- sandbox / escaping strict

---

# 6. Focus ultra détaillé : XSS

## 6.1 Définition
XSS = injection de contenu exécuté dans le navigateur des victimes, sous le domaine de l’application.

## 6.2 Les 3 types
1. **Reflected** : renvoyé immédiatement par le serveur
2. **Stored** : stocké puis affiché à d’autres
3. **DOM XSS** : vulnérabilité purement côté JS

## 6.3 Pourquoi c’est grave ?
- vol de session (si cookies accessibles)
- actions en tant que la victime
- phishing “dans” l’application
- exfiltration de données affichées
- pivot admin → compromission complète

## 6.4 Le triptyque anti-XSS
1) **Output encoding** (selon contexte)  
2) **CSP** (réduit l’impact)  
3) **Éviter les sinks dangereux** (`innerHTML`, `eval`, `document.write`)

### Encodage selon contexte (très important)
- Contexte HTML : encoder `< > & " '`
- Contexte attribut : encoder + éviter les event handlers
- Contexte JS : éviter l’injection dans une string JS
- Contexte URL : encoder URL

## 6.5 CSP (Content Security Policy) — idée
CSP dit au navigateur quelles sources de scripts sont autorisées.
Une CSP stricte peut :
- bloquer beaucoup de XSS
- empêcher le chargement de scripts externes

## 6.6 Erreurs classiques
- “je nettoie l’entrée donc c’est bon” → non, encode la sortie
- accepter du HTML riche sans sanitizer robuste
- stocker des données non fiables et les réutiliser dans plusieurs contextes

---

# 7. Focus : Authentification, mots de passe, sessions

## 7.1 Mots de passe : ce qu’il faut retenir
- Stockage : **hash adaptatif** (Argon2id/bcrypt)
- Salt : intégré par ces algos modernes
- Option “pepper” possible (secret serveur)
- Politique : privilégier des passphrases + blocage des passwords courants

## 7.2 Reset password (souvent attaqué)
- tokens courts et expirants
- usage unique
- confirmation utilisateur
- mêmes règles de password policy

## 7.3 Sessions : règles d’or
- ID session non prédictible (haute entropie)
- expiration absolue + inactivity timeout
- rotation après login
- invalidation au logout
- cookies HttpOnly/Secure/SameSite

---

# 8. Focus : APIs — pourquoi ça augmente le risque ?

Les APIs ont des risques accentués parce que :
- elles exposent directement des objets (IDs, ressources)
- elles sont facilement automatisables (scripts, bots)
- elles gèrent souvent beaucoup de données (JSON riche)
- la séparation front/back pousse à des erreurs d’authz

Risques typiques (liés au Top 10 web + API Top 10) :
- BOLA (IDOR API)
- Auth cassée (tokens, scopes)
- Mass assignment
- Excessive data exposure
- Rate limit absent
- Erreurs trop bavardes

Bon réflexe : **authz sur chaque endpoint**, schéma strict, rate limits.

---

# 9. Mini-labs Kali + outils OWASP (ZAP, Amass, Juice Shop, Dependency-Check)

## 9.1 Lab Holberton WebSec 0x01
L’énoncé mentionne généralement :
- une machine cible “Cyber - WebSec 0x01”
- l’ajout `web0x01.hbtn` dans `/etc/hosts`

> Fais-le uniquement si demandé, et respecte le scope.

## 9.2 OWASP ZAP (DAST)
Objectifs pédagogiques :
- comprendre proxy/interception
- scan passif (safe)
- rapport et tri des alertes

Workflow :
1. Lancer ZAP
2. Configurer le navigateur en proxy
3. Naviguer sur l’app (crawl)
4. Lire les alertes et comprendre “risk/confidence”
5. Mapper alertes ↔ OWASP Top 10

## 9.3 Juice Shop (app vulnérable)
Objectif : pratiquer XSS, injection, auth, access control… sur une app volontairement vulnérable.
- idéalement en local (Docker) pour rester 100% légal/autorisé

## 9.4 Amass (recon) — seulement autorisé
Objectif : découvrir des sous-domaines dans un scope autorisé.
- te fait comprendre “attack surface management”

## 9.5 Dependency-Check (SCA)
Objectif :
- scanner un projet
- générer un rapport
- interpréter CVE/severity
- décider patch/upgrade/mitigation

---

# 10. Contraintes de scripts (Kali 2023.2 + règles Holberton)

Ton énoncé mentionne typiquement :
- exécution sur **Kali Linux 2023.2**
- éditeurs autorisés : `vi`, `vim`, `emacs`
- **substituer la range IP par `$1`** (argument de script)
- première ligne : `#!/bin/bash`
- fichier termine par une newline
- **interdit** : backticks, `&&`, `||`, `;`

## 10.1 Template minimal (conforme aux contraintes)

```bash
#!/bin/bash
range="$1"

if [ -z "$range" ]
then
    echo "Usage: $0 <ip-range>"
    exit 1
fi

echo "Target range: $range"
```

✅ pas de `;`  
✅ pas de `&&` `||`  
✅ pas de backticks

## 10.2 Astuce : remplacer `cmd1 && cmd2`
Tu fais simplement :

```bash
cmd1
if [ "$?" -ne 0 ]
then
    echo "cmd1 failed"
    exit 1
fi
cmd2
```

---

# 11. Checklists finales (dev & audit)

## 11.1 Checklist dev (prévention)
- AuthN : MFA/OTP sur comptes sensibles, rate limit login
- AuthZ : deny by default + checks serveur par objet
- Inputs : validation stricte, allowlists
- Outputs : encoding selon contexte (anti-XSS)
- Crypto : HTTPS, passwords hash adaptatif, secrets hors Git
- Config : debug off, headers, CORS minimal
- Dépendances : update + SCA
- Logs : events auth + actions sensibles + alerting simple

## 11.2 Checklist audit (détection)
- A01 : tester IDOR, rôles, endpoints cachés
- A02 : vérifier HTTPS, cookies secure, secrets, hashes
- A03 : repérer concat SQL/shell/template
- A04 : rate limit, workflow serveur, abuse cases
- A05 : debug, headers, CORS, permissions, XXE
- A06 : versions, CVE, libs non maintenues
- A07 : brute force, reset flow, sessions, JWT
- A08 : CI/CD, plugins, deserialization
- A09 : logs, alerting, détection d’anomalies
- A10 : fetch URL, redirects, accès intranet/metadata

---

# 12. Quiz (révision rapide) + corrigé

## 12.1 Questions
1. Définis OWASP Top 10 (en 2 phrases).
2. Pourquoi l’injection est dangereuse ? (2 impacts)
3. Explique XSS et ses 3 types.
4. “Broken authentication” : cite 3 causes.
5. “Sensitive data exposure” correspond à quel item 2021 ?
6. Donne 5 exemples de misconfiguration.
7. Explique XXE (cause + impact).
8. Donne un exemple d’IDOR.
9. Pourquoi insecure deserialization est risqué ?
10. Pourquoi logging/monitoring est crucial ?
11. Pourquoi une dépendance vulnérable suffit à compromettre une app ?
12. Pourquoi les APIs augmentent les risques ? (3 points)

## 12.2 Corrigé (pistes)
1. Document d’awareness OWASP, 10 catégories de risques majeurs web.
2. Exfiltration data, bypass auth, RCE selon contexte.
3. Reflected/stored/DOM + exécution chez la victime.
4. Pas de rate limit, sessions mal gérées, reset faible, secrets faibles.
5. A02 Cryptographic Failures.
6. Debug on, CORS large, headers manquants, services exposés, erreurs bavardes…
7. XML parser résout entités externes → lire fichiers/SSRF/DoS.
8. Modifier un ID dans l’URL pour accéder aux données d’un autre.
9. Peut modifier l’état, parfois exécuter du code.
10. Détecter, enquêter, répondre aux incidents.
11. CVE exploitable “prêt” → pwn sans bug dans ton code.
12. Automatisation facile, IDs d’objets, JSON riche, authz endpoint, rate limits.

---

# Annexe : 2021 vs 2025 (culture générale)

OWASP a publié une édition 2025 : les grandes idées restent proches, mais certaines catégories évoluent (supply chain, erreurs, etc.).  
Pour ton module : **reste focus 2021**, c’est ce qui est demandé.

---

# Références (liens)

> Astuce : ouvre ces liens au fur et à mesure du cours (c’est littéralement ta “boîte à outils” sécu).

- OWASP Top 10:2021  
  - https://owasp.org/Top10/2021/
- OWASP Cheat Sheet Series (index)  
  - https://cheatsheetseries.owasp.org/index.html
- SQL Injection Prevention Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- Injection Prevention Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html
- XSS Prevention Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
- Authentication Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
- Session Management Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html
- Password Storage Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html
- CSRF Prevention Cheat Sheet  
  - https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
- OWASP ZAP  
  - https://www.zaproxy.org/
- OWASP Amass  
  - https://owasp.org/www-project-amass/
- OWASP Juice Shop  
  - https://owasp.org/www-project-juice-shop/
- OWASP Dependency-Check  
  - https://owasp.org/www-project-dependency-check/
- OWASP Web Security Testing Guide (WSTG)  
  - https://owasp.org/www-project-web-security-testing-guide/
- OWASP API Security Top 10 (2023)  
  - https://owasp.org/API-Security/editions/2023/en/0x11-t10/
