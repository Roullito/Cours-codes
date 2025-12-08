# Passive Reconnaissance & Target Machines  
_Holberton School – Linux Security / Cybersecurity Basics_

---

## 0. Avertissement légal & éthique

Ce que tu vas apprendre ici permet de **collecter des informations** sur des systèmes et des organisations.  
Ces techniques sont utilisées :

- par les **analystes sécurité / pentesters** pour tester la sécurité
- par les **administrateurs** pour mieux connaître leur infrastructure
- mais aussi par des **attaquants**…

👉 **Tu ne dois les utiliser QUE sur :**

- des machines **qui t’appartiennent**
- des machines / labos **explicitement destinés à l’entraînement** (exercices Holberton, TryHackMe, HackTheBox, etc.)
- des systèmes pour lesquels tu as un **accord écrit / contrat de pentest**

Toute utilisation non autorisée peut être **illégale**.

---

## 1. Introduction : Reconnaissance & rôle de la phase passive

En cybersécurité offensive, la première étape est presque toujours la **reconnaissance** (“recon”).

> **Reconnaissance = récolter un maximum d’informations sur une cible AVANT toute attaque.**

Dans le **Unified Kill Chain** (modèle qui décrit toutes les étapes d’une attaque moderne), la recon correspond aux phases de :

- _Initial reconnaissance_
- _Weaponization & Delivery preparation_
- etc.

On distingue deux grands types :

- **Reconnaissance passive**
- **Reconnaissance active**

### 1.1. Reconnaissance passive (ce module)

- Tu **ne touches pas directement** la cible.
- Tu interroges des **sources publiques** :  
  moteurs de recherche, WHOIS, DNS publics, Shodan, archives, réseaux sociaux, etc.
- Objectif : connaître au maximum la cible, **sans générer de logs** visibles sur ses serveurs.

**Exemples :**

- `whois holbertonschool.com`
- `dig holbertonschool.com`
- Recherche Google avancée (Google Dorks)
- DNSDumpster pour cartographier les sous-domaines
- Shodan.io pour voir si des IP associées à une organisation exposent des services

### 1.2. Reconnaissance active (pour comparaison)

- Tu **envoies directement du trafic** à la cible.
- Exemple typique : `nmap`, `dirb`, `nikto`, bruteforce, etc.
- Ces actions sont **beaucoup plus visibles** : elles peuvent déclencher des alertes sur les IDS/IPS, WAF, logs serveurs, etc.

---

## 2. Rappel : qu’est-ce qu’on peut apprendre sur un serveur ?

Avant de parler d’outils, il faut comprendre **quels types d’infos** on cherche.

Avec des techniques majoritairement **passives**, on peut obtenir :

- **Sur le nom de domaine** (ex. `holbertonschool.com`) :
  - Propriétaire / organisation (parfois masqué)
  - Registrar, dates de création / expiration
  - Serveurs de noms (NS)
- **Sur le DNS** :
  - Adresse(s) IP (A / AAAA) du site
  - Serveurs mail (MX)
  - Autres sous-domaines potentiels (dev, test, api, mail, vpn…)
- **Sur l’IP / l’hébergement** :
  - Fournisseur (ISP / cloud provider / hébergeur)
  - Localisation approximative
  - Ports / services connus (via index type Shodan)
- **Sur les services exposés** (via index publics) :
  - Version d’un serveur web (Apache, nginx…)
  - Bannières de certains services
- **Sur l’organisation** :
  - Emails publics
  - Noms de sous-domaines internes fuité dans des documents PDF, docx, etc.
  - Technologies utilisées (frameworks web, CMS, etc.)

Ces informations servent ensuite à :

- Prioriser les **angles d’attaque**.
- Repérer des **vieux systèmes** ou services oubliés.
- Construire une **carte de l’infrastructure**.

---

## 3. DNS Fundamentals : ce qui se passe quand tu tapes `www.holbertonschool.com`

### 3.1. Qu’est-ce qu’un serveur DNS ?

**DNS = Domain Name System**

> C’est l’“annuaire d’Internet” : il traduit les noms de domaine en adresses IP.

