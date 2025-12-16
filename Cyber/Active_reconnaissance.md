# Active Reconnaissance — Cours ultra complet (Kali Linux)

> **Contexte** : cours conçu pour un **environnement de lab autorisé** (CTF / machine de formation type `cyber_netsec_0x02`).  
> **Règle d’or** : ne scanne / teste **que** des machines pour lesquelles tu as une autorisation explicite (scope).  
> **But** : apprendre à **observer**, **déduire**, **valider**, puis **documenter**.

---

## 0) Objectifs pédagogiques

À la fin, tu dois pouvoir expliquer clairement (sans Google) :

- C’est quoi la **reconnaissance active** (et la différence avec la passive)
- Pourquoi c’est critique en cybersécurité (attaque **et** défense)
- Comment profiler une appli avec **Wappalyzer / WhatWeb / headers**
- Les bases de **DNS enumeration** (records, subdomains, AXFR)
- Les bases d’énumération **SMTP** (banner, EHLO, extensions)
- Comment faire un **OS fingerprinting** (et pourquoi c’est imparfait)
- Ce qu’est une **SQL injection**, comment elle naît, comment on la bloque
- Comment **sqlmap** détecte une injection et **comment il “dump”** une DB en lab

---

## 1) Active reconnaissance : définition et enjeux

### 1.1 Passive vs active (simple)
- **Passive recon** : tu observes sans toucher la cible (OSINT, DNS publics, archives web…).
- **Active recon** : tu **interagis** avec la cible (ping, scan de ports, requêtes HTTP, banner grabbing…).

### 1.2 Pourquoi c’est important ?
- Côté attaquant : trouver **surface d’attaque** (services, versions, endpoints, mauvaises configs).
- Côté défense : comprendre ce qu’un adversaire peut apprendre et **réduire l’exposition** (hardening, segmentation, WAF, logs).

---

## 2) Accéder à la cible (lab) et valider la connectivité

### 2.1 Checklist rapide
- VPN connecté ✅
- IP de la cible récupérée ✅
- Scope clair (IP/host + ce que tu as le droit de faire) ✅

### 2.2 Ping (ICMP)
```bash
ping -c 1 <TARGET_IP>
```
- Si ça répond : la machine est joignable (au moins en ICMP).
- Si ça ne répond pas : **ça ne prouve pas** qu’elle est down (ICMP peut être filtré).

### 2.3 Résolution du nom “endpoint” (ex: `active.hbtn`)
Dans beaucoup de labs, le “endpoint” est un **nom DNS interne** non résolu par ton système tant que tu ne le mappes pas.

Ajouter un mapping local :
```bash
sudo bash -c 'echo "<TARGET_IP> active.hbtn" >> /etc/hosts'
```

Vérifier :
```bash
getent hosts active.hbtn
curl -I http://active.hbtn/
```

> Astuce : si tu changes de container / IP, évite d’empiler des entrées. Supprime les anciennes lignes `active.hbtn` dans `/etc/hosts`.

---

## 3) Méthode simple (celle qui marche tout le temps)

### 3.1 La “4 passes”
1) **Reachability** : ping / routes / DNS
2) **Ports** : scan (rapide → complet)
3) **Services** : versions, bannières, scripts
4) **App-layer** : HTTP endpoints, content discovery, auth, injections (si dans le scope)

### 3.2 Discipline de base
- Scans **progressifs** (ne pars pas direct en full scan agressif).
- Notes au fur et à mesure : **commande → résultat → interprétation → prochaine action**.

---

## 4) Traceroute : comprendre le chemin

### 4.1 Pourquoi
Identifier la route, latence, filtrages, sauts.

```bash
traceroute <TARGET_IP>
# ou (souvent plus fiable sur certains réseaux)
traceroute -I <TARGET_IP>
```

---

## 5) Scan de ports : Nmap (la base)

### 5.1 Les concepts (à connaître)
- Port **open / closed / filtered**
- TCP SYN scan (`-sS`) vs connect scan (`-sT`)
- Service/version detection (`-sV`)
- Scripts NSE (`--script`)
- Host discovery (`-Pn` = “ne fais pas de ping préalable”)

### 5.2 Commandes utiles (réalistes)
Top ports (rapide) :
```bash
nmap -n -Pn <TARGET_IP>
```

Top 1000 + versions :
```bash
nmap -n -Pn -sV <TARGET_IP>
```

Scan complet TCP (plus long) :
```bash
nmap -n -Pn -p- <TARGET_IP>
```

Ports trouvés → scan précis :
```bash
nmap -n -Pn -sV -p <PORTS_COMMA_SEPARATED> <TARGET_IP>
```

