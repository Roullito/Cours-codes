# Networking Foundations & Architecture

> **Holberton – Cybersecurity basics**  
> **Project:** Networking Foundations & Architecture  
> **Level:** Amateur – Weight: 1  
> **Author of project:** Yosri Ghorbel  
> **Support de révision créé par :** _Roullito + ChatGPT_ 😈

---

## 📚 Table des matières

1. [Introduction : c’est quoi un réseau ?](#-introduction--cest-quoi-un-réseau-)
2. [Topologie & LAN](#-topologie--lan)
   - [2.1. Qu’est-ce qu’une topologie réseau ?](#21-quest-ce-quune-topologie-réseau-)
   - [2.2. Qu’est-ce qu’un LAN ?](#22-quest-ce-quun-lan-)
   - [2.3. Les 3 topologies LAN de base](#23-les-3-topologies-lan-de-base)
   - [2.4. Framing & addressing en LAN](#24-framing--addressing-en-lan)
   - [2.5. Matériels LAN : hub, switch, routeur…](#25-matériels-lan--hub-switch-routeur)
3. [Modèles de référence : OSI & TCP/IP](#-modèles-de-référence--osi--tcpip)
   - [3.1. Pourquoi des modèles ?](#31-pourquoi-des-modèles-)
   - [3.2. Les 7 couches du modèle OSI](#32-les-7-couches-du-modèle-osi)
   - [3.3. Exemple de protocoles par couche](#33-exemple-de-protocoles-par-couche)
   - [3.4. Modèle TCP/IP vs OSI](#34-modèle-tcpip-vs-osi)
4. [Adressage : MAC, IP, CIDR & classes](#-adressage--mac-ip-cidr--classes)
   - [4.1. Adresse MAC](#41-adresse-mac)
   - [4.2. Adresse IP (IPv4)](#42-adresse-ip-ipv4)
   - [4.3. Ancien système par classes](#43-ancien-système-par-classes)
   - [4.4. CIDR & sous-réseaux modernes](#44-cidr--sous-réseaux-modernes)
5. [Routage & tables de routage](#-routage--tables-de-routage)
6. [Services réseau essentiels : DHCP, NAT, DNS](#-services-réseau-essentiels--dhcp-nat-dns)
   - [6.1. DHCP & le processus DORA](#61-dhcp--le-processus-dora)
   - [6.2. NAT : Network Address Translation](#62-nat--network-address-translation)
   - [6.3. DNS : Domain Name System](#63-dns--domain-name-system)
7. [Cheatsheet : commandes réseau sous Kali](#-cheatsheet--commandes-réseau-sous-kali)
8. [Cheatsheet : ports réseaux importants](#-cheatsheet--ports-réseaux-importants)
9. [Glossaire réseau](#-glossaire-réseau)
10. [Rappel des contraintes projet Holberton (scripts Bash)](#-rappel-des-contraintes-projet-holberton-scripts-bash)
11. [Checklist des objectifs pédagogiques](#-checklist-des-objectifs-pédagogiques)

---

## 🌐 Introduction : c’est quoi un réseau ?

**Définition simple :**  
Un **réseau informatique** est un ensemble de machines (PC, serveurs, smartphones, objets connectés…) qui **échangent des données** entre elles grâce à des **liens physiques** (câbles, fibres) ou **sans fil** (Wi-Fi, 4G/5G, Bluetooth…).

Quelques exemples concrets :

- Ton **réseau Wi-Fi à la maison** : box + PC + téléphone + console.
- Le **réseau d’une entreprise** : dizaines/centaines de postes reliés à des switches, routeurs, serveurs.
- **Internet** : gigantesque interconnexion de milliers de réseaux.

### Pourquoi les réseaux sont importants ?

- **Partager des ressources** : imprimantes, fichiers, serveurs, bases de données.
- **Communiquer** : emails, messageries, visioconférences.
- **Accéder à des services distants** : web, cloud, jeux en ligne.
- **Centraliser & sécuriser** : sauvegardes, monitoring, contrôles d’accès.

👉 Sans réseaux, chaque machine serait isolée. Pas de GitHub, pas de Google, pas de Holberton Intranet.

---

## 🕸 Topologie & LAN

### 2.1. Qu’est-ce qu’une topologie réseau ?

La **topologie réseau** décrit **la façon dont les machines sont connectées entre elles**, comme un plan de métro :

- Qui est relié à qui ?
- Via quel câble ou quel équipement ?
- Comment les données circulent-elles ?

On distingue :

- **Topologie physique** : comment les câbles sont réellement posés.
- **Topologie logique** : comment les données circulent (vue “abstraite”).

---

### 2.2. Qu’est-ce qu’un LAN ?

Un **LAN (Local Area Network)** est un réseau :

- **Géographiquement limité** (maison, bureau, bâtiment, campus),
- Généralement **propriété d’une seule organisation**,
- Avec des **débits élevés** (100 Mb/s, 1 Gb/s, 10 Gb/s),
- Utilisant des technologies comme **Ethernet** et **Wi-Fi**.

Par opposition :

- **WAN (Wide Area Network)** : grande échelle (entre villes/pays) – ex : Internet.
- **MAN (Metropolitan Area Network)** : à l’échelle d’une ville.

---

### 2.3. Les 3 topologies LAN de base

#### 2.3.1. Topologie bus

Toutes les machines sont connectées sur **un même câble partagé**.

```text
[PC1]──┬──[PC2]──┬──[PC3]──┬──[PC4]
       │         │         │
     (câble commun = "bus")
```

- **Avantages :**
  - Simple, peu de câble, peu coûteux.
- **Inconvénients :**
  - Si le câble principal est coupé → tout le réseau tombe.
  - Collisions fréquentes (tout le monde parle sur le même “bus”).
  - Difficile à étendre et dépanner.
- **Usage actuel :** quasi plus utilisé (Ethernet moderne est plutôt en étoile).

---

#### 2.3.2. Topologie étoile (star)

Toutes les machines sont reliées à un **équipement central** (souvent un switch).

```text
         [PC1]
           |
[PC2]───[SWITCH]───[PC3]
           |
         [PC4]
```

- **Avantages :**
  - Si un câble tombe → seul le PC relié est impacté.
  - Facile à étendre (on branche un nouveau câble/switch).
  - Domine les réseaux Ethernet modernes.
- **Inconvénients :**
  - Point central critique : si le switch tombe → plus de réseau.
  - Nécessite plus de câbles.

---

#### 2.3.3. Topologie anneau (ring)

Chaque machine est connectée à la suivante, formant un **anneau**.

```text
[PC1]──[PC2]──[PC3]──[PC4]
  ^                      |
  └──────────────────────┘
```

- **Avantages :**
  - Contrôle facile du passage de la parole (token ring, etc.).
- **Inconvénients :**
  - Si un lien ou une machine tombe, l’anneau peut être coupé.
- **Usage actuel :**
  - Rare en LAN classiques, mais concepts repris dans certaines techno (MPLS, réseaux de transport).

---

### 2.4. Framing & addressing en LAN

Sur un **LAN Ethernet**, les données ne circulent pas sous forme de “packets IP” directement sur le câble.  
Le support physique transporte des **trames (frames)** de niveau 2 (OSI).

**Framing (encapsulation en trame)** :

Une trame Ethernet contient typiquement :

- Adresse MAC **destination**,
- Adresse MAC **source**,
- Type (quel protocole est encapsulé, ex : IPv4, ARP, IPv6),
- Données (payload),
- CRC/FCS (contrôle d’erreur).

**Addressing en LAN :**

- Chaque interface réseau (carte réseau) possède une **adresse MAC** unique (48 bits).
- Les switches utilisent ces adresses MAC pour **diriger les trames** vers le bon port.
- Une trame est donc dirigée vers une **MAC destination**, pas vers une IP.  
  (L’IP est gérée par la couche supérieure – on y revient plus loin.)

---

### 2.5. Matériels LAN : hub, switch, routeur…

| Matériel      | Couche OSI principale | Rôle                                   | Comportement                |
|---------------|------------------------|----------------------------------------|-----------------------------|
| Hub           | 1 – Physique           | Répéteur multi-port                    | Diffuse tout à tous         |
| Switch        | 2 – Liaison (Data Link)| Relier des hôtes dans un LAN           | Commute selon MAC           |
| Routeur       | 3 – Réseau (Network)   | Relier des réseaux différents          | Route selon IP              |
| Modem         | 1/2                    | Conversion signal (fournisseur ↔ LAN)  | Internet ↔ box              |
| AP Wi-Fi      | 2/1                    | Point d’accès sans fil                 | Pont filaire ↔ radio        |
| Firewall      | 3–7                    | Filtre de sécurité                     | Autorise/bloque le trafic   |

**Résumé simple :**

- **Hub** : idiot, tout ce qu’il reçoit, il le renvoie partout.
- **Switch** : un peu intelligent, sait à quelle MAC correspond quel port.
- **Routeur** : très intelligent, sait à quel réseau envoyer les paquets IP.

---

## 🧱 Modèles de référence : OSI & TCP/IP

### 3.1. Pourquoi des modèles ?

Les modèles de référence (OSI, TCP/IP) servent à :

- **Structurer** les fonctions réseau en couches.
- Permettre de **standardiser** les protocoles.
- Permettre de **troubleshooter** plus facilement (“c’est un problème couche 2 ou couche 3 ?”).

---

### 3.2. Les 7 couches du modèle OSI

Du plus bas (proche du câble) au plus haut (proche de l’utilisateur) :

1. **Physique (Physical)**  
   - Bits, signaux électriques/optique, câbles, connecteurs, radio.
   - Ex : RJ45, fibre, Wi-Fi (radio), spécifications électriques.

2. **Liaison de données (Data Link)**  
   - Trames, adresses MAC, détection d’erreurs.
   - Découpe le flux en **frames** et gère l’accès au médium.
   - Ex : Ethernet, Wi-Fi (802.11), PPP, VLAN (802.1Q).

3. **Réseau (Network)**  
   - Adresses IP, routage, choix du chemin.
   - Ex : IP (IPv4, IPv6), ICMP, protocoles de routage (OSPF, BGP).

4. **Transport**  
   - Transport de segments de bout en bout, fiabilité.
   - Ex : TCP (fiable, connecté), UDP (non fiable, non connecté).

5. **Session**  
   - Gestion de sessions logiques (ouvertures, fermetures, reprise).
   - Peu visible, beaucoup de tâches gérées par les couches sup/inf.

6. **Présentation (Presentation)**  
   - Format des données, encodage, chiffrement, compression.
   - Ex : TLS, SSL, encodage (ASCII, UTF-8), formats (JPEG, JSON…).

7. **Application**  
   - Services proches de l’utilisateur : HTTP, FTP, DNS, SMTP…
   - Tout ce que l’utilisateur “voit”.

**Mnémonique:**  
- De 7 → 1 : _“All People Seem To Need Data Processing”_  
  (Application, Presentation, Session, Transport, Network, Data Link, Physical)  
- En français, tu peux inventer la tienne 😄

---

### 3.3. Exemple de protocoles par couche

| Couche OSI | Nom               | Exemples de protocoles/technos                        |
|-----------:|-------------------|--------------------------------------------------------|
| 7          | Application       | HTTP, HTTPS, FTP, SSH, SMTP, POP3, IMAP, DNS          |
| 6          | Présentation      | TLS/SSL, formats (JPEG, MP3, JSON, XML)               |
| 5          | Session           | RPC, NetBIOS session                                  |
| 4          | Transport         | TCP, UDP                                              |
| 3          | Réseau            | IPv4, IPv6, ICMP, OSPF, BGP                           |
| 2          | Liaison de données| Ethernet, Wi-Fi (802.11), PPP, VLAN (802.1Q), ARP     |
| 1          | Physique          | Câbles cuivre, fibre optique, Wi-Fi radio, RJ45       |

---

### 3.4. Modèle TCP/IP vs OSI

Le **modèle TCP/IP** (plus proche de la réalité d’Internet) a moins de couches :

1. **Accès réseau (Network Access)** ≈ couches 1 et 2 OSI  
2. **Internet** ≈ couche 3 OSI  
3. **Transport** ≈ couche 4 OSI  
4. **Application** ≈ couches 5, 6, 7 OSI du point de vue pratique

Pour ton projet, retiens surtout l’**OSI** pour expliquer clairement “où se passe quoi”, mais garde en tête que TCP/IP est ce qu’on utilise réellement sur Internet.

---

## 🧮 Adressage : MAC, IP, CIDR & classes

### 4.1. Adresse MAC

- **MAC (Media Access Control)** = adresse **physique** d’une interface réseau.
- Taille : **48 bits**, écrits en **hexadécimal**, souvent séparés par `:` ou `-`.

Exemple :

```text
00:1A:2B:3C:4D:5E
```

- Les **24 premiers bits** : identifient le constructeur (OUI – Organizationally Unique Identifier).
- Les **24 derniers bits** : identifient la carte chez ce constructeur.

**Rôle :**

- Utilisée au **niveau 2 (Data Link)**, principalement dans les LAN.
- Les **switches** apprennent les MAC pour savoir sur quel port envoyer les trames.

---

### 4.2. Adresse IP (IPv4)

Une adresse **IPv4** :

- Taille : **32 bits**, généralement écrits en notation **décimale pointée**.
- Exemple : `192.168.1.42`

Interprétation logique :

- Une IP est découpée en :
  - **partie réseau**,
  - **partie hôte** (host).

Sur un réseau, on retrouve :

- **Adresse réseau** : identifie le sous-réseau (ex : 192.168.1.0).
- **Adresse broadcast** : pour envoyer à tous les hôtes du réseau (ex : 192.168.1.255).
- **Plage d’hôtes** : IP utilisables pour les machines (ex : 192.168.1.1 → 192.168.1.254).

---

### 4.3. Ancien système par classes

Historiquement, les réseaux étaient divisés en **classes** :

| Classe | Plage 1er octet | Masque par défaut      | Nombre d’hôtes possibles                |
|--------|------------------|------------------------|-----------------------------------------|
| A      | 0–127            | 255.0.0.0 (`/8`)       | ~16 millions par réseau                 |
| B      | 128–191          | 255.255.0.0 (`/16`)    | ~65 000 par réseau                      |
| C      | 192–223          | 255.255.255.0 (`/24`)  | ~254 par réseau                         |
| D      | 224–239          | Multicast              | Pas pour hôtes                          |
| E      | 240–255          | Réservé, expérimental  |                                        |

**Limites de ce système :**

- Peu flexible : soit tu as 254 hôtes, soit 65 534, soit 16M…  
- Gaspillage énorme d’adresses IPv4.
- Difficile à adapter à la réalité des besoins.

👉 C’est pour ça qu’on est passé à **CIDR**.

---

### 4.4. CIDR & sous-réseaux modernes

**CIDR (Classless Inter-Domain Routing)** :

- Abandon de l’idée de classes strictes.
- On note le réseau sous forme : `adresse/prefix_length`.

Exemples :

- `192.168.1.0/24` → 24 bits pour le réseau, 8 bits pour les hôtes.
- `10.0.0.0/16` → 16 bits pour le réseau, 16 pour les hôtes.

**Règles rapides :**

- Nombre de bits pour les hôtes = `32 - prefix_length`.
- Nombre d’adresses totales = `2^(bits_hôte)`.
- Nombre d’hôtes utilisables ≈ `2^(bits_hôte) - 2`  
  (car on enlève réseau & broadcast dans les réseaux traditionnels).

Exemple :

- `192.168.10.0/24` :
  - 32 - 24 = 8 bits pour les hôtes
  - 2^8 = 256 adresses
  - 254 hôtes utilisables.

---

## 🚦 Routage & tables de routage

### Qu’est-ce que le routage ?

Le **routage** est le processus par lequel un **routeur** décide **où envoyer un paquet IP** pour qu’il atteigne son réseau de destination.

- Chaque routeur possède une **table de routage**.
- Cette table liste :
  - Des **préfixes réseau** (ex : `192.168.1.0/24`, `10.0.0.0/8`),
  - La **passerelle suivante** (next hop),
  - L’**interface de sortie**,
  - Une **métrique** (coût, distance).

### Exemple simple de table de routage (vue logique)

```text
Destination     Prefixe     Passerelle     Interface
--------------------------------------------------------
192.168.1.0     /24         0.0.0.0        eth0
10.0.0.0        /8          192.168.1.1    eth0
0.0.0.0         /0          192.168.1.254  eth0
```

- `192.168.1.0/24` : réseau local (pas de passerelle, next hop = directement).
- `10.0.0.0/8` : on envoie vers 192.168.1.1.
- `0.0.0.0/0` : **route par défaut** (default route) → tout ce qui ne correspond à aucune autre route.

**Règle importante :**  
Le routeur utilise le **“longest prefix match”** → la route la plus spécifique gagne.

---

## 🛠 Services réseau essentiels : DHCP, NAT, DNS

### 6.1. DHCP & le processus DORA

**Problème sans DHCP :**  
Configurer à la main IP, masque, passerelle, DNS sur chaque machine → long, erreur-prone.

**DHCP (Dynamic Host Configuration Protocol)** permet :

- D’attribuer automatiquement :
  - Adresse IP,
  - Masque,
  - Passerelle par défaut,
  - Serveurs DNS,
  - Durée de bail (lease).

#### Processus DORA

**DORA** = **D**iscover, **O**ffer, **R**equest, **A**ck.

1. **Discover**  
   Le client n’a pas d’IP → il envoie un broadcast **DHCP Discover** sur le réseau :  
   “Y a-t-il un serveur DHCP ?”

2. **Offer**  
   Un serveur DHCP répond avec un **DHCP Offer** :  
   “Je te propose l’IP 192.168.1.50 pour telle durée…”

3. **Request**  
   Le client répond avec **DHCP Request** :  
   “Ok, je veux bien cette IP 192.168.1.50.”

4. **Acknowledge**  
   Le serveur confirme via **DHCP ACK** :  
   “C’est validé, tu as 192.168.1.50 pour 8h.”

À la fin, le client a une configuration IP complète et fonctionnelle.

---

### 6.2. NAT : Network Address Translation

**Contexte :**

- Les adresses IPv4 publiques sont limitées.
- Les réseaux privés utilisent des plages réservées (ex : `192.168.x.x`, `10.x.x.x`).
- Mais sur Internet, il faut une **adresse publique**.

**NAT** permet à plusieurs machines privées de **partager une même IP publique**.

Schéma simple :

```text
[PC1 192.168.0.10] [PC2 192.168.0.11]  [PC3 192.168.0.12]   >── [ROUTEUR NAT] ── Internet
                         IP publique : 203.0.113.5
```

- En interne : IP privées.
- Le routeur NAT traduit :
  - IP source privée → IP publique + port unique.
- C’est souvent de la **PAT** (Port Address Translation) :
  - Plusieurs flux internes passent via la même IP publique avec des ports différents.

**Pourquoi NAT est utilisé ?**

- Économie d’IPv4 publiques.
- Masquage de la structure interne (un peu de “pseudo-sécurité”).
- Indispensable dans presque tous les réseaux domestiques.

---

### 6.3. DNS : Domain Name System

**Problème :**  
Les humains retiennent mal `142.250.75.206`, mais très bien `google.com`.

**DNS** est un **service distribué** qui traduit les **noms de domaine** en **adresses IP**.

- Tu demandes : `www.example.com`
- DNS te répond : `192.0.2.123`

#### Structure des noms de domaine

- `www.example.com.`
  - `.` → racine (root)
  - `com` → TLD (Top Level Domain)
  - `example` → domaine
  - `www` → sous-domaine (subdomain)

#### Types d’enregistrements

- **A** : nom → IPv4
- **AAAA** : nom → IPv6
- **CNAME** : alias vers un autre nom
- **MX** : serveurs de mail
- **NS** : serveurs de noms autoritatifs

#### Processus de résolution (simplifié)

1. Tu tapes `www.example.com` dans ton navigateur.
2. Ton OS interroge le **resolver DNS** (souvent ton routeur/FAI).
3. Si le resolver ne sait pas :
   - il interroge les serveurs **root**,
   - puis les **TLD** (`.com`),
   - puis les serveurs **autoritaires** pour `example.com`.
4. Il obtient l’IP, la **met en cache**, et te la renvoie.

---

## 💻 Cheatsheet : commandes réseau sous Kali

Commandes Linux utiles pour ce projet (et pour la vie 😎) :

| Commande                     | Rôle principal                                          |
|------------------------------|--------------------------------------------------------|
| `ip a`                       | Afficher les interfaces et leurs adresses IP           |
| `ip r`                       | Afficher la table de routage                           |
| `ping <IP/nom>`              | Tester la connectivité (ICMP echo)                     |
| `traceroute <IP/nom>`       | Voir le chemin (route) emprunté par les paquets        |
| `ss -tulnp` ou `netstat -tulnp` | Voir les ports ouverts et les services              |
| `arp -a`                     | Voir la table ARP (IP ↔ MAC)                           |
| `nslookup <domaine>`        | Interroger un serveur DNS (basique)                    |
| `dig <domaine>`             | Interroger DNS de manière détaillée                    |
| `host <domaine>`            | Résolution rapide de nom                               |
| `curl <URL>`                | Faire une requête HTTP/HTTPS en ligne de commande      |
| `wget <URL>`                | Télécharger un fichier depuis le web                   |
| `nmcli`                     | Gérer NetworkManager (wifi, etc.)                      |
| `tcpdump`                   | Sniffer le trafic réseau (à manier avec précaution)    |
| `nmap <cible>`              | Scanner des ports/services sur une machine             |

Tu utiliseras potentiellement certaines de ces commandes dans tes **scripts Bash** du projet (ex : ping, nmap, etc.), mais attention à **respecter les contraintes** (pas de `&&`, `||`, `;`, etc.).

---

## 🔢 Cheatsheet : ports réseaux importants

Les **ports** identifient des **services** au sein d’une même machine.

- **Port TCP** : orienté connexion, fiable (ex : HTTP, HTTPS, SSH).
- **Port UDP** : sans connexion, rapide mais non fiable (ex : DNS, VoIP).

Quelques ports très classiques :

| Port | Protocole | Transport | Service                         |
|------|-----------|-----------|---------------------------------|
| 20   | TCP       | FTP       | FTP data                        |
| 21   | TCP       | FTP       | FTP control                     |
| 22   | TCP       | SSH       | Secure Shell                    |
| 23   | TCP       | Telnet    | Terminal distant (non sécurisé)|
| 25   | TCP       | SMTP      | Mail (envoi)                    |
| 53   | UDP/TCP   | DNS       | Résolution de noms             |
| 67   | UDP       | DHCP      | Serveur DHCP                    |
| 68   | UDP       | DHCP      | Client DHCP                     |
| 80   | TCP       | HTTP      | Web non chiffré                 |
| 110  | TCP       | POP3      | Mail (récupération)            |
| 143  | TCP       | IMAP      | Mail (récupération avancée)    |
| 443  | TCP       | HTTPS     | Web chiffré                     |
| 3306 | TCP       | MySQL     | Base de données                 |
| 5432 | TCP       | PostgreSQL| Base de données                 |
| 6379 | TCP       | Redis     | In-memory DB/cache              |
| 8080 | TCP       | HTTP alt. | Serveurs web alternatifs        |

En **sécurité / scan de ports** (nmap), tu verras souvent les états :

- **open** : une appli écoute sur ce port.
- **closed** : aucune appli n’écoute, mais la machine répond.
- **filtered** : un firewall filtre, on ne sait pas si c’est open ou closed.

---

## 📖 Glossaire réseau

Quelques termes que tu dois pouvoir expliquer simplement :

- **LAN (Local Area Network)** : réseau local (maison, bureau).
- **WAN (Wide Area Network)** : réseau grande échelle (entre villes/pays).
- **MAN (Metropolitan Area Network)** : réseau à l’échelle d’une ville.
- **Frame (trame)** : unité de données au niveau 2 (Data Link).
- **Packet (paquet)** : unité de données au niveau 3 (Network).
- **Segment** : unité de données au niveau 4 (Transport – TCP).
- **PDU (Protocol Data Unit)** : nom générique pour “trame/paquet/segment”.
- **MTU (Maximum Transmission Unit)** : taille max d’une trame.
- **Bandwidth (bande passante)** : quantité de données transmissible par seconde.
- **Latency (latence)** : temps de trajet d’un paquet (ms).
- **Jitter** : variation de la latence dans le temps.
- **Collision domain** : zone où plusieurs machines peuvent entrer en collision (hub).
- **Broadcast domain** : zone où un broadcast est reçu par tous (limité par routeurs).
- **VLAN** : LAN logique construit au-dessus d’un LAN physique (Virtual LAN).
- **Gateway (passerelle)** : point de sortie vers d’autres réseaux (souvent routeur).
- **Firewall** : filtre le trafic selon des règles.
- **VPN (Virtual Private Network)** : tunnel sécurisé entre deux points via Internet.
- **NAT** : traduction d’adresse réseau (privée ↔ publique).
- **DHCP** : service d’attribution automatique d’IP.
- **DNS** : résolution de noms de domaine en IP.

---

## 🧾 Rappel des contraintes projet Holberton (scripts Bash)

Ce projet associe des **notions réseau** et des **scripts Bash**.

**Règles générales (résumé) :**

- Éditeurs autorisés : `vi`, `vim`, `emacs`.
- Tous les scripts sont testés sur **Kali Linux**.
- Tous les scripts doivent faire **exactement 2 lignes** :  
  - `wc -l file` doit afficher `2`.
- Tu dois **substituer la plage d’IP par `$1`** (argument du script).
- Tous les fichiers doivent se terminer par un **retour à la ligne**.
- La **première ligne** doit être **exactement** :

  ```bash
  #!/bin/bash
  ```

- Tu n’as pas le droit d’utiliser :  
  - les **backticks** \` \`,  
  - `&&`,  
  - `||`,  
  - `;`.
- Ton code doit respecter le **style Betty** (`betty-style.pl` et `betty-doc.pl`).
- Tous tes fichiers doivent être **exécutables** (`chmod +x ton_script.sh`).
- Un fichier `README.md` à la racine du projet est **obligatoire**.

👉 En pratique, ton script ressemblera à :

1. Ligne 1 : `#!/bin/bash`
2. Ligne 2 : ta commande (ou pipeline) qui utilise `$1` comme plage d’IP.

On travaillera **ensemble sur les exercices** ensuite, en respectant ces contraintes, sans te donner directement les solutions, mais en t’aidant à construire **toi-même** la bonne commande.

---

## ✅ Checklist des objectifs pédagogiques

À la fin de ce cours, tu dois pouvoir expliquer **à l’oral**, sans Google :

- [x] Ce qu’est un réseau et pourquoi c’est important.
- [x] Ce qu’est une **topologie réseau**.
- [x] Ce qu’est un **LAN**.
- [x] Les **trois topologies de base** : bus, étoile, anneau.
- [x] Ce que sont **framing & addressing** dans un LAN (trames & MAC).
- [x] Les **principaux équipements LAN** (hub, switch, routeur, etc.) et leur rôle.
- [x] Les **7 couches du modèle OSI** et leur fonction.
- [x] Quelques **protocoles** typiques pour chaque couche.
- [x] Le rôle des couches **physique** et **liaison de données**.
- [x] La différence entre **adresse IP** et **adresse MAC**.
- [x] Le **format d’une MAC** et d’une **IP v4**.
- [x] Ce qu’est **CIDR** et pourquoi il remplace les classes.
- [x] Les limitations du système de classes **A/B/C**.
- [x] Ce qu’on entend par **subnet classes** / sous-réseaux.
- [x] Ce qu’est le **routage** et comment lire une **table de routage** simple.
- [x] Comment fonctionne **DHCP** (processus **DORA**).
- [x] Ce qu’est le **NAT**, pourquoi on l’utilise (IPv4, partage, masquage).
- [x] Comment **DNS** résout un nom de domaine en adresse IP.
- [x] Les commandes réseau de base sur **Kali**.
- [x] Les ports réseaux les plus importants (22, 80, 443, 53, 67-68, etc.).

---
