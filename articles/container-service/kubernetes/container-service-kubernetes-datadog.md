---
title: Surveiller le cluster Azure Kubernetes avec Datadog
description: "Surveillance du cluster Kubernetes dans Azure Container Service à l’aide de Datadog"
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 96bbd88f163939b58263f2f3a94b7b4d90f85736
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Surveiller un cluster Azure Container Service avec Datadog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Composants requis
Cette procédure pas à pas suppose que vous avez [créé un cluster Kubernetes à l’aide d’Azure Container Service](container-service-kubernetes-walkthrough.md).

Elle suppose également que vous avez installé l’outil `az` de l’interface Azure CLI et l’outil `kubectl`.

Vous pouvez tester si l’outil `az` est installé en exécutant :

```console
$ az --version
```

Si l’outil `az` n’est pas installé, suivez les instructions figurant [ici](https://github.com/azure/azure-cli#installation).

Vous pouvez tester si l’outil `kubectl` est installé en exécutant :

```console
$ kubectl version
```

Si `kubectl` n’est pas installé, vous pouvez exécuter :

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog est un service de surveillance qui regroupe les données de surveillance provenant de vos conteneurs dans votre cluster Azure Container Service. Datadog intègre un tableau de bord Docker Integration qui affiche des mesures spécifiques dans vos conteneurs. Les mesures recueillies à partir de vos conteneurs sont classées par processeur, mémoire, réseau et E/S. Datadog fractionne les mesures en conteneurs et images.

Vous devez d’abord [créer un compte](https://www.datadoghq.com/lpg/).

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installation de l’Agent Datadog avec un DaemonSet
Les DaemonSets sont utilisés par DaemonSet pour exécuter une instance unique d’un conteneur sur chaque hôte du cluster.
Ils sont parfaits pour exécuter des agents de surveillance.

Lorsque vous êtes connecté à Datadog, suivez les [instructions de Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) pour installer des agents Datadog sur votre cluster à l’aide d’un DaemonSet.

## <a name="conclusion"></a>Conclusion
Et voilà ! Dès que les agents sont en cours d’exécution, des données s’affichent dans la console après quelques minutes. Vous pouvez consulter le [tableau de bord intégré de Kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) pour obtenir une synthèse de votre cluster.
