# Web Application Fast Incident Response

## Cours ultra complet, détaillé, explicatif et pédagogique

---

## 1. Introduction

La réponse à incident appliquée aux applications web consiste à détecter, analyser, contenir, éradiquer et corriger le plus rapidement possible tout événement de sécurité affectant une application exposée via HTTP ou HTTPS, ses API, son infrastructure d’hébergement, ses composants d’authentification, ses bases de données, ses dépendances et ses secrets.

Dans un contexte web, la rapidité est cruciale pour une raison simple : une application publique peut être attaquée à grande échelle, de manière automatisée, continue, et parfois en quelques minutes seulement. Une SQL injection exploitée, une faille de téléversement de fichier, une RCE, une compromission de session, un abus d’API, un web shell ou une mauvaise exposition d’un panneau d’administration peuvent rapidement mener à un vol de données, une altération du service, une propagation dans le système d’information ou une interruption d’activité.

La réponse à incident ne consiste donc pas seulement à "réagir quand quelque chose se passe". C’est une discipline complète qui combine :

- la préparation,
- la visibilité,
- la priorisation,
- la prise de décision rapide,
- la coordination technique,
- la communication,
- la restauration du service,
- et l’amélioration continue après l’incident.

Les recommandations modernes de NIST insistent sur le fait que la réponse à incident doit être intégrée à la gestion du risque cyber globale. Les phases opérationnelles classiques restent extrêmement utiles sur le terrain : préparation, détection et analyse, confinement, éradication, reprise, puis retour d’expérience. En parallèle, le cadre CSF 2.0 rappelle que l’incident response doit être pensé avec les fonctions Govern, Identify, Protect, Detect, Respond et Recover, et non comme une activité isolée.

Pour les applications web, cela veut dire une chose essentielle : on ne fait pas de bonne réponse à incident sans bons journaux, sans corrélation d’événements, sans alerting clair, sans playbooks, sans connaissance des comportements attaquants, et sans capacité à remettre rapidement en ligne un service propre et maîtrisé.

---

## 2. Objectifs pédagogiques du module

À la fin de ce cours, tu dois pouvoir expliquer clairement :

- les étapes d’une réponse à incident web et pourquoi chacune est importante,
- comment détecter rapidement une attaque sur une application web,
- comment contenir, éradiquer puis restaurer un service compromis,
- comment utiliser les logs, la supervision, l’EDR, les scanners et l’automatisation,
- comment documenter l’incident et communiquer correctement,
- pourquoi le retour d’expérience est indispensable,
- quelles compétences sont nécessaires pour prioriser correctement une menace,
- comment réduire l’impact business et remettre le service en fonctionnement le plus vite possible.

---

## 3. Qu’est-ce qu’un incident de sécurité web ?

Un incident de sécurité web est un événement qui compromet, ou menace de compromettre, la confidentialité, l’intégrité, la disponibilité, la traçabilité ou la conformité d’une application web ou d’une API.

### Exemples concrets

Un incident peut être :

- une exfiltration de données via SQL injection,
- l’upload d’un web shell via une fonctionnalité de téléversement,
- un vol de session via cookie compromise ou fuite de token,
- une compromission de compte via credential stuffing,
- une élévation de privilèges par mauvaise autorisation,
- une fuite de secrets dans des logs ou dans un dépôt,
- une modification malveillante du code applicatif,
- un abus massif d’une API,
- un ransomware sur le serveur hébergeant l’application,
- une défiguration de site,
- une indisponibilité provoquée par saturation ou exploitation applicative.

### Ce qui distingue un incident web d’un incident système classique

Une application web est au croisement de plusieurs couches :

- client navigateur ou mobile,
- reverse proxy, CDN, WAF,
- serveur web,
- code applicatif,
- gestion de session,
- API,
- base de données,
- services tiers,
- cloud,
- pipeline de déploiement,
- identité et accès.

Répondre à un incident web implique donc souvent de corréler des traces venant de nombreuses sources différentes.

---

## 4. Réponse à incident et cyber risk mitigation

La mitigation du risque cyber, ou réduction du risque, consiste à diminuer la probabilité qu’un incident se produise et à limiter son impact s’il se produit quand même.

La réponse à incident fait partie de cette logique :

- avant l’incident, elle impose de préparer l’environnement,
- pendant l’incident, elle réduit la durée et l’ampleur de l’attaque,
- après l’incident, elle corrige les causes racines pour éviter la répétition.

Autrement dit :

- la prévention réduit la surface d’attaque,
- la détection réduit le temps avant identification,
- la réponse réduit le temps avant maîtrise,
- la récupération réduit le temps avant retour au service normal,
- le retour d’expérience réduit le risque futur.

Les organisations les plus matures ne séparent pas prévention, détection et réponse : elles les articulent dans un cycle continu.

---

## 5. Le cycle de vie de la réponse à incident

## 5.1 Vue d’ensemble

Le modèle opérationnel le plus utile à retenir est le suivant :

1. Préparation
2. Détection et analyse
3. Confinement
4. Éradication
5. Récupération
6. Retour d’expérience

Tu verras parfois les trois étapes 3, 4 et 5 regroupées. En pratique terrain, les séparer aide beaucoup à raisonner proprement.

---

## 5.2 Phase 1 — Préparation

C’est la phase la plus sous-estimée. Pourtant, sans préparation, la réponse à incident devient lente, désordonnée, stressante, incomplète et parfois destructrice pour les preuves.

### Objectifs

- savoir qui fait quoi,
- savoir quoi surveiller,
- disposer d’outils prêts,
- disposer de journaux exploitables,
- disposer d’un inventaire clair,
- disposer de procédures de confinement,
- disposer de sauvegardes et de mécanismes de restauration,
- disposer de canaux de communication adaptés.

### Ce qui doit exister avant l’incident

#### a. Une équipe et des rôles définis

Exemples de rôles :

