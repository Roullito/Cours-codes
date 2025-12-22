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
10. [Tâches Holberton (0 → 8) : filtres prêts à coller](#tâches-holberton-0--8--filtres-prêts-à-coller)
11. [Debug rapide si “je ne vois rien”](#debug-rapide-si-je-ne-vois-rien)
12. [Mini-glossaire](#mini-glossaire)

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

## Tâches Holberton (0 → 8) : filtres prêts à coller

### Règles de format (IMPORTANT pour l’autograder)
L’énoncé dit : **“All your files must contain two lines”** et **finir par une newline**.

➡️ Fais simple :
- **Ligne 1** : ton filtre
- **Ligne 2** : vide (juste une newline)

Exemple correct :
```text
ip.addr==192.0.2.1

```

---

### 0. IP protocol scan (`nmap -sO <target>`)

**Idée réseau**
`-sO` itère sur le champ “IP protocol” et surveille notamment les ICMP “protocol unreachable” (type 3 code 2).

Filtre robuste (probes + réponses typiques) :
```text
(icmp.type==3 && icmp.code==2) || (ip && !(ip.proto==6 || ip.proto==17 || ip.proto==1))

```

Fichier attendu : `0-ip_scan.txt`

---

### 1. TCP SYN scan (`nmap -sS <target>`)

Filtre :
```text
tcp.flags.syn==1 && tcp.flags.ack==0 && tcp.len==0

```

Fichier : `1-tcp_syn_scan.txt`

---

### 2. TCP Connect() scan (`nmap -sT <target>`)

Astuce pratique : le SYN scan Nmap est souvent détectable par une fenêtre TCP de **1024** (valeur connue dans l’écosystème Nmap).

Filtre (SYN “non‑1024”) :
```text
tcp.flags.syn==1 && tcp.flags.ack==0 && tcp.len==0 && tcp.window_size_value!=1024

```

Fichier : `2-tcp_connect_scan.txt`

---

### 3. TCP FIN scan (`nmap -sF <target>`)

Filtre :
```text
tcp.flags.fin==1 && tcp.flags.syn==0 && tcp.flags.ack==0 && tcp.flags.reset==0

```

Fichier : `3-tcp_fin_scan.txt`

---

### 4. TCP ping sweeps (host discovery)

Nmap peut faire de la découverte d’hôtes via SYN ping (`-PS`) sur ports web (80/443).

Filtre :
```text
tcp.flags.syn==1 && tcp.flags.ack==0 && (tcp.dstport==80 || tcp.dstport==443)

```

Fichier : `4-tcp_ping_sweeps.txt`

---

### 5. UDP port scan (`nmap -sU -p200-300 <target>`)

Filtre :
```text
(udp && udp.dstport>=200 && udp.dstport<=300) || (icmp.type==3 && icmp.code==3)

```

Fichier : `5-udp_port_scan.txt`

---

### 6. UDP ping sweeps (`nmap -sn -PU <targets>`)

Par défaut `-PU` utilise le port **40125**.

Filtre :
```text
udp.dstport==40125 || (icmp.type==3 && icmp.code==3)

```

Fichier : `6-udp_ping_sweeps.txt`

---

### 7. ICMP ping sweep (`nmap -sn -PE <targets>`)

Filtre :
```text
(icmp.type==8 && icmp.code==0) || (icmp.type==0 && icmp.code==0)

```

Fichier : `7-icmp_ping_sweep.txt`

---

### 8. ARP scanning (`nmap -sn -PR <targets>` sur LAN)

Filtre :
```text
arp.opcode==1 || arp.opcode==2

```

Fichier : `8-arp_scanning.txt`

---

## Debug rapide si “je ne vois rien”

Checklist :

1) **Bonne interface ?** (`wlan0` vs `eth0` vs `any`)
2) **Tu captures au bon moment ?** start capture → lance `nmap` → stop
3) **Tu utilises display filter (barre verte) ?** pas capture filter par erreur
4) **Le trafic est local ?** ARP uniquement sur le LAN
5) **Firewall/VPN** : ça peut changer ce que tu vois
6) Test rapide :
 - `arp` (sur LAN)
 - `icmp` (ping)
 - `dns` (résolution)
 - `tcp` (navigation web)

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
