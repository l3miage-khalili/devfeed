# Expression du besoin — DevFeed

Plateforme de veille technologique avec recommandations personnalisées.  
Ce document liste les cas d'utilisation sous forme de user stories et critères d'acceptation, organisés par phase de développement.

---

## Acteurs

| Acteur | Description |
|---|---|
| **Visiteur** | Utilisateur non authentifié |
| **Utilisateur** | Inscrit et connecté |
| **Administrateur** | Gère les sources RSS et les tags globaux |
| **Système** | Job planifié (scraper, scoring) |

> ⚠️ La distinction Visiteur / Utilisateur n'est effective qu'à partir de la Phase 4 (authentification). En Phase 1 à 3, les rôles Visiteur et Administrateur coexistent sans gestion de session.

---

## Phase 1 — MVP : CRUD articles + tags manuels, affichage simple

### US-01 — Lister les articles

> **En tant que** visiteur, **je veux** voir la liste des articles disponibles **afin d'** accéder au contenu de la plateforme.

**Critères d'acceptation :**
- Les articles s'affichent avec : titre, source, date de publication, résumé court, et tags associés
- Les articles sont triés par date de publication décroissante par défaut
- Si aucun article n'existe, un message vide est affiché (pas d'erreur)

---

### US-02 — Accéder à l'article source

> **En tant que** visiteur, **je veux** cliquer sur un article **afin d'** être redirigé vers le contenu original.

**Critères d'acceptation :**
- Le clic sur un article ouvre l'URL source dans un nouvel onglet
- Le lien est toujours cliquable, même si la description est absente

---

### US-03 — Créer un article manuellement

> **En tant qu'** administrateur, **je veux** créer un article manuellement **afin d'** alimenter la plateforme avant la mise en place du scraping automatique.

