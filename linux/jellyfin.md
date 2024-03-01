# Jellyfin

I am running using the official docker container. (TODO LINK)

But I did not want to use the host network. I found the [Linux Server
container](https://docs.linuxserver.io/images/docker-jellyfin/) which doesn't
use host. Rather than switch containers, I copied the networking from their
compose file.

```yaml
    # network_mode: host
    ports:
      - 8096:8096 
      # - 8920:8920 # Optional - Https webUI (you need to set up your own certificate)
      # - 7359:7359/udp # Optional - Allows clients to discover Jellyfin on the local network
      # - 1900:1900/udp # Optional - Service discovery used by DNLA and clients
```

## Behind the proxy

Once I setup jellyfin behind the new [proxy](./nginx-proxy-manager.md) I was
able to comment out the ports: block entirely. This means 8096 is no longer used
on my host (aspire). The proxied URL is http://jellyfin.lan internally

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

I am keeping the commented out stuff for reference and because I might re-add
8096 for convenience from PC. I added host file entries to make proxy work while
on vpn though so I probably won't.

## Hardware decoding

I turned this on in config, using default (2/26/2024).

[Here](https://community.intel.com/t5/Processors/Celeron-Processor-J4125-video-codecs-capabilities/m-p/1441652)
is a link with some details on the supported features. For when defaults break.

![alt text](j4125_decode_encode.png)