- Incident Response Manager : coordonne la réponse,
- analyste sécurité ou SOC : qualifie et investigue,
- développeur ou lead applicatif : connaît la logique métier et le code,
- administrateur système ou cloud engineer : agit sur l’infra,
- DBA : vérifie l’état et l’intégrité des données,
- RSSI ou responsable sécurité : arbitre le niveau de réponse,
- juridique et conformité : gèrent notification, responsabilité, conservation de preuve,
- communication ou direction : pilotent le message externe si nécessaire.

#### b. Un inventaire des actifs critiques

Il faut savoir :

- quelles applications existent,
- quelles API sont exposées,
- quels domaines et sous-domaines sont actifs,
- quelles versions sont en production,
- où se trouvent les secrets,
- où sont stockées les données sensibles,
- quels services tiers sont utilisés,
- quels comptes de service existent,
- quelles dépendances sont critiques.

Si tu ignores ce que tu protèges, tu ne peux ni prioriser ni contenir correctement.

#### c. Une journalisation exploitable

Une application web doit produire des logs utiles à la sécurité. Les seuls logs du serveur web ne suffisent pas.

Il faut idéalement centraliser :

- logs reverse proxy ou load balancer,
- logs WAF ou CDN,
- logs application,
- logs API,
- logs authentification,
- logs système,
- logs base de données,
- logs cloud et IAM,
- traces EDR,
- alertes scanner,
- alertes SIEM.

#### d. Des playbooks

Un playbook est une procédure standardisée pour un type d’incident.

Exemples de playbooks web :

- SQL injection suspectée,
- téléversement de web shell,
- credential stuffing,
- fuite de token API,
- abus d’endpoint sensible,
- compromission admin,
- DDoS applicatif,
- défiguration de site,
- RCE sur composant exposé,
- fuite de données via API.

#### e. Une capacité de confinement rapide

Il faut savoir faire rapidement :

- bloquer une IP ou un ASN,
- imposer une règle WAF temporaire,
- désactiver un compte,
- invalider toutes les sessions,
- couper un endpoint,
- mettre une application en maintenance,
- retirer un conteneur ou un pod compromis,
- isoler un hôte via l’EDR,
- révoquer des clés ou secrets.

#### f. Des sauvegardes et une restauration testée

Une sauvegarde non testée n’est pas une vraie capacité de reprise.

Il faut vérifier :

- fréquence,
- intégrité,
- délai de restauration,
- périmètre couvert,
- séparation des sauvegardes du système compromis,
- restauration possible vers un environnement propre.

#### g. Une préparation forensic minimale

Il faut prévoir :

- horodatage synchronisé,
- conservation des logs,
- procédures de collecte,
- emplacement de stockage de preuve,
- hash des artefacts si nécessaire,
- chaîne de conservation si contexte légal ou réglementaire.

---

## 5.3 Phase 2 — Détection et analyse

C’est la phase où l’on décide si l’on a réellement un incident, ce qu’il touche, à quel point il est grave, et ce qu’il faut faire immédiatement.

### Détection : d’où vient le signal ?

Le signal initial peut venir de :

- logs applicatifs,
- SIEM,
- WAF,
- IDS ou IPS,
- EDR,
- alertes cloud,
- plaintes utilisateurs,
- bug bounty ou signalement externe,
- scanner de vulnérabilités,
- surveillance de disponibilité,
- anomalie métier,
- alerte sur export massif de données,
- activité IAM anormale,
- détection d’un web shell,
- volumétrie inhabituelle sur une API.

### Analyse : questions à se poser immédiatement

1. Est-ce un vrai incident ou un faux positif ?
2. Que voyons-nous exactement ?
3. Depuis quand ?
4. Quel est le périmètre touché ?
5. Quel est l’impact probable ?
6. L’attaquant est-il encore actif ?
7. L’incident touche-t-il des données sensibles ?
8. Faut-il confiner immédiatement avant même l’analyse complète ?

### Triage

Le triage consiste à classer l’incident selon :

- criticité de l’actif,
- impact confidentialité,
- impact intégrité,
- impact disponibilité,
- exposition externe ou interne,
- sensibilité des données,
- niveau de privilège compromis,
- possibilité de propagation,
- confiance dans le signal,
- urgence business.

### Exemples de signaux forts en web

- rafales de 401, 403 ou 404 sur des chemins sensibles,
- nombreuses erreurs de validation inhabituelles,
- chaînes caractéristiques d’injection dans paramètres,
- demandes sur fichiers d’admin, backups, .env, .git,
- création d’utilisateurs admin inattendue,
- export massif de données,
- uploads suspects,
- requêtes POST anormales vers des chemins rarement utilisés,
- appels API depuis pays ou ASN inattendus,
- hausse brutale de réponse 500 ou 502 après exploitation,
- ouverture d’un shell, d’un interpréteur ou d’un outil réseau sur l’hôte,
- modification de fichiers web hors fenêtre de déploiement,
- apparition de tâches planifiées, services ou nouveaux binaires.

### Pourquoi cette phase est critique

Une mauvaise analyse mène à deux erreurs opposées :

- sur-réagir et casser inutilement la production,
- sous-réagir et laisser l’attaquant opérer plus longtemps.

Le bon réflexe n’est pas la panique, mais la structuration.

---

## 5.4 Phase 3 — Confinement

Le confinement consiste à stopper l’hémorragie sans détruire inutilement les preuves et sans aggraver la situation.

### Objectif

Limiter :

- la propagation,
- l’exfiltration,
- la persistance,
- l’impact business,
- l’accès attaquant.

### Confinement court terme

Actions typiques :

- bloquer l’adresse IP, le sous-réseau ou le pays source si pertinent,
- appliquer une règle WAF ciblée,
- couper temporairement un endpoint vulnérable,
- invalider des sessions,
- désactiver un compte compromis,
- retirer un nœud compromis du load balancer,
- isoler un serveur ou conteneur via EDR,
- désactiver un token, une clé API, un secret ou un certificat compromis,
- suspendre une fonctionnalité à risque, par exemple l’upload de fichier.

