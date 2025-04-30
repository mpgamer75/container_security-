# Session 4 : Sécurité dans la CI/CD

## Signature d'images avec COSIGN
## 1.Générer une paire de clés
Installation de cosign
![alt text](image_session4/image-14.png)

On a choisi la passphrase : Choisirunepassphrase
![alt text](image_session4/image.png)
Push de l'image
![alt text](image_session4/image-4.png)
Signature avec Cosign + clé GPG
![alt text](image_session4/image-5.png)

## 3.Modifier l'image et pousser une version modifiée

Créer une modification (ex: ajouter un fichier vide)
![alt text](image_session4/image-6.png)

Committer la modification et pousser
![alt text](image_session4/image-7.png)
![alt text](image_session4/image-8.png)

## 4. Vérifier la signature avant/après modification

Vérification de l'image originale et altérée

![alt text](image_session4/image-10.png)

## 5. Quelles sont les résultats de ces 2 commandes ?
Pour l'image:v1
- Signature vérifiée avec succès.
- Cosign a validé les revendications.
- Il a vérifié l'inclusion dans le journal de transparence (hors ligne).
- La signature correspond à la clé publique dans cosign.pub.

Pour l'image:v2
- Aucune signature n'a été trouvée.
- Cela signifie que image:v2 n'a pas été signée avec cosign (ou du moins pas avec la clé que nous avons fourni).

## Sécurité dans les Pipelines CI/CD

![alt text](image_session4/image-11.png)
![alt text](image_session4/image-13.png)

- Lint (hadolint-scan) → vérifie la qualité du Dockerfile.

- Build & Sign (build-image) → construit et pousse l'image + signature Cosign.

- Verify (verify_image) → vérifie la signature Cosign.

- Scan (trivy-scan) →  Trivy détecte une vulnérabilité critique dans l'image.

![alt text](image_session4/image-12.png)

Ici il n'arrive pas à passer le lint (l'étape du Dockerfile)