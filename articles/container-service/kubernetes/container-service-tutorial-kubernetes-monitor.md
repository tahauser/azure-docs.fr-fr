---
title: "Didacticiel Azure Container Service – Surveiller Kubernetes"
description: "Didacticiel Azure Container Service - Surveiller Kubernetes à l’aide de Microsoft Operations Management Suite (OMS)"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 965ce4b7e154684fc1d171c90f17498afc828a66
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Surveiller un cluster Kubernetes à l’aide d’Operations Management Suite

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

La surveillance de votre cluster Kubernetes et des conteneurs est cruciale, particulièrement lorsque vous gérez un cluster de production à grande échelle avec plusieurs applications. 

Vous pouvez tirer parti de plusieurs solutions de surveillance Kubernetes, à partir de Microsoft ou d’autres fournisseurs. Dans ce didacticiel, vous surveillez votre cluster Kubernetes à l’aide de la solution Containers dans [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), la solution de gestion informatique en nuage de Microsoft. (La solution OMS Containers est en préversion.)

Dans ce didacticiel (le dernier d’une série de sept), les tâches suivantes sont abordées :

> [!div class="checklist"]
> * Obtenir les paramètres de l’espace de travail OMS
> * Configurer les agents OMS sur les nœuds Kubernetes
> * Accéder aux informations de surveillance dans le portail OMS ou le portail Azure

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans des images conteneur, ces images chargées sur Azure Container Registry et un cluster Kubernetes créé. 

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Obtenir les paramètres de l’espace de travail

Lorsque vous pouvez accéder au [portail OMS](https://mms.microsoft.com), allez dans **Paramètres** > **Sources connectées** > **Serveurs Linux**. Là, vous trouvez l’*ID de l’espace de travail* et une *Clé de l’espace de travail* principal ou secondaire. Prenez en note ces valeurs, elles vous sont nécessaires pour configurer les agents OMS sur le cluster.

## <a name="set-up-oms-agents"></a>Configurer les agents OMS

Voici un fichier YAML pour configurer les agents OMS sur les nœuds de cluster Linux. Il crée un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes qui exécute un seul et même pod sur chaque nœud de cluster. La ressource DaemonSet s’avère idéale pour le déploiement d’un agent de surveillance. 

Enregistrez le texte suivant dans un fichier nommé `oms-daemonset.yaml` et remplacez les valeurs d’espace réservé de *myWorkspaceID* et de *myWorkspaceKey* par votre ID et clé d’espace de travail OMS. (En production, vous pouvez encoder ces valeurs en tant que secrets).

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

Créer le DaemonSet à l’aide de la commande suivante :

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Pour vérifier que le DaemonSet est créé, exécutez :

```azurecli-interactive
kubectl get daemonset
```

Le résultat ressemble à ce qui suit :

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Dès lors que les agents sont en cours d’exécution, quelques minutes sont nécessaires à OMS pour ingérer et traiter les données.

## <a name="access-monitoring-data"></a>Accéder aux données de surveillance

Affichez et analysez le conteneur OMS surveillant les données au moyen de la [solution Containers](../../log-analytics/log-analytics-containers.md), dans le portail OMS ou le portail Azure. 

Pour installer la solution Containers à l’aide du [portail OMS](https://mms.microsoft.com), accédez à **Galerie de solutions**. Ensuite, ajoutez **Solution Containers**. Vous pouvez également ajouter la solution Containers à partir d’[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Dans le portail OMS, recherchez une vignette récapitulative **Containers** sur le tableau de bord OMS. Cliquez sur la vignette pour obtenir plus d’informations, notamment sur les événements de conteneur, les erreurs, l’état, l’inventaire des images et l’utilisation du processeur et de la mémoire. Pour obtenir des informations plus précises, cliquez sur une ligne dans une vignette, ou effectuez une [recherche dans les journaux](../../log-analytics/log-analytics-log-searches.md).

![Tableau de bord Containers dans le portail OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

De même, dans le portail Azure, accédez à **Log Analytics** et sélectionnez le nom de votre espace de travail. Pour voir la vignette récapitulative **Containers**, cliquez sur **Solutions** > **Containers**. Pour obtenir plus de détails, cliquez sur la vignette.

Consultez la [documentation Azure Log Analytics](../../log-analytics/index.yml) pour obtenir des instructions détaillées sur l’interrogation et l’analyse des données de surveillance.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous surveillez votre cluster Kubernetes avec OMS. Les tâches traitées ont inclus :

> [!div class="checklist"]
> * Obtenir les paramètres de l’espace de travail OMS
> * Configurer les agents OMS sur les nœuds Kubernetes
> * Accéder aux informations de surveillance dans le portail OMS ou le portail Azure


Suivez ce lien pour consulter des exemples de scripts prédéfinis pour Container Service.

> [!div class="nextstepaction"]
> [Exemples de scripts Azure Container Service](cli-samples.md)
