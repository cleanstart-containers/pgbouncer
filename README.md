# PgBouncer Container

## Overview

PgBouncer is a lightweight connection pooler for PostgreSQL databases. It reduces connection overhead by maintaining a pool of active connections that can be reused by client applications.

## Image Information

- **Image Name**: `ghcr.io/cleanstart-containers/pgbouncer:latest`
- **Base User**: `clnstrt` (non-root)
- **Entrypoint**: `/usr/bin/pgbouncer`
- **Default Port**: 6432

## What is pgbouncer.ini?

`pgbouncer.ini` is PgBouncer's main configuration file containing:
- **[databases]**: PostgreSQL database connection details
- **[pgbouncer]**: Pool settings, authentication, ports, timeouts, and logging

## Quick Start

### Pull Commands
```bash
docker pull ghcr.io/cleanstart-containers/pgbouncer:latest
docker pull ghcr.io/cleanstart-containers/pgbouncer:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name pgbouncer-test ghcr.io/cleanstart-containers/pgbouncer:latest-dev
```


See the [sample-project](./sample-project/) directory for a complete working example.

```bash
cd sample-project

# Create network

docker network create pgbouncer-net

# Start PostgreSQL

docker run -d --name postgres-db --network pgbouncer-net \
  -e POSTGRES_DB=testdb -e POSTGRES_USER=testuser -e POSTGRES_PASSWORD=testpass \
  postgres:16-alpine


# Start PgBouncer
# Create valid pgbouncer.ini config file, to test furthur

docker run -d --name pgbouncer --network pgbouncer-net -p 6432:6432 \
  -v $(pwd)/pgbouncer.ini:/etc/pgbouncer/pgbouncer-other.ini/pgbouncer.ini:ro \
  -v $(pwd)/userlist.txt:/etc/pgbouncer/userlist.txt/userlist.txt:ro \
  ghcr.io/cleanstart-containers/pgbouncer:latest /etc/pgbouncer/pgbouncer-other.ini/pgbouncer.ini

# Test connection

docker run --rm -it --network pgbouncer-net postgres:16-alpine \
  psql -h pgbouncer -p 6432 -U testuser -d testdb
```

## Pool Modes

- **session**: Connection released when client disconnects (default, safest)
- **transaction**: Connection released after each transaction
- **statement**: Connection released after each statement (highest performance)

## Use Cases

- Web applications with variable load
- Microservices limiting connections to PostgreSQL
- Reducing connection overhead without code changes
- High-availability setups

## Basic Usage

```bash
docker run -d \                                                                                           
  --name pgbouncer \
  -p 6432:6432 \
  -v $(pwd)/pgbouncer.ini:/etc/pgbouncer/pgbouncer-other.ini/pgbouncer.ini:ro \
  -v $(pwd)/userlist.txt:/etc/pgbouncer/userlist.txt/userlist.txt:ro \
  ghcr.io/cleanstart-containers/pgbouncer:latest /etc/pgbouncer/pgbouncer-other.ini/pgbouncer.ini
```

---

## Architecture Support

### Multi-Platform Images
```bash
docker pull --platform linux/amd64 ghcr.io/cleanstart-containers/pgbouncer:latest
```
```bash
docker pull --platform linux/arm64 ghcr.io/cleanstart-containers/pgbouncer:latest
```

---

## Resources

- **Official Documentation:** https://www.google.com/search?q=pgbouncer+documentation
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/pgbouncer
- **Docker Hub:** https://hub.docker.com/r/cleanstart/pgbouncer
- **CleanStart All Images:** https://images.cleanstart.com
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart
