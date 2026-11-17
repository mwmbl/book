---
weight: 3
bookFlatSection: true
title: "Join the Community"
date: "2025-06-17"
lastmod: "2025-06-17"
---

# Mwmbl is a Community First

The community is the most important part of Mwmbl, since everything we
do depends on you. We aim to be one of the most friendly, diverse and
welcoming communities on the internet.

# Socials

The main place we hang out is on [our Matrix Space](https://matrix.to/#/#mwmbl:matrix.org)
but we also have a [Discord server](https://discord.gg/SJF689E3Rv),
and a [YouTube channel](https://www.youtube.com/@mwmbl), along with a
bunch of unmaintained socials...

If you like Mwmbl, please get in touch and tell us! Knowing that
people care about Mwmbl is the biggest motivation to keep going :)

# How to help

There are many ways to help:
 - Crawling the web
 - [Curating content](https://book.mwmbl.org/page/curating/)
 - Contributing code
 - Writing documentation
 - [Writing blog posts](https://book.mwmbl.org/page/blog-posts/)
 - Supporting the community
 - Helping form official organisations

If you'd like to help in any way, just get in touch and let us know.

# Running the Mwmbl Crawler with Docker Compose

You can easily run the Mwmbl crawler on your own server using Docker Compose. This setup includes both the crawler and a Redis instance for task coordination and state management.


## API Key

In order to run the crawler you need an API key. You can create one
yourself:

1. Go to [mwmbl.org](https://mwmbl.org) and log in (or register a
   new account).
2. Click **Account** in the navigation.
3. Under **API Keys**, click **Create API key**.
4. Copy the key immediately — it is only shown once and cannot be
   retrieved later.

Note that each page you crawl is recorded against your account. This
helps us identify and address any fraudulent or harmful crawling
activity.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- A Mwmbl API key (create one via [Account → Create API key](https://mwmbl.org/account/))

---

## Quick Start

1. **Clone the repository** (if you haven't already):

   ```bash
   git clone https://github.com/mwmbl/mwmbl.git
   cd mwmbl
   ```

2. **Set your API key**

   You can either export your API key in your shell:

   ```bash
   export MWMBL_API_KEY=your-api-key-here
   ```

   Or create a `.env` file in the root of the repository:

   ```env
   MWMBL_API_KEY=your-api-key-here
   ```

3. **Start the crawler**

   ```bash
   docker-compose up -d
   ```

   This will:
   - Build the crawler using `Dockerfile.crawler`
   - Start the crawler and a Redis instance
   - Persist crawler data in a volume (`~/.mwmbl` inside the container)
   - Persist Redis data in a separate volume

---

## Configuration Options

You can configure the crawler via environment variables in the `docker-compose.yml` file or `.env` file:

- `MWMBL_API_KEY` – **Required**: Your API key.
- `MWMBL_CONTACT_INFO` – **Required**: An email address or URL site owners can use to reach you.
- `CRAWLER_WORKERS` – Number of crawl processes to run (default: 2). Note that the total number of processes is `CRAWLER_WORKERS + 1`, because the main process spawns the others.
- `CRAWL_THREADS` – Number of threads per crawl process, each fetching and parsing one page at a time (default: 20). Total concurrent fetches are `(CRAWLER_WORKERS × CRAWL_THREADS) + 1`; allow roughly 20MB of RAM for each.
- `CRAWL_DELAY_SECONDS` – Delay each thread waits between the URLs it crawls (default: 0).
- `REDIS_URL` – Redis connection string (default: `redis://redis:6379`).

---

## Optimizing on First Run

The best `CRAWLER_WORKERS` and `CRAWL_THREADS` depend on your CPU, RAM and connection. After your crawler is running for the first time, run the benchmark in the directory you start it from:

```bash
curl -fsSL https://raw.githubusercontent.com/mwmbl/mwmbl/main/deploy/crawler/benchmark.sh | sh
```

It needs nothing beyond what the crawler needs, plus `python3` 3.9 or later, which Linux and macOS
already have: nothing is installed or built. The script downloads the benchmark and runs it. If you
run `docker` with `sudo`, pipe it to `sudo sh` instead. To pass flags, put them after `sh -s --`:

```bash
curl -fsSL https://raw.githubusercontent.com/mwmbl/mwmbl/main/deploy/crawler/benchmark.sh | sh -s -- --exhaustive
```

From a checkout of the repository, `make optimize` is the same thing (as are `make crawler` and
`python3 -m mwmbl.crawler.benchmark`).
It takes several minutes. Nothing needs exporting: Docker Compose reads the `.env` beside `docker-compose.yml`
as it does for the crawler (or the file given with `--env-file`), and the benchmark needs no API key.

For each combination it stops your crawler, restarts it with `CRAWLER_WORKERS` and `CRAWL_THREADS` set,
waits out a 10 second warmup, measures for 30 seconds, and stops it again. It scores each combination by
**fetches per minute**, read from the crawler's own Redis counter. It ends with a ranked table and the
winner, and whether it finishes, fails or is interrupted with Ctrl+C, your crawler is put back with the
settings it had before.

By default the search climbs: threads go up for a process count until more of them fetch less than 10% more,
and process counts go up until doubling them fetches less than 10% more. To try every combination, or to
choose your own, pass flags:

```bash
curl -fsSL https://raw.githubusercontent.com/mwmbl/mwmbl/main/deploy/crawler/benchmark.sh | sh -s -- --exhaustive --processes 1,2,4 --threads 25,50,100 --duration 60s
```

or, from a checkout:

```bash
make optimize ARGS='--exhaustive --processes 1,2,4 --threads 25,50,100 --duration 60s'
```

Combinations whose estimated memory exceeds your machine's RAM are skipped (`--no-memory-check` runs them
anyway). Results are appended to `benchmark-results-fetches-<date>.csv`.

When it finishes, set the winning values in your `.env` or `docker-compose.yml` and run
`docker compose up -d`. Some things to keep in mind:

- Each combination runs once for only seconds, so differences under about 10% are noise. When two are
  close, choose the one with fewer processes and threads.
- It counts every URL fetched, not how many were new or worth indexing, and more threads always fetch
  more. Treat the winner as the most your machine can usefully do, not a target to exceed: check the
  crawler's logs for refusals, and stay within what the sites you crawl and your connection can bear.
- Compose names its project after the directory, so run it from the directory your crawler was started from.

---

## Data Persistence

Both services store data in named Docker volumes:

- **Crawler data**: Persisted in a volume mapped to `~/.mwmbl` inside the container.
- **Redis data**: Persisted in a volume mapped to `/data` inside the Redis container.

These volumes ensure that data is **retained between restarts and upgrades**.

---

## Managing the Crawler

Stop the services without removing data:

```bash
docker-compose down
```

Stop and **remove all data** (crawler + Redis):

```bash
docker-compose down -v
```

View logs:

```bash
docker-compose logs -f
```

Access the crawler container:

```bash
docker exec -it mwmbl-crawler bash
```

---

## Automatic Updates (Recommended)

The crawler image follows the `mwmbl/crawler:stable` tag, which is republished on every
crawler release. To keep your crawler up to date without running anything by hand, install
the systemd timer shipped in [`deploy/crawler/`](https://github.com/mwmbl/mwmbl/tree/main/deploy/crawler)
of the repository:

```bash
sudo cp deploy/crawler/mwmbl-crawler-updater.service deploy/crawler/mwmbl-crawler-updater.timer /etc/systemd/system/
echo 'MWMBL_REPO_DIR=/home/crawler/mwmbl' | sudo tee /etc/default/mwmbl-crawler-updater
sudo systemctl daemon-reload
sudo systemctl enable --now mwmbl-crawler-updater.timer
```

(`MWMBL_REPO_DIR` is the clone of the repository on your machine.) The timer runs
`docker compose pull && docker compose up -d` once an hour at a random point within the hour,
so the fleet of volunteer crawlers does not all restart at once.

The server can also refuse an outdated crawler: if your crawler's version is below the
minimum the server accepts, submitting results returns **426 Upgrade Required** and the
crawler waits for the next update instead of submitting. If you see that in
`docker compose logs -f`, an update is on its way — or run the two commands above yourself.

To update manually instead:

```bash
docker compose pull && docker compose up -d
```

---

## Running on Hetzner Cloud (ARM)

For a cost-effective 24/7 crawler setup, we recommend using Hetzner Cloud ARM instances. This guide walks you through setting up a dedicated crawler server.

### 1. Choosing a Server

The best value-for-money option is the **Hetzner CAX11** instance:

- **Specs**: 2 vCPU (ARM64), 4GB RAM, 40GB NVMe
- **Location**: Falkenstein (DE) or Nuremberg (DE) recommended (close to Mwmbl core infrastructure)
- **OS**: Ubuntu 24.04 LTS
- **Networking**: Ensure IPv6 is enabled

### 2. Initial Server Hardening

After creating the instance, SSH into it as root to create a dedicated user and secure the machine.

**Create the 'crawler' user:**

```bash
# Create the user
adduser crawler
usermod -aG sudo crawler
usermod -aG docker crawler

# Setup SSH access for the new user
mkdir -p /home/crawler/.ssh
cp /root/.ssh/authorized_keys /home/crawler/.ssh/
chown -R crawler:crawler /home/crawler/.ssh
chmod 700 /home/crawler/.ssh
chmod 600 /home/crawler/.ssh/authorized_keys
```

**Basic Firewall (UFW):**

```bash
ufw allow ssh
ufw enable
```

### 3. Install Docker

Log in as the crawler user and run the official Docker installation script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### 4. Deploying the Mwmbl Crawler

**Clone the repository:**

```bash
git clone https://github.com/mwmbl/mwmbl.git
cd mwmbl
```

**Create the configuration:**

Create a `.env` file in the mwmbl directory:

```env
MWMBL_API_KEY=your_api_key_here
MWMBL_CONTACT_INFO=yourname@example.com (Mwmbl Crawler Operator)
```

> **Note**: To get an API key, log in at [mwmbl.org](https://mwmbl.org) and go to **Account → Create API key**. The key is shown only once, so copy it straight away.

**The Docker Compose File:**

Use the following `docker-compose.yml` optimized for the CAX11 (4GB RAM) hardware:

```yaml
services:
  mwmbl-crawler:
    build:
      context: .
      dockerfile: Dockerfile.crawler
    image: mwmbl/crawler:latest
    container_name: mwmbl-crawler
    restart: unless-stopped
    depends_on:
      redis:
        condition: service_healthy
    environment:
      - MWMBL_API_KEY=${MWMBL_API_KEY}
      - MWMBL_CONTACT_INFO=${MWMBL_CONTACT_INFO}
      - CRAWLER_WORKERS=5
      - CRAWL_THREADS=10
      - CRAWL_DELAY_SECONDS=0.1
      - REDIS_URL=redis://redis:6379
    volumes:
      - crawler_data:/home/mwmbl/.mwmbl
    networks:
      - mwmbl-net

  redis:
    image: redis:alpine
    container_name: mwmbl-redis
    restart: unless-stopped
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
    volumes:
      - redis_data:/data
    networks:
      - mwmbl-net
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s

networks:
  mwmbl-net:

volumes:
  redis_data:
  crawler_data:
```

**Launch the Crawler:**

```bash
docker compose up -d --build
```

### 5. Maintenance Commands

| Action | Command |
|--------|---------|
| Check Logs | `docker compose logs -f mwmbl-crawler` |
| Monitor Resources | `docker stats` |
| Update Crawler | `docker compose pull && docker compose up -d` |
| Restart | `docker compose restart` |

### 6. Local Shortcuts (Optional)

To make managing the crawler easier, add this to your local `~/.ssh/config` file:

```
Host mwmbl
    HostName [YOUR_SERVER_IP]
    User crawler
    IdentityFile ~/.ssh/your_key
```

You can then log in by simply typing `ssh mwmbl`.

---

## Need Help?

If you run into any issues or have questions, feel free to [open an issue](https://github.com/mwmbl/mwmbl/issues) or get in touch with the team.
