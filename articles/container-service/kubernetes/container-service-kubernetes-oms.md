---
title: Surveiller le cluster Kubernetes Azure - Gestion des opérations
description: Analyse du cluster Kubernetes dans Azure Container Service à l’aide de Log Analytics
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: efe4b3a1a63fa1986682a2fdde1a20221dc5d93a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Analyser un cluster Azure Container Service avec Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Prérequis

Cette procédure pas à pas suppose que vous avez [créé un cluster Kubernetes à l’aide d’Azure Container Service](container-service-kubernetes-walkthrough.md).

Elle suppose également que vous avez installé l’outil `az` de l’interface Azure CLI et l’outil `kubectl`.

Vous pouvez tester si l’outil `az` est installé en exécutant :

```console
$ az --version
```

Si l’outil `az` n’est pas installé, suivez les instructions figurant [ici](https://github.com/azure/azure-cli#installation).  
Sinon, vous pouvez utiliser [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), avec `az`l’interface de ligne Azure et les `kubectl` outils déjà installés pour vous.  

Vous pouvez tester si l’outil `kubectl` est installé en exécutant :

```console
$ kubectl version
```

Si `kubectl` n’est pas installé, vous pouvez exécuter :
```console
$ az acs kubernetes install-cli
```

Pour tester si les clés kubernetes sont installées dans votre outil kubectl vous pouvez exécuter :
```console
$ kubectl get nodes
```

Si les erreurs de commande ci-dessus sortent, vous devez installer les clés de cluster kubernetes dans votre outil kubectl. Ceci peut se faire avec la commande suivante :
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Analyser des Containers avec Log Analytics

Log Analytics est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et dans le cloud. La solution de conteneurs fait partie intégrante de Log Analytics, qui vous octroie une visibilité sur le stock, les performances et les fichiers journaux des conteneurs à un emplacement unique. Vous pouvez auditer les conteneurs, résoudre les problèmes en consultant les fichiers journaux de manière centralisée, et identifier les conteneurs bruyants à consommation supérieure sur un hôte.

![](media/container-service-monitoring-oms/image1.png)

Pour plus d’informations sur la solution Conteneurs, reportez-vous à [Solution Conteneurs (version préliminaire) Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installation de Log Analytics sur Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obtenir l’ID et la clé d’espace de travail
Pour que l’agent OMS puisse communiquer avec le service, il doit être configuré avec un ID et une clé d’espace de travail. Pour obtenir l’ID et la clé de l’espace de travail, vous devez créer un compte sur <https://mms.microsoft.com>.
Suivez la procédure de création de compte. Une fois que vous avez créé le compte, vous devez obtenir votre ID et votre clé d’espace de travail en cliquant sur **Paramètres**, **Sources connectées**, puis sur **Serveurs Linux**, comme indiqué ci-dessous.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Installer l’agent OMS à l’aide d’un DaemonSet
Les DaemonSets sont utilisés par DaemonSet pour exécuter une instance unique d’un conteneur sur chaque hôte du cluster.
Ils sont parfaits pour exécuter des agents de surveillance.

Voici le [fichier DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Enregistrez-le sous le nom `oms-daemonset.yaml` et remplacez dans ce fichier les valeurs des espaces réservés pour `WSID` et `KEY` par l’ID et la clé de votre espace de travail.

Une fois vos ID et clé d’espace de travail ajoutés à la configuration de DaemonSet, vous pouvez installer l’agent OMS sur votre cluster à l’aide de l’outil de ligne de commande `kubectl` :

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Installation de l’agent OMS à l’aide d’une clé secrète Kubernetes
Pour protéger votre ID et la clé de votre espace de travail Log Analytics, vous pouvez utiliser le secret Kubernetes dans le cadre du fichier YAML DaemonSet.

 - Copiez le script, le fichier modèle de la clé secrète et le fichier YAML DaemonSet (dans le [référentiel](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) et assurez-vous qu’ils se trouvent dans le même répertoire. 
      - clé de secret générant le script - secret-gen.sh
      - modèle de clé de secret - secret-template.yaml
   - Fichier DaemonSet YAML - omsagent-ds-secrets.yaml
 - Exécutez le script. Le script demande l’ID et la clé primaire de l’espace de travail Log Analytics. Veuillez l’insérer. Le script crée un fichier yaml secret pour que vous puissiez l’exécuter.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Créez le pod de clés secrètes en exécutant la commande suivante : ``` kubectl create -f omsagentsecret.yaml ```
 
   - Pour vérifier, exécutez la commande suivante : 

   ``` 
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes 
   ```
 
  - Créer votre DaemonsSet omsagent en exécutant ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Conclusion
Et voilà ! Après quelques minutes, vous devez être en mesure de voir le flux de données vers votre tableau de bord OMS.
