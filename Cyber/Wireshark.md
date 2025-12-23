# Wireshark — Cours ultra détaillé (Holberton • Network Security • 0x05)

> Objectif : comprendre **Wireshark** (capture + analyse), maîtriser les **filtres** (display/capture), et réussir les **8 tâches Holberton** en écrivant des filtres dans des fichiers `.txt`.

---

## Table des matières

1. [C’est quoi Wireshark ?](#cest-quoi-wireshark-)
2. [À quoi ça sert en cybersécurité](#à-quoi-ça-sert-en-cybersécurité)
3. [Installation sur Kali + droits](#installation-sur-kali--droits)
4. [Anatomie de l’interface Wireshark](#anatomie-de-linterface-wireshark)
5. [Workflow pro d’analyse](#workflow-pro-danalyse)
6. [Filtres : capture vs display](#filtres--capture-vs-display)
7. [Display filters : syntaxe + cheatsheet](#display-filters--syntaxe--cheatsheet)
8. [Lire et analyser un paquet (méthode)](#lire-et-analyser-un-paquet-méthode)
9. [Cas d’usage : reconnaître un scan Nmap](#cas-dusage--reconnaître-un-scan-nmap)
10. [Mini-glossaire](#mini-glossaire)

---

## C’est quoi Wireshark ?

Wireshark est un analyseur de trafic réseau (“packet analyzer”) qui permet de :

- **Capturer** des paquets qui passent sur une interface (Ethernet/Wi‑Fi/Loopback/…)
- **Décoder** (“dissect”) des protocoles (Ethernet, IP, TCP, UDP, TLS, DNS, HTTP, …)
- **Filtrer** et **corréler** des flux pour comprendre ce qui se passe réellement sur le réseau

Il s’appuie sur une bibliothèque de capture (pcap/libpcap) et stocke les captures dans des fichiers `.pcap` / `.pcapng`.

---

## À quoi ça sert en cybersécurité

### Côté défense (blue team / SOC)
- Comprendre un incident : exfiltration, malware, C2, scans, brute‑force…
- Détecter des patterns (ex. scans SYN, rafales DNS, connexions TLS suspectes)
- Reconstituer une timeline : “qui parle à qui, quand, comment”

### Côté audit / pentest (red team)
- Vérifier ce que ton outil envoie *réellement* (Nmap, curl, scripts)
- Valider un contournement (firewall, proxy, NAT)
- Comprendre pourquoi un scan “rate” (filtrage, MTU, ICMP bloqué, etc.)

⚠️ **Éthique & légal** : sniffer un réseau sans autorisation est illégal. Fais ça sur ton lab / réseau de cours.

---

## Installation sur Kali + droits

Commandes (comme dans l’énoncé) :

```bash
sudo add-apt-repository ppa:wireshark-dev/stable
sudo apt update
sudo apt install wireshark
sudo usermod -aG wireshark $USER
```

Redémarre ta session (ou reboot) pour que le groupe prenne effet.

Lancer Wireshark :

```bash
wireshark
```

**Astuce** : si tu captures sur une interface et que tu ne vois rien, teste en root :
```bash
sudo wireshark
```

---

## Anatomie de l’interface Wireshark

Wireshark (par défaut) est divisé en 3 zones :

1. **Packet List** (liste)
 - Colonnes : No., Time, Source, Destination, Protocol, Length, Info
2. **Packet Details** (détails)
 - Arbre des couches : Frame → Ethernet → IP → TCP/UDP → Application…
3. **Packet Bytes** (octets)
 - Hex + ASCII, utile pour voir payload / signatures

### Outils indispensables (menu)
- **Statistics → Conversations / Endpoints** : top IP/ports, super utile pour repérer un scan
- **Statistics → Protocol Hierarchy** : % par protocole (DNS/TCP/ICMP…)
- **Follow → TCP Stream / UDP Stream** : reconstituer une conversation
- **View → Name resolution** (optionnel) : pratique mais peut “polluer” si tu veux du brut

---

## Workflow pro d’analyse

### 1) Capturer proprement
- Choisis l’interface (ex : `eth0`, `wlan0`, `any`, `lo`)
- Mets un **capture filter** si tu veux limiter la capture (facultatif)
- Démarre la capture
- Lance ton action (ex : commande `nmap...`)
- Stop la capture
- Sauvegarde `.pcapng`

### 2) Filtrer intelligemment (display filter)
- Commence large : `ip` puis `tcp` puis `tcp.port==80` etc.
- Réduis progressivement jusqu’à isoler le pattern (scan, requêtes, handshake…)

### 3) Inspecter 1 paquet puis généraliser
- Clique un paquet “typique”
- Observe les champs clés (flags, ports, codes ICMP, MAC, TTL, options TCP…)
- Fais **Right click → Apply as Filter → Selected** sur un champ
- Ajuste (ajout de ports, AND/OR, parenthèses)

---

## Filtres : capture vs display

### Capture filters
- S’appliquent **avant** la capture (limitent ce qui est enregistré)
- Syntaxe **BPF/libpcap** (comme tcpdump)
- Exemples :
 - `host 10.0.0.5`
 - `tcp port 80`
 - `udp portrange 200-300`

### Display filters
- S’appliquent **après** (sur la capture déjà enregistrée)
- Syntaxe Wireshark (plus riche, basée sur les champs disséqués)
- Exemples :
 - `ip.addr == 10.0.0.5`
 - `tcp.flags.syn == 1 && tcp.flags.ack == 0`
 - `icmp.type == 3 && icmp.code == 3`

➡️ **Dans ce projet Holberton**, on te demande des filtres dans des fichiers `.txt`. Dans 99% des cas, c’est du **display filter** (barre verte “Apply a display filter…”).

---

## Display filters : syntaxe + cheatsheet

### Les opérateurs indispensables
- Égalité : `==`
- Différent : `!=`
- Logique : `&&` (AND), `||` (OR), `!` (NOT)
- Comparaison : `>`, `<`, `>=`, `<=`
- Contient : `contains` (sur strings)
- Regex : `matches` (attention aux escapes)

### Exemples rapides
```wireshark
ip
tcp
udp
icmp
arp

ip.src == 192.168.1.10
ip.dst == 192.168.1.1
ip.addr == 192.168.1.10 # src OU dst

tcp.port == 80 # src OU dst port
tcp.dstport == 443
udp.srcport == 53

dns
dns.qry.name contains "google"
http.request
tls.handshake.type == 1
```

### Plage de ports
Utilise des comparaisons :
```wireshark
udp.dstport >= 200 && udp.dstport <= 300
tcp.dstport >= 200 && tcp.dstport <= 300
```

### Flags TCP (très utilisés pour Nmap)
```wireshark
tcp.flags.syn == 1
tcp.flags.ack == 1
tcp.flags.fin == 1
tcp.flags.reset == 1
tcp.len == 0
```

**Tip pro** : pour éviter les erreurs, clique un paquet → sélectionne le champ → clic droit → **Apply as Filter**.

---

## Lire et analyser un paquet (méthode)

Quand tu ouvres un paquet, lis **du bas vers le haut** (couches), mais comprends **du haut vers le bas** (sens réseau) :

1. **Frame** : timestamp, longueur, interface
2. **L2 Ethernet** : MAC src/dst, type (IPv4/ARP/IPv6)
3. **L3 IP** : IP src/dst, TTL, fragmentation, protocole (TCP/UDP/ICMP)
4. **L4 TCP/UDP** :
 - ports src/dst
 - flags TCP, seq/ack
 - fenêtre (window size)
5. **L7** : DNS, HTTP, TLS, SMB, etc.

### Exemple : reconnaître un SYN
- `tcp.flags.syn==1 && tcp.flags.ack==0`
- souvent `tcp.len==0` (pas de payload)
- réponse typique :
 - port **ouvert** : `SYN,ACK`
 - port **fermé** : `RST` / `RST,ACK`

---

## Cas d’usage : reconnaître un scan Nmap

### SYN scan (-sS)
- Nmap envoie des **SYN** sans compléter le handshake (scan “half‑open”)
- réponses :
 - SYN/ACK → open
 - RST → closed
 - rien / ICMP unreachable → filtered

### Connect scan (-sT)
- Nmap demande à l’OS de faire un **connect()** via l’appel système `connect`
- tu verras un **3‑way handshake complet** (SYN → SYN/ACK → ACK), puis souvent fermeture rapide

### FIN scan (-sF)
- Paquet avec seulement **FIN** (SYN absent)

### UDP scan (-sU)
- Nmap envoie des datagrammes UDP ; ports fermés répondent souvent par ICMP “port unreachable”

### Host discovery (pings)
- SYN ping `-PS`, ACK ping `-PA`, UDP ping `-PU` (par défaut port 40125), ICMP ping `-PE`, ARP scan `-PR`

---

# Wireshark x Nmap — Récap complet des scans & filtres (Holberton 0x05)

> Objectif : comprendre **ce que tu vois** dans Wireshark, **pourquoi** on utilise ces filtres, et **ce qu’on en déduit** en pentest / défense.

---

## 1) Wireshark et Nmap : qui fait quoi ?

### Wireshark (le microscope)
Wireshark ne “hack” rien : il **observe**.
- Il capture des paquets (trafic réseau brut).
- Il les **disséque** (Ethernet → IP → TCP/UDP/ICMP → applis).
- Il te permet de **filtrer**, **corréler**, **prouver** ce qu’un outil a envoyé/reçu.

### Nmap (l’explorateur)
Nmap est un outil d’exploration réseau :
- Découverte d’hôtes (host discovery / ping sweeps)
- Scan de ports (TCP/UDP)
- Détection de services/versions (optionnel)
- Scripts NSE (optionnel)

➡️ **Pourquoi Nmap avant Wireshark ?**  
Parce que Wireshark ne “crée” pas de trafic. Il faut **générer** un trafic (avec Nmap) puis l’analyser (avec Wireshark).  
En pentest : Nmap te dit “quoi explorer”.  
En défense : Wireshark te dit “ce qui s’est réellement passé sur le fil”.

---

## 2) Capture filters vs Display filters (super important)

### Capture filter (BPF)
Filtre **avant** d’enregistrer : réduit le bruit, économise CPU/RAM.  
Exemple : `icmp` ou `udp port 53`.

### Display filter (Wireshark)
Filtre **après** capture : tu peux changer à volonté.  
Exemple : `icmp.type==8`, `tcp.flags.syn==1`.

➡️ Dans le projet Holberton, “your filter string” = **display filter** (comme `ip.addr==...` dans l’énoncé).

---

## 3) Méthode standard pour réussir chaque tâche

1. **Start capture** sur la bonne interface (eth0/wlan0 qui “bouge”).
2. Lance la commande Nmap / arp-scan.
3. **Stop capture**
4. Repère 1 paquet typique (colonne Protocol + Info + Length).
5. Construis un filtre **générique** (sans IP fixes).
6. Teste le filtre, puis écris-le dans `X-*.txt` (2 lignes, newline final).

---

## 4) Les filtres qu’on a utilisés (et ce qu’ils prouvent)

> Note : on évite de filtrer par IP dans les fichiers, car le checker ne connaît pas tes IP.  
> On cherche des **invariants** : flags, type/code ICMP, ports par défaut, tailles typiques, etc.

---

### Task 0 — `nmap -sO <target>` (IP protocol scan)
**But :** tester les **protocoles IP** (champ “Protocol” dans IPv4), pas des ports.

**Ce que tu vois :**
- Des paquets IPv4 “minimalistes” envoyés vers la cible (parfois affichés “IPv4” non décodé)
- Et souvent des réponses ICMP “Protocol unreachable” (type 3 code 2) selon l’OS cible

**Filtre “proche du sujet” (si l’exemple montre ICMP len 34) :**
- `ip.proto==1 && frame.len==34`  
(= “paquet IPv4 dont Protocol=ICMP et taille minimale”)

**Ce qu’on peut déduire :**
- Certains protocoles IP passent / sont bloqués
- Comportement de filtrage/pare-feu (réponses ICMP vs silence)

**Valeur défense :**
- Détecter des rafales de paquets IPv4 “inhabituels” et/ou ICMP code 2 en retour.

---

### Task 1 — `nmap -sS <target>` (TCP SYN scan / half-open)
**But :** découvrir des ports TCP ouverts sans établir une connexion complète.

**Signature réseau :**
- L’attaquant envoie **SYN**
- Cible répond : SYN/ACK (port ouvert) ou RST (fermé)
- L’attaquant **ne termine pas** le handshake (souvent RST ensuite)

**Filtre (SYN “pur” + signature window Nmap vue chez toi) :**
- `tcp.flags.syn==1 && tcp.flags.ack==0 && tcp.window_size_value==1024`

**Ce qu’on déduit en pentest :**
- Liste de ports potentiellement ouverts/fermés (selon réponses)
- Discrétion relative vs connect() (moins de traces applicatives)

**Défense :**
- Rafale de SYN sur beaucoup de ports = scan classique.
- IDS/Firewall : détection de taux SYN élevé, pattern multi-ports.

---

### Task 2 — `nmap -sT <target>` (TCP Connect() scan)
**But :** scanner en établissant une connexion TCP **complète** (handshake complet), via l’OS (connect()).

**Signature :**
- SYN → SYN/ACK → ACK (3-way handshake)  
Puis fermeture rapide (RST/FIN) selon cas.

**Filtre “checker-friendly” basé sur le hint window_size (exemple Win=64240) :**
- `tcp.flags.syn==1 && tcp.flags.ack==0 && tcp.len==0 && tcp.window_size_value==64240`

**Ce qu’on déduit :**
- Très fiable, mais plus “bruyant” (logs applicatifs possibles)

**Défense :**
- Plus facile à tracer côté service (accept() côté serveur, logs, SIEM).

---

### Task 3 — `nmap -sF <target>` (TCP FIN scan)
**But :** scan furtif basé sur des paquets FIN “anormaux” (pas une vraie fin de connexion).

**Signature :**
- FIN “pur” envoyé vers beaucoup de ports
- Réponse souvent RST si port fermé ; silence si ouvert (selon OS)

**Filtre :**
- `tcp.flags.fin==1 && tcp.flags.syn==0 && tcp.flags.ack==0 && tcp.len==0 && tcp.window_size_value==1024`

**Déductions :**
- Fingerprinting possible (certains OS répondent différemment)
- Peut contourner certains filtres simples

**Défense :**
- Rafale FIN vers de multiples ports = scan furtif typique.

---

### Task 4 — `nmap -sn -PS/-PA <subnet>` (TCP SYN Ping / TCP ACK Ping)
**But :** découvrir quels hôtes sont “UP” sans faire un port scan complet.

- `-PS` : envoie SYN vers un port (souvent 80 si choisi)
- `-PA` : envoie ACK vers un port

**Filtre simple qui couvre les deux (port 80 dans l’exemple) :**
- `tcp.dstport==80 && ((tcp.flags.syn==1 && tcp.flags.ack==0) || (tcp.flags.ack==1 && tcp.flags.syn==0))`

**Déductions :**
- Host discovery efficace même si ICMP est filtré
- Différentes politiques firewall selon SYN/ACK/RST

**Défense :**
- Sur un subnet, rafales probes vers le même port = découverte d’hôtes.

---

### Task 5 — `nmap -sU <target>` (UDP port scan)
**But :** scanner des ports UDP.

**Signature clé :**
- Pour un port UDP fermé, la cible répond souvent :
  - ICMP Destination unreachable / **Port unreachable** (type 3 code 3)

**Filtre “exemple sujet” :**
- `icmp.type==3 && icmp.code==3`

**Déductions :**
- UDP est plus “ambigu” : absence de réponse = ouvert OU filtré
- ICMP code 3 = fermé (excellent signal)

**Défense :**
- Rafales UDP + rafales ICMP type 3 code 3 = scan UDP en cours.

---

### Task 6 — `nmap -sn -PU <subnet>` (UDP ping sweep)
**But :** découvrir des hôtes via UDP (host discovery).

**Signature :**
- UDP vers port “sonde” (ex: **40125** dans l’énoncé)
- Le payload peut varier selon l’environnement

**Filtre robuste (celui qui marchait chez toi) :**
- `udp.dstport==40125`

**Piège important (LAN) :**
Sur un réseau local, Nmap fait souvent **ARP discovery** par défaut → tu peux voir surtout `arp` si tu ne forces pas (ex: `--disable-arp-ping`).

**Défense :**
- Rafale UDP vers un port fixe sur tout un subnet = découverte d’hôtes.

---

### Task 7 — `nmap -sn -PE <subnet>` (ICMP ping sweep)
**But :** découvrir les hôtes via ICMP Echo (ping).

**Signature :**
- Echo Request = ICMP type 8 (envoyé)
- Echo Reply = ICMP type 0 (réponse)

**Filtre “packets sent by command” :**
- `icmp.type==8`

**Défense :**
- Rafale de ping sur /24 = sweep ICMP.

---

### Task 8 — `arp-scan -l` (ARP scanning)
**But :** découvrir les machines sur le LAN via ARP (couche 2).

**Signature :**
- ARP request “Who has X? Tell Y”
- opcode 1

**Filtre :**
- `arp.opcode==1`

**Défense :**
- Rafale d’ARP requests = scan LAN (réseau interne).

---

## 5) Pourquoi filtrer : à quoi ça sert vraiment ?

### En pentest (offensif)
Les filtres te servent à :
- **valider** que ton outil fait bien ce que tu penses
- distinguer “ça ne marche pas” vs “c’est filtré”
- comprendre les réponses (RST, ICMP unreachable, silence)
- faire du **troubleshooting** (interface, firewall local, NAT, etc.)
- capturer des preuves (PCAP) pour un rapport

### En défense (blue team)
Les filtres te servent à :
- détecter rapidement des patterns :
  - rafale SYN multi-ports (scan TCP)
  - rafale FIN/NULL/XMAS (scans furtifs)
  - rafale ICMP echo (ping sweep)
  - rafale ARP (scan LAN)
  - rafale ICMP code 3 (scan UDP)
- investiguer un incident :
  - quelles IP ont scanné ? quels ports ? quelle durée ? quelle cadence ?
- bâtir des règles IDS / SIEM et des alertes basées sur “signatures”

---

## 6) Suite logique : après ces scans, on fait quoi ?

### Côté pentest (logique d’enchaînement)
1) **Host discovery** (qui est vivant ?)  
   `-sn` + (-PE / -PS / -PA / -PU) + ARP-scan sur LAN
2) **Port scanning** (quelles portes ?)  
   `-sS` (rapide, discret) puis `-sU` (UDP ciblé)
3) **Version detection / services**  
   `-sV` (quels services exacts ?)
4) **OS fingerprinting**  
   `-O` (si autorisé)
5) **Enumeration / scripts**  
   Nmap NSE (`--script`) ou outils dédiés (HTTP enum, SMB enum, etc.)
6) **Exploitation / validation** (si scope le permet)  
   Avec preuves, pas “à l’aveugle”.

### Côté défense (logique d’investigation)
1) Corréler :
   - PCAP (Wireshark) + logs firewall + logs serveur + IDS
2) Identifier :
   - source, période, cadence, ports visés
3) Bloquer / atténuer :
   - rate limiting, firewall rules, segmentation, IDS signatures
4) Durcir :
   - fermer services inutiles, filtrer ICMP, limiter ARP spoofing, etc.

