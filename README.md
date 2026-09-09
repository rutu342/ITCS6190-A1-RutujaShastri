# Assignment #1: Containers with Docker

**Name:** Rutuja Hemant Shastri  
**Student ID:** 801484366  
**Email:** rshastri@charlotte.edu

This project implements a two-container application using Docker and Docker Compose. One container runs a PostgreSQL database that is automatically initialized with the trips table and seed data from `db/init.sql`. The second container runs a Python application that connects to the database, executes SQL queries, computes basic statistics, prints a JSON summary, and writes the results to `out/summary.json`.

## What is provided, and what you write

The assignment provides the Dockerfiles, database initialization script, Makefile, and project structure. I completed the missing parts of the Python application and Docker Compose configuration.

The following parts were completed:

- `app/main.py` — reads the database credentials from environment variables, counts the total trips, calculates the average fare by city, finds the top trips by duration, and builds the final summary.
- `compose.yml` — provides the database connection environment variables to the application container.

The PostgreSQL database is initialized automatically from `db/init.sql`.

## Repository layout

```text
.
├─ app/           main.py, Dockerfile
├─ db/            init.sql, Dockerfile
├─ out/           summary.json (created at run time)
├─ compose.yml
├─ Makefile
├─ .gitignore
└─ README.md

How to run

The complete application can be built and started with one command:

make

The make command cleans the previous containers and volumes, recreates the out/ directory, builds the Docker images, and starts both services.

To stop the containers and remove the volumes:

make down

The application can also be started directly with Docker Compose:

docker compose up --build

The database service starts first and is checked using a healthcheck. Once PostgreSQL is healthy, the Python application connects to the database and runs the required queries.

Example output

The following is the actual output produced when I ran make:

{
  "total_trips": 6,
  "avg_fare_by_city": [
    {
      "city": "Charlotte",
      "avg_fare": 16.25
    },
    {
      "city": "New York",
      "avg_fare": 19.0
    },
    {
      "city": "San Francisco",
      "avg_fare": 20.25
    }
  ],
  "top_by_minutes": [
    {
      "city": "San Francisco",
      "minutes": 28,
      "fare": 29.3
    },
    {
      "city": "New York",
      "minutes": 26,
      "fare": 27.1
    },
    {
      "city": "Charlotte",
      "minutes": 21,
      "fare": 20.0
    },
    {
      "city": "Charlotte",
      "minutes": 12,
      "fare": 12.5
    },
    {
      "city": "San Francisco",
      "minutes": 11,
      "fare": 11.2
    },
    {
      "city": "New York",
      "minutes": 9,
      "fare": 10.9
    }
  ]
}

The application completed successfully with exit code 0.

Where outputs are written

The application writes the generated JSON summary to:

out/summary.json

The out/ directory on the host is bind-mounted to /out inside the application container.

The generated output is intentionally excluded from Git using .gitignore.

# Troubleshooting

The app exits before the database is ready. The app service depends on the database healthcheck, so the application starts after PostgreSQL is healthy. The Python application also retries the database connection before reporting a failure.
Database connection problems. Check that the database credentials in compose.yml match the PostgreSQL settings. The application uses db as the database hostname because db is the Compose service name.
Permission errors on out/. On Linux, a bind-mounted directory may sometimes be owned by root. If this happens, sudo chown -R $USER out can be used to fix the permissions.
Stale database data. The initialization script runs when the PostgreSQL database is initialized for the first time. To remove the existing database volume and initialize the database again, run:
make down
make

#Notes on credentials

The database username and password used in this assignment are throwaway values intended only for local development. They are provided directly in compose.yml as allowed by the assignment.

In a real application, credentials should not be committed to the repository. They should be stored securely, such as in a .env file or a secrets manager. The .env file is included in .gitignore.


