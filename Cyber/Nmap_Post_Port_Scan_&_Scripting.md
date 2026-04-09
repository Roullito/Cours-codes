# Cours ultra complet — Nmap Post Port Scan & Scripting (NSE)

## 1. Introduction

Quand on débute avec Nmap, on pense souvent uniquement à la **découverte d’hôtes** et au **scan de ports**.
En réalité, dans un audit sérieux, le scan de ports n’est qu’un **point de départ**.

Une fois les ports ouverts identifiés, il faut répondre à des questions plus utiles :

- quel service tourne réellement derrière ce port ;
- quelle version est exposée ;
- quelle configuration peut être récupérée ;
- si le service présente des faiblesses connues ;
- si l’on peut automatiser l’énumération pour gagner du temps.

C’est précisément là qu’intervient la phase de **post port scan** et surtout le **Nmap Scripting Engine (NSE)**.

Le but de ce cours est de te donner une compréhension **solide, pédagogique et exploitable** de :

- la logique du post-scan avec Nmap ;
- le fonctionnement du moteur de scripts NSE ;
- les catégories de scripts ;
- les options de ligne de commande importantes ;
- l’organisation et l’exécution des scripts ;
- la lecture et la rédaction de documentation avec **NSEDoc** ;
- la manière d’utiliser tout cela proprement dans un projet Holberton / Kali Linux.

---

## 2. Qu’est-ce que le “post port scan” ?

Le **post port scan** désigne tout ce qu’on fait **après** avoir identifié les hôtes et les ports intéressants.

### 2.1 Objectif

Le scan initial te dit par exemple :

- 22/tcp ouvert
- 80/tcp ouvert
- 443/tcp ouvert
- 3306/tcp ouvert

Mais cela ne suffit pas pour comprendre réellement la surface d’attaque.

La phase post-scan sert à :

- **identifier précisément les services** ;
- **extraire des métadonnées** ;
- **détecter des configurations faibles** ;
- **rechercher des vulnérabilités** ;
- **automatiser des vérifications répétitives**.

### 2.2 Exemples de tâches post-scan

Après avoir trouvé un port 80 ouvert, tu peux vouloir :

- récupérer le titre HTTP ;
- identifier le serveur web ;
- voir les méthodes HTTP autorisées ;
- vérifier la présence de comptes par défaut ;
- relever des endpoints ou infos de version.

Après avoir trouvé un port 445 ouvert, tu peux vouloir :

- énumérer les partages SMB ;
- relever les versions SMB ;
- tester certaines mauvaises configurations ;
- vérifier des faiblesses connues.

Après avoir trouvé un port 53 ouvert, tu peux vouloir :

- tester un transfert de zone DNS ;
- récupérer des enregistrements ;
- identifier le type de serveur.

Autrement dit, le post-scan transforme un simple **inventaire de ports** en **renseignement opérationnel**.

---

## 3. Où se place NSE dans la chaîne d’analyse ?

Flux logique classique :

1. **Découverte d’hôtes**
2. **Scan de ports**
3. **Détection de services / versions**
4. **Scripts NSE**
5. **Analyse manuelle / corrélation**
6. **Validation de vulnérabilités**

NSE agit donc comme un **moteur d’automatisation** capable d’enrichir les résultats de scan.

---

## 4. Qu’est-ce que le Nmap Scripting Engine (NSE) ?

Le **Nmap Scripting Engine** est le moteur de scripts de Nmap.
Il permet d’exécuter des scripts écrits en **Lua** afin d’automatiser un grand nombre de tâches réseau et de sécurité.

### 4.1 Pourquoi NSE est important

NSE est important parce qu’il permet de dépasser le simple “quel port est ouvert ?”.
Il sert à :

- enrichir l’énumération ;
- automatiser des checks répétitifs ;
- interroger des services applicatifs ;
- détecter certaines vulnérabilités ;
- gagner un temps énorme pendant la reconnaissance technique.

### 4.2 Ce que NSE peut faire

Avec NSE, on peut par exemple :

- récupérer des bannières ;
- interroger un service HTTP, DNS, SMB, FTP, SSH, SNMP, LDAP, etc. ;
- lancer des scripts de découverte ;
- tester des comptes par défaut ;
- vérifier des vulnérabilités connues ;
- collecter des informations pour préparer la suite d’un audit.