### Confinement long terme

Actions plus stables :

- forcer une rotation globale des secrets,
- imposer MFA ou step-up auth,
- mettre en maintenance l’application,
- rediriger le trafic vers une version saine,
- restreindre l’accès admin via VPN ou bastion,
- basculer sur un mode dégradé maîtrisé.

### Attention

Confinir trop brutalement peut :

- faire perdre des preuves,
- déclencher l’attaquant,
- casser le service plus que nécessaire,
- empêcher de comprendre la cause racine.

Exemple : éteindre un serveur à chaud peut faire perdre une partie des artefacts de mémoire. Dans certains cas, on préfère isoler le système avant collecte.

---

## 5.5 Phase 4 — Éradication

Éradiquer, ce n’est pas juste supprimer le symptôme visible. C’est retirer la cause, la persistance, les artefacts, et fermer la porte utilisée.

### Actions typiques

- suppression d’un web shell,
- suppression d’un utilisateur malveillant,
- nettoyage de tâches planifiées ou services persistants,
- patch du composant vulnérable,
- correction du code applicatif,
- mise à jour d’une dépendance compromise,
- rotation des secrets,
- correction d’une règle IAM trop permissive,
- fermeture d’un port ou d’une console d’administration exposée,
- purge d’objets malveillants uploadés,
- révocation de certificats ou de tokens.

### Cause racine

Tu dois toujours te demander :

- quelle vulnérabilité ou faiblesse a été exploitée,
- pourquoi elle existait,
- pourquoi elle n’a pas été détectée plus tôt,
- pourquoi les contrôles de prévention n’ont pas suffi,
- comment empêcher la réintroduction du même problème.

Supprimer un web shell sans corriger la faille d’upload, c’est laisser la porte ouverte.

---

## 5.6 Phase 5 — Récupération

La récupération consiste à remettre le service en état de fonctionnement normal, mais seulement après validation suffisante que l’environnement restauré est sain.

### Objectifs

- restaurer le service rapidement,
- éviter une rechute immédiate,
- surveiller étroitement après remise en ligne,
- rassurer les parties prenantes avec des vérifications concrètes.

### Bonnes pratiques

- reconstruire depuis une image propre si possible,
- restaurer les données depuis une sauvegarde saine si nécessaire,
- revalider la configuration,
- rejouer les tests fonctionnels critiques,
- rejouer des tests de sécurité ciblés,
- activer une surveillance renforcée temporaire,
- réouvrir progressivement les fonctionnalités,
- vérifier l’absence d’artefacts ou d’accès persistants.

### Questions à valider avant remise en production

- la faille exploitée est-elle corrigée,
- les comptes compromis sont-ils neutralisés,
- les secrets ont-ils été tournés,
- les données ont-elles été vérifiées,
- les endpoints sensibles sont-ils surveillés,
- les alertes sont-elles bien déclenchées,
- la restauration n’a-t-elle pas réintroduit un composant vulnérable.

---

## 5.7 Phase 6 — Retour d’expérience

C’est l’étape qui transforme un incident subi en amélioration réelle.

### Pourquoi elle est indispensable

Sans retour d’expérience :

- les mêmes erreurs reviennent,
- les mêmes angles morts restent présents,
- les playbooks ne progressent pas,
- le temps de détection et de réponse ne s’améliore pas.

### Ce qu’il faut documenter

- chronologie complète,
- signal initial,
- périmètre touché,
- vecteur d’attaque,
- impact réel,
- décisions prises,
- actions de confinement,
- actions d’éradication,
- actions de récupération,
- difficultés rencontrées,
- contrôles manquants,
- améliorations à implémenter,
- responsables et échéances.

### Indicateurs utiles

- MTTD : Mean Time To Detect,
- MTTA : Mean Time To Acknowledge,
- MTTC : Mean Time To Contain,
- MTTR : Mean Time To Recover ou Remediate selon contexte,
- nombre d’actifs touchés,
- volume de données potentiellement exposées,
- temps d’indisponibilité,
- coût technique et business.

---

## 6. Le cadre NIST et son intérêt pratique

Le guide historique le plus connu pour la réponse à incident reste NIST SP 800-61. La révision 3, publiée en 2025, repositionne clairement la réponse à incident dans la gestion du risque cyber globale et dans l’écosystème CSF 2.0.

### Ce qu’il faut retenir sans se perdre

#### Vision opérationnelle classique

- préparation,
- détection et analyse,
- confinement,
- éradication,
- récupération,
- post-incident.

#### Vision de gouvernance moderne

- Govern,
- Identify,
- Protect,
- Detect,
- Respond,
- Recover.

En entretien, en examen ou en oral, tu peux expliquer que :

- NIST 800-61 garde une logique de terrain pour traiter un incident,
- CSF 2.0 permet de relier cette logique à la gouvernance, au risque, aux priorités métier et à l’amélioration continue.

---

## 7. Pourquoi la réponse à incident web doit être rapide

Une application web publique peut être :

- scannée automatiquement en permanence,
- exploitée à distance sans accès préalable,
- utilisée comme point d’entrée vers d’autres systèmes,
- attaquée à très fort volume,
- compromise puis monétisée très vite.

### Conséquences d’un retard

- augmentation du volume de données exfiltrées,
- multiplication des comptes compromis,
- propagation latérale,
- persistance de l’attaquant,
- altération plus importante des données,
- impact business plus élevé,
- perte de confiance client,
- difficulté à reconstituer la chronologie.

Une réponse rapide n’est pas seulement une qualité technique. C’est un facteur direct de réduction de l’impact.

---

## 8. Détecter et identifier rapidement les attaques web

## 8.1 Les sources de détection

### a. Logs application

