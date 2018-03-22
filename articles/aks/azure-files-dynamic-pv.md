---
title: Utiliser Azure Files avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ce37cfdd70f95822a912f6ea71b9e4a3f9a30a14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Volumes persistants avec les fichiers Azure

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans un cluster Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods, et être provisionné de façon statique ou dynamique. Ce document détaille le provisionnement dynamique d’un partage de fichiers Azure comme volume persistant Kubernetes dans un cluster AKS. 

Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes].

## <a name="prerequisites"></a>Prérequis

Lors du provisionnement dynamique d’un partage de fichiers Azure comme volume Kubernetes, il est possible d’utiliser un compte de stockage tant que celui-ci est contenu dans le même groupe de ressources que le cluster AKS. Si nécessaire, créez un compte de stockage dans le même groupe de ressources que le cluster AKS. 

Pour identifier le groupe de ressources approprié, utilisez la commande [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Le résultat de l’exemple suivant affiche les groupes de ressources, associés tous les deux à un cluster AKS. Le groupe de ressources dont le nom ressemble à *MC_monClusterAKS_monClusterAKS_eastus* contient les ressources du cluster AKS et se trouve à l’endroit où le compte de stockage doit être créé. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Une fois le groupe de ressources identifié, créez le compte de stockage à l’aide la commande [az storage account create][az-storage-account-create].

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Créer la classe de stockage

Une classe de stockage permet de définir la configuration d’un volume persistant créé dynamiquement. Les éléments tels que le nom du compte de stockage Azure, la référence (SKU) et la région sont définis dans l’objet de classe de stockage. Pour plus d’informations sur les classes de stockage Kubernetes, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

L’exemple suivant indique qu’un compte de stockage de type référence (SKU) `Standard_LRS` dans la région `eastus` peut être utilisé lors de la demande de stockage. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Pour utiliser un compte de stockage particulier, vous pouvez utiliser le paramètre `storageAccount`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant utilise l’objet de classe de stockage pour provisionner dynamiquement un élément de stockage. Lorsque vous utilisez Azure Files, un partage de fichiers Azure est créé dans le compte de stockage sélectionné ou spécifié dans l’objet de classe de stockage.

>  [!NOTE]
>   Assurez-vous qu’un compte de stockage approprié a été créé au préalable dans le même groupe de ressources que les ressources du cluster AKS. Le nom de ce groupe de ressources ressemble à *MC_monClusterAKS_monClusterAKS_eastus*. La revendication de volume persistant ne peut pas parvenir à provisionner le partage de fichiers Azure si un compte de stockage n’est pas disponible. 

Le manifeste suivant sert à créer une revendication de volume persistant `5GB` de même taille que l’accès `ReadWriteOnce`. Pour plus d’informations sur les modes d’accès de la revendication de volume persistant, consultez [Modes d’accès][access-modes].

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

## <a name="using-the-persistent-volume"></a>Utilisation du volume persistant

Une fois la revendication du volume persistant créée, et le stockage provisionné convenablement, un pod peut être créé avec un accès au volume. Le manifeste suivant crée un pod qui utilise la revendication de volume persistant `azurefile` pour monter le partage de fichiers Azure sur le chemin `/var/www/html`. 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
