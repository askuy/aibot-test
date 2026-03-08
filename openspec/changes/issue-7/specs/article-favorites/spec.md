## ADDED Requirements

### Requirement: User can favorite an article
The system SHALL allow authenticated users to favorite an article by sending a POST request to `/articles/:id/favorite`. The system MUST return an error if the user is not authenticated or the article does not exist. Duplicate favorite requests for the same article SHALL be idempotent.

#### Scenario: Authenticated user favorites an article
- **WHEN** an authenticated user sends `POST /articles/:id/favorite`
- **THEN** the system creates a favorite record linking the user and article, and returns HTTP 200 with the updated favorite status

#### Scenario: Unauthenticated user attempts to favorite an article
- **WHEN** an unauthenticated user sends `POST /articles/:id/favorite`
- **THEN** the system returns HTTP 401 Unauthorized

#### Scenario: User favorites a non-existent article
- **WHEN** an authenticated user sends `POST /articles/:id/favorite` with a non-existent article ID
- **THEN** the system returns HTTP 404 Not Found

#### Scenario: User favorites an already-favorited article
- **WHEN** an authenticated user sends `POST /articles/:id/favorite` for an article they have already favorited
- **THEN** the system returns HTTP 200 without creating a duplicate record

### Requirement: User can unfavorite an article
The system SHALL allow authenticated users to remove a favorite by sending a `DELETE /articles/:id/favorite`. The system MUST return an error if the user is not authenticated. Attempts to unfavorite an article that is not currently favorited SHALL be idempotent.

#### Scenario: Authenticated user unfavorites an article
- **WHEN** an authenticated user sends `DELETE /articles/:id/favorite` for an article they have favorited
- **THEN** the system removes the favorite record and returns HTTP 200

#### Scenario: Unauthenticated user attempts to unfavorite an article
- **WHEN** an unauthenticated user sends `DELETE /articles/:id/favorite`
- **THEN** the system returns HTTP 401 Unauthorized

#### Scenario: User unfavorites an article not in their favorites
- **WHEN** an authenticated user sends `DELETE /articles/:id/favorite` for an article they have not favorited
- **THEN** the system returns HTTP 200 without error

### Requirement: User can view their favorites list
The system SHALL provide an endpoint `GET /users/me/favorites` that returns a paginated list of articles favorited by the authenticated user, ordered by favorite creation time descending.

#### Scenario: Authenticated user retrieves favorites list
- **WHEN** an authenticated user sends `GET /users/me/favorites`
- **THEN** the system returns HTTP 200 with a paginated list of the user's favorited articles, each including article metadata and the time it was favorited

#### Scenario: Authenticated user with no favorites retrieves favorites list
- **WHEN** an authenticated user sends `GET /users/me/favorites` and has no favorites
- **THEN** the system returns HTTP 200 with an empty list

#### Scenario: Unauthenticated user attempts to retrieve favorites list
- **WHEN** an unauthenticated user sends `GET /users/me/favorites`
- **THEN** the system returns HTTP 401 Unauthorized

#### Scenario: User requests a specific page of their favorites list
- **WHEN** an authenticated user sends `GET /users/me/favorites` with pagination parameters (e.g., `?page=2&limit=10`)
- **THEN** the system returns the corresponding page of favorited articles

### Requirement: Article response includes favorite status for authenticated users
The system SHALL include a `favorited` boolean field in article detail and list responses when the requesting user is authenticated, indicating whether the current user has favorited that article.

#### Scenario: Authenticated user views an article they have favorited
- **WHEN** an authenticated user retrieves an article they have favorited
- **THEN** the article response includes `"favorited": true`

#### Scenario: Authenticated user views an article they have not favorited
- **WHEN** an authenticated user retrieves an article they have not favorited
- **THEN** the article response includes `"favorited": false`

#### Scenario: Unauthenticated user views an article
- **WHEN** an unauthenticated user retrieves an article
- **THEN** the article response omits the `favorited` field or returns `"favorited": false`

### Requirement: Favorites data is persisted in storage
The system SHALL persist all favorite records in a durable storage layer (e.g., a `user_favorites` database table). The storage MUST record the user ID, article ID, and the timestamp when the favorite was created. An index on `user_id` MUST exist to ensure efficient list queries.

#### Scenario: Favorite record is saved after a favorite action
- **WHEN** a user successfully favorites an article
- **THEN** a record with `user_id`, `article_id`, and `created_at` is written to persistent storage

#### Scenario: Favorite record is removed after an unfavorite action
- **WHEN** a user successfully unfavorites an article
- **THEN** the corresponding record is deleted from persistent storage

#### Scenario: Favorites list query performs within acceptable time
- **WHEN** the system queries favorites for a user with a large number of favorites
- **THEN** the query uses the `user_id` index and returns results without a full table scan