### 4.3 Idée essentielle

Nmap fournit déjà un moteur de scan très rapide.
NSE ajoute une **couche logique programmable**, sans devoir réécrire tout un scanner soi-même.

---

## 5. Comment fonctionne NSE ?

### 5.1 Vue d’ensemble

Quand tu lances Nmap avec l’option `--script`, Nmap :

1. charge les scripts sélectionnés ;
2. charge les bibliothèques Lua nécessaires ;
3. décide **sur quelle cible** et **sur quels ports** chaque script doit s’exécuter ;
4. exécute les scripts en parallèle selon la logique interne de Nmap ;
5. fusionne les résultats dans la sortie finale.

### 5.2 Les grandes idées techniques

Un script NSE contient généralement :

- des métadonnées ;
- des catégories ;
- une description ;
- une ou plusieurs règles d’exécution ;
- une fonction `action()` qui produit le travail utile.

### 5.3 Les “rules” dans NSE

Les scripts ne s’exécutent pas au hasard.
Ils sont contrôlés par des règles.
Les plus importantes sont :

#### a) `prerule`
Le script s’exécute **avant** le scan principal.

Usage typique :
- initialisation ;
- découverte préalable ;
- collecte d’informations globales.

#### b) `hostrule`
Le script s’exécute au **niveau d’un hôte**.

Usage typique :
- actions liées à l’hôte entier ;
- résumés ;
- énumérations qui ne dépendent pas d’un port précis.

#### c) `portrule`
Le script s’exécute sur un **port/service particulier**.

C’est la forme la plus fréquente.
Exemple : un script HTTP ne s’exécute que si un port ressemble à un service web.

#### d) `postrule`
Le script s’exécute **après** le scan.

Usage typique :
- synthèse finale ;
- corrélation ;
- enrichissement après collecte.

### 5.4 Pourquoi c’est puissant

Cette logique permet à Nmap de ne pas lancer n’importe quel script sur n’importe quel port.
Donc :

- moins de bruit ;
- plus de pertinence ;
- meilleure performance ;
- résultats mieux ciblés.

---

## 6. Organisation des scripts NSE

### 6.1 Emplacement des scripts

Sur Kali Linux, les scripts NSE sont généralement installés dans un dossier du type :

```bash
/usr/share/nmap/scripts/
```

Les bibliothèques Lua associées se trouvent généralement dans :

```bash
/usr/share/nmap/nselib/
```

### 6.2 Base de données des scripts

Nmap maintient un index des scripts, souvent mis à jour avec :

```bash
nmap --script-updatedb
```

Cette commande est utile après :

- ajout d’un script personnalisé ;
- modification locale d’un script ;
- installation d’un nouveau paquet de scripts.

### 6.3 Comment repérer un script

Tu peux lister ou rechercher des scripts localement avec :

```bash
ls /usr/share/nmap/scripts/
```

ou :

```bash
ls /usr/share/nmap/scripts/ | grep http
```

---

## 7. Les catégories de scripts NSE

Les scripts NSE sont classés en **catégories**.
Comprendre ces catégories est fondamental, car elles indiquent le **niveau de risque**, le **but** et le **type d’action** du script.

Voici les catégories majeures.

### 7.1 `auth`
Scripts liés à l’authentification.

Exemples d’usage :
- récupération d’informations d’authentification ;
- tests autour des mécanismes de login ;
- énumération liée aux comptes.

### 7.2 `broadcast`
Scripts qui utilisent des requêtes de broadcast sur le réseau local.

Utiles pour :
- découvrir des hôtes ;
- récupérer des infos locales sur un segment.

### 7.3 `brute`
Scripts de brute force.

Important :
- à utiliser seulement dans un cadre autorisé ;
- peut être bruyant ;
- peut verrouiller des comptes ;
- peut avoir un impact fort.

### 7.4 `default`
Scripts suffisamment utiles, stables et raisonnablement sûrs pour être lancés avec `-sC` ou `--script=default`.

C’est souvent le meilleur point de départ pour de l’énumération automatique légère.

### 7.5 `discovery`
Scripts destinés à la découverte d’informations.