Un **serveur DNS** :

- reçoit une question : `Quelle est l’IP de www.holbertonschool.com ?`
- répond avec l’IP correspondante (ex. `15.161.34.42`).

Il existe plusieurs types de serveurs :

- **Résolveur récursif** (souvent celui de ton FAI ou 1.1.1.1 / 8.8.8.8)
- **Serveurs racine** (.)
- **Serveurs TLD** (.com, .fr, .org…)
- **Serveurs faisant autorité** (authoritative) pour un domaine donné

### 3.2. Que se passe-t-il quand on tape `www.holbertonschool.com` et qu’on appuie sur ENTER ?

Version simplifiée :

1. Tu tapes l’URL dans ton navigateur et presses **ENTER**.
2. Le système vérifie d’abord son **cache local DNS** (et le cache du navigateur).
3. Si l’IP n’est pas connue, il envoie une requête au **résolveur DNS récursif** (souvent celui configuré sur ta machine : `/etc/resolv.conf`).
4. Le résolveur :
   - demande aux **serveurs racine** : “Qui gère le TLD `.com` ?”
   - les serveurs racine renvoient les serveurs TLD `.com`.
   - le résolveur demande aux serveurs TLD `.com` : “Qui gère `holbertonschool.com` ?”
   - les TLD renvoient les **serveurs de noms autoritatifs** pour `holbertonschool.com`.
   - le résolveur demande ensuite à ces serveurs autoritatifs : “Quelle est l’IP de `www.holbertonschool.com` ?”
5. Les serveurs autoritatifs renvoient un enregistrement **A** (IPv4) ou **AAAA** (IPv6).
6. Le résolveur met la réponse en **cache** pendant la durée du **TTL** (Time To Live).
7. La réponse est renvoyée à ta machine.
8. Ton navigateur ouvre une **connexion TCP** (et souvent TLS) à l’adresse IP.
9. Il envoie une requête HTTP/HTTPS, puis affiche la page.

Tu peux visualiser une partie de ce processus avec :

- `dig www.holbertonschool.com`
- `dig +trace www.holbertonschool.com` (pour voir le chemin complet des serveurs)

---

## 4. WHOIS : retrouver le propriétaire d’un domaine

### 4.1. Qu’est-ce que WHOIS ?

**WHOIS** est un protocole (décrit dans **RFC-3912**) et un service de base de données qui permet de récupérer les informations d’enregistrement d’un :

- **nom de domaine** (`example.com`)
- ou parfois d’une **adresse IP / plage IP**

Tu peux y trouver (selon la politique de confidentialité du registrar) :

- Organisation / personne responsable du domaine
- Registrar (OVH, Gandi, Namecheap, etc.)
- Dates de création, mise à jour, expiration
- Contacts technique / administratif
- Serveurs de noms (NS)

### 4.2. Utilisation de `whois` en ligne de commande

Sur Kali :

```bash
whois holbertonschool.com
whois 15.161.34.42
```

Tu obtiendras un long bloc de texte contenant :

- des informations **sur le domaine**
- parfois des infos **sur la plage IP** (propriétaire du bloc d’IP, pays, etc.)

> ⚠️ De plus en plus de registrars masquent les infos personnelles (GDPR, privacy).

---

## 5. DNS Records & Outils (`dig`, `nslookup`, `dnslookup`)

### 5.1. Types principaux de DNS RECORDS

| Type   | Rôle                                           | Exemple                         |
|--------|------------------------------------------------|---------------------------------|
| A      | Adresse IPv4 d’un nom de domaine               | `www → 93.184.216.34`          |
| AAAA   | Adresse IPv6                                   | `www → 2606:2800:...`          |
| CNAME  | Alias vers un autre nom                        | `www → webserver.example.com`  |
| MX     | Serveur(s) de mail                             | `MX → mail.example.com`        |
| NS     | Serveurs de noms autoritatifs                  | `NS → ns1.provider.net`        |
| TXT    | Texte libre (SPF, DKIM, vérifs Google, etc.)   | `TXT → "v=spf1 ..."`           |
| SOA    | “Start of Authority” : infos zone (admin, TTL) |                                 |
| PTR    | Reverse DNS (IP → nom de domaine)              | `1.2.3.4 → server.example.com` |
| SRV    | Service spécifique (ex. `_sip._tcp`, etc.)     | emplacements de services       |
| CAA    | Indique quelles CA peuvent émettre des certifs | `CAA → letsencrypt.org`        |

