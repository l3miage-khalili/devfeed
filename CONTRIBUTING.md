# Guide de contribution — DevFeed

## Prérequis

- Java 21
- Node.js 22
- Docker + Docker Compose v2
- Maven 3.9+

---

## Lancer les tests

### Backend

```bash
cd backend

# Tests unitaires uniquement
./mvnw test

# Tests unitaires + intégration (Testcontainers — Docker requis)
./mvnw verify
```

### Frontend

```bash
cd frontend

# Tests unitaires
npm test

# Tests en mode watch
npm run test:watch
```

---

## Conventions de commits

Ce projet suit la spécification [Conventional Commits](https://www.conventionalcommits.org/).

```
<type>(<scope>): <description courte>
```

| Type | Usage |
|---|---|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `docs` | Documentation uniquement |
| `refactor` | Refactoring sans changement de comportement |
| `test` | Ajout ou correction de tests |
| `chore` | Tâches de maintenance (deps, config...) |
| `ci` | Changements CI/CD |

**Exemples :**
```
feat(fil): ajouter le défilement infini sur la liste des articles
fix(auth): gérer correctement l'expiration du token JWT
docs(readme): mettre à jour les instructions de démarrage
chore(deps): mettre à jour spring-boot vers la version 3.3.13
```

---

## Stratégie de branches

```
main          ← branche protégée, déployée en production
  └── feature/xxx   ← développement d'une fonctionnalité
  └── fix/xxx       ← correction de bug
  └── chore/xxx     ← maintenance
```

- **Toujours** créer une branche depuis `main`
- **Toujours** passer par une Pull Request, même en solo
- Fermer les issues avec `Closes #XX` dans le message de commit ou la PR

---

## Ouvrir une Pull Request

1. Créer une branche : `git checkout -b feature/nom-feature`
2. Développer en commitant régulièrement
3. Pousser la branche : `git push origin feature/nom-feature`
4. Ouvrir une PR vers `main` en remplissant le template
5. Vérifier que le CI passe (tests + lint)
6. Merger une fois le CI vert

---

## Variables d'environnement

Copier `.env.example` en `.env` et renseigner les valeurs avant de lancer Docker Compose.  
Ne jamais committer le fichier `.env`.
