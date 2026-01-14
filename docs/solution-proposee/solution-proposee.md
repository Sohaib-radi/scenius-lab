# Proposition de Transformation Technique

## 1. Analyse des Options Proposées

Nous avons évalué trois stratégies pour résoudre la dette technique critique et répondre au nouveau besoin métier (modèle B2B / Multi-tenant).

### Option A : Mise à jour de la solution actuelle (Patching)
* **Description :** Tenter de refactoriser le fichier `models.py` existant et d'ajouter une entité `Company` par-dessus l'architecture actuelle.
* **Analyse :** **Non recommandée.** Le couplage fort (Tight Coupling) autour de l'objet "User" est trop profond. Chaque modification risque de casser des dizaines de vues et de sérialiseurs. Cette option ne fait que repousser l'effondrement du système.

### Option B : Approche Hybride (Maintenance + V2 en parallèle)
* **Description :** Maintenir la version actuelle pour les clients existants tout en développant la nouvelle architecture en parallèle.
* **Analyse :** **Acceptable mais coûteuse.** Elle demande un double effort de maintenance. Le "Time-to-Market" pour les fonctionnalités B2B sera plus long car l'équipe est divisée.

### Option C : Reconstruction via le Module Core (Recommandée)
* **Description :** Utiliser le module "Core" déjà développé pour rebâtir une architecture modulaire, saine et nativement multi-tenant.
* **Analyse :** **La meilleure solution.** Étant donné que le besoin métier change (passage à une offre pour entreprises), l'architecture actuelle est structurellement incapable de suivre. Une base propre permet d'implémenter le modèle `Company -> User` correctement dès le départ.

---

## 2. Synthèse de la Solution Préconisée (Option C)



### 2.1 Transformation du Modèle de Données
Le problème majeur est le **God Object (User)**. La solution consiste à inverser la logique de responsabilité :

* **Introduction de l'Entité `Organization` :** Le pivot central ne sera plus l'utilisateur, mais l'organisation (Company).
* **Découplage :** Les modèles `Course`, `Payment` ... seront liés à l'organisation. L'utilisateur ne sera plus qu'une entité d'authentification liée à une ou plusieurs organisations avec un rôle spécifique.
* **Modularité :** Fragmentation du `models.py` monolithique en packages Python par domaine (`users/`, `profile/`, `courses/`).

### 2.2 Refonte du Système de Permissions
Passage d'un système binaire à un système **RBAC (Role-Based Access Control)** granulaire :
* **Niveaux de rôles :** SuperAdmin (Platform), Admin (Company), Manager, Instructor, Student.
* **Permissions au niveau objet :** Vérification systématique de l'appartenance d'une ressource à l'organisation de l'utilisateur.

### 2.3 Sécurité et Cycle de Vie des Tokens
Correction de la gestion de l'authentification :
* **Configuration Dynamique :** Sortie des durées de validité du code pour les placer dans les `settings` de Django.
* **Standard JWT :** Implémentation de tokens de courte durée (Access: 30 min) avec rotation des Refresh Tokens (7 jours).
* **Créer Token Management App :**  Implémentation d’une application qui gère les tokens et les access.

### 2.4 Couche de Services (Service Layer)
Pour corriger la violation du principe de séparation des préoccupations (SoC) dans les vues :
* Toute la logique métier (création de compte, processus de paiement, logique de login) sera extraite des `views.py` pour être placée dans des `services.py`.

---

## 3. Comparaison Technique : Avant vs Après

| Caractéristique | Architecture Actuelle (V1) | Nouvelle Architecture (V2) |
| :--- | :--- | :--- |
| **Structure** | Monolithe (1700 lignes) | Modulaire (DDD - Domain Driven Design) |
| **Pivot de donnée** | User (God Object) | Company / Organization |
| **Permissions** | Binaires (Admin/User) | Granulaires (RBAC) |
| **Scalabilité** | Limitée (B2C uniquement) | Native (Multi-tenant B2B) |
| **Maintenance** | Risque élevé de régressions | Code isolé et testable |

---

## 4. Conclusion et Prochaines Étapes
La structure actuelle est un frein au développement commercial de l'entreprise. Le passage à l'**Option C** permet non seulement de résoudre les problèmes de sécurité et de performance détectés, mais surtout de rendre le produit compatible avec le marché **B2B**.

