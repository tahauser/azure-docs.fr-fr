---
title: Utiliser un disque Azure avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/25/2018
ms.author: nepeters
ms.openlocfilehash: aa89cf9fe4e2cd5b63017558e89401de86effdc9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Volumes persistants avec les disques Azure

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans un cluster Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods, et être provisionné de façon statique ou dynamique. Ce document détaille l’approvisionnement dynamique d’un disque Azure comme volume persistant Kubernetes dans un cluster AKS. 

Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes].

## <a name="built-in-storage-classes"></a>Classes de stockage intégrées

Une classe de stockage permet de définir la configuration d’un volume persistant créé dynamiquement. Pour plus d’informations sur les classes de stockage Kubernetes, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Chaque cluster AKS comprend deux classes de stockage précréées, toutes deux configurées pour utiliser des disques Azure. Utilisez la commande `kubectl get storageclass` pour les voir.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

Si ces classes de stockage sont adaptées à vos besoins, il est inutile d’en créer une autre.

## <a name="create-storage-class"></a>Créer la classe de stockage

Si vous souhaitez créer une nouvelle classe de stockage configurée pour les disques Azure, vous pouvez utiliser l’exemple de manifeste suivant. 

La valeur de `storageaccounttype`, `Standard_LRS`, indique qu’un disque Standard est créé. Elle peut être modifiée en `Premium_LRS` pour créer un [disque Premium][premium-storage]. Pour pouvoir utiliser des disques Premium, il faut que la machine virtuelle du nœud AKS ait une taille compatible. Consultez [ce document] [ premium-storage] pour connaître la liste des tailles compatibles.

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```

## <a name="create-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant utilise un objet de classe de stockage pour approvisionner dynamiquement un élément de stockage. Si vous utilisez un disque Azure, il sera créé dans le même groupe de ressources que les ressources AKS.

Cet exemple de manifeste crée une revendication de volume persistant à l’aide de la classe de stockage `azure-managed-disk`, pour créer un disque d’une taille de `5GB` avec un accès `ReadWriteOnce`. Pour plus d’informations sur les modes d’accès de la revendication de volume persistant, consultez [Modes d’accès][access-modes].

> [!NOTE]
> Un disque Azure peut être monté uniquement avec le type de mode d’accès ReadWriteOnce, qui le rend disponible à uniquement un seul nœud AKS. Si vous avez besoin de partager une volume persistent entre plusieurs nœuds, envisagez d’utiliser [Azure Files][azure-files-pvc]. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Utilisation du volume persistant

Une fois la revendication de volume persistant créée et le disque approvisionné convenablement, un pod peut être créé avec un accès au disque. Le manifeste suivant crée un pod qui utilise la revendication de volume persistant `azure-managed-disk` pour monter le disque Azure sur le chemin d’accès `/var/www/html`. 

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
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les volumes persistants Kubernetes utilisant des disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md