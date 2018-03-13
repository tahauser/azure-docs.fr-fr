---
title: "Exemple de déploiement de script Azure Service Fabric CLI"
description: "Créez un cluster Service Fabric Linux sécurisé dans Azure à partir de l’interface CLI Azure Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d49383b4f7b3b13beb9ea36ae725938e17ef1456
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Création d’un cluster Service Fabric Linux sécurisé dans Azure

Cette commande crée un certificat auto-signé, l’ajoute à un coffre de clés et télécharge le certificat localement.  Le nouveau certificat est utilisé pour sécuriser le cluster lorsqu’il se déploie.  Vous pouvez également utiliser un certificat existant au lieu d’en créer un nouveau.  Dans les deux cas, le nom de sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture d’un certificat SSL pour les points de terminaison de gestion HTTPS du cluster et pour Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine `.cloudapp.azure.com`. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Si nécessaire, installez l’interface de ligne de commande Azure 2.0 ([Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, le cluster et toutes les ressources associées.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) | Crée un cluster Service Fabric.  |

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).
