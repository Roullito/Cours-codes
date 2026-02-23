# Mandatory Access Control (MAC) sous Linux — Cours ultra détaillé (SELinux & AppArmor)

> Objectif : comprendre **MAC** en profondeur (concepts, modèles, labels, politiques), être capable d’expliquer **SELinux** et **AppArmor**, administrer et **dépanner** un système MAC, et maîtriser les commandes usuelles (`sestatus`, `getenforce`, `semanage`, `restorecon`, `audit2why`, `aa-status`, etc.).

---

## 0) Avant de commencer : pourquoi MAC existe ?

Sur un Linux “classique”, l’accès aux fichiers/processus est majoritairement gouverné par le modèle **DAC** (Discretionary Access Control) :
- le **propriétaire** d’un fichier décide (via `chmod`, `chown`, ACL) qui peut lire/écrire/exécuter,
- *donc* un utilisateur (ou un processus) peut parfois **élargir** l’accès (volontairement ou par erreur).

Problème : en sécurité, ce modèle seul est fragile face à :
- des **failles applicatives** (RCE, injection, upload vulnérable…),
- des **mauvaises permissions**,
- des **comptes compromis**,
- des escalades via des services qui tournent avec trop de droits.

👉 **MAC** ajoute une couche : *même si le DAC dit “oui”, le MAC peut dire “non”* (et il le fait selon une **politique centrale**).

---

## 1) Définitions : DAC vs MAC vs RBAC

### 1.1 DAC (Discretionary Access Control)
- Basé sur : identité (UID/GID), permissions `rwx`, ACL.
- Contrôle “discrétionnaire” : l’owner peut modifier la politique d’accès sur ses objets.
- Avantage : simple.
- Limite : si un process s’exécute avec les droits d’un utilisateur, il hérite de sa capacité à accéder aux objets autorisés au DAC.

### 1.2 MAC (Mandatory Access Control)
- **Politique imposée par le système** (administrateur / autorité centrale).
- Les sujets (processus) et objets (fichiers, sockets, ports…) portent des **labels**.
- L’accès est décidé par des règles de politique : **pas modifiable librement par l’utilisateur**.
- Exemple mental : “même si tu *peux* ouvrir la porte (DAC), un badge (MAC) peut te bloquer car tu n’as pas le bon niveau.”

### 1.3 RBAC (Role-Based Access Control)
- Un modèle (souvent utilisé dans/avec MAC) où les permissions sont liées à des **rôles** plutôt qu’à des utilisateurs.
- Important pour l’admin (séparation des tâches) : ex. rôle “webadmin”, “dbadmin”, “secadmin”.

👉 SELinux combine très fortement **TE** (Type Enforcement) + **RBAC** et peut aussi faire du **MLS/MCS** (niveau/catégories).

---

## 2) MAC dans Linux : la pièce maîtresse = LSM

Linux fournit un framework : **LSM (Linux Security Modules)**.
- C’est le “crochet” dans le kernel qui permet à un module de sécurité d’intercepter des opérations (open, exec, bind, connect, etc.).
- SELinux et AppArmor s’appuient sur LSM.

Conséquence :
- MAC est **enforcement côté kernel** (pas juste une “bonne pratique userland”).
- Les décisions sont prises sur des **hooks** critiques → donc plus robuste.

---

## 3) Principe fondamental : “Policy + Labels”

### 3.1 La politique (policy)
Un système MAC repose sur une **policy** :
- un ensemble de règles : “ce type de processus peut faire X sur ce type d’objet”,
- compilées/chargées dans le système,
- appliquées par le kernel via LSM.

Sans policy :
- les labels ne servent à rien,
- ou tout est “unconfined” (pas confiné).

### 3.2 Les labels
Les labels attachent de la **sémantique** de sécurité :
- fichier : “c’est un contenu web”, “c’est un binaire system”, “c’est une clé SSH”…
- processus : “c’est nginx”, “c’est sshd”, “c’est mysql”…

Ensuite la policy dit :
- “nginx (type A) peut lire contenu web (type B), mais pas lire /etc/shadow (type C)”.

---

