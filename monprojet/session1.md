# Session 1 : Introduction aux Containers et à la Sécurité

### 1. Lancer un Container Simple

## Intro 

- ```bash
    docker run -it --rm alpine sh
  ```

Cette commande lance une image docker, fais un pull puis nous place en tant que super user ( root ) 

## Voir les stats d'un container 

```bash
   charles@ubuntu-hack-uwu:~$ docker run -it --rm alpine sh
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
f18232174bc9: Pull complete 
Digest: sha256:a8560b36e8b8210634f77d9f7f9efd7ffa463e380b75e2e74aff4511df3ef88c
Status: Downloaded newer image for alpine:latest
/ # ls
CONTAINER ID   NAME             CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS 
a962eb7fedbb   test-container   --        -- / --             --        --        --          -- 
 
^C
got 3 SIGTERM/SIGINTs, forcefully exiting
charles@ubuntu-hack-uwu:~$ 

```
*Explication* : 




## Container avec privilège élévé et simulation d'évasion 

```bash

charles@ubuntu-hack-uwu:~$ docker run --rm -v /:/mnt alpine sh -c 'ls /mnt'
bin
boot
cdrom
dev
etc
home
lib
lib32
lib64
libx32
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
swapfile
sys
tmp
usr
var
```

*Explication* : 





## Création d'un container simple 

```bash 

charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ sudo docker build -t image_simple .
[sudo] password for charles: 
[+] Building 0.9s (6/6) FINISHED                                  docker:default
 => [internal] load build definition from Dockerfile                        0.0s
 => => transferring dockerfile: 122B                                        0.0s
 => [internal] load metadata for docker.io/library/alpine:latest            0.0s
 => [internal] load .dockerignore                                           0.0s
 => => transferring context: 2B                                             0.0s
 => [1/2] FROM docker.io/library/alpine:latest                              0.0s
 => [2/2] RUN adduser -D appuser                                            0.7s
 => exporting to image                                                      0.1s
 => => exporting layers                                                     0.0s
 => => writing image sha256:a5f4e91f25f43dc330363423c4750b915427ffdb2f1a1b  0.0s
 => => naming to docker.io/library/image_simple                             0.0s

 ```

*Explication* : 


## Part 2 

```bash

charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ sudo docker run image_simple
Container sécurisé

```

- UID : 

```bash 
charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ sudo docker run image_simple id appuser
uid=1000(appuser) gid=1000(appuser) groups=1000(appuser)
charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ 

```

*Explication* : 



## Déconnecter du réseau 

### Problème lié aux permissions et à la création d'un container 

**Résolution** : 

```bash
charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS             PORTS     NAMES
a962eb7fedbb   nginx     "/docker-entrypoint.…"   About an hour ago   Up About an hour   80/tcp    test-container
charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ sudo docker run -d --name my_image_container image_simple
[sudo] password for charles: 
2b36813a1e11598076a193caf96a954dc7cec671ac4d48ea152a0221624faaf7
charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ 
```

*Explication* : 


