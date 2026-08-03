# Mobile Application Fundamentals and Security

> **Niveau :** Novice  
> **Projet :** Mobile Application Fundamentals and Security  
> **Autrice du projet :** Oumayma Mizouni  
> **Objectif du cours :** comprendre le fonctionnement d'une application mobile Android, savoir la développer, la compiler, l'installer, l'observer et identifier ses principaux risques de sécurité.  
> **Version du support :** août 2026

---

## Avertissement éthique et cadre de travail

Ce cours aborde le développement, l'analyse et les tests de sécurité d'applications Android.

Toutes les manipulations doivent être réalisées uniquement sur :

- une application que tu as développée ;
- une application volontairement vulnérable destinée à l'apprentissage ;
- un appareil ou un émulateur qui t'appartient ;
- une application pour laquelle tu disposes d'une autorisation explicite de test.

L'objectif est de comprendre les risques afin de mieux protéger les utilisateurs, pas de contourner les protections d'applications tierces.

---

# Table des matières

1. [Pourquoi étudier la sécurité mobile ?](#1-pourquoi-étudier-la-sécurité-mobile-)
2. [Panorama des applications mobiles](#2-panorama-des-applications-mobiles)
3. [Architecture générale d'Android](#3-architecture-générale-dandroid)
4. [Structure d'un projet Android](#4-structure-dun-projet-android)
5. [Les composants fondamentaux d'une application Android](#5-les-composants-fondamentaux-dune-application-android)
6. [Le fichier AndroidManifest.xml](#6-le-fichier-androidmanifestxml)
7. [Développement avec Android Studio, Kotlin et Java](#7-développement-avec-android-studio-kotlin-et-java)
8. [Compilation, APK, AAB, signature et installation](#8-compilation-apk-aab-signature-et-installation)
9. [Cycle de vie d'une Activity et gestion de l'état](#9-cycle-de-vie-dune-activity-et-gestion-de-létat)
10. [Intents, communication et navigation](#10-intents-communication-et-navigation)
11. [Permissions Android](#11-permissions-android)
12. [Modèle de sécurité Android](#12-modèle-de-sécurité-android)
13. [Principales menaces contre les applications mobiles](#13-principales-menaces-contre-les-applications-mobiles)
14. [Stockage sécurisé des données](#14-stockage-sécurisé-des-données)
15. [Cryptographie et Android Keystore](#15-cryptographie-et-android-keystore)
16. [Authentification, session et autorisation](#16-authentification-session-et-autorisation)
17. [Communications réseau sécurisées](#17-communications-réseau-sécurisées)
18. [Sécurité des composants, Intents et IPC](#18-sécurité-des-composants-intents-et-ipc)
19. [WebView, liens profonds et contenu web](#19-webview-liens-profonds-et-contenu-web)
20. [Validation des entrées et sécurité des API](#20-validation-des-entrées-et-sécurité-des-api)
21. [Logs, sauvegardes, presse-papiers et captures d'écran](#21-logs-sauvegardes-presse-papiers-et-captures-décran)
22. [Dépendances, mises à jour et chaîne d'approvisionnement](#22-dépendances-mises-à-jour-et-chaîne-dapprovisionnement)
23. [Introduction à l'analyse statique et dynamique](#23-introduction-à-lanalyse-statique-et-dynamique)
24. [Mise en place d'un laboratoire Android sécurisé](#24-mise-en-place-dun-laboratoire-android-sécurisé)
25. [ADB : observer et piloter Android](#25-adb--observer-et-piloter-android)
26. [Analyse statique avec JADX et APKTool](#26-analyse-statique-avec-jadx-et-apktool)
27. [Analyse dynamique avec Frida et MobSF](#27-analyse-dynamique-avec-frida-et-mobsf)
28. [Méthodologie d'audit mobile](#28-méthodologie-daudit-mobile)
29. [Ateliers pratiques guidés](#29-ateliers-pratiques-guidés)
30. [Checklist de développement sécurisé](#30-checklist-de-développement-sécurisé)
31. [Erreurs fréquentes à éviter](#31-erreurs-fréquentes-à-éviter)
32. [Quiz de révision](#32-quiz-de-révision)
33. [Questions ouvertes de révision](#33-questions-ouvertes-de-révision)
34. [Glossaire](#34-glossaire)
35. [Ressources officielles](#35-ressources-officielles)

---

# 1. Pourquoi étudier la sécurité mobile ?

Un smartphone contient souvent davantage d'informations personnelles qu'un ordinateur traditionnel :

- comptes de messagerie ;
- photos et documents ;
- contacts ;
- position géographique ;
- données bancaires ;
- jetons de session ;
- informations de santé ;
- accès à la caméra, au microphone et aux capteurs.

Une application mobile se situe au croisement de plusieurs systèmes :

```text
Utilisateur
   │
   ▼
Interface mobile
   │
   ├── Stockage local
   ├── Système Android
   ├── Capteurs et permissions
   ├── API distante
   ├── Services cloud
   └── Applications tierces
```

Une faiblesse dans une seule de ces zones peut exposer l'ensemble du système.

## 1.1 Exemple simple

Une application stocke un jeton de connexion dans un fichier lisible ou dans les logs :

```text
Authorization: Bearer eyJhbGciOi...
```

Une autre personne, un logiciel malveillant ou un attaquant ayant accès à l'appareil pourrait récupérer ce jeton et se connecter à la place de l'utilisateur.

## 1.2 La sécurité mobile ne se limite pas au téléphone

Il faut protéger :

1. l'application installée ;
2. les données présentes sur l'appareil ;
3. les communications réseau ;
4. l'API et le serveur ;
5. les comptes utilisateurs ;
6. la chaîne de compilation et de distribution.

Une application mobile sécurisée avec une API vulnérable reste une application vulnérable.

---

# 2. Panorama des applications mobiles

Il existe trois grandes familles d'applications mobiles.

## 2.1 Application native

Une application native est développée spécifiquement pour une plateforme.

Pour Android, on utilise principalement :

- Kotlin ;
- Java ;
- Android SDK ;
- éventuellement du code natif C ou C++ avec le NDK.

### Avantages

- accès complet aux fonctionnalités Android ;
- bonnes performances ;
- intégration naturelle avec l'interface du système ;
- accès aux API de sécurité Android.

### Inconvénients

- développement séparé pour Android et iOS ;
- maintenance de plusieurs bases de code possible.

## 2.2 Application web mobile

Une application web mobile est un site web adapté aux petits écrans et consulté depuis un navigateur.

Technologies classiques :

- HTML ;
- CSS ;
- JavaScript ;
- frameworks web.

### Avantages

- une seule base de code ;
- mise à jour côté serveur ;
- pas forcément besoin d'installation.

### Inconvénients

- accès plus limité aux fonctions du téléphone ;
- dépendance au navigateur ;
- exposition aux risques classiques du Web : XSS, CSRF, injections, mauvaise gestion de session.

## 2.3 Application hybride ou multiplateforme

Une application hybride utilise une base de code commune pour plusieurs plateformes.

Exemples de technologies :

- Flutter ;
- React Native ;
- Ionic ou Capacitor ;
- Kotlin Multiplatform.

Certaines applications hybrides affichent une partie de leur interface dans une `WebView`. D'autres génèrent une interface native.

### Risques spécifiques

- pont entre JavaScript et code natif ;
- plugins tiers vulnérables ;
- configuration différente entre Android et iOS ;
- secrets parfois inclus dans le code compilé ;
- surface d'attaque plus large.

## 2.4 Comparaison

| Critère | Native | Web | Hybride/multiplateforme |
|---|---|---|---|
| Installation | Oui | Pas toujours | Oui |
| Accès matériel | Excellent | Limité | Bon à excellent |
| Performances | Très bonnes | Variables | Bonnes |
| Base de code partagée | Non, généralement | Oui | Oui |
| Risques dominants | Android, IPC, stockage | Web | Web + mobile + plugins |

---

# 3. Architecture générale d'Android

Android est construit par couches.

```text
┌──────────────────────────────────────────────┐
│ Applications                                │
├──────────────────────────────────────────────┤
│ Android Framework                           │
│ Activity Manager, Package Manager, Binder…  │
├──────────────────────────────────────────────┤
│ Android Runtime + bibliothèques natives      │
│ ART, libc, SQLite, SSL/TLS…                  │
├──────────────────────────────────────────────┤
│ Hardware Abstraction Layer                  │
├──────────────────────────────────────────────┤
│ Noyau Linux                                 │
└──────────────────────────────────────────────┘
```

## 3.1 Le noyau Linux

Android s'appuie sur Linux pour :

- la gestion des processus ;
- la mémoire ;
- les utilisateurs et permissions système ;
- les pilotes matériels ;
- le réseau ;
- l'isolation.

## 3.2 Android Runtime : ART

ART exécute le bytecode Android contenu dans les fichiers DEX.

Le code Kotlin ou Java suit approximativement ce chemin :

```text
Kotlin/Java
   ↓ compilation
Bytecode JVM
   ↓ conversion
DEX
   ↓ exécution/optimisation
ART
```

## 3.3 Le framework Android

Le framework fournit les API utilisées par les développeurs :

- `Activity` ;
- `Service` ;
- notifications ;
- stockage ;
- réseau ;
- biométrie ;
- caméra ;
- localisation ;
- permissions.

## 3.4 Binder et IPC

Android utilise notamment Binder pour la communication interprocessus, appelée IPC.

Une application peut demander une opération à un service système sans accéder directement à ses données internes.

```text
Application A ── Binder/Intent ──> Service système ou Application B
```

Cette communication doit être contrôlée : identité de l'appelant, permissions, validation des données et exposition des composants.

---

# 4. Structure d'un projet Android

Exemple simplifié :

```text
MyApplication/
├── app/
│   ├── build.gradle.kts
│   └── src/
│       └── main/
│           ├── AndroidManifest.xml
│           ├── java/com/example/app/
│           │   └── MainActivity.kt
│           └── res/
│               ├── drawable/
│               ├── layout/
│               ├── mipmap/
│               ├── values/
│               └── xml/
├── build.gradle.kts
├── settings.gradle.kts
└── gradlew
```

## 4.1 Dossier `java` ou `kotlin`

Il contient le code source de l'application.

## 4.2 Dossier `res`

Il contient les ressources :

- interfaces XML ;
- images ;
- icônes ;
- couleurs ;
- chaînes de caractères ;
- configurations XML.

## 4.3 `AndroidManifest.xml`

Il décrit au système :

- les composants ;
- les permissions ;
- les intent filters ;
- les caractéristiques de l'application ;
- certaines options de sécurité.

## 4.4 Gradle

Gradle gère :

- la compilation ;
- les dépendances ;
- les variantes `debug` et `release` ;
- les versions du SDK ;
- la signature ;
- la génération de l'APK ou de l'AAB.

Exemple simplifié :

```kotlin
android {
    namespace = "com.example.secureapp"
    compileSdk = 36

    defaultConfig {
        applicationId = "com.example.secureapp"
        minSdk = 26
        targetSdk = 36
        versionCode = 1
        versionName = "1.0"
    }
}
```

Les numéros exacts de SDK évoluent. Il faut utiliser une version maintenue et tester les changements de comportement associés.

---

# 5. Les composants fondamentaux d'une application Android

Android reconnaît quatre composants principaux :

1. `Activity` ;
2. `Service` ;
3. `BroadcastReceiver` ;
4. `ContentProvider`.

Les `Fragments` sont également très importants pour structurer l'interface, même s'ils ne font pas partie des quatre composants système fondamentaux.

## 5.1 Activity

Une `Activity` représente généralement un écran ou un point d'interaction avec l'utilisateur.

Exemples :

- écran de connexion ;
- liste de messages ;
- profil utilisateur ;
- écran de paiement.

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}
```

### Point de sécurité

Une `Activity` exportée peut être démarrée par une autre application. Elle doit donc traiter toutes les données entrantes comme non fiables.

## 5.2 Fragment

Un `Fragment` représente une portion réutilisable d'interface et de logique.

Exemple : sur tablette, une Activity peut afficher en même temps :

- un fragment contenant une liste ;
- un fragment contenant le détail sélectionné.

Un Fragment possède son propre cycle de vie, lié à celui de son Activity et de sa vue.

### Point de sécurité

Ne conserve pas de données sensibles dans une vue plus longtemps que nécessaire. Fais attention aux références qui survivent à la destruction de la vue.

## 5.3 Service

Un `Service` exécute une tâche sans interface graphique directe.

Exemples :

- lecture audio ;
- synchronisation ;
- communication avec un équipement ;
- opération liée à une notification persistante.

```kotlin
class SyncService : Service() {
    override fun onBind(intent: Intent?): IBinder? = null

    override fun onStartCommand(
        intent: Intent?,
        flags: Int,
        startId: Int
    ): Int {
        // Travail à effectuer
        return START_NOT_STICKY
    }
}
```

### Attention

Un Service ne crée pas automatiquement un nouveau thread. Du travail lourd exécuté sur le thread principal peut bloquer l'application.

Pour beaucoup de tâches planifiées ou différables, `WorkManager` est préférable.

### Point de sécurité

- ne pas exporter un service inutilement ;
- utiliser des Intents explicites ;
- contrôler l'identité d'un client qui se connecte à un service sensible.

## 5.4 BroadcastReceiver

Un `BroadcastReceiver` reçoit des événements diffusés par Android ou une application.

Exemples :

- démarrage du téléphone ;
- changement de connectivité ;
- événement interne à l'application.

```kotlin
class BatteryReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        // Réaction rapide à l'événement
    }
}
```

`onReceive()` doit rester court. Pour un travail plus long, il faut déléguer à un mécanisme adapté.

### Point de sécurité

Un broadcast personnalisé non protégé peut être falsifié par une autre application. Limite l'exportation, utilise des permissions appropriées et valide le contenu reçu.

## 5.5 ContentProvider

Un `ContentProvider` fournit une interface structurée pour accéder ou partager des données.

Exemples :

- contacts ;
- calendrier ;
- données partagées entre applications autorisées.

Les données sont souvent référencées par des URI :

```text
content://com.example.notes.provider/notes/42
```

### Point de sécurité

Un provider mal protégé peut permettre :

- lecture de données sensibles ;
- modification ou suppression ;
- injection dans une requête ;
- contournement d'autorisation.

S'il n'est pas destiné aux autres applications :

```xml
android:exported="false"
```

---

# 6. Le fichier AndroidManifest.xml

Exemple :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="false"
        android:label="SecureApp"
        android:theme="@style/Theme.SecureApp">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <service
            android:name=".InternalSyncService"
            android:exported="false" />

    </application>
</manifest>
```

## 6.1 Informations importantes

Le manifeste peut déclarer :

- package et namespace ;
- permissions demandées ;
- Activities, Services, Receivers et Providers ;
- composants exportés ;
- liens profonds ;
- configuration réseau ;
- sauvegarde ;
- version minimale d'Android ;
- fonctionnalités matérielles.

## 6.2 `android:exported`

- `true` : le composant peut être accessible depuis l'extérieur selon ses filtres et protections ;
- `false` : le composant est limité à l'application ou à certains composants partageant le même UID.

Règle simple :

> Tout composant qui n'a pas besoin d'être public doit être explicitement déclaré non exporté.

## 6.3 Principe du moindre privilège

Le manifeste doit demander uniquement ce qui est nécessaire.

Mauvais exemple : une application lampe torche demandant contacts, microphone, SMS et position.

Bon exemple : elle demande seulement l'accès réellement indispensable à sa fonction.

---

# 7. Développement avec Android Studio, Kotlin et Java

## 7.1 Android Studio

Android Studio est l'environnement officiel de développement Android.

Il fournit :

- éditeur de code ;
- gestion de Gradle ;
- émulateur ;
- débogueur ;
- Logcat ;
- inspecteurs réseau, mémoire et interface ;
- outils de compilation et de signature.

## 7.2 Kotlin

Kotlin est concis, moderne et officiellement privilégié dans l'écosystème Android.

```kotlin
fun greet(name: String): String {
    return "Bonjour $name"
}
```

### Gestion du `null`

```kotlin
var token: String? = null

val length = token?.length ?: 0
```

La null-safety réduit une partie des erreurs, mais ne protège pas contre les failles de logique ou de sécurité.

## 7.3 Java

Java reste largement utilisé.

```java
public String greet(String name) {
    return "Bonjour " + name;
}
```

Une application peut mélanger Java et Kotlin.

## 7.4 Jetpack Compose et vues XML

Deux approches principales existent pour l'interface :

- vues XML classiques ;
- Jetpack Compose.

Exemple Compose :

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Bonjour $name")
}
```

La sécurité fondamentale reste la même : ne pas faire confiance aux entrées, limiter les permissions, protéger le stockage, la communication et les composants.

---

# 8. Compilation, APK, AAB, signature et installation

## 8.1 Qu'est-ce qu'un APK ?

APK signifie **Android Package Kit**.

C'est une archive contenant notamment :

```text
AndroidManifest.xml
classes.dex
resources.arsc
res/
assets/
lib/
META-INF/
```

### `classes.dex`

Contient le bytecode Dalvik/ART.

### `lib/`

Contient les bibliothèques natives compilées, par architecture :

```text
lib/arm64-v8a/
lib/armeabi-v7a/
lib/x86_64/
```

### `META-INF/`

Contient des informations liées à la signature selon le format utilisé.

## 8.2 Qu'est-ce qu'un AAB ?

AAB signifie **Android App Bundle**.

Un AAB n'est pas généralement le fichier installé directement par l'utilisateur. Il permet à un distributeur comme Google Play de générer des APK adaptés à l'appareil : langue, architecture, densité d'écran, etc.

## 8.3 Build debug et release

### Debug

- conçu pour le développement ;
- débogage activé ;
- signé avec une clé de debug ;
- non destiné à la production.

### Release

- optimisé ;
- signé avec la clé de publication ;
- peut activer la réduction et l'obfuscation ;
- doit désactiver les comportements de test.

## 8.4 Compiler en ligne de commande

Depuis la racine du projet :

```bash
./gradlew assembleDebug
```

APK attendu :

```text
app/build/outputs/apk/debug/app-debug.apk
```

Installer directement la variante debug :

```bash
./gradlew installDebug
```

## 8.5 Installer avec ADB

```bash
adb devices
adb install app-debug.apk
```

Réinstaller en conservant les données :

```bash
adb install -r app-debug.apk
```

Désinstaller :

```bash
adb uninstall com.example.secureapp
```

## 8.6 Signature de l'application

Android exige que les APK soient signés.

La signature permet notamment :

- d'identifier le développeur ou la clé de publication ;
- d'autoriser les mises à jour uniquement avec la clé compatible ;
- d'établir des relations de confiance entre applications signées avec la même clé ;
- de protéger certaines permissions de niveau `signature`.

### Risque critique

La clé de signature de production ne doit jamais être :

- placée dans Git ;
- partagée dans un chat ;
- intégrée à l'APK ;
- stockée sans protection ;
- utilisée sur un poste non maîtrisé.

---

# 9. Cycle de vie d'une Activity et gestion de l'état

Android peut créer, mettre en pause, arrêter ou détruire une Activity selon les actions de l'utilisateur et les besoins en mémoire.

## 9.1 Les six callbacks principaux

```text
onCreate()
    ↓
onStart()
    ↓
onResume()
    ↓
[Activity active]
    ↓
onPause()
    ↓
onStop()
    ↓
onDestroy()
```

Une reprise peut suivre :

```text
onStop() → onRestart() → onStart() → onResume()
```

## 9.2 Rôle de chaque méthode

### `onCreate()`

- initialisation ;
- chargement de l'interface ;
- création des objets essentiels ;
- restauration d'un état sauvegardé.

### `onStart()`

L'Activity devient visible.

### `onResume()`

L'utilisateur peut interagir avec elle.

### `onPause()`

L'Activity perd le focus. Il faut suspendre les opérations sensibles à l'interaction.

### `onStop()`

L'Activity n'est plus visible. Libère les ressources qui ne sont plus nécessaires.

### `onDestroy()`

L'Activity est détruite. Cette méthode ne doit pas être considérée comme une garantie absolue pour sauvegarder une donnée critique.

## 9.3 Exemple de journalisation

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d("LIFECYCLE", "onCreate")
    }

    override fun onStart() {
        super.onStart()
        Log.d("LIFECYCLE", "onStart")
    }

    override fun onResume() {
        super.onResume()
        Log.d("LIFECYCLE", "onResume")
    }

    override fun onPause() {
        Log.d("LIFECYCLE", "onPause")
        super.onPause()
    }

    override fun onStop() {
        Log.d("LIFECYCLE", "onStop")
        super.onStop()
    }

    override fun onDestroy() {
        Log.d("LIFECYCLE", "onDestroy")
        super.onDestroy()
    }
}
```

Observation :

```bash
adb logcat -s LIFECYCLE
```

## 9.4 Rotation d'écran

Une rotation peut provoquer la recréation de l'Activity.

Il ne faut pas supposer qu'une variable en mémoire survivra :

```kotlin
private var counter = 0
```

Solutions possibles :

- `ViewModel` pour l'état d'interface ;
- `SavedStateHandle` ;
- `onSaveInstanceState()` pour un petit état temporaire ;
- stockage persistant pour les données qui doivent survivre à la fermeture.

## 9.5 Importance pour la sécurité

Lorsqu'une Activity passe en arrière-plan :

- arrêter une caméra ou un microphone qui n'est plus nécessaire ;
- masquer ou effacer les données très sensibles ;
- éviter de laisser un secret dans une variable globale ;
- verrouiller de nouveau une fonction critique après un délai ;
- vérifier l'état de session au retour.

---

# 10. Intents, communication et navigation

Un `Intent` décrit une action à effectuer.

Il peut être utilisé pour :

- démarrer une Activity ;
- démarrer ou lier un Service ;
- envoyer un broadcast ;
- ouvrir un navigateur ;
- partager du contenu.

## 10.1 Intent explicite

La cible est clairement indiquée.

```kotlin
val intent = Intent(this, ProfileActivity::class.java)
intent.putExtra("user_id", 42)
startActivity(intent)
```

C'est généralement le choix le plus sûr pour une communication interne.

## 10.2 Intent implicite

L'action est décrite, mais Android choisit une application compatible.

```kotlin
val intent = Intent(Intent.ACTION_VIEW).apply {
    data = Uri.parse("https://example.com/help")
}
startActivity(intent)
```

Autre exemple :

```kotlin
val shareIntent = Intent(Intent.ACTION_SEND).apply {
    type = "text/plain"
    putExtra(Intent.EXTRA_TEXT, "Bonjour")
}
startActivity(Intent.createChooser(shareIntent, "Partager avec"))
```

## 10.3 Intent filter

Un composant déclare les Intents qu'il sait traiter.

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data
        android:scheme="https"
        android:host="example.com" />
</intent-filter>
```

## 10.4 Risques

- interception d'un Intent implicite ;
- composant malveillant répondant à l'action ;
- données sensibles placées dans les extras ;
- redirection d'Intent ;
- composant exporté invoqué sans contrôle ;
- URI ou paramètre non validé.

## 10.5 Validation des extras

Mauvais :

```kotlin
val url = intent.getStringExtra("url")!!
startActivity(Intent(Intent.ACTION_VIEW, Uri.parse(url)))
```

Meilleur :

```kotlin
val rawUrl = intent.getStringExtra("url") ?: return
val uri = runCatching { Uri.parse(rawUrl) }.getOrNull() ?: return

if (uri.scheme == "https" && uri.host == "example.com") {
    startActivity(Intent(Intent.ACTION_VIEW, uri))
}
```

## 10.6 Ne pas démarrer un Service avec un Intent implicite

Pour un Service interne, indique explicitement la cible :

```kotlin
val intent = Intent(this, SyncService::class.java)
startService(intent)
```

---

# 11. Permissions Android

Les permissions protègent l'accès à certaines données et fonctions.

## 11.1 Grandes catégories

### Permissions normales

Elles présentent un risque limité et sont généralement accordées automatiquement.

Exemple : accès réseau avec `INTERNET`.

### Permissions d'exécution ou dangereuses

Elles donnent accès à des informations ou fonctions sensibles :

- caméra ;
- microphone ;
- localisation ;
- contacts ;
- téléphone ;
- capteurs sensibles selon la version.

Elles doivent être déclarées dans le manifeste et demandées à l'exécution lorsque nécessaire.

### Permissions spéciales

Elles nécessitent souvent un écran de paramètres dédié, par exemple certains accès système avancés.

### Permissions de niveau signature

Elles peuvent limiter un composant aux applications signées avec une clé autorisée.

## 11.2 Déclaration dans le manifeste

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

## 11.3 Demande à l'exécution avec Kotlin

```kotlin
private val cameraPermissionLauncher =
    registerForActivityResult(ActivityResultContracts.RequestPermission()) { granted ->
        if (granted) {
            openCamera()
        } else {
            showCameraUnavailableMessage()
        }
    }

fun requestCamera() {
    when {
        ContextCompat.checkSelfPermission(
            this,
            Manifest.permission.CAMERA
        ) == PackageManager.PERMISSION_GRANTED -> {
            openCamera()
        }

        shouldShowRequestPermissionRationale(Manifest.permission.CAMERA) -> {
            showRationaleThenRequest()
        }

        else -> {
            cameraPermissionLauncher.launch(Manifest.permission.CAMERA)
        }
    }
}
```

## 11.4 Bonne expérience utilisateur

Demande la permission :

- au moment où la fonctionnalité est utilisée ;
- avec une explication compréhensible ;
- sans bloquer toute l'application si elle n'est pas indispensable ;
- en prévoyant le refus ;
- en supprimant la permission si elle n'est plus nécessaire.

## 11.5 Principe du moindre privilège

Pose ces questions :

1. Cette permission est-elle réellement nécessaire ?
2. Une API système moins intrusive peut-elle remplacer cet accès ?
3. Peut-on demander un accès ponctuel ?
4. Peut-on utiliser une position approximative ?
5. L'application fonctionne-t-elle encore en cas de refus ?

## 11.6 Vérification avec ADB

```bash
adb shell dumpsys package com.example.secureapp
```

Permissions accordées :

```bash
adb shell pm list permissions -g
```

Accorder dans un laboratoire :

```bash
adb shell pm grant com.example.secureapp android.permission.CAMERA
```

Révoquer :

```bash
adb shell pm revoke com.example.secureapp android.permission.CAMERA
```

---

# 12. Modèle de sécurité Android

Android applique plusieurs couches de défense.

## 12.1 Sandbox par application

Chaque application reçoit généralement un UID Linux distinct.

Conséquence : une application ne peut pas lire directement les fichiers privés d'une autre application.

```text
App A → UID 10123 → /data/user/0/com.example.a/
App B → UID 10124 → /data/user/0/com.example.b/
```

La sandbox n'est pas une excuse pour stocker des secrets en clair. Elle réduit le risque, mais un appareil compromis, une sauvegarde, une mauvaise configuration ou une fuite applicative peuvent exposer les données.

## 12.2 Permissions

Les permissions contrôlent les accès sensibles au système et aux autres applications.

## 12.3 Signature

La signature permet à Android de vérifier la continuité des mises à jour et certaines relations de confiance.

## 12.4 SELinux

SELinux applique des politiques de contrôle d'accès obligatoires et limite les actions possibles, y compris pour certains processus privilégiés.

## 12.5 Verified Boot et intégrité

Android peut vérifier l'intégrité de plusieurs parties du système au démarrage. Cette protection concerne surtout la plateforme, pas les erreurs logiques de ton application.

## 12.6 Défense en profondeur

Aucune protection ne suffit seule.

```text
Permissions + sandbox + chiffrement + validation serveur + TLS + mises à jour
```

---

# 13. Principales menaces contre les applications mobiles

## 13.1 Malware

Une application malveillante peut :

- voler des identifiants ;
- afficher de fausses interfaces ;
- abuser des permissions ;
- lire les notifications ;
- enregistrer l'écran ou les frappes selon ses capacités ;
- détourner des communications.

## 13.2 Phishing

L'attaquant imite :

- une banque ;
- une page de connexion ;
- une notification ;
- une demande de permission ;
- un service connu.

L'application légitime doit aider l'utilisateur à reconnaître les actions sensibles et éviter les flux ambigus.

## 13.3 Fuite de données

Sources fréquentes :

- stockage en clair ;
- logs ;
- sauvegardes ;
- presse-papiers ;
- captures d'écran ;
- notifications ;
- cache WebView ;
- trafic HTTP ;
- analytics trop bavards ;
- erreurs serveur.

## 13.4 Authentification faible

Exemples :

- mot de passe sans limitation de tentatives ;
- jeton non expirant ;
- session non révoquée ;
- contrôle uniquement côté application ;
- absence de vérification pour une action critique.

## 13.5 Autorisation insuffisante

Exemple classique : l'application envoie :

```http
GET /api/users/42/invoices
```

Changer `42` en `43` ne doit jamais permettre d'accéder aux factures d'un autre utilisateur. Le serveur doit vérifier l'autorisation pour chaque objet et chaque action.

## 13.6 Communication non sécurisée

HTTP en clair ou mauvaise validation TLS :

- interception ;
- modification du trafic ;
- vol de session ;
- injection de contenu.

## 13.7 Composants exportés

Une Activity, un Service, un Receiver ou un Provider exposé peut devenir un point d'entrée inattendu.

## 13.8 Code et secrets embarqués

Tout contenu présent dans l'APK doit être considéré comme récupérable :

- URL d'API ;
- chaînes ;
- logique ;
- certificats publics ;
- clés codées en dur ;
- configurations.

L'obfuscation rend l'analyse plus difficile, mais ne transforme pas un secret embarqué en secret sûr.

---

# 14. Stockage sécurisé des données

## 14.1 Classifier les données

Avant de choisir un stockage, classe la donnée :

| Niveau | Exemple | Traitement attendu |
|---|---|---|
| Public | thème de l'application | stockage standard possible |
| Interne | préférence non sensible | stockage privé de l'application |
| Sensible | jeton de session | minimisation, protection cryptographique |
| Très sensible | clé privée, donnée bancaire | Keystore, durée minimale, contrôle renforcé |

## 14.2 Stockage interne privé

```kotlin
openFileOutput("settings.json", Context.MODE_PRIVATE).use { output ->
    output.write("{\"theme\":\"dark\"}".toByteArray())
}
```

`MODE_PRIVATE` empêche les autres applications ordinaires d'accéder directement au fichier.

## 14.3 SharedPreferences

Adapté à de petites préférences.

```kotlin
val prefs = getSharedPreferences("user_settings", MODE_PRIVATE)
prefs.edit()
    .putBoolean("notifications_enabled", true)
    .apply()
```

Ne place pas un mot de passe ou une clé d'API en clair dans des préférences.

## 14.4 Base SQLite ou Room

Room simplifie l'accès à SQLite et apporte une structure plus sûre au niveau du code.

```kotlin
@Entity
data class Note(
    @PrimaryKey val id: Long,
    val title: String,
    val content: String
)
```

Room ne chiffre pas automatiquement toute la base. La sensibilité des données doit guider une protection supplémentaire.

## 14.5 Stockage externe

Le stockage externe est moins adapté aux informations sensibles.

N'y place pas :

- jetons ;
- mots de passe ;
- clés ;
- documents confidentiels non protégés ;
- exécutables chargés dynamiquement sans vérification.

## 14.6 Minimisation

La meilleure donnée à protéger est parfois celle que l'application ne conserve pas.

Questions :

- en ai-je vraiment besoin ?
- combien de temps ?
- puis-je la stocker côté serveur ?
- puis-je conserver un identifiant opaque plutôt que la donnée ?
- puis-je l'effacer après utilisation ?

## 14.7 Mauvais exemple

```kotlin
prefs.edit()
    .putString("password", password)
    .apply()
```

## 14.8 Meilleure approche

- ne pas conserver le mot de passe ;
- utiliser un jeton de session court et révocable ;
- protéger les clés cryptographiques avec Android Keystore ;
- utiliser une authentification locale pour déverrouiller une opération sensible ;
- invalider les données à la déconnexion.

---

# 15. Cryptographie et Android Keystore

## 15.1 Objectif du Keystore

Android Keystore permet de générer ou stocker des clés cryptographiques de manière plus difficile à extraire.

Une clé peut être configurée pour :

- chiffrer/déchiffrer ;
- signer/vérifier ;
- exiger une authentification utilisateur ;
- utiliser uniquement certains algorithmes ;
- être protégée par du matériel sécurisé lorsque disponible.

## 15.2 Ne jamais coder une clé secrète en dur

Mauvais :

```kotlin
private const val SECRET_KEY = "SuperSecretKey123"
```

Elle sera présente dans l'APK et pourra être retrouvée.

## 15.3 Exemple de génération de clé AES

```kotlin
val keyGenerator = KeyGenerator.getInstance(
    KeyProperties.KEY_ALGORITHM_AES,
    "AndroidKeyStore"
)

val parameterSpec = KeyGenParameterSpec.Builder(
    "local_data_key",
    KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT
)
    .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
    .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
    .build()

keyGenerator.init(parameterSpec)
keyGenerator.generateKey()
```

## 15.4 Chiffrement authentifié

AES-GCM fournit :

- confidentialité ;
- contrôle d'intégrité/authenticité des données.

Il faut :

- générer un IV unique pour chaque chiffrement avec la même clé ;
- conserver l'IV avec le ciphertext ;
- vérifier l'échec d'authentification ;
- ne pas inventer son propre protocole cryptographique.

## 15.5 Hachage et chiffrement ne sont pas la même chose

### Hachage

Transformation à sens unique.

Utilisations :

- intégrité ;
- empreintes ;
- dérivation de mot de passe avec un algorithme adapté côté serveur.

### Chiffrement

Transformation réversible avec une clé.

Utilisation : confidentialité.

### Encodage Base64

Base64 n'est ni du chiffrement ni du hachage.

```text
secret → c2VjcmV0
```

Cette chaîne se décode immédiatement.

## 15.6 Règles pratiques

- utiliser les API standard ;
- éviter les algorithmes obsolètes ;
- protéger les clés, pas seulement les données ;
- séparer les usages des clés ;
- prévoir rotation et révocation ;
- ne jamais transmettre une clé secrète dans les logs.

---

# 16. Authentification, session et autorisation

## 16.1 Authentification

Répond à la question :

> Qui es-tu ?

Exemples : mot de passe, passkey, certificat, biométrie locale.

## 16.2 Autorisation

Répond à la question :

> As-tu le droit d'effectuer cette action sur cette ressource ?

L'autorisation doit être vérifiée côté serveur.

## 16.3 Biométrie

La biométrie peut permettre de confirmer localement l'identité de l'utilisateur ou de déverrouiller l'usage d'une clé.

Elle ne doit pas simplement remplacer une variable booléenne :

```kotlin
var isAuthenticated = true
```

Une valeur locale facilement modifiable ne constitue pas une autorisation serveur.

## 16.4 Jetons de session

Un bon jeton doit être :

- difficile à deviner ;
- transmis uniquement via TLS ;
- limité dans le temps ;
- révocable ;
- associé aux bons droits ;
- effacé à la déconnexion ;
- jamais écrit dans les logs.

## 16.5 Refresh token et access token

Architecture fréquente :

- access token : durée courte ;
- refresh token : permet d'obtenir un nouveau jeton, protection renforcée.

La durée et le modèle exact dépendent du risque. Le serveur doit pouvoir révoquer ou invalider une session compromise.

## 16.6 Ne pas faire confiance au client

Mauvais contrôle :

```kotlin
if (currentUser.role == "admin") {
    showAdminPanel()
}
```

Ce contrôle masque seulement l'interface. L'API doit vérifier le rôle à chaque opération administrative.

## 16.7 Actions sensibles

Pour un changement critique, prévoir une authentification récente ou renforcée :

- ajout d'un bénéficiaire ;
- changement d'adresse e-mail ;
- suppression de compte ;
- affichage d'une clé de récupération ;
- paiement.

---

# 17. Communications réseau sécurisées

## 17.1 Utiliser HTTPS/TLS

HTTP en clair permet l'observation et la modification du trafic.

Mauvais :

```text
http://api.example.com/login
```

Bon :

```text
https://api.example.com/login
```

TLS ne corrige pas une API mal autorisée. Il protège principalement le transport.

## 17.2 Network Security Configuration

Dans le manifeste :

```xml
<application
    android:networkSecurityConfig="@xml/network_security_config"
    ... />
```

Dans `res/xml/network_security_config.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="false">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

Cette configuration bloque le trafic en clair pour l'application selon la configuration et les bibliothèques utilisées.

## 17.3 Configuration debug séparée

Pour un laboratoire, une configuration de debug peut accepter un certificat utilisateur afin d'observer son propre trafic. Elle ne doit pas contaminer la production.

```xml
<debug-overrides>
    <trust-anchors>
        <certificates src="user" />
    </trust-anchors>
</debug-overrides>
```

## 17.4 Validation TLS

Ne jamais accepter tous les certificats.

Mauvais concept :

```text
HostnameVerifier = toujours vrai
TrustManager = accepte tout
```

Cela neutralise la protection contre l'interception.

## 17.5 Certificate pinning

Le pinning peut limiter les certificats acceptés, mais il augmente la complexité opérationnelle :

- renouvellement ;
- rotation ;
- certificat de secours ;
- risque de bloquer l'application.

Il doit répondre à un besoin réel et être conçu avec une stratégie de mise à jour.

## 17.6 Données sensibles dans les URL

Évite :

```http
GET /reset-password?token=secret-token
```

Les URL peuvent se retrouver dans :

- logs ;
- historiques ;
- outils de monitoring ;
- proxies ;
- analytics.

## 17.7 Timeouts et erreurs

Configure :

- timeout de connexion ;
- timeout de lecture ;
- nombre limité de nouvelles tentatives ;
- gestion sûre des erreurs ;
- absence de données internes dans les messages utilisateur.

---

# 18. Sécurité des composants, Intents et IPC

## 18.1 Composants non exportés par défaut

```xml
<activity
    android:name=".InternalAdminActivity"
    android:exported="false" />
```

## 18.2 Protection par permission de signature

```xml
<permission
    android:name="com.example.secureapp.INTERNAL_ACCESS"
    android:protectionLevel="signature" />

<service
    android:name=".PartnerService"
    android:exported="true"
    android:permission="com.example.secureapp.INTERNAL_ACCESS" />
```

## 18.3 PendingIntent

Un `PendingIntent` autorise une autre entité à exécuter ultérieurement une action avec l'identité de l'application créatrice.

Utiliser l'immuabilité par défaut :

```kotlin
val intent = Intent(this, MainActivity::class.java)

val pendingIntent = PendingIntent.getActivity(
    this,
    0,
    intent,
    PendingIntent.FLAG_IMMUTABLE
)
```

Si l'action ne doit être utilisable qu'une fois :

```kotlin
PendingIntent.FLAG_IMMUTABLE or PendingIntent.FLAG_ONE_SHOT
```

Évite un `PendingIntent` mutable et implicite.

## 18.4 Receiver dynamique

Pour un receiver réservé à l'application, utilise les API modernes permettant d'indiquer qu'il n'est pas exporté.

Conceptuellement :

```kotlin
ContextCompat.registerReceiver(
    context,
    receiver,
    filter,
    ContextCompat.RECEIVER_NOT_EXPORTED
)
```

## 18.5 Intent redirection

Une application reçoit un Intent dans un extra, puis le démarre sans validation.

Mauvais :

```kotlin
val nested = intent.getParcelableExtra<Intent>("next")
startActivity(nested)
```

Risque : l'attaquant peut influencer la destination ou les paramètres.

Il faut :

- éviter ce modèle ;
- utiliser une allowlist ;
- valider package, composant, action, données et extras ;
- utiliser `IntentSanitizer` lorsque pertinent.

## 18.6 ContentProvider et injections

Mauvais :

```kotlin
val selection = "username = '$username'"
```

Meilleur :

```kotlin
val selection = "username = ?"
val selectionArgs = arrayOf(username)
```

Même principe que pour SQL : paramètres plutôt que concaténation.

---

# 19. WebView, liens profonds et contenu web

## 19.1 WebView

Une `WebView` affiche du contenu web dans l'application.

```kotlin
webView.loadUrl("https://example.com")
```

## 19.2 JavaScript

JavaScript est désactivé par défaut dans une WebView classique. Ne l'active que si nécessaire.

```kotlin
webView.settings.javaScriptEnabled = false
```

## 19.3 `addJavaScriptInterface()`

Cette fonction crée un pont entre JavaScript et le code Android.

Elle ne doit être exposée qu'à du contenu entièrement maîtrisé.

Risques :

- appel de fonctions natives par du JavaScript non fiable ;
- vol de données ;
- actions privilégiées ;
- élargissement de l'impact d'une XSS.

## 19.4 Validation des URL

Mauvais :

```kotlin
webView.loadUrl(intent.getStringExtra("url")!!)
```

Meilleur :

```kotlin
val uri = intent.getStringExtra("url")
    ?.let(Uri::parse)
    ?: return

if (uri.scheme == "https" && uri.host == "app.example.com") {
    webView.loadUrl(uri.toString())
}
```

## 19.5 Liens profonds

Un deep link ouvre une partie précise de l'application.

Exemple :

```text
https://example.com/orders/123
```

Risques :

- paramètre non validé ;
- redirection ouverte ;
- contournement d'un écran d'authentification ;
- confusion entre plusieurs applications ;
- exposition d'identifiants dans l'URL.

## 19.6 Vérification d'état

Le fait qu'un lien ouvre directement `OrderActivity` ne dispense pas de vérifier :

- que l'utilisateur est connecté ;
- qu'il a accès à la commande ;
- que l'identifiant est valide ;
- que l'action demandée est autorisée.

---

# 20. Validation des entrées et sécurité des API

## 20.1 Toutes les entrées sont non fiables

Sources :

- champ utilisateur ;
- Intent ;
- deep link ;
- QR code ;
- fichier ;
- presse-papiers ;
- réponse API ;
- données d'une autre application ;
- stockage externe ;
- notification.

## 20.2 Validation côté client et côté serveur

### Côté client

- améliore l'expérience ;
- réduit les erreurs ;
- peut bloquer des formats évidents.

### Côté serveur

- est obligatoire pour la sécurité ;
- ne peut pas supposer que la requête vient de l'application officielle ;
- doit vérifier format, identité, droit et contexte.

## 20.3 Allowlist

Préférer une liste de valeurs autorisées.

```kotlin
val allowedSorts = setOf("date", "name", "price")
val safeSort = requestedSort.takeIf { it in allowedSorts } ?: "date"
```

## 20.4 Taille et format

Limiter :

- taille d'un fichier ;
- longueur d'une chaîne ;
- dimensions d'une image ;
- types MIME ;
- caractères ou formats autorisés ;
- profondeur d'un JSON.

## 20.5 Upload de fichier

Vérifier côté serveur :

- taille ;
- type réel ;
- contenu ;
- nom généré côté serveur ;
- emplacement non exécutable ;
- analyse de sécurité si nécessaire ;
- contrôle d'accès au téléchargement.

---

# 21. Logs, sauvegardes, presse-papiers et captures d'écran

## 21.1 Logs

Mauvais :

```kotlin
Log.d("AUTH", "token=$token password=$password")
```

Bon :

```kotlin
Log.d("AUTH", "Authentication request completed")
```

À exclure des logs :

- mot de passe ;
- jeton ;
- cookie ;
- clé ;
- numéro bancaire ;
- donnée médicale ;
- réponse serveur complète si sensible.

## 21.2 Sauvegardes

Selon la sensibilité, configure précisément la politique de sauvegarde. Ne suppose pas qu'un fichier privé n'apparaîtra jamais dans une sauvegarde.

Exemple conservateur :

```xml
<application
    android:allowBackup="false"
    ... />
```

Pour une application réelle, examine aussi les règles modernes de sauvegarde et de transfert de données plutôt que d'appliquer une option sans analyse.

## 21.3 Presse-papiers

Le presse-papiers peut être observé ou conservé selon le système et les applications.

Évite d'y placer automatiquement :

- mot de passe ;
- token ;
- phrase de récupération ;
- donnée bancaire.

## 21.4 Captures d'écran

Pour un écran très sensible, on peut empêcher les captures et l'affichage dans l'écran des applications récentes :

```kotlin
window.setFlags(
    WindowManager.LayoutParams.FLAG_SECURE,
    WindowManager.LayoutParams.FLAG_SECURE
)
```

À utiliser avec discernement : cela affecte l'accessibilité et l'expérience utilisateur.

## 21.5 Notifications

N'affiche pas de secret complet sur l'écran verrouillé.

Préférer :

```text
Vous avez reçu un nouveau message.
```

plutôt que :

```text
Votre code bancaire est 123456 et votre solde est...
```

---

# 22. Dépendances, mises à jour et chaîne d'approvisionnement

## 22.1 Pourquoi les dépendances sont un risque

Une application inclut souvent :

- bibliothèques réseau ;
- SDK analytics ;
- publicité ;
- paiement ;
- authentification ;
- traitement d'images ;
- code natif.

Une vulnérabilité ou compromission d'une dépendance peut affecter l'application.

## 22.2 Bonnes pratiques

- limiter le nombre de dépendances ;
- utiliser des sources fiables ;
- verrouiller et contrôler les versions ;
- suivre les avis de sécurité ;
- supprimer les bibliothèques inutilisées ;
- vérifier les permissions ajoutées indirectement ;
- contrôler les SDK tiers et leurs collectes de données ;
- mettre à jour régulièrement.

## 22.3 Obfuscation

R8 peut réduire et obfusquer une partie du code.

Cela aide à :

- réduire la taille ;
- rendre l'analyse moins directe ;
- limiter certaines informations lisibles.

Mais cela ne remplace pas :

- l'autorisation serveur ;
- le chiffrement ;
- la gestion des secrets ;
- la validation ;
- les contrôles d'accès.

## 22.4 Séparer les environnements

Ne mélange pas :

- production ;
- test ;
- développement.

Les builds de production ne doivent pas contenir :

- serveur de test ;
- compte de démonstration privilégié ;
- logs détaillés ;
- certificats de proxy de test ;
- fonctionnalité de contournement ;
- clé de debug.

---

# 23. Introduction à l'analyse statique et dynamique

## 23.1 Analyse statique

On examine l'application sans exécuter son code.

Éléments observés :

- manifeste ;
- permissions ;
- composants exportés ;
- chaînes ;
- ressources ;
- code décompilé ;
- bibliothèques natives ;
- configuration réseau ;
- secrets codés en dur ;
- URLs et endpoints.

Outils :

- JADX ;
- APKTool ;
- `apkanalyzer` ;
- `aapt` ;
- MobSF ;
- `strings`, `file`, `unzip`.

## 23.2 Analyse dynamique

On exécute l'application et observe son comportement.

Éléments observés :

- logs ;
- fichiers créés ;
- trafic réseau ;
- appels de fonctions ;
- réactions aux Intents ;
- permissions demandées ;
- comportement selon le cycle de vie ;
- stockage après connexion et déconnexion.

Outils :

- Android Studio ;
- ADB ;
- Logcat ;
- proxy d'interception dans un laboratoire autorisé ;
- Frida ;
- MobSF Dynamic Analyzer.

## 23.3 Comparaison

| Analyse statique | Analyse dynamique |
|---|---|
| Sans exécution | Pendant l'exécution |
| Bonne vue d'ensemble | Montre le comportement réel |
| Peut être gênée par l'obfuscation | Peut être gênée par des protections runtime |
| Trouve des patterns et configurations | Valide l'exploitabilité et les flux |

Les deux approches sont complémentaires.

---

# 24. Mise en place d'un laboratoire Android sécurisé

## 24.1 Architecture recommandée

```text
Machine hôte
├── Android Studio
├── Android SDK / platform-tools
├── Émulateur Android dédié
├── JADX / APKTool
├── Environnement Python Frida
└── MobSF dans Docker, éventuellement
```

## 24.2 Pourquoi utiliser un émulateur ?

- réinitialisation rapide ;
- snapshots ;
- isolation des données personnelles ;
- contrôle de la version Android ;
- observation facilitée.

Un émulateur n'est pas parfaitement identique à un téléphone réel. Certains tests doivent être reproduits sur un appareil physique de laboratoire.

## 24.3 Règles d'isolation

- ne pas utiliser ton compte Google personnel ;
- ne pas importer de vrais contacts ;
- ne pas utiliser de données bancaires ;
- ne pas connecter l'environnement à un réseau sensible ;
- utiliser des identifiants de test ;
- créer un snapshot propre ;
- restaurer après une analyse douteuse.

## 24.4 Vérifier ADB

```bash
adb version
adb devices
```

Résultat attendu :

```text
List of devices attached
emulator-5554    device
```

Si l'état est `unauthorized`, valider la demande sur l'appareil de laboratoire.

## 24.5 Installer les outils sur Kali/Linux

Les commandes exactes dépendent de la distribution et des versions disponibles.

Base Debian/Kali possible :

```bash
sudo apt update
sudo apt install adb apktool jadx default-jdk python3-venv docker.io
```

Pour Frida, utilise un environnement virtuel :

```bash
python3 -m venv ~/venvs/frida
source ~/venvs/frida/bin/activate
python -m pip install --upgrade pip
python -m pip install frida-tools
frida --version
```

## 24.6 Arborescence de travail

```bash
mkdir -p mobile-lab/{apks,decoded,reports,scripts,notes,hashes}
cd mobile-lab
```

## 24.7 Empreinte du fichier

Avant l'analyse :

```bash
sha256sum apks/application.apk | tee hashes/application.sha256
file apks/application.apk
```

Cela aide à :

- identifier précisément l'échantillon ;
- détecter un changement ;
- documenter le rapport.

---

# 25. ADB : observer et piloter Android

ADB signifie **Android Debug Bridge**.

Il relie l'ordinateur à l'émulateur ou à l'appareil.

## 25.1 Commandes essentielles

Lister les appareils :

```bash
adb devices -l
```

Ouvrir un shell :

```bash
adb shell
```

Informations système :

```bash
adb shell getprop ro.build.version.release
adb shell getprop ro.build.version.sdk
adb shell getprop ro.product.cpu.abi
```

## 25.2 Packages

Lister :

```bash
adb shell pm list packages
```

Filtrer :

```bash
adb shell pm list packages | grep example
```

Trouver le chemin d'un APK installé :

```bash
adb shell pm path com.example.secureapp
```

Informations détaillées :

```bash
adb shell dumpsys package com.example.secureapp
```

## 25.3 Démarrer une Activity

```bash
adb shell am start \
  -n com.example.secureapp/.MainActivity
```

Avec un extra de test :

```bash
adb shell am start \
  -n com.example.secureapp/.ProfileActivity \
  --es username alice
```

Cette commande est utile pour vérifier si un composant exporté accepte des données inattendues.

## 25.4 Logs

Tous les logs :

```bash
adb logcat
```

Nettoyer puis observer :

```bash
adb logcat -c
adb logcat
```

Filtrer un tag :

```bash
adb logcat -s AUTH
```

Filtrer par PID :

```bash
PID=$(adb shell pidof com.example.secureapp)
adb logcat --pid="$PID"
```

## 25.5 Copier des fichiers

Vers l'appareil :

```bash
adb push test.txt /sdcard/Download/test.txt
```

Depuis l'appareil :

```bash
adb pull /sdcard/Download/result.txt ./
```

## 25.6 `run-as`

Pour une application débogable que tu contrôles :

```bash
adb shell run-as com.example.secureapp
```

Puis :

```bash
pwd
ls -la
```

Cela permet d'examiner le stockage privé dans un laboratoire, sans root, lorsque l'application est construite comme débogable.

## 25.7 Effacer les données

```bash
adb shell pm clear com.example.secureapp
```

Cela simule une remise à zéro de l'application.

## 25.8 Capture d'écran

```bash
adb exec-out screencap -p > screenshot.png
```

## 25.9 Enregistrement écran

```bash
adb shell screenrecord /sdcard/demo.mp4
# arrêter avec Ctrl+C
adb pull /sdcard/demo.mp4
```

---

# 26. Analyse statique avec JADX et APKTool

## 26.1 JADX

JADX convertit les fichiers DEX en une représentation Java lisible.

Lancer l'interface :

```bash
jadx-gui apks/application.apk
```

Ligne de commande :

```bash
jadx -d decoded/jadx apks/application.apk
```

### Ce qu'il faut chercher

- package principal ;
- `AndroidManifest.xml` ;
- `MainActivity` ;
- fonctions d'authentification ;
- URLs ;
- clés ou tokens codés en dur ;
- `WebView` ;
- `setJavaScriptEnabled` ;
- `addJavaScriptInterface` ;
- vérification TLS personnalisée ;
- `SharedPreferences` ;
- logs ;
- composants et Intents.

### Limite

Une décompilation n'est pas le code source original. Elle peut contenir :

- noms modifiés ;
- erreurs ;
- structures reconstruites ;
- code incomplet ;
- logique difficile à lire.

## 26.2 APKTool

APKTool décode principalement :

- manifeste ;
- ressources ;
- code Smali.

Décoder :

```bash
apktool d apks/application.apk -o decoded/apktool
```

Contenu :

```text
decoded/apktool/
├── AndroidManifest.xml
├── apktool.yml
├── res/
├── smali/
└── assets/
```

## 26.3 Smali

Smali est une représentation assembleur du bytecode DEX.

Exemple simplifié :

```smali
const-string v0, "admin"
invoke-virtual {p1, v0}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z
move-result v0
```

Pour un novice, l'objectif n'est pas de tout maîtriser immédiatement, mais de reconnaître :

- chaînes ;
- appels de méthodes ;
- conditions ;
- retours ;
- accès aux ressources.

## 26.4 Extraction simple

Un APK est une archive ZIP :

```bash
unzip -l apks/application.apk | less
```

Extraire :

```bash
mkdir -p decoded/unzip
unzip apks/application.apk -d decoded/unzip
```

Chaînes :

```bash
strings apks/application.apk | less
```

## 26.5 Vérifier la signature

Selon les outils du SDK disponibles :

```bash
apksigner verify --verbose --print-certs apks/application.apk
```

## 26.6 Mini-checklist statique

```text
[ ] Empreinte SHA-256 calculée
[ ] Package identifié
[ ] Version et SDK identifiés
[ ] Permissions listées
[ ] Composants exportés listés
[ ] Liens profonds examinés
[ ] Configuration réseau examinée
[ ] Sauvegarde examinée
[ ] Mode debug vérifié
[ ] Secrets et URLs recherchés
[ ] WebView recherchée
[ ] Stockage local recherché
[ ] Bibliothèques natives identifiées
```

---

# 27. Analyse dynamique avec Frida et MobSF

## 27.1 Frida

Frida est un outil d'instrumentation dynamique. Il permet d'observer ou d'interagir avec des fonctions pendant l'exécution d'un programme.

Usages autorisés en laboratoire :

- comprendre le flux d'une application de test ;
- vérifier qu'une fonction de chiffrement est appelée ;
- observer les arguments d'une méthode de sa propre application ;
- tester l'efficacité d'un contrôle local ;
- reproduire une faiblesse dans une application volontairement vulnérable.

## 27.2 Connexion générale

Sur un émulateur ou appareil de laboratoire correctement préparé :

```bash
frida-ps -U
```

Lister les applications :

```bash
frida-ps -Uai
```

Lancer une application avec un script d'observation :

```bash
frida -U -f com.example.secureapp -l scripts/observe.js
```

## 27.3 Exemple pédagogique d'observation

Pour ta propre application Java/Kotlin :

```javascript
Java.perform(function () {
    var LoginValidator = Java.use("com.example.secureapp.LoginValidator");

    LoginValidator.isValid.implementation = function (username) {
        console.log("isValid appelé avec : " + username);
        return this.isValid(username);
    };
});
```

But : observer l'appel. Ne jamais utiliser ce type d'outil contre une application sans autorisation.

## 27.4 Ce que démontre Frida

Si toute la sécurité dépend d'une condition locale, un analyste contrôlant l'appareil peut parfois l'observer ou la modifier.

Conclusion :

> Une décision d'autorisation critique ne doit jamais reposer uniquement sur le client mobile.

## 27.5 MobSF

MobSF automatise une partie de l'analyse statique et dynamique.

Exécution Docker typique :

```bash
sudo docker pull opensecurity/mobile-security-framework-mobsf:latest
sudo docker run -it --rm \
  -p 8000:8000 \
  opensecurity/mobile-security-framework-mobsf:latest
```

Puis ouvrir localement :

```text
http://127.0.0.1:8000
```

Fonctions possibles :

- analyse du manifeste ;
- permissions ;
- certificats ;
- code et secrets potentiels ;
- bibliothèques ;
- règles de sécurité ;
- rapport synthétique ;
- environnement dynamique selon la configuration.

## 27.6 Limites des outils automatiques

Un rapport automatique peut contenir :

- faux positifs ;
- éléments non exploitables ;
- recommandations génériques ;
- résultats manquant le contexte métier.

Chaque finding doit être confirmé manuellement.

---

# 28. Méthodologie d'audit mobile

Cette méthode s'applique à une application autorisée.

## 28.1 Étape 1 — Définir le périmètre

Documenter :

- application et version ;
- hash de l'APK ;
- environnement ;
- comptes de test ;
- API autorisées ;
- actions interdites ;
- données de test ;
- fenêtre de test.

## 28.2 Étape 2 — Cartographier

Identifier :

- écrans ;
- rôles ;
- fonctionnalités ;
- composants Android ;
- permissions ;
- endpoints ;
- données sensibles ;
- flux d'authentification.

## 28.3 Étape 3 — Analyse statique

Examiner :

- manifeste ;
- ressources ;
- code ;
- dépendances ;
- configuration ;
- stockage ;
- secrets ;
- WebView ;
- liens profonds.

## 28.4 Étape 4 — Analyse dynamique

Observer :

- démarrage ;
- connexion ;
- déconnexion ;
- permission refusée ;
- rotation ;
- passage arrière-plan ;
- erreur réseau ;
- expiration de session ;
- changement de compte ;
- données créées localement.

## 28.5 Étape 5 — Vérifier l'API

Pour chaque fonction :

- authentification obligatoire ?
- autorisation objet ?
- autorisation fonctionnelle ?
- validation des entrées ?
- limitation de taux ?
- message d'erreur sûr ?
- données minimisées ?

## 28.6 Étape 6 — Reproduire proprement

Une preuve doit contenir :

1. préconditions ;
2. étapes exactes ;
3. résultat observé ;
4. résultat attendu ;
5. impact ;
6. recommandation ;
7. captures ou logs nettoyés.

## 28.7 Étape 7 — Nettoyer

- supprimer les données de test ;
- révoquer les sessions ;
- arrêter les services ;
- restaurer le snapshot ;
- protéger les preuves ;
- supprimer les secrets des captures et logs.

## 28.8 Modèle de finding

```markdown
## Titre

### Sévérité
Medium

### Composant affecté
com.example.app/.ExportedActivity

### Description
Le composant exporté accepte un paramètre non validé...

### Préconditions
Application installée, utilisateur connecté...

### Étapes de reproduction
1. ...
2. ...

### Résultat observé
...

### Impact
...

### Recommandation
Déclarer le composant non exporté ou appliquer...

### Preuves
Capture, log, commande, hash de version.
```

---

# 29. Ateliers pratiques guidés

## Atelier 1 — Observer le cycle de vie

### Objectif

Comprendre quand les callbacks sont appelés.

### Étapes

1. Crée une application Android vide.
2. Ajoute les logs du chapitre 9.
3. Lance l'application.
4. Appuie sur Home.
5. Reviens dans l'application.
6. Tourne l'écran.
7. Ferme l'Activity.

Commande :

```bash
adb logcat -s LIFECYCLE
```

### Questions

- Quelle séquence apparaît au premier lancement ?
- Que se passe-t-il avec Home ?
- Que se passe-t-il lors d'une rotation ?
- Où faudrait-il libérer la caméra ?

---

## Atelier 2 — Permission caméra

### Objectif

Déclarer et demander une permission au bon moment.

### Étapes

1. Ajoute `CAMERA` au manifeste.
2. Ajoute un bouton « Ouvrir la caméra ».
3. Demande la permission uniquement après le clic.
4. Gère le refus.
5. Révoque-la avec ADB.

```bash
adb shell pm revoke com.example.secureapp android.permission.CAMERA
```

### Validation

L'application ne doit pas :

- planter ;
- redemander en boucle ;
- bloquer les fonctions indépendantes de la caméra.

---

## Atelier 3 — Intent explicite et implicite

### Objectif

Comprendre la différence.

### Intent explicite

```kotlin
startActivity(Intent(this, DetailsActivity::class.java))
```

### Intent implicite

```kotlin
startActivity(
    Intent(Intent.ACTION_VIEW, Uri.parse("https://developer.android.com"))
)
```

### Questions

- Qui choisit la cible ?
- Quel Intent convient à un Service interne ?
- Une donnée sensible doit-elle être envoyée à une cible implicite ?

---

## Atelier 4 — Compiler et inspecter son APK

### Objectif

Suivre le chemin du code source à l'APK.

```bash
./gradlew assembleDebug
sha256sum app/build/outputs/apk/debug/app-debug.apk
unzip -l app/build/outputs/apk/debug/app-debug.apk | less
jadx-gui app/build/outputs/apk/debug/app-debug.apk
```

### À retrouver

- nom du package ;
- `MainActivity` ;
- chaînes de l'application ;
- permissions ;
- build debug.

### Conclusion attendue

Le contenu livré au client doit être considéré comme analysable.

---

## Atelier 5 — Rechercher une fuite dans les logs

### Objectif

Vérifier que l'application ne journalise pas de secret.

1. Connecte-toi avec un compte de test.
2. Observe Logcat.
3. Recherche `token`, `password`, `Authorization`, `Bearer`.

```bash
adb logcat | grep -Ei 'token|password|authorization|bearer'
```

### Correction

Supprimer le secret, pas seulement le masquer partiellement si la portion restante reste exploitable.

---

## Atelier 6 — Composant exporté

### Objectif

Comprendre l'exposition d'une Activity.

Déclare une Activity de démonstration :

```xml
<activity
    android:name=".DebugActivity"
    android:exported="true" />
```

Tente de la démarrer :

```bash
adb shell am start \
  -n com.example.secureapp/.DebugActivity
```

Puis corrige :

```xml
android:exported="false"
```

### Leçon

Un écran non présent dans la navigation peut tout de même être accessible s'il est exporté.

---

## Atelier 7 — Trafic en clair

### Objectif

Empêcher l'utilisation accidentelle de HTTP.

1. Ajoute une Network Security Configuration.
2. Définis `cleartextTrafficPermitted="false"`.
3. Tente une requête de test HTTP.
4. Vérifie l'échec.
5. Remplace l'URL par HTTPS.

### Leçon

Une politique centrale réduit le risque qu'un développeur ajoute accidentellement un endpoint non chiffré.

---

## Atelier 8 — Analyse statique de son propre APK

### Objectif

Produire une fiche d'analyse.

```bash
mkdir -p mobile-lab/{apks,decoded,reports,hashes}
cp app-debug.apk mobile-lab/apks/
sha256sum mobile-lab/apks/app-debug.apk \
  | tee mobile-lab/hashes/app-debug.sha256
apktool d mobile-lab/apks/app-debug.apk \
  -o mobile-lab/decoded/apktool
jadx -d mobile-lab/decoded/jadx \
  mobile-lab/apks/app-debug.apk
```

À documenter :

- SHA-256 ;
- package ;
- SDK min/target ;
- permissions ;
- composants exportés ;
- URLs ;
- stockage ;
- logs ;
- conclusion.

---

# 30. Checklist de développement sécurisé

## Architecture et conception

- [ ] Les données sensibles sont identifiées.
- [ ] Les menaces principales sont documentées.
- [ ] Les décisions de sécurité critiques sont vérifiées côté serveur.
- [ ] Les données collectées sont minimisées.

## Permissions

- [ ] Chaque permission a une justification fonctionnelle.
- [ ] Les permissions sont demandées au moment pertinent.
- [ ] Le refus est géré sans plantage.
- [ ] Les permissions inutiles sont supprimées.

## Composants Android

- [ ] `android:exported` est explicite.
- [ ] Les composants internes sont non exportés.
- [ ] Les composants publics valident toutes les entrées.
- [ ] Les composants sensibles sont protégés par permissions appropriées.
- [ ] Les Intents internes sont explicites.
- [ ] Les PendingIntents sont immuables par défaut.

## Stockage

- [ ] Aucun mot de passe n'est stocké en clair.
- [ ] Aucun token n'apparaît dans les logs.
- [ ] Les clés ne sont pas codées en dur.
- [ ] Android Keystore est utilisé lorsque nécessaire.
- [ ] Les sauvegardes sont configurées selon la sensibilité.
- [ ] Le stockage externe ne contient pas de secret.

## Réseau

- [ ] HTTPS est utilisé.
- [ ] Le trafic en clair est refusé.
- [ ] Aucun TrustManager permissif n'est présent.
- [ ] Les erreurs TLS ne sont pas ignorées.
- [ ] Les données sensibles ne sont pas placées dans les URL.
- [ ] Les timeouts sont configurés.

## Authentification et session

- [ ] Les jetons ont une durée adaptée.
- [ ] La déconnexion invalide ou supprime la session.
- [ ] Les actions sensibles demandent une confirmation renforcée.
- [ ] Le serveur contrôle chaque autorisation.
- [ ] Les changements de rôle ou d'utilisateur sont correctement gérés.

## WebView

- [ ] JavaScript est désactivé s'il n'est pas indispensable.
- [ ] Les domaines autorisés sont limités.
- [ ] `addJavaScriptInterface` n'est pas exposé à du contenu non fiable.
- [ ] Les redirections sont contrôlées.
- [ ] Les liens profonds réappliquent authentification et autorisation.

## Build et livraison

- [ ] Le build release n'est pas débogable.
- [ ] La clé de signature est protégée.
- [ ] Les endpoints de test sont absents.
- [ ] Les dépendances sont à jour.
- [ ] L'obfuscation est configurée si pertinente.
- [ ] Les dernières mises à jour de sécurité sont intégrées.

---

# 31. Erreurs fréquentes à éviter

## Erreur 1 — « C'est dans l'APK, donc personne ne le verra »

Faux. Un APK peut être extrait, décompilé et observé.

## Erreur 2 — « Base64 protège mon secret »

Faux. Base64 est un encodage réversible.

## Erreur 3 — « Le bouton admin est caché, donc il est protégé »

Faux. L'API doit contrôler le rôle.

## Erreur 4 — « HTTPS suffit »

Faux. HTTPS ne corrige pas l'IDOR, les permissions excessives, le stockage en clair ou une mauvaise logique métier.

## Erreur 5 — « Le composant n'est pas accessible depuis l'interface »

Faux. Un composant exporté peut être appelé directement.

## Erreur 6 — « La permission a été accordée une fois pour toujours »

Faux. L'utilisateur ou Android peut la révoquer. L'application doit vérifier avant usage.

## Erreur 7 — « onDestroy sera toujours appelée »

Faux. Le processus peut être terminé sans que ton code puisse compter sur cette méthode pour une sauvegarde critique.

## Erreur 8 — « L'obfuscation protège la logique métier »

Faux. Elle ralentit l'analyse, mais une règle de sécurité côté client reste contrôlable par un attaquant maîtrisant l'environnement.

## Erreur 9 — « Un outil automatique prouve la vulnérabilité »

Faux. Il produit des indices à confirmer.

## Erreur 10 — « Une mise à jour de sécurité peut attendre »

Une dépendance ou plateforme obsolète augmente la fenêtre d'exposition.

---

# 32. Quiz de révision

Essaie d'abord de répondre sans regarder le corrigé.

## Questions

### 1. Quel composant représente généralement un écran interactif ?

A. Service  
B. Activity  
C. ContentProvider  
D. BroadcastReceiver

### 2. Quel composant sert principalement à partager des données structurées ?

A. Fragment  
B. Activity  
C. ContentProvider  
D. ViewModel

### 3. Quel fichier déclare les composants et permissions ?

A. `settings.gradle.kts`  
B. `AndroidManifest.xml`  
C. `classes.dex`  
D. `resources.arsc`

### 4. Un Intent explicite :

A. laisse toujours l'utilisateur choisir l'application ;  
B. nomme précisément le composant cible ;  
C. ne peut pas contenir d'extra ;  
D. fonctionne uniquement avec un navigateur.

### 5. Quelle méthode est appelée lors de la création initiale d'une Activity ?

A. `onPause()`  
B. `onStop()`  
C. `onCreate()`  
D. `onRestart()`

### 6. Quelle permission doit généralement être demandée à l'exécution ?

A. Une permission sensible comme la caméra  
B. Toute permission normale  
C. Aucune permission  
D. Seulement INTERNET

### 7. Où faut-il vérifier une autorisation métier critique ?

A. Uniquement dans l'interface mobile  
B. Uniquement dans une variable Kotlin  
C. Côté serveur  
D. Dans le nom du bouton

### 8. Base64 est :

A. un chiffrement fort ;  
B. un hachage ;  
C. un encodage ;  
D. un système de signature.

### 9. Quel stockage convient le moins à un secret non chiffré ?

A. Android Keystore  
B. Stockage externe partagé  
C. Clé matérielle sécurisée  
D. Aucun, car un secret ne doit pas être en clair

### 10. Quel outil permet de décompiler un APK vers une représentation Java ?

A. JADX  
B. Wireshark  
C. Nmap  
D. Git

### 11. APKTool est particulièrement utile pour :

A. le manifeste, les ressources et le Smali ;  
B. créer un serveur DNS ;  
C. gérer les mots de passe ;  
D. compiler un noyau Linux.

### 12. `adb logcat` sert à :

A. signer l'APK ;  
B. afficher les logs Android ;  
C. chiffrer une base ;  
D. créer une Activity.

### 13. Un composant interne devrait généralement avoir :

A. `android:exported="true"` ;  
B. aucune déclaration ;  
C. `android:exported="false"` ;  
D. toutes les permissions.

### 14. Pour un PendingIntent, le choix sûr par défaut est :

A. mutable et implicite ;  
B. immuable ;  
C. sans cible ;  
D. transmis dans les logs.

### 15. Quelle affirmation sur HTTPS est correcte ?

A. Il corrige toutes les failles métier.  
B. Il protège le transport, mais pas toute la logique.  
C. Il remplace l'authentification.  
D. Il permet de stocker les mots de passe en clair.

### 16. Une clé API secrète incluse dans l'APK :

A. doit être considérée comme récupérable ;  
B. est protégée par le nom de variable ;  
C. devient sûre avec Base64 ;  
D. est invisible si Kotlin est utilisé.

### 17. Pourquoi demander une permission au moment de l'usage ?

A. Pour surprendre l'utilisateur  
B. Pour que la raison soit compréhensible  
C. Pour empêcher le refus  
D. Pour éviter le manifeste

### 18. Quelle méthode peut empêcher les captures d'un écran sensible ?

A. `FLAG_SECURE`  
B. `FLAG_PUBLIC`  
C. `MODE_WORLD_READABLE`  
D. `ALLOW_ALL_CERTIFICATES`

### 19. Quelle analyse montre le comportement réel pendant l'exécution ?

A. Statique  
B. Dynamique  
C. Lexicale uniquement  
D. Aucune

### 20. Une rotation d'écran peut :

A. recréer l'Activity ;  
B. signer l'APK ;  
C. accorder toutes les permissions ;  
D. changer le package.

### 21. `onPause()` signifie généralement :

A. l'Activity gagne le focus ;  
B. l'Activity perd l'interaction principale ;  
C. l'APK est désinstallé ;  
D. le serveur est arrêté.

### 22. Une WebView doit activer JavaScript :

A. systématiquement ;  
B. seulement si nécessaire ;  
C. pour corriger TLS ;  
D. pour protéger les mots de passe.

### 23. Quel contrôle est le plus important pour un deep link vers une facture ?

A. La couleur de l'écran  
B. Vérifier côté serveur que l'utilisateur a accès à la facture  
C. Masquer l'identifiant  
D. Utiliser une police différente

### 24. Un rapport MobSF :

A. remplace toute vérification manuelle ;  
B. doit être interprété et confirmé ;  
C. prouve automatiquement l'exploitabilité ;  
D. ne contient jamais de faux positif.

### 25. La meilleure manière de protéger un mot de passe utilisateur est généralement :

A. le conserver en clair dans SharedPreferences ;  
B. ne pas le conserver et utiliser une session correctement gérée ;  
C. le mettre dans `strings.xml` ;  
D. l'afficher dans Logcat.

## Corrigé

1. **B** — une Activity représente généralement un écran interactif.  
2. **C** — un ContentProvider gère et partage des données structurées.  
3. **B** — le manifeste décrit composants, permissions et capacités.  
4. **B** — l'Intent explicite désigne sa cible.  
5. **C** — `onCreate()` initialise l'Activity.  
6. **A** — les permissions sensibles sont généralement demandées au runtime.  
7. **C** — l'autorisation critique doit être vérifiée côté serveur.  
8. **C** — Base64 est un encodage.  
9. **D** — un secret ne doit pas être stocké en clair ; le stockage externe est particulièrement inadapté.  
10. **A** — JADX décompile le DEX vers une représentation Java.  
11. **A** — APKTool décode manifeste, ressources et Smali.  
12. **B** — Logcat affiche les journaux Android.  
13. **C** — un composant interne doit rester non exporté.  
14. **B** — utiliser l'immuabilité par défaut.  
15. **B** — TLS protège le canal, pas toute la logique.  
16. **A** — tout secret livré dans le client est récupérable.  
17. **B** — le contexte rend la demande compréhensible.  
18. **A** — `FLAG_SECURE` limite les captures et aperçus.  
19. **B** — l'analyse dynamique observe l'exécution.  
20. **A** — une rotation peut recréer l'Activity.  
21. **B** — l'Activity perd l'interaction principale.  
22. **B** — activer seulement ce qui est indispensable.  
23. **B** — l'autorisation d'accès à l'objet doit être vérifiée.  
24. **B** — les résultats automatisés nécessitent une validation humaine.  
25. **B** — le mot de passe ne doit normalement pas être conservé par l'application.

---

# 33. Questions ouvertes de révision

Tu dois pouvoir répondre simplement à chacune de ces questions.

1. Quelles différences existent entre application native, web et hybride ?
2. Quels sont les quatre composants Android fondamentaux ?
3. À quoi sert une Activity ?
4. Quelle est la différence entre un Service et un thread ?
5. À quoi sert un BroadcastReceiver ?
6. Quand utilise-t-on un ContentProvider ?
7. Quel rôle joue le manifeste ?
8. Que signifie `android:exported` ?
9. Quelles étapes principales composent le cycle de vie d'une Activity ?
10. Pourquoi une rotation peut-elle faire perdre un état local ?
11. Quelle différence existe entre Intent explicite et implicite ?
12. Pourquoi un Intent implicite vers un Service est-il dangereux ?
13. Comment fonctionne une permission runtime ?
14. Qu'est-ce que le principe du moindre privilège ?
15. Comment Android isole-t-il les applications ?
16. Pourquoi ne peut-on pas cacher durablement un secret dans un APK ?
17. Pourquoi Base64 n'est-il pas une protection ?
18. À quoi sert Android Keystore ?
19. Pourquoi l'autorisation doit-elle être vérifiée côté serveur ?
20. Que protège TLS et que ne protège-t-il pas ?
21. Pourquoi ne faut-il jamais accepter tous les certificats ?
22. Quel risque présente une WebView mal configurée ?
23. Pourquoi faut-il contrôler un deep link comme toute entrée externe ?
24. Quelles données ne doivent pas apparaître dans les logs ?
25. Quelle différence existe entre analyse statique et dynamique ?
26. À quoi servent ADB, JADX, APKTool, Frida et MobSF ?
27. Pourquoi un outil automatique peut-il produire des faux positifs ?
28. Comment documenter correctement une vulnérabilité mobile ?
29. Pourquoi les mises à jour et les dépendances comptent-elles pour la sécurité ?
30. Comment appliquer la défense en profondeur à une application mobile ?

---

# 34. Glossaire

| Terme | Définition simple |
|---|---|
| AAB | Format de publication permettant de générer des APK adaptés aux appareils. |
| ADB | Outil de communication entre l'ordinateur et Android. |
| Activity | Composant représentant généralement un écran interactif. |
| APK | Paquet installable Android. |
| APKTool | Outil de décodage des ressources, du manifeste et du Smali. |
| API | Interface utilisée pour communiquer avec un service ou composant. |
| ART | Environnement d'exécution Android. |
| Autorisation | Vérification qu'une identité peut effectuer une action. |
| Authentification | Vérification de l'identité. |
| Binder | Mécanisme Android de communication interprocessus. |
| BroadcastReceiver | Composant recevant des événements diffusés. |
| ContentProvider | Composant d'accès et de partage de données structurées. |
| DEX | Format de bytecode exécuté par Android. |
| Deep link | Lien ouvrant un emplacement précis d'une application. |
| Fragment | Portion réutilisable d'interface et de logique. |
| Frida | Outil d'instrumentation dynamique. |
| Intent | Message décrivant une action à effectuer. |
| IPC | Communication entre processus. |
| JADX | Décompilateur DEX/APK vers une représentation Java. |
| Keystore | Conteneur sécurisé de clés cryptographiques Android. |
| Kotlin | Langage moderne utilisé pour Android. |
| Logcat | Système et outil d'affichage des logs Android. |
| MASVS | Standard OWASP de vérification de la sécurité mobile. |
| MASTG | Guide OWASP de tests de sécurité mobile. |
| MobSF | Framework automatisé d'analyse de sécurité mobile. |
| PendingIntent | Jeton permettant une action future avec l'identité de l'application créatrice. |
| Permission runtime | Permission sensible demandée pendant l'utilisation. |
| Sandbox | Isolation d'une application et de ses données. |
| Service | Composant réalisant une tâche sans interface directe. |
| Smali | Représentation assembleur du bytecode DEX. |
| TLS | Protocole protégeant les communications réseau. |
| UID | Identifiant Linux utilisé notamment pour isoler les applications. |
| WebView | Composant affichant du contenu web dans une application. |

---

# 35. Ressources officielles

Les documentations évoluent. Vérifie régulièrement les versions en vigueur.

## Android Developers

- Application fundamentals: <https://developer.android.com/guide/components/fundamentals>
- App architecture: <https://developer.android.com/topic/architecture>
- App manifest overview: <https://developer.android.com/guide/topics/manifest/manifest-intro>
- Activity lifecycle: <https://developer.android.com/guide/components/activities/activity-lifecycle>
- Permissions overview: <https://developer.android.com/guide/topics/permissions/overview>
- Request runtime permissions: <https://developer.android.com/training/permissions/requesting>
- Android Debug Bridge: <https://developer.android.com/tools/adb>
- Build from command line: <https://developer.android.com/build/building-cmdline>
- Build for release: <https://developer.android.com/build/build-for-release>
- Security checklist: <https://developer.android.com/privacy-and-security/security-tips>
- Network Security Configuration: <https://developer.android.com/privacy-and-security/security-config>
- Android Keystore: <https://developer.android.com/privacy-and-security/keystore>
- Cryptography: <https://developer.android.com/privacy-and-security/cryptography>
- PendingIntent risks: <https://developer.android.com/privacy-and-security/risks/pending-intent>
- Hardcoded cryptographic secrets: <https://developer.android.com/privacy-and-security/risks/hardcoded-cryptographic-secrets>
- Cleartext communications: <https://developer.android.com/privacy-and-security/risks/cleartext-communications>

## OWASP Mobile Application Security

- OWASP MAS project: <https://mas.owasp.org/>
- OWASP MASVS: <https://mas.owasp.org/MASVS/>
- OWASP MASTG: <https://mas.owasp.org/MASTG/>
- Android Security Testing: <https://mas.owasp.org/MASTG/0x05b-Android-Security-Testing/>
- Reference applications: <https://mas.owasp.org/MASTG/apps/>

> En juillet 2026, OWASP a annoncé la version stable MASTG 2.0.0. La structure du projet relie les exigences MASVS, les faiblesses MASWE et les tests/techniques MASTG. Utilise le site en ligne comme référence actuelle.

## Outils

- JADX: <https://github.com/skylot/jadx>
- APKTool: <https://apktool.org/>
- Frida: <https://frida.re/docs/android/>
- MobSF: <https://github.com/MobSF/Mobile-Security-Framework-MobSF>

---

# Conclusion générale

Une application mobile sécurisée repose sur plusieurs idées simples mais essentielles :

1. comprendre comment Android lance et isole les composants ;
2. demander le minimum de permissions ;
3. traiter toute entrée externe comme non fiable ;
4. ne jamais considérer l'APK comme un coffre-fort ;
5. protéger les données locales et les clés ;
6. utiliser TLS sans désactiver ses vérifications ;
7. vérifier l'authentification et l'autorisation côté serveur ;
8. limiter l'exposition des Activities, Services, Receivers et Providers ;
9. analyser l'application statiquement et dynamiquement ;
10. mettre à jour régulièrement le code, les dépendances et les pratiques.

Le bon réflexe est toujours de se demander :

> Quelle donnée est manipulée, qui peut atteindre ce composant, à qui faisons-nous confiance et que se passe-t-il si le client mobile est entièrement contrôlé par un attaquant ?

C'est cette manière de raisonner qui transforme un développeur mobile en développeur orienté sécurité.
