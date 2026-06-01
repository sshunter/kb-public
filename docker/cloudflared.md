# Cloudflared

## Cloudflare in a docker container

<!-- markdownlint-disable MD034 -->

I added a new service to my [jellyfin](./nginx-proxy-manager.md)'s stack via its
docker compose file.

```yaml
  cloudflared:
    image: cloudflare/cloudflared
    container_name: cloudflare-tunnel
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=${TUNNEL_TOKEN}
    depends_on:
      - jellyfin
```

The TUNNEL_TOKEN is acquired from cloudflare's Tunnels page (apologies I have no
idea how to link that generically yet). Zero Trust > Networks > Connectors. You
create a tunnel and follow the prompts and will eventually (step3?) be presented
with the token to add the .env file.  Once done, follow the rest of the prompts.

At the end I have =K

Tunnel name: home-proxy  
Public hostnames

| hostname                  | path | service              |
|---------------------------|------|----------------------|
| jeffyfin.publicdomain.com | *    | http://jellyfin:8096 |

> **WARNING** I added this to the .env file via the dockge page, but the .env
> file created by dockge is world readable and that seems to
> defeat the purpose. So `chmod o-r .env` from an ssh login after you set the
> first variable (this means I need to `sudo docker` for some ops on the
> stack)

## Cloudflare in a docker container, shared network for multiple containers

I added a new service to my [proxy](./nginx-proxy-manager.md)'s stack via its
docker compose file.

```yaml
  cloudflared:
    image: cloudflare/cloudflared
    container_name: cloudflare-tunnel
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=${TUNNEL_TOKEN}
    networks:
      - shared-proxy-network
```

The TUNNEL_TOKEN is acquired from cloudflare's Tunnels page (apologies I have no
idea how to link that generically yet). You create a tunnel and follow the
prompts and will eventually (step3?) be presented with the token to add the .env
file.  Once done, follow the rest of the prompts.

At the end I have:

Tunnel name: home-proxy  
Public hostnames

| hostname                  | path | service      |
|---------------------------|------|--------------|
| jeffyfin.publicdomain.com | *    | http://proxy |

I realized after I could have not bothered with the shared network or the nginx
proxy.

- Add the cloudflared service to jellyfin's compose file.
- Make sure those two are on the same default network (e.g. remove the shared
  network configured now)
- And then in the cloudflare admin page, point the tunnel to the container at
  http://jellyfin:8096

I might try that for the next service but the proxy container has no shell to
speak of so hopefully the way I've done it is low risk enough.

> **WARNING** I added this to the .env file via the dockge page, but the .env
> file created by dockge is world readable and that seems to
> defeat the purpose. So `chmod o-r .env` from an ssh login after you set the
> first variable

## (I DID NOT USE) Install on Debian 12 with apt

```bash
# Add cloudflare gpg key
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

# Add this repo to your apt repositories
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared bookworm main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

# install cloudflared
sudo apt-get update && sudo apt-get install cloudflared
```
