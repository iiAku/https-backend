# 🔐 Universal HTTPS Backend

![Traefik](https://img.shields.io/badge/Proxy-Traefik-skyblue)
![HTTPS](https://img.shields.io/badge/HTTPS-Configured-green)
![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-blue)
![Redis](https://img.shields.io/badge/Cache-Redis-red)
![Website](https://img.shields.io/badge/Website-React%20App-blue)

This repository is a minimalistic setup for running your services with Docker Compose behind Traefik with autogenerated `https` certificates.

Let's say, you have the `API` service (e.g. NodeJS app), React app for frontend and would like to make it available via `https://api.your-domain.com` and `https://dashboard.your-domain.com`, then you are at the right place!

> _HTTPS Backend_ is a part of [starters.dev](https://github.com/starters-dev) collection.

## Getting Started

### Quickstart

1. Connect to your remote server

```bash
> ssh root@IP_ADDRESS
```

> Hint: you can use a cloud computing providers such as [DigitalOcean](https://digitalocean.com).

2. Clone this repo

```bash
> git clone https://github.com/starters-dev/https-backend backend
> cd backend
```

3. Run `setup` script and fill in `env` variables

```bash
> bash setup.sh
> nano .env
```

Check [Environment variables](#environment-variables) section for more information.

4. Get a service repo

> Our example is based on [backend.starters.dev-website](https://github.com/starters-dev/backend.starters.dev-website) repo.

```bash
> git clone https://github.com/starters-dev/backend.starters.dev-website services/frontend
```

5. Build and run

```bash
> bash build.sh
```

It will setup everything, including https certificates, and will start Docker Compose services in the background.

## Structure

The folder structure is pretty simple but lets you reuse and write less boilerplate code.

- `docker-compose.main.yml` - main Docker Compose file with DNS challence, ACME and ports configuration.
- `build.sh` - `build` script that runs `setup` script with all scripts under `run/services` folder and builds and runs Docker images in background.
- `.env` - list of environment variables.
- `run/` - folder with general bash scripts.
- `run/services/` - folder with bash scripts for a specific service.
- `services/` - folder with Docker services files. For example, if you have `redis` service, then you would have `services/docker-compose.redis.yml` with specific configuration for `redis` service. Or if your service is a frontend app, then you'll need to clone it to `services/frontend/` and create `services/docker-compose.frontend.yml`.
- `services/your-service/` - folder with your service, for ex. `frontend`.

## What's inside

The setup uses [Traefik](https://github.com/traefik/traefik), the cloud native application proxy, to simplify the process of getting `https` certificates and the way to describe services.

You can always delete existing (if you don't need them) or add your own services, just make sure to apply necessary changes.

### Services

- [PostgreSQL](https://www.postgresql.org) - open source object-relational database known for reliability and data integrity. It uses [Bitnami Docker image](https://hub.docker.com/r/bitnami/postgresql).
- [Redis](https://redis.io) - open source, in-memory data store used by millions of developers as a database, cache, streaming engine, and message broker. It uses [Bitnami Docker image](https://hub.docker.com/r/bitnami/redis).
- [React App](https://backend.starters.dev) - sample frontend app. It uses [backend.starters.dev-website](https://github.com/starters-dev/backend.starters.dev-website) repo

> more services will be added...

## Environment variables

Current setup requires you to fill in `.env` file with variables that are used in services.

```
# [GENERAL]
DOMAIN_NAME=your-website.com
DO_AUTH_TOKEN=xxxxxxxxxxxxxxxxxxxxxx # for DNS challenge
ACME_EMAIL=email@your-website.com    # for let's encrypt
GITHUB_TOKEN=xxxxxxxxxxxxxxxxxxxxxx  # (optional) for private repos

# [POSTGRES]
POSTGRESQL_USERNAME=admin
POSTGRESQL_PASSWORD=123456qwerty
POSTGRESQL_DATABASE=db

# [REDIS]
REDIS_PASSWORD=
REDIS_DISABLE_COMMANDS=FLUSHDB,FLUSHALL
```

#### General

- `DOMAIN_NAME` - your registered domain.
- `DO_AUTH_TOKEN` - Digital Ocean token that is going to be used for DNS challenge and generating https certificates. It's required by Traefik and they provide other options, you can find them [here](https://doc.traefik.io/traefik/https/acme/#providers). If you'd like to continue with Digital Ocean, then you can create a token in `Dashboard` -> `API` -> `Tokens/Keys`.
- `ACME_EMAIL` - email that is used for [Let's Encrypt](https://letsencrypt.org) and `https` certificates.
- `GITHUB_TOKEN` - github token for private repos.

#### PostreSQL

- `POSTGRESQL_USERNAME` - username for PostgreSQL.
- `POSTGRESQL_PASSWORD` - password for PostgreSQL.
- `POSTGRESQL_DATABASE` - name of the database in PostgreSQL.

#### Redis

- `REDIS_PASSWORD` - password for Redis.
- `REDIS_DISABLE_COMMANDS` - commands disabled from execution.

## Why?

While developing `API` and similar services for mobile apps, you can not really access `localhost` (on mobile device) if, let's say, you have running Docker image on you local machine. Also you have to make only `https` requests from mobile app.

Another reason was to have `PostgreSQL` and `Redis` always running in the cloud but for cheap cost. You can run both of them easily on less than $5 server.

#### Note

This backend setup is a great fit if you just want to start writing business logic without messing with DevOps and spending a few days on that what's already has been done for you. It can be used for development and early production stages (of course, depends on your project), however, it's strongly **recommended** to have services like `PostgreSQL`, `Redis`, and similar to be seperated and independent on production stage.