Ils sont essentiels car ils connaissent la logique métier. Ils peuvent révéler :

- échec d’authentification,
- échec d’autorisation,
- changement de privilège,
- création ou suppression d’objet sensible,
- usage d’une fonctionnalité à haut risque,
- upload de contenu,
- export de données,
- erreurs métier anormales,
- enchaînements d’actions incohérents.

### b. Logs reverse proxy, web server, load balancer

Ils révèlent :

- méthodes HTTP utilisées,
- chemins demandés,
- user-agent,
- IP source,
- codes de réponse,
- taille de requête et de réponse,
- volumétrie,
- fréquences de requêtes.

### c. WAF, CDN, bot protection

Ils aident à repérer :

- payloads d’injection,
- scans automatisés,
- traversals,
- uploads suspects,
- anomalies de protocole,
- bot abuse,
- requêtes massives sur endpoints sensibles.

### d. EDR et télémétrie endpoint

L’EDR devient crucial dès qu’une attaque applicative touche l’hôte ou le conteneur.

Exemples :

- création de processus suspects,
- exécution d’interpréteurs,
- curl, wget, powershell, bash lancés par le serveur web,
- création de nouveaux binaires,
- modification de fichiers applicatifs,
- persistance,
- connexions sortantes inhabituelles.

### e. Logs base de données

Ils aident à détecter :

- requêtes anormales,
- volumétrie inhabituelle,
- erreurs de syntaxe répétées,
- accès massifs à des tables sensibles,
- export ou dump.

### f. Logs IAM et cloud

Indispensables en environnement moderne :

- création de nouveaux accès,
- changement de policy,
- rotation non autorisée,
- accès console inhabituels,
- usage anormal de comptes de service,
- lecture de secrets,
- snapshots inattendus.

### g. Scanners de vulnérabilités

Ils ne détectent pas toujours un incident en temps réel, mais ils servent à :

- découvrir une faiblesse exploitée,
- vérifier l’exposition résiduelle,
- confirmer qu’un correctif est bien en place,
- prioriser la remédiation post-incident.

---

## 8.2 Ce qu’il faut absolument journaliser

Pour aller vite, il faut avoir les bons événements. Selon les recommandations OWASP, il faut journaliser autant que possible :

- échecs de validation d’entrée,
- succès et échecs d’authentification,
- échecs d’autorisation,
- anomalies de session,
- erreurs applicatives et système,
- activités d’administration,
- accès à données sensibles,
- import ou export de données,
- uploads de fichiers,
- erreurs de désérialisation,
- connexions réseau et verbes HTTP inattendus,
- activités métier suspectes,
- tentatives de contournement de workflow,
- dépassements de limite.

### Ce qu’il ne faut pas logguer en clair

Toujours selon les bonnes pratiques de journalisation :

- mots de passe,
- tokens d’accès,
- identifiants de session bruts,
- clés et secrets,
- chaînes de connexion BD,
- données bancaires,
- données personnelles sensibles.

On conserve l’utilité du log sans transformer le système de logs en fuite secondaire.

---

## 8.3 Indicateurs d’attaque typiques par famille

### SQL Injection

Signaux possibles :

- erreurs SQL inhabituelles,
- requêtes contenant UNION, SELECT, SLEEP, OR 1=1,
- hausse d’erreurs 500 sur un endpoint précis,
- latences anormales liées à time-based payloads,
- accès massif à tables sensibles,
- dump ou export inattendu.

### File Upload Abuse et Web Shell

Signaux possibles :

- upload de fichiers avec extensions inattendues,
- double extension,
- MIME type incohérent,
- nouveaux fichiers exécutables dans répertoires web,
- processus shell lancés par l’utilisateur du serveur web,
- connexions sortantes après upload.

### Credential Stuffing ou Brute Force

Signaux possibles :

- très nombreux échecs de login,
- tentatives réparties sur de nombreux comptes,
- même IP ou botnet,
- user-agent automatisés,
- hausse de MFA failures,
- connexions réussies suivies d’actions rapides à risque.

### Broken Access Control ou IDOR

Signaux possibles :

- accès à des identifiants séquentiels,
- navigation horizontale ou verticale anormale,
- échecs 403 suivis de succès sur objets voisins,
- export ou consultation massive hors profil normal,
- enchaînement illogique d’actions métier.

### Command Injection ou RCE

Signaux possibles :

- payloads avec séparateurs de commande,
- création de processus inhabituels,
- téléchargements de scripts,
- activité shell liée au processus web,
- connexions réseau inattendues,
- nouveaux jobs planifiés.

### Session Hijacking

Signaux possibles :

- changement brutal d’IP, device ou geoloc,
- même session utilisée depuis plusieurs emplacements,
- actions admin inattendues,
- réauthentification ou changement MFA suspect,
- cookies anormaux.

### Data Exfiltration

Signaux possibles :

- volumétrie de réponse anormale,
- nombreuses pages d’export,
- usage abusif des endpoints de reporting,
- compression ou archivage,
- transferts sortants inhabituels,
- scans avant export massif.

---

## 8.4 Utiliser le comportement attaquant pour raisonner plus vite

MITRE ATT&CK aide à penser en termes de tactiques et techniques plutôt qu’en simple liste de bugs.

Pour le web, quelques catégories utiles à garder en tête :

- Initial Access : exploitation d’application exposée,
- Valid Accounts : utilisation de comptes volés,
- Persistence : maintien d’accès après premier compromis,
- Credential Access : vol de secrets ou credentials,
- Defense Evasion : suppression ou altération de traces,
- Discovery : exploration de fichiers, répertoires, endpoints,
- Collection : préparation d’exfiltration,
- Exfiltration : sortie de données,
- Impact : altération, destruction, indisponibilité.

Ce modèle aide à répondre à cette question essentielle :

où en est l’attaquant dans sa chaîne d’action ?

Si tu sais cela, tu priorises mieux.

