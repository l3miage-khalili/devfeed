# ⚡ DevFeed

Plateforme de veille technologique avec recommandations personnalisées.  
Agrège automatiquement des articles tech depuis des flux RSS, les catégorise par tags et propose à chaque utilisateur un fil personnalisé selon ses centres d'intérêt et son historique de lecture.

[![CI Backend](https://github.com/GITHUB_USERNAME/devfeed/actions/workflows/ci-backend.yml/badge.svg)](https://github.com/GITHUB_USERNAME/devfeed/actions/workflows/ci-backend.yml)
[![CI Frontend](https://github.com/GITHUB_USERNAME/devfeed/actions/workflows/ci-frontend.yml/badge.svg)](https://github.com/GITHUB_USERNAME/devfeed/actions/workflows/ci-frontend.yml)
[![Quality Gate](https://sonarcloud.io/api/project_badges/measure?project=devfeed&metric=alert_status)](https://sonarcloud.io/project/overview?id=devfeed)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Stack technique

| Couche | Technologie |
|---|---|
| Backend | Java 21 · Spring Boot 3.3.13 · Spring Security (JWT) · Spring Data JPA |
| Base de données | PostgreSQL 17 · Flyway (migrations) |
| Cache | Redis 7 |
| Frontend | Angular 21 |
| Qualité | SonarQube · JUnit 5 · Testcontainers |
| Infra | Docker · Docker Compose · GitHub Actions · Terraform |
| Monitoring | Spring Actuator · Micrometer · Prometheus · Grafana |

---

## Démarrage rapide (dev)

### Prérequis
- Docker Desktop ou Docker Engine + Docker Compose v2
- Java 21 (pour le développement backend hors Docker)
- Node.js 22 (pour le développement frontend hors Docker)

### Lancer l'environnement complet

```bash
# 1. Cloner le repo
git clone https://github.com/GITHUB_USERNAME/devfeed.git
cd devfeed

# 2. Créer le fichier d'environnement
cp .env.example .env
# Éditer .env et remplir les valeurs

# 3. (Linux uniquement) Prérequis Elasticsearch / SonarQube
sudo sysctl -w vm.max_map_count=262144

# 4. Démarrer tous les services
docker compose up --build -d
```

### Services disponibles

| Service | URL |
|---|---|
| Frontend Angular | http://localhost:4200 |
| Backend API | http://localhost:8080 |
| Swagger UI | http://localhost:8080/swagger-ui.html |
| PgAdmin | http://localhost:5050 |
| SonarQube | http://localhost:9000 |
| Prometheus | http://localhost:9090 |
| Grafana | http://localhost:3000 |

---

## Structure du projet

```
devfeed/
├── backend/          # Spring Boot 3.3.13
├── frontend/         # Angular 21
├── docker/           # Configurations des services Docker
├── docs/             # Documentation (expression du besoin, modélisation)
├── .github/          # GitHub Actions workflows, templates PR/issues
├── docker-compose.yml
├── docker-compose.override.yml   # Dev
└── docker-compose.prod.yml       # Production
```

---

## Documentation

- [Expression du besoin & user stories](docs/expression-du-besoin.md)
- [Modèle de données](docs/modelisation/devfeed.drawio.png)
- [Guide de contribution](CONTRIBUTING.md)

---

## Licence

MIT
