# banking-portal-dev-config

My local Docker Compose setup for running the IRT banking portal on my own
machine, so I don't have to wait for a slot on the shared staging box.

The portal image itself lives on Docker Hub (I push it from my laptop after a
`next build`) — this repo is only the orchestration bits: compose file, nginx
front end, and the env values the containers need.

## Usage

```bash
cp .env.example .env     # then fill in the values
docker compose pull
docker compose up -d
```

Portal comes up on <https://localhost> (self-signed cert, just accept the
warning). The `db` service seeds itself on first boot from the volume.

## Notes to self

* The `/api/updates` endpoint checks an HMAC over the request body, so the
  signing key has to match the one the real staging box uses or every call
  comes back 403. Key goes in `.env` as `INTERNAL_API_SECRET`.
* Don't bother with the public marketing page for testing — go straight to
  `/remote-login` and sign in, the console is behind the session cookie.
* Rebuild + push after frontend changes:
  ```bash
  docker build -f frontend/Dockerfile.dev -t abob120893/bellwood-portal:dev frontend/
  docker push abob120893/bellwood-portal:dev
  ```

## TODO

* Move this image out of my personal Docker Hub namespace into the org one
  once platform gives me a robot account — right now the push still uses my
  own login and I don't want that in here.
* Ask about getting added to the `irt-financial` GitHub org so this can live
  next to `core-banking-portal` instead of out here on my own account.
