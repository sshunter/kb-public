# Pi-hole

> **NOTE:** This is on hold because I couldn't get eero to point to it.  
> I think its related to the fact that eero isn't showing/assigning and IPv6
> address to the thing. Maybe because DHCP IPv6 connections in debian 12 are
> weird (all the examples I found in 3 minutes of searching showed static IP
> configuration.

## Installation with dockge

I tweaked the quickstart compose.yaml and pasted it into dockge.y
```
# More info at https://github.com/pi-hole/docker-pi-hole/ and https://docs.pi-hole.net/
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    # For DHCP it is recommended to remove these ports and instead add: network_mode: "host"
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 1989:80/tcp
    environment:
      TZ: America/Denver
      # WEBPASSWORD: 'set a secure password here or it will be random'
      DNSMASQ_LISTENING: all
      # Volumes store your data between container upgrades
    volumes:
      - ./etc-pihole:/etc/pihole
      - ./etc-dnsmasq.d:/etc/dnsmasq.d
    #   https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
    cap_add:
      - NET_ADMIN # Required if you are using Pi-hole as your DHCP server, else not needed
    restart: unless-stopped
networks: {}
```

For the password, I left WEBPASSWORD commented out and then started the thing
and connected to it in a shell so I could set a
password with `pihole -a -p`
[Reference](https://discourse.pi-hole.net/t/how-do-i-set-or-reset-the-web-interface-password/1328)

And that was really it.

## Configuration

I logged into /admin and looked at Settings. Only took notes on DNS but I messed
with the block lists a lot, mostly choosing tracker blocking.

### DNS

**Upstream DNS Servers:** Quad9 (filtered, DNSSEC). I checked all for boxes, am
not sure if the IPv6 are actually used here.

#### EERO side

In the app, on the DNS settings page, set IPv4 Primary and Secondary to the same
IPv4 address of the server. And do the same for both IPv6 address. Use the IPv4
and IPv6 addresses shown in the app for the server.

The only thing that worries me is I have no option to reserve the IPv6 address,
I may end up configuring the server to have static address and trust that eero
won't reassign it.

#### What if I don't get an Ipv6 address

- When I started, eero showed no IPv6 associated with the server (unlikee my
nvidia shield which showed both IPv4 and IPv6)
- My big brain idea to simply convert the IPv4 address to its IPv6
representation didn't work, I still am not sure why.
- What worked? => I updated eero, which rebooted eero, then I rebooted
server to get it back on network. And now I have an IPv6 address.


