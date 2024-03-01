# Samba on Docker

I pretty much just stripped down the compose file from
https://github.com/servercontainers/samba/

I am only using samba so I disabled all the optional goodness and moved the
container off of the host network by mapping port 445

## Compose file

```yml
version: "3"
# docker-compose.yml, from example at https://github.com/ServerContainers/samba

services:
  samba:
    build: .
    image: ghcr.io/servercontainers/samba:latest
    restart: always
    ports:
      - 445:445 # SMB Protocol  
    environment:
      SAMBA_CONF_LOG_LEVEL: 1
      # disable optional services
      WSDD2_DISABLE: 1
      AVAHI_DISABLE: 1
      NETBIOS_DISABLE: 1
      
      GROUP_smbuser: 1500
      ACCOUNT_shunter: ${ACCOUNT_shunter}
      UID_shunter: 1000
      GROUPS_shunter: smbuser

      SAMBA_VOLUME_CONFIG_shared_home: "[Home]; path=/shares/homes/%U; valid users =
        shunter; guest ok = no; read only = no; browseable = yes"
      SAMBA_VOLUME_CONFIG_public: "[Public]; path=/shares/public; valid users =
        shunter; guest ok = no; read only = no; browseable = yes; force group =
        smbuser"
      SAMBA_VOLUME_CONFIG_public_ro: "[Public ReadOnly]; path=/shares/public; guest ok
        = yes; read only = yes; browseable = yes; force group = smbuser"
      SAMBA_VOLUME_CONFIG_shunter: "[Shunter]; path=/shares/shunter; valid users =
        shunter; guest ok = no; read only = no; browseable = yes"
    volumes:
      - ./shares/public:/shares/public
      - ./shares/homes:/shares/homes
      - /mnt/seagate/shunter:/shares/shunter
networks: {}
```

## TODO 

- Sometime when I am bored, look in the sbmd-only build of the container. I did
  not switch to it now because its a month older than `samba:latest` and its not
  clear if that's oversight or intentional