---

## 9. Threat hunting web : comment investiguer vite et proprement

Le threat hunting en contexte de réponse à incident consiste à rechercher activement des indices d’activité malveillante, même avant d’avoir une preuve complète.

### Questions de chasse prioritaires

- Quels endpoints ont reçu des requêtes anormales ?
- Quels comptes ont réalisé des actions inhabituelles ?
- Quelles IP ou ASN apparaissent en forte volumétrie ?
- Quels uploads récents doivent être revus ?
- Quels nouveaux fichiers sont apparus dans l’arborescence web ?
- Quels processus ont été lancés par le serveur web ?
- Quels secrets ont été lus ou modifiés ?
- Quels objets métier ont été exportés ou massivement consultés ?

### Ordre d’investigation recommandé

1. confirmer le signal,
2. cadrer la fenêtre temporelle,
3. identifier les actifs touchés,
4. identifier le ou les comptes impliqués,
5. rechercher les artefacts système,
6. rechercher la persistance,
7. estimer l’impact données,
8. décider confinement.

---

## 10. Confinement, éradication et récupération en détail

## 10.1 Méthodes de confinement efficaces pour les incidents web

### a. Confinement réseau

- blocage IP ou ASN,
- filtrage géographique si justifié,
- limitation de débit,
- fermeture temporaire d’un port ou d’un accès admin.

### b. Confinement applicatif

- désactivation d’un endpoint,
- feature flag pour couper une fonction,
- ajout d’une validation côté serveur,
- réponse maintenance pour routes critiques,
- restriction de méthodes HTTP,
- limitation stricte de taille de requête.

### c. Confinement identité

- suspension de comptes,
- révocation de sessions,
- reset forcé mots de passe,
- MFA forcé,
- révocation tokens OAuth, JWT ou API keys.

### d. Confinement hôte ou conteneur

- isolation EDR,
- retrait du trafic via load balancer,
- arrêt contrôlé du pod ou conteneur compromis,
- bascule vers une instance propre.

### e. Confinement données

- suspendre exports,
- désactiver synchronisations externes,
- figer certaines écritures le temps de vérifier l’intégrité.

---

## 10.2 Éradication efficace

### Règle importante

On n’éradique pas à moitié.

Exemples :

- si un secret est potentiellement exposé, on le tourne,
- si un serveur est suspect d’avoir été utilisé pour une RCE, une reconstruction propre est souvent préférable à un simple "nettoyage",
- si une dépendance est vulnérable, on met à jour et on vérifie toutes les instances.

### Validation d’éradication

- plus d’artefacts malveillants,
- plus de comportement anormal observé,
- patch ou correctif déployé,
- secrets tournés,
- accès revus,
- détection renforcée en place.

---

## 10.3 Récupération rapide sans rechute

### Logique recommandée

- rebuild propre,
- restauration contrôlée,
- tests de sécurité ciblés,
- remise en ligne progressive,
- surveillance renforcée.

### Exemple simple

Une application est compromise via upload RCE :

- l’upload est désactivé,
- l’hôte est isolé,
- les artefacts sont collectés,
- le web shell est confirmé,
- une image saine est redéployée,
- la fonction d’upload est corrigée,
- les fichiers précédemment téléversés sont rescannés,
- les secrets présents sur l’hôte sont tournés,
- la remise en ligne se fait avec monitoring renforcé.

---

## 11. Le rôle de l’automatisation dans la réponse rapide

L’automatisation n’est pas là pour remplacer la réflexion. Elle est là pour accélérer les tâches répétitives, standardisées et urgentes.

## 11.1 Pourquoi c’est utile

Les équipes humaines ne peuvent pas traiter manuellement toutes les alertes.

L’automatisation permet :

- la corrélation rapide d’événements,
- l’enrichissement automatique d’alertes,
- le déclenchement de playbooks,
- l’isolement rapide d’un poste ou serveur,
- la révocation de sessions ou de tokens,
- la génération de timeline,
- la documentation automatique partielle,
- la réduction du temps de triage.

## 11.2 SIEM, SOAR, EDR : différence simple

### SIEM

Il collecte, centralise, corrèle et alerte à partir des logs et événements.

### SOAR

Il orchestre et automatise les actions de réponse selon des playbooks.

### EDR

Il surveille les endpoints, détecte les comportements suspects et peut contenir automatiquement certaines menaces, par exemple en isolant un hôte.

## 11.3 Exemples d’automatisation utiles en web

- si rafale de credential stuffing détectée, appliquer rate limit et captcha renforcé,
- si un endpoint admin reçoit une tentative d’accès non autorisé depuis un pays inhabituel, alerter et bloquer temporairement,
- si EDR voit un shell lancé par le processus du serveur web, isoler l’hôte et ouvrir un ticket critique,
- si un secret fuit dans un dépôt ou log, déclencher une rotation et une notification,
- si un scanner détecte une CVE critique exposée, enrichir le ticket avec criticité, périmètre et version.

## 11.4 Ce qu’il ne faut pas automatiser aveuglément

- suppression irréversible de preuves,
- rotation massive destructrice sans analyse,
- blocages trop larges qui cassent inutilement le service,
- décisions juridiques ou de communication,
- qualification finale d’un incident complexe.

L’automatisation doit être encadrée par des garde-fous.

---

## 12. Documentation et communication pendant l’incident

## 12.1 Pourquoi documenter en temps réel

Parce qu’en incident :

- la mémoire humaine est imparfaite,
- plusieurs équipes interviennent,
- les décisions doivent être justifiables,
- certaines obligations légales ou réglementaires peuvent exister,
- le retour d’expérience dépend d’une trace fiable.

## 12.2 Ce qu’il faut noter

- date et heure,
- détecteur initial,
- système concerné,
- hypothèse initiale,
- éléments de preuve collectés,
- décisions prises,
- personnes impliquées,
- actions exécutées,
- résultat de chaque action,
- impact estimé,
- statut courant.

