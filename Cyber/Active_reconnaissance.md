# Active Reconnaissance — Cours ultra complet (Kali Linux)

> **Contexte** : ce cours est conçu pour un **environnement de lab autorisé** (votre cible **cyber_netsec_0x02**).  
> **Règle d’or** : on ne scanne / teste **que** des machines pour lesquelles on a une autorisation explicite.

---

## 0) Objectifs pédagogiques (à savoir expliquer sans Google)

À la fin, vous devez pouvoir expliquer clairement :

- Ce qu’est la **reconnaissance active**
- Pourquoi elle est critique en cybersécurité
- Comment **Wappalyzer** aide à profiler une appli web
- Ce qu’est la **DNS enumeration**
- Comment **énumérer SMTP** en CLI
- Comment faire du **OS fingerprinting** (et ses limites)
- Ce qu’est **sqlmap** et comment l’utiliser **de façon responsable** en lab

---

## 1) Reconnaissance : le “repérage” avant l’action

### 1.1 Reconnaissance passive vs active
- **Passive recon** : vous collectez de l’info **sans toucher la cible** directement (OSINT, fuites, moteurs de recherche, archives…).
- **Active recon** : vous envoyez **des paquets/requêtes** à la cible pour obtenir des réponses (ICMP, TCP, HTTP, SMTP…).  
  ➜ La cible (ou ses logs/IDS) **peut vous voir**.

**Analogie** :
- Passive = regarder la maison depuis la rue + chercher des infos publiques.
- Active = sonner à la porte, tester la poignée, regarder par la fenêtre. Ça laisse des traces.

### 1.2 Pourquoi c’est important ?
La recon active sert à :
- **Cartographier** : IP, ports, services, versions, techno web
- **Réduire l’incertitude** : “Qu’est-ce qui tourne vraiment ?”
- **Prioriser le risque** : services exposés, versions obsolètes, endpoints sensibles
- **Préparer la phase suivante** : tests, durcissement, remédiation, pentest autorisé

---

## 2) Accéder et lancer une machine cible (lab)

Selon votre environnement, la “cible” peut être :
- une VM locale (VirtualBox/VMware)
- une machine de lab distante (VPN)
- une instance cloud isolée

### 2.1 Checklist avant de commencer
1. **Vous connaissez l’IP/hostname** de la cible (ex: `cyber_netsec_0x02`)
2. Vous êtes sur Kali et votre réseau est prêt (VPN/bridge/nat configuré)
3. Vous notez : date/heure, cible, scope, objectifs (utile pour un rapport)

### 2.2 Validation de connectivité (minimum)
```bash
ip a; ip r
ping -c 1 <TARGET>
```
Si `ping` ne répond pas, ça ne veut pas dire “down” (ICMP peut être filtré). On continuera avec du TCP.

### 2.3 Bonne pratique : fixer un nom local (optionnel)
Dans un lab, c’est pratique de mapper un hostname :
```bash
echo "<IP> cyber_netsec_0x02" | sudo tee -a /etc/hosts
```

---

## 3) Outils & mindset : une méthode simple

### 3.1 Méthode “4 passes”
1) **Reachability** : la cible répond-elle à quelque chose ?
2) **Découverte** : quels ports/services sont ouverts ?
3) **Identification** : quelles versions/technos ?
4) **Approfondissement ciblé** : DNS/SMTP/HTTP/OS fingerprinting, etc.

### 3.2 Un scan propre = précis + modéré
- Évitez de spammer (bruit + faux positifs + blocage)
- Sauvegardez vos résultats (`-oN`, `-oX`, `-oG`)
- Notez ce que vous faites (même en CTF/lab)

---

## 4) Ping (ICMP) : “Es-tu là ?”

### 4.1 À quoi sert ping ?
- Tester la **latence** et la **reachability** via ICMP Echo Request/Reply.
- Donner une première idée de la stabilité réseau.

### 4.2 Commandes utiles
```bash
ping -c 4 <TARGET>          # 4 paquets
ping -c 2 -W 1 <TARGET>     # timeout court
ping -c 5 -i 0.2 <TARGET>   # intervalle (attention au bruit)
```

### 4.3 Limites
- ICMP peut être **bloqué** : ping fail ≠ machine down.
- Certains systèmes répondent différemment selon firewall.

---

## 5) Traceroute : “Par où je passe ?”

### 5.1 Ce que fait traceroute
Traceroute tente d’identifier les **routeurs intermédiaires** entre vous et la cible, en jouant sur le champ **TTL** (Time To Live).

