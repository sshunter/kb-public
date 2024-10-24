# Watchtower

https://containrrr.dev/watchtower/

Watchtower is "a process for automating Docker container base image updates."

## compose.yaml

I created this thru dockge using the default run command provided on [their
website](https://containrrr.dev/watchtower/usage-overview/).


```bash
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower
```
Here is the resultant compose file:

I also added a restart line. When I updated docker on the host, this container
didn't restart, I'm hoping this cures it.

```yaml
version: "3.3"
services:
  watchtower:
    container_name: watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    image: containrrr/watchtower
    restart: unless-stopped
networks: {}
```



This by default updates once a day, each 24 hours after the container starts.

## disabling for a container

I am not using this yet, mostly because this is all for my home server and I am
not super worried about stability here, but if there is a container that has
problems autoupdating, here is an example of the label used to turn it off.


```yaml
version: "3"
services:
  someimage:
    container_name: someimage
    labels:
      - "com.centurylinklabs.watchtower.enable=false"
```