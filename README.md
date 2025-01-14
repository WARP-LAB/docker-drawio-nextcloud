# draw.io self hosted for Nextcloud

Reasons for this:

- the official [docker-drawio](https://github.com/jgraph/docker-drawio) image
  - uses dev branch of [drawio](https://github.com/jgraph/drawio)
  - ~~is~~ was broken because of [upstream changes](https://github.com/jgraph/drawio/commit/2a6f49ab0fbe6e61ae6d0c2147e0e51961d4c843)
  - tries to do SSL termination (via self signed or letsencrypt)
- this
  - uses latest tagged release version of [drawio](https://github.com/jgraph/drawio/releases)
  - this is HTTP only and does not enforce signing, as SSL termination is left to proxy
  - is used within existing Nextcloud instance - web interface is mostly for sharing, and [draw.io desktop app](https://www.drawio.com/blog/diagrams-offline) is for actually working on locally synced files, i.e., no other file storage drivers needed (Google Drive, OneDrive)

## Install

- Create DNS record `mydrawioinstance.tld`
- Create proxy (extras dir has an example for nginx proxy pass w/ SSL termination)
- Get SSL cert
- Build

```sh
git clone # this repo
cp .env.example .env
vi .env # edit mydrawioinstance.tld and ports
docker compose build && \
docker compose up --detach
```

- For [Nextcloud draw.io app](https://apps.nextcloud.com/apps/drawio) settings set *Draw.io URL* to `https://mydrawioinstance.tld`
- Profit

## Variations

The main composer runs three services - [PlantUML Server](https://github.com/plantuml/plantuml-server), image exporting server (for PDF), and [draw.io itself](https://github.com/jgraph/drawio). Here are some other variations.

### draw.io only

```sh
docker compose --file ./docker-compose.drawio-base.yml build --no-cache && \
docker compose --file ./docker-compose.drawio-base.yml up
```

### draw.io & PDF export

```sh
docker compose --file ./docker-compose.image-export.yml build --no-cache && \
docker compose --file ./docker-compose.image-export.yml up
```