Sortie “open ports only” vers un fichier (style projet) :
```bash
nmap -n -Pn <TARGET_IP> | grep -E "/tcp\s+open" | cut -d/ -f1 | paste -sd ", " - > 0-ports.txt
```

### 5.3 Lire un résultat Nmap (mini-guide)
- **PORT/STATE/SERVICE** : “open” t’indique ce qui écoute.
- **VERSION** : oriente tes hypothèses (web server, sshd, smtp…).
- Ensuite tu passes en **énumération** du service (HTTP, SMTP, SSH…).

---

## 6) Masscan : scanner très vite de grandes plages (à utiliser avec prudence)

**À quoi ça sert ?**  
Masscan est un scanner très rapide (asynchrone) adapté aux **grandes plages IP**. En lab, utile pour comprendre la différence “fast sweep” vs “scan détaillé”.

**Quand l’utiliser ?**
- Quand tu as beaucoup d’IPs (ex: /24) et que tu veux juste savoir “qui a le port 80/443 ouvert”.

Exemple (ports web) :
```bash
sudo masscan <CIDR_OR_RANGE> -p80,443 --rate 1000
```

- `--rate` contrôle l’agressivité (plus haut = plus de paquets/sec).
- Ensuite, tu confirmes toujours avec **Nmap** sur les IPs trouvées.

> ⚠️ Masscan est bruyant. Hors lab, tu peux provoquer des alertes et des blocages.

---

## 7) Banner grabbing : telnet / netcat (nc)

### 7.1 Pourquoi
Beaucoup de services “parlent” dès la connexion (bannière). Ça te donne :
- produit + version
- parfois un hostname interne
- parfois des capacités/commands

### 7.2 Netcat (couteau suisse)
```bash
nc -nv <TARGET_IP> <PORT>
```

Exemples fréquents :
- HTTP : `nc -nv <IP> 80` puis tape manuellement une requête `GET / HTTP/1.1`
- SMTP : `nc -nv <IP> 25` puis `EHLO test`

---

## 8) Web fingerprinting : Wappalyzer + WhatWeb + Headers

### 8.1 Wappalyzer (browser)
- Techno front (framework, analytics)
- Indices backend (nginx, CDN, CMS, libs)

### 8.2 WhatWeb (CLI)
```bash
whatweb http://active.hbtn
```

### 8.3 Headers HTTP (curl)
```bash
curl -I http://active.hbtn/
```
Cherche :
- `Server: nginx/1.18.0`
- cookies, redirections, CSP, HSTS, etc.

---

## 9) Gobuster : directory enumeration (content discovery)

**But** : découvrir des chemins non visibles dans le menu (ex: `/admin`, `/api`, `/backup`).

Exemple simple :
```bash
gobuster dir -u http://active.hbtn/ -w /usr/share/wordlists/dirb/common.txt
```

### 9.1 Gérer les “wildcard responses” (302 vers `/`)
Parfois, le serveur renvoie un code “valide” même pour des chemins inexistants (ex: redirection 302 vers `/`). Gobuster te prévient.

Solutions courantes :
- Exclure le status code “wildcard”
- Exclure une taille de réponse constante
- Utiliser le mode wildcard

Exemple (exclure longueur + ajuster codes) :
```bash
gobuster dir -u http://active.hbtn/   -w /usr/share/wordlists/dirb/common.txt   -s "200,204,301,302,307,401,403"   -b ""   --exclude-length 189   -t 30   -o gobuster.txt
```

Ce que ça fait :
- `dir` : mode énumération répertoires/fichiers
- `-u` : base URL
- `-w` : wordlist
- `-s` : codes considérés “intéressants”
- `-b ""` : désactive la blacklist (404) par défaut (sinon conflit avec `-s`)
- `--exclude-length 189` : ignore les réponses dont la taille = 189 (souvent la page de redirect)
- `-t` : threads (plus haut = plus rapide, plus bruyant)
- `-o` : output file

---

## 10) Nikto : web vulnerability scanning (quick checks)

**But** : scanner rapidement un serveur web pour repérer :
- headers manquants
- fichiers/paths connus
- configs dangereuses
- versions vulnérables (indices)

Exemple :
```bash
nikto -h http://active.hbtn
```

> Nikto n’est pas “la vérité”. Il donne des **pistes** à valider.

---

## 11) DNS Enumeration + Amass

### 11.1 Outils de base (records)
```bash
dig active.hbtn
dig A active.hbtn
dig MX active.hbtn
dig TXT active.hbtn
host active.hbtn
nslookup active.hbtn
```