### 5.2 Commandes utiles
```bash
traceroute <TARGET>         # classique
traceroute -n <TARGET>      # pas de résolution DNS (plus rapide)
traceroute -T <TARGET>      # TCP SYN (utile si UDP/ICMP filtrés)
```

### 5.3 Interprétation rapide
- Des `* * *` = pas de réponse (filtrage, rate limit…)
- Un hop très lent = congestion/filtrage/route longue
- En lab, ça sert surtout à valider “je passe bien par le réseau attendu”.

---

## 6) Nmap : l’outil central d’active recon

### 6.1 Concepts clés
- **Port** : “porte” d’entrée vers un service (HTTP, SSH, SMTP…)
- **État** : open / closed / filtered
- **Service** : ce qui répond sur ce port
- **Version** : info critique (vulnérabilités liées à version)

### 6.2 Les “Top commandes Nmap” (vraiment utiles)
> Adaptez les timings au lab. Plus agressif = plus bruyant.

**1) Découverte simple**
```bash
nmap <TARGET>
```

**2) Scan des 1000 ports TCP “classiques” + output**
```bash
nmap -oN scan.txt <TARGET>
```

**3) Tous les ports TCP**
```bash
nmap -p- <TARGET>
```

**4) Service / version detection**
```bash
nmap -sV <TARGET>
```

**5) Default scripts (NSE) + version**
```bash
nmap -sC -sV <TARGET>
```

**6) “Aggressive” (OS detect + traceroute + scripts + versions)**
```bash
nmap -A <TARGET>
```

**7) OS fingerprinting**
```bash
sudo nmap -O <TARGET>
```

**8) UDP (plus lent, souvent filtré)**
```bash
sudo nmap -sU --top-ports 50 <TARGET>
```

### 6.3 Options ultra fréquentes
- `-Pn` : ne pas ping avant (utile si ICMP bloqué)
- `-T0..T5` : timing (T3 par défaut, T4 souvent OK en lab)
- `--open` : n’afficher que les ports ouverts
- `-vv` : plus verbeux
- `-oN/-oX/-oG` : outputs (normal / XML / grepable)

Exemple “propre” :
```bash
nmap -Pn -T4 --open -sC -sV -oN nmap_full.txt <TARGET>
```

### 6.4 Lire un résultat Nmap (mini guide)
- **PORT / STATE / SERVICE / VERSION** = base
- Si `http` détecté → on part sur du web fingerprinting (Wappalyzer + curl)
- Si `smtp` → banner grabbing + énumération SMTP
- Si `mysql/postgresql` → attention, ne tentez pas d’accès non autorisé (en lab seulement)

---

## 7) Banner grabbing : identifier un service “à la main” (telnet / netcat)

### 7.1 Pourquoi c’est utile ?
Avant d’automatiser, savoir “parler” au service aide à :
- confirmer que le port n’est pas un faux positif
- voir un **banner** (ex: “OpenSSH_8.4”, “nginx/1.18”)
- comprendre le protocole

### 7.2 Telnet (simple, mais ancien)
Telnet peut ouvrir une connexion TCP et afficher ce que le service renvoie.
```bash
telnet <TARGET> 80
telnet <TARGET> 25
```
> Telnet est **non chiffré** et obsolète pour l’admin, mais pratique en lab pour tester une connexion.

### 7.3 Netcat (nc) : le couteau suisse
```bash
nc -vz <TARGET> 80          # test de connectivité (verbose + zero-I/O)
nc <TARGET> 80              # se connecter
printf "HEAD / HTTP/1.0\r\n\r\n" | nc <TARGET> 80
```

---

## 8) Web fingerprinting (Wappalyzer + CLI)

### 8.1 Wappalyzer : à quoi ça sert ?
Wappalyzer détecte des technologies côté web :
- serveur web (nginx, Apache)
- frameworks (Django, React, Laravel…)
- CMS (WordPress…)
- analytics, CDNs, libs JS…

**Pourquoi c’est de l’active recon ?**  
Parce que votre navigateur fait des requêtes HTTP vers la cible.

### 8.2 Comment l’utiliser efficacement
- Ouvrez le site cible dans le navigateur
- Regardez Wappalyzer : techno détectées
- Croisez avec une vérification CLI :

```bash
curl -I http://<TARGET>/
curl -s http://<TARGET>/ | head
whatweb http://<TARGET>/    # souvent dispo sur Kali
```

