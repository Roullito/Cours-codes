# Cours ultra complet — File Inclusion (LFI / RFI) en application web

> **Contexte du module**  
> Ce module porte sur les vulnérabilités de **File Inclusion** dans un contexte Web Security.  
> Même si l’acronyme **IDOR** apparaît parfois à côté dans certains parcours OWASP ou modules Web, **IDOR et File Inclusion sont deux familles de vulnérabilités différentes** :  
> - **IDOR** = problème d’autorisation / contrôle d’accès sur des objets  
> - **File Inclusion** = problème d’utilisation dangereuse d’un chemin ou d’une ressource fichier fournie par l’utilisateur

---

## Sommaire

1. [Objectifs pédagogiques](#objectifs-pédagogiques)  
2. [Pourquoi cette vulnérabilité existe](#pourquoi-cette-vulnérabilité-existe)  
3. [Définition générale de la File Inclusion](#définition-générale-de-la-file-inclusion)  
4. [LFI vs RFI vs Path Traversal vs IDOR](#lfi-vs-rfi-vs-path-traversal-vs-idor)  
5. [Fonctionnement de `include()` / `require()` en PHP](#fonctionnement-de-include--require-en-php)  
6. [Anatomie d’un code vulnérable](#anatomie-dun-code-vulnérable)  
7. [Que signifie `../../` ?](#que-signifie-)  
8. [Local File Inclusion (LFI)](#local-file-inclusion-lfi)  
9. [Remote File Inclusion (RFI)](#remote-file-inclusion-rfi)  
10. [Mécanismes d’exploitation d’une File Inclusion](#mécanismes-dexploitation-dune-file-inclusion)  
11. [Comment une LFI peut mener à une RCE](#comment-une-lfi-peut-mener-à-une-rce)  
12. [Impacts possibles sur un système](#impacts-possibles-sur-un-système)  
13. [Détection en boîte noire et boîte blanche](#détection-en-boîte-noire-et-boîte-blanche)  
14. [Méthodologie de test sur Cyber - WebSec 0x07](#méthodologie-de-test-sur-cyber---websec-0x07)  
15. [Exemples pédagogiques de code vulnérable et corrigé](#exemples-pédagogiques-de-code-vulnérable-et-corrigé)  
16. [Stratégies de prévention et de mitigation](#stratégies-de-prévention-et-de-mitigation)  
17. [Checklist développeur / auditeur](#checklist-développeur--auditeur)  
18. [Pièges fréquents et fausses bonnes idées](#pièges-fréquents-et-fausses-bonnes-idées)  
19. [Questions de révision](#questions-de-révision)  
20. [Résumé final](#résumé-final)  
21. [Références et ressources conseillées](#références-et-ressources-conseillées)

---

## Objectifs pédagogiques

À la fin de ce cours, tu dois être capable d’expliquer clairement :

- ce qu’est une **LFI**
- ce qu’est une **RFI**
- le rôle de `../../` dans une attaque
- le lien entre **path traversal** et **file inclusion**
- comment une **LFI peut évoluer vers une RCE**
- les mécanismes d’exploitation les plus courants
- les impacts métiers et techniques d’une FI réussie
- comment détecter une FI en **black box** et en **white box**
- comment mettre en place une prévention robuste côté code, configuration et architecture

---

## Pourquoi cette vulnérabilité existe

Les applications web ont souvent besoin de charger des ressources dynamiques :

- un template
- une vue
- une page de langue
- un fragment HTML/PHP
- un fichier de configuration
- un thème
- un document à télécharger
- un avatar, une archive, un rapport

Le problème commence lorsque **le chemin du fichier à charger dépend directement d’une entrée contrôlée par l’utilisateur**.

Autrement dit, si l’application fait quelque chose comme :

```php
include($_GET['page']);
```

elle délègue à l’utilisateur le choix de ce qui sera ouvert, lu ou exécuté.

C’est exactement là que naît la vulnérabilité.

---

## Définition générale de la File Inclusion

Une **File Inclusion vulnerability** apparaît lorsqu’une application :

1. accepte une donnée utilisateur servant à désigner un fichier ou une ressource
2. transmet cette donnée à un mécanisme d’inclusion ou de lecture
3. sans validation stricte, sans allowlist réelle, ou sans confinement efficace

Selon le contexte, cela peut permettre à un attaquant :

- de lire des fichiers locaux sensibles
- d’inclure un fichier local inattendu
- d’inclure une ressource distante
- d’obtenir des informations internes
- d’exécuter du code
- de provoquer un déni de service

---

## LFI vs RFI vs Path Traversal vs IDOR

### LFI vs RFI

#### LFI — Local File Inclusion

L’attaquant force l’application à inclure **un fichier déjà présent sur le serveur**.

Exemples de cibles possibles :

- fichiers système
- fichiers de configuration
- code source
- logs
- sessions
- fichiers temporaires
- fichiers uploadés

#### RFI — Remote File Inclusion

L’attaquant force l’application à inclure **un fichier situé sur une ressource distante**, généralement via une URL si la technologie ou la configuration l’autorise.

L’impact peut être plus grave encore, car l’application peut aller chercher un contenu externe et l’interpréter localement.

---

### Lien avec le Path Traversal

Le **path traversal** consiste à manipuler un chemin pour sortir du répertoire prévu.

Exemple typique :

```text
../../../../etc/passwd
```

La File Inclusion utilise souvent le path traversal comme **technique de navigation** dans l’arborescence.

En pratique :

- **Path Traversal** = technique de déplacement dans le système de fichiers
- **LFI/RFI** = vulnérabilité d’inclusion/lecture qui peut exploiter ce déplacement

Le path traversal n’est donc pas toujours une File Inclusion, mais il est **très souvent lié** à elle.

---

### Différence avec IDOR

Un **IDOR** permet d’accéder à une ressource parce que l’application vérifie mal l’autorisation sur un identifiant.

Exemple :

```text
GET /invoice?id=42
```

Si l’utilisateur 43 peut consulter la facture 42, c’est un IDOR.

Une **File Inclusion**, elle, concerne la manière dont l’application ouvre ou inclut un fichier.

Donc :

- **IDOR** = problème d’autorisation
- **File Inclusion** = problème de gestion de chemin / chargement de ressource

---

## Fonctionnement de `include()` / `require()` en PHP

PHP a historiquement beaucoup exposé ce type de faille, car il propose des mécanismes d’inclusion simples et puissants :

- `include`
- `require`
- `include_once`
- `require_once`

### Différence rapide

- `include` : tente d’inclure un fichier ; en cas d’échec, produit un warning
- `require` : nécessite le fichier ; en cas d’échec, l’exécution s’arrête généralement
- les variantes `_once` évitent de réinclure plusieurs fois le même fichier

### Pourquoi c’est dangereux

Si le développeur écrit :

```php
include($_GET['file']);
```

alors :

- l’utilisateur contrôle le chemin
- l’application tente d’ouvrir ce que l’utilisateur désigne
- selon le contenu et le contexte, cela peut aboutir à :
  - lecture d’un fichier
  - fuite d’informations
  - exécution de code
  - chargement d’une ressource distante

### Point fondamental

En PHP, **inclure un fichier n’est pas juste “lire du texte”**.  
Selon le contenu et l’extension, le moteur peut **évaluer du code PHP**.

C’est ce qui rend la faille potentiellement très grave.

---

## Anatomie d’un code vulnérable

### Exemple minimal

```php
<?php
$page = $_GET['page'];
include($page);
?>
```

Pourquoi c’est vulnérable ?

- l’entrée utilisateur est utilisée telle quelle
- aucune validation
- aucun mapping interne
- aucun confinement dans un répertoire fixe
- aucune canonicalisation de chemin

### Variante très fréquente

```php
<?php
$file = $_GET['file'];
include($file . ".php");
?>
```

Certains développeurs pensent qu’ajouter une extension fixe suffit.

En réalité, cela **réduit parfois l’exploitation**, mais ne la supprime pas forcément :

- contournements historiques par null byte
- contournements historiques par troncature de chemin
- usage de wrappers
- inclusion de fichiers internes ayant déjà du code interprétable
- scénarios d’upload ou de logs

---

## Que signifie `../../` ?

`../` signifie :

> “remonter d’un répertoire parent”

Donc :

- `../` = remonter d’un dossier
- `../../` = remonter de deux dossiers
- `../../../` = remonter de trois dossiers

### Exemple simple

Supposons que l’application se trouve dans :

```text
/var/www/html/app/pages/
```

et qu’elle doit normalement inclure :

```text
/var/www/html/app/pages/home.php
```

Si l’utilisateur contrôle le paramètre, il peut essayer :

```text
../../../../etc/passwd
```

Ce qui revient à dire :

1. sortir de `pages/`
2. sortir de `app/`
3. sortir de `html/`
4. sortir de `www/`
5. viser ensuite `/etc/passwd`

### Pourquoi c’est si courant

Parce que beaucoup d’applications :

- concatènent un morceau de chemin
- présument que l’utilisateur restera dans un sous-répertoire
- oublient qu’un chemin relatif peut remonter dans l’arborescence

### Encodages et variantes

En audit, on rencontre aussi :

- encodage URL
- double encodage
- séparateurs différents selon l’OS
- chemins absolus
- variantes Windows (`..\`)
- normalisations inattendues côté framework, proxy ou serveur

L’idée à retenir n’est pas “mémoriser toutes les payloads”, mais comprendre la logique :

> si l’application te laisse influencer un chemin, tu essaies d’en sortir.

---

## Local File Inclusion (LFI)

### Définition

La **Local File Inclusion** survient lorsqu’un attaquant parvient à faire inclure à l’application un **fichier local** présent sur la machine cible.

### Scénario typique

```php
<?php
include($_GET['page']);
?>
```

URL attendue :

```text
/page.php?page=about.php
```

URL malveillante :

```text
/page.php?page=../../../../etc/passwd
```

### Ce que l’attaquant cherche souvent

- `/etc/passwd`
- fichiers de configuration
- sources PHP
- variables d’environnement
- logs Apache/Nginx/PHP
- fichiers de session
- fichiers temporaires
- archives ou uploads présents sur le serveur

### Pourquoi `/etc/passwd` revient tout le temps

Ce n’est pas “magique” : c’est un **indicateur pratique**.

- il existe sur beaucoup de systèmes Unix/Linux
- il est souvent lisible
- son contenu est reconnaissable
- il permet de confirmer rapidement une lecture arbitraire

Donc en pentest, ce fichier sert souvent de **preuve de concept**.

---

## Remote File Inclusion (RFI)

### Définition

La **Remote File Inclusion** survient lorsqu’une application accepte un chemin distant et va récupérer un contenu externe pour l’inclure.

Exemple pédagogique :

```php
<?php
include($_GET['file']);
?>
```

avec un paramètre du style :

```text
?file=http://exemple-distant/fichier
```

### Pourquoi c’est dangereux

Si l’environnement autorise ce comportement, le serveur peut :

- contacter une ressource contrôlée par l’attaquant
- récupérer du contenu externe
- l’interpréter localement
- exécuter un code non prévu

### Important

La RFI dépend davantage :

- du langage
- des wrappers disponibles
- de la configuration
- des protections activées
- du type de fonction utilisée

En PHP, le paramètre `allow_url_include` a historiquement eu un rôle important.

---

## Mécanismes d’exploitation d’une File Inclusion

Une FI peut être exploitée de plusieurs façons. Il faut comprendre le **mécanisme**, pas seulement les payloads.

### 1. Inclusion directe d’un fichier sensible

L’attaquant fournit un chemin vers un fichier local sensible.

Objectif :

- lire des informations
- comprendre l’environnement
- récupérer des secrets
- préparer une chaîne d’attaque

### 2. Traversée d’arborescence

Le chemin fourni contient des remontées parent :

```text
../
../../
../../../
```

Objectif :

- sortir du répertoire autorisé
- atteindre un répertoire système ou applicatif

### 3. Inclusion d’un fichier local contenant déjà des données contrôlables

L’idée n’est plus seulement de lire un fichier sensible, mais de faire inclure un fichier dans lequel l’attaquant a pu injecter quelque chose auparavant.

Exemples conceptuels :

- logs d’accès
- sessions
- fichiers temporaires
- contenu uploadé

### 4. Inclusion d’une ressource distante

Si la techno et la config le permettent, l’application peut charger une ressource externe.

### 5. Abuse de wrappers

Certains wrappers modifient la façon dont la ressource est lue ou interprétée.

Exemples connus dans l’écosystème PHP :

- `php://filter`
- `data://`
- `zip://`
- `expect://`
- plus largement d’autres wrappers ou wrappers personnalisés selon l’environnement

### 6. Contournement de suffixe ajouté par l’application

Si le code ajoute `.php`, certains contournements historiques ou contextuels peuvent être envisagés :

- null byte (historique)
- troncature de chemin (historique)
- wrappers
- variation de logique applicative
- inclusion d’un fichier qui reste interprétable malgré le suffixe
- combinaisons avec upload / archive / log / session

---

## Comment une LFI peut mener à une RCE

C’est un point central du module.

### Principe général

Une **LFI** devient une **RCE** lorsque l’attaquant arrive à faire inclure un **fichier local contenant du code qu’il contrôle** et que ce code est ensuite interprété par le serveur.

Autrement dit :

1. il y a une LFI
2. l’attaquant réussit à déposer ou injecter du contenu contrôlé dans un fichier local
3. ce fichier est ensuite inclus
4. le contenu est exécuté

### Chaîne logique

```text
Contrôle sur un chemin
→ inclusion d’un fichier local
→ contrôle partiel sur le contenu de ce fichier
→ interprétation serveur
→ exécution de code
```

### Cas conceptuels fréquents

#### 1. Log poisoning

L’attaquant injecte un contenu dans un log applicatif ou serveur, puis exploite la LFI pour inclure ce log.

#### 2. Session poisoning

Si des données contrôlées par l’utilisateur se retrouvent dans un fichier de session, la LFI peut parfois viser ce fichier.

#### 3. Upload + inclusion

L’attaquant téléverse un fichier ou une archive contenant du contenu interprétable, puis provoque son inclusion.

#### 4. Fichiers temporaires / caches / exports

Certaines applications créent des fichiers intermédiaires. Si l’utilisateur peut influencer leur contenu et si l’emplacement est accessible via LFI, le risque augmente.

#### 5. Wrappers

Certains wrappers permettent de transformer une LFI “lecture seule” en scénario plus dangereux selon la configuration.

### Point clé d’examen

> Une LFI ne donne pas automatiquement une RCE.  
> Elle donne souvent d’abord une **lecture arbitraire**.  
> La RCE arrive lorsqu’on combine la LFI avec une **source de contenu contrôlable** ou une **fonctionnalité adjacente**.

---

## Les wrappers importants à connaître en PHP

> Cette section est pédagogique. Le but est de comprendre **pourquoi** les wrappers augmentent le risque.

### `php://filter`

Très important en audit.

Idée :

- lire un fichier
- lui appliquer un filtre
- éviter l’exécution directe
- obtenir parfois le contenu source encodé

Usage pédagogique :

```text
php://filter/convert.base64-encode/resource=index.php
```

Pourquoi c’est utile à l’attaquant ?

- permet parfois de récupérer le **source code** au lieu d’exécuter le script
- donc :
  - credentials
  - logique métier
  - routes cachées
  - clés API
  - noms de fichiers
  - requêtes SQL
  - autres failles

### `data://`

Permet de fournir une ressource inline.

Risque :

- si la configuration autorise l’inclusion d’URL
- l’application peut interpréter un contenu injecté directement

### `zip://`

Intéressant lorsque l’application permet l’upload d’archives ou d’un fichier renommé contenant une archive.

Risque :

- l’archive contient un fichier interprétable
- la LFI vise ce contenu via le wrapper

### `expect://`

Particulièrement dangereux lorsqu’il est disponible.

Il est lié à l’interaction avec des processus.  
Il n’est pas activé par défaut, mais s’il est présent, le niveau de risque monte fortement.

### À retenir

Les wrappers sont importants parce qu’ils changent la nature de l’attaque :

- lecture de source
- exécution
- contournement de logique
- pivot vers RCE

---

## Bypasses historiques et subtilités classiques

### Null byte injection

Historique très connu.

L’idée :

- injecter un caractère nul `%00`
- tronquer la chaîne côté traitement natif
- ignorer le suffixe ajouté après

Exemple conceptuel :

```php
include($_GET['file'] . '.php');
```

Un attaquant a historiquement pu essayer quelque chose comme :

```text
/etc/passwd%00
```

### Important à comprendre

C’est surtout un **concept historique** à connaître pour la culture offensive et la compréhension des vieux codes, pas une garantie universelle sur des environnements modernes.

---

### Path and dot truncation

Autre subtilité historique : les très longs chemins pouvaient provoquer une troncature et faire “sortir” l’extension ajoutée de la zone effectivement traitée.

Même logique que pour le null byte :

- ce n’est pas un bouton magique
- c’est un ancien contournement à connaître
- il rappelle qu’un simple suffixe ajouté par le développeur n’est pas une défense solide

---

## Impacts possibles sur un système

Une attaque FI réussie peut entraîner :

### 1. Divulgation d’informations sensibles

- fichiers de configuration
- identifiants BD
- secrets applicatifs
- clés API
- tokens
- chemins internes
- variables d’environnement
- versions de logiciels
- structure du projet

### 2. Compromission partielle ou totale de l’application

- accès à la logique métier
- découverte de routes internes
- préparation d’autres attaques
- contournement de protections

### 3. Exécution de code

Dans le pire des cas :

- exécution de commandes ou de code
- shell web
- pivot latéral
- persistance

### 4. Déni de service

Une inclusion mal gérée peut aussi :

- boucler
- provoquer des erreurs fatales
- charger de gros contenus
- faire planter un composant

### 5. Atteinte à la confidentialité, intégrité, disponibilité

- **Confidentialité** : fuite de données
- **Intégrité** : modification possible après pivot ou RCE
- **Disponibilité** : crash, surcharge, blocage

### 6. Impact métier

- exposition de données clients
- violation de conformité
- perte de confiance
- incident de sécurité majeur
- escalade vers compromission complète

---

## Détection en boîte noire et boîte blanche

## Détection en boîte noire

### Ce qu’on cherche

On repère des paramètres comme :

- `file`
- `page`
- `view`
- `template`
- `lang`
- `module`
- `inc`
- `path`
- `doc`
- `download`

### Indices typiques

- la page change selon un paramètre
- erreurs “failed to open stream”
- erreurs “include/require”
- messages de chemin absolu
- différences de comportement selon la valeur du paramètre
- inclusion visible de contenu inattendu

### Démarche

1. Identifier un paramètre lié à un fichier ou une vue  
2. Envoyer une valeur attendue  
3. Envoyer une valeur inexistante  
4. Observer les erreurs  
5. Tenter une remontée d’arborescence  
6. Tester lecture, pas seulement exécution  
7. Chercher si un suffixe est ajouté  
8. Vérifier si des wrappers sont interprétés  
9. Chercher les fonctionnalités annexes permettant une chaîne vers RCE

### Outils utiles

- navigateur + DevTools
- Burp Suite
- OWASP ZAP
- wordlists de paramètres
- fuzzing ciblé
- logs applicatifs si disponibles

---

## Détection en boîte blanche

Quand tu as le code source, cherche :

- `include`
- `require`
- `include_once`
- `require_once`
- `readfile`
- `file_get_contents`
- `fopen`
- loaders de templates
- import dynamique de fichiers
- logique basée sur `$_GET`, `$_POST`, `$_REQUEST`, cookies, headers, session

### Questions à poser

- l’entrée utilisateur est-elle utilisée directement ?
- y a-t-il une allowlist réelle ?
- le chemin est-il canonicalisé ?
- le répertoire cible est-il forcé côté serveur ?
- les chemins absolus sont-ils bloqués ?
- la traversée `..` est-elle rejetée ?
- y a-t-il une validation métier ou juste un blacklisting ?
- la config permet-elle les wrappers distants ?
- l’application stocke-t-elle des données contrôlables dans des fichiers locaux ?

---

## Méthodologie de test sur Cyber - WebSec 0x07

> À utiliser uniquement dans un cadre autorisé : lab, machine pédagogique, environnement Cyber dédié.

### 1. Cartographier la surface

Cherche tous les endpoints ou paramètres qui semblent choisir :

- une page
- une langue
- un template
- un document
- une image
- un module
- un export
- un fichier de configuration
- un aperçu de ressource

### 2. Tester des valeurs légitimes

Exemple :

- valeur valide
- valeur vide
- valeur inexistante
- valeur surprenante mais non malveillante

But : comprendre la logique de l’application.

### 3. Observer les messages d’erreur

Les erreurs de type :

- fichier introuvable
- chemin absolu
- extension ajoutée
- warning PHP
- stack trace

sont très précieuses.

### 4. Tester une traversée simple

Commence toujours **petit** et **méthodique** :

- un niveau de remontée
- puis deux
- puis plusieurs
- comparer les réponses
- noter les statuts HTTP
- noter les différences de taille et de contenu

### 5. Déterminer si on est en lecture seule ou plus

Questions :

- l’application affiche-t-elle le contenu ?
- exécute-t-elle le contenu ?
- ajoute-t-elle une extension ?
- filtre-t-elle certains caractères ?
- accepte-t-elle un schéma de wrapper ?
- y a-t-il une fonctionnalité d’upload, log ou session exploitable en chaîne ?

### 6. Documenter proprement

Pour chaque test :

- endpoint
- paramètre
- valeur envoyée
- comportement attendu
- comportement obtenu
- impact
- hypothèse technique
- preuve de concept contrôlée
- recommandation

---

## Exemples pédagogiques de code vulnérable et corrigé

## Exemple 1 — vulnérable

```php
<?php
include($_GET['page']);
?>
```

### Pourquoi c’est mauvais

- contrôle total par l’utilisateur
- aucun répertoire imposé
- aucun mapping
- aucun contrôle d’autorisation
- aucun contrôle sur le type de ressource

---

## Exemple 2 — vulnérable malgré suffixe

```php
<?php
$page = $_GET['page'];
include($page . ".php");
?>
```

### Pourquoi ce n’est pas suffisant

- historique des contournements
- wrappers
- logique métier contournable
- inclusion de fichiers inattendus
- dépendance à la configuration

---

## Exemple 3 — approche plus saine avec mapping interne

```php
<?php

$pages = [
    'home' => __DIR__ . '/pages/home.php',
    'about' => __DIR__ . '/pages/about.php',
    'contact' => __DIR__ . '/pages/contact.php',
];

$key = $_GET['page'] ?? 'home';

if (!array_key_exists($key, $pages)) {
    http_response_code(404);
    exit('Page not found');
}

require $pages[$key];
```

### Pourquoi c’est mieux

- l’utilisateur ne contrôle pas un chemin
- l’utilisateur ne choisit qu’une **clé logique**
- le serveur décide du fichier réel
- surface d’attaque réduite drastiquement

---

## Exemple 4 — vérification par canonicalisation

```php
<?php

$baseDir = realpath(__DIR__ . '/pages');
$userInput = $_GET['page'] ?? 'home.php';

$target = realpath($baseDir . DIRECTORY_SEPARATOR . $userInput);

if ($target === false || strncmp($target, $baseDir, strlen($baseDir)) !== 0) {
    http_response_code(400);
    exit('Invalid path');
}

require $target;
```

### Pourquoi cette approche aide

- `realpath()` normalise le chemin réel
- on vérifie que la cible finale reste dans le répertoire autorisé
- cela réduit les risques de traversal

### Attention

Ce pattern est plus sûr qu’un `include($_GET['page'])`, mais il faut encore réfléchir à :

- extension attendue
- droits systèmes
- exposition d’erreurs
- contenus réellement inclus
- symlinks éventuels selon le contexte
- logique métier

---

## Stratégies de prévention et de mitigation

La vraie sécurité repose sur plusieurs couches.

## 1. Ne jamais inclure directement une entrée utilisateur

C’est la règle d’or.

Évite absolument :

```php
include($_GET['page']);
require($_POST['template']);
file_get_contents($_REQUEST['path']);
```

---

## 2. Utiliser une allowlist stricte

Au lieu de laisser passer un chemin, laisse passer un **identifiant logique**.

Exemple :

- `home`
- `about`
- `faq`

Puis fais un mapping interne côté serveur.

---

## 3. Imposer le répertoire serveur

Le code doit construire lui-même le chemin :

- base fixe
- nom logique contrôlé
- extension fixe si nécessaire
- vérification du chemin final

---

## 4. Canonicaliser puis vérifier

Utilise des mécanismes comme :

- `realpath()`
- comparaison stricte avec un répertoire de base
- refus des chemins qui sortent du périmètre autorisé

---

## 5. Refuser explicitement les chemins absolus et les séquences interdites

À minima, selon le contexte :

- `..`
- séparateurs non attendus
- schémas `://`
- wrappers
- caractères nuls
- noms de fichiers non autorisés

### Important

Le **blacklisting seul** n’est pas une stratégie suffisante.  
Le bon modèle reste :

> **allowlist + mapping interne + confinement de chemin**

---

## 6. Désactiver les inclusions distantes si possible

Côté PHP, il faut connaître les options :

- `allow_url_fopen`
- `allow_url_include`

Même si la configuration aide, ce n’est **jamais** une excuse pour écrire du code dangereux.

La configuration est une **défense en profondeur**, pas le remède principal.

---

## 7. Utiliser `open_basedir` comme filet supplémentaire

`open_basedir` peut limiter les accès fichiers de PHP à certains répertoires.

### Attention

Ce n’est **pas une protection complète**.  
C’est utile comme **barrière additionnelle**, mais pas comme solution unique.

---

## 8. Stocker les secrets hors du web root

Même si une FI existe, il faut rendre l’accès aux secrets plus difficile.

À éviter dans le web root :

- `.env`
- configs sensibles
- backups
- dumps SQL
- clés privées
- exports internes

---

## 9. Sécuriser les uploads

Beaucoup de chaînes LFI → RCE passent par l’upload.

Il faut :

- contrôler type réel et extension
- renommer côté serveur
- stocker hors zone exécutable si possible
- empêcher l’interprétation serveur
- séparer le stockage du code applicatif
- analyser les formats autorisés
- éviter qu’un upload serve ensuite d’entrée à une inclusion

---

## 10. Réduire les permissions système

Principe du moindre privilège :

- utilisateur système dédié
- droits minimaux
- accès en lecture limité
- pas de permissions inutiles sur les logs, sessions, configs et dossiers d’upload

Même si une LFI existe, le périmètre lisible doit rester limité.

---

## 11. Gérer proprement les erreurs

Ne jamais exposer en production :

- stack traces
- chemins absolus
- messages internes d’inclusion
- structure du projet

Une erreur verbeuse aide énormément l’attaquant.

---

## 12. Segmenter les responsabilités

Bonne architecture :

- vues connues
- templates maîtrisés
- routeur applicatif clair
- aucun choix de chemin brut par l’utilisateur

Autrement dit :  
**l’utilisateur choisit une action métier, jamais un fichier.**

---

## 13. Journaliser et monitorer

Détecter :

- séquences `../`
- tentatives sur paramètres de type `file`, `page`, `lang`
- schémas `php://`, `data://`, `zip://`
- erreurs répétées d’inclusion
- forte variation sur un même paramètre

C’est utile en détection d’attaque et en investigation.

---

## Checklist développeur / auditeur

## Checklist développeur

- [ ] Aucun `include/require/readfile/file_get_contents` n’utilise directement une entrée utilisateur  
- [ ] Les pages sont sélectionnées via une clé logique et non un chemin  
- [ ] Une allowlist stricte est en place  
- [ ] Les chemins sont canonicalisés et confinés  
- [ ] Les chemins absolus et traversals sont rejetés  
- [ ] Les erreurs détaillées ne sont pas exposées  
- [ ] Les secrets sont hors web root  
- [ ] Les uploads sont isolés et non exécutables  
- [ ] Les permissions système sont minimales  
- [ ] La config PHP réduit l’exposition (`allow_url_include`, etc.)  
- [ ] `open_basedir` est utilisé si pertinent, comme défense complémentaire

## Checklist auditeur

- [ ] Identifier tous les paramètres liés à des fichiers, vues ou langues  
- [ ] Tester valeurs valides / invalides / vides  
- [ ] Observer les erreurs et comportements différents  
- [ ] Tester traversée relative et chemins absolus  
- [ ] Vérifier si un suffixe est ajouté  
- [ ] Tester lecture de contenu source  
- [ ] Vérifier la présence de wrappers interprétés  
- [ ] Chercher une chaîne avec upload, logs, sessions, archives  
- [ ] Évaluer l’impact réel : lecture seule, disclosure, RCE potentielle  
- [ ] Documenter précisément reproduction, impact et remédiation

---

## Pièges fréquents et fausses bonnes idées

### “J’ajoute `.php`, donc c’est sécurisé”
Faux.

### “Je retire `../` avec un replace”
Insuffisant.

### “Je blacklist quelques caractères”
Fragile.

### “J’ai désactivé une option PHP, donc le problème est réglé”
Non.  
Le code reste vulnérable si l’utilisateur contrôle toujours le chemin.

### “LFI = toujours RCE”
Faux.  
Souvent la première étape est la **lecture**. La RCE dépend d’une chaîne supplémentaire.

### “Seul PHP est concerné”
Faux.  
PHP est emblématique, mais d’autres technologies peuvent avoir des logiques similaires d’inclusion ou de chargement dynamique.

### “Si ça marche localement, c’est bon”
Non.  
Les différences d’OS, de serveur, de proxy, de normalisation et de configuration changent beaucoup la réalité de l’attaque.

---

## Questions de révision

### 1. Qu’est-ce qu’une LFI ?
Une vulnérabilité permettant d’inclure un fichier **local** présent sur le serveur à cause d’un chemin contrôlé par l’utilisateur.

### 2. Qu’est-ce qu’une RFI ?
Une vulnérabilité permettant d’inclure une ressource **distante**, souvent via URL, lorsque la techno et la configuration le permettent.

### 3. À quoi sert `../../` ?
À **remonter dans l’arborescence** pour sortir du répertoire prévu et viser d’autres fichiers.

### 4. Comment prévenir les attaques FI ?
En n’utilisant jamais directement l’entrée utilisateur comme chemin, en mettant une allowlist stricte, un mapping interne, un confinement du chemin, des permissions minimales et une configuration défensive.

### 5. Comment une LFI peut-elle mener à une RCE ?
Si l’attaquant peut faire inclure un fichier local contenant du code qu’il contrôle (logs, session, upload, archive, wrapper, etc.).

### 6. Quels impacts peut avoir une FI ?
Lecture arbitraire, fuite de secrets, divulgation de code source, préparation d’autres attaques, RCE, DoS, compromission globale.

### 7. Comment détecter une FI ?
En identifiant les paramètres candidats, en testant les variations de chemins, en observant les erreurs, en auditant les points d’inclusion et en évaluant les chaînes d’exploitation adjacentes.

### 8. Pourquoi le simple filtrage de `../` ne suffit-il pas ?
Parce qu’il existe d’autres chemins, encodages, schémas, comportements de normalisation et surfaces de contournement.

---

## Résumé final

La **File Inclusion** est une vulnérabilité serveur grave qui apparaît lorsqu’une application fait confiance à l’utilisateur pour choisir un fichier à ouvrir, lire ou inclure.

Les idées essentielles à retenir sont :

1. **LFI** = inclusion d’un fichier local  
2. **RFI** = inclusion d’un fichier distant  
3. `../../` sert à **remonter dans l’arborescence**  
4. Le **path traversal** est souvent la technique qui permet d’atteindre le fichier visé  
5. Une **LFI** mène souvent d’abord à une **lecture arbitraire**  
6. Elle peut devenir **RCE** si l’attaquant réussit à faire inclure un fichier local contenant un contenu qu’il contrôle  
7. La vraie défense n’est pas le blacklisting : c’est le **mapping interne + allowlist + confinement + moindre privilège**

---

## Mini fiche mémo

### À savoir par cœur

- **LFI** : Local File Inclusion  
- **RFI** : Remote File Inclusion  
- `../` : remonte d’un dossier parent  
- Risques : disclosure, source leak, RCE, DoS  
- Défense principale : **ne jamais passer un chemin brut utilisateur à une fonction de fichier**

### Réponse courte d’examen

> Une vulnérabilité de File Inclusion apparaît lorsqu’une application utilise une entrée utilisateur pour inclure ou lire un fichier sans validation stricte.  
> La **LFI** vise un fichier local, la **RFI** une ressource distante.  
> Le motif `../../` sert à sortir du répertoire prévu via path traversal.  
> Une LFI peut mener à une RCE si l’attaquant fait inclure un fichier local contenant du code contrôlé.  
> La meilleure prévention repose sur une allowlist stricte, un mapping interne, un confinement de chemin et une configuration défensive.

---

## Références et ressources conseillées

### Références techniques majeures

- OWASP — **Testing for File Inclusion**
- OWASP — **Testing for Local File Inclusion**
- OWASP — **Testing for Remote File Inclusion**
- OWASP — **Path Traversal**
- OWASP — **PHP File Inclusion**
- PHP Manual — **include**
- PHP Manual — **include_once**
- PHP Manual — **require / require_once**
- PHP Manual — **Supported Protocols and Wrappers**
- PHP Manual — **Runtime Configuration**
- PHP Manual — **Core php.ini directives**
- OWASP Cheat Sheet Series — **File Upload Cheat Sheet**

### Ressources complémentaires utiles pour ce module

- *LFI to RCE: Basic Exploitation Guide*
- *File Inclusion Types, Examples, and Prevention*
- *File Inclusion Path Traversal*
- *File Inclusion Cheat Sheet*
- *File Inclusion Payloads* (GitHub)

---

## Conseils de travail pour réussir le module

1. Comprends d’abord la **logique**, pas les payloads.  
2. Sois capable de distinguer **LFI**, **RFI**, **Path Traversal** et **IDOR**.  
3. Révise les **wrappers PHP** les plus importants.  
4. Sache expliquer le passage **LFI → source leak → compréhension de l’appli → chaîne vers RCE**.  
5. En lab, documente toujours :
   - l’entrée
   - la sortie
   - l’impact
   - la remédiation

---

## Annexe — Méthode mentale rapide pendant un audit

Quand tu vois un paramètre de type fichier, pose-toi immédiatement ces questions :

1. **Qui choisit le fichier ?**
2. **L’application prend-elle un chemin brut ?**
3. **Puis-je sortir du répertoire prévu ?**
4. **Le serveur lit-il ou exécute-t-il ?**
5. **Y a-t-il un suffixe ajouté ?**
6. **Y a-t-il des wrappers ?**
7. **Existe-t-il un upload, un log, une session, un cache ou une archive pouvant servir de relais ?**
8. **Quel est l’impact réel ? lecture, disclosure, RCE potentielle ?**

Si tu réponds clairement à ces huit questions, tu comprends déjà le cœur du sujet.

---

## Fin du cours

Ce cours te donne la vision complète nécessaire pour :
- comprendre la vulnérabilité
- l’identifier
- l’exploiter proprement en laboratoire autorisé
- surtout, la **corriger correctement**



## Compléments importants pour aller plus loin

### Surface d’attaque typique dans une application moderne

La faille n’apparaît pas uniquement dans un `include($_GET['x'])` caricatural.

On peut la retrouver dans :

- systèmes de templates artisanaux
- systèmes de thèmes
- sélecteurs de langue
- visualisation de fichiers
- aperçu de documents
- téléchargement de pièces jointes
- export/import
- plugins/modules dynamiques
- routeurs bricolés
- lecteurs d’images ou de rapports
- visionneuses de logs
- fonctionnalités “preview”
- moteurs de rendu Markdown / HTML mal intégrés
- mécanismes de chargement de partials / fragments

---

## Chemins typiques visés en laboratoire

> Les chemins ci-dessous servent à comprendre la logique de l’attaque et à reconnaître des cibles fréquentes en environnement pédagogique.

### Environnements Linux / Unix

- `/etc/passwd`
- `/etc/hosts`
- `/etc/issue`
- répertoires applicatifs sous `/var/www/`
- fichiers `.env`
- fichiers de configuration applicative
- logs web et logs applicatifs
- fichiers de session selon l’environnement
- dossiers d’uploads
- caches et exports temporaires

### Environnements Windows

- `C:\Windows\win.ini`
- `C:\Windows\System32\drivers\etc\hosts`
- répertoires IIS / applications
- dossiers temporaires
- logs IIS / applicatifs
- configurations locales

### Pourquoi connaître ces chemins ?

Pas pour “attaquer au hasard”, mais pour :

- reconnaître rapidement un indice de lecture arbitraire
- adapter la méthodologie au système cible
- distinguer Linux et Windows
- comprendre où vivent les données intéressantes

---

## Signes faibles qui doivent te mettre en alerte

Même sans erreur explicite, certains symptômes sont suspects :

- une page change complètement selon un paramètre `page`, `lang` ou `view`
- une erreur 500 apparaît uniquement pour certaines valeurs
- la taille de la réponse varie fortement quand on modifie un paramètre de fichier
- un mot-clé comme `include`, `require`, `template`, `render`, `preview`, `download`, `path` apparaît dans l’interface, les paramètres, le JS ou les logs
- l’application charge des fragments HTML/PHP selon un sélecteur utilisateur
- des warnings cachés apparaissent dans le HTML ou la console
- la réponse contient un chemin absolu du serveur

---

## Erreurs fréquentes de conception qui créent une FI

### 1. Le routeur artisanal

```php
<?php
include('pages/' . $_GET['page']);
```

Le développeur pense confiner l’inclusion dans `pages/`, mais sans canonicalisation ni allowlist, l’utilisateur peut souvent en sortir.

### 2. Le sélecteur de langue

```php
<?php
include('lang/' . $_GET['lang'] . '.php');
```

C’est un classique.

### 3. Le visualiseur de rapport

```php
<?php
readfile($_GET['report']);
```

Même si ce n’est pas `include`, la logique de contrôle de chemin reste dangereuse.

### 4. Le thème dynamique

```php
<?php
require('themes/' . $_COOKIE['theme'] . '/header.php');
```

Le danger ne vient pas seulement des paramètres GET/POST ; il peut aussi venir de :

- cookies
- headers
- données de session
- corps de requête
- stockage BD réinjecté ensuite dans un chemin

---

## White box : points de revue de code à ne pas oublier

Quand tu lis du code, ne t’arrête pas au mot-clé `include`.

Cherche aussi les endroits où un chemin utilisateur est utilisé dans :

- `readfile`
- `fopen`
- `file_get_contents`
- `glob`
- `scandir`
- `unlink`
- `copy`
- `move_uploaded_file`
- loaders de templates
- loaders de plugins
- viewers de documents
- composants d’export/import
- parseurs d’archives
- générateurs PDF/HTML si un chemin de template est piloté par l’utilisateur

Même si le sujet du module est “File Inclusion”, la compréhension du **contrôle de chemin** est plus large.

---

## Lecture arbitraire vs exécution : bien faire la distinction

Un étudiant confond souvent ces deux situations.

### Cas 1 — lecture arbitraire

L’application lit un fichier et renvoie son contenu.

Conséquences :

- fuite d’informations
- compréhension du code
- préparation d’autres attaques

### Cas 2 — inclusion interprétée

L’application inclut un contenu que l’environnement interprète comme du code.

Conséquences :

- exécution de code
- impact critique

### Pourquoi c’est important

Parce que le **niveau de gravité** et la **chaîne d’exploitation** ne sont pas les mêmes.

Dans un rapport sérieux, il faut distinguer :

- **ce qui est prouvé**
- **ce qui est possible**
- **ce qui nécessite une condition supplémentaire**

---

## Comment raisonner sur l’impact réel

Quand tu découvres une LFI, pose-toi ces questions :

### A. Puis-je lire uniquement des fichiers texte non sensibles ?
Impact faible à modéré selon le contexte.

### B. Puis-je lire des secrets applicatifs ?
Impact élevé.

### C. Puis-je lire le code source ?
Impact élevé à critique, car cela révèle souvent :
- credentials
- endpoints internes
- logique métier
- autres failles

### D. Puis-je viser des logs, sessions, uploads ou caches ?
Risque de chaîne vers RCE.

### E. Puis-je déclencher une inclusion distante ou un wrapper dangereux ?
Risque accru.

### F. Le compte système de l’application a-t-il beaucoup de droits ?
Le blast radius augmente.

---

## Pourquoi la prévention doit être pensée au niveau architecture

Un bon développeur ne “filtre pas des payloads”.  
Il construit un système où la notion même de payload n’a plus d’intérêt.

### Mauvaise architecture

```text
Utilisateur → envoie un chemin → application inclut le chemin
```

### Bonne architecture

```text
Utilisateur → choisit une option logique → application mappe cette option vers une ressource interne connue
```

Exemple :

- utilisateur choisit `faq`
- le serveur décide que `faq` correspond à `pages/faq.php`
- l’utilisateur n’a jamais contrôlé le chemin réel

C’est la vraie solution.

---

## Défense en profondeur : couches recommandées

### Couche 1 — Code sûr
- mapping interne
- allowlist
- canonicalisation
- refus des chemins bruts

### Couche 2 — Configuration
- `allow_url_include` désactivé
- limitation des wrappers si possible
- `open_basedir` si pertinent

### Couche 3 — Permissions système
- utilisateur applicatif non privilégié
- répertoires sensibles non lisibles
- séparation code / données / uploads

### Couche 4 — Déploiement
- secrets hors web root
- uploads hors zones exécutables
- logs correctement protégés

### Couche 5 — Détection
- monitoring des erreurs d’inclusion
- détection des traversals
- alertes sur schémas suspects

---

## LFI et upload : pourquoi cette chaîne revient souvent

Le scénario logique est simple :

1. l’application permet d’envoyer un fichier
2. ce fichier est stocké sur le serveur
3. l’attaquant connaît ou devine son emplacement
4. une LFI permet ensuite de viser ce fichier
5. selon le contexte, le contenu peut être lu ou interprété

Même si l’upload semble “inoffensif” :
- archive
- image
- document
- avatar

le risque réapparaît si une autre fonctionnalité autorise une inclusion.

Conclusion :

> Il faut toujours auditer **les interactions entre fonctionnalités**, pas seulement chaque endpoint isolément.

---

## LFI et logs : pourquoi c’est un classique pédagogique

Beaucoup de données envoyées par le client finissent quelque part :

- User-Agent
- URL demandée
- paramètres
- erreurs
- traces applicatives

Si ces données sont journalisées dans un fichier local et si une LFI permet de lire ou d’inclure ce fichier, on a parfois une chaîne d’attaque intéressante.

L’idée importante n’est pas de retenir une recette fixe, mais de comprendre ce pattern :

```text
donnée utilisateur → écrite dans un fichier local → ce fichier devient une cible LFI
```

---

## LFI et sessions : logique à comprendre

Les sessions peuvent stocker des données liées à l’utilisateur :

- nom
- préférences
- contenu de formulaire
- identifiants métiers
- états intermédiaires

Si ces données se retrouvent dans un fichier local et que ce fichier devient accessible via LFI, le risque augmente.

Encore une fois, la vraie leçon est :

> Tout fichier local contenant des données contrôlées par l’utilisateur peut devenir un tremplin.

---

## Liens avec d’autres vulnérabilités

La File Inclusion ne vit pas seule.

Elle se combine souvent avec :

- **Path Traversal**
- **Unrestricted File Upload**
- **Information Disclosure**
- **RCE**
- **Weak Permissions**
- **Misconfiguration**
- **SSRF-like behaviors** dans certains contextes de chargement distant
- **AuthZ flaws** si certains fichiers sont accessibles sans contrôle
- **LFI révélant du code source**, puis exploitation d’une autre faille trouvée dans ce code

Un bon auditeur pense toujours en **chaîne d’attaque**.

---

## Comment rédiger l’impact dans un rapport

Évite les phrases vagues du style :

> “Le site est hackable.”

Privilégie une formulation précise :

### Exemple de formulation propre

> Le paramètre `page` permet de contrôler le chemin d’un fichier inclus côté serveur sans validation stricte. Cette faiblesse autorise la lecture de fichiers locaux en dehors du répertoire prévu. Selon la présence de fichiers contenant des données contrôlées par l’utilisateur, cette vulnérabilité pourrait être chaînée vers une exécution de code côté serveur. L’impact immédiat est une divulgation d’informations sensibles, pouvant inclure du code source et des secrets applicatifs.

---

## Comment rédiger la remédiation dans un rapport

Évite :

> “Filtrer les inputs.”

Préfère :

1. remplacer le choix de fichier par un identifiant logique
2. mettre en place une allowlist serveur
3. canonicaliser le chemin final et vérifier son appartenance à un répertoire autorisé
4. désactiver l’inclusion d’URL si elle n’est pas nécessaire
5. stocker secrets et uploads hors zones exposées
6. réduire les permissions de l’utilisateur applicatif

---

## Exemples de paramètres à surveiller en reconnaissance

Sans être exhaustif, voici des noms fréquents :

- `file`
- `page`
- `view`
- `template`
- `lang`
- `include`
- `inc`
- `doc`
- `document`
- `folder`
- `path`
- `style`
- `theme`
- `module`
- `content`
- `layout`
- `load`
- `attachment`
- `download`

Attention : le nom du paramètre n’est jamais une preuve, juste un **indice**.

---

## Comment expliquer simplement la différence entre mauvais filtre et vraie validation

### Mauvais filtre

> “Je retire `../`.”

Problèmes :
- contournements
- encodages
- autres séparateurs
- logique incomplète

### Vraie validation

> “Je n’accepte qu’un identifiant parmi une liste fermée, puis le serveur choisit lui-même le fichier.”

Voilà la vraie différence entre :
- **nettoyer une attaque**
- **supprimer la possibilité structurelle de l’attaque**

---

## Rappel sur les exigences du projet

Pour ce module / projet pédagogique :

- environnement de test : **Kali Linux**
- focus : **Cyber - WebSec 0x07**
- scripts demandés : souvent **exactement une ligne**
- fin de ligne obligatoire
- `README.md` obligatoire à la racine du dossier du projet

### Ce que cela implique pour toi

Tu dois être capable de produire :
- des tests propres
- des preuves de concept courtes
- une documentation claire
- une explication du **pourquoi**
- pas seulement “une payload qui marche”

---

## Conseils pratiques de révision orale

Si on te pose une question à l’oral, structure ta réponse en 4 blocs :

### 1. Définition
> Une File Inclusion est une vulnérabilité où une entrée utilisateur influence un fichier chargé côté serveur.

### 2. Variante
> LFI pour un fichier local, RFI pour une ressource distante.

### 3. Impact
> Divulgation d’informations, lecture du code source, et parfois RCE si la faille est chaînée avec un fichier contenant du contenu contrôlé.

### 4. Remédiation
> Mapping interne, allowlist, canonicalisation, moindre privilège, configuration défensive.

Cette structure marche très bien en soutenance, entretien ou correction de projet.

---

## Glossaire express

- **File Inclusion** : inclusion d’un fichier basée sur une entrée non sûre
- **LFI** : Local File Inclusion
- **RFI** : Remote File Inclusion
- **Traversal** : déplacement dans l’arborescence via le chemin
- **Canonicalisation** : transformation d’un chemin en forme normalisée/réelle
- **Allowlist** : liste fermée de valeurs autorisées
- **Wrapper** : mécanisme de flux/protocole permettant un accès particulier à une ressource
- **RCE** : Remote Code Execution
- **Disclosure** : divulgation d’informations sensibles

---
