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

