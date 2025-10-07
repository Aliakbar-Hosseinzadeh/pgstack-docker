# 🐘 **[pgstack-docker](https://medium.com/@aliakbarhosseinzadeh/how-i-built-a-clean-and-safe-postgresql-pgadmin-stack-with-docker-compose-c038207f24b4)**

A clean, secure, and environment-aware **PostgreSQL + pgAdmin** stack using **Docker Compose**, ready for development, testing, and production.
It includes strong secrets management, health checks, and environment-specific overrides that make it simple to run safely in all stages.

---

### 📁 **Project Structure**

```
pgstack-docker/
│
├── docker-compose.yml            # Base configuration (shared across all stages)
├── docker-compose.dev.yml        # Development override (pgAdmin + ports)
├── docker-compose.test.yml       # Test override (ephemeral, CI-friendly)
├── docker-compose.prod.yml       # Production override (hardened, no ports)
│
├── .env                          # Default environment variables (local/dev)
├── .env.prod                     # Environment variables for production
│
├── secrets/
│   ├── postgres_password.txt     # Postgres password (Docker secret)
│   └── pgadmin_password.txt      # pgAdmin password (Docker secret)
│
└── README.md                     # You're reading it 🙂
```

---

### ⚙️ **Services**

| Service     | Description                                                                        |
| ----------- | ---------------------------------------------------------------------------------- |
| **db**      | PostgreSQL 18 container with persistent data volume (`db_data`) and health checks. |
| **pgadmin** | pgAdmin 4 (web UI for PostgreSQL), enabled only in development mode.               |

---

### 🧩 **Compose Profiles**

| Profile  | Description                                         | pgAdmin | Ports                  | Data Persistence   | Security |
| -------- | --------------------------------------------------- | ------- | ---------------------- | ------------------ | -------- |
| **dev**  | Full development stack for local use.               | ✅       | `5432:5432`, `8080:80` | Persistent volumes | Medium   |
| **test** | Ephemeral test environment (tmpfs DB, random port). | ❌       | Random                 | In-memory          | Light    |
| **prod** | Hardened production environment.                    | ❌       | None                   | Persistent volumes | Strong   |

---

### 🚀 **Quick Start**

#### 🧱 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/pgstack-docker.git
cd pgstack-docker
```

#### 🔐 2. Create Secrets

```bash
mkdir -p secrets
echo "strong-db-password" > secrets/postgres_password.txt
echo "strong-pgadmin-password" > secrets/pgadmin_password.txt
```

#### ⚙️ 3. Adjust Environment Files

Edit `.env` for local/dev and `.env.prod` for production.

---

## 🧪 **Run Environments**

### 🧑‍💻 Development

Runs PostgreSQL and pgAdmin locally with exposed ports.

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml --profile dev up -d
```

Access:

* **pgAdmin:** [http://localhost:8080](http://localhost:8080)
* **PostgreSQL:** `localhost:5432`
  Credentials from `.env` and `secrets/postgres_password.txt`.

Stop and clean:

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml --profile dev down
```

---

### 🧪 Test

Ephemeral PostgreSQL instance using **tmpfs** (fast, no data persistence).
Great for CI/CD or integration testing.

```bash
docker compose -f docker-compose.yml -f docker-compose.test.yml up -d db
```

Discover the random test port:

```bash
docker compose -f docker-compose.yml -f docker-compose.test.yml port db 5432
```

Clean up:

```bash
docker compose -f docker-compose.yml -f docker-compose.test.yml down -v
```

---

### 🏗️ Production

Hardened configuration — no pgAdmin, no published ports, read-only file system, and resource limits.

```bash
docker compose --env-file .env.prod \
  -f docker-compose.yml -f docker-compose.prod.yml \
  --profile prod up -d
```

Validate before running:

```bash
docker compose --env-file .env.prod \
  -f docker-compose.yml -f docker-compose.prod.yml \
  --profile prod config
```

Stop and remove:

```bash
docker compose --env-file .env.prod \
  -f docker-compose.yml -f docker-compose.prod.yml \
  --profile prod down
```

---

### 🩺 **Health Checks**

| Service      | Health Check Command                              |
| ------------ | ------------------------------------------------- |
| **Postgres** | `pg_isready -U $POSTGRES_USER -d $POSTGRES_DB`    |
| **pgAdmin**  | `wget -qO- http://127.0.0.1/misc/ping >/dev/null` |

All services will wait until dependencies are healthy before startup.

---

### 🧹 **Cleaning Up Everything**

```bash
docker compose down -v --remove-orphans
```

---

### 🧰 **Tips**

* Use `docker compose config` to preview the final merged configuration.
* For Swarm or resource enforcement, add `--compatibility` when deploying.
* Keep `PGADMIN_CONFIG_` values as **Python literals** (`'True'`, `10`, or `'string'`).
* Never expose `5432` or pgAdmin in production.

---

### 📄 **License**

MIT License © 2025

#
Better is better than best.
