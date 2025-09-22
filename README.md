# Vaultwarden + Graylog (Docker Compose)

## Quick start

1. Create `.env` and set required variables (see below), for local dev you can use:

```bash
cat > .env <<'EOF'
GRAYLOG_PASSWORD_SECRET=<long-random-string>
GRAYLOG_ROOT_PASSWORD_SHA2=<sha256-of-admin-password>
GRAYLOG_HTTP_EXTERNAL_URI=http://127.0.0.1:9000/

VAULTWARDEN_ADMIN_TOKEN=<long-random-token>
VAULTWARDEN_DOMAIN=http://127.0.0.1:8080
VAULTWARDEN_SIGNUPS_ALLOWED=false
VAULTWARDEN_LOG_LEVEL=info
EOF
```

2. Launch the stack:

```bash
docker compose up -d
```

3. Access:
- Graylog UI: http://127.0.0.1:9000
- Vaultwarden: http://127.0.0.1:8080

If Graylog starts in first-time setup mode, the temporary password is printed in `docker logs graylog`. Log in as `admin` with that password and complete setup.

## Required environment variables

- GRAYLOG_PASSWORD_SECRET: long random string (>= 16 chars)
- GRAYLOG_ROOT_PASSWORD_SHA2: SHA256 of Graylog admin password
  - Generate on macOS:
    ```bash
    echo -n 'yourpassword' | shasum -a 256 | awk '{print $1}'
    ```
- GRAYLOG_HTTP_EXTERNAL_URI: public URL for Graylog UI, e.g. http://127.0.0.1:9000/

- VAULTWARDEN_ADMIN_TOKEN: long random token for /admin
  - Generate:
    ```bash
    openssl rand -base64 48
    ```
- VAULTWARDEN_DOMAIN: external URL for Vaultwarden, e.g. http://127.0.0.1:8080
- VAULTWARDEN_SIGNUPS_ALLOWED: true|false
- VAULTWARDEN_LOG_LEVEL: info|warn|error|trace

## Logs to Graylog

Vaultwarden uses the Docker GELF driver to send logs to Graylog (GELF UDP 12201). The compose is configured to send to `udp://127.0.0.1:12201` which maps to the Graylog container port published on the host. In Graylog, create/ensure a GELF UDP input on port 12201.

## Notes

- Graylog 5 requires OpenSearch. This stack runs OpenSearch 2 in single-node mode with security disabled.
- For production, place behind a reverse proxy (Traefik/Nginx), set HTTPS URLs in env, and configure persistent backups for volumes.
