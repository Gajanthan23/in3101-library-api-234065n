# IN3101 Lab 03 - Containerised Library API

Gajanthan Sivaanbu | 234065N

Repository: https://github.com/Gajanthan23/in3101-library-api-234065n

Spring Boot CRUD REST API backed by MySQL, following the controller -> service -> repository layers in the lab sheet. Java 21, Spring Boot 4.1.2, springdoc-openapi 3.1.1, Maven Wrapper 3.9.16, MySQL 8.4.

## Run locally

Install JDK 21 and Docker Desktop (or use an existing MySQL server). From the project directory:

```sh
cp .env.example .env
# Set DB_ROOT_PASSWORD in .env.
set -a
. ./.env
set +a
docker compose up -d --wait
./mvnw spring-boot:run
```

On Windows PowerShell, set `$env:DB_ROOT_PASSWORD` to the value in your local `.env`, then run `docker compose up -d --build --wait`.

Alternatively, create `library_db` in an existing MySQL instance and set `DB_URL`, `DB_USER` and `DB_PASSWORD` when running the API outside Compose. Hibernate creates/updates the `book` table.

The application listens at `http://localhost:8080`. Swagger UI: `http://localhost:8080/swagger-ui.html`. OpenAPI JSON: `http://localhost:8080/v3/api-docs`.

## Endpoints

| Method | Path | Success | Errors |
| --- | --- | --- | --- |
| POST | `/api/books` | 201 Created with generated id | 400 invalid input |
| GET | `/api/books` | 200 OK, JSON array | |
| GET | `/api/books/{id}` | 200 OK | 404 missing book |
| PUT | `/api/books/{id}` | 200 OK, updated book | 400 invalid input; 404 missing book |
| DELETE | `/api/books/{id}` | 204 No Content, empty body | 404 missing book |

`title` and `author` must contain non-whitespace text. `price`, when supplied, must be zero or positive. `isbn` and `price` are optional, as in the lab model. POST assigns an id regardless of any id supplied by the client. PUT updates all editable fields.

Example POST body:

```json
{
  "title": "234065N - Clean Code",
  "author": "Robert C. Martin",
  "isbn": "9780132350884",
  "price": 4500.00
}
```

## Reproduce the submission evidence

Import the supplied Postman collection from `postman/` (when included) and use `http://localhost:8080` as the base URL. Create at least three books, including the index-number title, before capturing S2. Capture S1 POST, S2 list, S3 get, S4 update, S5 delete, S6 GET the same deleted id, and S7 invalid POST with an empty title. Capture the entire Postman window. For S8, expand POST in Swagger UI and show the summary and documented 201/400 responses.

The submission PDF and captured evidence are packaged separately from the source. Any test logs supplied alongside the project describe the actual run rather than a guarantee that a different local environment is configured.

## Build and stop

```sh
./mvnw clean package
# Stop and remove the containers while retaining the named database volume.
docker compose down
```

The MySQL data volume persists between runs. Never use `docker compose down -v` for this lab. Use `SELECT * FROM library_db.book;` in a MySQL client to inspect stored books through host port `3307`.

## Reference documentation

- [Spring Boot system requirements](https://docs.spring.io/spring-boot/system-requirements.html)
- [springdoc OpenAPI](https://springdoc.org/)
- [Maven Wrapper](https://maven.apache.org/wrapper/)