## 4) SELinux : comment ça marche (vraiment)

### 4.1 SELinux en 3 phrases
- SELinux = MAC basé sur **labels** + **type enforcement**, renforcé par RBAC et MLS/MCS.
- Chaque processus et objet porte un **contexte SELinux**.
- Les décisions passent par **AVC (Access Vector Cache)** : “autorisé/refusé”, et tout refus est loggable (audit).

### 4.2 Les modes SELinux
- **Enforcing** : applique la policy (blocage réel).
- **Permissive** : n’applique pas (autorise), mais **log** ce qui aurait été bloqué.
- **Disabled** : SELinux désactivé (souvent déconseillé en prod).

Commandes :
- `getenforce` → Enforcing/Permissive/Disabled
- `setenforce 0|1` → bascule temporaire (permissive/enforcing)
- `sestatus` → état complet (policy, modes, booleans, etc.)

### 4.3 Le contexte SELinux (label) : `user:role:type:range`
Exemple :
- `system_u:system_r:httpd_t:s0`
- `unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023` (selon distro)

Champs :
1. **SELinux user** : “identité SELinux” (peut borner rôles/levels).
2. **role** : utile pour RBAC (ex. `sysadm_r`).
3. **type** : le champ **le plus important** (TE).
4. **range / level / categories** : MLS/MCS (sensibilité/catégories).

🧠 Mémo : en pratique admin, on se concentre souvent sur le **type** et le contexte des fichiers/process.

### 4.4 Type Enforcement (TE) = le cœur
SELinux TE = on associe :
- un **type** aux processus (souvent appelé *domain*),
- un **type** aux objets (fichiers, ports, sockets…).

Policy TE typique :
- `httpd_t` peut lire `httpd_sys_content_t`
- `httpd_t` ne peut pas lire `shadow_t`
- `sshd_t` peut lire `ssh_home_t` (selon policy)
- etc.

### 4.5 RBAC (dans SELinux)
RBAC contrôle *quels domains* un utilisateur peut adopter via un rôle.
- Exemple classique : l’utilisateur login dans un rôle “user_r”, qui n’a pas accès aux domains d’administration.
- Pour administrer, il bascule dans `sysadm_r` via `sudo`/`su` (selon config) et obtient davantage de droits.

### 4.6 MLS / MCS (niveau et catégories)
- **MLS (Multi-Level Security)** : niveaux de sensibilité (secret/top secret). Basé sur “dominance” et règles “no read up / no write down” (selon modèle).
- **MCS (Multi-Category Security)** : catégories (compartiments), souvent en environnements multi-tenant.

Sur desktop/serveur “classique”, MLS complet est souvent peu utilisé, mais **MCS** apparaît :
- conteneurs,
- isolation multi-projets,
- etc.

---

## 5) Observer les labels SELinux

### 5.1 Voir les contextes
- Fichiers : `ls -Z /chemin`
- Process : `ps -eZ | head`
- Contexte de l’utilisateur : `id -Z`

### 5.2 Contextes “typiques” utiles
- contenus web : `httpd_sys_content_t`
- fichiers config sensibles : `etc_t`, `shadow_t`
- ports web : `http_port_t`
- home user : `user_home_t`, `ssh_home_t` (selon distro/policy)

---

## 6) Gérer SELinux (commandes indispensables)

### 6.1 Statut / bascules
- `sestatus`
- `getenforce`
- `setenforce 0|1` (temporaire)

⚠️ La désactivation permanente se fait via config (ex. `/etc/selinux/config`) et reboot, mais en sécurité on préfère **corriger la policy/labels** plutôt que désactiver.

### 6.2 Booleans : “interrupteurs” de policy
Les booleans sont des options qui activent/désactivent un comportement prévu par la policy.

- Lister : `getsebool -a | head`
- Voir un boolean précis : `getsebool httpd_can_network_connect`
- Changer (persistant) : `setsebool -P httpd_can_network_connect on|off`

🧠 Bonne pratique : utiliser les booleans plutôt que bricoler des règles custom si le besoin est prévu.

