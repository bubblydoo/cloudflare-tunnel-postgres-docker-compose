# Cloudflare Tunnel Postgres Docker Compose

Running a Cloudflare Tunnel enables you to use your Postgres database in a Cloudflare Worker.

You can then use [@bubblydoo/cloudflare-workers-postgres-client](https://github.com/bubblydoo/cloudflare-workers-postgres-client) to access the database from a Cloudflare Worker.

Based on [worker-template-postgres](https://github.com/cloudflare/worker-template-postgres/blob/master/scripts/postgres/docker-compose.yml).

Includes `cloudflared`, but also `pgbouncer`, a connection pooler.

This config was tested on AWS, with an Amazon Linux 2 instance, but it should work on any Linux server. Note that it only works on x86/amd64/Intel processors for now, because both `pgbouncer` and `cloudflared` only offer `amd64` images.

## Steps

To install a Postgres tunnel:

- run `mkdir ~/.cloudflared && chown 65532:65532 -R ~/.cloudflared` (the `cloudflared` docker uses user 65532)
- run `docker run -v ~/.cloudflared:/home/nonroot/.cloudflared cloudflare/cloudflared:2022.6.2 login` on the server
- copy `docker-compose.yml` to a server (e.g. with `git clone https://github.com/bubblydoo/cloudflare-tunnel-postgres-docker-compose`)
- next to the `docker-compose.yml` file, create an `.env` file with:
  - DB_NAME (e.g. `db`)
  - DB_HOSTNAME (e.g. `123.rds.amazonaws.net`)
  - DB_USER (e.g. `postgres`)
  - DB_PASSWORD (e.g. `keyboardcat`)
  - TUNNEL_NAME (e.g. `dev`)
  - TUNNEL_HOSTNAME (e.g. `db-tunnel.example.net`)
- run `docker-compose up -d`
- check `docker-compose logs -f` to see if everything was configured correctly

If you want to delete the tunnel, you can run `docker run -v ~/.cloudflared:/etc/cloudflared cloudflare/cloudflared:2022.6.2 tunnel delete $TUNNEL_NAME`