## 12.3 La timeline d’incident

Une timeline bien tenue aide à répondre à :

- quand l’attaque a commencé,
- quand elle a été détectée,
- quand elle a été contenue,
- quelles actions ont été faites,
- quand le service a été rétabli,
- ce qui reste incertain.

## 12.4 Communication interne

Il faut informer les bons acteurs sans créer de bruit inutile.

Exemples d’informations à transmettre :

- nature de l’incident,
- périmètre touché,
- gravité,
- actions en cours,
- impact service,
- besoins décisionnels,
- point de situation suivant.

## 12.5 Communication externe

Elle dépend du contexte :

- clients,
- partenaires,
- régulateurs,
- autorités,
- médias.

Elle doit être :

- factuelle,
- coordonnée,
- juridiquement relue si besoin,
- honnête sur ce qui est confirmé et ce qui est encore en cours d’analyse.

---

## 13. Post-incident review : pourquoi c’est si important

Le retour d’expérience permet de transformer l’incident en levier d’amélioration.

### Trois niveaux d’analyse

#### a. Technique

- quelle faille,
- quel vecteur,
- quels logs ont aidé,
- quels contrôles ont échoué,
- quels artefacts ont été observés.

#### b. Processus

- qui a été alerté trop tard,
- quel playbook manquait,
- quelle décision a pris trop de temps,
- quel outil manquait,
- quel accès n’était pas prêt.

#### c. Gouvernance et métier

- quel impact business,
- quel arbitrage a été nécessaire,
- quel niveau de risque était accepté ou non,
- quelles priorités budgétaires doivent changer.

### Résultat attendu

À la fin, il doit exister un plan d’action concret :

- correctifs techniques,
- améliorations de logs,
- nouveaux playbooks,
- nouveaux tests,
- évolution du monitoring,
- durcissement architecture,
- formation des équipes.

---

## 14. Outils de détection et de monitoring utiles

## 14.1 Log management et log monitoring

Le log monitoring consiste à collecter, analyser et exploiter les journaux de manière continue afin de détecter des anomalies, des incidents et des problèmes opérationnels.

### Objectifs

- visibilité temps réel,
- corrélation d’événements,
- recherche rapide lors d’un incident,
- détection d’anomalies,
- support forensic,
- support conformité.

### Bonnes pratiques

- centraliser les logs,
- uniformiser le format,
- synchroniser les horloges,
- protéger l’intégrité des journaux,
- définir des rétentions adaptées,
- créer des alertes utiles et non bruyantes,
- distinguer logs de debug et logs de sécurité,
- utiliser des identifiants de corrélation par requête ou interaction.

## 14.2 SIEM

Le SIEM permet :

- ingestion multi-source,
- corrélation,
- détection,
- tableaux de bord,
- requêtes d’investigation,
- conservation d’historique,
- déclenchement d’alertes.

## 14.3 EDR

L’EDR est particulièrement utile quand une attaque web sort de la couche HTTP et touche l’hôte.

Exemple très important :

si un serveur nginx, apache, php-fpm, gunicorn, node ou tomcat lance un shell, un utilitaire de téléchargement ou modifie brutalement des fichiers applicatifs, l’EDR peut être le signal décisif.

## 14.4 Vulnerability scanners

Les scanners de vulnérabilités web, souvent de type DAST, aident à :

- repérer des faiblesses connues,
- vérifier l’exposition après incident,
- confirmer l’application d’un correctif,
- réduire la probabilité de récurrence.

Mais un scanner ne remplace ni un programme de gestion des vulnérabilités, ni l’analyse humaine, ni la réponse à incident.

## 14.5 WAF

Le WAF est un contrôle de protection et aussi une source de détection. Il peut aider à :

- bloquer certains payloads,
- gagner du temps en confinement,
- générer des alertes utiles,
- observer les motifs d’attaque les plus fréquents.

Il ne remplace pas la correction du code.

---

## 15. Compétences nécessaires pour identifier et prioriser correctement les menaces

Un bon répondant à incident web doit combiner plusieurs familles de compétences.

## 15.1 Compétences techniques

- HTTP, HTTPS, cookies, sessions, JWT,
- architecture web et API,
- authN et authZ,
- logs et SIEM,
- Linux et processus,
- bases de données,
- cloud et IAM,
- sécurité applicative OWASP,
- forensic de base,
- bash et investigation,
- lecture de code et compréhension métier.

## 15.2 Compétences analytiques

- distinguer bruit et signal,
- prioriser sous stress,
- construire une chronologie,
- formuler et tester des hypothèses,
- raisonner par impact,
- rechercher la cause racine.

## 15.3 Compétences humaines

- communiquer clairement,
- documenter correctement,
- coordonner plusieurs équipes,
- escalader au bon moment,
- garder une pensée structurée.

Un très bon technicien qui ne sait pas prioriser ou communiquer peut ralentir la réponse. Un très bon coordinateur qui ne comprend pas les signaux techniques peut mal arbitrer. Il faut les deux dimensions.

---

## 16. Restaurer les opérations normales le plus vite possible

L’objectif n’est pas seulement de "supprimer l’attaque". L’objectif métier est de restaurer un service fiable, contrôlé et défendable.

### Principes

- isoler vite,
- comprendre assez vite,
- corriger réellement,
- reconstruire proprement,
- réouvrir progressivement,
- surveiller davantage après reprise.

### Stratégies utiles

- mode dégradé plutôt qu’arrêt total si possible,
- bascule vers infrastructure saine,
- coupure ciblée plutôt que globale,
- priorisation des parcours métier critiques,
- communication claire sur l’état du service,
- vérification post-remise en ligne.

Le meilleur rétablissement n’est pas forcément le plus rapide à court terme. C’est celui qui évite une seconde compromission quelques heures après.

---

## 17. Cas concrets de réponse rapide

