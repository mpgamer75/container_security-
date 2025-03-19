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


## Image vulnérable

```bash 
charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ docker pull vulnerables/web-dvwa
Using default tag: latest
latest: Pulling from vulnerables/web-dvwa
3e17c6eae66c: Pull complete 
0c57df616dbf: Pull complete 
eb05d18be401: Pull complete 
e9968e5981d2: Pull complete 
2cd72dba8257: Pull complete 
6cff5f35147f: Pull complete 
098cffd43466: Pull complete 
b3d64a33242d: Pull complete 
Digest: sha256:dae203fe11646a86937bf04db0079adef295f426da68a92b40e3b181f337daa7
Status: Downloaded newer image for vulnerables/web-dvwa:latest
docker.io/vulnerables/web-dvwa:latest
```

*Explication*

## Scan avec Trivy 

```bash 
                     │                                    │ https://avd.aquasec.com/nvd/cve-2019-18218                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9022    │          │              │                             │ 7.0.33-0+deb9u2                    │ php: memcpy with negative length via crafted DNS response    │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9022                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9024    │          │              │                             │                                    │ php: Out-of-bounds read in base64_decode_xmlrpc in           │
│                          │                  │          │              │                             │                                    │ ext/xmlrpc/libxmlrpc/base64.c                                │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9024                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9637    │          │              │                             │ 7.0.33-0+deb9u3                    │ php: File rename across filesystems may allow unwanted       │
│                          │                  │          │              │                             │                                    │ access during processing                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9637                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9638    │          │              │                             │                                    │ php: Uninitialized read in exif_process_IFD_in_MAKERNOTE     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9638                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9639    │          │              │                             │                                    │ php: Uninitialized read in exif_process_IFD_in_MAKERNOTE     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9639                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9640    │          │              │                             │                                    │ php: Invalid read in exif_process_SOFn()                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9640                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9675    │          │              │                             │                                    │ php: buffer overflow in ext/phar/tar.c                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9675                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7062    │          │              │                             │ 7.0.33-0+deb9u8                    │ php: NULL pointer dereference in PHP session upload progress │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7062                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7067    │          │              │                             │                                    │ php: out-of-bounds read when using a malformed url-encoded   │
│                          │                  │          │              │                             │                                    │ string                                                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7067                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21702   │          │              │                             │ 7.0.33-0+deb9u11                   │ php: NULL pointer dereference in SoapClient                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21702                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21703   │          │              │                             │ 7.0.33-0+deb9u12                   │ php: Local privilege escalation via PHP-FPM                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21703                   │
│                          ├──────────────────┤          ├──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2022-31625   │          │ fix_deferred │                             │                                    │ php: Uninitialized array in pg_query_params() leading to RCE │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2022-31625                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2022-31626   │          │              │                             │                                    │ php: password of excessive length triggers buffer overflow   │
│                          │                  │          │              │                             │                                    │ leading to RCE                                               │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2022-31626                   │
│                          ├──────────────────┼──────────┼──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-14851   │ MEDIUM   │ fixed        │                             │ 7.0.33-0+deb9u1                    │ php: exif: Buffer over-read in                               │
│                          │                  │          │              │                             │                                    │ exif_process_IFD_in_MAKERNOTE()                              │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-14851                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-17082   │          │              │                             │                                    │ php: Cross-site scripting (XSS) flaw in Apache2 component    │
│                          │                  │          │              │                             │                                    │ via body of 'Transfer-Encoding:...                           │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-17082                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11038   │          │              │                             │ 7.0.33-0+deb9u5                    │ gd: Information disclosure in gdImageCreateFromXbm()         │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11038                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11045   │          │              │                             │ 7.0.33-0+deb9u7                    │ php: DirectoryIterator class accepts filenames with embedded │
│                          │                  │          │              │                             │                                    │ \0 byte and treats them...                                   │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11045                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11046   │          │              │                             │                                    │ php: OOB read in bc_shift_addsub                             │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11046                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11047   │          │              │                             │                                    │ php: Information disclosure in exif_read_data()              │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11047                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11048   │          │              │                             │ 7.0.33-0+deb9u8                    │ php: Integer wraparounds when receiving multipart forms      │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11048                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11050   │          │              │                             │ 7.0.33-0+deb9u7                    │ php: Out of bounds read when parsing EXIF information        │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11050                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7063    │          │              │                             │ 7.0.33-0+deb9u8                    │ php: Files added to tar with Phar::buildFromIterator have    │
│                          │                  │          │              │                             │                                    │ all-access permissions                                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7063                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7064    │          │              │                             │                                    │ php: Information disclosure in exif_read_data() function     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7064                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7066    │          │              │                             │                                    │ php: Information disclosure in function get_headers          │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7066                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7070    │          │              │                             │ 7.0.33-0+deb9u10                   │ php: URL decoding of cookie names can lead to different      │
│                          │                  │          │              │                             │                                    │ interpretation of...                                         │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7070                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7071    │          │              │                             │ 7.0.33-0+deb9u11                   │ php: FILTER_VALIDATE_URL accepts URLs with invalid userinfo  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7071                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21704   │          │              │                             │                                    │ php: security issues in pdo_firebase module                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21704                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21705   │          │              │                             │                                    │ php: SSRF bypass in FILTER_VALIDATE_URL                      │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21705                   │
│                          ├──────────────────┤          ├──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21707   │          │ affected     │                             │                                    │ php: Special character breaks path in xml parsing            │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21707                   │
│                          ├──────────────────┼──────────┼──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7068    │ LOW      │ fixed        │                             │ 7.0.33-0+deb9u9                    │ php: Use of freed hash key in the phar_parse_zipfile         │
│                          │                  │          │              │                             │                                    │ function                                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7068                    │
├──────────────────────────┼──────────────────┼──────────┼──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ php7.0-xml               │ CVE-2017-8923    │ CRITICAL │ will_not_fix │                             │                                    │ php: Overflowing the length of string causes crash           │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2017-8923                    │
│                          ├──────────────────┤          ├──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11034   │          │ fixed        │                             │ 7.0.33-0+deb9u5                    │ php: Heap buffer overflow in function exif_process_IFD_TAG() │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11034                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11035   │          │              │                             │                                    │ php: Heap buffer overflow in function exif_iif_add_value()   │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11035                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11036   │          │              │                             │                                    │ php: Buffer over-read in exif_process_IFD_TAG() leading to   │
│                          │                  │          │              │                             │                                    │ information disclosure                                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11036                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11039   │          │              │                             │                                    │ php: Out-of-bounds read due to integer overflow in           │
│                          │                  │          │              │                             │                                    │ iconv_mime_decode_headers()                                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11039                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11040   │          │              │                             │                                    │ php: Buffer over-read in exif_read_data()                    │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11040                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11043   │          │              │                             │ 7.0.33-0+deb9u6                    │ php: underflow in env_path_info in fpm_main.c                │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11043                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-13224   │          │              │                             │ 7.0.33-0+deb9u5                    │ oniguruma: Use-after-free in onig_new_deluxe() in regext.c   │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-13224                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9020    │          │              │                             │ 7.0.33-0+deb9u2                    │ php: Invalid memory access in function xmlrpc_decode()       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9020                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9021    │          │              │                             │                                    │ php: Heap-based buffer over-read in PHAR reading functions   │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9021                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9023    │          │              │                             │                                    │ php: Heap-based buffer over-read in mbstring regular         │
│                          │                  │          │              │                             │                                    │ expression functions                                         │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9023                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9641    │          │              │                             │ 7.0.33-0+deb9u3                    │ php: Uninitialized read in exif_process_IFD_in_TIFF          │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9641                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7059    │          │              │                             │ 7.0.33-0+deb9u7                    │ php: Out of bounds read in php_strip_tags_ex                 │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7059                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7060    │          │              │                             │                                    │ php: Global buffer-overflow in mbfl_filt_conv_big5_wchar     │
│                          │                  │          │              │                             │                                    │ function                                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7060                    │
│                          ├──────────────────┼──────────┼──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2017-7189    │ HIGH     │ will_not_fix │                             │                                    │ php: misparsing fsockopen calls in main/streams/xp_socket.c  │
│                          │                  │          │              │                             │                                    │ leads to information disclosure                              │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2017-7189                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2017-7272    │          │              │                             │                                    │ php: potential SSRF via fsockopen                            │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2017-7272                    │
│                          ├──────────────────┤          ├──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-14883   │          │ fixed        │                             │ 7.0.33-0+deb9u1                    │ php: exif: integer overflow leading to out-of-bound buffer   │
│                          │                  │          │              │                             │                                    │ read in exif_thumbnail_extract()                             │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-14883                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-19518   │          │              │                             │                                    │ php: imap_open() allows running arbitrary shell commands via │
│                          │                  │          │              │                             │                                    │ mailbox parameter                                            │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-19518                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-19935   │          │              │                             │                                    │ php: NULL pointer dereference in ext/imap/php_imap.c         │
│                          │                  │          │              │                             │                                    │ resulting in a denial of service...                          │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-19935                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-20783   │          │              │                             │                                    │ php: Buffer over-read in PHAR reading functions              │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-20783                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11041   │          │              │                             │ 7.0.33-0+deb9u5                    │ php: Heap buffer over-read in exif_scan_thumbnail()          │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11041                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11042   │          │              │                             │                                    │ php: Heap buffer over-read in exif_process_user_comment()    │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11042                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-18218   │          │              │                             │ 7.0.33-0+deb9u11                   │ file: heap-based buffer overflow in cdf_read_property_info   │
│                          │                  │          │              │                             │                                    │ in cdf.c                                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-18218                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9022    │          │              │                             │ 7.0.33-0+deb9u2                    │ php: memcpy with negative length via crafted DNS response    │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9022                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9024    │          │              │                             │                                    │ php: Out-of-bounds read in base64_decode_xmlrpc in           │
│                          │                  │          │              │                             │                                    │ ext/xmlrpc/libxmlrpc/base64.c                                │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9024                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9637    │          │              │                             │ 7.0.33-0+deb9u3                    │ php: File rename across filesystems may allow unwanted       │
│                          │                  │          │              │                             │                                    │ access during processing                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9637                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9638    │          │              │                             │                                    │ php: Uninitialized read in exif_process_IFD_in_MAKERNOTE     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9638                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9639    │          │              │                             │                                    │ php: Uninitialized read in exif_process_IFD_in_MAKERNOTE     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9639                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9640    │          │              │                             │                                    │ php: Invalid read in exif_process_SOFn()                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9640                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-9675    │          │              │                             │                                    │ php: buffer overflow in ext/phar/tar.c                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-9675                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7062    │          │              │                             │ 7.0.33-0+deb9u8                    │ php: NULL pointer dereference in PHP session upload progress │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7062                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7067    │          │              │                             │                                    │ php: out-of-bounds read when using a malformed url-encoded   │
│                          │                  │          │              │                             │                                    │ string                                                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7067                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21702   │          │              │                             │ 7.0.33-0+deb9u11                   │ php: NULL pointer dereference in SoapClient                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21702                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21703   │          │              │                             │ 7.0.33-0+deb9u12                   │ php: Local privilege escalation via PHP-FPM                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21703                   │
│                          ├──────────────────┤          ├──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2022-31625   │          │ fix_deferred │                             │                                    │ php: Uninitialized array in pg_query_params() leading to RCE │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2022-31625                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2022-31626   │          │              │                             │                                    │ php: password of excessive length triggers buffer overflow   │
│                          │                  │          │              │                             │                                    │ leading to RCE                                               │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2022-31626                   │
│                          ├──────────────────┼──────────┼──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-14851   │ MEDIUM   │ fixed        │                             │ 7.0.33-0+deb9u1                    │ php: exif: Buffer over-read in                               │
│                          │                  │          │              │                             │                                    │ exif_process_IFD_in_MAKERNOTE()                              │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-14851                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-17082   │          │              │                             │                                    │ php: Cross-site scripting (XSS) flaw in Apache2 component    │
│                          │                  │          │              │                             │                                    │ via body of 'Transfer-Encoding:...                           │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-17082                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11038   │          │              │                             │ 7.0.33-0+deb9u5                    │ gd: Information disclosure in gdImageCreateFromXbm()         │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11038                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11045   │          │              │                             │ 7.0.33-0+deb9u7                    │ php: DirectoryIterator class accepts filenames with embedded │
│                          │                  │          │              │                             │                                    │ \0 byte and treats them...                                   │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11045                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11046   │          │              │                             │                                    │ php: OOB read in bc_shift_addsub                             │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11046                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11047   │          │              │                             │                                    │ php: Information disclosure in exif_read_data()              │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11047                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11048   │          │              │                             │ 7.0.33-0+deb9u8                    │ php: Integer wraparounds when receiving multipart forms      │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11048                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2019-11050   │          │              │                             │ 7.0.33-0+deb9u7                    │ php: Out of bounds read when parsing EXIF information        │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2019-11050                   │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7063    │          │              │                             │ 7.0.33-0+deb9u8                    │ php: Files added to tar with Phar::buildFromIterator have    │
│                          │                  │          │              │                             │                                    │ all-access permissions                                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7063                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7064    │          │              │                             │                                    │ php: Information disclosure in exif_read_data() function     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7064                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7066    │          │              │                             │                                    │ php: Information disclosure in function get_headers          │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7066                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7070    │          │              │                             │ 7.0.33-0+deb9u10                   │ php: URL decoding of cookie names can lead to different      │
│                          │                  │          │              │                             │                                    │ interpretation of...                                         │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7070                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7071    │          │              │                             │ 7.0.33-0+deb9u11                   │ php: FILTER_VALIDATE_URL accepts URLs with invalid userinfo  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7071                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21704   │          │              │                             │                                    │ php: security issues in pdo_firebase module                  │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21704                   │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21705   │          │              │                             │                                    │ php: SSRF bypass in FILTER_VALIDATE_URL                      │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21705                   │
│                          ├──────────────────┤          ├──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-21707   │          │ affected     │                             │                                    │ php: Special character breaks path in xml parsing            │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-21707                   │
│                          ├──────────────────┼──────────┼──────────────┤                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2020-7068    │ LOW      │ fixed        │                             │ 7.0.33-0+deb9u9                    │ php: Use of freed hash key in the phar_parse_zipfile         │
│                          │                  │          │              │                             │                                    │ function                                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2020-7068                    │
├──────────────────────────┼──────────────────┼──────────┤              ├─────────────────────────────┼────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ rsync                    │ CVE-2016-9841    │ CRITICAL │              │ 3.1.2-1+deb9u1              │ 3.1.2-1+deb9u2                     │ zlib: Out-of-bounds pointer arithmetic in inffast.c          │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2016-9841                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2016-9843    │          │              │                             │                                    │ zlib: Big-endian out-of-bounds pointer                       │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2016-9843                    │
│                          ├──────────────────┼──────────┤              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2016-9840    │ HIGH     │              │                             │                                    │ zlib: Out-of-bounds pointer arithmetic in inftrees.c         │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2016-9840                    │
│                          ├──────────────────┤          │              │                             │                                    ├──────────────────────────────────────────────────────────────┤
│                          │ CVE-2016-9842    │          │              │                             │                                    │ zlib: Undefined left shift of negative number                │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2016-9842                    │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2018-5764    │          │              │                             │ 3.1.2-1+deb9u3                     │ rsync: sanitization bypass in parse_argument in options.c    │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-5764                    │
├──────────────────────────┼──────────────────┼──────────┤              ├─────────────────────────────┼────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ tar                      │ CVE-2018-20482   │ MEDIUM   │              │ 1.29b-1.1                   │ 1.29b-1.1+deb9u1                   │ tar: Infinite read loop in sparse_dump_region function in    │
│                          │                  │          │              │                             │                                    │ sparse.c                                                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-20482                   │
├──────────────────────────┼──────────────────┼──────────┤              ├─────────────────────────────┼────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ tzdata                   │ DLA-2424-1       │ UNKNOWN  │              │ 2018e-0+deb9u1              │ 2020d-0+deb9u1                     │ tzdata - new upstream version                                │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┤                                                              │
│                          │ DLA-2509-1       │          │              │                             │ 2020e-0+deb9u1                     │                                                              │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┤                                                              │
│                          │ DLA-2542-1       │          │              │                             │ 2021a-0+deb9u1                     │                                                              │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┤                                                              │
│                          │ DLA-2797-1       │          │              │                             │ 2021a-0+deb9u2                     │                                                              │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ DLA-2963-1       │          │              │                             │ 2021a-0+deb9u3                     │ tzdata - new timezone database                               │
│                          ├──────────────────┤          │              │                             ├────────────────────────────────────┤                                                              │
│                          │ DLA-3051-1       │          │              │                             │ 2021a-0+deb9u4                     │                                                              │
├──────────────────────────┼──────────────────┼──────────┼──────────────┼─────────────────────────────┼────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ util-linux               │ CVE-2016-2779    │ HIGH     │ affected     │ 2.29.2-1+deb9u1             │                                    │ util-linux: runuser tty hijack via TIOCSTI ioctl             │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2016-2779                    │
│                          ├──────────────────┼──────────┤              │                             ├────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                          │ CVE-2021-37600   │ LOW      │              │                             │                                    │ util-linux: integer overflow can lead to buffer overflow in  │
│                          │                  │          │              │                             │                                    │ get_sem_elements() in sys-utils/ipcutils.c...                │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2021-37600                   │
├──────────────────────────┼──────────────────┼──────────┼──────────────┼─────────────────────────────┼────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ xz-utils                 │ CVE-2022-1271    │ HIGH     │ fixed        │ 5.2.2-1.2+b1                │ 5.2.2-1.2+deb9u1                   │ gzip: arbitrary-file-write vulnerability                     │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2022-1271                    │
├──────────────────────────┼──────────────────┤          │              ├─────────────────────────────┼────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ zlib1g                   │ CVE-2018-25032   │          │              │ 1:1.2.8.dfsg-5              │ 1:1.2.8.dfsg-5+deb9u1              │ zlib: A flaw found in zlib when compressing (not             │
│                          │                  │          │              │                             │                                    │ decompressing) certain inputs...                             │
│                          │                  │          │              │                             │                                    │ https://avd.aquasec.com/nvd/cve-2018-25032                   │
└──────────────────────────┴──────────────────┴──────────┴──────────────┴─────────────────────────────┴────────────────────────────────────┴──────────────────────────────────────────────────────────────┘

/etc/ssl/private/ssl-cert-snakeoil.key (secrets)

Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)

HIGH: AsymmetricPrivateKey (private-key)
═════════════════════════════════════════════════════════════════════════════════
Asymmetric Private Key
─────────────────────────────────────────────────────────────────────────────────
 /etc/ssl/private/ssl-cert-snakeoil.key:1 (added by 'apt-get update &&     apt-get upgrade -y')
─────────────────────────────────────────────────────────────────────────────────
   1 [ -----BEGIN PRIVATE KEY-----*******************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************-----END PRIVATE KEY-----
   2   
─────────────────────────────────────────────────────────────────────────────────


charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ 

```