### 11.2 Zone transfer (AXFR) — concept
Si un serveur DNS est mal configuré, tu peux parfois demander un “transfert de zone” :
```bash
dig axfr <DOMAIN> @<NS_SERVER>
```
En vrai, c’est souvent bloqué (et c’est normal).

### 11.3 Amass (subdomain enumeration)
Amass est très utilisé pour découvrir des sous-domaines (OSINT + résolutions).
En lab, ça te montre la logique “subdomain discovery”.

Exemple :
```bash
amass enum -d <DOMAIN>
```

---

## 12) SMTP enumeration (CLI)

Banner + EHLO :
```bash
nc <TARGET_IP> 25
# puis
EHLO test
```

Nmap NSE (si port SMTP ouvert) :
```bash
nmap -n -Pn -p25 --script smtp-commands,smtp-enum-users <TARGET_IP>
```

---

## 13) OS fingerprinting (et ses limites)

Nmap OS detection :
```bash
sudo nmap -n -Pn -O <TARGET_IP>
```

À savoir :
- nécessite souvent privilèges (raw sockets)
- dépend de la latence, des firewalls, du NAT, etc.
- donne un **guess** (probabiliste), pas une certitude

---

## 14) SQL Injection : comment ça fonctionne vraiment

### 14.1 Le bug en une phrase
Une SQL injection arrive quand une appli **concatène** une entrée utilisateur dans une requête SQL **sans** requêtes préparées / paramétrage sûr.

### 14.2 Exemple mental (dangereux vs safe)
**Dangereux** (concat) :
```sql
SELECT * FROM users WHERE username = '<USER_INPUT>';
```

**Safe** (requête préparée / paramètre) :
```sql
SELECT * FROM users WHERE username = ?;
```

### 14.3 Pourquoi “ça marche”
Le SQL est un langage. Si ton input est interprété comme du SQL, tu peux :
- casser la structure (quotes, opérateurs)
- modifier la logique (vrai/faux)
- extraire des données si l’app renvoie le résultat

### 14.4 Les grandes familles (niveau concept)
- **Error-based** : erreurs SQL visibles → info leak
- **Union-based** : `UNION SELECT ...` pour “coller” des résultats
- **Boolean-based blind** : tu déduis des infos via vrai/faux (page change)
- **Time-based blind** : tu déduis via le temps de réponse (`SLEEP`)
- **Out-of-band** : exfil via DNS/HTTP (plus rare)

### 14.5 Défense (à savoir expliquer)
- Requêtes préparées / ORM correctement utilisé
- Validation stricte côté serveur (types, longueur)
- Principe de moindre privilège pour l’utilisateur DB
- WAF / RASP (utile mais pas suffisant)
- Logs + monitoring + tests SAST/DAST

---

## 15) sqlmap : comment ça détecte et comment ça “dump” une DB (lab)

> ⚠️ À n’utiliser que sur **cibles autorisées** (CTF/lab). Les exemples ci-dessous utilisent des placeholders.

### 15.1 Le workflow “propre”
1) Identifier un **point d’entrée** (param GET/POST, segment d’URL, cookie)
2) Lancer sqlmap en **détection** (preuve d’injection)
3) Fingerprint DBMS (MySQL/Postgres…)
4) Enumération : DBs → tables → colonnes
5) Extraction ciblée (dump) **uniquement dans le scope du lab**

### 15.2 Cibler une requête (3 façons)
**A) URL avec paramètres (GET)**  
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch
```

**B) Requête HTTP brute (recommandé en vrai)**  
Tu exportes une requête (DevTools/Burp) vers `request.txt` :
```bash
sqlmap -r request.txt --batch
```
Avantage : garde cookies, headers, POST body, etc.

**C) Injection dans l’URI (segment d’URL)**  
Sqlmap peut tester le path si tu l’acceptes, ou via marqueur `*` :
```bash
sqlmap -u "http://<TARGET>/product/1*" --batch
```

### 15.3 Les options qui reviennent tout le temps
- `--batch` : pas de questions interactives
- `--dbs` : liste les bases
- `-D <db>` : sélectionne une base
- `--tables` : liste les tables de la base
- `-T <table>` : sélectionne une table
- `--columns` : liste les colonnes d’une table
- `-C col1,col2` : sélectionne des colonnes
- `--dump` : extrait (dump) des données
- `--level` / `--risk` : augmente profondeur/agressivité des tests (lab)
- `--flush-session` : repart à zéro si la session sqlmap garde de vieux résultats

### 15.4 Enumération (DB → tables → colonnes)
Lister les DBs :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch --dbs
```