## 17.1 Cas 1 — Credential stuffing sur portail utilisateur

### Signaux

- hausse massive d’échecs login,
- grand nombre de comptes visés,
- taux de réussite faible mais non nul,
- IP multiples ou proxys,
- alertes sur MFA failures.

### Réponse

- activer rate limiting renforcé,
- ajouter CAPTCHA ou step-up auth,
- bloquer IP ou ASN les plus agressifs,
- forcer reset sur comptes probablement compromis,
- invalider sessions suspectes,
- surveiller transactions ou changements profil,
- communiquer aux utilisateurs concernés si nécessaire.

### Cause racine possible

- mots de passe réutilisés,
- absence de MFA,
- détection de bot trop faible,
- seuils d’alerte mal calibrés.

---

## 17.2 Cas 2 — Web shell après upload malveillant

### Signaux

- upload d’un fichier suspect,
- exécution de processus shell par le serveur web,
- connexions sortantes anormales,
- nouveaux fichiers dans répertoire web.

### Réponse

- désactiver immédiatement l’upload,
- retirer l’instance compromise du trafic,
- collecter logs et artefacts,
- identifier fichiers déposés et appels associés,
- rechercher persistance,
- reconstruire depuis une base saine,
- corriger le filtre d’upload, le stockage et l’exécution,
- tourner les secrets présents sur l’hôte.

---

## 17.3 Cas 3 — Exfiltration via API

### Signaux

- volumétrie anormale,
- export massif,
- accès répétés à objets séquentiels,
- token réutilisé intensivement,
- réponses très volumineuses.

### Réponse

- révoquer le token ou la clé,
- limiter ou suspendre l’endpoint,
- analyser l’étendue des données touchées,
- vérifier les journaux d’accès par objet,
- corriger l’autorisation ou le rate limit,
- notifier si obligations réglementaires.

---

## 18. Lien avec OWASP Top 10 et la sécurité applicative

La réponse à incident web est fortement liée à plusieurs catégories OWASP.

### A09 Security Logging and Monitoring Failures

Sans journalisation suffisante et sans surveillance, l’incident peut durer longtemps avant détection.

### Broken Access Control

Très fréquent en pratique, souvent difficile à détecter si les logs métier sont pauvres.

### Injection

Peut conduire à exfiltration, RCE, altération de données.

### Security Misconfiguration

Expositions d’admin, headers faibles, debug activé, stockage mal protégé, buckets exposés.

### Cryptographic Failures

Peut compliquer la détection ou aggraver la fuite de données.

Une bonne réponse à incident améliore ensuite la prévention sur chacune de ces catégories.

---

## 19. Checklists opérationnelles

## 19.1 Checklist de triage rapide

- Quel est le signal exact ?
- Quelle source a déclenché ?
- Quel système est touché ?
- Quel type d’incident soupçonne-t-on ?
- Quelle est la fenêtre temporelle ?
- Quel est l’impact estimé ?
- Le service est-il encore attaqué ?
- Faut-il confiner immédiatement ?
- Quelles preuves doivent être conservées ?
- Qui doit être informé maintenant ?

## 19.2 Checklist de confinement

- IP ou comptes bloqués ?
- Sessions invalidées ?
- Tokens ou secrets révoqués ?
- Endpoint vulnérable désactivé ?
- Hôte compromis isolé ?
- Capture de logs et artefacts réalisée ?
- Périmètre étendu vérifié ?

## 19.3 Checklist de récupération

- Cause racine corrigée ?
- Artefacts supprimés ?
- Secrets tournés ?
- Tests fonctionnels passés ?
- Tests sécurité passés ?
- Monitoring renforcé activé ?
- Communication de reprise préparée ?

---

## 20. Mini-lab mental autour du fichier logs.txt

Le projet mentionne un fichier logs.txt. L’idée pédagogique est généralement d’apprendre à reconnaître rapidement des signes d’attaque dans des journaux simples.

### Ce que tu peux chercher dans logs.txt

- IPs récurrentes,
- chemins sensibles,
- méthodes HTTP rares,
- erreurs 401, 403, 404, 500 fréquentes,
- motifs d’injection,
- scans de répertoires,
- uploads suspects,
- anomalies de user-agent,
- séquences temporelles rapides,
- accès à des ressources d’administration.

### Questions d’analyse

- Quelle IP semble la plus agressive ?
- Quel endpoint est le plus ciblé ?
- Voit-on une tentative d’énumération ?
- Voit-on une tentative d’injection ?
- Voit-on une compromission probable ou seulement du scan ?
- Quelle action de confinement serait prioritaire ?
- Quels logs supplémentaires voudrait-on avoir ?

---

## 21. Exemples Bash simples pour Kali Linux

Les contraintes projet indiquent souvent :

- première ligne exacte : #!/bin/bash,
- fichiers exécutables,
- pas de backticks,
- pas de &&,
- pas de ||,
- pas de point-virgule,
- usage de $1 sans guillemets selon l’énoncé.

Ces exemples sont pédagogiques et restent volontairement simples.

### 21.1 Repérer les codes d’erreur fréquents

```bash
#!/bin/bash
awk '{print $9}' $1 | sort | uniq -c | sort -nr
```

### 21.2 Repérer les IP les plus présentes

```bash
#!/bin/bash
awk '{print $1}' $1 | sort | uniq -c | sort -nr
```

### 21.3 Chercher des motifs d’attaque classiques

```bash
#!/bin/bash
grep -Ei 'union|select|sleep|\.\./|cmd=|/bin/sh|wget|curl|php' $1
```

### 21.4 Isoler les erreurs serveur

```bash
#!/bin/bash
awk '$9 ~ /^5/ {print}' $1
```

### 21.5 Trouver les requêtes vers des fichiers sensibles

```bash
#!/bin/bash
grep -Ei '\.env|\.git|admin|backup|config|phpinfo' $1
```

