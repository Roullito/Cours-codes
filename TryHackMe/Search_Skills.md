# TryHackMe – Search Skills (Compétences de recherche)

Tu entres maintenant dans un truc fondamental pour TOUT le reste de la cybersécu : **savoir chercher**.

En sécu, tu passes ta vie à :

- chercher **comment** fonctionne une techno,
- trouver des **vulnérabilités**,
- lire des **CVE**, des **PoC**, des docs officielles,
- vérifier si une info est **fiable** ou juste un thread random sur un forum douteux.

Ce module “Search Skills” te sert de **boîte à outils mental** pour ne plus être noyé dans l’info.

---

## Objectifs du cours

À la fin de ce module, tu dois être capable de :

- Évaluer rapidement si une **source d’information** est fiable ou non.
- Utiliser un **moteur de recherche** avec de vrais opérateurs (pas juste taper une phrase).
- Connaître plusieurs **moteurs spécialisés** (Shodan, Censys, VirusTotal, HIBP, CVE, Exploit-DB, GitHub…).
- Lire et exploiter de la **documentation technique officielle** (man pages, docs Microsoft, docs produits).
- Utiliser **les réseaux sociaux et les news** comme outils de veille, tout en comprenant les risques (OSINT, oversharing, etc.).

---

## 1. Pourquoi les compétences de recherche sont vitales en cybersécu ?

Tu tapes “learn cyber security” → des **centaines de millions** de résultats.
Tu tapes “learn hacking” → encore plus.

Tu as deux options :

1. **Subir** : tu cliques sur les 3 premiers liens, tu acceptes ce qu’on te donne.
2. **Contrôler** : tu sais filtrer, fouiller, recouper, remonter à la **bonne information**.

> En cybersécu, celui qui sait chercher efficacement progresse 10x plus vite que celui qui attend “le bon tuto YouTube”.

---

## 2. Évaluer la qualité d’une source

Sur Internet, **tout le monde peut publier** : blogs, tweets, posts LinkedIn, wiki éditable, threads Reddit…
Résultat : on trouve **des pépites** et **des énormes bêtises** au même endroit.

Tu dois donc **évaluer** une info avant de la prendre pour vérité.

### 2.1 Quatre questions à se poser

1. **Source** – Qui parle ?
   - Auteur identifiable ? Organisation connue ?
   - Blog perso obscur, ou doc officielle de Microsoft / RFC / OWASP ?
   - Publier un article ne fait pas de quelqu’un un expert.

2. **Preuves & raisonnement**
   - Y a-t-il :
     - des exemples concrets ?
     - des liens vers des docs officielles / papers ?
     - des reproductions testables ?
   - Ou juste : “Trust me bro, j’ai 10 ans d’expérience” ?

3. **Objectivité & biais**
   - Est-ce que l’auteur pousse une **idée**, un **produit**, un **service** ?
   - Est-ce ce qu’il dit compatible avec d’autres sources ?
   - Ton radar à bullshit doit se déclencher sur : “Ce script est LA solution ultime à tous vos problèmes de sécu”.

4. **Corroboration (recouper)**
   - Est-ce que **plusieurs sources indépendantes** disent la même chose ?
   - Si une info n’apparaît nulle part ailleurs… prudence.

> Réflexe à adopter : **“OK intéressant. Qui d’autre dit la même chose ?”**

---

## 3. Utiliser les moteurs de recherche comme un pro

Tu connais déjà Google / Bing / DuckDuckGo.
Mais est-ce que tu les utilises vraiment à fond ?

### 3.1 Les opérateurs de recherche utiles (Google)

> Tu peux vérifier pour ton moteur préféré quels opérateurs il supporte, mais ceux-ci sont un bon socle.

#### 1) `"exact phrase"`

Cherche une **expression exacte**.

```text
"passive reconnaissance"
```

→ Résultats qui contiennent exactement cette expression (et pas juste “passive” d’un côté et “reconnaissance” de l’autre).