### 6.3 Contextes fichiers : `chcon` vs `semanage fcontext` vs `restorecon`
- `chcon` : change **temporairement** (peut être écrasé par un relabel / restorecon).
- `semanage fcontext` : ajoute une règle de mapping “chemin → type” (persistant).
- `restorecon` : applique le contexte attendu (d’après la policy + règles locales).

Workflow propre :
1) définir la règle persistante : `semanage fcontext ...`
2) appliquer : `restorecon -Rv /chemin`

### 6.4 `semanage` : la commande “admin policy sans recompiler”
`semanage` sert à modifier des éléments “gérés” de la policy : mappings, ports, file contexts, users, permissive domains, etc.

Sous-commandes fréquentes :
- `semanage fcontext` (fichiers)
- `semanage port` (ports)
- `semanage permissive` (domain permissif)
- `semanage boolean` (selon distro)
- `semanage login/user` (mapping Linux user → SELinux user)

Exemples (à connaître) :
- Lister : `semanage fcontext -l | head`
- Ajouter un mapping de contexte fichier :
  - `semanage fcontext -a -t httpd_sys_content_t "/srv/myweb(/.*)?"`
  - puis `restorecon -Rv /srv/myweb`
- Autoriser httpd à écouter sur un port non-standard :
  - `semanage port -a -t http_port_t -p tcp 8080`

### 6.5 Domain permissif “local” (granulaire)
Au lieu de mettre tout SELinux en permissive, on peut mettre **un domain** en permissive :
- `semanage permissive -a httpd_t`
- `semanage permissive -d httpd_t`

Intérêt :
- le reste du système reste protégé en enforcing,
- on collecte des logs pour corriger proprement.

---

## 7) SELinux troubleshooting : méthode “pro”

### 7.1 Les symptômes classiques
- “Ça marche en permissive mais pas en enforcing”
- “Permission denied” alors que `chmod` semble correct
- service systemd qui fail au démarrage
- nginx/apache ne peut pas lire un répertoire, ou bind sur un port

### 7.2 Les logs : comprendre un AVC denial
SELinux log typique : **AVC** (Access Vector Cache) denial.
Sources :
- `/var/log/audit/audit.log` (si auditd)
- `journalctl` (selon setup)
- outils d’aide : `sealert` / `setroubleshoot` (selon distro)

Commandes utiles :
- `ausearch -m avc -ts recent`
- `grep -i avc /var/log/audit/audit.log | tail`

Tu cherches :
- le **process/domain** (ex. `httpd_t`)
- l’**objet/type** (ex. `default_t`, `var_t`, etc.)
- l’**action** (read/write/execute/name_bind…)
- le **chemin** (souvent présent)

### 7.3 `audit2why` et `audit2allow` (à utiliser intelligemment)
- `audit2why` explique *pourquoi* c’est bloqué.
- `audit2allow` propose des règles “allow” (mais attention : ça peut ouvrir trop large).

Règle d’or :
1) d’abord vérifier **labeling** (mauvais contexte) → souvent la vraie cause,
2) ensuite booleans prévus,
3) ensuite `semanage port/fcontext`,
4) en dernier recours : module custom (très contrôlé).

### 7.4 Le piège n°1 : fichiers en `default_t`
Si un fichier/répertoire finit en `default_t`, beaucoup de services ne pourront pas y accéder.
Solution typique :
- définir fcontext via `semanage fcontext -a ...`
- appliquer via `restorecon`

### 7.5 Le piège n°2 : ports non déclarés
Service web sur 8080 mais SELinux ne le permet pas :
- `semanage port -l | grep http`
- `semanage port -a -t http_port_t -p tcp 8080`

---

## 8) AppArmor : l’autre MAC majeur

### 8.1 AppArmor en 3 phrases
- AppArmor est un MAC basé sur des **profils** décrivant ce qu’un programme peut faire.
- Il est majoritairement **path-based** (chemins) plutôt que label/type (SELinux).
- Très “lisible” et souvent plus simple à démarrer.

### 8.2 Profil AppArmor : c’est quoi ?
Un **profil** décrit les accès autorisés :
- fichiers (lecture/écriture),
- capacités,
- accès réseau,
- exécution d’autres binaires,
- etc.

