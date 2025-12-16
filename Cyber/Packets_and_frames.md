# Packets and Frames — Reading (Transport Layer Focus)

> Objectif : comprendre **comment les données circulent** sur un réseau, et être capable d’expliquer (sans Google) :
> - le rôle de la **couche Transport (OSI Layer 4)**
> - les différences **TCP vs UDP**
> - la différence **packets vs frames**
> - comment **TCP** assure la fiabilité “end-to-end” sur un réseau non fiable
> - le **TCP three-way handshake**
> - avantages / inconvénients de TCP et UDP
> - différences entre **headers TCP vs UDP**
> - “connection-oriented” vs “connectionless”
> - des exemples d’applications utilisant TCP et UDP
>
> ⚠️ Ce cours est volontairement **pédagogique et très détaillé**, avec analogies + exercices “lecture de trames/paquets”.

---

## 0) Vision d’ensemble (en 60 secondes)

Quand une appli envoie des données (ex: navigateur → serveur), ces données descendent la pile réseau :

- **L7 Application** : HTTP, DNS, SSH, etc. (le “langage” applicatif)
- **L4 Transport** : TCP/UDP (ports, fiabilité, ordre, congestion…)
- **L3 Network** : IP (adresses IP, routage)
- **L2 Data Link** : Ethernet/Wi‑Fi (adresses MAC, trames)
- **L1 Physical** : bits sur câble / ondes

Puis à l’arrivée, ça remonte dans l’ordre inverse.

👉 Chaque couche ajoute un **en-tête (header)** : c’est l’**encapsulation**.

---

## 1) OSI Layer 4 — Transport Layer (but & responsabilités)

### 1.1 À quoi sert la couche Transport ?
La couche Transport fournit une **communication de bout en bout** (*end-to-end*) entre deux processus applicatifs.

Concrètement, elle s’occupe de :

1) **Multiplexage / démultiplexage**
- Plusieurs applis sur la même machine partagent le réseau.
- Les **ports** (ex: 443, 53) servent à savoir “à quelle appli” livrer les données.

2) **Fiabilité (si protocole fiable)**
- Détecter pertes / erreurs, retransmettre, garantir l’ordre (TCP).

3) **Contrôle de flux**
- Éviter de submerger le récepteur (fenêtre TCP).

4) **Contrôle de congestion**
- Éviter de saturer le réseau (algos TCP : slow start, congestion avoidance…).

### 1.2 Analogie simple
- **IP (L3)** = “adresse postale” + “le camion choisit l’itinéraire”
- **TCP/UDP (L4)** = “à quel service / appartement livrer le colis” (ports)
- **TCP** = envoi en recommandé avec accusé de réception, suivi, réexpédition si perdu
- **UDP** = carte postale : rapide, simple, mais aucune garantie

---

## 2) Packets vs Frames (et où se placent TCP/UDP)

### 2.1 Définitions
- **Frame (trame)** : unité de données **couche 2 (Data Link)**  
  Exemple : **Ethernet frame**, **Wi‑Fi frame**
- **Packet (paquet)** : unité de données **couche 3 (Network)**  
  Exemple : **IP packet**
- **Segment / Datagram**
  - **TCP segment** : unité de données couche 4 pour TCP
  - **UDP datagram** : unité de données couche 4 pour UDP

> Beaucoup de gens disent “packet” pour tout. En cours/réseau, on distingue :
> - L2 = **frame**
> - L3 = **packet**
> - L4 = **segment (TCP)** / **datagram (UDP)**

### 2.2 Encapsulation (schéma mental)
Imagine un colis dans un autre colis :

```
[ L2 FRAME HEADER ][  L3 IP HEADER  ][ L4 TCP/UDP HEADER ][   DATA   ][ L2 TRAILER ]
        MAC               IP               Ports
```

- La **trame** contient un **paquet IP**
- Le **paquet IP** contient un **segment TCP** ou **datagram UDP**
- Le segment/datagram contient les **données applicatives**

