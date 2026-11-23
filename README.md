# Vikunja in Docker

A self-hosted Vikunja task manager running in Docker Compose, extended with n8n
as a separate service. n8n will talk to Vikunja over its REST API once the first
workflow is built.

This setup uses Vikunja's default database, SQLite.

## Requirements

Docker Desktop, or Docker Engine with the Compose plugin.

## How to run

```bash
mkdir db files
docker compose up -d
```

Then open http://localhost:3456/ and register an account. There is no default
user — the first account is created through the web interface.

To stop and remove the containers:

```bash
docker compose down
```

The data stays in `db/` and `files/` and survives this.

## Design decisions

**SQLite to start with.** It is Vikunja's default and needs no second service,
which keeps the first version down to a single container. Postgres is planned
once the setup is stable.

**A pinned version instead of `latest`.** `latest` is a moving pointer, not a
version — it refers to a different image over time, so anyone cloning this repo
later would get different software than I did. Pinning `2.8.0` is what makes the
setup reproducible.

**Two bind mounts.** Vikunja runs as a non-root user inside the container and is
not allowed to create these directories there, so `./db` and `./files` are
mounted in from the host. They also hold all the data — the SQLite database and
uploaded attachments — which is why it survives `docker compose down`, and why
both directories are gitignored instead of committed.