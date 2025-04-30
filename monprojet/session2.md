# Session 2

## Gestion des Réseaux et des Secrets

## Sécurisation Réseau

### Éviter l’Exposition Involontaire de Ports

Lancer un container avec restriction de ports  :

![alt text](image_session2/image-4.png)

Vérifier si le port est exposé avec netstat ou ss.

![alt text](image_session2/image-5.png)

### Restreindre les permissions d’accès aux fichiers sensibles

Monter un volume avec des permissions spécifiques

Pouvez-vous lire le fichier /mnt/passwd ?

![alt text](image_session2/image-6.png)

On peut effectivement voir

Pouvez-vous écrire dans /mnt/passwd ?

![alt text](image_session2/image-7.png)

On ne peut pas écrire dans le passwd

### Auditer la configuration d’un container avec Docker Bench

Installer et exécuter Docker Bench for Security 

![alt text](image_session2/image-8.png)

![alt text](image-9.png)

