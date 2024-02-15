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

I then started the thing and connected to it with a shell so I could set a
password with `pihole -a -p`
[Reference](https://discourse.pi-hole.net/t/how-do-i-set-or-reset-the-web-interface-password/1328)


And that was  really it.

## Configuration

I logged into /admin and looked at Settings.



### DNS

**Upstream DNS Servers:** Quad9 (filtered, DNSSEC). I checked all for boxes, am
not sure if the IPv6 are actually used here.


**TODO:** And this is where things went blat. I haven't sorted out how to get a
IPv6 address for the server that eero is happy with.

- eero shows no IPv6 associated with the server (unlile my nvidia shield which
shows both IPv4 and IPv6)
- My big brain idea to simply convert the IPv4 address to its IPv6
representation isn't working either. That one hurts my big brain and I am
setting this aside.
