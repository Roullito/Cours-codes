# Cours ultra complet — Nmap Advanced Port Scans

## Sommaire

1. [Introduction](#1-introduction)
2. [Rappels indispensables : ports, TCP, UDP, états Nmap](#2-rappels-indispensables--ports-tcp-udp-états-nmap)
3. [Pourquoi parler de “Advanced Port Scans” ?](#3-pourquoi-parler-de-advanced-port-scans-)
4. [Différence entre un scan Nmap standard et un scan avancé](#4-différence-entre-un-scan-nmap-standard-et-un-scan-avancé)
5. [Comment fonctionnent les scans avancés Nmap](#5-comment-fonctionnent-les-scans-avancés-nmap)
6. [Les états de ports renvoyés par Nmap](#6-les-états-de-ports-renvoyés-par-nmap)
7. [Les grands types de scans avancés](#7-les-grands-types-de-scans-avancés)
8. [TCP Connect Scan (`-sT`)](#8-tcp-connect-scan--st)
9. [TCP SYN Scan (`-sS`)](#9-tcp-syn-scan--ss)
10. [UDP Scan (`-sU`)](#10-udp-scan--su)
11. [TCP ACK Scan (`-sA`)](#11-tcp-ack-scan--sa)
12. [TCP FIN / NULL / Xmas (`-sF`, `-sN`, `-sX`)](#12-tcp-fin--null--xmas--sf--sn--sx)
13. [TCP Window Scan (`-sW`)](#13-tcp-window-scan--sw)
14. [TCP Maimon Scan (`-sM`)](#14-tcp-maimon-scan--sm)
15. [TCP Idle Scan (`-sI`)](#15-tcp-idle-scan--si)
16. [Choisir les ports à scanner intelligemment](#16-choisir-les-ports-à-scanner-intelligemment)
17. [Détection de version, d’OS et enrichissement des résultats](#17-détection-de-version-dos-et-enrichissement-des-résultats)
18. [Pare-feu, IDS/IPS et évitement basique](#18-pare-feu-idsips-et-évitement-basique)
19. [Ce qu’un advanced port scan peut révéler](#19-ce-quun-advanced-port-scan-peut-révéler)
20. [Cas d’usage concrets en pentest, audit et défense](#20-cas-dusage-concrets-en-pentest-audit-et-défense)
21. [Comparatif synthétique des scans](#21-comparatif-synthétique-des-scans)
22. [Méthodologie pratique recommandée](#22-méthodologie-pratique-recommandée)
23. [Commandes utiles à connaître par cœur](#23-commandes-utiles-à-connaître-par-cœur)
24. [Pièges classiques et erreurs d’interprétation](#24-pièges-classiques-et-erreurs-dinterprétation)
25. [Partie spéciale projet Holberton : contraintes de scripts Bash](#25-partie-spéciale-projet-holberton--contraintes-de-scripts-bash)
26. [Mini fiche mémo](#26-mini-fiche-mémo)
27. [Conclusion](#27-conclusion)
28. [Références recommandées](#28-références-recommandées)

---

## 1. Introduction

Nmap est avant tout un **outil de cartographie réseau et de scan de ports**. Son objectif n’est pas seulement de dire “ce port est ouvert” : bien utilisé, il permet aussi de comprendre **comment la cible répond**, **ce qu’un pare-feu laisse passer**, **quels services semblent exposés**, **comment ils réagissent à différents types de paquets**, et parfois **quelle logique de filtrage** protège la machine.

Le module **Nmap Advanced Port Scans** pousse plus loin qu’un scan “classique”.  
Au lieu d’envoyer toujours le même type de paquet, on varie les techniques pour répondre à des questions précises :

- le port est-il réellement ouvert, fermé ou filtré ?
- le pare-feu est-il **stateful** ou **stateless** ?
- est-ce que la cible réagit différemment selon les **flags TCP** ?
- peut-on distinguer un **service exposé** d’un simple **filtrage** ?
- comment scanner quand on n’a pas les privilèges root ?
- comment tester **TCP** et **UDP** proprement ?
- quelles techniques sont plus **bruyantes**, plus **rapides**, plus **fiables** ou plus **discrètes** ?

Ce cours vise à te donner non seulement les commandes, mais aussi la **logique réseau** derrière chaque scan.

---

## 2. Rappels indispensables : ports, TCP, UDP, états Nmap

### 2.1 Qu’est-ce qu’un port ?

Un port est un **point logique de communication** sur une machine.  
Une même IP peut exposer plusieurs services grâce à des numéros de port différents :

- `22/tcp` → souvent SSH
- `80/tcp` → souvent HTTP
- `443/tcp` → souvent HTTPS
- `53/udp` → souvent DNS
- `161/udp` → souvent SNMP

Un port **n’est pas un service**, mais un **numéro** sur lequel un service peut écouter.

### 2.2 TCP vs UDP

#### TCP
TCP est orienté connexion.  
Avant d’échanger des données, il y a un **three-way handshake** :

1. Client → `SYN`
2. Serveur → `SYN/ACK`
3. Client → `ACK`

Grâce à ce comportement prévisible, Nmap peut fabriquer plusieurs stratégies de scan.

#### UDP
UDP est non orienté connexion.  
Il n’y a pas de handshake standard équivalent. Cela rend le scan :

- plus ambigu,
- souvent plus lent,
- parfois plus difficile à interpréter.

### 2.3 Pourquoi les scans de ports sont importants ?

Un scan de ports sert à identifier la **surface d’exposition réseau** :

- quels services sont joignables,
- quels ports sont inutiles mais ouverts,
- quelles règles de pare-feu sont actives,
- quels services sont mal configurés ou oubliés,
- quelles machines présentent une exposition anormale.

En offensive, cela prépare l’énumération et la recherche de vulnérabilités.  
En défense, cela sert à **vérifier l’hygiène d’exposition**.

---

## 3. Pourquoi parler de “Advanced Port Scans” ?

Un scan standard répond souvent à une question simple :  
**“Quels ports paraissent ouverts ?”**

Un scan avancé répond à des questions plus fines :

- ce port est-il **filtré** par un firewall ?
- le firewall est-il **stateful** ?
- la pile TCP/IP de la cible suit-elle strictement le comportement standard ?
- est-ce qu’une technique plus discrète ou plus adaptée à la situation existe ?
- comment scanner un réseau quand le SYN scan n’est pas possible ?
- comment enrichir l’analyse avec version, OS, ports TCP et UDP, etc. ?

Le mot “advanced” ne signifie pas seulement “plus compliqué”.  
Il signifie surtout : **scan plus ciblé, plus interprétable, plus contextuel**.

---

## 4. Différence entre un scan Nmap standard et un scan avancé

### 4.1 Scan standard
Dans l’usage courant, beaucoup de gens lancent simplement :

```bash
sudo nmap 192.168.1.10
```

Cela suffit pour une première vue, mais :

- Nmap scanne par défaut les **1000 ports les plus fréquents**,
- il ne teste pas forcément toutes les subtilités de filtrage,
- il ne dit pas toujours pourquoi un port semble inaccessible,
- il ne couvre pas forcément UDP,
- il ne donne pas toute la logique défensive derrière la cible.

### 4.2 Scan avancé
Un scan avancé choisit explicitement :

- **le type de scan** (`-sS`, `-sT`, `-sA`, `-sU`, `-sF`, `-sN`, `-sX`, `-sW`, `-sM`, `-sI`),
- **les ports** (`-p 1-1024`, `-p-`, `--top-ports 100`, `-F`),
- parfois **le rythme** (`-T4`),
- parfois **l’enrichissement** (`-sV`, `-O`),
- parfois **l’évasion basique** (`-f`, `--mtu`),
- et surtout **l’objectif précis du scan**.

Autrement dit :

> Un scan standard cherche surtout des ports ouverts.  
> Un scan avancé cherche à comprendre **la posture réseau** de la cible.

---

## 5. Comment fonctionnent les scans avancés Nmap

### 5.1 Idée générale

Chaque type de scan repose sur un principe :

- envoyer un **certain type de paquet**,
- observer la **réponse**,
- classer le port selon un **état**.

### 5.2 Ce que Nmap observe

Nmap interprète principalement :

- une réponse TCP `SYN/ACK`,
- une réponse TCP `RST`,
- l’absence totale de réponse,
- certains messages ICMP d’inaccessibilité.

### 5.3 Pourquoi plusieurs scans existent-ils ?

Parce que selon :

- le **protocole** (TCP ou UDP),
- le **pare-feu**,
- le **type de filtrage**,
- l’OS,
- les privilèges disponibles,
- la visibilité souhaitée,

un scan sera :

- plus fiable,
- plus rapide,
- plus discret,
- plus révélateur.

---

## 6. Les états de ports renvoyés par Nmap

Nmap n’emploie pas seulement “open” ou “closed”. Il utilise plusieurs états.

### 6.1 `open`
Le port accepte activement des connexions ou des paquets applicatifs.  
Un service est probablement à l’écoute.

### 6.2 `closed`
Le port est joignable, mais aucun service n’y écoute.

### 6.3 `filtered`
Nmap pense qu’un filtre réseau empêche d’obtenir une réponse claire.  
Souvent :

- firewall,
- ACL,
- filtrage intermédiaire,
- ICMP bloqué,
- règles réseau.

### 6.4 `unfiltered`
Le port est joignable, mais le scan utilisé ne permet pas de dire s’il est ouvert ou fermé.  
Typiquement rencontré avec un **ACK scan**.

### 6.5 `open|filtered`
Nmap n’arrive pas à distinguer entre “open” et “filtered”.  
Très fréquent en **UDP**, **FIN**, **NULL**, **Xmas**.

### 6.6 `closed|filtered`
Ambiguïté particulière, notamment avec certains scans comme **Idle scan**.

### 6.7 Point fondamental

Les états Nmap décrivent **la perception du scanner**, pas une vérité absolue.  
Un même port peut sembler :

- `open` depuis le LAN,
- `filtered` depuis Internet,
- `closed` depuis un autre segment.

---

## 7. Les grands types de scans avancés

Voici les scans majeurs à connaître pour ce module :

### TCP
- `-sT` → TCP Connect Scan
- `-sS` → TCP SYN Scan
- `-sA` → TCP ACK Scan
- `-sF` → TCP FIN Scan
- `-sN` → TCP NULL Scan
- `-sX` → TCP Xmas Scan
- `-sW` → TCP Window Scan
- `-sM` → TCP Maimon Scan
- `-sI` → TCP Idle Scan

### UDP
- `-sU` → UDP Scan

---

## 8. TCP Connect Scan (`-sT`)

### 8.1 Principe

Le **TCP Connect Scan** s’appuie sur l’appel système `connect()`.  
Au lieu d’émettre lui-même des paquets raw comme le SYN scan, Nmap demande à l’OS :

> “Essaie d’ouvrir une vraie connexion TCP à cette cible.”

### 8.2 Quand est-il utilisé ?

C’est le scan TCP par défaut quand le SYN scan n’est pas possible, notamment :

- quand tu n’as pas les privilèges permettant les raw packets,
- dans certains contextes IPv6,
- ou quand tu lances Nmap sans capacités suffisantes.

### 8.3 Comportement réseau

#### Port ouvert
1. Scanner → `SYN`
2. Cible → `SYN/ACK`
3. Scanner → `ACK`
4. Connexion établie
5. Fermeture rapide (`RST` en général côté OS/Nmap)

#### Port fermé
- la cible répond généralement par `RST`

#### Port filtré
- pas de réponse ou ICMP d’erreur selon le filtrage

### 8.4 Avantages
- fonctionne sans raw packet privileges,
- simple,
- fiable pour savoir si un port TCP est joignable.

### 8.5 Inconvénients
- plus “bruyant”,
- complète réellement la connexion sur les ports ouverts,
- plus susceptible d’être journalisé,
- moins discret que le SYN scan.

### 8.6 Quand l’utiliser ?
- quand tu n’es pas root,
- quand le SYN scan n’est pas disponible,
- quand tu veux un comportement proche d’un vrai client.

### 8.7 Exemple

```bash
sudo nmap -sT -p 22,80,443 192.168.1.10
```

### 8.8 À retenir
Le **TCP Connect Scan** est souvent le “plan B” du SYN scan.  
Il est plus visible, mais reste très utile.

---

## 9. TCP SYN Scan (`-sS`)

### 9.1 Principe

Le **SYN scan** est le scan vedette de Nmap.  
Il envoie un paquet `SYN` et observe la réponse sans terminer complètement la connexion.

### 9.2 Pourquoi l’appelle-t-on souvent “half-open scan” ?
Parce qu’il n’achève pas le three-way handshake complet.

### 9.3 Comportement réseau

#### Port ouvert
1. Scanner → `SYN`
2. Cible → `SYN/ACK`
3. Scanner → `RST`

Le scan comprend que le port est ouvert, mais coupe avant établissement complet.

#### Port fermé
1. Scanner → `SYN`
2. Cible → `RST`

#### Port filtré
- aucune réponse,
- ou message ICMP d’inaccessibilité.

### 9.4 Avantages
- rapide,
- efficace,
- moins bruyant qu’un connect scan,
- très bon standard pour TCP,
- large compatibilité.

### 9.5 Inconvénients
- nécessite généralement des privilèges élevés,
- peut être détecté par IDS/IPS,
- n’est pas “invisible”.

### 9.6 Quand l’utiliser ?
- presque tout le temps pour un scan TCP sérieux,
- comme scan principal de découverte de services TCP.

### 9.7 Exemple

```bash
sudo nmap -sS -T4 192.168.1.10
```

### 9.8 SYN Scan vs TCP Connect Scan

| Critère | `-sS` SYN | `-sT` Connect |
|---|---|---|
| Connexion complète ? | Non | Oui |
| Discrétion relative | Meilleure | Moins bonne |
| Rapidité | Très bonne | Bonne à moyenne |
| Privilèges élevés requis | Oui, en général | Non |
| Journalisation côté service | Moins probable | Plus probable |

### 9.9 Réponse attendue à l’objectif pédagogique
**Différence principale entre un TCP Connect Scan et un SYN Scan :**

- `-sT` effectue une **vraie connexion TCP complète** via l’OS ;
- `-sS` envoie directement un `SYN`, lit la réponse, puis coupe avant la connexion complète.

---

## 10. UDP Scan (`-sU`)

### 10.1 Pourquoi UDP mérite une section à part ?

Parce que beaucoup d’étudiants se concentrent sur TCP et oublient que des services critiques tournent en UDP :

- DNS (`53/udp`)
- SNMP (`161/udp`, `162/udp`)
- DHCP (`67/udp`, `68/udp`)
- NTP (`123/udp`)
- TFTP (`69/udp`)
- syslog (`514/udp`)
- SIP, RTP, services industriels, etc.

### 10.2 Principe

Nmap envoie un paquet UDP vers le port ciblé.

### 10.3 Interprétation typique

#### Réponse UDP reçue
- le port est considéré comme `open`

#### ICMP port unreachable (type 3, code 3)
- le port est `closed`

#### Autres ICMP unreachable liés au filtrage
- le port est `filtered`

#### Aucune réponse
- le port est `open|filtered`

### 10.4 Pourquoi `open|filtered` est fréquent ?

Parce qu’un service UDP **ouvert** répond souvent peu ou pas du tout à un paquet vide ou inattendu.  
Mais un **firewall** peut aussi simplement silencier le paquet.

Donc absence de réponse = ambiguïté.

### 10.5 Pourquoi le scan UDP est lent ?

Beaucoup d’hôtes limitent le débit de réponses ICMP.  
Résultat :

- Nmap doit attendre,
- retransmettre,
- temporiser davantage.

### 10.6 Comment améliorer un scan UDP ?

En combinant avec la détection de version :

```bash
sudo nmap -sUV 192.168.1.10
```

`-sV` peut parfois transformer un `open|filtered` en `open`, car Nmap envoie des sondes applicatives plus pertinentes.

### 10.7 Quand l’utiliser ?
- quand tu audites sérieusement un hôte ou un réseau,
- quand tu ne veux pas ignorer DNS, SNMP, NTP, etc.,
- quand tu fais un audit défensif complet.

### 10.8 Exemple

```bash
sudo nmap -sU -p 53,67,68,69,123,161,162 192.168.1.10
```

### 10.9 Point important
Un audit qui ignore UDP est souvent **incomplet**.

---

## 11. TCP ACK Scan (`-sA`)

### 11.1 Objectif
L’ACK scan ne sert **pas** à trouver directement les ports ouverts.  
Il sert surtout à **cartographier les règles de pare-feu**.

### 11.2 Principe

Nmap envoie un paquet avec **seulement le flag ACK**.

### 11.3 Interprétation

#### Réponse `RST`
- le port est classé `unfiltered`

Cela veut dire :
- le paquet ACK est arrivé à la cible,
- mais on ne sait pas si le port est ouvert ou fermé.

#### Pas de réponse ou ICMP de filtrage
- le port est `filtered`

### 11.4 Pourquoi est-ce utile ?

L’ACK scan aide à répondre à des questions comme :

- le firewall laisse-t-il passer certains paquets TCP déjà “établis” ?
- le filtrage est-il **stateful** ?
- quelles zones semblent filtrées ou non filtrées ?

### 11.5 Stateful vs stateless firewall

#### Stateful firewall
Il garde la mémoire des connexions.  
Un paquet ACK isolé, non lié à une session existante, est suspect et souvent bloqué.

#### Stateless firewall
Il applique des règles paquet par paquet sans contexte.  
Il peut laisser passer certains paquets ACK selon ses règles.

### 11.6 Quand utiliser `-sA` ?
- après un SYN scan,
- pour comprendre un comportement de filtrage,
- pour tester la logique du pare-feu,
- pour distinguer accessibilité réseau et état réel du port.

### 11.7 Exemple

```bash
sudo nmap -sA -p 1-1024 192.168.1.10
```

### 11.8 Réponse attendue à l’objectif pédagogique
**Comment un ACK Scan aide à déterminer des règles de firewall ?**

Parce qu’il ne cherche pas “open/closed”, mais plutôt :

- **est-ce que ce type de paquet passe ?**
- **est-ce que le port est joignable ou filtré ?**
- **le firewall traite-t-il l’ACK comme un paquet autorisé ou suspect ?**

---

## 12. TCP FIN / NULL / Xmas (`-sF`, `-sN`, `-sX`)

Ces trois scans sont souvent étudiés ensemble, car ils reposent sur la même logique.

### 12.1 Idée générale

Ils exploitent un comportement décrit dans la RFC TCP :

- sur un **port fermé**, un paquet anormal déclenche souvent un `RST`,
- sur un **port ouvert**, ce paquet peut être **ignoré**.

### 12.2 FIN Scan (`-sF`)
Envoie un paquet avec le flag `FIN`.

### 12.3 NULL Scan (`-sN`)
Envoie un paquet sans aucun flag TCP positionné.

### 12.4 Xmas Scan (`-sX`)
Envoie un paquet avec les flags `FIN`, `PSH` et `URG` activés.  
On l’appelle “Xmas” car le paquet “illumine” plusieurs bits.

### 12.5 Interprétation typique

#### Réponse `RST`
- port `closed`

#### Pas de réponse
- `open|filtered`

#### ICMP de filtrage
- `filtered`

### 12.6 Pourquoi ces scans sont intéressants ?

Parce qu’ils peuvent parfois traverser certains **filtres non stateful** qui bloquent surtout les paquets `SYN`.

### 12.7 Limite majeure
Ils ne fonctionnent pas parfaitement partout.  
Certaines piles TCP/IP, notamment historiquement plusieurs systèmes dérivés de Windows, ne suivent pas ce comportement de façon utile pour ces scans.

### 12.8 Ce qu’ils révèlent
Ils ne donnent pas toujours un “open” net.  
Ils révèlent souvent plutôt :

- `closed`
- `filtered`
- `open|filtered`

Donc leur intérêt est souvent **comparatif** ou **complémentaire**.

### 12.9 Quand les utiliser ?
- pour compléter un SYN scan,
- pour tester un filtrage particulier,
- pour observer des différences de comportement réseau,
- pour un exercice pédagogique avancé.

### 12.10 Exemples

```bash
sudo nmap -sF 192.168.1.10
sudo nmap -sN 192.168.1.10
sudo nmap -sX 192.168.1.10
```

### 12.11 Réponse attendue à l’objectif pédagogique
**Que sont les scans FIN, NULL et Xmas, et comment servent-ils à déterminer l’état des ports ?**

Ce sont des scans TCP basés sur des **combinaisons inhabituelles de flags**.  
Ils s’appuient sur le fait que :

- un port **fermé** répond souvent par `RST`,
- un port **ouvert** peut ignorer ces paquets,
- un filtre peut aussi les bloquer silencieusement.

Résultat : ils aident à distinguer **fermé** de **filtré** ou **open|filtered**, surtout en complément d’autres scans.

---

## 13. TCP Window Scan (`-sW`)

### 13.1 Principe

Le Window scan ressemble à l’ACK scan, mais il examine un détail d’implémentation :  
la **valeur du champ TCP Window** dans la réponse `RST`.

### 13.2 Idée
Sur certains systèmes :

- une réponse `RST` avec fenêtre **non nulle** peut indiquer `open`,
- une réponse `RST` avec fenêtre **nulle** peut indiquer `closed`.

### 13.3 États possibles
- `open`
- `closed`
- `filtered`

### 13.4 Limite
Ce scan dépend d’un comportement d’implémentation qui n’est **pas universel**.  
Donc il peut être très utile dans certains cas, mais totalement trompeur dans d’autres.

### 13.5 Quand l’utiliser ?
- pour enrichir une analyse TCP avancée,
- pour recouper avec FIN/ACK/SYN,
- pour laboratoire ou approfondissement.

### 13.6 Exemple

```bash
sudo nmap -sW 192.168.1.10
```

### 13.7 À retenir
Le Window scan est un **scan de niche** :
- très intéressant pédagogiquement,
- utile ponctuellement,
- mais moins universel que `-sS`.

---

## 14. TCP Maimon Scan (`-sM`)

### 14.1 Principe

Le Maimon scan envoie un paquet `FIN/ACK`.

### 14.2 Origine
Il exploite un comportement observé historiquement sur certains systèmes BSD-like.

### 14.3 Interprétation
Typiquement :

- pas de réponse → `open|filtered`
- `RST` → `closed`
- ICMP de filtrage → `filtered`

### 14.4 Limite
Aujourd’hui, ce scan est rarement très utile sur les systèmes modernes.  
Beaucoup répondent simplement `RST` partout, ce qui rend l’analyse pauvre.

### 14.5 Pourquoi l’étudier alors ?
Parce qu’il montre une idée clé :

> Certains scans Nmap reposent sur des **détails d’implémentation** ou des **comportements historiques** des piles TCP/IP.

### 14.6 Exemple

```bash
sudo nmap -sM 192.168.1.10
```

---

## 15. TCP Idle Scan (`-sI`)

### 15.1 Pourquoi ce scan est spécial ?

Parce qu’il permet un scan **blind** et **indirect**.  
Le principe est de scanner une cible **sans lui envoyer de paquets depuis ton IP réelle**.

### 15.2 Idée générale
Tu utilises un **zombie host** :

- un hôte tiers peu actif,
- avec un comportement IP ID prévisible.

### 15.3 Logique simplifiée

1. Tu mesures l’IP ID du zombie.
2. Tu envoies à la cible un `SYN` forgé comme s’il venait du zombie.
3. Tu remesures l’IP ID du zombie.

### 15.4 Interprétation

#### Si le port cible est ouvert
- la cible répond au zombie avec `SYN/ACK`,
- le zombie renvoie `RST`,
- son IP ID augmente davantage.

#### Si le port cible est fermé
- la cible renvoie `RST` au zombie,
- le zombie ignore,
- son IP ID n’augmente pas de la même façon.

### 15.5 Ce que cela apporte
- très grande discrétion vis-à-vis de la cible,
- possibilité de tester des **relations de confiance basées sur l’IP**,
- intérêt offensif élevé,
- intérêt pédagogique très fort.

### 15.6 Limites
- nécessite un bon zombie,
- plus complexe,
- moins pratique en conditions modernes,
- résultats parfois instables.

### 15.7 États
L’Idle scan peut retourner notamment `open` et `closed|filtered`.

### 15.8 Exemple conceptuel

```bash
sudo nmap -sI zombie_host 192.168.1.10
```

### 15.9 À retenir
L’Idle scan est l’un des scans les plus élégants théoriquement, mais pas le plus simple opérationnellement.

---

## 16. Choisir les ports à scanner intelligemment

### 16.1 Par défaut
Nmap scanne les **1000 ports les plus communs**.

### 16.2 Scanner des ports précis

```bash
sudo nmap -sS -p 22,80,443 192.168.1.10
```

### 16.3 Scanner une plage

```bash
sudo nmap -sS -p 1-1024 192.168.1.10
```

### 16.4 Scanner tous les ports TCP

```bash
sudo nmap -sS -p- 192.168.1.10
```

`-p-` signifie de **1 à 65535**.

### 16.5 Scan rapide des ports les plus fréquents

```bash
sudo nmap -F 192.168.1.10
```

### 16.6 `--top-ports`
Très utile pour un compromis vitesse / pertinence :

```bash
sudo nmap -sS --top-ports 100 192.168.1.10
```

### 16.7 Pourquoi le choix des ports est stratégique ?
Parce que scanner :

- trop peu → tu rates des services,
- trop large trop tôt → tu perds du temps,
- sans stratégie → tu génères du bruit inutile.

### 16.8 Méthode recommandée
Souvent :

1. scan rapide ciblé,
2. puis élargissement,
3. puis version detection,
4. puis UDP si nécessaire.

---

## 17. Détection de version, d’OS et enrichissement des résultats

Le scan de port seul répond à :  
**“quel port semble accessible ?”**

L’enrichissement répond à :  
**“quel service exact tourne ici ? quelle version ? quel OS ?”**

### 17.1 Service/version detection (`-sV`)

```bash
sudo nmap -sS -sV 192.168.1.10
```

Cela permet souvent d’identifier :

- protocole (HTTP, SSH, FTP, DNS…),
- application (Apache, nginx, OpenSSH…),
- version,
- parfois hostname, device type, CPE, famille d’OS.

### 17.2 OS detection (`-O`)

```bash
sudo nmap -sS -O 192.168.1.10
```

Nmap compare les réponses de la pile TCP/IP à sa base d’empreintes.

### 17.3 Mode agressif (`-A`)

```bash
sudo nmap -A 192.168.1.10
```

`-A` active plusieurs briques :
- détection de version,
- détection d’OS,
- scripts par défaut,
- traceroute.

Attention :
- plus riche,
- mais plus bruyant.

### 17.4 Pourquoi c’est lié au module ?
Parce qu’un **advanced scan** ne s’arrête pas au port.  
Il cherche à transformer un simple `open` en **information exploitable**.

---

## 18. Pare-feu, IDS/IPS et évitement basique

### 18.1 Tous les scans ne se valent pas face au filtrage

- `-sS` : excellent standard TCP
- `-sA` : très utile pour comprendre le firewall
- `-sF`, `-sN`, `-sX` : utiles contre certains filtres non-stateful
- `-sU` : indispensable mais souvent lent
- `-sI` : très discret en théorie

### 18.2 Évasion simple : fragmentation

```bash
sudo nmap -sS -f 192.168.1.10
```

Ou :

```bash
sudo nmap -sS --mtu 24 192.168.1.10
```

L’idée est de fragmenter pour compliquer l’inspection par certains filtres.

### 18.3 Attention
L’évasion n’est pas magique :
- certains équipements modernes reconstituent très bien les flux,
- certaines options rendent le scan plus instable,
- le résultat peut devenir moins fiable.

### 18.4 Timing (`-T0` à `-T5`)

```bash
sudo nmap -sS -T4 192.168.1.10
```

Templates :

- `-T0` paranoid
- `-T1` sneaky
- `-T2` polite
- `-T3` normal
- `-T4` aggressive
- `-T5` insane

#### Interprétation pratique
- `-T3` : défaut
- `-T4` : souvent très bon choix en lab / réseau fiable
- `-T5` : rapide mais plus agressif, plus risqué
- `-T0`/`-T1` : surtout pour réduire le rythme

### 18.5 Ce qu’il faut comprendre
Le timing influence :
- la durée,
- le risque de perte de précision,
- la visibilité,
- la charge réseau.

---

## 19. Ce qu’un advanced port scan peut révéler

Un scan avancé peut révéler bien plus que “port ouvert”.

### 19.1 Surface d’exposition
- quels ports sont ouverts,
- quels services tournent,
- quelles versions sont visibles.

### 19.2 Structure de filtrage
- ports filtrés,
- ports non filtrés,
- indices sur stateful/stateless firewall,
- incohérences de politique réseau.

### 19.3 Exposition UDP
- DNS,
- SNMP,
- NTP,
- services oubliés.

### 19.4 Indices d’architecture
- serveur web,
- bastion SSH,
- équipement réseau,
- imprimante,
- routeur,
- appliance.

### 19.5 Comportement TCP/IP
- réaction aux flags atypiques,
- détails exploitables pour fingerprinting,
- logique d’implémentation.

### 19.6 Relations de confiance ou indirectes
- particulièrement avec l’Idle scan,
- ou avec l’OS detection / séquences TCP.

### 19.7 Défauts de durcissement
- trop de ports ouverts,
- services obsolètes,
- ports administratifs exposés,
- services superflus.

---

## 20. Cas d’usage concrets en pentest, audit et défense

### 20.1 Découverte initiale d’une machine
Objectif :
- voir rapidement ce qui écoute.

Commande typique :

```bash
sudo nmap -sS -T4 192.168.1.10
```

### 20.2 Vérifier l’exposition complète TCP
Objectif :
- ne rien rater.

```bash
sudo nmap -sS -p- -T4 192.168.1.10
```

### 20.3 Vérifier les versions réellement exposées

```bash
sudo nmap -sS -sV -p 22,80,443,3306 192.168.1.10
```

### 20.4 Tester si le firewall filtre réellement

```bash
sudo nmap -sA -p 1-1024 192.168.1.10
```

### 20.5 Compléter un scan TCP par UDP

```bash
sudo nmap -sU -sV -p 53,123,161,162 192.168.1.10
```

### 20.6 Comparer plusieurs scans pour lever une ambiguïté
Exemple :
- un SYN scan,
- puis ACK,
- puis FIN ou Window.

L’objectif n’est pas de “faire tous les scans pour faire joli”, mais de **croiser les résultats**.

### 20.7 Audit défensif
Tu peux utiliser Nmap pour :
- vérifier qu’un serveur n’expose que ce qui est nécessaire,
- tester les règles de firewall après changement,
- faire une baseline avant mise en production,
- contrôler qu’un durcissement a bien fonctionné.

### 20.8 Environnement CI/CD ou infra as code
Après déploiement :
- scan automatique d’exposition,
- validation qu’aucun port non prévu n’est ouvert,
- contrôle des services attendus.

---

## 21. Comparatif synthétique des scans

| Scan | But principal | Révèle surtout | Avantages | Limites |
|---|---|---|---|---|
| `-sT` | Connexion TCP complète | open / closed / filtered | fonctionne sans raw packets | bruyant |
| `-sS` | Scan TCP standard avancé | open / closed / filtered | rapide, efficace | nécessite privilèges |
| `-sU` | Exposition UDP | open / closed / filtered / open\|filtered | indispensable pour audit complet | lent, ambigu |
| `-sA` | Cartographier firewall | filtered / unfiltered | excellent pour comprendre le filtrage | ne dit pas open/closed |
| `-sF` | Test TCP atypique | closed / filtered / open\|filtered | utile contre certains filtres | pas universel |
| `-sN` | Test TCP atypique | closed / filtered / open\|filtered | même logique que FIN | pas universel |
| `-sX` | Test TCP atypique | closed / filtered / open\|filtered | utile en complément | pas universel |
| `-sW` | Exploiter la fenêtre TCP | open / closed / filtered | parfois très révélateur | dépend de l’OS |
| `-sM` | Variante historique FIN/ACK | closed / filtered / open\|filtered | intérêt pédagogique | souvent peu utile aujourd’hui |
| `-sI` | Scan blind via zombie | open / closed\|filtered | très discret théoriquement | complexe, dépend d’un zombie |

---

## 22. Méthodologie pratique recommandée

Voici une méthode propre et logique.

### 22.1 Étape 1 — Vérifier l’hôte
Selon le contexte, un simple scan peut suffire, ou tu peux désactiver la découverte avec `-Pn` si besoin.

### 22.2 Étape 2 — Scan TCP principal
Le meilleur point de départ en général :

```bash
sudo nmap -sS -T4 target
```

### 22.3 Étape 3 — Tous les ports si nécessaire

```bash
sudo nmap -sS -p- -T4 target
```

### 22.4 Étape 4 — Enrichir les ports trouvés

```bash
sudo nmap -sS -sV -O -p <ports> target
```

### 22.5 Étape 5 — Explorer UDP critique

```bash
sudo nmap -sU -sV -p 53,67,68,69,123,161,162 target
```

### 22.6 Étape 6 — Analyser le filtrage

```bash
sudo nmap -sA -p <ports> target
```

### 22.7 Étape 7 — Utiliser des scans atypiques si ambiguïté
Par exemple :

```bash
sudo nmap -sF target
sudo nmap -sX target
sudo nmap -sW target
```

### 22.8 Règle d’or
Ne te contente jamais d’une seule sortie Nmap si le résultat paraît ambigu ou incohérent.

---

## 23. Commandes utiles à connaître par cœur

### 23.1 SYN scan classique
```bash
sudo nmap -sS 192.168.1.10
```

### 23.2 Connect scan
```bash
sudo nmap -sT 192.168.1.10
```

### 23.3 Tous les ports TCP
```bash
sudo nmap -sS -p- 192.168.1.10
```

### 23.4 Ports spécifiques
```bash
sudo nmap -sS -p 22,80,443 192.168.1.10
```

### 23.5 UDP ciblé
```bash
sudo nmap -sU -p 53,123,161 192.168.1.10
```

### 23.6 ACK scan
```bash
sudo nmap -sA 192.168.1.10
```

### 23.7 FIN / NULL / Xmas
```bash
sudo nmap -sF 192.168.1.10
sudo nmap -sN 192.168.1.10
sudo nmap -sX 192.168.1.10
```

### 23.8 Window scan
```bash
sudo nmap -sW 192.168.1.10
```

### 23.9 Maimon scan
```bash
sudo nmap -sM 192.168.1.10
```

### 23.10 Version detection
```bash
sudo nmap -sS -sV 192.168.1.10
```

### 23.11 OS detection
```bash
sudo nmap -sS -O 192.168.1.10
```

### 23.12 Mode agressif
```bash
sudo nmap -A 192.168.1.10
```

### 23.13 Timing agressif raisonnable
```bash
sudo nmap -sS -T4 192.168.1.10
```

### 23.14 Fragmentation
```bash
sudo nmap -sS -f 192.168.1.10
```

---

## 24. Pièges classiques et erreurs d’interprétation

### 24.1 Confondre “pas de réponse” et “port ouvert”
En UDP, FIN, NULL, Xmas :
- absence de réponse ne signifie pas automatiquement “open”.

### 24.2 Croire qu’ACK scan trouve les ports ouverts
Non.  
Il sert surtout à distinguer `filtered` de `unfiltered`.

### 24.3 Scanner seulement TCP
Erreur très fréquente.  
Tu peux manquer DNS, SNMP, NTP, DHCP, etc.

### 24.4 Croire qu’un scan “discret” est invisible
Même un SYN scan ou un FIN scan peut être vu.

### 24.5 Croire qu’un seul scan donne “la vérité”
Le scan donne une **observation** depuis un point donné, à un instant donné, avec une méthode donnée.

### 24.6 Oublier le contexte réseau
Un pare-feu intermédiaire, un NAT, un WAF, un IDS, un load balancer ou un rate-limit peuvent changer complètement l’interprétation.

### 24.7 Scanner trop large trop tôt
Toujours adapter :
- vitesse,
- profondeur,
- objectifs,
- bruit acceptable.

---

## 25. Partie spéciale projet Holberton : contraintes de scripts Bash

Tu as donné des contraintes très précises. Voici comment les intégrer proprement.

### 25.1 Contraintes à respecter
- éditeurs autorisés : `vi`, `vim`, `emacs`
- scripts testés sur **Kali Linux**
- chaque script doit faire **exactement 2 lignes**
- la première ligne doit être exactement :

```bash
#!/bin/bash
```

- le script doit utiliser **`sudo`**
- il faut utiliser **`$1` sans guillemets**
- pas de backticks
- pas de `&&`
- pas de `||`
- pas de `;`
- pas de `echo`
- fichiers exécutables
- fin de fichier avec saut de ligne
- utiliser des flags Nmap avec le bon format

### 25.2 Pourquoi `$1` sans guillemets ?
Parce que l’énoncé l’impose.  
Dans la vie réelle, on quote souvent les variables shell pour éviter certains effets de bord.  
Ici, tu suis **strictement la consigne projet**.

### 25.3 Pourquoi `sudo` ?
Beaucoup de scans avancés Nmap ont besoin des privilèges nécessaires aux raw packets.

### 25.4 Exemples de scripts conformes

#### SYN scan simple
```bash
#!/bin/bash
sudo nmap -sS $1
```

#### TCP Connect scan
```bash
#!/bin/bash
sudo nmap -sT $1
```

#### ACK scan
```bash
#!/bin/bash
sudo nmap -sA $1
```

#### Xmas scan
```bash
#!/bin/bash
sudo nmap -sX $1
```

#### UDP scan
```bash
#!/bin/bash
sudo nmap -sU $1
```

#### Scan de tous les ports TCP
```bash
#!/bin/bash
sudo nmap -sS -p- $1
```

### 25.5 Vérifier le nombre de lignes

```bash
wc -l nom_du_script
```

Le résultat doit être `2`.

### 25.6 Vérifier les permissions

```bash
chmod +x nom_du_script
```

### 25.7 Pourquoi éviter `echo` ?
Parce que l’énoncé l’interdit.  
Le script doit juste exécuter la commande Nmap.

---

## 26. Mini fiche mémo

### Les scans à connaître en priorité

#### `-sS`
Le meilleur scan TCP généraliste.

#### `-sT`
Alternative si pas de privilèges élevés ou pas de raw packets.

#### `-sU`
Indispensable pour ne pas rater les services UDP.

#### `-sA`
Très utile pour comprendre le firewall.

#### `-sF`, `-sN`, `-sX`
Scans atypiques pour affiner l’analyse face au filtrage.

#### `-sW`
Exploite le champ window sur certains OS.

#### `-sM`
Historique, à connaître surtout pour la culture technique.

#### `-sI`
Très avancé, blind scan via zombie.

### États Nmap à retenir
- `open`
- `closed`
- `filtered`
- `unfiltered`
- `open|filtered`
- `closed|filtered`

### Ports
- défaut : 1000 plus communs
- `-p 1-1024` → plage
- `-p-` → tous les ports
- `-F` → rapide
- `--top-ports 100` → top 100

### Enrichissement
- `-sV` → version
- `-O` → OS
- `-A` → agressif / enrichi
- `-T4` → bon compromis vitesse

---

## 27. Conclusion

Le vrai niveau sur Nmap ne consiste pas à réciter des flags.  
Il consiste à comprendre :

- **pourquoi** un scan existe,
- **quand** le choisir,
- **ce qu’il permet réellement d’inférer**,
- **ce qu’il ne permet pas de conclure**.

### En une phrase :

Un **scan standard** te dit surtout **quels ports semblent ouverts**.  
Un **advanced port scan** t’aide à comprendre **comment la cible, ses services et son pare-feu se comportent réellement**.

### Ce que tu dois savoir expliquer à la fin de ce module
Tu dois être capable d’expliquer simplement :

- la différence entre `-sT` et `-sS`,
- l’intérêt de `-sA` pour les règles de firewall,
- le fonctionnement de `-sF`, `-sN`, `-sX`,
- pourquoi UDP est ambigu et lent,
- ce que signifie `open|filtered`,
- pourquoi croiser plusieurs scans est souvent nécessaire,
- pourquoi Nmap est essentiel pour sécuriser les ports d’un système.

---

## 28. Références recommandées

### Documentation principale
- Nmap Network Scanning — *Port Scanning Basics*
- Nmap Network Scanning — *Port Scanning Techniques*
- Nmap Network Scanning — *TCP Connect Scan (`-sT`)*
- Nmap Network Scanning — *UDP Scan (`-sU`)*
- Nmap Network Scanning — *TCP ACK Scan (`-sA`)*
- Nmap Network Scanning — *TCP FIN, NULL, and Xmas Scans (`-sF`, `-sN`, `-sX`)*
- Nmap Network Scanning — *TCP Window Scan (`-sW`)*
- Nmap Network Scanning — *TCP Maimon Scan (`-sM`)*
- Nmap Network Scanning — *TCP Idle Scan (`-sI`)*
- Nmap Network Scanning — *Port Specification and Scan Order*
- Nmap Network Scanning — *Service and Version Detection*
- Nmap Network Scanning — *OS Detection*
- Nmap Network Scanning — *Timing and Performance*
- Nmap Network Scanning — *Firewall/IDS Evasion and Spoofing*

### Référence générale
- Port scanning — vue d’ensemble conceptuelle sur le balayage de ports

---

## Bonus — Stratégie d’apprentissage conseillée

Pour bien maîtriser ce module, entraîne-toi dans cet ordre :

1. `-sT`
2. `-sS`
3. `-sU`
4. `-sA`
5. `-sF`, `-sN`, `-sX`
6. `-sW`
7. `-sM`
8. `-sI`

Et pour chaque scan, pose-toi toujours les 5 questions suivantes :

1. Quel paquet Nmap envoie-t-il ?
2. Quelle réponse attend-il ?
3. Quels états peut-il conclure ?
4. Dans quel cas ce scan est-il utile ?
5. Dans quel cas peut-il être trompeur ?

Si tu sais répondre à ces 5 questions pour chaque type de scan, tu maîtrises réellement le sujet.
