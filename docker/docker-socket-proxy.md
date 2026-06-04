# docker-socket-proxy

Tech Note: Securing the Docker Socket with `docker-socket-proxy`

## The Problem: The Docker Socket Vulnerability

Containers like Traefik, Autoheal, and Watchtower require access to the host's Docker socket (`/var/run/docker.sock`) to monitor events, read container labels, or manage lifecycles.

Mounting the socket directly into a container—even as Read-Only (`:ro`)—carries massive security risks:

- **Read-Only (`:ro`):** Allows the container to read all cluster configurations, environment variables (including API keys and database passwords), and sensitive metadata. If a public-facing container like Traefik is compromised, the attacker instantly gains a blueprint of the entire server.
- **Read-Write (`:rw`):** Gives the container absolute root privileges over the host VPS. An attacker can create new containers, mount the host's root filesystem (`/`), and completely compromise the server.

---

## The Solution: `tecnativa/docker-socket-proxy`

`docker-socket-proxy` acts as an enhanced, application-level firewall for the Docker API. Instead of mounting the raw UNIX socket into your public or operational containers, you deploy the socket proxy as an intermediary.

### How It Works

1. **Isolation:** Only the socket proxy container touches the actual `/var/run/docker.sock`.
2. **Private Network:** The proxy exposes the Docker API over a secure, internal Docker bridge network on port `2375`.
3. **Granular Access Control:** You use environment variables to explicitly toggle what API routes (`GET`, `POST`, `DELETE`) are allowed for specific containers.

---

## Why Implement This Soon?

### 1. Absolute Isolation for Traefik (Public Facing)

Traefik only needs to **read** container metadata to route traffic. It never needs to start, stop, or delete containers.
By routing Traefik through the proxy, you can set `CONTAINERS=1` (to allow reading container data) but block all execution capabilities. If Traefik is breached via a zero-day vulnerability, the attacker hits a brick wall at the proxy and cannot manipulate the host system.

### 2. Privilege Reduction for Automation (Autoheal/Watchtower)

Tools like Autoheal and Watchtower require write access to do their jobs, but they don't need _total_ control.

- You can configure a proxy instance for Autoheal that _only_ allows container restarts, blocking it from deleting images or volumes.
- You can restrict Watchtower to only image pulling and container recreation, preventing it from executing malicious network changes.

---

## Baseline Implementation Reference

Instead of mounting `/var/run/docker.sock` directly, the architecture shifts to this pattern:

### 1. The Proxy Definition

```yaml
services:
  socket-proxy:
    image: tecnativa/docker-socket-proxy
    container_name: docker-socket-proxy
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro # Only the proxy touches the socket
    environment:
      - NETWORKS=1 # Allow reading network info
      - SERVICES=1 # Allow reading service info
      - CONTAINERS=1 # Allow reading container info
      # All POST/PUT/DELETE options default to 0 (Blocked)
    networks:
      - socket-isolated-net

networks:
  socket-isolated-net:
    internal: true # Completely cut off from the internet
```

### 2. The Client Definition (e.g., Traefik)

Instead of mounting the volume, you instruct the client container to look at the proxy via the internal network:

```yaml
services:
  traefik:
    image: traefik:v3
    # ... ports and labels ...
    environment:
      - DOCKER_HOST=tcp://docker-socket-proxy:2375 # Connects to proxy instead of socket
    networks:
      - proxy-net
      - socket-isolated-net # connects to proxy network
```

## Next Steps for Infrastructure Hardening

- [ ] Audit current stacks for `/var/run/docker.sock` usage.
- [ ] Deploy a single `docker-socket-proxy` instance on an internal-only bridge network.
- [ ] Migrate Traefik to use the `DOCKER_HOST` environment variable pointing to the proxy.
- [ ] Analyze individual POST/DELETE permission requirements for Watchtower and Autoheal before migrating them.
