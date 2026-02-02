# BYKT

**Bayesian Kepler-Tregoe Cognitive ITSM**

An AI-powered IT Service Management platform with intelligent workflow orchestration, Kepner-Tregoe investigation methodology, and Bayesian reasoning.

## Quick Start

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) (Docker Desktop on Windows/Mac)
- [Ollama](https://ollama.ai/) with a local LLM:
  ```bash
  ollama pull qwen2.5:7b
  ```

### Installation

**Linux / Mac:**
```bash
curl -sSL https://raw.githubusercontent.com/dx111ge/bykt/main/install.sh | bash
cd bykt
docker compose -f docker-compose.prod.yml up -d
```

**Windows (PowerShell):**
```powershell
irm https://raw.githubusercontent.com/dx111ge/bykt/main/install.ps1 | iex
cd bykt
docker compose -f docker-compose.prod.yml up -d
```

**That's it!** The script downloads config files, generates secure secrets, and you're ready to go.

Open **http://127.0.0.1** and login:
- **Username:** `admin`
- **Password:** `admin`

> **Tip:** To set a custom password for test users, add `SEED_USER_PASSWORD=YourPassword` to `.env` before first start.

> **Note:** Use `127.0.0.1` instead of `localhost` to avoid IPv6 connection issues.

### Troubleshooting

**Verify Docker is working first:** Before troubleshooting BYKT issues, test that Docker can pull images:
```bash
docker pull hello-world
docker run hello-world
```
If this fails, the problem is with Docker/network configuration on your system, not BYKT.

**Image pull fails (EOF/network errors):** Retry the command, or pull images separately:
```bash
docker pull dx111ge/bykt-backend:latest
docker pull dx111ge/bykt-frontend:latest
```

**Ollama not connecting:**
- Ensure Ollama is running: `ollama serve`
- Linux users: Edit `.env` and set `OLLAMA_BASE_URL=http://YOUR_HOST_IP:11434`

### Stop BYKT

```bash
docker compose -f docker-compose.prod.yml down
```

### Reset Data

```bash
docker compose -f docker-compose.prod.yml down -v
```

## Networking

### Traefik Reverse Proxy

BYKT uses [Traefik](https://traefik.io/) as a reverse proxy, providing:
- **Single port access** - Everything through port 80
- **Automatic routing** - `/api/*` and `/socket.io/*` to backend, `/*` to frontend
- **WebSocket support** - Real-time updates work seamlessly
- **Dashboard** - Available at http://127.0.0.1:8080 for debugging

### Public Access with Cloudflare Tunnel

Want to share BYKT externally without opening firewall ports? Use Cloudflare Tunnel:

```bash
# Start with tunnel profile
docker compose -f docker-compose.prod.yml --profile tunnel up -d

# Get your public URL
docker logs bykt-cloudflared
```

Look for a URL like `https://random-words.trycloudflare.com` - that's your public HTTPS endpoint. No account required, no configuration needed.

**Note:** The tunnel is temporary and changes on restart. For permanent URLs, set up a [Cloudflare Zero Trust tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/).

## Architecture

```
                    User Interaction
                          |
                          v
+----------------------------------------------------------+
|                    CHAT MODULE                            |
|         Primary interface - voice-ready                   |
+----------------------------------------------------------+
                          |
                          v
+----------------------------------------------------------+
|                    ORCHESTRATOR                           |
|    Coordinates workflow, manages conversation state       |
+----------------------------------------------------------+
                          |
                          v
+----------------------------------------------------------+
|               WORKFLOW BLUEPRINT                          |
|     Data-driven tree structure (nodes, edges, decisions)  |
+----------------------------------------------------------+
                          |
                          v
+----------------------------------------------------------+
|               BAYESIAN NETWORK                            |
|    Probabilistic reasoning for complex diagnostics        |
+----------------------------------------------------------+
```

## Features

### Admin Section
- **System Setup** - Configuration and settings
- **Tools & Integrations** - External system connections
- **People Management** - Users, teams, roles
- **Runbooks** - Operational procedures
- **K-T Investigation** - Investigation template management
- **Service Catalog** - Service request templates and workflows

### Personal Section
- **Intake** - Ticket submission and initial triage
- **Service Requests** - End-user service request portal

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Frontend** | Next.js 15, React 19, TypeScript, Tailwind CSS, Zustand |
| **Backend** | Python 3.13, FastAPI, SQLAlchemy 2.0 (async), Socket.IO |
| **Database** | PostgreSQL 15 |
| **Cache** | Redis 7 |
| **AI/ML** | LangChain, pgmpy (Bayesian inference) |
| **Infrastructure** | Docker, Docker Compose, Traefik |

## Configuration

Edit `.env` to customize:

| Variable | Default | Description |
|----------|---------|-------------|
| `POSTGRES_PASSWORD` | bykt_dev_password | Database password |
| `SECRET_KEY` | (dev key) | App secret - **change in production!** |
| `JWT_SECRET_KEY` | (dev key) | JWT secret - **change in production!** |
| `OLLAMA_BASE_URL` | host.docker.internal:11434 | Ollama API endpoint |
| `OLLAMA_MODEL` | qwen2.5:7b | LLM model to use |

## Development

For local development with hot reload:

```bash
# Use development compose file (builds from source)
docker compose up -d

# View logs
docker compose logs -f backend
docker compose logs -f frontend
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for development guidelines.

## License

This project is licensed under the Business Source License 1.1 - see the [LICENSE](LICENSE) file for details.

**Free for production use** with up to 10 users. 