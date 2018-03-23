---
title: Utiliser Azure Files avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1def417f97a94fa0770b99606cd3a68189d1d51b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="volumes-with-azure-files"></a>Volumes avec fichiers Azure

Les applications basées sur des conteneurs doivent souvent consulter et conserver des données dans un volume de données externe. Azure Files peut être utilisé en tant que banque de données externe. Cet article explique comment utiliser Azure Files comme volume Kubernetes dans Azure Container Service.

Pour plus d’informations sur les volumes Kubernetes, consultez [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Avant d’utiliser un partage de fichiers Azure en tant que volume Kubernetes, vous devez créer un compte de stockage Azure et le partage de fichiers. Vous pouvez utiliser le script suivant pour effectuer ces tâches. Notez ou mettez à jour les valeurs de paramètres. Certaines d’entre elles sont nécessaires lors de la création du volume Kubernetes.

```azurecli-interactive
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Créer une clé secrète Kubernetes

Kubernetes a besoin d’informations d’identification pour accéder au partage de fichiers. Ces informations d’identification sont stockées dans un [secret Kubernetes][kubernetes-secret], qui est référencé lors de la création d’un module Kubernetes.

Utilisez la commande suivante pour créer le secret. Remplacez `STORAGE_ACCOUNT_NAME` par le nom de votre compte de stockage et `STORAGE_ACCOUNT_KEY` par la clé de stockage.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Monter le partage de fichiers en tant que volume

Vous pouvez monter votre partage Azure Files dans votre pod en configurant le volume dans ses spécifications. Créez un fichier nommé `azure-files-pod.yaml` avec le contenu suivant. Mettez à jour la valeur `aksshare` avec le nom donné au partage Azure Files.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Utilisez kubectl pour créer un pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Vous disposez maintenant d’un conteneur en cours d’exécution avec le partage Azure Files monté dans le répertoire `/mnt/azure`. Vous pouvez voir le volume monté en inspectant votre pod via `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les volumes Kubernetes utilisant Azure Files.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour Azure Files][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