Emplacement classique :
- `/etc/apparmor.d/` (selon distro)

### 8.3 Modes AppArmor
- **enforce** : bloque réellement
- **complain** : n’applique pas, mais log
- (et on peut désactiver un profil)

Commandes (souvent disponibles via `apparmor-utils`) :
- `aa-status` : voir l’état global
- `aa-enforce /etc/apparmor.d/<profil>`
- `aa-complain /etc/apparmor.d/<profil>`

### 8.4 Recharger des profils
Selon distro/outils :
- `systemctl reload apparmor` (si support)
- ou via parser :
  - `apparmor_parser -r /etc/apparmor.d/<profil>`
  - `apparmor_parser -r /etc/apparmor.d/*` (attention)

### 8.5 `aa-logprof` : génération assistée
`aa-logprof` lit les logs et propose des règles à ajouter au profil.
Approche :
1) mettre le profil en complain
2) exécuter l’app → générer des événements
3) lancer `aa-logprof` pour affiner proprement
4) repasser en enforce

---

## 9) SELinux vs AppArmor : différences qui tombent en entretien

### 9.1 Philosophie
- **SELinux** : *label-based* + policy plus “formelle” (TE/RBAC/MLS/MCS).
- **AppArmor** : *path-based* + profils centrés “par programme”.

### 9.2 Granularité et robustesse
- SELinux :
  - très puissant (types, transitions, ports, niveaux),
  - robuste face à certains contournements de chemin (hardlinks, bind mounts, renames… selon cas),
  - plus complexe à maîtriser.
- AppArmor :
  - plus simple à écrire/maintenir,
  - dépend fortement de la stabilité des chemins et des conventions de profils,
  - très efficace dans beaucoup de contextes (desktop/serveurs standard).

### 9.3 Adoption
- SELinux très courant : RHEL/CentOS/Rocky/Alma/Fedora, Android.
- AppArmor courant : Ubuntu/Debian (souvent), SUSE (selon variantes).

---

## 10) Least Privilege (principe du moindre privilège) dans MAC

MAC sert précisément à appliquer le **moindre privilège** :
- un service n’a que les droits dont il a besoin,
- même si le service est compromis, l’attaquant reste enfermé dans son “domaine/profil”.

Exemples concrets :
- `httpd` peut lire `/var/www/html`, pas `/root`, pas `/etc/shadow`.
- un process confiné ne peut pas ouvrir une socket arbitraire si non autorisé.
- un process ne peut pas exécuter n’importe quel binaire si non autorisé.

---

## 11) Audit logs : pourquoi c’est crucial

Les logs MAC donnent :
- une visibilité sur les tentatives d’accès anormales,
- une piste de debug (“pourquoi mon service fail ?”),
- et un signal sécurité (tentatives suspectes répétées).

SELinux :
- AVC denials très structurés
- outils `ausearch`, `sealert`, `audit2why`

AppArmor :
- logs souvent dans `journalctl` / syslog
- événements “DENIED” liés au profil

👉 En entreprise, l’audit est aussi utile pour :
- durcir progressivement (phase complain/permissive),
- prouver la conformité (contrôles, investigations).

---

## 12) Linux Capabilities : lien avec MAC (et pourquoi ça compte)

### 12.1 Pourquoi les capabilities existent ?
Traditionnellement, root = “tout”. Les **capabilities** découpent les privilèges root en “morceaux” :
- `CAP_NET_BIND_SERVICE` : bind ports <1024
- `CAP_NET_ADMIN` : config réseau
- `CAP_SYS_ADMIN` : énorme (dangereux)
- etc.

### 12.2 Capabilities et MAC
- DAC : permissions fichiers / ownership.
- Capabilities : privilèges kernel spécifiques.
- MAC (SELinux/AppArmor) : peut **restreindre** un process même s’il a certaines capabilities, ou contrôler des actions où le DAC ne suffit pas.

🧠 Exemple :
- donner `CAP_NET_BIND_SERVICE` à un binaire permet d’écouter sur 80 sans root,
- mais SELinux peut quand même bloquer le `name_bind` si le port n’est pas étiqueté ou si le domain n’a pas le droit.

