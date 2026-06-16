# Tolgee Platform

## Project Overview

Tolgee is an open-source localization platform designed to simplify translation processes for developers. It features in-context translation, one-click screenshots, and machine translation support. 

### Architecture
- **Backend:** Kotlin, Spring Boot, Gradle. Uses Postgres for the database and Liquibase for database migrations.
- **Frontend (`webapp/`):** React, TypeScript, Vite.
- **Library (`library/`):** Shared internal packages or UI library.
- **E2E Testing (`e2e/`):** Cypress-based end-to-end tests.

## Building and Running

### Prerequisites
- Java 21
- Docker
- Node.js 18 or higher

### Running the Stack locally

1. **Backend:**
   ```bash
   ./gradlew server-app:bootRun --args='--spring.profiles.active=dev'
   ```
   *Configuration for the dev profile can be found or overridden in `backend/app/src/main/resources/application-dev.yaml`.*

2. **Frontend:**
   ```bash
   npm --prefix library ci
   cd webapp
   npm ci
   npm run start
   ```
   *The frontend will be available at `http://localhost:3000`.*

## Testing

- **Backend Tests (Unit & Integration):**
  ```bash
  ./gradlew test
  ```
- **E2E Tests:** Run Cypress tests located in the `e2e` directory.
  ```bash
  cd e2e
  npm run cy:open # Or npm run cy:run for headless
  ```

## Development Conventions

### Database Migrations
Tolgee uses Liquibase for schema management. To generate a new changelog after modifying entities:
```bash
./gradlew diffChangeLog
```

### Static Analysis and Formatting
- **Frontend (webapp):** Before committing, ensure the code passes static checks:
  ```bash
  cd webapp
  npm run prettier
  npm run tsc
  npm run eslint
  ```
- **Backend:** Format Kotlin code using Ktlint:
  ```bash
  ./gradlew ktlintFormat
  ```

### Logging Business Events
- Backend events are logged automatically using `ActivityHolder` or manually via `BusinessEventPublisher`.
- Frontend events use the hooks `useReportEvent` or `useReportOnce` from `tg.hooks/useReportEvent`.

### Miscellaneous
- You can use the provided `.git-blame-ignore-revs` file by running: `git config blame.ignoreRevsFile .git-blame-ignore-revs`
