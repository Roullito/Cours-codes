# Cours complet — Nessus : scan de vulnérabilités et gestion des risques

> Module : **Nessus: Using Nessus for Security Vulnerability Scanning**  
> Objectif : comprendre, installer, configurer et utiliser Nessus pour identifier, analyser, prioriser et documenter des vulnérabilités de sécurité.

---

## 1. Introduction

Nessus est un scanner de vulnérabilités très utilisé en cybersécurité. Il permet d’analyser des machines, serveurs, applications web, services réseau, configurations système et équipements afin d’identifier des failles connues, de mauvaises configurations ou des expositions dangereuses.

Dans un contexte professionnel, Nessus ne sert pas uniquement à “trouver des failles”. Il s’intègre dans une démarche plus large appelée **vulnerability management**, c’est-à-dire la gestion continue des vulnérabilités.

L’objectif n’est pas seulement de scanner, mais de :

- découvrir les actifs exposés ;
- identifier les vulnérabilités ;
- évaluer leur gravité ;
- prioriser les corrections ;
- suivre leur remédiation ;
- produire des rapports compréhensibles pour les équipes techniques et non techniques.

---

## 2. Les fondamentaux de la gestion des vulnérabilités

### 2.1 Définition

La gestion des vulnérabilités est un processus continu visant à identifier, classifier, prioriser, corriger et suivre les faiblesses de sécurité présentes dans un système d’information.

Une vulnérabilité peut être :

- une version logicielle obsolète ;
- une mauvaise configuration ;
- un service exposé inutilement ;
- un mot de passe faible ;
- une faille applicative ;
- une absence de chiffrement ;
- une permission excessive ;
- un composant vulnérable connu via une CVE.

### 2.2 Cycle de gestion des vulnérabilités

Le cycle classique est le suivant :

1. **Inventaire des actifs**  
   Identifier les machines, services, applications, domaines, IP et équipements à protéger.

2. **Scan et détection**  
   Utiliser un outil comme Nessus pour repérer les vulnérabilités.

3. **Analyse des résultats**  
   Lire les détails techniques, comprendre le risque réel et éliminer les faux positifs.

4. **Priorisation**  
   Classer les vulnérabilités selon leur gravité, leur exploitabilité et le contexte métier.

5. **Remédiation**  
   Corriger les problèmes : patch, configuration, désactivation de service, durcissement, filtrage réseau.

6. **Validation**  
   Relancer un scan pour confirmer que la vulnérabilité est corrigée.

7. **Reporting**  
   Produire un rapport clair avec les preuves, impacts et recommandations.

### 2.3 Différence entre scan de vulnérabilités et pentest

Un **scan de vulnérabilités** est généralement automatisé. Il identifie des failles potentielles à partir de signatures, de tests connus et de mauvaises configurations.

Un **test d’intrusion** est plus manuel et plus approfondi. Il vise à démontrer l’impact réel d’une vulnérabilité, souvent en chaînant plusieurs faiblesses.

Nessus est donc un outil très utile, mais ses résultats doivent toujours être interprétés par un humain.

---

## 3. Rôle du scan de vulnérabilités

Le scan de vulnérabilités joue un rôle central dans la sécurité défensive.

Il permet de :

- détecter rapidement des failles connues ;
- vérifier l’état de patching des systèmes ;
- identifier des services exposés ;
- contrôler la conformité de certaines configurations ;
- repérer les certificats expirés ou faibles ;
- détecter des versions logicielles vulnérables ;
- fournir une base de travail aux équipes sécurité et infrastructure.

Cependant, un scan ne remplace pas :

- une revue de code ;
- un audit de configuration manuel ;
- un pentest applicatif complet ;
- une analyse de logique métier ;
- une surveillance continue des journaux.

---

## 4. Présentation de Nessus

### 4.1 Qu’est-ce que Nessus ?

Nessus est un scanner de vulnérabilités développé par Tenable. Il utilise des plugins pour tester des milliers de vulnérabilités connues sur différents systèmes et services.

Nessus peut scanner :

- des serveurs Linux ;
- des serveurs Windows ;
- des applications web ;
- des équipements réseau ;
- des bases de données ;
- des services exposés ;
- des configurations SSL/TLS ;
- des environnements cloud selon les versions et intégrations.

### 4.2 Nessus Essentials