---

## 7) Tableau mémo (ultra utile)

| Action | Commande | Signature réseau | Filtre “générique” |
|---|---|---|---|
| IP protocol scan | `nmap -sO` | probes IP + ICMP code 2 possible | `ip.proto==1 && frame.len==34` (si sujet) |
| SYN scan | `nmap -sS` | SYN pur en rafale | `tcp.flags.syn==1 && tcp.flags.ack==0 && tcp.window_size_value==1024` |
| Connect scan | `nmap -sT` | handshake complet | `tcp.flags.syn==1 && tcp.flags.ack==0 && tcp.window_size_value==64240` |
| FIN scan | `nmap -sF` | FIN pur en rafale | `tcp.flags.fin==1 && tcp.flags.ack==0 && tcp.window_size_value==1024` |
| TCP ping sweep | `nmap -sn -PS/-PA` | SYN ou ACK vers port fixe | `tcp.dstport==80 && (...)` |
| UDP port scan | `nmap -sU` | ICMP port unreachable | `icmp.type==3 && icmp.code==3` |
| UDP ping sweep | `nmap -sn -PU` | UDP vers 40125 | `udp.dstport==40125` |
| ICMP ping sweep | `nmap -sn -PE` | Echo request | `icmp.type==8` |
| ARP scan | `arp-scan -l` | ARP request broadcast | `arp.opcode==1` |