Exemples :
- noms de services ;
- métadonnées ;
- capacités d’un service ;
- découverte réseau.

### 7.6 `dos`
Scripts liés au déni de service.

Très sensibles.
À éviter sauf lab explicitement autorisé.

### 7.7 `exploit`
Scripts capables d’exploiter une vulnérabilité ou d’aller au-delà de la simple détection.

Très sensibles aussi.
Cadre légal obligatoire.

### 7.8 `external`
Scripts qui peuvent interagir avec des ressources externes.

Exemple :
- consultation de bases externes ;
- dépendance à un service hors cible.

### 7.9 `fuzzer`
Scripts de fuzzing.

Souvent plus agressifs.
Peuvent provoquer des comportements imprévus côté cible.

### 7.10 `info`
Scripts dont l’objectif principal est la collecte d’informations.

Très utiles pour la reconnaissance propre.

### 7.11 `intrusive`
Scripts considérés comme plus intrusifs.

Cela ne signifie pas forcément “exploit”, mais ils peuvent :
- modifier l’état du service ;
- générer plus de charge ;
- être moins discrets.

### 7.12 `malware`
Scripts destinés à détecter des traces ou comportements liés à des malwares.

### 7.13 `safe`
Scripts censés être raisonnablement sûrs.

En général :
- peu destructifs ;
- adaptés à une collecte modérée ;
- bons candidats pour la reconnaissance initiale.

### 7.14 `version`
Scripts conçus pour compléter ou améliorer la détection de version des services.

### 7.15 `vuln`
Scripts de détection de vulnérabilités.

Attention :
- “détecter” n’est pas toujours “exploiter” ;
- certains scripts peuvent rester légers ;
- d’autres sont plus intrusifs selon la logique du test.

---

## 8. Comprendre les catégories par stratégie d’utilisation

### 8.1 Catégories souvent sûres pour démarrer

En phase de reconnaissance initiale, on privilégie souvent :

- `default`
- `safe`
- `discovery`
- `info`
- parfois `version`

### 8.2 Catégories à manipuler avec prudence

- `intrusive`
- `brute`
- `vuln`
- `external`

### 8.3 Catégories hautement sensibles

- `dos`
- `exploit`
- `fuzzer`

Dans un environnement réel, ces catégories nécessitent une validation explicite du périmètre et des règles d’engagement.

---

## 9. Commandes essentielles pour lancer des scripts NSE

### 9.1 Utiliser les scripts par défaut

```bash
nmap -sC 192.168.1.10
```

ou équivalent :

```bash
nmap --script=default 192.168.1.10
```

### 9.2 Combiner détection de version + scripts par défaut

```bash
nmap -sV -sC 192.168.1.10
```

C’est une combinaison extrêmement classique.

### 9.3 Lancer un script spécifique

```bash
nmap --script=http-title -p 80 192.168.1.10
```

### 9.4 Lancer plusieurs scripts

```bash
nmap --script=http-title,http-headers,http-methods -p 80 192.168.1.10
```

### 9.5 Lancer une catégorie complète

```bash
nmap --script=safe 192.168.1.10
```

ou :

```bash
nmap --script=vuln 192.168.1.10
```

### 9.6 Utiliser des expressions de sélection

Tu peux combiner :

```bash
nmap --script="safe and http-*" -p 80,443 192.168.1.10
```

ou exclure :

```bash
nmap --script="default and not brute" 192.168.1.10
```

### 9.7 Fournir des arguments aux scripts

```bash
nmap --script smb-enum-users --script-args smbusername=admin,smbpassword=admin 192.168.1.10
```

### 9.8 Demander l’aide d’un script

```bash
nmap --script-help http-title
```

Ou pour une catégorie :

```bash
nmap --script-help vuln
```

---

## 10. Arguments de ligne de commande NSE à connaître

### 10.1 `--script`
Option principale pour choisir quels scripts lancer.

Peut prendre :

- un nom précis ;
- une catégorie ;
- un motif ;
- une expression logique.

Exemples :

```bash
nmap --script=http-title 10.10.10.10
nmap --script=default 10.10.10.10
nmap --script='http-*' 10.10.10.10
```

### 10.2 `--script-args`
Permet de transmettre des arguments aux scripts.

Exemple :