Nessus Essentials est la version gratuite destinée à l’apprentissage, aux étudiants et aux usages personnels limités. Elle permet de scanner un nombre limité d’adresses IP, ce qui est suffisant pour des labs, des machines locales ou des exercices pédagogiques.

### 4.3 Plugins Nessus

Les plugins sont le cœur de Nessus. Chaque plugin correspond à un test spécifique : détection de version, faille CVE, mauvaise configuration, service dangereux, certificat faible, etc.

Un plugin contient généralement :

- un identifiant ;
- un nom ;
- une description ;
- une famille ;
- une sévérité ;
- une méthode de détection ;
- une solution recommandée ;
- des références comme CVE, CWE, CVSS, bulletins éditeurs.

---

## 5. Installation de Nessus

### 5.1 Installation sur Linux Debian/Ubuntu/Kali

1. Télécharger le paquet `.deb` depuis le site officiel Tenable.
2. Installer le paquet :

```bash
sudo dpkg -i Nessus-*.deb
```

3. Démarrer le service :

```bash
sudo systemctl start nessusd
```

4. Activer le démarrage automatique :

```bash
sudo systemctl enable nessusd
```

5. Accéder à l’interface web :

```text
https://localhost:8834
```

Le navigateur peut afficher un avertissement de certificat, car Nessus utilise souvent un certificat local auto-signé.

### 5.2 Installation sur Windows

1. Télécharger l’installateur Windows depuis Tenable.
2. Lancer le fichier `.msi`.
3. Suivre l’assistant d’installation.
4. Ouvrir l’interface via :

```text
https://localhost:8834
```

5. Créer le compte administrateur local et activer Nessus avec le code reçu par mail.

### 5.3 Installation sur macOS

1. Télécharger le paquet macOS officiel.
2. Installer l’application.
3. Lancer le service Nessus.
4. Accéder à :

```text
https://localhost:8834
```

### 5.4 Première configuration

Lors du premier lancement :

1. Choisir **Nessus Essentials**.
2. Renseigner la clé d’activation.
3. Créer un compte administrateur local.
4. Attendre le téléchargement et la compilation des plugins.

Cette étape peut prendre plusieurs minutes.

---

## 6. Configuration optimale de Nessus

### 6.1 Performances

Un scan peut consommer beaucoup de ressources. Il faut adapter Nessus à la machine et au réseau.

Paramètres importants :

- nombre maximum de scans simultanés ;
- nombre maximum d’hôtes scannés en parallèle ;
- nombre de checks simultanés par hôte ;
- timeout réseau ;
- intensité du scan ;
- scan authentifié ou non authentifié.

### 6.2 Scan agressif vs scan prudent

Un scan trop agressif peut :

- ralentir un serveur ;
- déclencher des alertes IDS/IPS ;
- provoquer des erreurs applicatives ;
- perturber un service fragile.

Sur une cible de production, il faut privilégier :

- une intensité modérée ;
- une fenêtre horaire adaptée ;
- une autorisation écrite ;
- des exclusions si nécessaire ;
- l’absence de tests destructifs.

### 6.3 Scans authentifiés

Un scan authentifié utilise des identifiants valides pour se connecter à la machine cible.

Avantages :

- meilleure précision ;
- moins de faux positifs ;
- détection des logiciels installés localement ;
- analyse des patchs manquants ;
- visibilité sur la configuration interne.

Exemples :

- SSH pour Linux ;
- WinRM/SMB pour Windows ;
- credentials web pour certaines applications ;
- SNMP pour équipements réseau.

Un scan non authentifié voit surtout ce qui est exposé depuis l’extérieur. Un scan authentifié voit beaucoup plus profondément.

---

## 7. Interface utilisateur de Nessus

### 7.1 Dashboard

Le tableau de bord permet de visualiser :

- les scans récents ;
- leur état ;
- les vulnérabilités détectées ;
- les niveaux de sévérité ;
- l’historique des scans.

### 7.2 Menus principaux

Les menus peuvent varier selon la version, mais on retrouve généralement :

- **Scans** : création, lancement, gestion des scans ;
- **Policies** : création de politiques personnalisées ;
- **Plugin Rules** : gestion de règles sur les plugins ;
- **Settings** : configuration générale ;
- **User Management** : gestion des utilisateurs locaux ;
- **Reports / Export** : exportation des résultats.

### 7.3 Vue d’un scan

