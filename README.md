# Caddy Zero Trust Auth Proxy

A lightweight Caddy-based authentication proxy that protects upstream services with Basic Auth. Designed to run behind a TLS-terminating edge (e.g., Railway, Cloudflare) and proxy to internal services.

## Use Case

Protect a private service (e.g., Data Logger Service) running on an internal network:

```
User (HTTPS) → Railway Edge (TLS) → Caddy (Basic Auth) → Data Logger (internal:5555)
```

The upstream service has no public URL. Caddy is the only entry point and requires authentication.

## Features

- Basic Authentication via environment variables
- Automatic password hashing at startup (plain password in, bcrypt hash generated)
- Lightweight Alpine-based image
- Works behind TLS-terminating proxies (Railway, Cloudflare, etc.)

## Configuration

| Environment Variable | Description                          | Example                               |
| -------------------- | ------------------------------------ | ------------------------------------- |
| `AUTH_USER`          | Basic auth username                  | `admin`                               |
| `AUTH_PASS`          | Plain text password (hashed at boot) | `mysecurepassword`                    |
| `UPSTREAM_URL`       | Backend service URL with port        | `http://flower.railway.internal:5555` |

## Usage

### Build

```bash
docker build -t caddy-zero-trust .
```

### Run

```bash
docker run -d \
  -e AUTH_USER=admin \
  -e AUTH_PASS=mysecurepassword \
  -e UPSTREAM_URL=http://your-backend:8080 \
  -p 80:80 \
  caddy-zero-trust
```

### Railway Deployment

1. Deploy this repo to Railway
2. Set environment variables: `AUTH_USER`, `AUTH_PASS`, `UPSTREAM_URL`
3. Add custom domain pointing to port 80
4. Caddy proxies authenticated requests to your internal service

## Files

- `Dockerfile` - Builds image with Python/bcrypt for password hashing
- `Caddyfile` - Caddy config with Basic Auth and reverse proxy
- `entrypoint.sh` - Hashes password at startup, then runs Caddy
- `site/` - Static files (optional)

## License

See [LICENSE](LICENSE) for details.

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/jPo5no?referralCode=5oF91f&utm_medium=integration&utm_source=template&utm_campaign=generic)
