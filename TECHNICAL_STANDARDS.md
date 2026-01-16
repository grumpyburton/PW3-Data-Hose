# Technical Standards for the Application

[Return to README](./README.md)

---
## AI Assistant Directives
* Always comply with the standards in this document before generating code.
* Infer missing configurations from conventions listed here.
* Do not add additional libraries unless explicitly mentioned.
* For all file outputs, use standard directory structure and naming conventions.
* Validate generated code for security, performance, and maintainability.
* DO NOT EDIT THE FOLLOWING FILES:
  * README.md
  * TECHNICAL_STANDARDS.md
  * REQUIREMENTS.md

---

## Locale 
* Locale code: en-AU
* Language: English (en)
* Country: Australia (AU)
* Date notation: The day-month-year format (e.g., 23/12/2025)
* Currency code: AUD
* Decimal separator: . (period)

---

## Code Style and Conventions
* Follow standard Java conventions for naming and formatting (Google Java Style Guide preferred).
* Use descriptive names for classes, methods, and variables.
* Include Javadoc for all public classes and methods.
* In Angular:
    * Follow Angular Style Guide (https://angular.io/guide/styleguide).
    * Use kebab-case for component selectors and SCREAMING_SNAKE_CASE for constants.
    * Use reactive forms, not template-driven forms.
    * Use Observables (RxJS) over Promises where possible.
* Include unit tests for all services and components.

---

## Directory Structure
* The backend will be created in the directory "/backend"
* The frontend will be created in the directory "/frontend"
```
/project-root
    /backend
        /src
            /main
                /java/com/example/app
                /resources
                    /static (frontend build output)
                    /templates (if used)
            /test
    /frontend
        /src
            /app
                /core
                /shared
                /features
            /assets
            /environments
```


---

## Front end
* The front end will be developed in Angular 20 using Angular Material as the component library
* No other libraries will be used unless specified here
* Admin components will run on the "/admin/" prefix
* Use images from the "/resources" directory for things like favicon
* Build navigation toolbar at the top of every page
* Add a footer at the bottom of ever page
* If a 403 is received on an API call, redirect the application back to the home page
* Any Dates will have the Angular Material Datepicker component
---

## Back end
* The back end will be developed in Spring Boot v3.5.9
* Java version to target is 21
* All APIs will run on the "/api/" prefix
* * If required, Spring AI will be used for all generative AI tasks
  * pgvector will be used as the vector store in postgres
  * openai models can be used and set by config in the app
* If required, Drools v8.44.0.Final will be used as the rules engine
* DO NOT USE Project Lombok library in the project
* DO NOT USE flyawaydb library in the project

---

## API Design and Documentation
* REST endpoints should follow standard naming conventions:
    * `GET /api/customers`
    * `POST /api/customers`
    * `PUT /api/customers/{id}`
    * `DELETE /api/customers/{id}`
* Use request/response DTOs (no entity exposure).
* Swagger / OpenAPI must be enabled via SpringDoc.
* Return appropriate HTTP status codes.

---

## AI and Rules Integration
* Spring AI will handle all generative and LLM-based tasks.
* Prompt templates must be externalized (YAML or JSON in `/resources/prompts`).
* Use Drools for business eligibility, compliance, and next-best-action logic.
* Rules must be organized by domain (e.g., `/rules/customer/`, `/rules/product/`).
* Maintain a clear separation between:
    * Deterministic logic (Drools)
    * Non-deterministic generation (Spring AI)


---

## Security and Identity
* Use Spring Security for authentication and authorization.
* JWT-based authentication preferred.
* All API endpoints must require authentication unless explicitly marked as public.
* Sanitize all user input and validate request payloads.
* HTTPS must be enforced in all Heroku environments.

---

## Error Handling and Logging
* Use `@ControllerAdvice` and `@ExceptionHandler` for centralized exception handling.
* Return structured error responses with:
    * `timestamp`, `status`, `error`, `message`, and `path`
* Use SLF4J for logging (no `System.out.println()`).
* Log at appropriate levels:
    * `INFO` for major lifecycle events.
    * `DEBUG` for internal flow.
    * `ERROR` for exceptions and failures.


---

## Hosting Platform
* The application will be hosted on the Heroku platform
* The database will be postgres
    * setup a docker instance for local development

---

## Configuration Management
* Use Spring Profiles (`dev`, `staging`, `prod`) for environment-specific configs.
* Store sensitive credentials in environment variables, never in code.
* Use `application.yml` rather than `application.properties` for readability.
* For frontend, use Angular environment files (`environment.ts`, `environment.prod.ts`).

---

## Environments
Separate configuration for each of the below environments will be needed:

* dev - will be on a local developer machine
    * Postgres will be in a hosted in a docker instance using the following:
        * spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
        * spring.datasource.username=postgres
        * spring.datasource.password=password
        * spring.jpa.hibernate.ddl-auto=create-drop
        * spring.jpa.show-sql=true

    * Angular proxy will be used to allow the running of Angular on port 4200 and
      Spring Boot on port 8080
* staging - will be on Heroku staging application
    * Postgres will be a heroku managed service using "Heroku Postgres"
* prod - will be on the Heroku production application
    * Postgres will be a heroku managed service using "Heroku Postgres"

---

## Testing Standards
* Backend:
    * Use JUnit 5 and Mockito for unit testing.
    * Use Spring Boot Test for integration tests.
    * Aim for 80%+ code coverage on core logic.
* Frontend:
    * Use Jasmine and Karma for unit testing.
    * Use Cypress for end-to-end (E2E) testing.
* Tests must be runnable via Maven (`mvn test`).

---

## Performance and Non-Functional Requirements
* APIs should respond within 500ms under normal load.
* Support concurrent users up to 1000 (configurable).
* Use connection pooling (HikariCP).
* Enable GZIP compression in Spring Boot.
* Use Angular lazy loading for large modules.


---

## Build and Deployment
* Maven must build both backend and frontend (`mvn clean install` triggers Angular build).
* Parent POM (pom.xml):
  * Packaging: Set the packaging to pom.
  * Modules: Define the backend and frontend modules.
  * Build Plugins: Configure plugins for building both the backend and frontend.
* Packaging: The frontend build output should be placed in the
  backend/src/main/resources/static directory, so Spring Boot can serve it.

* The final JAR must include the frontend static assets.
* Use Heroku Git deployment or GitHub integration.
* Add Procfile: web: java -Dserver.port=$PORT -jar target/*.jar

---

## Version Control
* Use Git for source control.
* Branching strategy: `main` (production), `develop` (staging), feature branches prefixed with `feature/`.
* Use conventional commits:
    * `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`