```bash
nmap --script ftp-anon --script-args ftp-anon.maxlist=20 10.10.10.10
```

### 10.3 `--script-args-file`
Charge des arguments depuis un fichier.
Utile si la ligne devient trop longue.

### 10.4 `--script-help`
Affiche l’aide et la documentation des scripts sélectionnés.

### 10.5 `--script-trace`
Très utile pour le debug.
Affiche les échanges script/service.

### 10.6 `-d` et `-dd`
Augmentent le niveau de debug général.
Très utile avec NSE si tu veux comprendre ce qui se passe réellement.

---

## 11. Scripts NSE très utiles en pratique

> Important : les exemples ci-dessous servent à comprendre la logique d’usage. Ils doivent être employés uniquement sur des systèmes autorisés.

### 11.1 Web

#### `http-title`
Récupère le titre d’une page web.

```bash
nmap --script=http-title -p 80,443 10.10.10.10
```

#### `http-headers`
Récupère les en-têtes HTTP.

#### `http-methods`
Liste les méthodes HTTP supportées.

#### `http-enum`
Tente d’énumérer des chemins web communs.

#### `http-default-accounts`
Teste la présence de comptes par défaut sur certaines applications web.

### 11.2 SMB

#### `smb-os-discovery`
Collecte des informations système via SMB.

#### `smb-enum-shares`
Énumère les partages.

#### `smb-enum-users`
Énumère des utilisateurs selon le contexte et les privilèges.

### 11.3 DNS

#### `dns-brute`
Bruteforce de sous-domaines dans certains cas.

#### `dns-zone-transfer`
Teste un transfert de zone.

### 11.4 FTP

#### `ftp-anon`
Teste l’accès anonyme FTP.

### 11.5 SSH

#### `ssh-hostkey`
Récupère les clés publiques hôte.

#### `ssh2-enum-algos`
Liste les algorithmes supportés.

### 11.6 TLS/SSL

#### `ssl-cert`
Récupère le certificat.

#### `ssl-enum-ciphers`
Énumère les suites cryptographiques supportées.

### 11.7 Vulnérabilités

#### `vulners` (si disponible selon installation)
Peut aider à faire correspondre versions/services et vulnérabilités connues.

#### Scripts `http-vuln-*`, `smb-vuln-*`, etc.
Scripts spécialisés pour vérifier certaines vulnérabilités.

---

## 12. Lire correctement les résultats NSE

L’erreur fréquente d’un débutant est de croire qu’un résultat NSE est une vérité absolue.

En pratique, il faut distinguer :

- **information observée** ;
- **interprétation** ;
- **preuve technique** ;
- **impact réel**.

### 12.1 Exemple

Si un script indique qu’une méthode HTTP `PUT` est autorisée, cela ne veut pas automatiquement dire qu’un upload exploitable est possible.

Il faut encore vérifier :

- sur quel chemin ;
- avec quel type de contenu ;
- si l’écriture est réellement autorisée ;
- si le contenu est ensuite servi ou exécuté.

### 12.2 Bonne méthode

Toujours faire :

1. collecte automatique ;
2. lecture critique ;
3. validation manuelle.

---

## 13. Comment les scripts sont exécutés et organisés en interne

### 13.1 Structure générale d’un script NSE

Un script contient souvent :

- description ;
- auteur ;
- licence ;
- catégories ;
- imports de bibliothèques ;
- règle d’exécution ;
- fonction `action`.

### 13.2 Schéma mental simplifié

```text
Métadonnées
↓
Catégories
↓
Bibliothèques Lua
↓
Règle d’exécution (hostrule/portrule/...)
↓
Fonction action()
↓
Sortie formatée dans Nmap
```

### 13.3 Exemple conceptuel

Un script HTTP simple peut dire :

- “je suis un script `safe`, `discovery`” ;
- “je m’exécute si le port ressemble à du HTTP” ;
- “j’envoie une requête GET” ;
- “je récupère le titre HTML” ;
- “j’affiche le résultat dans la sortie Nmap”.

---

## 14. Bibliothèques NSE

NSE ne se limite pas à des scripts isolés.
Il existe aussi des **bibliothèques** (`nselib`) qui facilitent le travail.

Exemples de familles de bibliothèques :

- HTTP
- SMB
- DNS
- SNMP
- SSL/TLS
- utilitaires généraux