---

## 8) Conseils Wireshark (pour progresser vite)

- **Follow → TCP Stream** : reconstituer une conversation (HTTP, etc.)
- **Statistics → Conversations** : qui parle avec qui ? (top talkers)
- **Statistics → IO Graphs** : voir un scan “en rafale” visuellement
- **Expert Info** : repérer anomalies (segments missing, retransmissions)
- Sauvegarder un PCAP : preuve / reporting / analyse offline

---

## 9) Éthique et cadre
Ne scanne que :
- tes machines
- ton lab (TryHackMe, Holberton)
- ou un scope explicitement autorisé

---

## 10) Commandes utiles (à garder sous la main)

```bash
# SYN scan
sudo nmap -sS <target>

# TCP connect scan
sudo nmap -sT <target>

# FIN scan
sudo nmap -sF <target>

# UDP scan
sudo nmap -sU <target>

# ICMP ping sweep
sudo nmap -sn -PE <subnet>

# TCP ping sweeps
sudo nmap -sn -PS80 <subnet>
sudo nmap -sn -PA80 <subnet>

# UDP ping sweep (port de l’énoncé)
sudo nmap -sn -PU40125 <subnet>

# ARP scan LAN
sudo arp-scan -l
```

---

Fin.


---

## Mini-glossaire

- **pcap/pcapng** : formats de capture
- **dissector** : module qui “comprend” un protocole et expose ses champs
- **SYN/ACK/RST/FIN** : flags TCP
- **ICMP type/code** : catégories de messages (echo, unreachable, …)
- **handshake** : négociation de connexion TCP en 3 étapes
- **sweep** : scan d’un range d’IP (host discovery)

---

Fin du cours.

## Sources (pour aller plus loin)

- Wireshark User’s Guide : sections “Filtering while capturing”, “Filtering packets while viewing”, “Building Display Filter Expressions”.
- Nmap Network Scanning (book) : sections “TCP SYN scan (-sS)”, “TCP Connect scan (-sT)”, “UDP scan (-sU)”, “IP Protocol Scan (-sO)”, “Host Discovery Techniques”.
- Manual pages : `wireshark-filter(4)`, `wireshark(1)`.