Connaître ces records est **fondamental** en recon.

### 5.2. `dig` : DNS lookup avancé

**`dig`** (Domain Information Groper) est un outil très puissant pour interroger les DNS.

Quelques commandes de base :

```bash
dig holbertonschool.com
dig www.holbertonschool.com A
dig holbertonschool.com MX
dig holbertonschool.com NS
dig +short holbertonschool.com
```

- `+short` : sortie simplifiée (juste les infos importantes).
- Tu peux interroger un serveur DNS précis :

```bash
dig @8.8.8.8 holbertonschool.com A
```

- Pour suivre tout le chemin de résolution :

```bash
dig +trace www.holbertonschool.com
```

### 5.3. `nslookup` / `dnslookup`

**`nslookup`** est un autre outil pour interroger le DNS (plus ancien, mais encore très répandu).

```bash
nslookup holbertonschool.com
nslookup -type=MX holbertonschool.com
```

**`dnslookup`** (selon les distributions) est parfois juste un alias / wrapper.  
Dans la pratique, pour la recon moderne, on préfère souvent **`dig`** qui est plus complet et scriptable.

---

## 6. Trouver des sous-domaines & cartographier l’infrastructure DNS

### 6.1. Pourquoi les sous-domaines sont importants ?

Les sous-domaines peuvent révéler :

- des environnements **de test / staging** (`staging.example.com`)
- des applications internes exposées par erreur (`intranet.example.com`)
- des outils d’admin (`vpn.example.com`, `admin.example.com`, `dev.example.com`)
- des services métiers (`api.example.com`, `pay.example.com`)

Un attaquant va souvent chercher :

1. **Un maximum de sous-domaines**
2. Puis des applications/services vulnérables sur ces sous-domaines

### 6.2. DNSDumpster

**DNSDumpster** est un service web qui :

- prend un **nom de domaine** en entrée
- effectue diverses requêtes DNS et OSINT
- retourne :
  - des sous-domaines trouvés
  - leurs IP
  - les serveurs MX / NS
  - parfois un **graph** de l’infrastructure

Usage (côté attaquant/pentester) :

- Tu entres `example.com`
- Tu récupères une liste : `www.example.com`, `mail.example.com`, `vpn.example.com`, etc.
- Tu peux exporter les résultats pour les utiliser ensuite dans d’autres outils.

> DNSDumpster utilise lui-même des méthodes actives / passives, mais **toi**, tu restes dans une démarche **passive** car tu interroges uniquement **leurs** serveurs, pas la cible.

### 6.3. Subfinder : OSINT Subdomain Enumeration

**Subfinder** est un outil de subdomain enumeration (en Go) très utilisé en bug bounty.

> Il se concentre sur les **sources OSINT** (certificats, DNS publics, archives, etc.) :  
> tu restes donc en majorité dans une recon **passive** vis-à-vis de la cible.

Exemples (génériques) :

```bash
subfinder -d example.com
subfinder -d example.com -all
```

- `-d` : domaine
- `-all` : utiliser plus de sources (souvent plus lent mais plus complet)
- Tu peux sauvegarder les résultats :

```bash
subfinder -d example.com -o subdomains.txt
```

> ⚠️ Comme toujours : n’utilise ces outils que sur des domaines pour lesquels tu as **l’autorisation**.

---

## 7. Shodan.io : “Google pour les machines connectées”

### 7.1. Qu’est-ce que Shodan ?

**Shodan** est un moteur de recherche qui indexe :

- des **adresses IP** accessibles sur Internet
- les **ports ouverts**
- les **bannières** de services (bannières HTTP, bannières SSH, bannières FTP, etc.)
- parfois la **géolocalisation**, l’ISP, et quelques métadonnées

