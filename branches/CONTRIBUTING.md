# Stratégie de Branches (GitFlow)

Ce document définit la stratégie de gestion des branches Git du projet, inspirée de GitFlow. L'objectif est d'organiser le développement, de minimiser les conflits et de garantir des livraisons stables et prévisibles.

## Branches Principales

* `main` : Représente l'état de la **production**. Cette branche est protégée et ne contient que les versions stables, identifiées par des tags (ex: `v1.2.3`). Tout commit sur `main` doit être une version déployable.
* `develop` : Branche d'**intégration** où toutes les fonctionnalités terminées sont fusionnées. Elle sert de base pour les nouvelles fonctionnalités et les versions à venir. Cette branche doit toujours rester stable et passer les tests.

## Types de Branches

| Type de Branche | Rôle | Part de... | Fusionne vers... | Convention de nommage |
| :--- | :--- | :--- | :--- | :--- |
| **feature** | Développement d'une nouvelle fonctionnalité | `develop` | `develop` | `feature/<ticket-id>-<description>` |
| **release** | Préparation d'une nouvelle version (corrections mineures, docs) | `develop` | `main` et `develop` | `release/<version>` (ex: `1.2.0`) |
| **hotfix** | Correction d'un bug critique en production | `main` | `main` et `develop` | `hotfix/<description>` (ex: `fix-500-login`) |

---

## Flux de Travail

### 1. Développement d'une fonctionnalité (`feature`)

1.  **Créer une branche** depuis `develop` :
    ```bash
    git checkout -b feature/123-nom-de-la-feature develop
    ```
2.  **Développer** en réalisant des commits clairs (`feat:`, `fix:`, etc.).
3.  **Synchroniser** régulièrement sa branche avec `develop` pour éviter les conflits :
    ```bash
    git pull --rebase origin develop
    ```
4.  **Ouvrir une Pull Request (PR)** vers `develop`.
5.  Une fois la PR validée et les tests passés, elle est fusionnée via un **squash merge**.

### 2. Création d'une version (`release`)

1.  **Geler les fonctionnalités** en créant une branche `release` depuis `develop` :
    ```bash
    git checkout -b release/1.2.0 develop
    ```
2.  Sur cette branche, effectuer uniquement les **corrections de dernière minute** (montée de version, CHANGELOG, etc.).
3.  **Ouvrir une PR** de `release/1.2.0` vers `main`.
4.  Après fusion, **taguez le commit** sur `main` avec la version :
    ```bash
    git checkout main
    git pull
    git tag v1.2.0
    git push --tags
    ```
5.  **Rétro-fusionner** (`back-merge`) les modifications de `main` vers `develop` pour que `develop` contienne la version la plus à jour.
    ```bash
    git checkout develop
    git merge --no-ff main
    git push
    ```

### 3. Correction Urgente (`hotfix`)

1.  **Créer une branche** depuis `main` pour une correction minimale et ciblée :
    ```bash
    git checkout -b hotfix/correction-critique main
    ```
2.  **Commit** le correctif.
3.  **Ouvrir une PR** de `hotfix` vers `main`.
4.  Après fusion, **taguez la nouvelle version** patchée (ex: `v1.2.1`).
5.  **Rétro-fusionner** `main` vers `develop` pour que le correctif soit aussi inclus dans le prochain cycle de développement.

---

## Règles et Bonnes Pratiques

* **Commits Conventionnels** : Utiliser des messages de commit structurés (ex: `feat(login): add password validation`).
* **Protection des branches** : Les branches `main` et `develop` sont protégées. Les pushs directs sont interdits et les fusions se font obligatoirement via Pull Request avec au moins une revue et la validation de la CI (tests, lint).
* **Versioning** : Le projet suit la convention **SemVer** (`MAJEUR.MINEUR.PATCH`).
* **Historique propre** : Privilégier le `rebase` sur les branches de `feature` et le `squash merge` pour les PR afin de maintenir un historique linéaire et lisible.