Cela évite de réécrire à chaque fois :

- la gestion des sockets ;
- les requêtes protocolaires ;
- la sérialisation ;
- certaines fonctions de parsing.

---

## 15. NSE et performance

### 15.1 Parallélisme

NSE profite du modèle de performance de Nmap et peut exécuter des scripts efficacement en parallèle.

### 15.2 Mais attention

Lancer trop de scripts, surtout intrusifs, peut :

- rallonger fortement le scan ;
- augmenter le bruit réseau ;
- fatiguer la cible ;
- fausser les résultats ;
- déclencher des protections.

### 15.3 Bonne pratique

Procéder par couches :

1. `-sV -sC`
2. scripts ciblés selon les ports trouvés
3. scripts plus spécifiques selon hypothèse
4. scripts vuln ou intrusive seulement si justifié

---

## 16. Méthodologie post-port-scan avec Nmap

Voici une méthode simple et propre.

### Étape 1 — Découverte initiale

```bash
nmap -Pn -p- 10.10.10.10
```

But : repérer tous les ports TCP ouverts.

### Étape 2 — Détection de services sur les ports intéressants

```bash
nmap -sV -sC -p 22,80,443,445 10.10.10.10
```

But : obtenir une première couche riche d’info.

### Étape 3 — Scripts ciblés par technologie

Exemple web :

```bash
nmap --script=http-title,http-headers,http-methods -p 80,443 10.10.10.10
```

Exemple SMB :

```bash
nmap --script=smb-os-discovery,smb-enum-shares -p 445 10.10.10.10
```

### Étape 4 — Scripts de vulnérabilité ciblés

Seulement si pertinent et autorisé.

### Étape 5 — Validation manuelle

Toujours confirmer avec :

- navigateur ;
- curl ;
- openssl ;
- smbclient ;
- nc ;
- outils spécifiques.

---

## 17. Comment choisir les bons scripts

La bonne question n’est pas :

> “Quel est le meilleur script Nmap ?”

La bonne question est :

> “Quel script répond à mon hypothèse technique actuelle ?”

### 17.1 Exemple de raisonnement

Tu observes :

- 80/tcp : serveur web
- 443/tcp : HTTPS
- 445/tcp : SMB
- 3306/tcp : MySQL

Alors tu peux raisonner ainsi :

- Web → `http-title`, `http-headers`, `http-methods`, `http-enum`
- SMB → `smb-os-discovery`, `smb-enum-shares`
- MySQL → scripts `mysql-*`
- TLS → `ssl-cert`, `ssl-enum-ciphers`

### 17.2 Règle simple

**Un script doit servir une hypothèse.**
Pas juste “être lancé parce qu’il existe”.

---

## 18. Recherche et consultation des scripts

### 18.1 Aide locale

```bash
nmap --script-help http-title
```

### 18.2 Recherche dans le dossier des scripts

```bash
grep -R "categories" /usr/share/nmap/scripts/ | less
```

### 18.3 Portail NSEDoc

Le portail NSEDoc permet de consulter :

- les scripts disponibles ;
- leurs catégories ;
- leurs arguments ;
- leurs dépendances ;
- des exemples d’usage.

---

## 19. Qu’est-ce que NSEDoc ?

**NSEDoc** est le système de documentation utilisé pour les scripts et bibliothèques NSE.

Il s’agit d’une documentation embarquée dans le code source, sous forme de commentaires structurés.

### 19.1 Pourquoi c’est utile

NSEDoc permet :

- de comprendre ce que fait un script ;
- de connaître ses catégories ;
- de voir ses arguments ;
- de lire ses dépendances ;
- de produire une doc exploitable par les outils de documentation Nmap.

### 19.2 Ce qu’on documente généralement

Dans un script NSE, on documente souvent :

- le résumé ;
- la description ;
- les catégories ;
- les arguments attendus ;
- les dépendances ;
- la sortie attendue ;
- des exemples de commande.

---

## 20. Comment écrire une documentation NSE avec NSEDoc

### 20.1 Idée générale

La documentation se trouve directement dans les commentaires du script.

### 20.2 Éléments importants à documenter

Quand tu écris ou étudies un script, pose-toi ces questions :

