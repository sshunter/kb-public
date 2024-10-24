# Autoheal

I want to restart unhealth containers. This isn't baked into docker (yet?)

I found this [autheal image](https://hub.docker.com/r/willfarrell/autoheal/) and
am experimenting with it.


## Compose file

```
version: "3.3"
services:
  autoheal:
    container_name: autoheal
    restart: always
    environment:
      - AUTOHEAL_CONTAINER_LABEL=all
      - AUTOHEAL_INTERVAL=30
      - AUTOHEAL_START_PERIOD=30
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    image: willfarrell/autoheal
networks: {}
```

Then label the containers to be modified (in docker compose):

```
  labels:
    autoheal: true
```

> **N.B.** I think the docks are suspect. I believe
> `AUTOHEAL_CONTAINER_LABEL=all` means I don't need to label cantainers to heal.
> It will restart any that go unhealthy. To only watch labelled containers, I
> should set `AUTOHEAL_CONTAINER_LABEL=autoheal` (where `autoheal` is the name
> of the label being set, `autoheal` is the default label searched for if
> nothing is set in the environment)
>
> For now I am just leaving the label in place.

## TODO 

- I am unsure how this plays with the `retries` value set in healthchecks. Spend
  some time and figure that out.
- I probably do only want to monitor a subset of containers