#### 2) `site:`

Limite la recherche à un **domaine**.

```text
site:tryhackme.com "success stories"
site:microsoft.com ipconfig
site:owasp.org "broken access control"
```

Utile pour :

- fouiller la doc d’un produit,
- chercher des exemples sur un site précis,
- trouver des articles dans un scope défini.

#### 3) `-` (exclusion)

Permet d’**exclure** des termes.

```text
pyramids -tourism
```

Je veux parler des pyramides, mais **pas** des sites de tourisme.

En cybersécu :

```text
sql injection -“bug bounty program”
```

→ Tu exclus certaines pages typiques si elles polluent les résultats.

#### 4) `filetype:`

Indispensable pour chercher des **fichiers** (PDF, PPT, DOC, XLS, etc.).

```text
filetype:ppt "cyber security"
filetype:pdf "web application security"
filetype:xls "vulnerability scan report"
```

Très utile pour :

- trouver des slides de conf,
- des rapports d’audit en exemple,
- des fiches techniques.

---

### 3.2 Combiner les opérateurs

Tu peux enchaîner plusieurs opérateurs pour des recherches très ciblées :

```text
site:nmap.org "port scanning" filetype:pdf
"cross site scripting" -reflected -dom
"Active Directory" hardening filetype:ppt
```

> Astuce : quand tu cherches une commande ou une erreur, essaie **avec** et **sans** les guillemets.

---

## 4. Moteurs de recherche spécialisés

En cybersécu, tu n’utilises pas que Google. Il existe des moteurs spécialisés pour :

- les **machines exposées sur Internet**,
- les **certificats / hôtes / services**,
- les **malwares & URLs**,
- les **données fuitées**,
- les **vulnérabilités & exploits**.

Voyons les principaux.

### 4.1 Shodan – Le moteur des objets connectés

**Shodan** = moteur de recherche pour **appareils connectés à Internet** :

- serveurs web,
- caméras IP,
- routeurs,
- systèmes industriels,
- objets IoT, etc.

Tu peux, par exemple :

- chercher tous les serveurs qui renvoient `apache 2.4.1` dans leurs bannières,
- filtrer par pays, ports, organisations, etc.

> ⚠️ **Éthique & légal** : Shodan montre ce qui est accessible sur Internet, mais ce n’est pas une autorisation pour attaquer ces systèmes. On observe, on étudie, mais on ne touche pas sans permission.

---

### 4.2 Censys – Vue globale sur les hôtes & certificats

Ressemble à Shodan, mais avec un focus différent :

- Censys se concentre sur les **hôtes**, **certificats**, ports, protocoles…
- Utile pour :
  - **cartographier** les actifs d’une organisation,
  - voir quels domaines & sous-domaines sont actifs,
  - vérifier quels services & ports sont ouverts,
  - trouver des assets “fantômes” (rogue assets).

Shodan → plutôt orienté **“quels devices sont là et à quoi ressemblent-ils ?”**
Censys → plutôt **“quelle est la surface exposée (hôtes, certificats, services) d’une organisation ?”**

---

### 4.3 VirusTotal – Scanner multi-antivirus & réputation

**VirusTotal** permet :

- d’**envoyer un fichier** suspect ou une URL,
- ou de rechercher via un **hash** (MD5, SHA1, SHA256),
- et de voir les résultats de **dizaines de moteurs antivirus** et scanners d’URL.

Tu peux :

- vérifier si un fichier est détecté comme malware,
- voir quelles familles d’AV le détectent,
- lire les **commentaires de la communauté** (souvent très instructifs).

⚠️ Attention :

- Un fichier peut être **faux positif** (détecté à tort),
- Tous les AV ne sont pas d’accord entre eux,
- VirusTotal est un **indice**, pas un verdict absolu.

---

### 4.4 Have I Been Pwned (HIBP) – Fuites de données

**HIBP** répond à une question simple :

> “Mon adresse email a-t-elle fuité dans des data breaches ?”