### 8.3 Limites de Wappalyzer
- Détection basée sur **signatures** → faux positifs possibles
- Certaines technos sont masquées (headers modifiés, reverse proxies)

---

## 9) DNS Enumeration : “Quels noms, quelles zones, quels enregistrements ?”

### 9.1 C’est quoi ?
La DNS enumeration consiste à découvrir :
- les enregistrements **A/AAAA** (IP)
- **CNAME** (alias)
- **MX** (serveurs mail)
- **NS** (serveurs DNS)
- **TXT** (SPF, DKIM… parfois infos utiles)
- des **sous-domaines** (subdomain discovery)

### 9.2 Outils CLI essentiels
**dig**
```bash
dig <domain> A
dig <domain> MX
dig <domain> NS
dig <domain> TXT
dig @<dns_server> <domain> ANY
```

**host / nslookup**
```bash
host <domain>
nslookup <domain>
```

### 9.3 Zone transfer (AXFR) — concept important
Si un serveur DNS est mal configuré, il peut autoriser un **transfert de zone**.
```bash
dig @<ns-server> <domain> AXFR
```
> À faire **uniquement** en lab autorisé. Très révélateur : liste complète des noms.

### 9.4 Subdomain enumeration (idée)
En lab, on peut tester des sous-domaines communs :
- `dev`, `staging`, `api`, `admin`, `mail`, `vpn`, `intranet`

Approche simple (concept) :
- wordlist + résolution DNS
- outils : `dnsrecon`, `fierce` (souvent disponibles sur Kali)

---

## 10) Énumération SMTP (mail) en ligne de commande

### 10.1 Pourquoi c’est intéressant ?
SMTP peut révéler :
- le **banner** (produit + version)
- les commandes supportées (`EHLO`)
- parfois des comportements d’énumération d’utilisateurs (souvent désactivé)

### 10.2 Rappels rapides SMTP
- Port classique : `25` (serveur à serveur)
- Souvent aussi : `587` (submission), `465` (smtps legacy)

### 10.3 Banner grabbing SMTP avec nc/telnet
```bash
nc <TARGET> 25
# ou
telnet <TARGET> 25
```

Ensuite (manuellement) :
- tapez `EHLO test.local`
- observez les extensions (SIZE, STARTTLS, AUTH, etc.)
- puis `QUIT`

> **Ne forcez pas d’auth** hors lab. Ici l’objectif est l’identification.

### 10.4 Nmap NSE pour SMTP
Nmap a des scripts utiles (selon disponibilité) :
```bash
nmap -p 25,465,587 --script smtp-commands <TARGET>
nmap -p 25,465,587 --script smtp-enum-users <TARGET>
```
> `smtp-enum-users` peut être intrusif. En lab, ok si demandé, sinon restez sur `smtp-commands`.

---

## 11) OS Fingerprinting : “Quel OS est en face ?”

### 11.1 C’est quoi ?
L’OS fingerprinting tente d’inférer l’OS (Linux/Windows/routeur…) à partir de :
- réponses TCP/IP (TTL, Window Size, flags)
- comportement réseau
- signatures connues

### 11.2 OS fingerprinting avec Nmap
```bash
sudo nmap -O <TARGET>
nmap -A <TARGET>            # inclut OS detect (si possible)
```

### 11.3 Limites (important à expliquer)
- Si la cible est derrière un **firewall** ou un **proxy**, la signature peut être trompée
- Les signatures ne sont jamais 100% fiables
- Un service containerisé peut brouiller les pistes

**Réflexe pro** : confirmer via indices croisés  
(OS guess + bannières + comportement services + TTL observé).

---

## 12) sqlmap : automatiser les tests d’injection SQL (en lab autorisé)

### 12.1 C’est quoi sqlmap ?
`sqlmap` est un outil qui :
- détecte automatiquement certaines injections SQL
- identifie le DBMS (MySQL, PostgreSQL…)
- peut (en contexte autorisé) automatiser l’exploration

> Dans un cours, on l’utilise surtout pour **tester** et **démontrer** une vulnérabilité sur une cible **autorisée**.

### 12.2 Pré-requis conceptuels
- Comprendre l’URL et ses paramètres : `page.php?id=1`
- Comprendre qu’une injection arrive quand l’entrée utilisateur est concaténée à une requête SQL sans protection
- Notions de base : validation, requêtes préparées, ORM, WAF, logs