- Que fait le script exactement ?
- Sur quel type de cible s’exécute-t-il ?
- Quels arguments sont obligatoires ?
- Quels effets peut-il avoir ?
- Quelle sortie doit-on attendre ?
- Dans quelle catégorie doit-il être rangé ?

### 20.3 Exemple conceptuel de documentation

```lua
---
-- Short description of the script.
--
-- @usage
-- nmap --script mon-script -p 80 10.10.10.10
--
-- @args monscript.option Description of the argument.
--
-- @output
-- PORT   STATE SERVICE
-- 80/tcp open  http
-- |_mon-script: useful information
--
-- @xmloutput
-- <elem key="something">value</elem>
```

Ce n’est pas un script complet, juste un modèle mental de la manière dont la documentation est structurée.

### 20.4 Bonne documentation = bon script

Un bon script NSE n’est pas seulement “fonctionnel”.
Il doit aussi être :

- compréhensible ;
- documenté ;
- maintenable ;
- classé correctement.

---

## 21. Différence entre scan classique, version detection et NSE

### Scan classique
Te dit principalement :
- ouvert / fermé / filtré.

### Version detection (`-sV`)
Tente d’identifier :
- service ;
- produit ;
- version.

### NSE
Ajoute :
- logique applicative ;
- interrogation avancée ;
- automatisation ciblée ;
- détection enrichie.

En simplifiant :

- `-p` te dit **où regarder** ;
- `-sV` te dit **ce qui tourne** ;
- `--script` t’aide à comprendre **ce que cela implique**.

---

## 22. Exemples de workflows utiles

## 22.1 Workflow web

```bash
nmap -sV -p 80,443 --script=http-title,http-headers,http-methods,http-enum 10.10.10.10
```

Ce workflow aide à :

- récupérer les titres ;
- identifier les headers ;
- repérer des méthodes HTTP risquées ;
- découvrir des chemins courants.

## 22.2 Workflow SMB

```bash
nmap -sV -p 139,445 --script=smb-os-discovery,smb-enum-shares,smb-protocols 10.10.10.10
```

Utile pour :

- identifier l’OS ;
- voir les partages ;
- relever les versions/protocoles SMB.

## 22.3 Workflow TLS

```bash
nmap -sV -p 443 --script=ssl-cert,ssl-enum-ciphers 10.10.10.10
```

Utile pour :

- inspecter le certificat ;
- analyser les suites cryptographiques.

---

## 23. Les erreurs fréquentes avec NSE

### 23.1 Tout lancer d’un coup

Mauvaise idée :

```bash
nmap --script "*" 10.10.10.10
```

Pourquoi c’est mauvais :

- bruit énorme ;
- temps considérable ;
- résultats difficiles à analyser ;
- risque accru pour la cible ;
- très mauvaise hygiène méthodologique.

### 23.2 Confondre détection et exploitation

Un script `vuln` ne prouve pas toujours à lui seul un impact exploitable.
Il indique souvent une **piste**.

### 23.3 Ignorer les catégories

Lancer un script `dos` ou `exploit` “pour voir” est une erreur classique.

### 23.4 Négliger la validation manuelle

NSE aide beaucoup, mais ne remplace pas l’analyse du pentester.

---

## 24. Conseils pratiques pour bien utiliser NSE

1. Commence par `-sC -sV`.
2. Observe les services réellement exposés.
3. Choisis des scripts ciblés.
4. Consulte `--script-help` avant de lancer un script sensible.
5. Lis la catégorie du script.
6. Utilise `--script-trace` pour comprendre un comportement bizarre.
7. Conserve les sorties dans des fichiers.
8. Corrèle toujours avec d’autres outils.

---

## 25. Exemples de sauvegarde de résultats

```bash
nmap -sV -sC -oA scan_initial 10.10.10.10
```

Cela produit généralement :

- `scan_initial.nmap`
- `scan_initial.gnmap`
- `scan_initial.xml`

Pourquoi c’est utile :

- relire les résultats ;
- parser en automatique ;
- documenter un audit ;
- refaire l’analyse sans rescanner tout de suite.

---

## 26. Écriture de scripts Bash dans ton projet Holberton

Tu as aussi des contraintes précises de rendu.
Voici comment les comprendre.

