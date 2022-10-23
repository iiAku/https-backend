# 🔐 Universal HTTPS Backend

![Traefik](https://img.shields.io/badge/Proxy-Traefik-skyblue)
![HTTPS](https://img.shields.io/badge/HTTPS-Configured-green)
![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-blue)
![Redis](https://img.shields.io/badge/Cache-Redis-red)
![Website](https://img.shields.io/badge/Website-React%20App-blue)

This repository is a minimalistic setup for running services with Docker Compose behind Traefik and autogenerated `https` certificates.

Let's say, you have the `API` service (e.g. NodeJS app), React app for frontend and would like to make it available via `https://api.your-domain.com` and `https://dashboard.your-domain.com`, then you are at the right place!

It includes the collection of `docker-compose` files and bash scripts that automate and simplify startup of your project, by taking care of some DevOps routine. List with available services can be found at [Services section](#services).

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

### Service creation

If you'd like to add your own service, then you should follow the steps below to keep the same structure:

1. Add service's Docker Compose file `services/docker-compose.YOUR_SERVICE.yml`.

2. `(optional)` Add service's folder `services/YOUR_SERVICE`. For example, if you are going to store some data or configuration files only related to that service.

3. `(optional)` Add service's script file `run/services/YOUR_SERVICE.sh`. For example, if you need to craete some folders in advance on `setup` step.

### Service deletion

If you don't need a service, you can easily delete it by running:

```bash
> bash run/sdel.sh SERVICE_NAME
```

For example, if you don't need `postgresql` service, you will run `bash run/sdel.sh postgresql` and it will delete related files.

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
- [React App](https://backend.starters.dev) - simple [frontend app](https://github.com/starters-dev/backend.starters.dev-website).

> more services will be added...

## Environment variables

Current setup requires you to fill in `.env` file with variables that are used in services.

#### General

- `DOMAIN_NAME` - your registered domain.
- `DO_AUTH_TOKEN` - Digital Ocean token that is going to be used for DNS challenge and generating https certificates. It's required by Traefik and they provide other options, you can find them [here](https://doc.traefik.io/traefik/https/acme/#providers). If you'd like to continue with Digital Ocean, then you can create a token in `Dashboard` -> `API` -> `Tokens/Keys`.
- `ACME_EMAIL` - email that is used for [Let's Encrypt](https://letsencrypt.org) and `https` certificates.
- `GITHUB_TOKEN` - `(optional)` github token for private repos.

<details>
<summary>PostgreSQL</summary>

- `POSTGRESQL_USERNAME` - username for PostgreSQL.
- `POSTGRESQL_PASSWORD` - password for PostgreSQL.
- `POSTGRESQL_DATABASE` - name of the database in PostgreSQL.

</details>

<details>
<summary>Redis</summary>

- `REDIS_PASSWORD` - password for Redis.
- `REDIS_DISABLE_COMMANDS` - commands disabled from execution.

</details>

<details>
<summary>Plausible</summary>

`.env` file:

- `PLAUSIBLE_POSTGRES_PASSWORD` - password for Plausible PostgreSQL.

`services/plausible/conf.env` file:

- `SECRET_KEY_BASE` - secret key of your app. Can be generated by running `openssl rand -base64 64 | tr -d '\n' ; echo`.
- `BASE_URL` - base url where this instance is accessible, including the scheme (eg. `http://` or `https://`), the domain name, and optionally a port.
- `ADMIN_USER_EMAIL` - admin email.
- `ADMIN_USER_NAME` - admin usenamename.
- `ADMIN_USER_PWD` - admin password.

> [Original docs](https://plausible.io/docs/self-hosting).

</details>

## Enhancements

There are still some things I would like to add to the backend setup:

- [x] [PostgreSQL](https://www.postgresql.org) - open source object-relational database known for reliability and data integrity.
- [x] [Redis](https://redis.io) - open source, in-memory data store used by millions of developers as a database, cache, streaming engine, and message broker.
- [ ] [Plausible](https://plausible.io) - simple and privacy-friendly Google Analytics alternative. [Github repo](https://github.com/plausible/hosting).
- [ ] [GlitchTip](https://glitchtip.com) - open source reimplementation of Sentry error tracking platform.
- [ ] [Mattermost](https://mattermost.com) - open source platform for developer collaboration. [Github repo](https://github.com/starters-dev/mattermost).
- [ ] [Taiga](https://www.taiga.io) - open source, self-hosted project management tool. [Github repo](https://github.com/starters-dev/taiga).
- [ ] [Focalboard](https://focalboard.com) - open source, self-hosted alternative to Trello, Notion, and Asana. [Github repo](https://github.com/starters-dev/focalboard).
- [ ] Github actions or similar technique

## Why?

While developing `API` and similar services for mobile apps, you can not really access `localhost` (on mobile device) if, let's say, you have running Docker image on you local machine. Also you have to make only `https` requests from mobile app.

Another reason was to have `PostgreSQL` and `Redis` always running in the cloud but for cheap cost. You can run both of them easily on the $6 server.

#### Note

This backend setup is a great fit if you just want to start writing business logic without messing with DevOps and spending a few days on that what's already has been done for you. It can be used for development and early production stages (of course, depends on your project), however, it's strongly **recommended** to have services like `PostgreSQL`, `Redis`, and similar to be seperated and independent on production stage.
