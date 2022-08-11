# docker-python-web Docs

## Introduction

This is the documentation site for [`cal-itp/docker-python-web`](https://github.com/cal-itp/docker-python-web).

`docker-python-web` is a base [Docker image](https://www.docker.com/) for Cal-ITP Python web applications.

## Features

- Base image `python:3.10`
- Image configured with non-`root` user (`calitp` by default)
- `nginx` configured as a reverse proxy listening on container port `8000`
- `gunicorn` configured as a WSGI application server, communicates with `nginx` over Unix socket
- `gunicorn` default configuration location in `$GUNICORN_CONF`
- `WORKDIR` set to `/home/$USER/app`; `gunicorn` configuration in `/home/$USER/run`

## Non-Features

- Bring Your Own Web Framework
  - see [`benefits`](https://github.com/cal-itp/benefits) for a `Django` example
  - see [`eligibility-server`](https://github.com/cal-itp/eligibility-server) for a `Flask` example
- Bring Your Own `CMD`: drop in to `bash` with the default `ENTRYPOINT`.

## Usage

Reference one of the `image:tag` from GitHub Container Registry in a `Dockerfile`. E.g. for the `main` branch:

```dockerfile
FROM ghcr.io/cal-itp/docker-python-web:main

COPY my_app my_app

CMD "nginx && python -m gunicorn -c $GUNICORN_CONF my_app.wsgi"
```

Or from the command line:

```shell
docker pull ghcr.io/cal-itp/docker-python-web:main
```