Shodan effectue lui-même le scan (côté Shodan, **actif**), mais toi :

- tu te contentes d’interroger leur **base de données**
- donc, pour toi, cette étape reste **passive** vis-à-vis de la cible

### 7.2. Quelques idées d’utilisation en recon passive

- Voir si une **IP** appartenant à une organisation expose :
  - un RDP ouvert
  - une interface d’administration web
  - un service industriel (SCADA, ICS, etc.) – d’un point de vue purement informationnel
- Cartographier les **technologies** utilisées :
  - types de serveurs web
  - certificats TLS

> ⚠️ Les requêtes Shodan peuvent rapidement devenir très puissantes. Reste dans une approche **d’observation**, toujours dans un cadre légal et pédagogique.

---

## 8. Google Hacking (Google Dorks)

### 8.1. Idée générale

**Google Hacking** consiste à utiliser les **opérateurs avancés de Google** pour retrouver des informations spécifiques exposées publiquement.

Exemples d’opérateurs :

- `site:example.com` → restreint la recherche à un domaine
- `filetype:pdf` → cherche un type de fichier
- `"exact phrase"` → correspondance exacte
- `intitle:login` → mots dans le titre de la page

Combiner ces opérateurs permet de :

- trouver des **documents PDF / DOCX** publics d’une organisation
- repérer des **pages d’admin** exposées (d’un point de vue OSINT / audit)

> ⚠️ On reste sur ce module dans une **explication conceptuelle**. En pratique, toute exploitation de résultats sensibles doit rester dans un **cadre légal / contractuel**.

---

## 9. Accéder et lancer les “Cybersecurity Target Machines”

Le sujet mentionne : **“Accessing and Launching Cybersecurity Target Machines”**.  
Concrètement, dans un cursus comme Holberton, ça veut dire :

- des **VM** spécialement préparées comme cibles
- parfois des **machines distantes** accessibles via **VPN** (type TryHackMe / HackTheBox)
- des **containers Docker** ou labs internes

### 9.1. Typologie des target machines

1. **Machines locales** (VM VirtualBox/VMware)  
   - Tu installes une VM vulnérable (Metasploitable, DVWA, etc.) sur ton PC.
   - Tu mets ta Kali et cette VM dans le même réseau (host-only / NAT + port forwarding).
2. **Machines distantes via VPN**  
   - Tu télécharges un fichier de configuration VPN.
   - Tu te connectes au réseau du labo.
   - On te donne une IP de cible (ex. `10.10.23.45`).
3. **Labs web / containers**  
   - L’énoncé fournit une **URL** ou une **IP** d’un service vulnérable.

### 9.2. Processus typique

1. **Lancer la target machine** (locale ou distante).
2. Vérifier la **connectivité** :
   - `ping <IP_de_la_cible>` (si ICMP autorisé)
   - `traceroute <IP>` (optionnel)
3. **Phase de recon PASSIVE** :
   - Regarder le nom de domaine (si fourni)
   - `whois`, `dig`, `nslookup`, subdomain enumeration, OSINT
4. **Ensuite seulement**, éventuellement, **recon active** (dans un labo prévu pour ça) :
   - `nmap`, `nikto`, `dirb`, etc.

L’idée du module est que tu sois à l’aise avec **toutes les étapes passives** avant de passer à l’attaque.

---

## 10. Active vs Passive Reconnaissance : différence claire

| Caractéristique              | Passive Reconnaissance                                      | Active Reconnaissance                        |
|-----------------------------|-------------------------------------------------------------|---------------------------------------------|
| Trafic vers la cible        | Aucun / très indirect (via tiers)                          | Oui, directement vers la cible              |
| Exemple d’outils            | WHOIS, `dig`, Shodan, DNSDumpster, Google, Subfinder       | `nmap`, `dirb`, `nikto`, bruteforce, etc.   |
| Visibilité pour la cible    | Faible (souvent invisible dans ses logs)                   | Élevée (logs, IDS, pare-feu)                |
| Risque juridique / détection| Plus faible (mais pas nul)                                 | Plus élevé                                  |
| Utilisation principale      | Comprendre l’organisation / surface d’attaque              | Tester les services / trouver des vulnérabilités |

