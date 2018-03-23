---
title: Utiliser un disque Azure avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 36e25d7e5f1e5c6e1cf72442b73ac081810d216a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Volumes persistants avec les disques Azure

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods, et être provisionné de façon statique ou dynamique. Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes].

Ce document explique comment utiliser les volumes persistants avec des disques Azure dans un cluster Azure Container Service (AKS).

## <a name="built-in-storage-classes"></a>Classes de stockage intégrées

Une classe de stockage permet de définir la création dynamique d’une unité de stockage avec un volume persistant. Pour plus d’informations sur les classes de stockage Kubernetes, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Chaque cluster AKS comprend deux classes de stockage précréées, toutes deux configurées pour utiliser des disques Azure. La classe de stockage `default` configure un disque Azure standard. La classe de stockage `managed-premium` configure un disque Azure Premium. Si les nœuds AKS dans votre cluster utilisent le stockage Premium, sélectionnez la classe `managed-premium`.

Utilisez la commande [kubectl get sc][kubectl-get] pour afficher les classes de stockage créées au préalable.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant (PVC) est utilisée pour configurer automatiquement le stockage basé sur une classe de stockage. Dans ce cas, une PVC peut utiliser une des classes de stockage créées au préalable pour créer un disque géré Azure standard ou Premium.

Créez un fichier nommé `azure-premimum.yaml` et copiez-y le manifeste suivant.

Notez que la classe de stockage `managed-premium` est spécifiée dans l’annotation et que la revendication demande un disque de taille `5GB` avec l’accès `ReadWriteOnce`. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Créez la revendication de volume persistant avec la commande [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

> [!NOTE]
> Un disque Azure peut être monté uniquement avec le type de mode d’accès ReadWriteOnce, qui le rend disponible à uniquement un seul nœud AKS. Si vous avez besoin de partager une volume persistent entre plusieurs nœuds, envisagez d’utiliser [Azure Files][azure-files-pvc].

## <a name="using-the-persistent-volume"></a>Utilisation du volume persistant

Une fois la revendication de volume persistant créée et le disque approvisionné convenablement, un pod peut être créé avec un accès au disque. Le manifeste suivant crée un pod qui utilise la revendication de volume persistant `azure-managed-disk` pour monter le disque Azure sur le chemin d’accès `/mnt/azure`. 

Créez un fichier nommé `azure-pvc-disk.yaml` et copiez-y le manifeste suivant.

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
        claimName: azure-managed-disk
```

Créez le pod avec la commande [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution avec le disque Azure monté dans le répertoire `/mnt/azure`. Vous pouvez voir le volume monté en inspectant votre pod via `kubectl describe pod mypod`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les volumes persistants Kubernetes utilisant des disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md