### 2.3 Pourquoi cette distinction est importante ?
Parce que **chaque couche** a des objectifs différents :

- **L2 (frames)** : livrer sur le **même réseau local** (LAN) grâce aux **MAC**
- **L3 (packets)** : traverser des réseaux via des **routeurs** grâce aux **IP**
- **L4 (segments/datagrams)** : livrer au **bon processus** et assurer (ou pas) la fiabilité

### 2.4 Tableau rapide
| Couche | PDU (unité) | Identité/Adresses | Exemple |
|---|---|---|---|
| L2 Data Link | Frame (trame) | MAC src/dst | Ethernet, Wi‑Fi |
| L3 Network | Packet (paquet) | IP src/dst | IPv4/IPv6 |
| L4 Transport | Segment (TCP) / Datagram (UDP) | Ports src/dst | TCP/UDP |

---

## 3) TCP vs UDP — les vraies différences

### 3.1 TCP (Transmission Control Protocol)
TCP est **orienté connexion** (*connection-oriented*) et **fiable**.

TCP fournit :
- **fiabilité** (ACK, retransmissions)
- **ordre** (numéros de séquence)
- **contrôle de flux** (window size)
- **contrôle de congestion**

👉 Idéal quand tu veux : “les données doivent arriver complètes et dans l’ordre”.

Exemples : HTTP/HTTPS (traditionnel), SSH, SMTP, IMAP, FTP control, etc.

### 3.2 UDP (User Datagram Protocol)
UDP est **sans connexion** (*connectionless*) et **non fiable** (pas de garanties).

UDP fournit :
- **ports** (multiplexage) + un minimum de structure
- éventuellement un **checksum** pour détecter une corruption (pas de retransmission)

👉 Idéal quand tu veux : “rapide, faible latence, et on accepte des pertes”.

Exemples : DNS (souvent), VoIP, streaming temps réel, jeux en ligne, NTP, DHCP.

---

## 4) Comment TCP assure une communication fiable sur un réseau “pas fiable”

### 4.1 Pourquoi le réseau est “non fiable” ?
Même si ça marche souvent, un réseau peut :
- **perdre** des paquets (congestion, wifi instable, routeur saturé)
- **réordonner** (paquets qui prennent des routes différentes)
- **dupliquer** (retransmission mal gérée en bas niveau)
- **corrompre** (rare aujourd’hui mais possible)

### 4.2 Les 4 mécanismes clés de TCP

#### A) Numéros de séquence (Sequence Number)
- Chaque octet (ou bloc) envoyé a une position dans le flux.
- Le récepteur peut remettre dans l’ordre.

#### B) Accusés de réception (ACK)
- Le récepteur confirme “j’ai reçu jusqu’à tel numéro”.
- Si l’ACK n’arrive pas, l’émetteur soupçonne une perte.

#### C) Retransmission (RTO, fast retransmit)
- Si un segment est perdu, TCP le renvoie.
- Deux grandes logiques :
  - **timeout** (RTO)
  - **triple duplicate ACK** → retransmission “rapide”

#### D) Fenêtre (Window) — contrôle de flux
- Le récepteur annonce combien il peut recevoir (buffer).
- L’émetteur n’envoie pas au-delà, sinon ça déborde.

> Résultat : TCP transforme un réseau “best effort” (IP) en un flux fiable pour l’appli.

### 4.3 Congestion control (idée simple)
Même si le récepteur peut suivre, le réseau peut être saturé.

TCP ajuste son débit :
- augmente progressivement quand ça va
- réduit quand il détecte des pertes (souvent signe de congestion)

📌 Tu n’as pas besoin de connaître tous les algos, mais retiens :
> TCP essaye d’être “gentil” avec le réseau, UDP s’en fiche.

---

## 5) Le TCP Three‑Way Handshake (SYN / SYN‑ACK / ACK)

