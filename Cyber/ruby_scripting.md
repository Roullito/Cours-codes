# Ruby Scripting pour la Cybersécurité

> Support de cours complet — module **Ruby Scripting (Cyber)**  
> Niveau visé : étudiant cybersécurité / pentest junior / scripting d'automatisation sur Kali Linux  
> Cadre : **lab autorisé, CTF, environnement de formation, bug bounty dans le scope uniquement**

---

## Table des matières

1. [Objectifs du module](#1-objectifs-du-module)
2. [Pourquoi apprendre Ruby en cybersécurité ?](#2-pourquoi-apprendre-ruby-en-cybersécurité-)
3. [Cadre éthique, légal et sécurité opérationnelle](#3-cadre-éthique-légal-et-sécurité-opérationnelle)
4. [Environnement Ruby sur Kali Linux](#4-environnement-ruby-sur-kali-linux)
5. [Contraintes de projet Holberton / Cyber](#5-contraintes-de-projet-holberton--cyber)
6. [Bases solides de Ruby](#6-bases-solides-de-ruby)
7. [Méthodes, classes, héritage et modules](#7-méthodes-classes-héritage-et-modules)
8. [Récursivité, itération, factorielle et nombres premiers](#8-récursivité-itération-factorielle-et-nombres-premiers)
9. [Gestion des fichiers : lecture, écriture, copie](#9-gestion-des-fichiers--lecture-écriture-copie)
10. [HTTP en Ruby : Net::HTTP, POST, JSON, HTTParty](#10-http-en-ruby--nethttp-post-json-httparty)
11. [Téléchargement de fichiers sécurisé](#11-téléchargement-de-fichiers-sécurisé)
12. [Réseau : ping, sockets, ports, bannières](#12-réseau--ping-sockets-ports-bannières)
13. [Scanner de ports et banner grabbing en Ruby](#13-scanner-de-ports-et-banner-grabbing-en-ruby)
14. [Scanner de vulnérabilités basique et défensif](#14-scanner-de-vulnérabilités-basique-et-défensif)
15. [Hashing, mots de passe et audit local](#15-hashing-mots-de-passe-et-audit-local)
16. [SSH avec Net::SSH : automatisation légitime et limites](#16-ssh-avec-netssh--automatisation-légitime-et-limites)
17. [Détection SQLi, XSS et traversal : approche OWASP](#17-détection-sqli-xss-et-traversal--approche-owasp)
18. [Packet capture, MITM et sniffing : uniquement en lab](#18-packet-capture-mitm-et-sniffing--uniquement-en-lab)
19. [Malware analysis, reverse shell, buffer overflow, keylogger : cadre défensif](#19-malware-analysis-reverse-shell-buffer-overflow-keylogger--cadre-défensif)
20. [Automatisation phishing : ce qu'il faut apprendre sans attaquer](#20-automatisation-phishing--ce-quil-faut-apprendre-sans-attaquer)
21. [SQLite avec Ruby](#21-sqlite-avec-ruby)
22. [JSON, REST API et parsing propre](#22-json-rest-api-et-parsing-propre)
23. [Multithreading et files de tâches](#23-multithreading-et-files-de-tâches)
24. [Créer une petite application web Sinatra](#24-créer-une-petite-application-web-sinatra)
25. [Tests avec RSpec](#25-tests-avec-rspec)
26. [Applications CLI propres en Ruby](#26-applications-cli-propres-en-ruby)
27. [Web scraping avec Mechanize](#27-web-scraping-avec-mechanize)
28. [Gestion des exceptions et robustesse](#28-gestion-des-exceptions-et-robustesse)
29. [Architecture recommandée d'un projet Ruby cyber](#29-architecture-recommandée-dun-projet-ruby-cyber)
30. [Méthodologie de travail pour les exercices](#30-méthodologie-de-travail-pour-les-exercices)
31. [Mini-projets progressifs](#31-mini-projets-progressifs)
32. [Checklist finale avant rendu](#32-checklist-finale-avant-rendu)
33. [Cheat sheet Ruby cyber](#33-cheat-sheet-ruby-cyber)
34. [Sources et documentation officielle](#34-sources-et-documentation-officielle)

---

## 1. Objectifs du module

À la fin de ce module, tu dois être capable d'expliquer et de pratiquer Ruby dans un contexte cybersécurité.

Tu dois savoir :

- expliquer pourquoi Ruby est utile en cybersécurité ;
- comprendre le rôle de Ruby dans Metasploit ;
- écrire des scripts Ruby exécutables sur Kali Linux ;
- manipuler les arguments de ligne de commande ;
- créer des fonctions, classes, héritages et modules ;
- lire, écrire et copier des fichiers ;
- faire des requêtes HTTP GET et POST ;
- consommer une API REST ;
- parser du JSON ;
- utiliser des gems via RubyGems / Bundler ;
- faire des tests simples avec RSpec ;
- automatiser des tâches de reconnaissance autorisée ;
- comprendre les limites légales et éthiques des scripts offensifs ;
- documenter proprement un projet cyber en README.md.

Ce cours est volontairement long et détaillé : l'objectif n'est pas seulement de réussir les tâches, mais de construire une vraie base de scripting cyber.

---

## 2. Pourquoi apprendre Ruby en cybersécurité ?

Ruby est moins populaire aujourd'hui que Python pour l'automatisation cyber généraliste, mais il reste très important pour plusieurs raisons.

### 2.1 Ruby est le langage historique de Metasploit

Metasploit Framework, l'un des frameworks les plus connus en pentest, est écrit majoritairement en Ruby. Comprendre Ruby permet de :

- lire des modules Metasploit ;
- comprendre comment sont structurés les exploits ;
- modifier un module existant en lab ;
- créer un module auxiliaire ;
- comprendre les payloads, options, sessions et handlers ;
- mieux lire le code d'outils offensifs historiques.

Même si tu n'écris pas tous les jours du Ruby, savoir le lire est un vrai avantage en cybersécurité.

### 2.2 Ruby est excellent pour le scripting rapide

Ruby a une syntaxe lisible, expressive et proche du langage naturel. Pour un analyste ou pentester, cela permet d'écrire rapidement :

- des scripts de parsing de logs ;
- des outils de vérification HTTP ;
- des scanners simples ;
- des scripts de génération de rapports ;
- des scripts d'analyse de fichiers ;
- des outils CLI internes ;
- des prototypes de PoC dans un lab.

### 2.3 Ruby favorise le prototypage

Ruby permet de transformer rapidement une idée en script testable. En cyber, c'est utile pour :

- valider une hypothèse ;
- automatiser une requête Burp répétitive ;
- tester une API dans le scope ;
- parser une réponse JSON ;
- comparer des hashs ;
- générer des payloads de test non destructifs ;
- analyser une liste d'URLs.

### 2.4 Ruby est orienté objet dès le départ

En Ruby, presque tout est objet. Même un nombre, une chaîne de caractères ou un tableau possède des méthodes.

Exemple conceptuel :

```ruby
#!/usr/bin/env ruby

message = "ruby cyber"
puts message.upcase
puts message.length
```

Tu peux lire cette logique comme :

> L'objet `message` reçoit la méthode `upcase`, puis la méthode `length`.

Cette façon de penser est utile pour structurer des outils propres : un scanner, un parseur, un client API ou un module de rapport peuvent devenir des classes.

---

## 3. Cadre éthique, légal et sécurité opérationnelle

Ce module touche à des sujets sensibles : port scanning, banner grabbing, détection de vulnérabilités, SSH, packet capture, reverse shell, phishing simulation, etc.

La règle centrale :

> Tu ne testes que ce que tu as le droit explicite de tester.

Cadres acceptables :

- machines TryHackMe ;
- machines Hack The Box ;
- lab local Docker ;
- VM volontairement vulnérable ;
- environnement d'école autorisé ;
- programme bug bounty avec scope clair ;
- infrastructure personnelle ;
- application d'un collègue avec autorisation écrite.

Cadres non acceptables :

- scanner des IP publiques au hasard ;
- brute force SSH contre des serveurs réels ;
- intercepter le trafic d'autrui ;
- automatiser du phishing contre des personnes réelles ;
- créer ou déployer un keylogger ;
- exécuter un reverse shell hors lab ;
- utiliser un exploit sans autorisation.

Dans ce cours, les parties dangereuses sont traitées sous angle :

- compréhension ;
- détection ;
- prévention ;
- lab local ;
- documentation professionnelle ;
- preuves non destructives.

---

## 4. Environnement Ruby sur Kali Linux

### 4.1 Vérifier Ruby

Sur Kali, Ruby est souvent déjà installé parce que certains outils l'utilisent.

Commandes utiles :

```bash
ruby -v
gem -v
which ruby
```

Tu veux obtenir quelque chose du style :

```text
ruby 3.x.x
```

La version exacte dépend de Kali et de tes paquets installés.

### 4.2 Installer Ruby si nécessaire

```bash
sudo apt update
sudo apt install ruby ruby-dev build-essential
```

`ruby-dev` et `build-essential` sont utiles pour installer certaines gems qui compilent des extensions natives.

### 4.3 RubyGems

RubyGems est le gestionnaire de paquets Ruby.

Exemples :

```bash
gem list
gem install httparty
gem install net-ping
gem install net-ssh
gem install sqlite3
gem install rspec
gem install sinatra
gem install mechanize
```

### 4.4 Bundler

Bundler permet de gérer les dépendances d'un projet Ruby avec un fichier `Gemfile`.

Installation :

```bash
gem install bundler
```

Exemple de `Gemfile` :

```ruby
source "https://rubygems.org"

gem "httparty"
gem "net-ping"
gem "net-ssh"
gem "sqlite3"
gem "rspec"
gem "sinatra"
gem "mechanize"
```

Puis :

```bash
bundle install
```

### 4.5 Exécuter un script Ruby

Un script Ruby peut être exécuté comme ceci :

```bash
ruby script.rb
```

Mais dans ton projet, les fichiers doivent être exécutables et commencer par :

```ruby
#!/usr/bin/env ruby
```

Donc tu dois pouvoir faire :

```bash
chmod +x script.rb
./script.rb
```

---

## 5. Contraintes de projet Holberton / Cyber

Tu as donné ces contraintes :

- éditeurs autorisés : `vi`, `vim`, `emacs` ;
- scripts testés sur Kali Linux ;
- première ligne exacte de chaque script : `#!/usr/bin/env ruby` ;
- tous les fichiers doivent finir par une nouvelle ligne ;
- `README.md` obligatoire à la racine ;
- tous les fichiers doivent être exécutables ;
- interdiction d'utiliser les backticks, `&&`, `||` ou `;` dans les scripts ;
- code vérifié avec des scripts de style type Betty ;
- l'IP ou la plage IP doit venir du premier argument utilisateur.

### 5.1 Point important : `$1` en Bash vs Ruby

Dans un script Bash, `$1` désigne le premier argument.

En Ruby, le premier argument de ligne de commande se lit avec :

```ruby
#!/usr/bin/env ruby

target = ARGV[0]
puts target
```

Attention : en Ruby, `$1` existe aussi, mais il ne signifie pas “premier argument CLI”. Il correspond généralement à une capture de regex après un match. Pour un script Ruby, tu dois donc utiliser `ARGV[0]` pour récupérer l'argument donné après le nom du script.

Exemple :

```bash
./scan.rb 192.168.1.10
```

Dans le script :

```ruby
#!/usr/bin/env ruby

target = ARGV[0]
puts "Target: #{target}"
```

### 5.2 Template minimal de script propre

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./script.rb <target>"
  exit 1
end

target = ARGV[0]
puts "Target: #{target}"
```

Ce template respecte l'interdiction de `&&`, `||` et `;`.

### 5.3 Pourquoi finir par une nouvelle ligne ?

Un fichier texte POSIX propre se termine par une newline. Sans cette newline :

- certains outils CLI affichent mal le fichier ;
- certains checkers considèrent le fichier comme mal formé ;
- `cat`, `diff`, `git` ou certains scripts de correction peuvent produire des sorties moins propres.

Vérifier la fin de ligne :

```bash
tail -c 1 file.rb
```

Tu peux simplement ouvrir le fichier et t'assurer qu'il y a une ligne vide après la dernière ligne.

### 5.4 Rendre les fichiers exécutables

```bash
chmod +x *.rb
```

Vérifier :

```bash
ls -l
```

Tu dois voir `x` dans les permissions.

---

## 6. Bases solides de Ruby

### 6.1 Affichage

```ruby
#!/usr/bin/env ruby

puts "Hello Ruby"
print "No newline"
puts " after print"
warn "This is an error message"
```

Différences :

- `puts` ajoute une nouvelle ligne ;
- `print` n'ajoute pas automatiquement de nouvelle ligne ;
- `warn` écrit sur la sortie d'erreur standard.

### 6.2 Variables

```ruby
#!/usr/bin/env ruby

name = "Kali"
age = 5
is_lab = true

puts name
puts age
puts is_lab
```

Ruby n'oblige pas à déclarer le type. Il l'infère automatiquement.

### 6.3 Types courants

```ruby
#!/usr/bin/env ruby

text = "cyber"
number = 42
price = 19.99
items = ["http", "ssh", "dns"]
service = { "port" => 22, "name" => "ssh" }

puts text.class
puts number.class
puts price.class
puts items.class
puts service.class
```

Types importants :

- `String` : texte ;
- `Integer` : entier ;
- `Float` : nombre décimal ;
- `Array` : liste ordonnée ;
- `Hash` : dictionnaire clé-valeur ;
- `TrueClass` / `FalseClass` : booléens ;
- `NilClass` : absence de valeur.

### 6.4 Chaînes de caractères

```ruby
#!/usr/bin/env ruby

protocol = "https"
host = "example.com"
url = "#{protocol}://#{host}"

puts url
puts url.upcase
puts url.include?("https")
```

L'interpolation avec `#{}` fonctionne dans les doubles guillemets.

### 6.5 Tableaux

```ruby
#!/usr/bin/env ruby

ports = [22, 80, 443]

puts ports.first
puts ports.last
puts ports.length

ports.each do |port|
  puts "Port: #{port}"
end
```

En cyber, les tableaux servent souvent à stocker :

- des ports ;
- des URLs ;
- des endpoints ;
- des payloads de test ;
- des lignes de fichier ;
- des résultats.

### 6.6 Hashes

```ruby
#!/usr/bin/env ruby

service = {
  "port" => 443,
  "name" => "https",
  "encrypted" => true
}

puts service["port"]
puts service["name"]
```

Avec symboles :

```ruby
#!/usr/bin/env ruby

service = {
  port: 443,
  name: "https",
  encrypted: true
}

puts service[:port]
puts service[:name]
```

Les symboles sont très utilisés en Ruby pour des clés internes.

### 6.7 Conditions

```ruby
#!/usr/bin/env ruby

port = 443

if port == 443
  puts "HTTPS"
elsif port == 80
  puts "HTTP"
else
  puts "Other service"
end
```

Tu peux aussi utiliser `unless` :

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Missing argument"
  exit 1
end
```

Pour rester clair dans des scripts de projet, `if` est souvent préférable à `unless` quand tu débutes.

### 6.8 Opérateurs logiques sans utiliser les symboles interdits

Ton projet interdit `&&` et `||`. En Ruby, tu peux utiliser les mots-clés :

```ruby
#!/usr/bin/env ruby

port = 443
service = "https"

if port == 443 and service == "https"
  puts "Secure web service"
end

if port == 80 or port == 443
  puts "Web service"
end
```

Dans beaucoup de projets Ruby réels, les opérateurs symboliques sont fréquents. Mais ici, respecte la contrainte du sujet.

### 6.9 Boucles

```ruby
#!/usr/bin/env ruby

ports = [22, 80, 443]

ports.each do |port|
  puts "Testing port #{port}"
end
```

Boucle avec range :

```ruby
#!/usr/bin/env ruby

(1..5).each do |number|
  puts number
end
```

Boucle `while` :

```ruby
#!/usr/bin/env ruby

counter = 0

while counter < 3
  puts counter
  counter += 1
end
```

### 6.10 Conversion de types

Les arguments CLI arrivent comme chaînes de caractères.

```ruby
#!/usr/bin/env ruby

value = ARGV[0]
number = value.to_i

puts number * 2
```

Attention :

```ruby
"abc".to_i
```

retourne `0`. Pour valider un nombre, il faut être plus strict.

```ruby
#!/usr/bin/env ruby

value = ARGV[0]

begin
  number = Integer(value)
  puts number
rescue ArgumentError
  warn "Invalid integer"
  exit 1
end
```

---

## 7. Méthodes, classes, héritage et modules

### 7.1 Méthodes Ruby

Une méthode est un bloc de code réutilisable.

```ruby
#!/usr/bin/env ruby

def greet(name)
  "Hello #{name}"
end

puts greet("Ruby")
```

Ruby retourne implicitement la dernière expression.

```ruby
#!/usr/bin/env ruby

def square(number)
  number * number
end

puts square(5)
```

### 7.2 Méthodes avec validation

```ruby
#!/usr/bin/env ruby

def require_argument(value)
  if value.nil? or value.empty?
    warn "Missing argument"
    exit 1
  end

  value
end

target = require_argument(ARGV[0])
puts "Target: #{target}"
```

### 7.3 Classes

Une classe est un plan de construction d'objets.

```ruby
#!/usr/bin/env ruby

class Greeter
  def initialize(name)
    @name = name
  end

  def say_hello
    "Hello #{@name}"
  end
end

greeter = Greeter.new("Cyber")
puts greeter.say_hello
```

Explication :

- `class Greeter` définit une classe ;
- `initialize` est appelé quand on fait `Greeter.new` ;
- `@name` est une variable d'instance ;
- `say_hello` est une méthode d'instance.

### 7.4 Attributs

```ruby
#!/usr/bin/env ruby

class Service
  attr_reader :port, :name

  def initialize(port, name)
    @port = port
    @name = name
  end
end

service = Service.new(22, "ssh")
puts service.port
puts service.name
```

`attr_reader` crée automatiquement des méthodes de lecture.

Autres variantes :

- `attr_writer` : écriture ;
- `attr_accessor` : lecture et écriture.

### 7.5 Héritage

L'héritage permet de créer une classe spécialisée à partir d'une classe générale.

```ruby
#!/usr/bin/env ruby

class Scanner
  def initialize(target)
    @target = target
  end

  def target
    @target
  end
end

class HttpScanner < Scanner
  def description
    "HTTP scanner for #{target}"
  end
end

scanner = HttpScanner.new("example.com")
puts scanner.description
```

### 7.6 Modules

Un module sert à regrouper du code sans forcément créer des objets.

```ruby
#!/usr/bin/env ruby

module Formatter
  def self.banner(title)
    "=== #{title} ==="
  end
end

puts Formatter.banner("Ruby Cyber")
```

En cyber, tu peux imaginer :

- `Formatter` pour affichage ;
- `HttpHelpers` pour requêtes ;
- `Validation` pour vérifier les entrées ;
- `ReportWriter` pour générer un rapport.

---

## 8. Récursivité, itération, factorielle et nombres premiers

### 8.1 Factorielle

La factorielle de `n`, notée `n!`, est :

```text
n! = n × (n - 1) × (n - 2) × ... × 1
```

Exemple :

```text
5! = 5 × 4 × 3 × 2 × 1 = 120
```

### 8.2 Version itérative

```ruby
#!/usr/bin/env ruby

def factorial_iterative(number)
  if number < 0
    raise ArgumentError, "number must be positive"
  end

  result = 1

  (1..number).each do |current|
    result *= current
  end

  result
end

puts factorial_iterative(5)
```

Avantages :

- simple ;
- efficace ;
- évite les problèmes de pile d'appels.

### 8.3 Version récursive

```ruby
#!/usr/bin/env ruby

def factorial_recursive(number)
  if number < 0
    raise ArgumentError, "number must be positive"
  end

  if number == 0
    return 1
  end

  number * factorial_recursive(number - 1)
end

puts factorial_recursive(5)
```

Explication :

- cas d'arrêt : `0! = 1` ;
- cas récursif : `n! = n × (n - 1)!`.

### 8.4 Nombres premiers

Un nombre premier est un entier supérieur à 1 qui n'a que deux diviseurs positifs : 1 et lui-même.

Exemples :

- 2 est premier ;
- 3 est premier ;
- 4 n'est pas premier ;
- 5 est premier ;
- 9 n'est pas premier.

### 8.5 Vérification manuelle

```ruby
#!/usr/bin/env ruby

def prime?(number)
  return false if number <= 1
  return true if number == 2
  return false if number.even?

  divisor = 3

  while divisor * divisor <= number
    return false if number % divisor == 0
    divisor += 2
  end

  true
end

puts prime?(2)
puts prime?(9)
puts prime?(17)
```

Pourquoi tester jusqu'à la racine carrée ?

Si `n` a un diviseur plus grand que sa racine carrée, alors il a forcément un diviseur correspondant plus petit. Il est donc inutile de tester tous les nombres jusqu'à `n - 1`.

### 8.6 Utiliser la classe Prime

Ruby fournit une bibliothèque standard `prime`.

```ruby
#!/usr/bin/env ruby

require "prime"

number = 17
puts Prime.prime?(number)
```

---

## 9. Gestion des fichiers : lecture, écriture, copie

En cyber, la manipulation de fichiers est essentielle :

- lire des logs ;
- écrire des résultats ;
- parser des listes d'URLs ;
- générer des rapports ;
- traiter des wordlists en lab ;
- stocker des réponses JSON.

### 9.1 Lire un fichier complet

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./read_file.rb <file>"
  exit 1
end

path = ARGV[0]
content = File.read(path)
puts content
```

### 9.2 Lire ligne par ligne

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./read_lines.rb <file>"
  exit 1
end

path = ARGV[0]

File.foreach(path).with_index(1) do |line, index|
  puts "#{index}: #{line}"
end
```

Avantage : pour de gros fichiers, tu ne charges pas tout en mémoire.

### 9.3 Écrire dans un fichier

```ruby
#!/usr/bin/env ruby

output = "scan-results.txt"

File.open(output, "w") do |file|
  file.puts "Ruby scan report"
  file.puts "Status: OK"
end

puts "Written to #{output}"
```

Modes utiles :

- `r` : lecture ;
- `w` : écriture avec écrasement ;
- `a` : ajout à la fin ;
- `rb` : lecture binaire ;
- `wb` : écriture binaire.

### 9.4 Ajouter à un fichier

```ruby
#!/usr/bin/env ruby

File.open("events.log", "a") do |file|
  file.puts "New event"
end
```

### 9.5 Copier un fichier

```ruby
#!/usr/bin/env ruby

if ARGV.length < 2
  warn "Usage: ./copy_file.rb <source> <destination>"
  exit 1
end

source = ARGV[0]
destination = ARGV[1]

content = File.binread(source)
File.binwrite(destination, content)

puts "Copied #{source} to #{destination}"
```

### 9.6 Copier avec FileUtils

```ruby
#!/usr/bin/env ruby

require "fileutils"

if ARGV.length < 2
  warn "Usage: ./copy_fileutils.rb <source> <destination>"
  exit 1
end

source = ARGV[0]
destination = ARGV[1]

FileUtils.cp(source, destination)
puts "Copied"
```

### 9.7 Sécurité : attention aux chemins fournis par l'utilisateur

Si ton script prend un chemin en entrée, tu dois éviter :

- d'écraser un fichier sensible ;
- de lire un fichier hors scope ;
- d'accepter un chemin absolu dangereux ;
- de suivre aveuglément des `../`.

Exemple de validation simple pour un dossier de sortie :

```ruby
#!/usr/bin/env ruby

base_dir = File.expand_path("reports")
Dir.mkdir(base_dir) unless Dir.exist?(base_dir)

filename = "report.txt"
path = File.expand_path(File.join(base_dir, filename))

unless path.start_with?(base_dir)
  warn "Invalid output path"
  exit 1
end

File.write(path, "safe report\n")
puts path
```

---

## 10. HTTP en Ruby : Net::HTTP, POST, JSON, HTTParty

HTTP est central en cybersécurité web : reconnaissance, API, tests d'authentification, endpoints, headers, cookies, redirections, statuts, JSON, etc.

### 10.1 Requête GET avec Net::HTTP

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

if ARGV.empty?
  warn "Usage: ./http_get.rb <url>"
  exit 1
end

uri = URI(ARGV[0])
response = Net::HTTP.get_response(uri)

puts "Status: #{response.code}"
puts response.body
```

### 10.2 Gérer HTTPS

`Net::HTTP.get_response` gère les URIs HTTPS quand l'URI est correctement fournie.

Pour plus de contrôle :

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

if ARGV.empty?
  warn "Usage: ./http_get_secure.rb <url>"
  exit 1
end

uri = URI(ARGV[0])

Net::HTTP.start(uri.host, uri.port, use_ssl: uri.scheme == "https") do |http|
  request = Net::HTTP::Get.new(uri)
  request["User-Agent"] = "RubyCyberLab/1.0"

  response = http.request(request)
  puts "Status: #{response.code}"
  puts response.body
end
```

### 10.3 Requête POST JSON

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"
require "json"

uri = URI("https://httpbin.org/post")
body = {
  username: "student",
  lab: "ruby"
}

Net::HTTP.start(uri.host, uri.port, use_ssl: true) do |http|
  request = Net::HTTP::Post.new(uri)
  request["Content-Type"] = "application/json"
  request.body = JSON.generate(body)

  response = http.request(request)
  puts response.code
  puts response.body
end
```

### 10.4 Lire les headers

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

if ARGV.empty?
  warn "Usage: ./headers.rb <url>"
  exit 1
end

uri = URI(ARGV[0])
response = Net::HTTP.get_response(uri)

response.each_header do |key, value|
  puts "#{key}: #{value}"
end
```

### 10.5 HTTParty

HTTParty est une gem qui simplifie les requêtes HTTP.

Installation :

```bash
gem install httparty
```

Exemple :

```ruby
#!/usr/bin/env ruby

require "httparty"

if ARGV.empty?
  warn "Usage: ./httparty_get.rb <url>"
  exit 1
end

response = HTTParty.get(ARGV[0])

puts response.code
puts response.body
```

HTTParty peut parser automatiquement certaines réponses JSON selon le `Content-Type`.

### 10.6 Bonnes pratiques HTTP en cyber

Toujours enregistrer :

- URL testée ;
- méthode HTTP ;
- code de réponse ;
- headers importants ;
- taille de réponse ;
- redirections ;
- message d'erreur ;
- timestamp ;
- contexte d'autorisation.

Éviter :

- les tests destructifs ;
- les boucles infinies ;
- les scans agressifs ;
- l'envoi massif de requêtes ;
- les payloads sans scope clair.

---

## 11. Téléchargement de fichiers sécurisé

Télécharger un fichier avec Ruby peut se faire avec `Net::HTTP` ou `open-uri`.

### 11.1 Téléchargement simple avec Net::HTTP

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

if ARGV.length < 2
  warn "Usage: ./download.rb <url> <output>"
  exit 1
end

uri = URI(ARGV[0])
output = ARGV[1]

unless uri.scheme == "http" or uri.scheme == "https"
  warn "Only HTTP and HTTPS are allowed"
  exit 1
end

response = Net::HTTP.get_response(uri)

unless response.is_a?(Net::HTTPSuccess)
  warn "Download failed with status #{response.code}"
  exit 1
end

File.binwrite(output, response.body)
puts "Saved to #{output}"
```

### 11.2 Sécurité du téléchargement

À vérifier :

- scheme autorisé : `http` ou `https` ;
- taille maximale ;
- nom de fichier contrôlé ;
- dossier de sortie fixe ;
- pas d'écrasement involontaire ;
- certificat TLS valide ;
- type de contenu attendu ;
- pas d'exécution automatique du fichier téléchargé.

### 11.3 Version avec limite de taille

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

MAX_BYTES = 2_000_000

if ARGV.length < 2
  warn "Usage: ./safe_download.rb <url> <output>"
  exit 1
end

uri = URI(ARGV[0])
output = ARGV[1]

unless uri.scheme == "http" or uri.scheme == "https"
  warn "Invalid scheme"
  exit 1
end

response = Net::HTTP.get_response(uri)

unless response.is_a?(Net::HTTPSuccess)
  warn "HTTP error #{response.code}"
  exit 1
end

if response.body.bytesize > MAX_BYTES
  warn "File too large"
  exit 1
end

File.binwrite(output, response.body)
puts "Downloaded safely"
```

---

## 12. Réseau : ping, sockets, ports, bannières

### 12.1 Ping : attention au sens du mot

En réseau, “ping” désigne souvent ICMP Echo Request / Echo Reply. Mais en Ruby, selon les gems, un ping peut être :

- ICMP ping ;
- TCP ping ;
- HTTP ping ;
- UDP ping ;
- external ping.

Sur Linux, ICMP peut nécessiter des permissions particulières.

### 12.2 Net::Ping

Installation :

```bash
gem install net-ping
```

Exemple TCP ping sur un port :

```ruby
#!/usr/bin/env ruby

require "net/ping"

if ARGV.empty?
  warn "Usage: ./tcp_ping.rb <host>"
  exit 1
end

host = ARGV[0]
ping = Net::Ping::TCP.new(host, 80, 2)

if ping.ping?
  puts "#{host}: reachable on TCP/80"
else
  puts "#{host}: not reachable on TCP/80"
end
```

### 12.3 Socket TCP simple

Ruby fournit `socket` en bibliothèque standard.

```ruby
#!/usr/bin/env ruby

require "socket"
require "timeout"

if ARGV.length < 2
  warn "Usage: ./tcp_check.rb <host> <port>"
  exit 1
end

host = ARGV[0]
port = Integer(ARGV[1])

timeout = 2

begin
  Timeout.timeout(timeout) do
    socket = TCPSocket.new(host, port)
    socket.close
    puts "open"
  end
rescue Errno::ECONNREFUSED
  puts "closed"
rescue Timeout::Error
  puts "filtered or timeout"
rescue SocketError
  puts "name resolution error"
end
```

### 12.4 Comprendre les états réseau

Quand tu testes un port TCP :

- `open` : connexion établie ;
- `closed` : l'hôte répond mais refuse la connexion ;
- `filtered` ou timeout : pare-feu, filtrage, perte réseau ou service muet ;
- `unknown host` : résolution DNS impossible.

Un scanner sérieux doit distinguer ces cas.

---

## 13. Scanner de ports et banner grabbing en Ruby

### 13.1 Cadre autorisé

Un scanner de ports est un outil de reconnaissance. Il peut être légitime dans un lab ou un audit autorisé, mais abusif sur des cibles non autorisées.

Utilisation correcte :

```bash
./port_scan.rb 127.0.0.1
./port_scan.rb scanme.local
./port_scan.rb 10.10.10.5
```

Pas d'utilisation contre des cibles publiques sans autorisation.

### 13.2 Scanner simple de ports courants

```ruby
#!/usr/bin/env ruby

require "socket"
require "timeout"

COMMON_PORTS = [21, 22, 25, 53, 80, 110, 139, 143, 443, 445, 3306, 5432, 8080]
TIMEOUT_SECONDS = 1

if ARGV.empty?
  warn "Usage: ./port_scan.rb <host>"
  exit 1
end

host = ARGV[0]

COMMON_PORTS.each do |port|
  begin
    Timeout.timeout(TIMEOUT_SECONDS) do
      socket = TCPSocket.new(host, port)
      socket.close
      puts "#{host}:#{port} open"
    end
  rescue Errno::ECONNREFUSED
    next
  rescue Timeout::Error
    next
  rescue SocketError => error
    warn "Socket error: #{error.message}"
    exit 1
  end
end
```

### 13.3 Banner grabbing

Le banner grabbing consiste à se connecter à un service et tenter de lire sa bannière.

Exemples de bannières :

- SSH renvoie souvent une ligne du type `SSH-2.0-OpenSSH_...` ;
- certains serveurs FTP annoncent leur version ;
- certains services HTTP renvoient un header `Server`.

Script simple :

```ruby
#!/usr/bin/env ruby

require "socket"
require "timeout"

if ARGV.length < 2
  warn "Usage: ./banner.rb <host> <port>"
  exit 1
end

host = ARGV[0]
port = Integer(ARGV[1])

begin
  Timeout.timeout(3) do
    socket = TCPSocket.new(host, port)
    socket.write("\r\n")
    banner = socket.readpartial(1024)
    puts banner
    socket.close
  end
rescue EOFError
  puts "No banner"
rescue Timeout::Error
  puts "Timeout"
rescue Errno::ECONNREFUSED
  puts "Closed"
end
```

### 13.4 Banner grabbing HTTP propre

Pour HTTP, il vaut mieux envoyer une vraie requête.

```ruby
#!/usr/bin/env ruby

require "socket"
require "timeout"

if ARGV.empty?
  warn "Usage: ./http_banner.rb <host>"
  exit 1
end

host = ARGV[0]

begin
  Timeout.timeout(3) do
    socket = TCPSocket.new(host, 80)
    socket.write("HEAD / HTTP/1.1\r\n")
    socket.write("Host: #{host}\r\n")
    socket.write("Connection: close\r\n")
    socket.write("\r\n")

    response = socket.read
    puts response
    socket.close
  end
rescue Timeout::Error
  warn "Timeout"
rescue Errno::ECONNREFUSED
  warn "Connection refused"
end
```

### 13.5 Limites d'un scanner maison

Un scanner Ruby maison est pédagogique, mais ne remplace pas Nmap.

Limites :

- détection de filtrage approximative ;
- pas de SYN scan bas niveau ;
- gestion limitée des timeouts ;
- pas d'OS fingerprinting ;
- pas de NSE ;
- risque de faux positifs ;
- performances limitées sans parallélisation.

But pédagogique : comprendre ce qui se passe sous le capot.

---

## 14. Scanner de vulnérabilités basique et défensif

Un scanner de vulnérabilités basique ne doit pas “exploiter” violemment. Il peut vérifier des signaux faibles :

- headers de sécurité absents ;
- version serveur exposée ;
- HTTP au lieu de HTTPS ;
- redirection manquante ;
- cookies sans flags ;
- pages d'erreur trop bavardes ;
- endpoints connus dans un lab ;
- fichiers publics oubliés dans un lab.

### 14.1 Exemple : vérification de headers HTTP

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

RECOMMENDED_HEADERS = [
  "strict-transport-security",
  "content-security-policy",
  "x-content-type-options",
  "x-frame-options",
  "referrer-policy"
]

if ARGV.empty?
  warn "Usage: ./header_check.rb <url>"
  exit 1
end

uri = URI(ARGV[0])
response = Net::HTTP.get_response(uri)
headers = {}

response.each_header do |key, value|
  headers[key.downcase] = value
end

puts "Status: #{response.code}"

RECOMMENDED_HEADERS.each do |header|
  if headers.key?(header)
    puts "[OK] #{header}: #{headers[header]}"
  else
    puts "[WARN] missing #{header}"
  end
end
```

### 14.2 Exemple : détection de serveur exposé

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

if ARGV.empty?
  warn "Usage: ./server_header.rb <url>"
  exit 1
end

uri = URI(ARGV[0])
response = Net::HTTP.get_response(uri)
server = response["server"]

if server.nil?
  puts "No Server header"
else
  puts "Server header: #{server}"
end
```

### 14.3 Ne pas confondre signal et vulnérabilité

Un header absent n'est pas automatiquement une faille critique. C'est souvent :

- une faiblesse de durcissement ;
- une mauvaise configuration ;
- un point de défense en profondeur ;
- un indice pour creuser.

Un bon rapport doit expliquer :

- l'impact réel ;
- les conditions d'exploitation ;
- la preuve ;
- la recommandation ;
- le niveau de risque justifié.

---

## 15. Hashing, mots de passe et audit local

### 15.1 Hash vs chiffrement

Un hash cryptographique transforme une entrée en empreinte de taille fixe.

Propriétés attendues :

- déterministe : même entrée, même hash ;
- sens unique : difficile de retrouver l'entrée ;
- effet avalanche : petit changement, grande différence ;
- résistance aux collisions.

Exemple avec SHA-256 :

```ruby
#!/usr/bin/env ruby

require "digest"

password = "training"
hash = Digest::SHA256.hexdigest(password)

puts hash
```

### 15.2 Pourquoi ne pas stocker un mot de passe en clair ?

Si une base fuit :

- mots de passe en clair : compromission immédiate ;
- mots de passe hashés faibles : cassables rapidement ;
- mots de passe hashés avec sel et algorithme adapté : attaque beaucoup plus coûteuse.

Pour du vrai stockage de mots de passe, on utilise des algorithmes dédiés comme :

- bcrypt ;
- Argon2 ;
- scrypt ;
- PBKDF2.

SHA-256 seul n'est pas suffisant pour stocker des mots de passe d'utilisateurs.

### 15.3 Audit local de hash en lab

Le but pédagogique est de comprendre pourquoi les mots de passe faibles sont dangereux, pas de voler des identifiants.

Exemple local avec une mini-wordlist contrôlée :

```ruby
#!/usr/bin/env ruby

require "digest"

if ARGV.length < 2
  warn "Usage: ./hash_audit.rb <sha256_hash> <wordlist>"
  exit 1
end

target_hash = ARGV[0]
wordlist = ARGV[1]
found = false

File.foreach(wordlist) do |line|
  candidate = line.strip
  candidate_hash = Digest::SHA256.hexdigest(candidate)

  if candidate_hash == target_hash
    puts "Match found in lab wordlist: #{candidate}"
    found = true
    break
  end
end

puts "No match" unless found
```

À retenir :

- ceci doit rester local/lab ;
- utiliser des hash réels volés est illégal ;
- pour défendre, impose des mots de passe forts, MFA, rate limiting, stockage robuste.

---

## 16. SSH avec Net::SSH : automatisation légitime et limites

### 16.1 Usage légitime de Net::SSH

`net-ssh` permet d'automatiser des connexions SSH légitimes :

- exécuter une commande sur un serveur que tu administres ;
- récupérer une information système ;
- déployer un fichier ;
- tester une configuration ;
- automatiser un lab.

Installation :

```bash
gem install net-ssh
```

Exemple d'automatisation légitime avec une seule connexion connue :

```ruby
#!/usr/bin/env ruby

require "net/ssh"

if ARGV.length < 2
  warn "Usage: ./ssh_command.rb <host> <user>"
  exit 1
end

host = ARGV[0]
user = ARGV[1]

begin
  Net::SSH.start(host, user) do |ssh|
    output = ssh.exec!("hostname")
    puts output
  end
rescue Net::SSH::AuthenticationFailed
  warn "Authentication failed"
rescue SocketError
  warn "Host not found"
end
```

Ce script suppose une authentification légitime, par exemple via clé SSH configurée.

### 16.2 Pourquoi je ne fournis pas de brute force SSH opérationnel

Un brute force SSH est une technique d'attaque contre l'authentification. Hors lab strictement contrôlé, cela peut :

- déclencher des alertes ;
- verrouiller des comptes ;
- violer une politique d'utilisation ;
- être illégal ;
- causer un impact réel sur une infrastructure.

Dans un cours propre, tu dois comprendre le concept et savoir t'en défendre, mais ne pas construire un outil d'attaque réutilisable contre des systèmes réels.

### 16.3 Version défensive : détecter des tentatives SSH dans les logs

Sur Linux, les échecs SSH apparaissent souvent dans `/var/log/auth.log` ou via `journalctl` selon la distribution.

Exemple de parseur de fichier de log fourni explicitement :

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./ssh_log_audit.rb <auth_log_file>"
  exit 1
end

path = ARGV[0]
counts = Hash.new(0)

File.foreach(path) do |line|
  next unless line.include?("Failed password")

  if line.match(/from ([0-9a-fA-F:.]+)/)
    ip = Regexp.last_match(1)
    counts[ip] += 1
  end
end

counts.sort_by do |item|
  -item[1]
end.each do |ip, count|
  puts "#{ip}: #{count} failed attempts"
end
```

Mesures défensives :

- clés SSH plutôt que mots de passe ;
- désactiver root login ;
- fail2ban ;
- MFA si possible ;
- allowlist IP ;
- logs centralisés ;
- alerting ;
- rotation des clés ;
- politique de mots de passe forte.

---

## 17. Détection SQLi, XSS et traversal : approche OWASP

Cette section est destinée aux labs, CTFs, environnements autorisés et applications que tu possèdes.

### 17.1 SQL Injection

Une SQL injection apparaît quand une application construit une requête SQL à partir d'entrées utilisateur non maîtrisées.

Exemple vulnérable conceptuel :

```text
SELECT * FROM users WHERE username = '<input utilisateur>'
```

Si l'entrée modifie la structure de la requête, l'application peut :

- lire des données non autorisées ;
- contourner une authentification ;
- modifier des données ;
- supprimer des données ;
- provoquer des erreurs révélatrices ;
- dans certains cas, atteindre le système sous-jacent.

Défense : requêtes préparées, ORM, validation, moindre privilège, logs, WAF en défense complémentaire.

### 17.2 XSS

Une XSS apparaît quand une application renvoie du contenu utilisateur dans une page sans encodage adapté au contexte.

Impacts possibles :

- exécution JavaScript dans le navigateur ;
- vol de jetons si mal protégés ;
- actions au nom de l'utilisateur ;
- phishing interne ;
- défiguration partielle ;
- pivot vers d'autres vulnérabilités.

Défense : encodage de sortie contextuel, CSP, validation, sanitation, cookies `HttpOnly`, frameworks sûrs.

### 17.3 Directory traversal

Une traversal vise à accéder à des fichiers hors du dossier prévu en manipulant un chemin.

Exemple conceptuel :

```text
/download?file=report.pdf
```

Si l'application concatène directement le paramètre `file` avec un chemin système, l'attaquant peut essayer de sortir du dossier autorisé.

Défense :

- allowlist de fichiers ;
- identifiants internes au lieu de chemins ;
- normalisation avec `realpath` ;
- vérification du préfixe de dossier ;
- droits minimaux ;
- pas d'accès direct aux fichiers sensibles.

### 17.4 Scanner de détection non destructif

Voici un exemple pédagogique qui envoie des probes simples à une application de lab et cherche des indices de réflexion ou d'erreur. Il ne confirme pas seul une vulnérabilité.

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

if ARGV.empty?
  warn "Usage: ./web_probe.rb <base_url>"
  exit 1
end

base_url = ARGV[0]
probes = [
  "'",
  "test<script>alert(1)</script>",
  "../test"
]

probes.each do |probe|
  uri = URI(base_url)
  params = URI.decode_www_form(uri.query.to_s)
  params << ["q", probe]
  uri.query = URI.encode_www_form(params)

  response = Net::HTTP.get_response(uri)
  body = response.body.to_s

  puts "Probe: #{probe}"
  puts "Status: #{response.code}"

  if body.include?(probe)
    puts "[INFO] input reflected"
  end

  if body.downcase.include?("sql") or body.downcase.include?("syntax")
    puts "[WARN] possible verbose error"
  end

  puts "---"
end
```

Limites :

- beaucoup de faux positifs ;
- beaucoup de faux négatifs ;
- ne remplace pas Burp Suite, OWASP ZAP ou une analyse manuelle ;
- un reflet n'est pas automatiquement une XSS ;
- une erreur SQL n'est pas toujours exploitable ;
- il faut toujours prouver l'impact dans le respect du scope.

---

## 18. Packet capture, MITM et sniffing : uniquement en lab

### 18.1 Comprendre sans abuser

Packet capture et MITM sont des sujets fondamentaux pour comprendre :

- TCP/IP ;
- DNS ;
- HTTP ;
- TLS ;
- ARP ;
- proxying ;
- segmentation réseau ;
- détection d'incidents.

Mais intercepter le trafic d'autrui sans autorisation est illégal.

### 18.2 Utilisation défensive

Cas légitimes :

- analyser un trafic de lab ;
- comprendre une requête HTTP générée par ton application ;
- vérifier qu'une application utilise TLS ;
- diagnostiquer un problème DNS ;
- détecter un scan interne ;
- analyser un PCAP fourni dans un CTF ;
- investiguer un incident avec autorisation.

### 18.3 Ruby packet capture

Ruby peut interagir avec libpcap via des gems comme `ruby-pcap` ou `pcaprub`, mais ces gems peuvent dépendre de bibliothèques natives et de permissions système.

Installation système probable :

```bash
sudo apt install libpcap-dev
```

Puis selon le cas :

```bash
gem install ruby-pcap
gem install pcaprub
```

En pratique, pour apprendre, il est souvent plus efficace d'utiliser :

- Wireshark pour visualiser ;
- tcpdump pour capturer ;
- Ruby pour parser un export texte ou JSON ;
- Zeek pour générer des logs réseau ;
- puis Ruby pour analyser ces logs.

### 18.4 Exemple défensif : parser un log réseau exporté

Supposons un fichier `connections.log` contenant :

```text
192.168.1.10 93.184.216.34 443
192.168.1.11 10.0.0.5 22
192.168.1.10 8.8.8.8 53
```

Script :

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./conn_summary.rb <connections.log>"
  exit 1
end

path = ARGV[0]
counts = Hash.new(0)

File.foreach(path) do |line|
  parts = line.split
  next if parts.length < 3

  destination = parts[1]
  port = parts[2]
  key = "#{destination}:#{port}"
  counts[key] += 1
end

counts.sort_by do |item|
  -item[1]
end.each do |destination, count|
  puts "#{destination}: #{count} connection(s)"
end
```

---

## 19. Malware analysis, reverse shell, buffer overflow, keylogger : cadre défensif

Cette section regroupe des sujets sensibles. L'objectif du module Ruby n'est pas de fournir des outils malveillants, mais de comprendre les concepts pour analyser, documenter et défendre.

### 19.1 Malware analysis

La malware analysis consiste à étudier un fichier ou comportement suspect.

Deux approches :

- analyse statique : sans exécuter ;
- analyse dynamique : en sandbox isolée.

Ruby peut aider à :

- calculer des hashs ;
- extraire des chaînes ;
- parser des logs ;
- renommer des samples ;
- générer un rapport ;
- comparer des indicateurs ;
- interroger une API interne de threat intelligence.

Exemple sûr : calculer les hashs d'un fichier fourni en lab.

```ruby
#!/usr/bin/env ruby

require "digest"

if ARGV.empty?
  warn "Usage: ./file_hashes.rb <file>"
  exit 1
end

path = ARGV[0]
content = File.binread(path)

puts "MD5: #{Digest::MD5.hexdigest(content)}"
puts "SHA1: #{Digest::SHA1.hexdigest(content)}"
puts "SHA256: #{Digest::SHA256.hexdigest(content)}"
```

### 19.2 Reverse shell

Un reverse shell est une connexion sortante depuis une machine compromise vers un attaquant. C'est très sensible.

Ce qu'il faut comprendre :

- flux sortant initié par la cible ;
- contournement possible de filtrage entrant ;
- interaction avec un shell ;
- souvent utilisé après exploitation ;
- très surveillé en défense.

Indicateurs défensifs :

- processus shell lancé par un service web ;
- connexions sortantes inhabituelles ;
- trafic vers IP inconnue ;
- usage anormal de `bash`, `sh`, `ruby`, `python`, `nc` ;
- parent process suspect ;
- commandes encodées ;
- persistance après connexion.

Ce cours ne fournit pas de reverse shell opérationnel.

### 19.3 Buffer overflow

Un buffer overflow est souvent lié à des langages bas niveau comme C. Ruby est mémoire-managé, donc tu ne l'utilises pas généralement pour créer l'overflow, mais Ruby peut servir à :

- générer des patterns ;
- envoyer des entrées de test à un programme de lab ;
- parser des crash logs ;
- automatiser un fuzzing contrôlé ;
- documenter des offsets.

Exemple sûr : générer une chaîne de test locale.

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./pattern.rb <length>"
  exit 1
end

length = Integer(ARGV[0])
pattern = "A" * length
puts pattern
```

### 19.4 Keylogger

Un keylogger capture les frappes clavier. C'est une technique très intrusive.

Ce qu'il faut savoir pour la défense :

- un keylogger peut être logiciel ou matériel ;
- il vise les identifiants, messages, données sensibles ;
- il peut persister au démarrage ;
- il peut exfiltrer vers un serveur ;
- il peut se cacher dans un processus légitime.

Détection :

- processus inconnus ;
- accès suspect aux périphériques d'entrée ;
- connexions sortantes régulières ;
- persistance inhabituelle ;
- hooks clavier ;
- alertes EDR ;
- intégrité des fichiers.

Ce cours ne fournit pas de code de keylogger.

---

## 20. Automatisation phishing : ce qu'il faut apprendre sans attaquer

Le phishing est une attaque d'ingénierie sociale. Automatiser des campagnes contre de vraies personnes sans cadre est illégal et dangereux.

Ce que tu peux apprendre proprement :

- fonctionnement SMTP ;
- structure d'un email ;
- headers ;
- SPF, DKIM, DMARC ;
- détection de liens suspects ;
- analyse de pièces jointes ;
- création de campagnes internes autorisées ;
- sensibilisation ;
- mesure défensive.

### 20.1 Parser des headers email fournis en lab

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./email_headers.rb <headers.txt>"
  exit 1
end

headers = File.read(ARGV[0])

["From", "Reply-To", "Return-Path", "Received", "Authentication-Results"].each do |name|
  headers.each_line do |line|
    if line.start_with?("#{name}:")
      puts line
    end
  end
end
```

### 20.2 Signaux suspects

- domaine proche mais différent ;
- `Reply-To` différent du `From` ;
- lien raccourci ;
- pièce jointe inattendue ;
- urgence artificielle ;
- demande de mot de passe ;
- SPF/DKIM/DMARC en échec ;
- URL qui ne correspond pas au texte affiché.

### 20.3 Simulation autorisée

Une simulation phishing légitime doit avoir :

- autorisation écrite ;
- périmètre défini ;
- objectif pédagogique ;
- pas de collecte de vrais mots de passe ;
- page d'atterrissage de sensibilisation ;
- métriques anonymisées si possible ;
- communication RH/juridique si entreprise ;
- rapport final défensif.

---

## 21. SQLite avec Ruby

SQLite est pratique pour stocker des résultats localement : scans, endpoints, observations, logs, etc.

### 21.1 Installation

```bash
gem install sqlite3
```

### 21.2 Créer une base et une table

```ruby
#!/usr/bin/env ruby

require "sqlite3"

DB_PATH = "findings.db"

db = SQLite3::Database.new(DB_PATH)

db.execute <<SQL
CREATE TABLE IF NOT EXISTS findings (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  target TEXT NOT NULL,
  title TEXT NOT NULL,
  severity TEXT NOT NULL,
  created_at TEXT NOT NULL
)
SQL

puts "Database ready"
```

### 21.3 Insérer une donnée avec paramètres

Important : utiliser des paramètres évite la concaténation SQL dangereuse.

```ruby
#!/usr/bin/env ruby

require "sqlite3"
require "time"

if ARGV.length < 3
  warn "Usage: ./add_finding.rb <target> <title> <severity>"
  exit 1
end

db = SQLite3::Database.new("findings.db")

db.execute(
  "INSERT INTO findings (target, title, severity, created_at) VALUES (?, ?, ?, ?)",
  [ARGV[0], ARGV[1], ARGV[2], Time.now.iso8601]
)

puts "Finding added"
```

### 21.4 Lire les résultats

```ruby
#!/usr/bin/env ruby

require "sqlite3"

db = SQLite3::Database.new("findings.db")
db.results_as_hash = true

rows = db.execute("SELECT id, target, title, severity, created_at FROM findings")

rows.each do |row|
  puts "#{row['id']} | #{row['severity']} | #{row['target']} | #{row['title']}"
end
```

---

## 22. JSON, REST API et parsing propre

### 22.1 Parser du JSON

```ruby
#!/usr/bin/env ruby

require "json"

raw = '{"service":"ssh","port":22}'
data = JSON.parse(raw)

puts data["service"]
puts data["port"]
```

### 22.2 Générer du JSON

```ruby
#!/usr/bin/env ruby

require "json"

data = {
  target: "127.0.0.1",
  open_ports: [22, 80]
}

puts JSON.pretty_generate(data)
```

### 22.3 Consommer une API REST

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"
require "json"

if ARGV.empty?
  warn "Usage: ./api_client.rb <url>"
  exit 1
end

uri = URI(ARGV[0])
response = Net::HTTP.get_response(uri)

unless response.is_a?(Net::HTTPSuccess)
  warn "HTTP error #{response.code}"
  exit 1
end

data = JSON.parse(response.body)
puts JSON.pretty_generate(data)
```

### 22.4 Gérer les erreurs JSON

```ruby
#!/usr/bin/env ruby

require "json"

if ARGV.empty?
  warn "Usage: ./parse_json_file.rb <file>"
  exit 1
end

begin
  data = JSON.parse(File.read(ARGV[0]))
  puts data.class
rescue JSON::ParserError => error
  warn "Invalid JSON: #{error.message}"
  exit 1
end
```

---

## 23. Multithreading et files de tâches

Le multithreading permet de lancer plusieurs tâches en parallèle. En cyber, c'est utile pour :

- vérifier plusieurs URLs ;
- scanner plusieurs ports dans un lab ;
- traiter plusieurs fichiers ;
- accélérer des requêtes réseau lentes.

Mais attention : trop de threads peuvent saturer une cible ou ton poste.

### 23.1 Thread simple

```ruby
#!/usr/bin/env ruby

thread = Thread.new do
  puts "Task in thread"
end

thread.join
puts "Done"
```

### 23.2 Plusieurs threads

```ruby
#!/usr/bin/env ruby

threads = []

5.times do |index|
  threads << Thread.new do
    puts "Thread #{index}"
  end
end

threads.each(&:join)
```

### 23.3 Queue thread-safe

```ruby
#!/usr/bin/env ruby

require "thread"

queue = Queue.new
[22, 80, 443, 8080].each do |port|
  queue << port
end

workers = []

2.times do
  workers << Thread.new do
    until queue.empty?
      port = queue.pop(true) rescue nil
      puts "Testing #{port}" unless port.nil?
    end
  end
end

workers.each(&:join)
```

### 23.4 Bonnes pratiques

- limiter le nombre de threads ;
- ajouter des timeouts ;
- logger les erreurs ;
- respecter le scope ;
- éviter les scans agressifs ;
- rendre le nombre de workers configurable ;
- documenter le rythme des requêtes.

---

## 24. Créer une petite application web Sinatra

Sinatra est un micro-framework Ruby très léger.

Installation :

```bash
gem install sinatra rackup puma
```

### 24.1 Hello World Sinatra

```ruby
#!/usr/bin/env ruby

require "sinatra"

get "/" do
  "Hello Ruby Cyber"
end
```

Lancer :

```bash
ruby app.rb
```

Puis visiter :

```text
http://localhost:4567
```

### 24.2 Endpoint JSON

```ruby
#!/usr/bin/env ruby

require "sinatra"
require "json"

get "/status" do
  content_type :json

  {
    status: "ok",
    service: "ruby-cyber-lab"
  }.to_json
end
```

### 24.3 Petit endpoint de scan simulé

Cet exemple ne scanne rien réellement. Il montre comment exposer une réponse API.

```ruby
#!/usr/bin/env ruby

require "sinatra"
require "json"

get "/scan/:host" do
  content_type :json

  {
    target: params[:host],
    status: "queued",
    message: "Scan simulation only"
  }.to_json
end
```

### 24.4 Sécurité Sinatra minimale

À retenir :

- ne jamais exposer un outil de scan sans authentification ;
- ne jamais exécuter des commandes système depuis des paramètres utilisateurs ;
- valider les entrées ;
- logger proprement ;
- ne pas afficher les stack traces en production ;
- ajouter une authentification si nécessaire ;
- limiter les actions possibles ;
- éviter les secrets dans le code.

---

## 25. Tests avec RSpec

RSpec est un framework de tests Ruby orienté comportement.

Installation :

```bash
gem install rspec
```

Initialiser :

```bash
rspec --init
```

### 25.1 Exemple de fonction testable

Fichier `lib/math_tools.rb` :

```ruby
#!/usr/bin/env ruby

module MathTools
  def self.factorial(number)
    raise ArgumentError, "negative number" if number < 0

    result = 1

    (1..number).each do |current|
      result *= current
    end

    result
  end
end
```

Fichier `spec/math_tools_spec.rb` :

```ruby
require_relative "../lib/math_tools"

RSpec.describe MathTools do
  describe ".factorial" do
    it "returns 1 for 0" do
      expect(described_class.factorial(0)).to eq(1)
    end

    it "returns 120 for 5" do
      expect(described_class.factorial(5)).to eq(120)
    end

    it "raises on negative number" do
      expect do
        described_class.factorial(-1)
      end.to raise_error(ArgumentError)
    end
  end
end
```

Lancer :

```bash
rspec
```

### 25.2 Pourquoi tester en cyber ?

Parce qu'un script cyber mal testé peut :

- scanner la mauvaise cible ;
- écraser des fichiers ;
- mal parser des logs ;
- produire de faux positifs ;
- rater une vulnérabilité ;
- envoyer trop de requêtes ;
- exposer des secrets.

### 25.3 Tests utiles pour scripts cyber

Tester :

- validation d'arguments ;
- parsing d'URL ;
- parsing JSON ;
- détection de headers ;
- écriture de rapport ;
- gestion des erreurs ;
- limite de taille ;
- timeouts simulés ;
- non-régression.

---

## 26. Applications CLI propres en Ruby

Une application CLI doit être prévisible et documentée.

### 26.1 OptionParser

`OptionParser` est dans la bibliothèque standard.

```ruby
#!/usr/bin/env ruby

require "optparse"

options = {
  ports: "80,443",
  timeout: 2
}

parser = OptionParser.new do |opts|
  opts.banner = "Usage: ./cli.rb [options] <target>"

  opts.on("-p", "--ports PORTS", "Comma-separated ports") do |value|
    options[:ports] = value
  end

  opts.on("-t", "--timeout SECONDS", Integer, "Timeout in seconds") do |value|
    options[:timeout] = value
  end
end

parser.parse!

if ARGV.empty?
  warn parser
  exit 1
end

target = ARGV[0]
ports = options[:ports].split(",").map do |port|
  Integer(port)
end

puts "Target: #{target}"
puts "Ports: #{ports.join(', ')}"
puts "Timeout: #{options[:timeout]}"
```

### 26.2 Codes de sortie

- `0` : succès ;
- `1` : erreur générale ;
- `2` : mauvais usage ;
- autre : erreur spécifique selon ton design.

Exemple :

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Missing target"
  exit 2
end

puts "OK"
exit 0
```

### 26.3 Sortie lisible et sortie machine

Pour un humain :

```text
[OK] 443 open
[WARN] missing CSP
```

Pour une machine :

```json
{
  "target": "example.com",
  "open_ports": [443],
  "warnings": ["missing CSP"]
}
```

Un bon outil propose parfois les deux formats.

---

## 27. Web scraping avec Mechanize

Mechanize permet d'automatiser certaines interactions web : suivre des liens, soumettre des formulaires, gérer des cookies.

Installation :

```bash
gem install mechanize
```

### 27.1 Usage autorisé

Le scraping doit respecter :

- robots.txt quand applicable ;
- conditions d'utilisation ;
- rate limits ;
- scope bug bounty ;
- vie privée ;
- pas de contournement d'authentification ;
- pas de collecte massive abusive.

### 27.2 Récupérer une page

```ruby
#!/usr/bin/env ruby

require "mechanize"

if ARGV.empty?
  warn "Usage: ./mechanize_get.rb <url>"
  exit 1
end

agent = Mechanize.new
page = agent.get(ARGV[0])

puts page.title
puts page.uri
```

### 27.3 Extraire les liens

```ruby
#!/usr/bin/env ruby

require "mechanize"

if ARGV.empty?
  warn "Usage: ./links.rb <url>"
  exit 1
end

agent = Mechanize.new
page = agent.get(ARGV[0])

page.links.each do |link|
  puts link.href
end
```

### 27.4 Cas cyber défensif

Mechanize peut aider à :

- vérifier des liens cassés ;
- détecter des formulaires sans CSRF dans un lab ;
- auditer une application interne ;
- automatiser des tests de navigation ;
- récupérer des pages d'un environnement de test.

---

## 28. Gestion des exceptions et robustesse

Un script cyber doit échouer proprement.

### 28.1 begin / rescue

```ruby
#!/usr/bin/env ruby

begin
  number = Integer(ARGV[0])
  puts number
rescue ArgumentError
  warn "Invalid number"
  exit 1
end
```

### 28.2 Exceptions réseau courantes

À gérer :

- `SocketError` : DNS ou host invalide ;
- `Errno::ECONNREFUSED` : port fermé ;
- `Timeout::Error` : timeout ;
- `OpenSSL::SSL::SSLError` : problème TLS ;
- `JSON::ParserError` : JSON invalide ;
- `Net::ReadTimeout` : lecture trop lente ;
- `Net::OpenTimeout` : connexion trop lente.

### 28.3 Ne pas cacher toutes les erreurs

Mauvais réflexe : tout attraper sans rien afficher.

Meilleur réflexe : afficher un message clair et sortir avec un code d'erreur.

```ruby
#!/usr/bin/env ruby

begin
  content = File.read(ARGV[0])
  puts content
rescue Errno::ENOENT
  warn "File not found"
  exit 1
rescue Errno::EACCES
  warn "Permission denied"
  exit 1
end
```

---

## 29. Architecture recommandée d'un projet Ruby cyber

Structure simple :

```text
ruby_scripting/
├── README.md
├── Gemfile
├── bin/
│   ├── header_check.rb
│   ├── port_scan.rb
│   └── file_hashes.rb
├── lib/
│   ├── validators.rb
│   ├── http_client.rb
│   ├── report_writer.rb
│   └── scanners/
│       ├── header_scanner.rb
│       └── port_scanner.rb
├── spec/
│   ├── validators_spec.rb
│   └── header_scanner_spec.rb
├── reports/
└── data/
```

### 29.1 Rôle des dossiers

- `bin/` : scripts exécutables ;
- `lib/` : logique réutilisable ;
- `spec/` : tests ;
- `reports/` : sorties générées ;
- `data/` : fichiers d'entrée de lab ;
- `README.md` : documentation.

### 29.2 README minimal

````markdown
# Ruby Scripting Cyber

## Description

Scripts Ruby pédagogiques pour automatiser des tâches défensives et de reconnaissance en environnement autorisé.

## Requirements

- Kali Linux
- Ruby 3.x
- RubyGems

## Installation

```bash
bundle install
```

## Usage

```bash
./bin/header_check.rb https://example.local
./bin/port_scan.rb 127.0.0.1
```

## Ethical Notice

Use only against systems you own or are explicitly authorized to test.

## Files

- `bin/header_check.rb`: checks common HTTP security headers
- `bin/port_scan.rb`: simple educational TCP scanner
- `bin/file_hashes.rb`: computes file hashes
````

---

## 30. Méthodologie de travail pour les exercices

Pour chaque script :

1. lire le sujet ;
2. identifier les entrées ;
3. identifier les sorties attendues ;
4. écrire un pseudo-code ;
5. créer le fichier avec le shebang ;
6. gérer les arguments ;
7. gérer les erreurs ;
8. coder la logique minimale ;
9. tester un cas nominal ;
10. tester un cas d'erreur ;
11. vérifier le style ;
12. vérifier l'exécutable ;
13. vérifier la newline finale ;
14. documenter dans le README.

### 30.1 Exemple de pseudo-code

Pour un scanner de headers :

```text
Lire URL depuis ARGV[0]
Si URL absente, afficher usage et exit 1
Construire URI
Envoyer GET
Récupérer headers
Pour chaque header recommandé
  Si présent, afficher OK
  Sinon, afficher WARN
Gérer erreurs réseau
```

### 30.2 Tests manuels utiles

Toujours tester :

```bash
./script.rb
./script.rb invalid
./script.rb valid_input
```

Puis :

```bash
ruby -c script.rb
```

`ruby -c` vérifie la syntaxe.

---

## 31. Mini-projets progressifs

### Mini-projet 1 — Hello Cyber Ruby

Objectif : créer un script avec fonction et classe.

Contraintes :

- shebang obligatoire ;
- argument utilisateur ;
- affichage propre ;
- pas de symboles interdits.

Fonctionnalités :

- `greet(name)` ;
- classe `CyberGreeter` ;
- sortie : `Hello <name>, welcome to Ruby Cyber`.

### Mini-projet 2 — File Analyzer

Objectif : analyser un fichier texte.

Fonctionnalités :

- compter les lignes ;
- compter les mots ;
- compter les caractères ;
- afficher les 10 lignes les plus longues ;
- gérer fichier absent.

Utilité cyber : parsing de logs.

### Mini-projet 3 — Hash Reporter

Objectif : calculer les hashs d'un fichier.

Fonctionnalités :

- MD5 ;
- SHA1 ;
- SHA256 ;
- sortie texte ;
- sortie JSON optionnelle.

Utilité cyber : malware analysis, forensic, intégrité.

### Mini-projet 4 — HTTP Header Auditor

Objectif : vérifier les headers de sécurité d'une URL autorisée.

Fonctionnalités :

- status code ;
- Server header ;
- HSTS ;
- CSP ;
- X-Frame-Options ;
- X-Content-Type-Options ;
- Referrer-Policy ;
- rapport `.txt`.

### Mini-projet 5 — Local Port Scanner

Objectif : scanner uniquement une cible de lab.

Fonctionnalités :

- cible via argument ;
- ports courants ;
- timeout ;
- sortie claire ;
- pas de scan massif.

### Mini-projet 6 — SQLite Findings Tracker

Objectif : stocker des observations de sécurité.

Fonctionnalités :

- créer DB ;
- ajouter finding ;
- lister findings ;
- filtrer par sévérité ;
- exporter JSON.

### Mini-projet 7 — Sinatra Lab API

Objectif : créer une petite API locale pour tester tes scripts.

Endpoints :

- `/status` ;
- `/headers-demo` ;
- `/echo?q=test` ;
- `/findings`.

But : avoir une cible locale maîtrisée.

---

## 32. Checklist finale avant rendu

Avant de push :

```text
[ ] Chaque fichier Ruby commence par #!/usr/bin/env ruby
[ ] Chaque fichier finit par une newline
[ ] Chaque script est exécutable
[ ] Aucun script n'utilise les opérateurs interdits par le sujet
[ ] Les arguments sont validés
[ ] Les erreurs sont gérées
[ ] README.md présent à la racine
[ ] Usage documenté
[ ] Tests manuels faits
[ ] Pas de secret dans le dépôt
[ ] Pas de scan agressif
[ ] Pas de cible non autorisée codée en dur
[ ] ruby -c passe sur les scripts
[ ] Le code est lisible et découpé
```

Commandes utiles :

```bash
find . -name "*.rb" -type f -exec ruby -c {} \;
find . -name "*.rb" -type f -exec chmod +x {} \;
```

Attention : ces commandes shell utilisent un point-virgule échappé pour `find`. Cette contrainte d'interdiction concerne les scripts Ruby du projet, pas nécessairement les commandes tapées dans le terminal. Si ton checker interdit aussi le caractère dans certains fichiers, n'inclus pas ces commandes dans les scripts rendus.

---

## 33. Cheat sheet Ruby cyber

### 33.1 Arguments CLI

```ruby
#!/usr/bin/env ruby

if ARGV.empty?
  warn "Usage: ./script.rb <target>"
  exit 1
end

target = ARGV[0]
```

### 33.2 Lire fichier

```ruby
#!/usr/bin/env ruby

File.foreach("input.txt") do |line|
  puts line
end
```

### 33.3 Écrire fichier

```ruby
#!/usr/bin/env ruby

File.write("output.txt", "hello\n")
```

### 33.4 JSON

```ruby
#!/usr/bin/env ruby

require "json"

data = { status: "ok" }
puts JSON.generate(data)
```

### 33.5 HTTP GET

```ruby
#!/usr/bin/env ruby

require "net/http"
require "uri"

uri = URI("https://example.com")
response = Net::HTTP.get_response(uri)
puts response.code
```

### 33.6 Socket TCP

```ruby
#!/usr/bin/env ruby

require "socket"

socket = TCPSocket.new("127.0.0.1", 80)
socket.close
```

### 33.7 Timeout

```ruby
#!/usr/bin/env ruby

require "timeout"

Timeout.timeout(2) do
  puts "task"
end
```

### 33.8 Hash SHA256

```ruby
#!/usr/bin/env ruby

require "digest"

puts Digest::SHA256.hexdigest("hello")
```

### 33.9 SQLite insert paramétré

```ruby
#!/usr/bin/env ruby

require "sqlite3"

db = SQLite3::Database.new("app.db")
db.execute("INSERT INTO logs(message) VALUES (?)", ["hello"])
```

### 33.10 Règles d'or

- Toujours valider `ARGV` ;
- toujours gérer les exceptions ;
- toujours limiter les timeouts ;
- toujours documenter le scope ;
- toujours écrire un README ;
- ne jamais coder une cible réelle en dur ;
- ne jamais stocker de secret dans Git ;
- privilégier les tests non destructifs ;
- prouver l'impact sans dépasser le périmètre ;
- garder des logs clairs.

---

## 34. Sources et documentation officielle

Ressources principales utilisées et recommandées :

- Ruby official documentation : https://docs.ruby-lang.org/
- Ruby language documentation portal : https://www.ruby-lang.org/en/documentation/
- Ruby Net::HTTP documentation : https://docs.ruby-lang.org/en/master/Net/HTTP.html
- Ruby Socket documentation : https://docs.ruby-lang.org/en/master/Socket.html
- Ruby IO documentation : https://docs.ruby-lang.org/en/master/IO.html
- Ruby File documentation : https://docs.ruby-lang.org/en/master/File.html
- Ruby JSON documentation : https://docs.ruby-lang.org/en/master/JSON.html
- Ruby Digest documentation : https://docs.ruby-lang.org/en/master/Digest.html
- Ruby OpenURI documentation : https://docs.ruby-lang.org/en/master/OpenURI.html
- HTTParty documentation : https://github.com/jnunemaker/httparty
- Net::SSH documentation : https://www.rubydoc.info/gems/net-ssh/Net/SSH
- Net::Ping documentation : https://www.rubydoc.info/gems/net-ping
- ruby-pcap documentation : https://github.com/ahobson/ruby-pcap
- pcaprub documentation : https://www.rubydoc.info/gems/pcaprub
- SQLite3 Ruby gem : https://github.com/sparklemotion/sqlite3-ruby
- Sinatra documentation : https://sinatrarb.com/
- RSpec documentation : https://rspec.info/documentation/
- Mechanize documentation : https://www.rubydoc.info/gems/mechanize/Mechanize
- OWASP SQL Injection : https://owasp.org/www-community/attacks/SQL_Injection
- OWASP SQL Injection Prevention Cheat Sheet : https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- OWASP XSS : https://owasp.org/www-community/attacks/xss/
- OWASP XSS Prevention Cheat Sheet : https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
- OWASP Path Traversal : https://owasp.org/www-community/attacks/Path_Traversal
- OWASP Web Security Testing Guide : https://owasp.org/www-project-web-security-testing-guide/
- Metasploit Framework : https://www.metasploit.com/
- Metasploit Unleashed : https://www.offsec.com/metasploit-unleashed/

---

## Conclusion

Ruby est un excellent langage pour apprendre le scripting cyber parce qu'il combine :

- syntaxe lisible ;
- orientation objet simple ;
- bibliothèque standard utile ;
- gems réseau et web ;
- lien historique avec Metasploit ;
- rapidité de prototypage ;
- bonne capacité d'automatisation.

Pour ton module, le plus important n'est pas de copier des scripts, mais de comprendre la logique :

1. entrée utilisateur ;
2. validation ;
3. action contrôlée ;
4. gestion d'erreur ;
5. sortie claire ;
6. documentation ;
7. respect du scope.

Un bon script cyber n'est pas seulement un script qui “fonctionne”. C'est un script qui fonctionne proprement, qui respecte le cadre légal, qui produit des résultats exploitables, et qui échoue sans casser l'environnement.