### 26.1 Contraintes rappelées

Tes scripts doivent :

- être exécutables ;
- faire exactement **2 lignes** ;
- commencer par :

```bash
#!/bin/bash
```

- utiliser `$1` pour la cible / plage IP ;
- ne pas entourer `$1` de guillemets selon la consigne ;
- ne pas utiliser :
  - backticks
  - `&&`
  - `||`
  - `;`
  - `echo`
- utiliser les **numéros de ports** et non les noms de service.

### 26.2 Pourquoi ces contraintes existent

Elles forcent à :

- écrire des commandes simples ;
- éviter des constructions shell ambiguës ;
- respecter un style strict ;
- être évalué automatiquement facilement.

### 26.3 Exemple de script valide dans l’esprit du projet

```bash
#!/bin/bash
nmap -sV -sC -p 80,443 $1
```

### 26.4 Exemple avec script NSE ciblé

```bash
#!/bin/bash
nmap --script=http-title,http-headers -p 80,443 $1
```

### 26.5 Exemple avec script d’énumération SMB

```bash
#!/bin/bash
nmap --script=smb-os-discovery,smb-enum-shares -p 139,445 $1
```

### 26.6 Erreurs à éviter

#### Mauvais

```bash
#!/bin/bash
nmap -sV -sC -p 80,443 "$1"
```

Ici, même si en shell classique ce n’est pas absurde, **ta consigne dit explicitement de ne pas entourer `$1`**.

#### Mauvais

```bash
#!/bin/bash
echo test
```

Interdit par la consigne.

#### Mauvais

```bash
#!/bin/bash
nmap -sV -sC -p http,https $1
```

Il faut utiliser **80,443** et non `http,https`.

---

## 27. Exemples de petits scripts compatibles avec les contraintes

## 27.1 Scan par défaut enrichi

```bash
#!/bin/bash
nmap -sV -sC $1
```

## 27.2 Scan web post-port-scan

```bash
#!/bin/bash
nmap --script=http-title,http-methods,http-headers -p 80,443 $1
```

## 27.3 Scan TLS

```bash
#!/bin/bash
nmap --script=ssl-cert,ssl-enum-ciphers -p 443 $1
```

## 27.4 Scan DNS

```bash
#!/bin/bash
nmap --script=dns-zone-transfer -p 53 $1
```

## 27.5 Scan SMB

```bash
#!/bin/bash
nmap --script=smb-os-discovery,smb-enum-shares -p 139,445 $1
```

---

## 28. Comment réfléchir exercice par exercice

Si un exercice te demande un script NSE, voici la logique que tu dois appliquer :

### Question 1
Quel **objectif** cherche l’exercice ?

- découverte ?
- version ?
- vuln ?
- web ?
- smb ?
- tls ?

### Question 2
Quels **ports** sont concernés ?

Toujours mettre les **numéros**.

### Question 3
Quel est le **script NSE** pertinent ?

Chercher le nom exact.

### Question 4
La commande respecte-t-elle les contraintes shell ?

- 2 lignes
- pas de `echo`
- pas de `;`
- pas de `&&`
- pas de `||`
- pas de backticks
- `$1` non quoté

---

## 29. Comment lire le nom d’un script NSE

Les noms sont souvent très parlants.

Exemples :

- `http-title` → récupère le titre HTTP
- `http-methods` → récupère les méthodes HTTP
- `ftp-anon` → teste l’accès anonyme FTP
- `dns-zone-transfer` → teste le transfert de zone DNS
- `ssl-enum-ciphers` → liste les suites cryptographiques SSL/TLS
- `smb-enum-shares` → énumère les partages SMB

Apprends à lire les noms comme des mini-descriptions.

---

## 30. NSE pour la documentation d’audit

NSE est aussi très utile pour documenter ton travail.

### Exemple
Tu peux écrire dans un rapport :

- ports ouverts détectés ;
- service et version identifiés ;
- scripts NSE utilisés ;
- résultats observés ;
- validation manuelle réalisée ensuite.

### Bon format de note

```text
Cible : 10.10.10.10
Ports : 80,443
Commande : nmap -sV --script=http-title,http-headers,http-methods -p 80,443 10.10.10.10
Résultats : serveur nginx, titre “Admin Portal”, méthode PUT non observée, header Server exposé
Conclusion : surface web présente, pas d’indice immédiat de méthode dangereuse, énumération web complémentaire recommandée
```

