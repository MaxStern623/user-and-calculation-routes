# 📦 Project Setup

---

# Fast API Calculator

Small FastAPI project that provides user registration/login and calculation operations (addition, subtraction, multiplication, division). Includes tests (unit, integration, e2e) and CI to run tests and push Docker images.

## Features

- User registration: `POST /auth/register`
- User login: `POST /auth/login` (JSON) and `POST /auth/token` (form)
- Calculation BREAD endpoints:
   - `POST /calculations` (create)
   - `GET /calculations` (browse for current user)
   - `GET /calculations/{id}` (read)
   - `PUT /calculations/{id}` (update)
   - `DELETE /calculations/{id}` (delete)
- JWT access & refresh tokens
- PostgreSQL-backed persistence
- Tests: pytest (unit, integration, e2e)

## Quickstart (local)

Prerequisites:
- Python 3.10+
- Docker (for PostgreSQL, optional)
- Virtualenv recommended

1. Create and activate virtualenv

```bash
python -m venv .venv
source .venv/bin/activate
```

2. Install dependencies

```bash
pip install -r requirements.txt
```

3. Start a PostgreSQL instance (optional – tests will need it). Using Docker:

```bash
docker run -d --name fastapi_test_db -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=fastapi_db -p 5432:5432 postgres:17
```

4. Run the app locally (development)

```bash
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

Open OpenAPI docs:

- http://127.0.0.1:8000/docs
- http://127.0.0.1:8000/redoc

## Running tests

Run the entire test suite:

```bash
pytest
```

Run a single test file:

```bash
pytest tests/integration/test_calculation.py -q
```

The test suite starts a local uvicorn server for e2e tests and expects a Postgres DB available at `DATABASE_URL` (set via environment or in `app/core/config.py`).

## CI / Docker

- The repository contains a GitHub Actions workflow `workflows/test.yml` that:
   - Spins up PostgreSQL as a service for tests
   - Installs dependencies and runs tests
   - If security checks pass, builds and pushes an image to Docker Hub (tags use `${{ secrets.DOCKERHUB_USERNAME }}` and `${{ github.sha }}`)

To enable CI pushing, add these repository secrets:
- `DOCKERHUB_USERNAME` — Docker Hub username
- `DOCKERHUB_TOKEN` — Docker Hub access token (or password)

### Docker Hub image link

After CI pushes the image, it will be published under your Docker Hub account with the repository name `fastapi-calculator` by default. Replace the placeholders below with your Docker Hub username.

Docker image (example):

```
docker pull maxstern623/fastapi-calculator:latest
```

Add your Docker Hub link here once the image is pushed:

- Docker Hub repository: https://hub.docker.com/r/maxstern623/fastapi-calculator

### How to verify CI ran successfully

1. Push a commit to `main` or open a PR against `main`.
2. Go to the repository Actions tab on GitHub and open the latest workflow run.
3. Confirm the `test` job passed and then the `security` and `deploy` jobs completed successfully.
4. (Optional) In the `deploy` job logs, confirm the `docker/build-push-action` step displays the image tags and `push: true` output.

To create the screenshots required for submission:

- GitHub Actions workflow: open the run page and take a screenshot showing all jobs green/successful.
- Application in browser: run the app locally (`uvicorn app.main:app`) and open `/docs`, then perform register/login and create a calculation — take screenshots of the successful responses or the OpenAPI UI.


## Notes and troubleshooting

- The tests run uvicorn using the same Python interpreter as pytest to ensure all environment dependencies are available.
- If OpenAPI email validation fails on startup, ensure `email-validator` is installed (it's included in `requirements.txt`).

## Contact

If you need the repository pushed to another remote or want me to set up release tags, tell me where and I'll push the changes.