Ces scripts ne remplacent pas un SIEM, mais ils entraînent le regard analytique.

---

## 22. Erreurs fréquentes en réponse à incident web

- ne pas avoir de logs applicatifs exploitables,
- logguer trop peu ou trop tard,
- logguer des secrets en clair,
- confondre scan et compromission confirmée,
- éteindre immédiatement sans collecte minimale,
- supprimer l’artefact sans corriger la cause,
- restaurer trop vite une version toujours vulnérable,
- ne pas tourner les secrets après compromission probable,
- ne pas documenter les décisions,
- oublier la logique métier et se concentrer uniquement sur l’infra,
- ne pas faire de retour d’expérience.

---

## 23. Ce qu’il faut être capable d’expliquer simplement à l’oral

### Quelles sont les étapes de la réponse à incident web ?

Préparation, détection et analyse, confinement, éradication, récupération, puis retour d’expérience. Chaque étape sert à aller plus vite, réduire l’impact et éviter la répétition.

### Comment détecter vite une attaque web ?

En corrélant les logs applicatifs, les logs serveur, le WAF, l’EDR, les alertes cloud, les logs IAM et les signaux métier. Sans visibilité, on ne peut pas réagir vite.

### Comment contenir un incident ?

En coupant l’accès de l’attaquant sans casser inutilement le service : blocage IP, désactivation d’endpoint, révocation de session, isolement d’hôte, rotation de secrets.

### Comment éradiquer ?

En supprimant non seulement l’artefact malveillant, mais aussi la faille exploitée, la persistance et les accès compromis.

### Comment récupérer ?

En restaurant depuis un environnement propre, en validant le correctif, en tournant les secrets, en testant, puis en remettant progressivement en ligne avec surveillance renforcée.

### Quel est le rôle de l’automatisation ?

Elle accélère les tâches répétitives : corrélation, enrichissement, isolation, playbooks, révocation, timeline. Elle réduit le temps de réponse mais doit rester encadrée.

### Pourquoi documenter ?

Pour coordonner l’équipe, justifier les décisions, aider le juridique et la conformité, reconstituer la chronologie et améliorer la sécurité après l’incident.

### Pourquoi le post-mortem est-il important ?

Parce qu’il transforme l’incident en amélioration durable : meilleurs logs, meilleurs playbooks, meilleurs contrôles, meilleure détection, meilleure priorisation.

---

## 24. Conclusion générale

La réponse à incident rapide sur application web repose sur une idée centrale : la vitesse utile n’est possible que si la préparation a été sérieuse.

Une équipe rapide n’est pas une équipe qui improvise. C’est une équipe qui :

- connaît ses actifs,
- sait quels événements surveiller,
- dispose de logs de qualité,
- comprend le comportement attaquant,
- possède des playbooks,
- sait contenir proprement,
- sait éradiquer la cause racine,
- sait reconstruire un service sain,
- sait documenter et apprendre.

Dans la vraie vie, la performance d’une réponse à incident web dépend moins de l’outil miracle que de la qualité de l’observabilité, de la discipline de journalisation, de la compréhension technique et de la coordination humaine.

Retenir cela te donne déjà une base très solide :

- détecter tôt,
- qualifier vite,
- contenir sans casser inutilement,
- corriger réellement,
- remettre en ligne proprement,
- apprendre pour ne pas revivre la même crise.

---

## 25. Sources recommandées pour approfondir

### Références principales

- NIST SP 800-61 Rev. 3 — Incident Response Recommendations and Considerations for Cybersecurity Risk Management
- NIST SP 800-61 Rev. 2 — Computer Security Incident Handling Guide
- NIST Cybersecurity Framework 2.0
- CISA Cybersecurity Incident & Vulnerability Response Playbooks
- OWASP Logging Cheat Sheet
- OWASP Application Logging Vocabulary Cheat Sheet
- OWASP REST Security Cheat Sheet
- OWASP Vulnerability Management Guide
- OWASP Web Security Testing Guide
- OWASP Top 10 — Security Logging and Monitoring Failures
- MITRE ATT&CK — Exploit Public-Facing Application
- MITRE ATT&CK — Valid Accounts
- Microsoft Security — What is Incident Response?
- Microsoft Security — What is SOAR?
- Microsoft Security — What is EDR?
- Elastic — What is Log Monitoring?

### Liens utiles

- https://csrc.nist.gov/pubs/sp/800/61/r3/final
- https://csrc.nist.gov/pubs/sp/800/61/r2/final
- https://www.nist.gov/cyberframework
- https://www.cisa.gov/topics/cyber-threats-and-response/incident-response
- https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html
- https://cheatsheetseries.owasp.org/cheatsheets/Logging_Vocabulary_Cheat_Sheet.html
- https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html
- https://owasp.org/www-project-vulnerability-management-guide/
- https://owasp.org/www-project-web-security-testing-guide/
- https://owasp.org/Top10/2021/A09_2021-Security_Logging_and_Monitoring_Failures/
- https://attack.mitre.org/techniques/T1190/
- https://attack.mitre.org/techniques/T1078/
- https://www.microsoft.com/en-us/security/business/security-101/what-is-incident-response
- https://www.microsoft.com/en-us/security/business/security-101/what-is-soar
- https://www.microsoft.com/en-us/security/business/security-101/what-is-edr-endpoint-detection-response
- https://www.elastic.co/what-is/log-monitoring

---

## 26. Rappel projet

Le projet mentionne :

- Kali Linux,
- scripts bash exécutables,
- README.md obligatoire,
- première ligne exacte #!/bin/bash pour les scripts,
- pas de backticks, &&, || ou ; dans les scripts,
- usage de $1 selon l’énoncé,
- fichier logs.txt.

Quand tu travailles dessus, garde bien une séparation claire entre :

- le cours et la théorie,
- l’analyse de logs,
- les scripts bash demandés,
- le README de rendu final.

