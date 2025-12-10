Voici une **fiche de révision complète**, claire, ultra‐structurée et directement prête à coller dans Notion.
Elle regroupe **toutes les commandes essentielles** pour la cybersécurité :

* 🔍 Reconnaissance passive
* 🌐 Reconnaissance active
* 🛡️ Audit, analyse, durcissement & protection système

Chaque commande contient :

* **Ce qu’elle fait**
* **Pourquoi l’utiliser**
* **Ce qu’elle permet de découvrir**
* **Exemples concrets**

---

# 🟦 **📚 FICHE DE RÉVISION — COMMANDES ESSENTIELLES CYBERSÉCURITÉ**

*(Reconnaissance passive / active / audit système)*

---

# 🟪 1) 🔍 RECONNAISSANCE PASSIVE

*(collecte d’informations sans interagir directement avec la cible)*

---

## 🟩 **1.1. `whois` — Informations sur les propriétaires d’un domaine**

### 🎯 Rôle

Interroger les bases d’enregistrement pour obtenir :

* propriétaire du domaine
* contacts administratifs / techniques
* registrar, pays
* serveurs DNS et dates importantes

### 👍 Pourquoi l'utiliser

* identifier l’organisation derrière un domaine
* trouver d’autres domaines liés
* cartographier l’infrastructure administrative (registrar, DNS, etc.)

### 🔎 Informations récupérables

* Registrant/Admin/Tech
* Email de contact (parfois masqué GDPR)
* Nameservers
* Vieillesse du domaine (peut indiquer des scams récents)

### 🧪 Exemple

```bash
whois holbertonschool.com
```

---

## 🟩 **1.2. `dig` — Analyse DNS avancée (A, MX, NS, TXT, TXT SPF, DKIM, DMARC…)**

### 🎯 Rôle

Interroger directement des serveurs DNS pour obtenir les enregistrements détaillés.

### 👍 Pourquoi l'utiliser

* connaître l’infrastructure mail (MX, SPF, DKIM, DMARC)
* trouver les sous-domaines
* vérifier la configuration DNS d’un domaine
* OSINT essentiel pour analyser une entreprise

### 🔎 Informations récupérables

* A / AAAA : adresses IP
* MX : serveurs mail → important pour attaques mail / protection
* NS : architecte DNS du domaine
* SOA : informations internes
* TXT : SPF / Google-site verification
* `_dmarc.` : politique DMARC

### 🧪 Exemples

```bash
dig holbertonschool.com A
dig holbertonschool.com MX
dig TXT holbertonschool.com
dig _dmarc.holbertonschool.com TXT
dig @8.8.8.8 github.com A
```

---

## 🟩 **1.3. `nslookup` — Résolution DNS simple**

### 🎯 Rôle

Résoudre un domaine vers une IP ou inversement.

### 👍 Pourquoi l'utiliser

* alternative rapide à `dig`
* bon pour vérifier une résolution DNS ou un MX

### 🔎 Exemple

```bash
nslookup holbertonschool.com
nslookup -type=MX holbertonschool.com
```

---

## 🟩 **1.4. `host` — Résolution DNS rapide**

### 🎯 Rôle

Interroger un enregistrement spécifique.

### Exemple

```bash
host -t mx holbertonschool.com
host google.com
```

---

## 🟩 **1.5. `subfinder` / `amass` — Découverte passive de sous-domaines**

### 🎯 Rôle

Énumérer les sous-domaines d’un domaine via OSINT.

### 👍 Pourquoi l’utiliser

* découvrir surfaces d’attaque cachées (admin., api., dev., staging.)
* trouver des environnements internes exposés

### 🔎 Exemple

```bash
subfinder -d holbertonschool.com
amass enum -passive -d holbertonschool.com
```

---

## 🟩 **1.6. `curl -I` et `wget` (headers HTTP)**

### 🎯 Rôle

Récupérer les en-têtes HTTP **sans toucher au contenu**, pour reconnaissance passive minimale.

### 👍 Pourquoi l’utiliser

* voir les technologies : nginx, Apache, cloudflare
* détecter des redirections
* voir des bannières intéressantes

### 🧪 Exemple

```bash
curl -I https://holbertonschool.com
```

---

## 🟩 **1.7. Services OSINT : Shodan, Censys, DNSDumpster**

### Shodan.io

→ Permet de voir ce que le monde entier voit d’un serveur cible.
Ports, services, versions, vulnérabilités connues.

### DNSDumpster

→ Cartographie graphique DNS : MX, NS, sous-domaines, technologies.

---

# 🟥 2) 🔥 RECONNAISSANCE ACTIVE

*(interactions directes avec la cible : scans, requêtes…)*
⚠️ *À faire uniquement en environnement légal et autorisé.*

---

## 🟩 **2.1. `nmap` — Le scanner réseau incontournable**

### 🎯 Rôle

Découvrir les ports ouverts, services, versions, OS fingerprint.

### 👍 Pourquoi l'utiliser

* cartographier la surface d’attaque
* détecter services obsolètes
* trouver des portes d’entrée possibles

### 🔎 Informations récupérables

* ports ouverts
* versions exactes d’Apache, SSH, FTP
* systèmes d’exploitation
* scripts NSE (failles connues)

### 🧪 Exemples utiles

Scan rapide :

```bash
nmap 192.168.1.10
```

Scan intensif :

```bash
nmap -sV -A 192.168.1.10
```

Scan furtif (SYN scan) :

```bash
nmap -sS 192.168.1.10
```

Découverte du réseau local :

```bash
nmap -sn 192.168.1.0/24
```

---

