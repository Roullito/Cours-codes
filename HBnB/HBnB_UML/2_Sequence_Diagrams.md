# 📋 Diagrammes de Séquence - API HBnB

## 🌟 Objectif

Visualiser le flot d’interactions entre les couches de l’application HBnB (Présentation, Business Logic, Persistence) à travers des **diagrammes de séquence UML** pour les principales requêtes API.

---

## 🧬 Vue d'ensemble

Chaque diagramme représente une action côté client (frontend ou API) et le cheminement des données et commandes à travers les différentes couches du système jusqu'à la base de données, en suivant le modèle MVC avec le **Facade Pattern**.

API couvertes :

1. User Registration
2. Place Creation
3. Review Submission
4. Fetching List of Places

---

## 1. 👤 User Registration

```mermaid
sequenceDiagram
participant Client
participant API
participant UserService
participant UserRepository

Client->>API: POST /users/register
API->>UserService: validate_and_create_user(data)
UserService->>UserRepository: save(user_data)
UserRepository-->>UserService: confirmation
UserService-->>API: success / error
API-->>Client: response (status + message)
```

**Notes** :

* Le `UserService` agit comme facade
* `UserRepository` encapsule l'accès à la base

---

## 2. 🏡 Place Creation

```mermaid
sequenceDiagram
participant Client
participant API
participant PlaceService
participant PlaceRepository
participant AmenityRepository

Client->>API: POST /places
API->>PlaceService: validate_and_create_place(data)
PlaceService->>PlaceRepository: save(place_data)
PlaceService->>AmenityRepository: link amenities
PlaceRepository-->>PlaceService: success
PlaceService-->>API: created / error
API-->>Client: response (201 / error)
```

**Notes** :

* La gestion des amenities peut être déléguée à un composant secondaire ou traité en interne.

---

## 3. 📝 Review Submission

```mermaid
sequenceDiagram
participant Client
participant API
participant ReviewService
participant ReviewRepository
participant PlaceRepository
participant UserRepository

Client->>API: POST /reviews
API->>ReviewService: submit_review(data)
ReviewService->>UserRepository: verify user exists
ReviewService->>PlaceRepository: verify place exists
ReviewService->>ReviewRepository: save(review_data)
ReviewRepository-->>ReviewService: confirmation
ReviewService-->>API: success / error
API-->>Client: response
```

**Notes** :

* On vérifie toujours les entités liées (User/Place)

---

## 4. 📖 Fetching a List of Places

```mermaid
sequenceDiagram
participant Client
participant API
participant PlaceService
participant PlaceRepository
participant AmenityRepository

Client->>API: GET /places
API->>PlaceService: get_places(filters)
PlaceService->>PlaceRepository: fetch places
PlaceService->>AmenityRepository: fetch linked amenities
PlaceRepository-->>PlaceService: list of places
PlaceService-->>API: list with amenities
API-->>Client: JSON response
```

**Notes** :

* Possibilité d'utiliser des jointures ou requêtes multiples selon l'ORM

---

## ✅ À retenir

* Les 3 couches sont toujours impliquées : API -> Service -> Repository
* Le `Service` est responsable de la validation, logique métier, et de faire le lien entre API et DB
* Les `Repository` encapsulent les opérations base de données (pattern DAO)

---

## 📚 Ressources utiles

* [UML Sequence Diagram Tutorial](https://www.visual-paradigm.com/guide/uml-unified-modeling-language/uml-sequence-diagram/)
* [Understanding REST API Flows](https://restfulapi.net/)
* [Mermaid.js Sequence Diagrams](https://mermaid.js.org/syntax/sequenceDiagram.html)
