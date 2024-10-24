# Install Docker on Debian 12

>  Long term, I want to look at rootless podman but I am going with the devil I
>  know for now.
 
[Reference](https://linuxiac.com/how-to-install-docker-on-debian-12-bookworm/)
 
## Prerequisites
```bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg
```
  
## Docker 

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/debian bookworm stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin
```

That will give you the latest docker and docker compose. It should be started
and setup to run at boot.

### Post install test

- Verify its running with: `sudo systemctl is-active docker`
- Verify it can run simple container: `sudo docker run docker.io/hello-world`

> **NOTE** The `docker.io/` prefix is unnecessay with docker but needed for
> podman so I'm trying to build the habit now.

## Allow my user to run docker commands

```bash
sudo usermod -aG docker ${USER}
```

Logout and login to get the new group applied.

You can test with `docker ps`. It should show headers but no actual running
containers.
```
shunter@aspire:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
shunter@aspire:~$`
```

The hello-world container now runs without sudo as well:
```bash
docker run docker.io/hello-world
```

## dockge

I want to play with this for managing my containers.

"A fancy, easy-to-use and reactive self-hosted docker compose.yaml
stack-oriented manager."

GitHub project: https://github.com/louislam/dockge

> **NOTE** If I switch to podman,  I will need to install podman-docker along
> with it for dockge to work.

### Installation

[Reference](https://github.com/louislam/dockge/blob/master/README.md#-how-to-install)

```bash
# Do this as root

mkdir -p /opt/stacks /opt/dockge
cd /opt/dockge

curl https://raw.githubusercontent.com/louislam/dockge/master/compose.yaml --output compose.yaml

```

You may edit the port and stacks directorty in the compose.yaml.
- If you change stacks directory, make sure you get all _3_ path references.
- If you cnage port, only change the Host Port (front) side

And then start the server
```
docker compose up -d
```

> **Note:** I did a reboot and confirmed that dockge was indeed restarted with
> docker.

### Updating
```
cd /opt/dockge
docker compose pull && docker compose up -d
```
