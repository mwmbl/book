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
 - Supporting the community
 - Helping form official organisations

If you'd like to help in any way, just get in touch and let us know.

# Running the Mwmbl Crawler with Docker Compose

You can easily run the Mwmbl crawler on your own server using Docker Compose. This setup includes both the crawler and a Redis instance for task coordination and state management.

## New crawler vs old crawler

The new crawler lives in the [Mwmbl repo](https://github.com/mwmbl/mwmbl/).
This crawler is considerably more resource intensive compared to the
old crawler, which was a relatively simple Python script. The problem
with the [old crawler](https://github.com/mwmbl/crawler-script/) was
that in order to run it, we needed to use a
lot of resources on the central server, which was slowing down the
main site. This was not good.

So if you're able to run the new crawler, please do. Eventually, we
will ask people who are still running the old crawler to stop so that
we can free up resources on the central server.

## API Key

In order to run the crawler you need an API key.
If you would like to run the crawler, please get in touch either on
[our Matrix Space](https://matrix.to/#/#mwmbl:matrix.org) or by
sending an email to [info@mwmbl.org](mailto:info@mwmbl.org).

Please give a few words about yourself and why you are interested in
crawling for Mwmbl.

Once we've approved your application, we'll send you an API key.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- A Mwmbl API key (you can [request one here](#))

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
- `CRAWLER_WORKERS` – Number of parallel processes to run (default: 2).
- `REDIS_URL` – Redis connection string (default: `redis://redis:6379`).

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

> **Note**: To get an API key, join the [Mwmbl Matrix channel](https://matrix.to/#/#mwmbl:matrix.org) or email [info@mwmbl.org](mailto:info@mwmbl.org).

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
      - CRAWLER_WORKERS=4
      - CRAWLER_THREADS=30
      - CRAWL_DELAY_SECONDS=0.1
      - REDIS_URL=redis://redis:6379
    volumes:
      - crawler_data:/root/.mwmbl
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
| Update Crawler | `git pull && docker compose pull && docker compose up -d --build` |
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
