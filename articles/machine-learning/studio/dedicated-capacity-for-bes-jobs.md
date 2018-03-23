---
title: Capacité dédiée pour les travaux du service d’exécution de lot de Machine Learning | Microsoft Docs
description: Vue d’ensemble du service Azure Batch pour les travaux Machine Learning.
services: machine-learning
documentationcenter: ''
author: serinakaye
ms.author: serinak
manager: mwinkle
editor: ''
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 51ae9d72906799e0e4f2f7d8a8a228cd0df0d91a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Service Azure Batch pour les travaux Machine Learning

Le traitement par pool Batch de Machine Learning utilise une échelle gérée par le client pour le service d’exécution de lot d’Azure Machine Learning. Le traitement par lots classique pour Machine Learning a lieu dans un environnement multi-clients, qui limite le nombre de travaux simultanés que vous pouvez soumettre. Les travaux sont mis en file d’attente d’après le principe premier entré, premier sorti. Cette incertitude signifie que vous ne pouvez pas prédire précisément à quel moment votre travail sera exécuté.

Le traitement par pool Batch vous permet de créer des pools dans lesquels vous pouvez soumettre des programmes de traitement par lots. Vous contrôlez la taille du pool ainsi que le pool auquel le travail est soumis. Le travail du service d’exécution de lot s’exécute dans son propre espace de traitement fournissant ainsi des performances de traitement prévisibles et la possibilité de créer des pools de ressources qui correspondent à la charge de traitement que vous soumettez.

## <a name="how-to-use-batch-pool-processing"></a>Comment utiliser le traitement par pool Batch

La configuration du traitement par pool Batch n’est pas disponible actuellement via le portail Azure. Pour utiliser le traitement par pool Batch, vous devez :

-   appeler le service CSS pour créer un compte de pool Batch et obtenir une URL de service du pool et une clé d’autorisation ;
-   créer un service web et un plan de facturation basés sur un nouveau Resource Manager.

Pour créer votre compte, appelez le Support technique et Service clientèle Microsoft (CSS) et indiquez votre ID d’abonnement. CSS collabore avec vous pour déterminer la capacité appropriée à votre situation. CSS configure ensuite votre compte avec le nombre maximal de pools que vous pouvez créer et le nombre maximal de machines virtuelles que vous pouvez placer dans chaque pool. Une fois votre compte configuré, vous recevez l’URL de service du pool et une clé d’autorisation.

Une fois votre compte créé, vous utilisez l’URL de service du pool et une clé d’autorisation pour effectuer des opérations de gestion de pool sur votre pool Batch.

![Architecture de service du pool Batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Créez des pools en appelant l’opération Créer un pool dans l’URL de service du pool qui vous a été fournie par CSS. Lorsque vous créez un pool, spécifiez le nombre de machines virtuelles et l’URL du fichier swagger.json d’un service web Machine Learning basé sur un nouveau Resource Manager. Ce service web est fourni pour établir l’association de facturation. Le service du pool Batch utilise le fichier swagger.json pour associer le pool à un plan de facturation. Vous pouvez exécuter n’importe quel service web du service d’exécution de lot, classique ou basé sur le nouveau Resource Manager, que vous choisissez dans le pool.

Vous pouvez utiliser n’importe quel service web basé sur le nouveau Resource Manager, mais sachez que la facturation correspondant aux travaux est établie par rapport au plan de facturation associé à ce service. Vous pouvez créer un service web et un plan de facturation spécialement pour l’exécution des travaux du pool Batch.

Pour plus d’informations sur la création de services web, consultez [Déploiement d’un service web Azure Machine Learning](publish-a-machine-learning-web-service.md).

Une fois que vous avez créé un pool, soumettez le travail du service d’exécution de lot à l’aide de l’URL des requêtes de lots du service web. Vous pouvez choisir de le soumettre à un traitement par lots classique ou par pool. Pour soumettre un travail au traitement par pool Batch, ajoutez le paramètre suivant dans le corps de la demande de soumission de travail :

« AzureBatchPoolId »:« &lt;ID du pool&gt; »

Si vous n’ajoutez pas le paramètre, le travail est exécuté dans l’environnement du processus classique de traitement par lots. Si le pool possède des ressources disponibles, le travail démarre immédiatement. S’il n’en possède pas, votre travail est mis en file d’attente jusqu’à ce qu’une ressource soit disponible.

Si vous estimez que vous atteignez régulièrement la limite de la capacité de vos pools et que vous devez l’augmenter, vous pouvez appeler CSS et demander à un représentant d’accroître vos quotas.

Exemple de demande :

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Considérations relatives à l’utilisation du traitement par pool Batch

Le traitement par pool Batch est un service toujours facturable qui vous oblige à l’associer à un plan de facturation basé sur un Resource Manager. Vous êtes facturé uniquement pour le nombre d’heures de calcul d’exécution du pool, quel que soit le nombre de travaux exécutés au cours de ce temps. Si vous créez un pool, vous êtes facturé pour les heures de calcul de chaque machine virtuelle du pool jusqu’à ce qu’il soit supprimé, même si aucun travail de traitement par lots ne s’exécute dans le pool. La facturation des machines virtuelles démarre lorsque leur approvisionnement est terminé et s’arrête lorsqu’elles ont été supprimées. Vous pouvez utiliser l’un des plans indiqués dans la [Machine Learning Tarification](https://azure.microsoft.com/pricing/details/machine-learning/).

Exemple de facturation :

Si vous créez un pool Batch comprenant 2 machines virtuelles et le supprimez après 24 heures, votre plan de facturation est débité de 48 heures de calcul quel que soit le nombre de travaux exécutés pendant cette période.

Si vous créez un pool Batch comportant 4 machines virtuelles et le supprimez après 12 heures, votre plan de facturation est également débité de 48 heures de calcul.

Nous vous recommandons d’interroger l’état des travaux pour déterminer leur achèvement. Lorsque l’exécution de tous vos travaux est terminée, appelez l’opération de redimensionnement du pool pour définir le nombre de machines virtuelles du pool sur zéro. Si vous manquez de ressources de pool et que vous devez créer un pool, par exemple pour facturer sur un autre plan de facturation, il est préférable de supprimer le pool lorsque l’exécution de tous les travaux est terminée.


| **Utilisez le traitement par pool Batch si**    | **Utilisez le traitement par lots classique si**  |
|---|---|
|Vous devez exécuter un grand nombre de travaux<br>Ou<br/>Vos travaux doivent s’exécuter immédiatement<br/>Ou<br/>Vous avez besoin d’un débit garanti. Par exemple, vous devez exécuter plusieurs travaux dans un laps de temps donné et souhaitez augmenter la taille des instances de vos ressources de calcul pour satisfaire à vos besoins.    | Vous exécutez quelques travaux<br/>and<br/> Vous n’avez pas besoin que les travaux s’exécutent immédiatement |
