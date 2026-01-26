# Burp Suite – Fundamentals  
🎓 Holberton School | Cybersecurity  
🧠 Niveau : Débutant → Intermédiaire  
🖥️ Environnement : Kali Linux  
🎯 Cible : https://web0x02.hbtn  

---

## 1️⃣ Qu’est-ce que Burp Suite ?

**Burp Suite** est une plateforme complète de **test de sécurité des applications web**.  
Il agit comme un **proxy d’interception** entre ton navigateur et l’application cible.

👉 Il te permet de :
- Voir toutes les requêtes HTTP / HTTPS
- Les modifier en temps réel
- Les rejouer, automatiser, analyser
- Détecter des vulnérabilités OWASP Top 10

🧠 **Idée clé**  
> Si tu ne vois pas les requêtes HTTP, tu testes à l’aveugle.

---

## 2️⃣ Architecture HTTP (fondamental à comprendre)
Navigateur ───▶ Burp Proxy ───▶ Serveur Web Navigateur ◀─── Burp Proxy ◀─── Serveur Web
Copier le code

Burp devient **l’homme du milieu volontaire** (MITM contrôlé).

---

## 3️⃣ Mise en place du Proxy Burp

### 📌 Étapes
1. Lancer Burp Suite (`burpsuite`)
2. Onglet **Proxy → Intercept**
3. Configurer le navigateur :
   - Proxy HTTP : `127.0.0.1`
   - Port : `8080`

### 🔐 HTTPS
- Installer le certificat Burp :
  - `http://burp`
  - Exporter le certificat
  - L’ajouter aux autorités de confiance du navigateur

🎯 Sans certificat → HTTPS illisible

---

## 4️⃣ Les composants principaux de Burp Suite

### 🧩 Proxy
- Interception des requêtes
- Modification en temps réel
- Historique HTTP complet

### 🧩 Target
- Cartographie du site
- Arborescence des endpoints
- Paramètres observés

### 🧩 Spider / Crawler
- Exploration automatique du site
- Découverte de pages cachées
- Basé sur liens, formulaires, JS

⚠️ Attention : peut générer beaucoup de bruit

---

## 5️⃣ Repeater – Le cœur du pentest manuel

### 🎯 Objectif
Tester **une requête précise**, autant de fois que nécessaire.

### Cas typiques
- SQL Injection
- XSS
- Bypass d’authentification
- Manipulation de paramètres

### Méthode
1. Intercepter une requête
2. `Send to Repeater`
3. Modifier un paramètre
4. Observer la réponse

🧠 Ici, **tu penses comme un attaquant**

---

## 6️⃣ Intruder – Attaques automatisées

### 🎯 Objectif
Tester **des variations massives** d’une requête.

### Cas d’usage
- Bruteforce login
- Fuzzing de paramètres
- Tests d’IDOR
- Enumération

### Étapes
1. Envoyer une requête à Intruder
2. Définir les positions (`§`)
3. Choisir un type d’attaque
4. Charger des payloads
5. Lancer l’attaque

⚠️ Intruder Community = limité mais suffisant pour apprendre

---

## 7️⃣ Burp Scanner

### 🧠 Scanner = automatisation
- Détecte des vulnérabilités connues
- Génère des rapports

### Exemples détectés
- XSS
- SQL Injection
- Headers manquants
- Mauvaises configs TLS

⚠️ Ne jamais faire confiance à 100 %
➡️ Toujours **valider manuellement**

---

## 8️⃣ Lecture et interprétation des résultats

Chaque issue contient :
- Description
- Impact
- Preuve
- Requête concernée
- Recommandation

🧠 En tant que pentester :
- Tu comprends **le pourquoi**
- Tu expliques **le risque**
- Tu proposes **la correction**

---

## 9️⃣ Vulnérabilités courantes détectables

| Vulnérabilité | Module |
|---------------|--------|
| XSS | Repeater / Scanner |
| SQLi | Repeater / Intruder |
| CSRF | Proxy |
| IDOR | Intruder |
| Headers manquants | Scanner |
| Auth faible | Proxy / Intruder |

---

## 🔟 Méthodologie type sur https://web0x02.hbtn

1. Configurer Proxy + HTTPS
2. Naviguer sur l’application
3. Observer Target / HTTP History
4. Spider si autorisé
5. Tester manuellement avec Repeater
6. Automatiser avec Intruder
7. Scanner en complément
8. Documenter

---

## 🧠 Conclusion

Burp Suite n’est PAS un bouton magique.

👉 C’est :
- Un **amplificateur de compréhension**
- Un **outil de précision**
- Un **compagnon du raisonnement sécurité**

> Le cerveau fait 80 %, Burp fait le reste.

---

## 📚 Références
- Documentation officielle Burp
- OWASP Testing Guide
- OWASP Top 10
🔥 Prochaine étape (je te propose)
Exercices guidés sur web0x02.hbtn
Scénarios :
XSS stockée
IDOR
Bypass auth
Fiches “cheat-sheet Burp”
Comparaison Burp vs ZAP
Lien Burp ↔ Wireshark ↔ Nmap
👉 Dis-moi ce que tu veux faire en pratique maintenant 💣