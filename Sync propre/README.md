# Sync propre (Git / GitFlow)

## 🧰 Glossaire rapide

- **Rebase** : réapplique tes commits au-dessus d’une branche plus à jour (pour avoir un historique linéaire).  
- **Fast-forward (FF)** : faire avancer le pointeur sans créer de commit de merge.  
- **force‑with‑lease** : pousser en forçant tout en assurant de ne pas écraser du travail distant (sécurité).

---

## Objectif

- Garder un historique **lisible et linéaire**  
- Réduire les conflits  
- S’assurer que les branches `feature/*` restent alignées avec `develop` (avant le push / la PR ou durant la revue)

---

## Configuration initiale (à faire une seule fois)

```bash
git config --global pull.rebase true        # “git pull” fait un rebase au lieu d’un merge
git config --global rebase.autoStash true   # stash automatiquement les modifications non commitées avant le rebase
git config --global fetch.prune true        # supprime les branches distantes supprimées localement
git config --global push.default simple
```

---

## Rituel quotidien de synchronisation

1. **Mettre à jour `develop` et `main` en local**  
   ```bash
   git fetch origin
   git checkout develop && git pull --ff-only
   git checkout main    && git pull --ff-only
   ```

2. **Rebaser ta branche feature sur la version la plus récente de `develop`**  
   ```bash
   git checkout feature/XXX
   git fetch origin
   git rebase origin/develop
   # résoudre les conflits si nécessaire
   git push --force-with-lease
   ```

3. **Avant de créer ou mettre à jour une Pull Request (PR)**  
   - Toujours rebaser si `develop` a évolué  
   - Relancer la CI après le push  

---

## Gestion des conflits pendant un rebase

- `git status` pour identifier les fichiers en conflit  
- Modifier les fichiers conflictuels, puis `git add …`  
- `git rebase --continue` pour poursuivre le rebase  
- `git rebase --abort` pour annuler le rebase si besoin  

**Astuce** : résoudre les conflits fichier par fichier, tester localement après chaque correction.

---

## Quand faire un **merge** plutôt qu’un rebase ?

- Après une **release** ou un **hotfix**, pour conserver une trace explicite dans l’historique :  
  ```bash
  git checkout develop
  git pull
  git merge --no-ff main
  git push
  ```

---

## Normalisation des fins de ligne (LF / CRLF)

Dans le fichier `.gitattributes` recommandé :

```
* text=auto eol=lf
*.bat text eol=crlf
*.sh  text eol=lf
```

Cela évite les “faux diffs” entre systèmes d’exploitation.

---

## Bonnes pratiques CI & PR

- Les PR doivent cibler `develop`, avec **squash merge** par défaut  
- La CI (lint + tests) doit passer après le rebase  
- Supprimer la branche distante après le merge

---

## Cheatsheet rapide

```bash
# Mettre develop à jour
git fetch origin
git checkout develop && git pull --ff-only

# Rebaser la feature
git checkout feature/XXX
git rebase origin/develop
git push --force-with-lease

# En cas de problème
git rebase --abort

# Back‑merge après release/hotfix
git checkout develop
git merge --no-ff main
git push
```

---

## Livrables attendus (Definition of Done - DoD)

- `develop` et `main` toujours synchronisés localement  
- Branches feature rebasées avant tout push / PR  
- `.gitattributes` configuré (gestion des fins de ligne)  
- Push effectué avec `--force-with-lease`  
- CI verte après rebase  
- PR prête pour un squash merge  
