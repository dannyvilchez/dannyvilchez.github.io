---
title: Setting up local development environment
date: "2025-07-15"
summary: "A quick breakdown of how I set up a local development environment with postgres, pgadmin, and a minimalist SQL migration script."
description: "A quick breakdown of how I set up a local development environment with postgres, pgadmin, and a minimalist SQL migration script."
toc: true
readTime: true
autonumber: false
math: false
tags: ["portfolio", "postgres", "SQL", "python"]
showTags: true
draft: false
---

Now that I’ve decided to build a portfolio project around my fitness and health data, it’s time to actually start building.

The first thing I need to focus on is setting a up a local development environment.

The setup consists of two parts:

1. Two docker containers runnning postgres and pgadmin on my homelab (which is running ubuntu server)
2. A simple python script that updates the database with the SQL files in a migration folder.

With this, I can iterate locally before publishing to the cloud.

_Side note: I could have used known migration tools but I want to keep this project lean and I wanted to understand in a simple way how those tools worked so I built my own minimalist script._

## Docker Containers

First, the `docker-compose.yml` that contains the Docker container definitions.

```yml
services:
  postgres:
    image: postgres:17
    container_name: fit-dw
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: ${PG_USER}
      POSTGRES_PASSWORD: ${PG_PASSWORD}
      POSTGRES_DB: ${PG_DB}
    volumes:
      - pgdata:/var/lib/postgresql/data

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_EMAIL}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_PASSWORD}
    ports:
      - "5050:80"
    depends_on:
      - postgres
    volumes:
      - pgadmin_data:/var/lib/pgadmin

volumes:
  pgdata:
  pgadmin_data:
```

I created an `.env` file in the project root directory to stores the admin credentials for both applications. Here is an example:

```
PG_HOST=127.0.0.1
PG_PORT=5432
PG_NAME=TEST
PG_USER=doug
PG_PASSWORD=password

PGADMIN_EMAIL=doug@example.com
PGADMIN_PASSWORD=password
```

### **Useful Commands**

These are quick ways to connect to the Postgres database during development:

**From the host (using psql):**
_Connect to the running database via TCP from your local machine._

```
psql -h localhost -U admin -d mydb
```

**From inside the Docker container:**
_Exec into the container and run psql directly._

```
docker exec -it postgres psql -U admin -d mydb
```

---

## SQL Migrations

If you’re unfamiliar with migrations, they’re a way to keep track of schema changes in a database. It allows you to store SQL changes in version control instead of applying them manually.

You may be asking, _"Why didn't you use a migration tool? Why roll your own?"_. Two reasons:

1. I wanted to keep the dependencies lean.
2. I wanted to learn how these tools work by building my own.

In the future, if I find that I need more features, I'll gladly bring **dbmate** or **alembic** into the project.

Until that moment presents itself, here's the script I created to publish the SQL migrations:

```python
import os
import psycopg
from psycopg import Connection
from dotenv import load_dotenv


ENV_PATH = os.path.abspath(".env")
MIGRATION_PATH = os.path.abspath("migrations/")


def load_environment_variables(env: str) -> None:
    if not os.path.exists(env):
        raise Exception(f"{env} does not exist")

    load_dotenv(env)


def connect_to_database() -> Connection:
    return psycopg.connect(
        host=os.getenv("PG_HOST"),
        port=os.getenv("PG_PORT"),
        dbname=os.getenv("PG_NAME"),
        user=os.getenv("PG_USER"),
        password=os.getenv("PG_PASSWORD"),
    )


def ensure_migrations_table_exists(conn: Connection) -> None:
    with conn.cursor() as cur:
        cur.execute("""
            CREATE TABLE IF NOT EXISTS schema_migrations (
                MigrationID BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
                Filename TEXT UNIQUE NOT NULL,
                AppliedDate TIMESTAMP DEFAULT CURRENT_TIMESTAMP
            );
        """)
    conn.commit()


def get_applied_migrations(conn: Connection) -> set[str]:
    with conn.cursor() as cur:
        cur.execute("SELECT filename FROM schema_migrations;")
        return {row[0] for row in cur.fetchall()}


def apply_migrations(conn: Connection, file_path: str) -> None:
    filename = os.path.splitext(os.path.basename(file_path))[0]
    with open(file_path, "r") as f:
        sql = f.read()
    with conn.cursor() as cur:
        cur.execute(sql)
        cur.execute(
            "INSERT INTO schema_migrations (Filename) VALUES (%s);",
            (filename,),
        )
        print(f"Applied {filename} to database")
    conn.commit()


def main():
    load_environment_variables(ENV_PATH)
    conn = connect_to_database()

    ensure_migrations_table_exists(conn)
    applied = get_applied_migrations(conn)
    file_list = sorted(os.listdir(MIGRATION_PATH))

    """
    This the main loop that applies the migrations to the database.
    I thought about abstracting this out to its own function but
    decided against it because there's no major complexity here that
    I want to develop or test separately. And there's no branching in it.
    """


	for file in file_list:
        if file.endswith(".sql") and file not in applied:
            apply_migrations(conn, os.path.join(MIGRATION_PATH, file))

    with conn.cursor() as cur:
        cur.execute("SELECT * FROM schema_migrations")
        rows = cur.fetchall()
        print(rows)



if __name__ == "__main__":
    main()
```

Here’s a quick rundown of what each function in the migration script does:

- **load_environment_variables(env: str)**: Loads environment variables from the .env file. Raises an error if the file doesn’t exist.
- **connect_to_database()**: Connects to the Postgres database using credentials from the environment file.
- **ensure_migrations_table_exists(conn)**: Creates the schema_migrations table if it doesn’t already exist. This table keeps track of which SQL files have been applied.
- **get_applied_migrations(conn)**: Retrieves a set of filenames that have already been executed and recorded in the schema_migrations table.
- **apply_migrations(conn, file_path)**: Reads and executes a given SQL file against the database, then logs its filename in schema_migrations.
- **main()** Loads environment variables, connects to the database, ensures the tracking table exists, checks which migrations have been applied, and applies any new ones. Finally, it prints all applied migrations for confirmation.

### Issues I ran into:

Initially, in the `.env` file I had `PG_HOST` environment variable set to `localhost`, and the Python script was unable to connect to the database. This is because the default connection for `psycopg` uses a Unix socket when the host is omitted or set to `localhost`.

Since the Postgres server is inside a Docker container, the Unix socket doesn’t exist on the host. The solution was to use TCP explicitly by setting the host to `127.0.0.1`.

## Next Steps

The next part of the project is focused on designing the raw data model and starting the ingestion process. There are _so_ many data points I can ingest into the database, so prioritizing is key here, or I'll spend the rest of the year doing backend work and never get to using the data.

Garmin holds most of my health metrics, along with my running, cycling, and swimming data. However, working with Garmin data (both manually and with code) will be tedious, so I'm starting with my weightlifting data.

It is split across two apps: RP Hypertrophy and FitNotes. FitNotes allows CSV export, so I'll have to copy the data from RP Hypertrophy into FitNotes manually. I only need to do this once to consolidate the historical data, and once it's in the database, I can figure out a way to automate future CSV ingestions.