Une fois un scan terminé, Nessus affiche :

- la liste des hôtes scannés ;
- le nombre de vulnérabilités par sévérité ;
- les ports et services identifiés ;
- les vulnérabilités groupées par plugin ;
- les détails techniques et recommandations.

---

## 8. Création et gestion d’un scan

### 8.1 Choix du template

Nessus propose plusieurs modèles de scan, par exemple :

- **Basic Network Scan** ;
- **Advanced Scan** ;
- **Web Application Tests** ;
- **Credentialed Patch Audit** ;
- **Malware Scan** ;
- **Host Discovery**.

Le choix dépend de l’objectif.

Pour scanner un site web, le template **Web Application Tests** est pertinent car il active des tests orientés HTTP/HTTPS, applications web, formulaires, headers, configuration TLS et failles web connues.

### 8.2 Création d’un scan simple

Étapes générales :

1. Aller dans **Scans**.
2. Cliquer sur **New Scan**.
3. Choisir un template.
4. Donner un nom au scan.
5. Ajouter la cible : IP, domaine ou URL selon le cas.
6. Configurer les options.
7. Sauvegarder.
8. Lancer le scan.

### 8.3 Cibles

Exemples de cibles :

```text
192.168.1.10
example.com
https://example.com
10.10.10.0/24
```

Attention : scanner un domaine ou une IP sans autorisation peut être illégal. Toujours travailler dans un cadre autorisé.

### 8.4 Planification des scans

Nessus permet de programmer des scans :

- une fois ;
- quotidiennement ;
- chaque semaine ;
- chaque mois ;
- à une heure précise.

La planification est utile pour suivre l’évolution d’un périmètre dans le temps.

---

## 9. Scan policies et templates

### 9.1 Qu’est-ce qu’une policy ?

Une policy est une configuration réutilisable de scan.

Elle peut contenir :

- les ports à scanner ;
- les familles de plugins activées ;
- les paramètres de performance ;
- les credentials ;
- les exclusions ;
- les options web ;
- les paramètres de détection de services.

### 9.2 Pourquoi créer une policy personnalisée ?

Créer une policy permet de standardiser les scans.

Exemples :

- policy pour serveurs Linux ;
- policy pour serveurs Windows ;
- policy pour applications web ;
- policy douce pour production ;
- policy agressive pour lab ;
- policy authentifiée pour audit interne.

### 9.3 Paramètres importants

#### Discovery

Détermine comment Nessus découvre les hôtes et services.

#### Port Scanning

Permet de choisir les ports à analyser :

- ports communs ;
- tous les ports TCP ;
- ports personnalisés ;
- UDP si nécessaire.

#### Assessment

Configure les tests de vulnérabilités.

#### Credentials

Permet d’ajouter des identifiants pour scan authentifié.

#### Web Applications

Permet d’activer certains tests web comme :

- crawling ;
- tests de formulaires ;
- détection de headers ;
- analyse SSL/TLS ;
- détection de technologies web.

---

## 10. Analyse et interprétation des résultats

### 10.1 Niveaux de sévérité

Nessus classe les résultats par sévérité :

- **Critical** : risque très élevé, exploitation probable ou impact majeur ;
- **High** : risque élevé nécessitant une correction rapide ;
- **Medium** : risque modéré, dépend souvent du contexte ;
- **Low** : risque faible ;
- **Info** : information utile, pas forcément une vulnérabilité.

### 10.2 CVSS

Le score CVSS aide à évaluer la gravité technique d’une vulnérabilité.

Il prend en compte des critères comme :

- vecteur d’attaque ;
- complexité ;
- privilèges requis ;
- interaction utilisateur ;
- impact sur confidentialité ;
- impact sur intégrité ;
- impact sur disponibilité.

Un score CVSS élevé ne signifie pas toujours que la vulnérabilité est prioritaire dans ton contexte. Il faut aussi regarder l’exposition réelle et la criticité de l’actif.

### 10.3 Lire une vulnérabilité Nessus

Une fiche Nessus contient souvent :

- le nom de la vulnérabilité ;
- la sévérité ;
- la description ;
- la preuve ou sortie du plugin ;
- le port concerné ;
- le protocole ;
- la solution ;
- les références ;
- les CVE associées ;
- le score CVSS ;
- la date de publication et de mise à jour du plugin.

### 10.4 Faux positifs

