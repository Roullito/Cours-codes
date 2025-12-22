# Nmap Live Host Discovery (Ping Scan) — Cours ultra détaillé (Kali Linux)

> **Objectif du module :** découvrir **quels hôtes sont “up”** (actifs) sur une plage d’adresses IP **avant** de lancer des scans plus lourds (ports, services, OS).
>
> ⚠️ **Éthique / légal :** n’utilise Nmap **que** sur des réseaux/systèmes pour lesquels tu as une autorisation explicite (lab TryHackMe, machines perso, infra d’entreprise avec accord).

---

## Sommaire

1. [C’est quoi Nmap ?](#cest-quoi-nmap-)
2. [Pourquoi “Live Host Discovery” ?](#pourquoi-live-host-discovery-)
3. [Comment fonctionne un scan Nmap (vue globale)](#comment-fonctionne-un-scan-nmap-vue-globale)
4. [Subnetworks (sous-réseaux) et notation CIDR](#subnetworks-sous-réseaux-et-notation-cidr)
5. [Énumérer / spécifier des cibles (Targets)](#énumérer--spécifier-des-cibles-targets)
6. [Host Discovery : concepts & options clés](#host-discovery--concepts--options-clés)
7. [ARP Scan (-PR)](#arp-scan--pr)
8. [ICMP Echo Scan (-PE)](#icmp-echo-scan--pe)
9. [ICMP Timestamp Scan (-PP)](#icmp-timestamp-scan--pp)
10. [ICMP Address Mask Scan (-PM)](#icmp-address-mask-scan--pm)
11. [TCP SYN Ping Scan (-PS)](#tcp-syn-ping-scan--ps)
12. [TCP ACK Ping Scan (-PA)](#tcp-ack-ping-scan--pa)
13. [UDP Ping Scan (-PU)](#udp-ping-scan--pu)
14. [Ce que Nmap peut détecter](#ce-que-nmap-peut-détecter)
15. [Scanner une IP / une plage + lire la sortie](#scanner-une-ip--une-plage--lire-la-sortie)
16. [Vérifier des ports (port scan) : la suite logique](#vérifier-des-ports-port-scan--la-suite-logique)
17. [Optimisation & bonnes pratiques](#optimisation--bonnes-pratiques)
18. [Pièges fréquents](#pièges-fréquents)
19. [Exigences Holberton : scripts Bash 2 lignes](#exigences-holberton--scripts-bash-2-lignes)
20. [Cheat Sheet “Host Discovery”](#cheat-sheet-host-discovery)
21. [Références (officielles)](#références-officielles)

---

## C’est quoi Nmap ?

**Nmap (Network Mapper)** est un outil libre et open-source utilisé pour :
- **Découvrir des hôtes** actifs sur un réseau (host discovery / ping scan)
- **Scanner des ports**
- **Identifier des services** et parfois leurs versions
- **Déduire un OS** (OS fingerprinting) et d’autres caractéristiques (firewall/filtrage, latence, etc.)

Nmap fait tout ça en envoyant **des sondes réseau** (paquets) et en analysant **les réponses** (ou l’absence de réponse).

> Dans un pentest, Nmap sert surtout à **cartographier** une surface d’attaque : “qui est vivant ? quels services exposés ? quelles versions ? quelles erreurs de config ?”.

---

## Pourquoi “Live Host Discovery” ?

Quand tu as une plage d’IP (ex: `10.10.0.0/16`), scanner **toutes** les IP et **tous** les ports est :
- Long
- Bruyant (beaucoup de trafic)
- Souvent inutile

Le **host discovery** sert à réduire la liste :
> **Plage énorme → liste d’hôtes “up” → scan ports uniquement sur ces hôtes**

C’est un gain de temps et un bon réflexe **reconnaissance active**.

---

## Comment fonctionne un scan Nmap (vue globale)

Nmap suit souvent une logique par phases (conceptuellement) :

1. **Target specification** : quelles IP/hostnames ? (CIDR, ranges, fichiers…)
2. **Host discovery** : quels hôtes sont up ? (ARP/ICMP/TCP/UDP…)
3. **Port scanning** : quels ports sont ouverts/fermés/filtrés ?
4. **Service/version detection** : quel service derrière le port ? quelle version ?
5. **OS detection** : quelle signature OS probable ?
6. **NSE scripts** : scripts “discovery”, “safe”, parfois “vuln”, etc.
7. **Output/reporting** : sortie normale, grepable, XML, etc.

Dans ce module on se concentre sur **(2) Host Discovery**, mais tu dois comprendre le pipeline complet.

---

## Subnetworks (sous-réseaux) et notation CIDR

### 1) Rappel : IPv4
Une adresse IPv4 = 32 bits = 4 octets.
Ex : `192.168.1.10`

### 2) CIDR : `/n`
CIDR indique combien de bits sont “fixés” pour le réseau.

- `192.168.1.0/24` → 24 bits réseau, 8 bits hôte → **256 adresses** (0 à 255)
- `10.0.0.0/8` → 8 bits réseau, 24 bits hôte → **16 777 216 adresses** (énorme)
- `/32` → une seule IP (tous les bits fixés)

### 3) Pourquoi c’est important avec Nmap ?
Parce que tu vas scanner des plages du type :
- `10.10.10.0/24` (classique en lab)
- `192.168.0.0/16` (réseau d’entreprise)
- `172.16.0.0/12` (plage privée RFC1918)

---

## Énumérer / spécifier des cibles (Targets)

Nmap accepte plein de formats.

### 1) Une cible unique
- IP : `nmap <ip>`
- Hostname : `nmap <domaine>`

### 2) Un réseau (CIDR)
- `nmap 192.168.1.0/24`

### 3) Ranges par octets (IPv4)
Très pratique pour exclure .0 et .255, etc.
- `nmap 192.168.1.1-254`
- `nmap 192.168.3-5,7.1`

### 4) Plusieurs cibles
- `nmap 10.10.10.10 10.10.10.11 10.10.10.12`

### 5) Fichier de cibles
- `nmap -iL targets.txt`

### 6) Exclure des cibles
- `--exclude 10.10.10.1,10.10.10.254`
- `--excludefile exclude.txt`

### 7) Résolution DNS / pas de DNS
- `-n` : pas de DNS (souvent plus rapide)
- `-R` : forcer DNS (rarement utile)
- `--resolve-all` : scanner toutes les IP d’un hostname multi-A

> Conseil : en host discovery sur grosse plage, **-n** est souvent un gros gain.

---

## Host Discovery : concepts & options clés

### Vocabulaire
- **Host discovery** = “ping scanning” (au sens large)
- **Up** = Nmap a reçu une réponse indiquant que l’hôte existe/est joignable
- **Down** = aucune réponse aux probes (ou réponses “unreachable” selon contexte)
- **Filtered** (plutôt en ports) = un filtre/pare-feu empêche de conclure

### Options “cadre”
- `-sn` : **host discovery uniquement** (pas de scan de ports)
- `-Pn` : **pas de host discovery** (considère tout “up” et scanne tout)
- `-sL` : liste les cibles sans envoyer de paquets (souvent fait quand même du reverse DNS)

### Options “probe types” (celles que tu dois connaître)
- `-PR` : ARP ping (LAN)
- `-PE` : ICMP echo request (ping “classique”)
- `-PP` : ICMP timestamp request
- `-PM` : ICMP address mask request
- `-PS<ports>` : TCP SYN ping
- `-PA<ports>` : TCP ACK ping
- `-PU<ports>` : UDP ping

> Tu peux combiner plusieurs types pour augmenter les chances de détecter l’hôte :
> `nmap -sn -PE -PS443 -PA80 <targets>`

### Important : “privilégié” vs “non-privilégié”
Sur Linux/Kali :
- En **root/sudo**, Nmap peut envoyer des **raw packets** (plus puissant).
- En **user**, Nmap doit parfois utiliser des méthodes “connect()” (plus limité, différent).

---

## ARP Scan (-PR)

### Concept
Sur un **LAN Ethernet**, avant d’envoyer un paquet IP à une IP locale, ta machine doit connaître son **adresse MAC**.
Elle utilise ARP (Address Resolution Protocol) :
- “Qui a 192.168.1.42 ? Répond avec ta MAC.”

### Pourquoi ARP est excellent pour la découverte ?
- Très rapide
- Très fiable sur LAN
- Pas bloqué par un firewall IP (car c’est couche 2 / link layer)

### Quand ça marche ?
- **Uniquement** quand la cible est sur le **même réseau local** (même segment L2), typiquement même /24 ou VLAN (selon routing).

### Commandes utiles
Ping sweep ARP pur :
```bash
sudo nmap -sn -PR 192.168.1.0/24
```

Voir les paquets envoyés (apprentissage) :
```bash
sudo nmap -sn -PR --packet-trace 192.168.1.0/24
```

### Lecture de sortie (idée)
Tu verras souvent :
- `Nmap scan report for 192.168.1.X`
- `Host is up`
- parfois `MAC Address: ... (Vendor)`

---

## ICMP Echo Scan (-PE)

### Concept
C’est le ping “classique” :
- Envoi ICMP Echo Request (type 8)
- Réception ICMP Echo Reply (type 0)

### Quand l’utiliser ?
- Réseaux internes où ICMP n’est pas filtré
- Détection simple et rapide sur petites plages

### Commande
```bash
sudo nmap -sn -PE 10.10.10.0/24
```

### Limites
Beaucoup d’infras filtrent ICMP Echo (surtout sur Internet), donc tu peux “rater” des hôtes vivants.

---

## ICMP Timestamp Scan (-PP)

### Concept
ICMP Timestamp Request (type 13) → Timestamp Reply (type 14)

Intérêt :
- Certains admins bloquent Echo Request mais oublient Timestamp.

### Commande
```bash
sudo nmap -sn -PP 10.10.10.0/24
```

---

## ICMP Address Mask Scan (-PM)

### Concept
ICMP Address Mask Request (type 17) → Reply (type 18)

Même logique :
- Alternative ICMP qui peut passer quand Echo est filtré.

### Commande
```bash
sudo nmap -sn -PM 10.10.10.0/24
```

---

## TCP SYN Ping Scan (-PS)

### Concept
Nmap envoie un paquet TCP avec flag **SYN** vers un port donné (ou plusieurs).
- Si le port est **open** → souvent SYN/ACK en retour
- Si le port est **closed** → souvent RST en retour
Dans les deux cas, **une réponse TCP prouve que l’hôte est up**.

### Pourquoi c’est utile ?
- Beaucoup de firewalls laissent passer SYN vers ports “web” (80/443) ou services connus.
- Très bon pour découvrir des hôtes quand ICMP est filtré.

### Commandes
SYN ping sur 443 :
```bash
sudo nmap -sn -PS443 10.10.10.0/24
```

SYN ping multi-ports :
```bash
sudo nmap -sn -PS22,80,443 10.10.10.0/24
```

### Note importante
- Pas d’espace entre `-PS` et la liste de ports (`-PS443`, pas `-PS 443`)

---

## TCP ACK Ping Scan (-PA)

### Concept
Même principe mais avec flag **ACK**.
Un hôte “normal” répond souvent par **RST** (car pas de session existante).

### Pourquoi ça existe ?
- Certains firewalls filtrent les SYN mais laissent passer certains ACK (selon règles).
- Ça peut traverser des règles stateless qui bloquent les SYN vers ports non autorisés.

### Commandes
```bash
sudo nmap -sn -PA80 10.10.10.0/24
```

Multi-ports :
```bash
sudo nmap -sn -PA22,80,443 10.10.10.0/24
```

---

## UDP Ping Scan (-PU)

### Concept
Nmap envoie un datagramme UDP vers un port.
Si le port est **closed**, la cible répond souvent :
- ICMP Port Unreachable (type 3, code 3) → donc l’hôte est up
Si le port est open, beaucoup de services ignorent → pas de réponse → conclusion plus difficile.

### Pourquoi c’est utile ?
- Ça peut passer des filtres qui ne filtrent que TCP.
- Très pratique en complément (mais attention au bruit).

### Commandes
UDP ping sur port “rare” (Nmap a un défaut) :
```bash
sudo nmap -sn -PU 10.10.10.0/24
```

UDP ping sur DNS (53) ou NTP (123) selon contexte :
```bash
sudo nmap -sn -PU53,123 10.10.10.0/24
```

---

## Ce que Nmap peut détecter

Même si ton module est “host discovery”, tu dois savoir ce que Nmap sait faire :

- **Hôtes actifs** (host discovery)
- **Ports** : ouverts/fermés/filtrés
- **Services & versions** (`-sV`)
- **OS probable** (`-O`)
- **Traceroute** (`--traceroute`)
- **Scripts NSE** (découverte, audit, parfois vuln checks)
- **Type de filtrage/pare-feu** (déduction via réponses/absence de réponses)

> En pratique, Nmap sert à transformer un “réseau inconnu” en **carte exploitable**.

---

## Scanner une IP / une plage + lire la sortie

### 1) Ping scan simple
```bash
sudo nmap -sn 10.10.10.0/24
```

### 2) Sans DNS (plus rapide)
```bash
sudo nmap -n -sn 10.10.10.0/24
```

### 3) Forcer plusieurs probes (plus robuste)
```bash
sudo nmap -n -sn -PE -PS443 -PA80 10.10.10.0/24
```

### 4) Sortie : points à repérer
- `Nmap scan report for <ip>`
- `Host is up` (+ parfois latence)
- (LAN) `MAC Address: ...`
- En fin : `Nmap done: X IP addresses (Y hosts up) scanned in ...`

### 5) Comprendre “Host seems down”
Ce message arrive souvent si :
- ICMP bloqué
- SYN/ACK/RST bloqués
- Perte réseau / route
- Tu n’es pas en “sudo” et tes probes sont limitées

Solution : changer de probe, ajouter -PS/-PA, ou tester connectivité autrement.

---

## Vérifier des ports (port scan) : la suite logique

Après avoir trouvé des hôtes “up”, tu passes aux ports.

### 1) Scan des 1000 ports TCP “par défaut”
```bash
sudo nmap 10.10.10.10
```

### 2) Ports spécifiques
```bash
sudo nmap -p 22,80,443 10.10.10.10
```

### 3) Tous les ports TCP
```bash
sudo nmap -p- 10.10.10.10
```

### 4) Scan rapide des ports les plus courants
```bash
sudo nmap -F 10.10.10.10
```

> Important : un host “up” ne veut pas dire “vulnérable”.
> Ça veut dire “joignable”, et donc “investigable”.

---

## Optimisation & bonnes pratiques

### 1) Réduire le bruit
- Host discovery d’abord (`-sn`)
- DNS off (`-n`)
- Scanner ce qui est nécessaire (ports ciblés)

### 2) Temps et performance
- Sur grandes plages, le DNS peut ralentir → `-n`
- Les firewalls peuvent te faire “perdre” des hosts → combine probes
- Ne spam pas : en entreprise, tu peux déclencher des alertes IDS/IPS

### 3) Sorties exploitables
Tu peux enregistrer :
- Normal : `-oN out.txt`
- Grepable (moins utilisé aujourd’hui) : `-oG out.gnmap`
- XML : `-oX out.xml`

Ex :
```bash
sudo nmap -n -sn 10.10.10.0/24 -oN ping-scan.txt
```

---

## Pièges fréquents

1. **Scanner le mauvais réseau** (mauvais CIDR) → commence par `-sL` ou petite plage
2. **Oublier sudo** → probes moins efficaces
3. **Croire que “down” = inexistant** → parfois juste filtré
4. **ARP sur réseau non local** → ça n’a pas de sens (couche 2)
5. **Résolution DNS lente** → ajoute `-n`

---

## Exigences Holberton : scripts Bash 2 lignes

Tu as des contraintes très spécifiques. Voici comment les respecter **sans tricher** :

### Contraintes à respecter (rappel)
- **2 lignes exactement** (`wc -l` → 2)
- ligne 1 : `#!/bin/bash`
- ligne 2 : une commande (pas de `;`, pas de `&&`, pas de `||`, pas de backticks)
- **$1 sans guillemets** (ne pas écrire `"$1"` ni `'$1'`)
- fichier exécutable + newline final
- tester sur Kali

### Exemple de squelette (structure)
```bash
#!/bin/bash
nmap -n -sn $1
```

> Oui, en Bash “normal”, on mettrait plutôt `"$1"` pour la sécurité.
> Ici, **suis la consigne** du projet.

### Pourquoi “fin de fichier avec newline” ?
- Sur Unix, une “ligne” se termine par `\n`.
- Certains outils (compteurs de lignes, concat, parsers) supposent cette convention.
- C’est aussi ce qui rend les diff/patch et les scripts plus robustes.

---

## Cheat Sheet “Host Discovery”

### Basique
- Ping scan : `sudo nmap -sn <targets>`
- Pas de DNS : `sudo nmap -n -sn <targets>`

### Forcer un type
- ARP : `sudo nmap -sn -PR <LAN targets>`
- ICMP echo : `sudo nmap -sn -PE <targets>`
- ICMP timestamp : `sudo nmap -sn -PP <targets>`
- ICMP mask : `sudo nmap -sn -PM <targets>`
- TCP SYN : `sudo nmap -sn -PS443 <targets>`
- TCP ACK : `sudo nmap -sn -PA80 <targets>`
- UDP : `sudo nmap -sn -PU53,123 <targets>`

### Skipper le host discovery (à éviter en général)
- `sudo nmap -Pn <targets>`

### Lister les cibles sans packets
- `nmap -sL <targets>`

---

## Références (officielles)

- Nmap Documentation : https://nmap.org/docs.html  
- Nmap Reference Guide — Host Discovery : https://nmap.org/book/man-host-discovery.html  
- Nmap Reference Guide — Target Specification : https://nmap.org/book/man-target-specification.html  
- Nmap Book (Nmap Network Scanning) : https://nmap.org/book/  
