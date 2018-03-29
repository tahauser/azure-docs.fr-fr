---
title: Utiliser Azure Files avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78f447c96afe7955f115de4bbd28015cd231bb53
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Volumes persistants avec les fichiers Azure

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans un cluster Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods et être provisionné de façon statique ou dynamique. Ce document détaille le provisionnement dynamique d’un partage de fichiers Azure comme volume persistant Kubernetes dans un cluster AKS. 

Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Créer un compte de stockage

Lors du provisionnement dynamique d’un partage de fichiers Azure comme volume Kubernetes, il est possible d’utiliser un compte de stockage tant que celui-ci est contenu dans le même groupe de ressources que le cluster AKS. Si nécessaire, créez un compte de stockage dans le même groupe de ressources que le cluster AKS. 

Pour identifier le groupe de ressources approprié, utilisez la commande [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Recherchez un groupe de ressources portant un nom semblable à `MC_clustername_clustername_locaton`, où « clustername » est le nom de votre cluster AKS et « location » est la région Azure où le cluster a été déployé.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Utilisez la commande [az storage account create][az-storage-account-create] pour créer le compte de stockage. 

À l’aide de cet exemple, mettez à jour `--resource-group` avec le nom du groupe de ressources et `--name` avec le nom de votre choix.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Créer la classe de stockage

Une classe de stockage permet de définir la façon dont un partage de fichiers Azure est créé. Un compte de stockage peut être spécifié dans la classe. Si aucun compte de stockage n’est spécifié, un `skuName` et un `location` doivent être spécifiés, et tous les comptes de stockage du groupe de ressources associé sont évalués pour rechercher une correspondance.

Pour plus d’informations sur les classes de stockage Kubernetes pour les fichiers Azure, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Créez un fichier nommé `azure-file-sc.yaml` et copiez-y le manifeste suivant. Remplacez `storageAccount` par le nom de votre compte de stockage cible.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Créez la classe de stockage avec la commande [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant utilise l’objet de classe de stockage pour provisionner dynamiquement un partage de fichiers Azure. 

Le manifeste suivant sert à créer une revendication de volume persistant `5GB` de même taille que l’accès `ReadWriteOnce`.

Créez un fichier nommé `azure-file-pvc.yaml` et copiez-y le manifeste suivant. Vérifiez que `storageClassName` correspond à la classe de stockage créée à la dernière étape.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Créez la revendication de volume persistant avec la commande [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Une fois que c’est terminé, le partage de fichiers est créé. Un secret Kubernetes contenant des informations d’identification et des informations de connexion est également créé.

## <a name="using-the-persistent-volume"></a>Utilisation du volume persistant

Le manifeste suivant crée un pod qui utilise la revendication de volume persistant `azurefile` pour monter le partage de fichiers Azure sur le chemin `/mnt/azure`.

Créez un fichier nommé `azure-pvc-files.yaml` et copiez-y le manifeste suivant. Vérifiez que `claimName` correspond à la revendication de volume persistant créée à la dernière étape.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Créez le pod avec la commande [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution avec le disque Azure monté dans le répertoire `/mnt/azure`. Vous pouvez voir le volume monté en inspectant votre pod via `kubectl describe pod mypod`.

## <a name="mount-options"></a>Options de montage
 
Les valeurs par défaut fileMode et dirMode diffèrent d’une version Kubernetes à l’autre, comme détaillé dans le tableau suivant.
 
| version | value |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0-v1.8.5 | 0700 |
| v1.8.6 ou version supérieure | 0755 |
| V1.9.0 | 0700 |
| v1.9.1 ou version supérieure | 0755 |
 
Si vous utilisez un cluster de la version 1.8.5 ou supérieure, les options de montage peuvent être spécifiées sur l’objet de classe de stockage. L’exemple suivant définit `0777`.
 
```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```
 
Si vous utilisez un cluster de version 1.8.0 - 1.8.4, un contexte de sécurité peut être spécifié avec la définition de la valeur `runAsUser` sur `0`. Pour plus d’informations sur le contexte de sécurité Pod, consultez [Configurer un contexte de sécurité][kubernetes-security-context].

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les volumes persistants Kubernetes utilisant Azure Files.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
