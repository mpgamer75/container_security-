## Session 2 – Partie Pratique

### 1. Éviter l’Exposition Involontaire de Ports

1. Lancer un container Nginx en limitant l’exposition des ports :
   ```bash
   docker run -d --name nginx-limited -p 8080:80 nginx
2. Vérifier l’exposition du port sur l’hôte :
  # Avec netstat
  sudo netstat -tulpn | grep 8080

  # Ou avec ss
  sudo ss -tulpn | grep 8080



## Activités Pratiques

### 1. Éviter l’Exposition Involontaire de Ports

1. Lancer un container Nginx en restreignant l’exposition des ports :
   ```bash
   docker run -d --name nginx-test -p 8080:80 nginx

# Avec netstat
sudo netstat -tulpn | grep 8080
# Sortie  :
tcp        0      0 0.0.0.0:8080       0.0.0.0:*      LISTEN      12345/docker-proxy

# Avec ss
sudo ss -tulpn | grep 8080
# Sortie  :
LISTEN     0      128    0.0.0.0:8080      0.0.0.0:*    users:(("docker-proxy",pid=12345))

3. Questions :

Le port 80 du container est-il bien mappé sur le port 8080 de l’hôte ?
Réponse : Oui, on voit 0.0.0.0:8080 en écoute, lié au processus docker-proxy qui redirige vers le port 80 du container.

Que se passe-t-il si vous omettez le -p ?
Réponse : Aucun port n’est publié sur l’hôte, le service Nginx reste accessible uniquement depuis l’intérieur du réseau Docker (pas depuis l’interface localhost).


## 2. Restreindre les Permissions d’Accès aux Fichiers Sensibles

1. Monter /etc/passwd en lecture seule dans un container Alpine :

  docker run -it --rm \
  -v /etc/passwd:/mnt/passwd:ro \
  alpine sh

2. Dans le shell du container, tester :

  # Lecture
cat /mnt/passwd
# Sortie attendue : affichage des lignes du fichier passwd (ex. "root:x:0:0:root:/root:/bin/sh", etc.)

# Écriture
echo 'test' >> /mnt/passwd
# Sortie attendue : sh: can't create /mnt/passwd: Read-only file system

. Questions :

Pouvez-vous lire le contenu de /mnt/passwd ?
Réponse : Oui, la lecture est autorisée (cat /mnt/passwd affiche le contenu).

Pouvez-vous écrire dans /mnt/passwd ? Pourquoi ?
Réponse : Non, l’écriture échoue car le volume est monté en mode :ro (read-only), protégeant le fichier hôte.


## 3. Auditer la Configuration avec Docker Bench for Security

1. Cloner et lancer Docker Bench for Security :

git clone https://github.com/docker/docker-bench-security.git
cd docker-bench-security
sudo sh docker-bench-security.sh


Score de l’hôte :

Exemple de résultat :

[INFO] 1.1  - Host Configuration
[PASS] 1.1.1 - Ensure a separate partition for containers has been created
...
[WARN] 1.5.3 - Ensure aufs is not used (docker storage driver)
...
[INFO] Total checks: 50, PASS: 42, WARN: 6, INFO: 2, FAIL: 0
Score approximatif : 84 % (42/50 passes).

Auditer le container vulnerables/web-dvwa :

```bash
sudo sh docker-bench-security.sh -c vulnerables/web-dvwa
```
Exemple de remarques :

```yaml
[FAIL] 2.2.1 - Ensure no anonymous volumes are created
[FAIL] 2.3.4 - Ensure images are scanned for vulnerabilities
[WARN] 2.4.2 - Ensure capabilities are dropped
...
```
Total checks: 20, PASS: 8, WARN: 5, INFO: 2, FAIL: 5
Observation : De nombreux échecs et avertissements indiquant un container vulnérable et mal configuré (volumes anonymes, absence de scans, privilèges excessifs).

4. Stocker et Utiliser des Secrets avec Vault
Lancer un serveur Vault en mode développeur :

