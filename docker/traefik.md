# Traefik

## Adding a new service

**NOTE** I think this doesn't work for stuff on the host network. I ended up
mapping dockge in the dynamic.yml file mapping instead. Will experiment later.

1. Create your docker stack thru dockge as normal.
2. Add the following labels to the service entry for the container you want to expose:

    ```yaml
    labels:
      - traefik.enable=true
      - traefik.http.routers.dockge.rule=Host(`aspire-dockge.local.lan`)
      - traefik.http.routers.dockge.entrypoints=https
      - traefik.http.routers.dockge.tls=true    

      # If your container exposes more than one port, just map the one you want explicitly.
      # Otherwise it assumes you want to map the smallest port number. Better to just be explicit.
      #
      # - traefik.http.routers.dockge.service=dockge@docker
      # - traefik.http.services.dockge.loadbalancer.server.port=5001  

    ```

3. Add an entry to the `Local DNS records` section in settings on
   <https://pihole.local.lan> with the `192.168.4.34` (the docker server's IP
   address) and `aspire-dockge.local.lan` as the hostname.
4. If you want to use HTTPS, you can configure Traefik to do that as well. You'll need to generate a certificate and key for your domain and then add the following labels:
5. Add an entry to `/etc/hosts` on aspire with `127.0.0.1` 
6. *Because eero (or something else) hammers my pihole dns server,* add an entry
   to `c:\windows\system32\drivers\etc\hosts` etc with `192.168.4.34` as the IP and `aspire-dockge.local.lan` as the hostname.
7. (Re)start you new stack and then visit <https://traefik.local.lan> and you
   should see router and service entries for your new container.

You should now be able to reach your new service at <https://aspire-dockge.local.lan>.


## Problem

When I rebooted server, `traefik` failed to start. Haven't tried to debug it yet.
```
reverse-proxy-1  | 2025-05-05T12:16:59Z ERR error="accept tcp [::]:443: use of closed network connection" entryPointName=https
reverse-proxy-1  | 2025-05-05T12:16:59Z ERR Error while starting server error="accept tcp [::]:443: use of closed network connection" entryPointName=https
reverse-proxy-1  | 2025-05-05T12:16:59Z ERR error="accept tcp [::]:80: use of closed network connection" entryPointName=http
reverse-proxy-1  | 2025-05-05T12:16:59Z ERR error="accept tcp [::]:8080: use of closed network connection" entryPointName=traefik
reverse-proxy-1  | 2025-05-05T12:16:59Z ERR error="close tcp [::]:80: use of closed network connection" entryPointName=http
reverse-proxy-1  | 2025-05-05T12:16:59Z ERR Error while starting server error="accept tcp [::]:8080: use of closed network connection" entryPointName=traefik
reverse-proxy-1  | 2025-05-05T12:17:00Z INF Server stopped
reverse-proxy-1  | 2025-05-05T12:17:00Z INF Shutting down
reverse-proxy-1  | 2025-05-05T12:17:00Z ERR Failed to list containers for docker error="Get \"http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json\": context canceled" providerName=docker
reverse-proxy-1  | 2025-05-05T12:17:00Z ERR Cannot retrieve data error="context
canceled" providerName=docker
```