Un faux positif est un résultat signalé comme vulnérable alors que la vulnérabilité n’est pas réellement exploitable ou présente.

Causes possibles :

- bannière de version incorrecte ;
- service masquant sa vraie version ;
- patch backporté par une distribution Linux ;
- détection incomplète ;
- configuration spécifique réduisant l’impact.

Il faut toujours vérifier les résultats importants.

---

## 11. Types de vulnérabilités détectées

### 11.1 Vulnérabilités réseau

Exemples :

- ports inutiles exposés ;
- services obsolètes ;
- protocoles faibles ;
- absence de filtrage ;
- services d’administration accessibles publiquement.

### 11.2 Vulnérabilités système

Exemples :

- patchs manquants ;
- kernel vulnérable ;
- services système mal configurés ;
- comptes faibles ;
- permissions dangereuses.

### 11.3 Vulnérabilités web

Exemples :

- headers de sécurité manquants ;
- TLS faible ;
- informations exposées ;
- versions de CMS vulnérables ;
- répertoires listables ;
- formulaires potentiellement vulnérables.

### 11.4 Vulnérabilités SSL/TLS

Exemples :

- certificat expiré ;
- certificat auto-signé ;
- protocoles obsolètes ;
- suites cryptographiques faibles ;
- mauvaise chaîne de certification.

### 11.5 Mauvaises configurations

Exemples :

- accès anonyme autorisé ;
- comptes par défaut ;
- interfaces d’administration exposées ;
- permissions trop larges ;
- absence de journalisation.

---

## 12. Génération de rapports

### 12.1 Formats disponibles

Nessus permet généralement d’exporter les résultats en plusieurs formats :

- PDF ;
- HTML ;
- CSV ;
- Nessus DB / `.nessus` ;
- XML selon les versions.

### 12.2 Rapport exécutif vs rapport technique

Un rapport exécutif est destiné aux managers ou décideurs. Il doit être clair, synthétique et orienté risque.

Un rapport technique est destiné aux administrateurs, développeurs ou analystes sécurité. Il doit contenir les détails nécessaires à la reproduction et à la correction.

### 12.3 Structure recommandée d’un rapport

1. Titre
2. Contexte
3. Périmètre
4. Méthodologie
5. Configuration du scan
6. Résumé exécutif
7. Statistiques globales
8. Vulnérabilités critiques et élevées
9. Analyse détaillée d’une vulnérabilité
10. Recommandations
11. Plan de remédiation
12. Annexes et captures d’écran

---

## 13. Priorisation et remédiation

### 13.1 Critères de priorisation

Ne pas corriger uniquement selon le score CVSS. Il faut combiner plusieurs critères :

- sévérité Nessus ;
- exposition Internet ;
- exploit public disponible ;
- criticité de l’actif ;
- présence de données sensibles ;
- facilité de correction ;
- impact métier ;
- présence de contrôles compensatoires ;
- exploitation active connue.

### 13.2 Plan de remédiation

Pour chaque vulnérabilité importante, définir :

- responsable ;
- action corrective ;
- priorité ;
- délai ;
- risque si non corrigé ;
- méthode de vérification.

### 13.3 Exemples de remédiation

| Problème | Remédiation |
|---|---|
| Version obsolète | Mettre à jour le logiciel |
| Port inutile exposé | Fermer le port ou filtrer par firewall |
| Certificat expiré | Renouveler le certificat |
| TLS faible | Désactiver les protocoles obsolètes |
| Header manquant | Configurer le serveur web |
| Service admin public | Restreindre par VPN/IP allowlist |

---

## 14. Méthodologie pratique pour l’exercice

### 14.1 Objectif de l’exercice

L’objectif est de scanner un site autorisé avec Nessus Essentials, d’analyser les résultats, puis de produire un rapport complet avec captures d’écran.

Sites proposés par l’exercice :

- hellboundhackers
- hackaday
- catb.org
- hacklang.org
- hacker.org

Avant de scanner un site externe, il faut vérifier que l’exercice l’autorise explicitement et rester sur une configuration prudente.

### 14.2 Choix du template Web Application Tests

Le template **Web Application Tests** est adapté parce que l’objectif est de scanner un site web.

Il permet d’analyser :

- les services HTTP/HTTPS ;
- les configurations SSL/TLS ;
- certains headers de sécurité ;
- les technologies web détectables ;
- les formulaires et chemins accessibles ;
- certaines vulnérabilités web connues.

