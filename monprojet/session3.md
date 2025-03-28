# Session 3 : Sécurité des kubes ( kubernetes )

**capture d'écran 1**


# Déployer un Cluster Kubernetes avec Kind

## Déployer un Cluster Kubernetes avec Kind
Créer un cluster avec 2 master node et 2 worker node avec un l'aide d'un fichier yaml. 
![alt text](image_session3\image.png)

Vérifier l’état du cluster

![alt text](image_session3\image-1.png)

Pour afficher la liste des namespace 
```bash
kubectl get namespaces

```
![alt text](image_session3\image-2.png)

kubectl version

![alt text](image_session3\image-3.png)

## Expérimentation des RBAC

Créer un namespace test-rbac

![alt text](image_session3\image-4.png)

Déployer ce pod dans le namespace test-rbac avec la commande kubectl apply -f mon-pod.yaml

![alt text](image_session3\image-5.png)

Pour afficher les logs de ce pod ```bash kubectl logs nginx -n test-rbac```

![alt text](image_session3\image-6.png)

Créer un role pour lire les pods dans le namespace test-rbac toujours avec la commande kubectl apply -f

![alt text](image_session3\image-7.png)
Comment afficher ce role ```bash kubectl get role -n test-rbac```
![alt text](image_session3\image-8.png)

Ensuite il faut lier ce Role avec un utilisateur "fictif" titi, appliquer ce fichier avec kubectl

![alt text](image_session3\image-9.png)

Il faut créer l'utilisateur titi dans le cluster maintenant, parce que par défaut vous avez un compte admin avec les clés localisées ici ~/.kube/config

![alt text](image_session3\image-10.png)

kind-control-plane est le nom du container créer par kind à adapter si besoin.

![alt text](image_session3\image-11.png)

Ajouter cet utilisateur dans le kube context

## Détection et alerte d'intrusions dans kubernetes avec l'outil Falco

Ajouter le repo helm et faire un update
![alt text](image_session3\image-12.png)

Créer un namespace falco 

![alt text](image_session3\image-13.png)

Installer la chart falco dans le namespace falco et ici on active falcosidekick pour avoir une UI

![alt text](image_session3\image-14.png)

Vérifier que vos pods soit running, cela peut prendre 2 minutes

![alt text](image_session3\image-15.png)

![alt text](image_session3\image-16.png)
Ouvrez un nouveau shell, dans celui ci faites un port forward afin d'afficher l'UI http://127.0.0.1:2802 de falco dans votre navigateur


![alt text](image_session3\image-17.png)

## Falco en pratique

Il n'y a pas beaucoup d'activités sur le cluster (pod), pour y remédier on va installer un pod avec kubectl apply -f

![alt text](image_session3\image-18.png)

Executer un shell dans le pod afin de générer une alerte consultable via l'interface de falco.

![alt text](image_session3\image-19.png)
On a une alerte concernant cette action
priorité notice et critical
la regle ets de Drop and execute new binary in container et Terminal shell in container

![alt text](image_session3\image-20.png)

Toujours dans le shell du pod, générer une requête sur l'API Kubernetes

![alt text](image_session3\image-21.png)

![alt text](image_session3\image-22.png)


![alt text](image_session3\image-23.png)

![alt text](image_session3\image-24.png)

![alt text](image_session3\image-25.png)

On a eu une alerte concernant cette action, sa priorité était notice et ça regle Contact K8S API Server From Containe