### 5.1 Objectif du handshake
Avant d’envoyer des données, TCP doit :
- **synchroniser les numéros de séquence**
- **négocier des options** (MSS, SACK, window scaling…)
- vérifier que les deux côtés sont **joignables**

### 5.2 Les 3 étapes
Supposons Client → Serveur (port 443).

1) **SYN** (Client → Serveur)
- “Je veux ouvrir une connexion, voici mon ISN (initial sequence number)”

2) **SYN‑ACK** (Serveur → Client)
- “OK, moi aussi je veux, voici mon ISN, et j’ACK le tien”

3) **ACK** (Client → Serveur)
- “Parfait, j’ACK ton ISN”

Après ça, la connexion est **établie** et les données peuvent circuler.

### 5.3 Schéma
```
Client                                Serveur
  | ----------- SYN (seq=x) ----------> |
  | <------- SYN-ACK (seq=y, ack=x+1) --|
  | ----------- ACK (ack=y+1) --------> |
  | ======= DATA (HTTP/TLS/...) ======= |
```

### 5.4 Bonus : fermeture (très utile à connaître)
La fermeture TCP est souvent un **four-way handshake** (FIN/ACK dans chaque sens) :
- un côté dit “je n’envoie plus” (FIN)
- l’autre ACK
- puis l’autre envoie son FIN quand prêt
- ACK final

---

## 6) Avantages & inconvénients — TCP vs UDP

### 6.1 TCP
✅ Avantages
- fiable (pertes gérées)
- ordre garanti
- contrôle de flux + congestion
- “stream” : l’appli lit un flux continu

❌ Inconvénients
- overhead (plus de header + état de connexion)
- handshake avant données (latence initiale)
- retransmissions → latence si pertes
- head-of-line blocking (un segment perdu peut bloquer la lecture dans l’ordre)

### 6.2 UDP
✅ Avantages
- très faible overhead
- pas de handshake (démarrage rapide)
- adapté au temps réel (pertes tolérées)
- l’appli gère elle-même fiabilité/ordre si besoin

❌ Inconvénients
- pas de fiabilité
- pas de contrôle de congestion “obligatoire”
- certaines infrastructures filtrent/limitent UDP
- l’appli doit gérer tout le “smart” si elle veut de la fiabilité

---

## 7) Headers TCP vs UDP — ce qui change vraiment

### 7.1 UDP header (simple)
UDP a un header minimal (8 octets) :

- **Source Port** (16 bits)
- **Destination Port** (16 bits)
- **Length** (16 bits)
- **Checksum** (16 bits)

👉 UDP = “voici une enveloppe, débrouille-toi”.

### 7.2 TCP header (riche)
TCP est plus long (20 octets minimum + options) :

Champs importants :
- **Source Port / Destination Port**
- **Sequence Number**
- **Acknowledgment Number**
- **Data Offset** (taille du header)
- **Flags** (bits de contrôle) :
  - SYN, ACK, FIN, RST, PSH, URG (et d’autres)
- **Window Size**
- **Checksum**
- **Urgent Pointer**
- **Options** (MSS, SACK, timestamps, window scale…)

👉 TCP = “je gère une connexion, l’ordre, la fiabilité”.

### 7.3 Comparatif express
| Aspect | TCP | UDP |
|---|---|---|
| Connexion | Oui (stateful) | Non (stateless) |
| Fiabilité | Oui | Non |
| Ordre | Oui | Non |
| Contrôle de flux | Oui | Non |
| Congestion control | Oui | Non (dépend de l’appli) |
| Header | Plus gros + options | Minimal |
| Usage typique | Web, email, SSH | DNS, VoIP, streaming, jeux |

---

## 8) Connection‑oriented vs Connectionless

### 8.1 Connection‑oriented (orienté connexion)
- Nécessite un **état** (state) côté client et serveur
- Met en place une “session” de transport (handshake)
- Permet : fiabilité, ordre, contrôle de flux…

Exemple : **TCP**