---

## 13) Check-list “j’explique MAC sans Google” (objectif Holberton)

Tu dois être capable de :
1) Définir DAC vs MAC vs RBAC et expliquer pourquoi MAC est ajouté.
2) Expliquer LSM (kernel hooks) et pourquoi c’est robuste.
3) Expliquer SELinux : context `user:role:type:range`, TE, RBAC, MLS/MCS.
4) Expliquer la notion de policy et pourquoi elle est centrale.
5) Lire un `ls -Z` / `ps -eZ` et repérer les types.
6) Connaître les commandes : `getenforce`, `setenforce`, `sestatus`, `getsebool`, `setsebool`, `semanage`, `restorecon`.
7) Expliquer `semanage fcontext` vs `chcon` vs `restorecon`.
8) Décrire AppArmor : profils, enforce/complain, `aa-status`, reload via `apparmor_parser`.
9) Comparer SELinux vs AppArmor (label-based vs path-based).
10) Dépanner : partir des logs audit, interpréter un denial, corriger par labels/booleans/ports.

---

## 14) Mini-labs (à faire sur ta VM pour devenir solide)

### Lab A — Observer
- Active SELinux/AppArmor (selon distro) et vérifie :
  - SELinux : `sestatus`, `getenforce`, `ls -Z`, `ps -eZ`
  - AppArmor : `aa-status`

### Lab B — “Ça marche en permissive mais pas en enforcing”
- Mets SELinux en permissive (`setenforce 0`) et démarre un service.
- Repasse enforcing (`setenforce 1`) et observe l’échec.
- Lis les logs AVC.
- Identifie : domain, type cible, action, chemin.
- Corrige par labeling correct (pas par “disable SELinux”).

### Lab C — Port non standard pour web
- Configure un service web sur un port non standard.
- Observe le blocage SELinux.
- Corrige via `semanage port`.

### Lab D — AppArmor profil en complain → génération
- Mets un profil en complain.
- Utilise l’app pour générer des événements.
- Lance `aa-logprof` et applique un durcissement.
- Reviens en enforce.

---

## 15) Encadré “contraintes scripts Holberton / Kali” (à connaître)

Tu as des contraintes de scripting (selon ton sujet) :
- première ligne : `#!/bin/bash`
- fichier finit par une nouvelle ligne
- scripts **2 lignes** (`wc -l` = 2)
- pas de `printf`
- pas de backticks
- pas de `&&`, `||`, ni `;`
- style Betty requis (même si c’est du bash, Holberton aime la rigueur)

👉 Interprétation pratique :
- ligne 1 = shebang
- ligne 2 = une seule commande (ou un pipeline simple sans `;`/`&&`/`||`)
- si tu dois faire “plusieurs actions”, il faudra les exprimer via :
  - substitutions `$(...)` (si autorisées dans ton projet),
  - redirections,
  - pipes,
  - ou des outils qui font plusieurs choses en une commande.
*(Attention : selon le checker, certaines substitutions peuvent être tolérées ou non. Respecte strictement la consigne de ton repo.)*

---

## 16) Références (à citer en soutenance / à relire)
- NIST (glossaire MAC)
- Documentation Red Hat SELinux (guides admin, contexts, booleans, semanage)
- Manpages : `semanage(8)`, `restorecon(8)`, `setenforce(8)`, `audit2why(1)`, `audit2allow(1)`
- SELinux Project Wiki
- AppArmor Project Wiki + outils `apparmor-utils`
- Docs Rocky/Alma/CentOS/Fedora SELinux
- Arch Wiki (SELinux/AppArmor, LSM, troubleshooting)
- Docs SUSE sur le “security context”
- Linux kernel docs : capabilities (`capabilities(7)`), LSM

---

# Fin

Si tu veux, envoie-moi :
- la sortie de `sestatus` (ou `aa-status`), 
- et un exemple de denial (AVC / AppArmor DENIED),
et je te guide **pas à pas** pour l’analyser comme en audit (sans te donner une “solution magique”).