Ce template est plus pertinent qu’un simple scan réseau si la cible principale est une application web.

### 14.3 Considérations de configuration

Lors de la configuration, il faut prendre en compte :

- l’autorisation de scanner ;
- le domaine exact ;
- la profondeur du scan ;
- l’intensité du scan ;
- le risque de perturber le site ;
- l’exclusion des tests destructifs ;
- la durée estimée ;
- le respect du périmètre.

Pour un site externe, il est recommandé de rester sur un scan modéré et non destructif.

---

## 15. Lancement et suivi du scan

### 15.1 Lancer le scan

Processus :

1. Ouvrir Nessus.
2. Aller dans **Scans**.
3. Cliquer sur le scan configuré.
4. Vérifier la cible.
5. Cliquer sur **Launch**.
6. Attendre le démarrage du scan.

### 15.2 Surveiller la progression

Pendant le scan, vérifier :

- le pourcentage d’avancement ;
- les hôtes détectés ;
- les éventuels messages d’erreur ;
- la durée ;
- la consommation CPU/RAM de la machine Nessus ;
- le comportement de la cible si elle est sous ton contrôle.

Si le scan semble trop agressif ou provoque des erreurs, il faut l’arrêter et revoir la configuration.

---

## 16. Analyse des résultats de l’exercice

### 16.1 Nombre de vulnérabilités

Dans ton rapport, ajoute une capture d’écran montrant :

- le nombre total de vulnérabilités ;
- le nombre de Critical ;
- le nombre de High ;
- le nombre de Medium ;
- le nombre de Low ;
- le nombre d’Info.

Exemple à compléter :

```text
Total vulnerabilities identified: XX
Critical: X
High: X
Medium: X
Low: X
Info: X
```

### 16.2 Résumé des vulnérabilités critiques et élevées

Pour chaque vulnérabilité Critical ou High :

```text
Name:
Severity:
Affected host:
Affected port/service:
Description:
Potential impact:
Recommended remediation:
Evidence:
```

### 16.3 Analyse détaillée d’une vulnérabilité

Choisis une vulnérabilité importante et détaille-la.

Exemple de structure :

```text
Vulnerability name:
Severity:
CVSS score:
Affected component:
Description:
Technical details:
Potential impact:
Attack scenario:
Remediation steps:
Validation method:
References:
```

---

## 17. Template de rapport pour la tâche 0

Tu peux utiliser directement cette structure pour ton rendu.

```markdown
# Nessus Vulnerability Scan Report

## 1. Introduction

This report presents the results of a vulnerability scan performed with Nessus Essentials against the target website: `[TARGET]`.

The objective was to identify potential vulnerabilities, analyze their severity, and propose remediation actions.

## 2. Scope

- Target: `[DOMAIN OR URL]`
- Tool: Nessus Essentials
- Scan template: Web Application Tests
- Date of scan: `[DATE]`
- Scanner environment: `[OS / VM / Network]`

## 3. Scan Configuration

### Why Web Application Tests?

The Web Application Tests template was selected because the target is a website. This template is designed to assess HTTP/HTTPS services, web technologies, SSL/TLS configuration, headers, forms, and common web-related vulnerabilities.

### Configuration considerations

The scan was configured carefully to avoid aggressive or destructive testing. The target scope was limited to the authorized website, and the scan intensity was kept reasonable to reduce the risk of service disruption.

Screenshots:

- Scan template selection
- Target configuration
- Scan settings

## 4. Scan Execution

The scan was launched from the Nessus dashboard by selecting the configured scan and clicking the Launch button.

During execution, the progress was monitored through the Nessus interface. I checked the scan status, progress percentage, discovered hosts, and potential errors to ensure that the scan was running smoothly.

Screenshots:

- Scan launch
- Scan running
- Scan completed

## 5. Results Summary

Total vulnerabilities identified: `[NUMBER]`

| Severity | Count |
|---|---:|
| Critical | X |
| High | X |
| Medium | X |
| Low | X |
| Info | X |

Screenshot:

- Vulnerability summary dashboard

## 6. Critical and High Severity Findings

### Finding 1

- Name:
- Severity:
- Affected host:
- Port/service:
- Description:
- Potential impact:
- Remediation:

### Finding 2

- Name:
- Severity:
- Affected host:
- Port/service:
- Description:
- Potential impact:
- Remediation:

## 7. Detailed Vulnerability Analysis

### Selected vulnerability

- Name:
- Severity:
- CVSS score:
- Affected component:
- Description:
- Evidence:
- Potential impact:
- Attack scenario:
- Remediation steps:
- Validation method:

## 8. Remediation Plan

| Priority | Vulnerability | Action | Responsible team | Deadline |
|---|---|---|---|---|
| P1 |  |  |  |  |
| P2 |  |  |  |  |
| P3 |  |  |  |  |

## 9. Conclusion

The Nessus scan helped identify potential weaknesses in the target website. The most important findings should be reviewed manually, prioritized based on their severity and business context, and remediated according to the proposed plan.

## 10. Appendices

- Nessus exported report
- Screenshots of scan setup
- Screenshots of scan execution
- Screenshots of results
```