Fonctionnement :

- Tu saisis une **adresse email**,
- Le site te dit si elle apparaît dans des bases de données compromises.

Intérêt :

- Savoir que des **mots de passe** liés à cette adresse ont potentiellement été exposés,
- Sensibiliser sur la **réutilisation de mot de passe** (une fuite → tous les sites réutilisant ce mot de passe sont en danger).

Même si les mots de passe sont hashés, beaucoup sont faibles et **cassés facilement**.

---

### 4.5 CVE & NVD – Dictionnaire des vulnérabilités

**CVE** (Common Vulnerabilities and Exposures) = **dictionnaire** des vulnérabilités.

- Chaque vulnérabilité a un identifiant unique :
  `CVE-AAAA-NNNNN` (ex : `CVE-2014-0160` – Heartbleed).
- Maintenu par **MITRE**.
- Permet à tout le monde de parler de **la même vulnérabilité** avec le même identifiant.

Tu peux chercher :

- sur le site du **CVE Program**,
- ou via la **NVD (National Vulnerability Database)** qui ajoute des infos (scores CVSS, etc.).

Réflexe à prendre :

1. Tu entends parler d’une vulnérabilité → tu récupères son **CVE ID**.
2. Tu la regardes sur CVE/NVD pour avoir l’info officielle.

---

### 4.6 Exploit Database & GitHub – Exploits & PoC

Une fois que tu connais une vulnérabilité, tu veux parfois :

- voir s’il existe un **exploit public**,
- ou un **proof-of-concept (PoC)**.

Deux ressources majeures :

#### Exploit Database

- Base de données d’exploits pour plein de vulnérabilités,
- Certains exploits sont marqués comme **“verified”** (testés),
- Utile pour :
  - tester un environnement de lab,
  - comprendre comment une vulnérabilité est exploitée.

#### GitHub

- Contient énormément de **PoC**, scripts, outils liés aux CVE.
- Tu peux chercher directement par **CVE ID** :

```text
CVE-2014-0160 exploit
CVE-2024-xxxx PoC github
```

⚠️ Comme toujours :
- Exploiter un système **sans autorisation** est illégal.
- Ces ressources sont pour le **lab**, le **pentest autorisé** ou l’étude.

---

## 5. Lire la documentation technique

Un des plus gros boosts de skill que tu peux avoir :
> Apprendre à lire la **doc officielle**.

### 5.1 Man pages Linux

Avant Internet, pour comprendre une commande, tu tapais :

```bash
man <commande>
```

Ex :

```bash
man ip
```

Les **man pages** décrivent :

- ce que fait la commande,
- sa syntaxe,
- ses options,
- parfois des exemples.

Dans un terminal Linux :

- `man ip` → lire (flèches haut/bas),
- `q` → quitter.

Tu peux aussi taper `man ip` dans un moteur de recherche → version web de la même doc.

Réflexe à prendre sur un système Unix-like :

- Tu ne comprends pas une commande → `man commande` ou `commande --help`.

---

### 5.2 Documentation Microsoft

Pour Windows et l’écosystème Microsoft, la **documentation technique officielle** est sur leurs sites docs.

Exemple : tu cherches la commande `ipconfig` :

- tu vas sur la doc Microsoft,
- tu as la description, les options, les exemples.

Toujours privilégier :

- La **doc officielle** pour comprendre le comportement garanti,
- Les blogs / tutos pour les exemples et la pédagogie complémentaire.

---

### 5.3 Documentation de produits & outils

Tout produit **sérieux** a une doc :

- Snort (IDS),
- Apache HTTP Server,
- PHP,
- Node.js,
- Nginx,
- etc.

Avantages de la doc officielle :

- **La plus à jour**,
- **La plus complète**,
- Référence de base pour comprendre les options, paramètres, formats, etc.

Stratégie :

1. Tu identifies un produit / outil.
2. Tu cherches `"<nom produit> documentation"` ou `"<nom produit> official docs"`.
3. Tu lis la doc officielle **puis** tu complètes avec des articles / vidéos si besoin.