### 8.2 Connectionless (sans connexion)
- Pas d’état durable : chaque datagram est indépendant
- Pas de handshake
- Plus simple, mais moins de garanties

Exemple : **UDP**

> À ne pas confondre : une appli au-dessus d’UDP peut “simuler” une connexion (ex: QUIC).

---

## 9) Exemples d’applications TCP et UDP (à connaître)

### 9.1 Souvent TCP
- **HTTP/HTTPS** (HTTP/1.1 & HTTP/2 sur TCP)
- **SSH**
- **SMTP / IMAP / POP3** (email)
- **FTP (contrôle)**, **SFTP**
- **Bases de données** (PostgreSQL/MySQL souvent via TCP)
- **RDP** (Windows Remote Desktop, souvent TCP/UDP mais base TCP)

### 9.2 Souvent UDP
- **DNS** (requêtes simples souvent UDP, TCP pour grosses réponses / zone transfers)
- **DHCP**
- **NTP**
- **VoIP** (RTP/RTCP)
- **Jeux en ligne** (latence critique)
- **Streaming temps réel** (selon techno)
- **QUIC / HTTP/3** (sur UDP, mais fiable au niveau applicatif)

---

## 10) “Packets & Frames — Reading” : apprendre à lire une capture

Cette partie est celle qui te fera réellement progresser en cybersécurité/pentest/forensic.

### 10.1 Les indices à repérer dans une capture
Quand tu vois une ligne dans Wireshark/tcpdump, demande-toi :

1) **L2 :** quelle techno ? Ethernet/Wi‑Fi ?  
   - MAC src/dst
   - VLAN tag ?
   - FCS/CRC (souvent pas visible)
2) **L3 :** IP ? IPv4/IPv6 ?  
   - IP src/dst
   - TTL / Hop Limit
   - fragmentation ?
   - protocole : TCP(6), UDP(17), ICMP(1)
3) **L4 :** TCP ou UDP ?  
   - ports
   - flags TCP (SYN/ACK/FIN)
   - seq/ack, window, options
4) **L7 :** quel protocole applicatif ?  
   - HTTP, TLS, DNS, etc.

### 10.2 Exemple mental : “j’ouvre un site web”
**Cas classique (HTTP/1.1 sur TCP)** :
1) DNS (souvent UDP/53) : “où est l’IP de example.com ?”
2) TCP handshake (SYN/SYN‑ACK/ACK) vers 443
3) TLS handshake (si HTTPS)
4) HTTP request/response (GET / …)

### 10.3 Filtres Wireshark utiles
- Trafic IP : `ip`
- Une IP : `ip.addr == 192.168.1.10`
- TCP : `tcp`
- UDP : `udp`
- Port : `tcp.port == 443` ou `udp.port == 53`
- SYN uniquement : `tcp.flags.syn == 1 and tcp.flags.ack == 0`
- Handshake : `tcp.flags.syn == 1 or tcp.flags.fin == 1 or tcp.flags.rst == 1`
- Retransmission : `tcp.analysis.retransmission`

### 10.4 Commandes utiles en ligne de commande (Kali/Linux)
> Le but n’est pas de tout maîtriser, mais de savoir **quoi utiliser** et **quoi lire**.

- Voir connexions TCP/UDP :
  - `ss -tulpn`
  - `netstat -tulpn` (souvent remplacé par ss)
- Capture :
  - `tcpdump -i eth0 -nn`
  - `tcpdump -i eth0 -nn tcp port 443`
  - `tcpdump -i wlan0 -nn udp port 53`
- Analyse plus simple :
  - `tshark` (Wireshark en CLI)
- Tester des ports/connexions :
  - `nc -vz host 80`
  - `nc -u -vz host 53` (UDP)

---

## 11) Points pièges & idées à corriger

### 11.1 “UDP = pas d’ordre” → nuance
UDP ne garantit rien, mais si les datagrams arrivent dans l’ordre, ton appli les reçoit dans cet ordre.  
Juste : **aucune garantie** + possible réordonnancement.

