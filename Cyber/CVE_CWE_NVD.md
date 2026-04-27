# CVE, CWE & NVD — Le socle de la gestion des vulnérabilités

> "On ne peut pas corriger ce que l’on ne peut pas nommer."

---

# Table des matières

1. Introduction  
2. Vue globale de l’écosystème des vulnérabilités  
3. CVE (Common Vulnerabilities and Exposures)  
4. CWE (Common Weakness Enumeration)  
5. NVD (National Vulnerability Database)  
6. CVSS (Common Vulnerability Scoring System)  a
7. Cycle de vie d’une vulnérabilité  
8. Lien CVE → exploitation réelle  
9. Associer une vulnérabilité à un CWE  
10. Utiliser le CVSS pour argumenter l’impact  
11. Bonnes pratiques  
12. ABAC  
13. Conclusion  

---

# 1. Introduction

Chaque jour, des vulnérabilités sont découvertes.

Sans standard :
- Pas de communication claire
- Pas de priorisation
- Pas d’automatisation

---

# 2. Vue globale

Faiblesse (CWE) → Vulnérabilité (CVE) → Score (CVSS via NVD)

---

# 3. CVE

## Définition
Identifiant unique d’une vulnérabilité.

Exemple :
CVE-2021-44228

---

# 4. CWE

## Définition
Faiblesse logicielle à l’origine d’une vulnérabilité.

Exemple :
CWE-79 → XSS

---

# 5. NVD

Base de données enrichissant les CVE :
- CVSS
- Impact
- Vecteurs

---

# 6. CVSS

Score de 0 à 10

- 0–3.9 → Low
- 4–6.9 → Medium
- 7–8.9 → High
- 9–10 → Critical

---

# 7. Cycle de vie

1. Faiblesse (CWE)
2. Découverte
3. CVE
4. NVD
5. CVSS
6. Patch

---

# 8. Lien CVE → exploitation réelle

Un CVE = une faille exploitable.

Exemple :
Log4Shell → RCE

Toujours :
- Comprendre
- Reproduire
- Exploiter

---

# 9. Associer CWE

Exemple :
XSS → CWE-79  
SQLi → CWE-89  

Toujours ajouter dans un rapport.

---

# 10. Utiliser CVSS

Permet de justifier l’impact.

Exemple :
- RCE → 9.8 (Critical)
- XSS → Medium

---

# 11. Bonnes pratiques

- Scanner
- Prioriser
- Patcher
- Automatiser

---

# 12. ABAC

Accès basé sur attributs :

user.role = admin AND location = interne

---

# 13. Conclusion

CVE = quoi  
CWE = pourquoi  
CVSS = gravité  
Exploit = comment

---

# Fin