**Critères d'acceptation :**
- Champs obligatoires : titre, URL source, date de publication
- Champs optionnels : description, nom de la source, tags
- Une URL déjà existante en base est rejetée (contrainte d'unicité)
- Une erreur claire est affichée si des champs obligatoires sont manquants
- L'article créé apparaît immédiatement dans la liste

---

### US-04 — Modifier un article

> **En tant qu'** administrateur, **je veux** modifier les métadonnées d'un article **afin de** corriger des erreurs de saisie.

**Critères d'acceptation :**
- Champs modifiables : titre, description, nom de la source, tags
- L'URL source n'est pas modifiable après création (clé métier)
- Les modifications sont reflétées immédiatement dans la liste

---

### US-05 — Supprimer un article

> **En tant qu'** administrateur, **je veux** supprimer un article **afin de** retirer du contenu obsolète ou erroné.

**Critères d'acceptation :**
- Une confirmation est demandée avant suppression
- La suppression retire également les associations de tags sur cet article
- L'article n'apparaît plus dans la liste après suppression

---

### US-06 — Créer un tag

> **En tant qu'** administrateur, **je veux** créer un tag **afin de** pouvoir catégoriser les articles.

**Critères d'acceptation :**
- Champs : nom du tag (obligatoire), description (optionnel)
- Le nom est unique et insensible à la casse (`Java` et `java` = même tag)
- Le tag est immédiatement disponible pour être associé à des articles

---

### US-07 — Lister les tags

> **En tant que** visiteur, **je veux** voir la liste de tous les tags **afin de** comprendre les catégories disponibles sur la plateforme.

**Critères d'acceptation :**
- Les tags s'affichent avec leur nom et le nombre d'articles associés
- Triés alphabétiquement par défaut

---

### US-08 — Associer / retirer des tags à un article

> **En tant qu'** administrateur, **je veux** associer un ou plusieurs tags à un article **afin de** le catégoriser.

**Critères d'acceptation :**
- On peut associer plusieurs tags à un même article
- On peut retirer un tag d'un article
- Seuls les tags existants peuvent être associés (pas de création de tag à la volée)

---

### US-09 — Filtrer les articles par tag

> **En tant que** visiteur, **je veux** filtrer la liste des articles par tag **afin de** ne voir que le contenu qui m'intéresse.

**Critères d'acceptation :**
- Le filtre par tag est accessible depuis la liste des articles
- La sélection de plusieurs tags retourne les articles ayant **au moins un** des tags sélectionnés (logique **OU**)
- Le filtre est combinable avec le tri par date

---

### US-10 — Modifier un tag

> **En tant qu'** administrateur, **je veux** modifier un tag existant **afin de** corriger son nom ou sa description.

**Critères d'acceptation :**
- Le nom et la description sont modifiables
- Le nouveau nom doit rester unique et insensible à la casse
- La modification est répercutée sur tous les articles déjà associés à ce tag

---

### US-11 — Supprimer un tag

> **En tant qu'** administrateur, **je veux** supprimer un tag **afin de** retirer une catégorie obsolète ou erronée.

**Critères d'acceptation :**
- Une confirmation est demandée avant suppression
- La suppression retire le tag de tous les articles associés (pas de suppression en cascade des articles eux-mêmes)
- Le tag n'apparaît plus dans la liste des tags ni dans les filtres

---

### Récapitulatif Phase 1

| ID | User Story | Acteur |
|---|---|---|
| US-01 | Lister les articles | Visiteur |
| US-02 | Accéder à l'article source (redirection) | Visiteur |
| US-03 | Créer un article manuellement | Admin |
| US-04 | Modifier un article | Admin |
| US-05 | Supprimer un article | Admin |
| US-06 | Créer un tag | Admin |
| US-07 | Lister les tags | Visiteur |
| US-08 | Associer / retirer des tags à un article | Admin |
| US-09 | Filtrer les articles par tag (OU) | Visiteur |
| US-10 | Modifier un tag | Admin |
| US-11 | Supprimer un tag | Admin |

---

## Phase 2 — Ingestion automatique : scraping RSS + déduplication

### US-12 — Ajouter une source RSS

> **En tant qu'** administrateur, **je veux** ajouter une source RSS **afin que** la plateforme puisse ingérer automatiquement ses articles.

**Critères d'acceptation :**
- Champs obligatoires : URL du flux RSS, nom de la source
- Champ optionnel : fréquence de scraping (sinon fréquence globale par défaut)
- L'URL du flux est validée au moment de l'ajout (format valide + flux RSS accessible)
- Une URL de flux déjà existante est rejetée (unicité)
- La source est immédiatement active après ajout

---

### US-13 — Lister les sources RSS

> **En tant qu'** administrateur, **je veux** voir la liste des sources configurées **afin de** savoir quelles sources alimentent la plateforme.

**Critères d'acceptation :**
- Chaque source affiche : nom, URL, statut (active/inactive), date du dernier scraping réussi

---

### US-14 — Activer / désactiver une source RSS

> **En tant qu'** administrateur, **je veux** activer ou désactiver une source **afin de** suspendre temporairement son ingestion sans la supprimer.

**Critères d'acceptation :**
- Une source désactivée n'est pas traitée lors des runs de scraping
- Le statut est visible dans la liste des sources (US-13)

---

### US-15 — Supprimer une source RSS

> **En tant qu'** administrateur, **je veux** supprimer une source RSS **afin de** retirer définitivement une source non pertinente.

**Critères d'acceptation :**
- Une confirmation est demandée avant suppression
- Les articles déjà ingérés depuis cette source **ne sont pas supprimés**
- La source n'apparaît plus dans la liste après suppression

---

### US-16 — Scraper périodiquement les flux RSS actifs

> **En tant que** système, **je veux** exécuter un job planifié **afin d'** ingérer automatiquement les nouveaux articles depuis les sources RSS actives.

**Critères d'acceptation :**
- Le job s'exécute selon la fréquence configurée (globale ou par source)
- Seules les sources avec statut "active" sont traitées
- Les articles parsés sont enregistrés en base avec : titre, URL, description, date de publication, source
- Si une source est inaccessible, le job continue avec les autres sources (pas d'arrêt global)
- Les erreurs par source sont enregistrées (pour US-20)

---

### US-17 — Dédupliquer les articles à l'ingestion

> **En tant que** système, **je veux** détecter les doublons avant insertion **afin d'** éviter que le même article apparaisse plusieurs fois dans la plateforme.

**Critères d'acceptation :**
- Un article dont l'URL canonique existe déjà en base n'est pas réinséré, quelle que soit la source
- Les articles ignorés pour doublon sont comptabilisés dans les métriques du run (US-18)

> 💡 La déduplication par similarité de titre est envisagée comme amélioration future (voir section *Améliorations futures*).

---

### US-18 — Enregistrer les métriques d'un run de scraping

> **En tant que** système, **je veux** enregistrer les résultats de chaque run **afin de** permettre leur consultation par l'administrateur.

**Critères d'acceptation :**
- Pour chaque run : date/heure de début et de fin, nombre d'articles ingérés, nombre de doublons ignorés, nombre d'erreurs par source
- Ces données sont persistées et consultables (voir US-20)

---

### US-19 — Déclencher manuellement un run de scraping

> **En tant qu'** administrateur, **je veux** déclencher manuellement un run de scraping **afin de** tester une source sans attendre le prochain run planifié.

**Critères d'acceptation :**
- Le déclenchement est disponible depuis l'interface d'administration
- Deux modes disponibles : run sur **toutes les sources actives**, ou run sur **une source spécifique**
- Un retour visuel indique que le run est en cours, puis terminé

---

### US-20 — Consulter les résultats des derniers runs

> **En tant qu'** administrateur, **je veux** consulter les résultats des derniers runs de scraping **afin de** vérifier que l'ingestion fonctionne correctement.

**Critères d'acceptation :**
- Les N derniers runs sont listés avec leurs métriques (US-18)
- En cas d'erreur sur une source, le message d'erreur est consultable

---

### Récapitulatif Phase 2

| ID | User Story | Acteur |
|---|---|---|
| US-12 | Ajouter une source RSS | Admin |
| US-13 | Lister les sources RSS | Admin |
| US-14 | Activer / désactiver une source RSS | Admin |
| US-15 | Supprimer une source RSS | Admin |
| US-16 | Scraper périodiquement les flux RSS actifs | Système |
| US-17 | Dédupliquer les articles à l'ingestion (par URL) | Système |
| US-18 | Enregistrer les métriques d'un run | Système |
| US-19 | Déclencher manuellement un run (global ou par source) | Admin |
| US-20 | Consulter les résultats des derniers runs | Admin |

---

## Phase 3 — Tagging automatique basique

### US-21 — Associer des mots-clés à un tag

> **En tant qu'** administrateur, **je veux** associer une liste de mots-clés à un tag **afin que** les articles contenant ces mots-clés soient automatiquement catégorisés.

**Critères d'acceptation :**
- Pour chaque tag existant, on peut définir une liste de mots-clés déclencheurs (ex : tag `DevOps` → mots-clés `docker`, `kubernetes`, `CI/CD`)
- La correspondance est insensible à la casse
- Un même mot-clé peut être associé à plusieurs tags
- Les mots-clés sont évalués sur le titre **et** la description de l'article

---

### US-22 — Consulter les règles de tagging

> **En tant qu'** administrateur, **je veux** voir la liste des règles de tagging configurées **afin de** les gérer facilement.

**Critères d'acceptation :**
- Pour chaque tag, ses mots-clés associés sont listés
- Les tags sans mots-clés configurés sont visibles (pour inciter à les compléter)

---

### US-23 — Supprimer un mot-clé d'un tag

> **En tant qu'** administrateur, **je veux** supprimer un mot-clé d'un tag **afin de** corriger une règle incorrecte.

**Critères d'acceptation :**
- La suppression ne retire le tag que des futurs articles ingérés (pas de rétro-application automatique)
- Les articles déjà tagués via ce mot-clé conservent leur tag

---

### US-24 — Tagger automatiquement un article à l'ingestion

> **En tant que** système, **je veux** appliquer les règles de tagging sur chaque article ingéré **afin de** le catégoriser sans intervention manuelle.

**Critères d'acceptation :**
- Le tagging automatique s'exécute immédiatement après l'ingestion d'un article (dans le même job)
- Les tags auto-assignés s'ajoutent aux éventuels tags manuels sans les écraser
- Si aucun mot-clé ne correspond, l'article est ingéré sans tag (pas d'erreur)
- Le nombre de tags auto-assignés est comptabilisé dans les métriques du run (US-18)
- L'origine d'un tag est enregistrée en base : `MANUAL` ou `AUTO`
- Dans l'interface, les tags auto-assignés sont distingués visuellement par une icône (ex : ✨ ou 🤖), les tags manuels s'affichent sans icône

---

### Récapitulatif Phase 3

| ID | User Story | Acteur |
|---|---|---|
| US-21 | Associer des mots-clés à un tag | Admin |
| US-22 | Consulter les règles de tagging | Admin |
| US-23 | Supprimer un mot-clé d'un tag | Admin |
| US-24 | Tagger automatiquement un article à l'ingestion | Système |

---

## Phase 4 — Authentification + profils utilisateurs + tags favoris

### US-25 — S'inscrire

> **En tant que** visiteur, **je veux** créer un compte **afin d'** accéder aux fonctionnalités personnalisées de la plateforme.

**Critères d'acceptation :**
- Champs obligatoires : adresse e-mail, mot de passe, nom d'affichage
- L'e-mail doit être unique (rejet avec message clair si déjà utilisé)
- Le mot de passe doit respecter une politique minimale (8 caractères minimum, au moins 1 chiffre)
- Après inscription, l'utilisateur est automatiquement connecté

---

### US-26 — Se connecter

> **En tant que** visiteur, **je veux** me connecter à mon compte **afin d'** accéder à mon espace personnel.

**Critères d'acceptation :**
- Authentification par e-mail + mot de passe
- En cas d'identifiants incorrects, un message d'erreur générique est affiché (ne pas préciser si c'est l'e-mail ou le mot de passe qui est faux)
- Une session JWT est créée et stockée côté client après connexion réussie
- L'utilisateur est redirigé vers la page d'accueil après connexion

---

### US-27 — Se déconnecter

> **En tant qu'** utilisateur, **je veux** me déconnecter **afin de** sécuriser mon compte sur un appareil partagé.

**Critères d'acceptation :**
- La déconnexion invalide le token JWT côté client
- L'utilisateur est redirigé vers la page d'accueil en tant que visiteur
- Les pages protégées deviennent inaccessibles immédiatement après déconnexion

---

### US-28 — Consulter son profil

> **En tant qu'** utilisateur, **je veux** accéder à ma page de profil **afin de** voir mes informations personnelles et mes tags favoris.

**Critères d'acceptation :**
- Les informations affichées : nom d'affichage, adresse e-mail, tags favoris
- La page est accessible uniquement aux utilisateurs connectés

---

### US-29 — Modifier ses informations personnelles

> **En tant qu'** utilisateur, **je veux** modifier mon nom d'affichage et mon adresse e-mail **afin de** maintenir mon profil à jour.

**Critères d'acceptation :**
- Le nom d'affichage et l'e-mail sont modifiables
- Si le nouvel e-mail est déjà utilisé par un autre compte, la modification est rejetée
- Les modifications sont reflétées immédiatement dans l'interface

---

### US-30 — Modifier son mot de passe

> **En tant qu'** utilisateur, **je veux** modifier mon mot de passe **afin de** sécuriser mon compte.

**Critères d'acceptation :**
- L'utilisateur doit saisir son mot de passe actuel avant de pouvoir le modifier
- Le nouveau mot de passe doit respecter la même politique que lors de l'inscription (US-25)
- Après modification, la session en cours reste active

---

### US-31 — Ajouter un tag à ses favoris

> **En tant qu'** utilisateur, **je veux** ajouter un tag à mes favoris **afin d'** indiquer mes centres d'intérêt pour les recommandations futures.

**Critères d'acceptation :**
- Un tag peut être mis en favori depuis la liste des tags (US-07) ou depuis un article
- Un tag ne peut être en favori qu'une seule fois par utilisateur
- L'ajout est reflété immédiatement dans le profil (US-28)

---

### US-32 — Retirer un tag de ses favoris

> **En tant qu'** utilisateur, **je veux** retirer un tag de mes favoris **afin de** mettre à jour mes centres d'intérêt.

**Critères d'acceptation :**
- Le retrait est possible depuis la page de profil et depuis la liste des tags
- Le retrait est reflété immédiatement

---

### US-33 — Protéger l'interface d'administration

> **En tant que** visiteur ou utilisateur, **je veux** que les pages d'administration soient inaccessibles sans rôle admin **afin de** protéger les données de la plateforme.

**Critères d'acceptation :**
- Toute route d'administration redirige vers une page d'erreur 403 si l'utilisateur n'a pas le rôle `ADMIN`
- Le rôle `ADMIN` est assigné manuellement en base de données (pas de flux d'inscription admin)

---

### US-34 — Protéger les fonctionnalités utilisateur

> **En tant que** visiteur, **je veux** être redirigé vers la page de connexion si j'essaie d'accéder à une page protégée **afin d'** être invité à me connecter.

**Critères d'acceptation :**
- Les routes protégées (profil, tags favoris) redirigent vers la page de connexion si non connecté
- Après connexion réussie, l'utilisateur est renvoyé vers la page qu'il tentait d'atteindre

---

### Récapitulatif Phase 4

| ID | User Story | Acteur |
|---|---|---|
| US-25 | S'inscrire | Visiteur |
| US-26 | Se connecter | Visiteur |
| US-27 | Se déconnecter | Utilisateur |
| US-28 | Consulter son profil | Utilisateur |
| US-29 | Modifier ses informations personnelles | Utilisateur |
| US-30 | Modifier son mot de passe | Utilisateur |
| US-31 | Ajouter un tag à ses favoris | Utilisateur |
| US-32 | Retirer un tag de ses favoris | Utilisateur |
| US-33 | Protéger l'interface d'administration | Système / Admin |
| US-34 | Protéger les fonctionnalités utilisateur | Système / Visiteur |

---

## Phase 5 — Algorithme de recommandation + tracking des interactions

### US-35 — Enregistrer la lecture d'un article

> **En tant que** système, **je veux** enregistrer automatiquement qu'un utilisateur a ouvert un article **afin d'** alimenter son historique de lecture pour les recommandations.

**Critères d'acceptation :**
- La lecture est tracée au clic sur l'article (redirection vers la source), sans action supplémentaire de l'utilisateur
- Un même article relu ne génère pas d'entrée dupliquée dans l'historique (idempotent)
- Le tracking ne s'applique qu'aux utilisateurs connectés (les visiteurs ne sont pas tracés)

---

### US-36 — Sauvegarder un article

> **En tant qu'** utilisateur, **je veux** sauvegarder un article **afin de** le retrouver facilement plus tard et d'affiner mes recommandations.

**Critères d'acceptation :**
- Un bouton "Sauvegarder" est disponible sur chaque article dans le fil
- Un article déjà sauvegardé affiche un état visuel différent (bouton actif)
- Il est possible d'annuler la sauvegarde depuis le fil ou depuis la liste des articles sauvegardés

---

### US-37 — Signaler "pas intéressé" par un article

> **En tant qu'** utilisateur, **je veux** signaler qu'un article ne m'intéresse pas **afin qu'** il n'apparaisse plus dans mon fil et que les recommandations similaires soient pénalisées.

**Critères d'acceptation :**
- Un bouton "Pas intéressé" est disponible sur chaque article dans le fil
- L'article est immédiatement retiré du fil après l'action
- L'action est prise en compte dans le calcul du score (pénalité sur les tags et profils d'articles similaires)

---

### US-38 — Consulter ses articles sauvegardés

> **En tant qu'** utilisateur, **je veux** accéder à la liste de mes articles sauvegardés **afin de** les relire à tout moment.

**Critères d'acceptation :**
- La liste est accessible depuis le profil ou la navigation principale
- Les articles sont triés par date de sauvegarde décroissante
- Il est possible de retirer un article de la liste directement depuis cette vue

---

### US-39 — Voir un fil d'articles personnalisé

> **En tant qu'** utilisateur, **je veux** voir un fil d'articles classés par pertinence **afin de** trouver rapidement du contenu correspondant à mes intérêts.

**Critères d'acceptation :**
- Le fil est calculé selon la formule de scoring : `score = (poids_tags_favoris × 3) + (poids_similarité_historique × 2) + (fraîcheur × 1)`
- Les articles déjà lus et les articles signalés "pas intéressé" sont exclus du fil
- Pour un visiteur non connecté, le fil affiche les articles les plus récents (pas de personnalisation)

---

### US-40 — Naviguer dans le fil avec infinite scroll

> **En tant qu'** utilisateur, **je veux** faire défiler le fil sans pagination explicite **afin de** consulter le contenu de façon fluide.

**Critères d'acceptation :**
- Les articles se chargent automatiquement au fur et à mesure du défilement (par lots)
- Un indicateur de chargement est affiché pendant le chargement du lot suivant
- Lorsqu'il n'y a plus d'articles à charger, un message de fin est affiché

---

### US-41 — Calculer le score de recommandation

> **En tant que** système, **je veux** calculer un score de pertinence pour chaque article et chaque utilisateur **afin de** classer le fil personnalisé.

**Critères d'acceptation :**
- Le score applique la formule : `score = (poids_tags_favoris × 3) + (poids_similarité_historique × 2) + (fraîcheur × 1)`
- **Poids tags favoris** : nombre de tags de l'article présents dans les favoris de l'utilisateur
- **Poids similarité historique** : nombre de tags en commun avec les articles lus ou sauvegardés
- **Fraîcheur** : score décroissant selon l'ancienneté de l'article (score max dans les 24h, décroissance progressive)
- Le calcul est effectué à la volée à chaque requête sur `GET /api/feed` (la mise en cache Redis est prévue en Phase 6)

---

### US-42 — Gérer le cold start

> **En tant que** système, **je veux** appliquer une stratégie de recommandation pour les utilisateurs sans historique **afin qu'** ils aient un fil pertinent dès leur inscription.

**Critères d'acceptation :**
- Un utilisateur est considéré en "cold start" s'il n'a ni historique de lecture ni article sauvegardé
- En cold start, le fil affiche les articles les plus populaires globalement (basé sur le nombre total de sauvegardes et de lectures), triés par fraîcheur
- Dès la première interaction (lecture ou sauvegarde), le scoring personnalisé prend le relais

---

### Récapitulatif Phase 5

| ID | User Story | Acteur |
|---|---|---|
| US-35 | Enregistrer la lecture d'un article (au clic) | Système |
| US-36 | Sauvegarder un article | Utilisateur |
| US-37 | Signaler "pas intéressé" par un article | Utilisateur |
| US-38 | Consulter ses articles sauvegardés | Utilisateur |
| US-39 | Voir un fil d'articles personnalisé | Utilisateur / Visiteur |
| US-40 | Naviguer dans le fil avec infinite scroll | Utilisateur |
| US-41 | Calculer le score de recommandation | Système |
| US-42 | Gérer le cold start | Système |

---

## Phase 6 — Optimisation : cache Redis, pagination, recherche full-text

### US-43 — Mettre en cache les scores de recommandation

> **En tant que** système, **je veux** mettre en cache les scores de recommandation calculés par utilisateur **afin d'** éviter de les recalculer à chaque requête sur le fil.

**Critères d'acceptation :**
- Après le premier calcul, le score de chaque article pour un utilisateur donné est stocké dans Redis
- Le cache est utilisé en priorité pour servir le fil (`GET /api/feed`) si une entrée valide existe
- TTL par défaut : **1 heure** (paramétrable via configuration)

---

### US-44 — Invalider le cache lors d'une interaction utilisateur

> **En tant que** système, **je veux** invalider le cache d'un utilisateur dès qu'il interagit avec un article **afin que** son fil reflète ses nouvelles préférences sans attendre l'expiration du TTL.

**Critères d'acceptation :**
- Toute interaction (lecture, sauvegarde, "pas intéressé", ajout/retrait de tag favori) invalide le cache de cet utilisateur
- Après invalidation, le prochain appel à `GET /api/feed` recalcule et re-met en cache les scores

---

### US-45 — Rechercher des articles par mots-clés

> **En tant qu'** utilisateur ou visiteur, **je veux** rechercher des articles par mots-clés **afin de** trouver du contenu sur un sujet précis sans passer par les filtres de tags.

**Critères d'acceptation :**
- Un champ de recherche est accessible depuis la navigation principale
- La recherche porte sur le titre et la description des articles
- Les résultats sont triés par pertinence (correspondance avec les termes recherchés)
- La recherche retourne un message explicite si aucun résultat n'est trouvé
- Implémentation : PostgreSQL full-text search (`tsvector` / `tsquery`)

> 💡 L'intégration d'Elasticsearch pour une recherche plus avancée est envisagée comme amélioration future (voir section *Améliorations futures*).

---

### US-46 — Paginer les résultats de recherche

> **En tant qu'** utilisateur ou visiteur, **je veux** naviguer dans les résultats de recherche par pages **afin de** parcourir de nombreux résultats sans surcharger l'interface.

**Critères d'acceptation :**
- Les résultats de recherche sont paginés (taille de page configurable, 20 résultats par défaut)
- Des contrôles de navigation (page précédente / suivante) sont disponibles
- Le nombre total de résultats est affiché

---

### US-47 — Paginer la liste des articles (vue admin)

> **En tant qu'** administrateur, **je veux** naviguer dans la liste des articles par pages **afin de** gérer un grand volume d'articles sans dégradation de performance.

**Critères d'acceptation :**
- La liste des articles en vue admin est paginée (20 articles par page par défaut)
- Des contrôles de navigation sont disponibles
- Les filtres et le tri existants (US-09) restent compatibles avec la pagination

---

### US-48 — Paginer la liste des articles sauvegardés

> **En tant qu'** utilisateur, **je veux** naviguer dans mes articles sauvegardés par pages **afin de** consulter facilement une longue liste de favoris.

**Critères d'acceptation :**
- La liste des articles sauvegardés (US-38) est paginée (20 articles par page par défaut)
- Des contrôles de navigation sont disponibles

---

### Récapitulatif Phase 6

| ID | User Story | Acteur |
|---|---|---|
| US-43 | Mettre en cache les scores de recommandation (TTL 1h) | Système |
| US-44 | Invalider le cache lors d'une interaction utilisateur | Système |
| US-45 | Rechercher des articles par mots-clés (PostgreSQL full-text) | Utilisateur / Visiteur |
| US-46 | Paginer les résultats de recherche | Utilisateur / Visiteur |
| US-47 | Paginer la liste des articles (vue admin) | Admin |
| US-48 | Paginer la liste des articles sauvegardés | Utilisateur |

---

## Phase 7 — Infra / déploiement : CI/CD, monitoring

### US-49 — Lancer l'environnement local en une commande

> **En tant que** développeur, **je veux** démarrer l'environnement complet (backend, frontend, PostgreSQL, Redis) avec une seule commande Docker Compose **afin de** contribuer au projet sans configuration manuelle.

**Critères d'acceptation :**
- Un `docker compose up` démarre tous les services nécessaires
- Les variables d'environnement sensibles sont externalisées dans un fichier `.env` (non versionné)
- Un fichier `.env.example` documenté est versionné en guise de référence
- L'application est accessible localement après démarrage (frontend + API)

---

### US-50 — Construire des images Docker optimisées

> **En tant que** système, **je veux** que les images Docker soient construites en multi-stage build **afin de** produire des artefacts légers et sécurisés pour la production.

**Critères d'acceptation :**
- Le Dockerfile backend utilise un multi-stage build (étape de build JVM séparée de l'image finale)
- Le Dockerfile frontend utilise un multi-stage build (étape de build Angular séparée du serveur de fichiers statiques)
- L'image finale ne contient pas d'outils de build (Maven, Node.js) ni de code source

---

### US-51 — Exécuter les tests automatiquement à chaque Pull Request

> **En tant que** développeur, **je veux** que les tests soient exécutés automatiquement à chaque Pull Request **afin de** détecter les régressions avant la fusion.

**Critères d'acceptation :**
- Le workflow CI se déclenche sur chaque PR ouverte ou mise à jour vers `main`
- Les étapes exécutées : lint, compilation, tests unitaires, tests d'intégration (Testcontainers)
- La PR est bloquée si une étape échoue (branch protection rule)
- Le rapport de couverture de tests est publié en commentaire de la PR

---

### US-52 — Analyser la qualité du code avec SonarQube

> **En tant que** développeur, **je veux** qu'une analyse de qualité de code soit exécutée automatiquement **afin de** maintenir des standards élevés visibles sur le dépôt.

**Critères d'acceptation :**
- L'analyse SonarQube est intégrée au workflow CI (US-51)
- Les métriques clés sont visibles : couverture de tests, code smells, vulnérabilités, duplications
- Un badge SonarQube est affiché dans le README du dépôt

---

### US-53 — Publier les images Docker sur GitHub Container Registry

> **En tant que** système, **je veux** que les images Docker soient publiées automatiquement après chaque merge sur `main` **afin de** disposer d'artefacts versionnés et prêts à déployer.

**Critères d'acceptation :**
- Le workflow CD se déclenche après merge sur `main` (uniquement si le CI est passé)
- Les images backend et frontend sont taguées avec le SHA du commit et le tag `latest`
- Les images sont publiées sur GitHub Container Registry (ghcr.io)

---

### US-54 — Déployer automatiquement l'application

> **En tant que** système, **je veux** que l'application soit déployée automatiquement sur la plateforme cible après publication des images **afin de** mettre en production les nouvelles fonctionnalités sans intervention manuelle.

**Critères d'acceptation :**
- Le déploiement se déclenche à la suite de la publication des images (US-53)
- La plateforme cible est Render ou Railway (configuration via variables d'environnement)
- Un échec de déploiement est notifié via GitHub Actions
- L'URL de l'application déployée est stable et documentée dans le README

---

### US-55 — Provisionner l'infrastructure avec Terraform

> **En tant que** développeur, **je veux** décrire l'infrastructure cible sous forme de code avec Terraform **afin de** pouvoir la reproduire, la versionner et l'auditer.

**Critères d'acceptation :**
- La configuration Terraform décrit les ressources nécessaires sur la plateforme cible
- Le state Terraform est stocké à distance (backend distant : Terraform Cloud ou bucket S3)
- Tout changement d'infrastructure passe par un `terraform plan` (review) puis `terraform apply`
- Les fichiers Terraform sont versionnés dans le dépôt dans un dossier `infra/`

---

### US-56 — Exposer les métriques applicatives

> **En tant que** système, **je veux** exposer les métriques de l'application via Spring Actuator et Micrometer **afin de** les rendre disponibles pour un système de monitoring.

**Critères d'acceptation :**
- L'endpoint `/actuator/prometheus` expose les métriques au format Prometheus
- Métriques métier incluses : nombre d'articles ingérés par run, taux d'échec de scraping, nombre de requêtes sur `/api/feed`
- L'endpoint `/actuator/health` expose le statut de santé de l'application (BDD, Redis)
- Les endpoints Actuator sont protégés (non exposés publiquement sans authentification)

---

### US-57 — Visualiser les métriques dans un dashboard Grafana

> **En tant qu'** administrateur, **je veux** consulter un dashboard Grafana **afin d'** avoir une vue consolidée des métriques de la plateforme.

**Critères d'acceptation :**
- Un service Prometheus est configuré pour scraper les métriques exposées (US-56)
- Un dashboard Grafana préconfiguré est versionné dans le dépôt
- Le dashboard inclut au minimum : santé des services, métriques de scraping, temps de réponse de l'API
- Prometheus et Grafana sont intégrés au `docker compose up` de l'environnement local (US-49)

---

### Récapitulatif Phase 7

| ID | User Story | Acteur |
|---|---|---|
| US-49 | Lancer l'environnement local en une commande (Docker Compose) | Développeur |
| US-50 | Construire des images Docker optimisées (multi-stage) | Système |
| US-51 | Exécuter les tests automatiquement à chaque Pull Request (CI) | Système |
| US-52 | Analyser la qualité du code avec SonarQube | Système |
| US-53 | Publier les images Docker sur GitHub Container Registry (CD) | Système |
| US-54 | Déployer automatiquement l'application | Système |
| US-55 | Provisionner l'infrastructure avec Terraform (IaC) | Développeur |
| US-56 | Exposer les métriques applicatives (Actuator + Micrometer) | Système |
| US-57 | Visualiser les métriques dans un dashboard Grafana | Admin |

---

## Améliorations futures

Cette section recense les idées et améliorations identifiées en cours de rédaction, délibérément écartées du scope initial pour ne pas complexifier les phases en cours.

| ID | Amélioration | Phase d'origine | Description |
|---|---|---|---|
| AF-01 | Déduplication par similarité de titre | Phase 2 — US-17 | Détecter les doublons inter-sources sur la base de la proximité de titre (ex : distance de Levenshtein) en complément de la déduplication par URL exacte. |
| AF-02 | Rétro-application du tagging automatique | Phase 3 — US-25 | Permettre à l'administrateur de déclencher manuellement l'application des règles de tagging sur les articles déjà ingérés, sans rééingérer le contenu. |
| AF-03 | Réinitialisation du mot de passe oublié | Phase 4 | Permettre à un utilisateur de réinitialiser son mot de passe via un lien envoyé par e-mail. Nécessite l'intégration d'un service d'envoi d'e-mail. |
| AF-04 | Suppression du compte utilisateur | Phase 4 | Permettre à un utilisateur de supprimer son compte et l'ensemble de ses données personnelles (profil, favoris, historique d'interactions), conformément aux exigences RGPD. |
| AF-05 | Recherche full-text avec Elasticsearch | Phase 6 — US-45 | Remplacer ou compléter la recherche PostgreSQL par Elasticsearch pour des capacités de recherche avancées (stemming, fuzzy matching, auto-complétion). |
