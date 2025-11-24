# Reflection — User & Calculation Routes Assignment

## Summary
This project implements user registration and authentication, plus BREAD operations for calculations (addition, subtraction, multiplication, division). It includes unit, integration, and end-to-end tests. CI runs tests and builds/pushes a Docker image to Docker Hub.

## What I implemented
- User registration (`POST /auth/register`) with password confirmation and Pydantic validation.
- User login (`POST /auth/login`) with password verification and JWT token issuance.
- Calculation creation (`POST /calculations`) that uses a factory to create specific operation types and persists results.
- Calculation Browse, Read, Update, Delete endpoints restricted to the authenticated user.
- Pydantic schemas for request validation (`CalculationBase`, `CalculationCreate`, `CalculationUpdate`, `UserCreate`, etc.).
- Integration tests that exercise the full request lifecycle including e2e tests that start a test server and use requests to call endpoints.

## Key experiences & challenges
- Environment parity for tests: The e2e tests launch `uvicorn` as a subprocess. Initially the system `uvicorn` binary lacked some Python packages available in the test virtual environment (for example `email-validator` required by Pydantic), causing app startup failures during tests. I solved this by launching uvicorn via the same Python interpreter used for pytest (`sys.executable -m uvicorn`) inside `tests/conftest.py`.

- Database availability: Tests require Postgres. Locally I started a Postgres container to run the tests. In CI, the workflow uses a Postgres service to provide the DB.

- Token handling: JWT creation/verification and optional token revocation (Redis) required careful separation between token type secrets (access vs refresh) and token payload validation. The repository includes helper functions in `app/auth/jwt.py` and a Redis stub for blacklisting tokens.

- CI image tagging and push: I updated GitHub Actions workflow to tag images with the Docker Hub username from secrets and to push images after tests and security scanning.

## How to reproduce locally
1. Create and activate a virtualenv
2. pip install -r requirements.txt
3. Start Postgres (Docker recommended)
4. Run tests: `pytest`
5. Run the app: `uvicorn app.main:app --reload --host 127.0.0.1 --port 8000`

## What I would improve next
- Implement refresh token endpoints and a proper logout flow (revocation via Redis).
- Add more CI safeguards: multi-stage releases, image signing, and more robust caching.
- Add monitoring for the deployed container and health-check endpoints beyond `/health`.

## Notes for graders
- The repository's GitHub Actions workflow will push Docker images only if you set `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` in repository secrets. After that, successful runs will create images at `hub.docker.com/r/<DOCKERHUB_USERNAME>/fastapi-calculator`.

