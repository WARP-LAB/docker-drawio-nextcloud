# draw.io self hosted for Nextcloud

Reasons for this:

- the official [docker-drawio](https://github.com/jgraph/docker-drawio) image is currently broken because of [upstream changes](https://github.com/jgraph/drawio/commit/2a6f49ab0fbe6e61ae6d0c2147e0e51961d4c843)
- official image tries to do SSL termination (via self signed or letsencrypt). naah, for that we have usual SSL termination proxy shtick, thus drawio should be HTTP only
- used within Nextcloud web interface instance (mostly for sharing), and via [draw.io desktop app](https://www.drawio.com/blog/diagrams-offline) for locally synced files, i.e., no other file storage drivers needed (Google Drive, OneDrive)

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
