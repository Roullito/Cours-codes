# 📄 Documentation Technique Complète - HBnB Evolution

## 🌎 Introduction

Ce document présente l’architecture technique et la modélisation du projet **HBnB Evolution**. Il a pour but de servir de référence pour les phases de développement ultérieures en décrivant la structure logicielle, les entités principales et le comportement attendu des API.

---

## 🏠 1. Architecture Haut Niveau

### Diagramme de package

Visualisation de l’architecture en 3 couches :

* **Presentation Layer** : Services et APIs
* **Business Logic Layer** : Modèles et logique métier
* **Persistence Layer** : Accès base de données (DAOs)

**Façade pattern** : permet de limiter l’accès direct entre les couches, tout passe par les "services" de la couche métier.

```mermaid
classDiagram
class PresentationLayer {
    <<Interface>>
    +UserService
    +PlaceService
}
class BusinessLogicLayer {
    +User
    +Place
    +Review
    +Amenity
}
class PersistenceLayer {
    +UserRepository
    +PlaceRepository
    +ReviewRepository
    +AmenityRepository
}
PresentationLayer --> BusinessLogicLayer : uses (Facade)
BusinessLogicLayer --> PersistenceLayer : accesses
```

---

## 🔹 2. Modélisation des Entités (Business Logic Layer)

### Diagramme de classes UML

Ce diagramme montre les entités principales du projet, leurs attributs, méthodes, et relations :

```mermaid
classDiagram
class User {
  +UUID id
  +str first_name
  +str last_name
  +str email
  +str password
  +bool is_admin
  +datetime created_at
  +datetime updated_at
  +register()
  +update_profile()
  +delete()
}

class Place {
  +UUID id
  +str title
  +str description
  +float price
  +float latitude
  +float longitude
  +datetime created_at
  +datetime updated_at
  +create()
  +update()
  +delete()
}

class Review {
  +UUID id
  +int rating
  +str comment
  +datetime created_at
  +datetime updated_at
  +submit()
  +update()
  +delete()
}

class Amenity {
  +UUID id
  +str name
  +str description
  +datetime created_at
  +datetime updated_at
  +create()
  +update()
  +delete()
}

User --> Place : owns
User --> Review : writes
Place --> Review : receives
Place --> Amenity : has >*
```

---

## 📊 3. Diagrammes de séquence API

### 1. User Registration

```mermaid
sequenceDiagram
Client->>API: POST /users/register
API->>UserService: validate_and_create_user(data)
UserService->>UserRepository: save(user_data)
UserRepository-->>UserService: confirmation
UserService-->>API: success / error
API-->>Client: response (status + message)
```

### 2. Place Creation

```mermaid
sequenceDiagram
Client->>API: POST /places
API->>PlaceService: validate_and_create_place(data)
PlaceService->>PlaceRepository: save(place_data)
PlaceService->>AmenityRepository: link amenities
PlaceRepository-->>PlaceService: success
PlaceService-->>API: created / error
API-->>Client: response (201 / error)
```

### 3. Review Submission

```mermaid
sequenceDiagram
Client->>API: POST /reviews
API->>ReviewService: submit_review(data)
ReviewService->>UserRepository: verify user exists
ReviewService->>PlaceRepository: verify place exists
ReviewService->>ReviewRepository: save(review_data)
ReviewRepository-->>ReviewService: confirmation
ReviewService-->>API: success / error
API-->>Client: response
```

### 4. Fetching List of Places

```mermaid
sequenceDiagram
Client->>API: GET /places
API->>PlaceService: get_places(filters)
PlaceService->>PlaceRepository: fetch places
PlaceService->>AmenityRepository: fetch linked amenities
PlaceRepository-->>PlaceService: list of places
PlaceService-->>API: list with amenities
API-->>Client: JSON response
```

---

## 🎓 Conclusion et recommandations

* Cette documentation UML constitue la base de l’implémentation de l’application HBnB.
* Elle permet de s’assurer que la structure logicielle est cohérente, modulaire, et maintenable.
* L’utilisation du **Facade Pattern** et d’une architecture en couches facilite la séparation des responsabilités.
* Tous les objets incluent un `UUID`, un `created_at`, et un `updated_at` pour assurer le suivi des modifications.

---

## 📃 Références

* UML Basics : [Visual Paradigm UML Guide](https://www.visual-paradigm.com/guide/uml-unified-modeling-language/)
* Mermaid.js : [Mermaid Diagrams](https://mermaid.js.org)
* SOLID & OOP : [OOP SOLID Principles](https://medium.com/@joeclever/solid-principles-explained-in-plain-english-5dc5fbbbecc6)
