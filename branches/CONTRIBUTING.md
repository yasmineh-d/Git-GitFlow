# Stratégie de Branches (GitFlow Minimal)

Ce document définit la stratégie Git adoptée par le projet, inspirée de **GitFlow**, dans une version **simplifiée et pragmatique**.  
L’objectif est de garantir un développement propre, des versions stables et un historique Git clair.

---

## 🌳 Branches Principales

| Branche | Rôle | Remarques |
|----------|------|-----------|
| **`main`** | Contient uniquement le code **en production**. Chaque commit sur cette branche correspond à une version stable (taguée). | Branche protégée, fusion via PR uniquement. |
| **`develop`** | Contient le code **de développement intégré**. Toutes les nouvelles fonctionnalités y sont fusionnées une fois terminées et testées. | Sert de base pour les branches `feature` et `release`. |

---

## 🌿 Branches Secondaires

| Type | Rôle | Part de… | Fusionne vers… | Exemple |
|------|------|-----------|----------------|----------|
| **feature/** | Développement d’une nouvelle fonctionnalité | `develop` | `develop` | `feature/login-page` |
| **release/** | Préparation d’une version avant mise en production | `develop` | `main` et `develop` | `release/v1.2.0` |
| **hotfix/** | Correction d’un bug critique en production | `main` | `main` et `develop` | `hotfix/v1.2.1` |

---

## ⚙️ Flux de Travail

### 🧩 1. Développement d’une fonctionnalité (`feature`)

1. Créer la branche à partir de `develop` :
   ```bash
   git checkout -b feature/nom-de-la-feature develop
   ```
2. Développer et **committer avec des messages clairs** (`feat:`, `fix:`, `refactor:`…).
3. Se synchroniser régulièrement :
   ```bash
   git pull --rebase origin develop
   ```
4. Ouvrir une **Pull Request vers `develop`**.
5. Une fois validée et testée, la PR est fusionnée avec **“Squash and Merge”**.

---

### 🚀 2. Préparation d’une version (`release`)

1. Créer la branche de release :
   ```bash
   git checkout -b release/v1.2.0 develop
   ```
2. Effectuer les derniers ajustements : documentation, corrections mineures, version bump.
3. Ouvrir une **PR vers `main`**.
4. Après validation et fusion :
   ```bash
   git checkout main
   git pull
   git tag v1.2.0
   git push origin main --tags
   ```
5. **Rétro-fusionner vers `develop`** pour conserver la cohérence :
   ```bash
   git checkout develop
   git merge --no-ff main
   git push origin develop
   ```

---

### 🩹 3. Correction urgente (`hotfix`)

1. Créer une branche depuis `main` :
   ```bash
   git checkout -b hotfix/v1.2.1 main
   ```
2. Apporter le correctif.
3. Ouvrir une **PR vers `main`**.
4. Une fois validée :
   ```bash
   git checkout main
   git merge --no-ff hotfix/v1.2.1
   git tag v1.2.1
   git push origin main --tags
   ```
5. Rétro-fusionner sur `develop` :
   ```bash
   git checkout develop
   git merge --no-ff hotfix/v1.2.1
   git push origin develop
   ```

---

## 🧱 Règles de Pull Request et CI

* ✅ **Fusion par “Squash and Merge” uniquement**  
* 👀 **Minimum un reviewer** obligatoire avant merge  
* 🧪 **Tous les tests CI (lint, build, tests)** doivent réussir  
* 🚫 **Aucun commit direct sur `main` ou `develop`**  
* 🧩 **Commits conventionnels** recommandés :  
  - `feat: ajout d’une nouvelle fonctionnalité`  
  - `fix: correction d’un bug`  
  - `docs: mise à jour documentation`

---

## 🏷️ Versioning (Semantic Versioning)

Le projet suit la convention **SemVer** :  
```
vMAJEUR.MINEUR.PATCH
```

| Type | Exemple | Signification |
|------|----------|---------------|
| **MAJEUR** | `v2.0.0` | Changements incompatibles (rupture d’API) |
| **MINEUR** | `v1.1.0` | Nouvelles fonctionnalités rétro-compatibles |
| **PATCH** | `v1.0.1` | Corrections de bugs ou hotfix |

Créer et pousser un tag :
```bash
git tag v1.1.0
git push origin v1.1.0
```

---

## 🔄 Processus de Back-Merge

Après chaque **release** ou **hotfix**, toujours réintégrer `main` dans `develop` :

```bash
git checkout develop
git merge main
git push origin develop
```

Cela garantit que `develop` contient toutes les mises à jour de production.