---

## 18. Bonnes pratiques Nessus

### 18.1 Avant le scan

- Obtenir une autorisation claire.
- Définir le périmètre.
- Identifier les contraintes de production.
- Prévenir les équipes concernées.
- Choisir un template adapté.
- Vérifier les exclusions.

### 18.2 Pendant le scan

- Surveiller l’avancement.
- Vérifier les erreurs.
- Observer la charge locale.
- Stopper le scan en cas de comportement anormal.

### 18.3 Après le scan

- Exporter le rapport.
- Analyser les vulnérabilités importantes.
- Vérifier les faux positifs.
- Prioriser les corrections.
- Relancer un scan de validation.
- Archiver les preuves.

---

## 19. Limites de Nessus

Nessus est puissant, mais il a des limites.

Il peut manquer :

- des failles de logique métier ;
- des IDOR complexes ;
- des vulnérabilités nécessitant un scénario utilisateur avancé ;
- des failles dépendantes d’un contexte applicatif ;
- des chaînes d’exploitation complexes ;
- des problèmes invisibles sans authentification.

Il peut aussi produire :

- des faux positifs ;
- des résultats trop nombreux ;
- des alertes de faible importance ;
- des conclusions nécessitant une validation manuelle.

---

## 20. Exemple de conclusion professionnelle

Nessus est un outil essentiel dans une démarche de gestion des vulnérabilités. Il permet d’automatiser la détection de nombreuses faiblesses connues et de produire une vision globale du niveau d’exposition d’un système.

Cependant, la valeur réelle de Nessus dépend de la qualité de la configuration, de l’interprétation humaine des résultats et de la capacité à transformer les findings en actions concrètes de remédiation.

Un bon analyste ne se contente pas de lire le score CVSS. Il replace chaque vulnérabilité dans son contexte : exposition, criticité de l’actif, exploitabilité, impact métier et facilité de correction.

Dans un environnement professionnel, Nessus doit donc être utilisé comme un outil de détection, de priorisation et de suivi, intégré dans un processus continu de sécurité.

---

## 21. Checklist rapide pour ton rendu

- [ ] Nessus installé et activé
- [ ] Template Web Application Tests choisi
- [ ] Cible correctement définie
- [ ] Scan configuré prudemment
- [ ] Scan lancé
- [ ] Progression surveillée
- [ ] Résultats exportés
- [ ] Captures d’écran ajoutées
- [ ] Nombre de vulnérabilités indiqué
- [ ] Critical/High résumées
- [ ] Une vulnérabilité analysée en détail
- [ ] Impact expliqué
- [ ] Remédiation proposée
- [ ] Rapport final structuré

---

## 22. Glossaire

**Asset** : actif informatique à protéger, comme un serveur, une application ou une base de données.  
**CVE** : identifiant public d’une vulnérabilité connue.  
**CVSS** : système de score permettant d’évaluer la gravité technique d’une vulnérabilité.  
**False positive** : résultat signalé comme vulnérable alors qu’il ne l’est pas réellement.  
**Plugin** : test Nessus permettant de détecter une vulnérabilité ou une configuration spécifique.  
**Policy** : configuration réutilisable de scan.  
**Remediation** : action corrective permettant de réduire ou supprimer une vulnérabilité.  
**Scan authentifié** : scan utilisant des identifiants pour obtenir une meilleure visibilité interne.  
**Severity** : niveau de gravité attribué à une vulnérabilité.  
**Vulnerability management** : processus continu d’identification, priorisation et correction des vulnérabilités.

