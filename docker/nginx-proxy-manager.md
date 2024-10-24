# Nginx Proxy Manager

> **Note** I have disabled this container in dockge so things may need to be
> reconfigured if need this.

https://nginxproxymanager.com/

## Setup

- https://nginxproxymanager.com/guide/#hosting-your-home-network
- https://nginxproxymanager.com/setup/

I stuck with the defaults and am thus using sqlite.

Be aware that you need to login on port 81 and change the admin email and
password. Default log in was `admin@example.com`/`changeme` and it forces you to
change them. So that is good.

> **TODO** Next steps are to move to a docker network and then proxy jellyfin. 

### Shared network

I want to be able to run a bunch of containers behind the proxy eventually. For
now we are starting with [jellyfin](./jellyfin.md)

First edit nginx-proxy's compose.yml
```yaml
# Add the following block to the app service.
  networks:
    - shared-proxy-netowrk

# and add the network to the end of the file
networks:
  shared-proxy-network:
    external: true
```

> NOTE: I think you are supposed to be about to use a default network in the
> bottom block and avaoid having to update the service too, but I failed to make
> that go.
The make similar changes to jellyfin's compose.yml. This should be true for
every container we add.

Then you can log into your proxy manager's admin console and add a "Proxy Host"
for each service.

- Domain Names: use the hostname you'll be accessing the service as. 
  > **NOTE:** For a local service, make sure you add an entry in pihole's dns
  > for this
- Scheme: http if possible to avoid cert nonsense, since these containers are on
  the same machine.
- Forward Hostname/IP: The service name specified in your compose.yml should
  work. If it doesn't maybe play wiht container_name and hostname entry options.
- Forward Port: The port on the service's container.

Once this is working you no longer need to expose the proxied service's port(s)
to the host.

```yml
# e.g. I commented out the whole block for jellyfin. I was only using 8096 and once it was
# commented out, at that point the 'ports' block was empty and leaving it empty is an error
# so it had to go as well

      # These aren't needed so long as we are using the proxy to access jellyfin
      #ports: 
      # - 8096:8096 Http web UI (this is the one I would use if I want access without the proxy)
      # - 8920:8920 # Optional - Https webUI (you need to set up your own certificate)      
      # - 7359:7359/udp # Optional - Allows clients to discover Jellyfin on the local network
      # - 1900:1900/udp # Optional - Service discovery used by DNLA and clients
```


> **TODO** After that we need to tunnel this to the interwebs and then see if I
> can make android app while accessing jellyfin over its non-standard port
> (8920). e.g. I want to use 443 because cloudflare tunnel wants it. 