### 11.2 “TCP = plus rapide”
Pas forcément. Sur un réseau propre, TCP peut être très performant.  
Sur un réseau instable, la fiabilité peut coûter cher (retransmissions).

### 11.3 “Frames = packets”
Non. Un paquet IP traverse des routeurs, mais la trame L2 change à chaque saut.
- Les **IP** restent (en général) les mêmes de bout en bout.
- Les **MAC** changent à chaque lien (chaque LAN).

---

## 12) Mini‑exercices (avec corrigé à la fin)

### Exercice 1 — Identifier couche par couche
On te donne une ligne (imaginée) :

- Ethernet II, Src: 00:11:22:33:44:55, Dst: aa:bb:cc:dd:ee:ff  
- IPv4, Src: 192.168.1.10, Dst: 142.250.74.206, TTL: 64  
- TCP, Src Port: 51544, Dst Port: 443, Flags: SYN

Questions :
1) C’est une **frame** ou un **packet** (au sens strict) ?
2) Quelle couche montre l’IP ? Quelle couche montre les MAC ?
3) Est-ce le début d’une connexion TCP ? Pourquoi ?

### Exercice 2 — UDP et DNS
Une capture montre :
- UDP src port 55231 → dst port 53
- puis une réponse UDP src port 53 → dst port 55231

Questions :
1) Quel protocole applicatif probable ?
2) Pourquoi le port source est “bizarre” (55231) ?
3) Quelle information clé manque à UDP pour être “fiable” ?

### Exercice 3 — Retransmission TCP
Tu vois dans Wireshark : `tcp.analysis.retransmission`

Questions :
1) Qu’est-ce que ça suggère sur l’état du réseau ?
2) Que fait TCP dans ce cas ?
3) Quel impact sur une appli (ex: streaming vs téléchargement) ?

---

## 13) Corrigés (garde-les pour te tester)

### Corrigé Exercice 1
1) C’est une **frame L2** (Ethernet II est explicitement mentionné).  
2) IP = couche 3. MAC = couche 2.  
3) Oui : flag **SYN** sans ACK → début du three-way handshake.

### Corrigé Exercice 2
1) DNS est très probable (port 53 UDP).  
2) Le port source est un **port éphémère** choisi par le client pour cette requête.  
3) Pas de séquence/ACK/retransmission : UDP n’assure pas la fiabilité.

### Corrigé Exercice 3
1) Perte ou congestion (ou wifi instable).  
2) TCP **réémet** le segment non confirmé.  
3) Téléchargement : plus lent mais correct. Temps réel : latence, micro-coupures possibles.

---

## 14) Cheat‑sheet final (à mémoriser)

- **Frame = L2 (MAC)**  
- **Packet = L3 (IP)**  
- **Segment TCP / Datagram UDP = L4 (ports)**  
- **TCP** : connexion, fiabilité, ordre, congestion/flow control  
- **UDP** : simple, rapide, pas de garanties  
- **Three-way handshake** : SYN → SYN-ACK → ACK  
- **MAC change** à chaque réseau local, **IP** traverse le routage  
- Pour “lire” un trafic : L2 → L3 → L4 → L7

---

## 15) Glossaire (super utile)
- **PDU** : Protocol Data Unit (unité de données d’une couche)
- **MTU** : taille max d’une trame (ex: Ethernet 1500 bytes de payload IP typique)
- **MSS** : taille max TCP (souvent MTU - headers)
- **ACK** : accusé de réception TCP
- **ISN** : initial sequence number
- **Ephemeral port** : port client temporaire (souvent 49152–65535, variable)

---

### Tu veux t’auto‑tester (challenge) ?
Explique à voix haute, sans notes :
1) La différence frame/packet/segment
2) Pourquoi TCP a besoin de handshake
3) Pourquoi DNS utilise souvent UDP
4) Ce que signifient SYN / ACK / FIN
5) Pourquoi une retransmission arrive