### 12.3 Usage responsable (focus détection & identification)
**Tester une URL avec paramètres** :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch
```

**Choisir un paramètre précis** :
```bash
sqlmap -u "http://<TARGET>/page.php?id=1&cat=2" -p id --batch
```

**Augmenter la profondeur des tests (lab)**
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --level=3 --risk=2 --batch
```

**Passer des cookies (si session)**
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --cookie="PHPSESSID=..." --batch
```

### 12.4 Bonnes pratiques avec sqlmap
- Garder une trace : `-v 1..6`, outputs, notes
- Ne pas lancer “tout et n’importe quoi” : ciblez le paramètre
- Comprendre la preuve : *qu’est-ce qui prouve l’injection ?*
- Côté défense : recommander requêtes préparées + validation + least privilege DB

> ⚠️ Je n’inclus volontairement pas ici les options orientées exfiltration (`--dump`) ou contournements (`--tamper`). En lab, votre encadrant vous dira exactement ce qui est attendu.

---

## 13) Exigences projet : scripts **1 ligne**, newline final, README

### 13.1 Scripts 1 ligne : comment y arriver ?
Objectif : `wc -l file` doit afficher `1`.

Techniques :
- Chaîner avec `;` ou `&&`
- Utiliser des pipes `|`
- Utiliser des substitutions `$(...)`

Exemples (patterns) :
```bash
command1; command2
command1 && command2
command | grep ... | awk ...
```

### 13.2 “Pourquoi un fichier doit finir par une newline ?”
Parce que :
- beaucoup d’outils UNIX attendent une fin de ligne pour traiter correctement la dernière ligne
- POSIX et les conventions texte supposent qu’une ligne se termine par `\n`
- éviter des comportements bizarres avec `cat`, `diff`, `grep`, git, etc.

En pratique : ajoutez un retour à la ligne à la fin (Enter).

### 13.3 README.md obligatoire
Votre README doit expliquer :
- objectif du projet
- outils utilisés (ping/traceroute/nmap…)
- comment exécuter vos scripts
- exemples de sortie (si demandé)
- avertissement “authorized lab only”

---

## 14) Cheat sheet rapide (résumé opérationnel)

### Reachability
```bash
ping -c 1 <TARGET>
nmap -Pn -p 80,443 <TARGET>
```

### Routing
```bash
traceroute -n <TARGET>
traceroute -T -n <TARGET>
```

### Nmap (bases)
```bash
nmap -Pn --open -sC -sV -oN out.txt <TARGET>
nmap -p- -T4 <TARGET>
sudo nmap -O <TARGET>
```

### Web fingerprinting
```bash
curl -I http://<TARGET>/
whatweb http://<TARGET>/
```

### DNS
```bash
dig <domain> A
dig <domain> MX
dig @<ns> <domain> AXFR
```

### SMTP
```bash
nc <TARGET> 25
nmap -p 25,465,587 --script smtp-commands <TARGET>
```

### sqlmap (détection)
```bash
sqlmap -u "http://<TARGET>/page.php?id=1" --batch
sqlmap -u "http://<TARGET>/page.php?id=1" -p id --level=3 --risk=2 --batch
```

---

## 15) Mini-exercices (pour apprendre, pas pour tricher)

1) Explique la différence passive/active en 3 phrases.
2) Sur votre cible, faites un scan Nmap **propre** et résumez :
   - ports ouverts
   - services + versions
   - hypothèses sur l’OS (et pourquoi c’est une hypothèse)
3) Identifiez une techno web avec Wappalyzer **puis** confirmez au moins un indice via `curl -I`.
4) Faites un banner grabbing SMTP (si port ouvert) et listez les commandes d’extension vues après `EHLO`.
5) Explique ce que `--level` et `--risk` changent dans sqlmap (sans parler d’exfiltration).

---

## 16) Glossaire rapide
- **Enumeration** : découverte détaillée (users, services, noms, endpoints…)
- **Banner** : message d’accueil d’un service, souvent révélateur
- **Fingerprinting** : identification par signatures/comportement
- **NSE** : Nmap Scripting Engine (scripts de détection/énumération)
- **Scope** : périmètre autorisé (IPs, domaines, temps, méthodes)

---

### Fin
Si tu veux, envoie-moi ensuite :
- l’IP/hostname exact de `cyber_netsec_0x02`
- 1 résultat Nmap (même partiel)
et je t’aide à **interpréter** proprement (services → hypothèses → prochaines commandes).