## 🟩 **2.2. `ss` / `netstat` — Voir les connexions réseau locales**

### 🎯 Rôle

Lister les sockets, connexions actives, ports ouverts.

### 👍 Pourquoi l’utiliser

* comprendre quels services tournent
* trouver des backdoors locales
* analyser des connexions suspectes (reverse shell, RAT…)

### 🔎 Informations récupérables

* ports en écoute (`LISTEN`)
* connexions externes (`ESTABLISHED`)
* processus associés (PID)

### 🧪 Exemples

```bash
ss -tunap
ss -atpn
netstat -tulpn
```

---

## 🟩 **2.3. `tcpdump` — Sniffer réseau en CLI**

### 🎯 Rôle

Observer les paquets en temps réel.

### 👍 Pourquoi l'utiliser

* analyser trafic suspect
* comprendre un protocole
* voir les handshakes TCP
* identifier un exfiltration de données

### 🔎 Informations récupérables

* IP sources / destinations
* ports
* flags TCP (SYN, ACK, PSH…)
* données brutes (mode verbose)

### 🧪 Exemples

Sniffer tout :

```bash
tcpdump -i eth0
```

Sniffer port 80 :

```bash
tcpdump -i eth0 port 80
```

Voir seulement les IP :

```bash
tcpdump -n
```

---

## 🟩 **2.4. `hping3` (avancé)**

Pour tester :

* firewall
* filtrage
* comportements TCP/ICMP

Exemple :

```bash
hping3 -S -p 80 192.168.1.10
```

---

# 🟩 **2.5. `traceroute` / `mtr` — Analyse du chemin réseau**

Pour analyser la route entre ton host et un serveur.

---

# 🟦 3) 🛡️ PROTECTION / AUDIT / DURCISSEMENT

*(ce que tu dois connaître comme admin ou analyst)*

---

## 🟩 **3.1. `ufw` — Firewall simple pour Linux**

### 🎯 Rôle

Configurer facilement des règles de firewall.

### 👍 Pourquoi

* pare-feu simple et lisible
* indispensable pour sécuriser un serveur

### 🧪 Exemples

```bash
ufw status verbose
ufw allow 22/tcp
ufw deny 3389
ufw enable
```

---

## 🟩 **3.2. `iptables` — Firewall avancé (netfilter)**

### 🎯 Rôle

Contrôle total du trafic réseau au niveau kernel.

### 👍 Pourquoi

* règles complexes
* chaînes INPUT / OUTPUT / FORWARD
* gestion de tables FILTER, NAT, RAW…

### 🧪 Exemples

Lister les règles :

```bash
sudo iptables -L -v -n
```

Liste table **security** (souvent demandée en exo Holberton) :

```bash
sudo iptables -t security -L -v
```

---

## 🟩 **3.3. `lynis` — Audit de sécurité système**

### 🎯 Rôle

Scanner un système Linux et identifier failles / mauvaises configs.

### 👍 Pourquoi

* indispensable pour un audit système
* donne un score de sécurité
* exploite plus de 200 tests

### 🧪 Exemple

```bash
sudo lynis audit system
```

---

## 🟩 **3.4. `fail2ban` — Protection anti-bruteforce**

### 🎯 Rôle

Bloquer automatiquement les IP qui font du bruteforce SSH / services.

Exemples :

```bash
fail2ban-client status
fail2ban-client status sshd
```

---

## 🟩 **3.5. `systemctl` / `journalctl` — Surveillance système**

### 🎯 Rôle

Lister services suspects & logs.

Exemples :

```bash
systemctl list-units --type=service
journalctl -u ssh --since "10 min ago"
```

---

# 🟪 4) 📘 TABLEAU FINAL DE SYNTHÈSE (PARFAIT POUR NOTION)

| Commande     | Catégorie | Rôle               | Ce que tu apprends              | Exemples              |
| ------------ | --------- | ------------------ | ------------------------------- | --------------------- |
| whois        | Passive   | Info domaine       | propriétaires, dates, registrar | `whois domain.com`    |
| dig          | Passive   | DNS complet        | MX, NS, SPF, DMARC              | `dig domain MX`       |
| nslookup     | Passive   | Résolution DNS     | IP ↔ Domaine                    | `nslookup domain`     |
| host         | Passive   | Lookup rapide      | A, MX, NS                       | `host -t mx domain`   |
| subfinder    | Passive   | Sous-domaines      | surfaces d’attaque              | `subfinder -d domain` |
| curl -I      | Passive   | Headers            | tech web (nginx, Apache)        | `curl -I site.com`    |
| nmap         | Active    | Scan réseaux       | ports, versions, OS             | `nmap -sV -A host`    |
| ss / netstat | Active    | Connexions locales | ports ouverts, processus        | `ss -tuna`            |
| tcpdump      | Active    | Sniffing           | paquets, flags, IP              | `tcpdump -i eth0`     |
| traceroute   | Active    | Route du trafic    | latence, hops                   | `traceroute host`     |
| ufw          | Audit     | Firewall simple    | règles d’accès                  | `ufw status`          |
| iptables     | Audit     | Firewall kernel    | tables NAT/FILTER/SECURITY      | `iptables -L -v`      |
| lynis        | Audit     | Scan sécurité      | configuration système           | `lynis audit system`  |

---

# 🟦 Si tu veux, je peux aussi te générer :

* une **mindmap** visuelle des commandes
* une **fiche séparée pour chaque outil**
* un **plan de cours complet cybersécurité réseau**
* des **exos pratiques** basés sur ces commandes

Souhaites-tu une version *résumée*, *PDF*, ou *encore plus détaillée* ?
