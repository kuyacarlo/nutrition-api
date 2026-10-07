# Nutrition API

Stable FastAPI microservice for nutritional food records, user management, JWT authentication, and generated OpenAPI docs. This repository is treated as a stable/showcase API, not an actively evolving product.

## What is included

- FastAPI app in `main.py` with routers under `server/`
- SQLite-by-default persistence through SQLModel
- Alembic migration wiring
- Dockerfile and `docker-compose.yml` for local container runs
- API smoke/integration tests in `test/test_unittest.py`

## Configuration

| Variable | Default | Purpose |
| --- | --- | --- |
| `SECRET_KEY` | unset | Required for JWT signing. Do not use the example value outside local tests. |
| `ALGORITHM` | unset | JWT algorithm, normally `HS256`. |
| `DATABASE_URL` | `sqlite:///database.db` | Runtime SQLAlchemy/SQLModel database URL. |
| `ALEMBIC_DB_URL` | `sqlite:///./database.db` | Alembic migration database URL. |
| `baseUrl` | `http://127.0.0.1:8000/` | Test target URL used by `test/test_unittest.py`. |

## Run locally

```sh
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export SECRET_KEY=supersecretkey ALGORITHM=HS256
uvicorn main:app --host 127.0.0.1 --port 8000
```

Open `http://127.0.0.1:8000/docs` for Swagger UI.

## Run with Docker

```sh
docker build -t nutrition-api:local .
docker run --rm \
  -p 8000:8000 \
  -e SECRET_KEY=supersecretkey \
  -e ALGORITHM=HS256 \
  -e DATABASE_URL=sqlite:///database.db \
  -e ALEMBIC_DB_URL=sqlite:///database.db \
  nutrition-api:local
```

Or use Compose for the published image:

```sh
docker compose up -d
```

## Safe verification

The test suite expects a running local API. Start the server in one terminal, then run:

```sh
SECRET_KEY=supersecretkey ALGORITHM=HS256 baseUrl=http://127.0.0.1:8000 python test/test_unittest.py
```

A lower-blast-radius smoke check that does not keep a service running is:

```sh
SECRET_KEY=supersecretkey ALGORITHM=HS256 python -m compileall main.py server test
```

## API notes

- `GET /api/ping` returns `"pong"`.
- Food, user, and auth routes are mounted under `/api`.
- The `/api/migrate` admin route runs Alembic migrations against `ALEMBIC_DB_URL`; use it only in a controlled local environment.

## License

[MIT](LICENSE)