```bash
docker run --cap-add=IPC_LOCK \
  -e 'VAULT_LOCAL_CONFIG={
    "storage": {"file":{"path":"/vault/file"}},
    "listener":[{"tcp":{"address":"0.0.0.0:8200","tls_disable":true}}],
    "default_lease_ttl":"168h","max_lease_ttl":"720h","ui":true
  }' \
  -p 8200:8200 vault:1.13.3 server
```
Dans l’UI http://localhost:8200 :

Root token (ex. s.ABCD1234EFGH5678) affiché au démarrage.

Créer une politique containers-read.hcl :

```hcl
path "secret/containers/mon-secret" {
  capabilities = ["read"]
}
```
Appliquer la politique :

```bash

vault policy write containers-read containers-read.hcl
```
Créer un userpass :

```bash

vault auth enable userpass
vault write auth/userpass/users/user1 \
  password="P@ssw0rd!" \
  policies="containers-read"
```
Stocker un secret :

```bash

vault kv put secret/containers/mon-secret api_key="ABC123SECRET"
```
Depuis un container Alpine, récupérer le secret :

```bash

docker run --rm alpine sh -c "\
  apk add --no-cache curl jq && \
  TOKEN=$(curl -s --request POST http://host.docker.internal:8200/v1/auth/userpass/login/user1 \
    --data '{\"password\":\"P@ssw0rd!\"}' | jq -r .auth.client_token) && \
  curl -s --header \"X-Vault-Token: $TOKEN\" \
    http://host.docker.internal:8200/v1/secret/data/containers/mon-secret | jq ."
```
Réponse attendue :

json
Copier
Modifier
{
  "data": {
    "data": {
      "api_key": "ABC123SECRET"
    }
  }
}
Questions :

Quel est le secret retourné ?
Réponse : "ABC123SECRET".

Comment sécuriser davantage Vault en production ?
Réponse : Activer TLS, limiter les adresses sources, utiliser AppRole ou JWT, restreindre les politiques, mettre en place un HA backend, etc.

5. Analyse d’une Image Compromise (API Leak)
Récupérer l’image de démonstration :

```bash

docker pull ety92/demo:v1
```
Inspecter l’image pour extraire les fichiers suspects :

```bash

docker create --name temp-demo ety92/demo:v1
docker export temp-demo > demo.tar
tar -tf demo.tar | grep -Ei 'api.*key|secret'
```
Sortie attendue :

```swift

usr/src/app/config/.env
```
Examiner le fichier :

```bash

tar -xOf demo.tar usr/src/app/config/.env
```
# Sortie :
API_KEY=LEAKED12345
Questions :

Où se trouve la clé API dans l’image ?
Réponse : Dans le fichier /usr/src/app/config/.env.

Comment le développeur aurait-il dû faire pour éviter cela ?
Réponse :

Ne pas inclure de fichiers .env dans l’image (ajouter .env au .dockerignore).

Charger les secrets au runtime via variables d’environnement ou services de gestion de secrets (Vault, AWS Secrets Manager, etc.).

6. Mode Rootless Docker
Installer Docker rootless sur Ubuntu/Debian :

```bash
curl -fsSL https://get.docker.com/rootless | sh
```
Ajouter à ~/.bashrc :

```bash
export PATH=$HOME/bin:$PATH
export DOCKER_HOST=unix:///run/user/1000/docker.sock

```

Démarrer le démon rootless :

```bash

systemctl --user start docker
```
Lancer Nginx en mode rootless :
```bash

docker run -d --name nginx-rootless -p 80:80 nginx
```

Tester l’accès http://localhost :

Résultat possible : Échec (Permission denied pour ports <1024).

Solution :

```bash
sudo setcap 'cap_net_bind_service=+ep' $(which dockerd-rootless.sh)
```
ou utiliser un port non privilégié (ex. -p 8080:80).

Réexécuter Docker Bench en mode rootless :

```bash
git clone https://github.com/docker/docker-bench-security.git
cd docker-bench-security
sh docker-bench-security.sh
```
Différences clés :

Moins de checks liés aux permissions root (ex. absence de certaines recommandations sur le démon).

Risque réduit d’élévation de privilèges sur l’hôte, mais certaines fonctionnalités (network, volumes) peuvent être limitées sans droits root.