---

## 6. Réseaux sociaux & news – Veille et OSINT

### 6.1 Découvrir de nouvelles sources & rester à jour

Les réseaux sociaux (Twitter/X, LinkedIn, etc.) sont :

- des mines d’infos techniques (threads, PoC, retours d’expérience),
- des relais très rapides d’**actus** (vulnérabilités critiques, campagnes d’attaques, nouvelles tools).

Tu peux :

- suivre des **chercheurs en sécu**, des **équipes de produits**, des **CERT**, des **communautés**,
- rejoindre des groupes orientés : bug bounty, blue team, malware analysis, etc.

### 6.2 Attention à l’OSINT & à l’oversharing

L’autre côté du miroir :

> Les réseaux sociaux sont aussi un outil pour les attaquants (OSINT).

En tant que défenseur, tu dois :

- surveiller que les utilisateurs **ne partagent pas trop** d’infos sensibles :
  - nom de l’école d’enfance,
  - date de naissance exacte,
  - lieux fréquents,
  - réponses probables aux questions secrètes (“pet name”, “first school”, etc.).

- expliquer que ces infos peuvent être utilisées pour :
  - le **doxing**,
  - du **social engineering**,
  - de la **prise de compte** (reset de mot de passe).

### 6.3 News & sites spécialisés

En plus des réseaux sociaux :

- Beaucoup de sites d’actualités tech / cybersécu publient des news sur :
  - nouvelles vulnérabilités majeures,
  - campagnes de ransomware,
  - fuites de données massives.

À toi d’en tester plusieurs et de garder ceux qui :

- sont fiables,
- ne tombent pas dans le sensationnel permanent,
- ont une bonne qualité d’analyse.

---

## 7. Fiche mémo – Compétences de recherche

### 7.1 Check-list “évaluer une info”

- Qui parle ? (source)
- Comment il le prouve ? (preuve / démo / doc)
- Quel intérêt il a ? (biais éventuel)
- Qui d’autre confirme ? (corroboration)

### 7.2 Opérateurs de recherche essentiels (Google-like)

- `"mot exact"` → phrase exacte
- `site:domain.tld` → limiter à un domaine
- `-mot` → exclure un terme
- `filetype:pdf/doc/ppt/xls` → chercher des fichiers spécifiques

### 7.3 Outils spécialisés à connaître

- **Shodan** → devices & services exposés sur Internet
- **Censys** → hôtes, certificats, surface d’attaque
- **VirusTotal** → réputation fichiers & URLs (multi-AV)
- **Have I Been Pwned** → emails présents dans des fuites
- **CVE / NVD** → dictionnaire officiel des vulnérabilités
- **Exploit-DB, GitHub** → PoC & exploits publics

### 7.4 Documentation & veille

- Linux : `man commande` / `commande --help`
- Windows & produits : docs officielles (Microsoft / vendor)
- Products : docs natives (Snort, Apache, PHP, Node.js, etc.)
- Veille : réseaux sociaux + sites d’actu spécialisés

---

## 8. Mini-exercices (facultatifs)

Pour t’entraîner, essaie :

1. Formuler une recherche pour trouver des **slides (PPT)** sur la “network segmentation”.
2. Trouver la **doc officielle** de la commande `ip` sous Linux et **de** `ipconfig` sous Windows.
3. Chercher des infos sur une vulnérabilité célèbre (ex : Heartbleed), en retrouvant :
   - son **CVE ID**,
   - sa page sur un **CVE/NVD**,
   - au moins un **PoC** sur Exploit-DB ou GitHub.

---

Tu as maintenant une première vraie **boîte à outils de recherche**.
Elle va te servir dans tous les autres modules TryHackMe, dans Holberton, et dans toute ta carrière de sécu / dev / admin.

On pourra ensuite faire un “kit de survie OSINT” plus avancé si tu veux, en s’appuyant sur ces bases.