## Scan des vulnérabilités 

Scan basique mais pas avecv notre image 

```bash

charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ grype alpine:latest
 ✔ Vulnerability DB                [updated]  
 ✔ Loaded image                                                  alpine:latest 
 ✔ Parsed image                    sha256:aded1e1a5b3705116fa0a92ba074a5e0b0031  
 ✔ Cataloged contents              a8b452e4987d5d281800a3174e98cea4c5c6d5311c81  
   ├── ✔ Packages                        [15 packages]  
   ├── ✔ File digests                    [82 files]  
   ├── ✔ File metadata                   [82 locations]  
   └── ✔ Executables                     [17 executables]  
 ✔ Scanned for vulnerabilities     [0 vulnerability matches]  
   ├── by severity: 0 critical, 0 high, 0 medium, 0 low, 0 negligible
   └── by status:   0 fixed, 0 not-fixed, 0 ignored 
No vulnerabilities found

```

*Explication* : 

**Avec notre image** : 

```bash 

charles@ubuntu-hack-uwu:~/Desktop/container_security/monprojet$ grype image_simple
 ✔ Loaded image                                            image_simple:latest 
 ✔ Parsed image                    sha256:a5f4e91f25f43dc330363423c4750b915427f  
 ✔ Cataloged contents              42b6780680164408d845ba58090dceeed577c7edb2ea  
   ├── ✔ Packages                        [15 packages]  
   ├── ✔ File digests                    [82 files]  
   ├── ✔ File metadata                   [82 locations]  
   └── ✔ Executables                     [17 executables]  
 ✔ Scanned for vulnerabilities     [0 vulnerability matches]  
   ├── by severity: 0 critical, 0 high, 0 medium, 0 low, 0 negligible
   └── by status:   0 fixed, 0 not-fixed, 0 ignored 
No vulnerabilities found
```