Lister les tables d’une DB :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB_NAME>" --tables
```

Lister les colonnes d’une table :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB_NAME>" -T "<TABLE>" --columns
```

### 15.5 Dump : extraire des données (ce que fait `--dump`)
Dump d’une table :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB_NAME>" -T "<TABLE>" --dump
```

Dump ciblé (colonnes) :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB_NAME>" -T "<TABLE>" -C "id,username" --dump
```

Dump filtré (ex: une seule ligne) :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB_NAME>" -T "<TABLE>" --dump --where="id=1"
```

> **Pourquoi c’est important** : en pentest, tu évites le “dump tout” (bruit + hors-scope). En lab, c’est pédagogique.

### 15.6 Comment sqlmap “dump” techniquement ?
sqlmap ne “magique” pas une DB : il **exécute des requêtes SQL** via le point injectable.

En gros :
1) **Détection** : il envoie des payloads pour voir si l’entrée est interprétée comme SQL (ex: différence vrai/faux, erreurs, temps).
2) **Fingerprint** : il identifie le DBMS (MySQL, Postgres…) grâce aux réponses (erreurs, fonctions, comportements).
3) **Canal d’extraction** : il choisit une méthode selon ce qui marche :
   - **UNION-based** (rapide si la réponse est affichée)
   - **Error-based** (si erreurs retournées)
   - **Boolean/time-based blind** (plus lent : il déduit bit par bit / caractère par caractère)
4) **Enumération** :
   - Sur MySQL/MariaDB, il interroge souvent `information_schema` (tables, colonnes…).
   - Sur d’autres DBMS, l’équivalent (`pg_catalog`, etc.).
5) **Extraction** :
   - il reconstruit les valeurs (rows/columns) et les écrit en fichiers (TXT/CSV) côté client.

### 15.7 Pourquoi ça marche même en “blind” ?
Si l’appli ne renvoie pas directement les résultats SQL, sqlmap peut :
- poser des questions “oui/non” (boolean-based) et déduire des caractères
- ou mesurer un temps de réponse (time-based) pour déduire la vérité d’une condition

C’est très lent… mais suffisant en lab.

### 15.8 Bonnes pratiques (même en lab)
- Toujours commencer par **détection minimale**
- Préférer `-r request.txt` si cookies/auth/redirections
- Documenter : injection point, DBMS, tables, preuve
- Ne pas utiliser d’options d’évasion avancées hors besoin (non couvert ici)

---

## 16) Contraintes “Holberton style” : scripts 1 ligne + newline

### 16.1 Script 1 ligne
Tu peux faire beaucoup avec des pipes `|` et redirections `>`.

Exemple :
```bash
nmap -n -Pn <IP> | grep -E "/tcp\s+open" | cut -d/ -f1 | paste -sd ", " - > 0-ports.txt
```

### 16.2 Newline final
Un fichier texte doit se terminer par `\n` :
- certains outils traitent mal la dernière ligne sinon
- ça évite des diffs bizarres

---

## 17) Troubleshooting rapide (les bugs classiques)

### 17.1 `active.hbtn` ne marche pas
- vérifier `/etc/hosts` (doublons)
- `getent hosts active.hbtn`

### 17.2 `nmap` semble “bloqué”
- c’est normal sur certains scans (timing / retransmissions)
- ajouter `-v` pour voir la progression
- éviter de mélanger `@IP` (ça n’existe pas en nmap)

### 17.3 `gobuster` “wildcard response”
- exclure longueur (`--exclude-length`)
- ou codes (`-s` + `-b ""`)

### 17.4 sqlmap ne trouve rien
- utiliser `-r request.txt` (cookies, POST, auth)
- suivre redirects (`-u` + `--batch` le propose)
- augmenter légèrement `--level=3 --risk=2` (lab)

---

## 18) Cheat sheet (résumé opérationnel)

### Connectivité
```bash
ping -c 1 <IP>
traceroute <IP>
```

### Ports
```bash
nmap -n -Pn <IP>
nmap -n -Pn -sV -p <PORTS> <IP>
```

### Web
```bash
curl -I http://<HOST>/
whatweb http://<HOST>/
gobuster dir -u http://<HOST>/ -w /usr/share/wordlists/dirb/common.txt
nikto -h http://<HOST>
```

### DNS
```bash
dig A <DOMAIN>
amass enum -d <DOMAIN>
```

### sqlmap (workflow)
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch --dbs
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB>" --tables
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB>" -T "<T>" --columns
sqlmap -u "http://<TARGET>/page.php?id=1" --batch -D "<DB>" -T "<T>" -C "col1,col2" --dump
```

---
