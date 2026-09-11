---
weight: 3
bookFlatSection: true
title: "Developers"
date: "2023-02-05"
lastmod: "2026-09-10"
---

# Developers Guide

## Local installation

Mwmbl uses Python, PostgreSQL, Redis, and a Vite-based frontend.

### 1. Install Python

Mwmbl currently requires Python 3.11.

Check your version:

```sh
python3 --version
```

### 2. Install the Python dependencies

Mwmbl uses [uv](https://docs.astral.sh/uv/) to manage its Python environment and dependencies.

From the root of the repository, run:

```sh
make install
```

This creates `.venv` and installs the project's dependencies.

You can activate the environment with:

```sh
source .venv/bin/activate
```

Or use `uv run` without activating it.

### 3. Set up PostgreSQL

Mwmbl needs PostgreSQL for development and testing.

Create the development database:

```sh
createdb mwmbl
```

The test suite creates its own test database automatically, so you do not need to create `test_mwmbl` yourself.

The PostgreSQL user used by Mwmbl must have permission to create databases. For example:

```sql
ALTER ROLE yourusername CREATEDB;
```

If PostgreSQL is running in Docker while Mwmbl is running directly on your host, publish PostgreSQL's port and use `127.0.0.1` as the database host. A Docker container name such as `dev-postgres` is only resolvable from other containers on the same Docker network.

### 4. Set up Redis

Mwmbl uses Redis for caching and background-task coordination.

Make sure Redis is available at `127.0.0.1:6379`

For example, with Docker:

```sh
docker run --name mwmbl-redis -p 6379:6379 -d redis
```

Some tests replace Redis with fake or monkey-patched clients, but Redis is still part of the normal development environment.

### 5. Configure the environment

For development, set:

```sh
export DJANGO_SETTINGS_MODULE=mwmbl.settings_dev
export DATABASE_URL="postgresql://yourusername:yourpassword@127.0.0.1:5432/mwmbl"
export REDIS_URL="redis://127.0.0.1:6379"
```

You can put these exports in your shell configuration if you want them to persist.

The test suite uses `mwmbl.settings_test` through the pytest configuration, so you do not need to change `DJANGO_SETTINGS_MODULE` when running tests.

### 6. Run the migrations

From the repository root:

```sh
make migrate
```

### 7. Build the frontend

Install [Node.js](https://nodejs.org/) and npm.

Then:

```sh
cd front-end
npm install
npm run build
```

### 8. Run the tests

Run the full test suite with:

```sh
make test
```

Django/pytest will create the test database automatically.

For a quicker development loop, run an individual test file:

```sh
make test-file FILE=test/test_voting_api.py
```

### 9. Run Mwmbl

Start the development server with:

```sh
make run
```

To run the background task processor in a second terminal, use `make run-background`.

Then visit `http://localhost:8000/`. For example: `http://localhost:8000/search?s=Newton`


## Useful Make targets

The Makefile provides shortcuts for common development tasks:

```sh
make install
make migrate
make test
make run
```

Run:

```sh
make help
```

to see the available targets.