---

## 31. Ce que tu dois savoir expliquer à l’oral

À la fin de ce module, tu dois pouvoir expliquer simplement :

### Qu’est-ce que NSE ?
C’est le moteur de scripts de Nmap, écrit autour de scripts Lua, qui permet d’automatiser l’énumération, la collecte d’informations et certains tests de sécurité.

### Pourquoi c’est important ?
Parce qu’un scan de ports brut ne suffit pas. NSE permet de transformer des ports ouverts en informations réellement utiles pour un audit.

### Comment ça marche ?
Nmap charge des scripts, décide où ils s’exécutent selon des règles (`prerule`, `hostrule`, `portrule`, `postrule`), puis exécute leurs actions et affiche les résultats.

### Quelles sont les catégories ?
`auth`, `broadcast`, `brute`, `default`, `discovery`, `dos`, `exploit`, `external`, `fuzzer`, `info`, `intrusive`, `malware`, `safe`, `version`, `vuln`.

### Comment les lancer ?
Avec `--script`, éventuellement `--script-args`, et très souvent en combinaison avec `-sV`.

### Que peut-on faire avec ?
Énumération web, SMB, DNS, TLS, détection de mauvaises configurations, tests de comptes par défaut, collecte avancée, et parfois vérification de vulnérabilités.

### Qu’est-ce que NSEDoc ?
Le système de documentation des scripts NSE, écrit dans le code source sous forme de commentaires structurés.

---

## 32. Résumé ultra synthétique

- Nmap ne sert pas qu’à lister des ports.
- Le post port scan consiste à enrichir l’information après découverte.
- NSE est le moteur de scripts de Nmap.
- Les scripts NSE sont écrits en Lua.
- Ils sont classés en catégories.
- Ils s’exécutent selon des règles (`prerule`, `hostrule`, `portrule`, `postrule`).
- `-sC` lance les scripts par défaut.
- `-sV` complète bien NSE.
- `--script-help` est ton ami.
- `--script-args` sert à passer des paramètres.
- `safe`, `default`, `discovery`, `info` sont de bons points de départ.
- `dos`, `exploit`, `fuzzer` demandent une extrême prudence.
- NSEDoc documente les scripts et leurs arguments.
- Dans ton projet, tes scripts shell doivent respecter strictement les contraintes imposées.

---

## 33. Mini antisèche commandes utiles

```bash
nmap -sC 10.10.10.10
nmap -sV -sC 10.10.10.10
nmap --script=http-title -p 80 10.10.10.10
nmap --script=http-title,http-headers,http-methods -p 80,443 10.10.10.10
nmap --script=smb-os-discovery,smb-enum-shares -p 139,445 10.10.10.10
nmap --script=ssl-cert,ssl-enum-ciphers -p 443 10.10.10.10
nmap --script-help http-title
nmap --script-updatedb
```

---

## 34. Références conseillées

Sources officielles et très utiles :

- Documentation officielle Nmap
- Guide de référence Nmap
- Chapitre NSE de *Nmap Network Scanning*
- Portail NSEDoc
- Documentation NSEDoc
- Liste des catégories NSE
- Liste des scripts NSE

Liens :

- https://nmap.org/
- https://nmap.org/book/man.html
- https://nmap.org/book/man-nse.html
- https://nmap.org/book/nse.html
- https://nmap.org/book/nse-usage.html
- https://nmap.org/book/nsedoc.html
- https://nmap.org/nsedoc/
- https://nmap.org/nsedoc/categories/
- https://nmap.org/nsedoc/scripts/

---

## 35. Conclusion

Le vrai intérêt de Nmap commence souvent **après** la découverte des ports.
Le **Nmap Scripting Engine** transforme Nmap en plateforme d’énumération avancée.

Si tu retiens une seule idée, retiens celle-ci :

> Le scan de ports te montre la surface. NSE t’aide à comprendre ce qu’elle signifie.

Et en pratique, la bonne méthode est toujours :

- scanner proprement ;
- enrichir avec des scripts ciblés ;
- lire les résultats avec esprit critique ;
- valider manuellement.