> Règle d’or : **Toujours commencer par la reconnaissance passive**.  
> Elle donne un contexte précieux et réduit le bruit quand tu passeras à l’actif (dans un cadre légal).

---

## 11. Lien avec le projet Holberton (scripts Bash)

Le projet impose des **contraintes précises** sur tes scripts :

- Éditeurs autorisés : `vi`, `vim`, `emacs`
- **Kali Linux** comme environnement
- **Tous les scripts en Bash**
- **Tous les scripts font exactement 2 lignes** :
  1. Ligne 1 : `#!/bin/bash`
  2. Ligne 2 : la commande
- Tu dois remplacer la plage d’IP / domaine / etc. par **`$1`**.
- **`$1` doit être utilisé sans guillemets** (contrainte du projet).
- Tous les fichiers doivent :
  - se terminer par un **retour à la ligne**
  - être **exécutables** (`chmod +x script.sh`)
- **Betty style** (même si ici ce sont des scripts très simples)
- Un **README.md** est obligatoire à la racine du projet.

### 11.1. Patterns typiques de scripts

Sans donner de solutions exactes, la structure générale sera toujours :

- Ligne 1 : shebang
- Ligne 2 : appel de la commande d’OSINT avec `$1`

Exemples conceptuels :

- Script qui lance `whois` sur le domaine donné en argument
- Script qui lance `dig` avec un type d’enregistrement
- Script qui lance `subfinder` sur le domaine de `$1`
- Script qui appelle un outil de DNS lookup

> 👉 Ton job : connecter les **commandes vues dans ce cours** avec la **syntaxe des scripts Bash** imposée.

---

## 12. Checklist des objectifs du module

À la fin de ce module, tu dois être capable d’expliquer (et de démontrer) :

- [ ] **Ce qu’on peut apprendre sur un serveur** en recon passive
- [ ] **Ce qu’est un serveur DNS** et son rôle
- [ ] **Ce qui se passe quand tu tapes** `www.holbertonschool.com` puis ENTER
- [ ] **Comment trouver les infos de propriétaire d’un domaine** (WHOIS)
- [ ] **Ce qu’est `dig`** et l’utiliser pour interroger différents types de records
- [ ] **Ce qu’est `nslookup` / `dnslookup`** et leur usage de base
- [ ] **Les principaux types de DNS records** (A, AAAA, MX, NS, CNAME, TXT, SOA, PTR…)
- [ ] **Ce qu’est DNSDumpster** et comment l’utiliser pour cartographier une cible
- [ ] **Ce qu’est Shodan.io** et le type d’informations qu’il fournit
- [ ] **Comment trouver des sous-domaines** (subfinder, DNSDumpster, OSINT)
- [ ] **Ce qu’est subfinder** et sa place parmi les outils de recon
- [ ] **La différence entre reconnaissance active et passive** (avec exemples concrets)
- [ ] **Comment accéder / lancer des machines cibles** (VM, VPN, lab distant) dans un contexte de formation

---

## 13. Mini-quiz pour t’auto-évaluer

1. Est-ce que lancer `whois holbertonschool.com` est une action de recon **active** ou **passive** ? Pourquoi ?
2. Cite **3 types de DNS records** et explique “en français simple” à quoi ils servent.
3. À quel moment dans le processus “je tape une URL et j’appuie sur ENTER” intervient le **résolveur DNS récursif** ?
4. Donne un exemple de situation où **Shodan.io** te donne une info utile **sans que tu aies scanné la cible toi-même**.
5. Pourquoi les **sous-domaines** sont-ils une cible privilégiée pour les pentesters ?
6. Donne **2 exemples** d’outils plutôt passifs et **2 exemples** d’outils plutôt actifs.
7. Dans le cadre du projet, pourquoi est-il important d’utiliser **`$1`** à la place d’un domaine en dur dans les scripts ?

Si tu peux répondre à tout ça clairement, tu es prêt pour attaquer les **exercices** et les **scripts** de ce module.

---
