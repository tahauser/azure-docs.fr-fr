---
title: "Créer un espace de noms Service Bus Messaging à l’aide d’un modèle Azure Resource Manager | Microsoft Docs"
description: "Utiliser un modèle Azure Resource Manager pour créer un espace de noms Service Bus Messaging"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm;shvija
ms.openlocfilehash: f184cc9418e4af95423c0ede65bca312dfca7393
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Créer un espace de noms Service Bus à l’aide d’un modèle Azure Resource Manager

Cet article explique comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Service Bus de type **Messagerie** avec une référence (SKU) Standard. L'article définit également les paramètres qui sont spécifiés pour l'exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates].

Pour le modèle complet, consultez le [modèle d’espace de noms Service Bus][Service Bus namespace template] sur GitHub.

> [!NOTE]
> Les modèles Azure Resource Manager suivants sont disponibles au téléchargement et au déploiement. 
> 
> * [Créer un espace de noms Service Bus avec file d’attente](service-bus-resource-manager-namespace-queue.md)
> * [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-resource-manager-namespace-topic.md)
> * [Créer un espace de noms Service Bus avec file d'attente et règle d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
> * [Créer un modèle d’espace de noms Service Bus avec rubrique, abonnement et règle](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Pour rechercher les derniers modèles, recherchez « Service Bus » dans la galerie de [modèles de démarrage rapide Azure][Azure Quickstart Templates].
> 
> 

## <a name="what-will-you-deploy"></a>Qu'allez-vous déployer ?

Avec ce modèle, vous déployez un espace de noms Service Bus avec une référence (SKU) [Standard ou Premium](https://azure.microsoft.com/pricing/details/service-bus/).

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déployer sur Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>parameters

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent inchangées. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Ce modèle définit les paramètres suivants :

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nom de l’espace de noms Service Bus à créer.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

Nom de la référence [SKU](https://azure.microsoft.com/pricing/details/service-bus/) Service Bus à créer.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Le modèle définit les valeurs autorisées pour ce paramètre (Standard ou Premium). Si aucune valeur n’est spécifiée, Resource Manager affecte une valeur par défaut (Standard).

Pour plus d’informations sur la tarification de Service Bus, consultez [Service Bus pricing and billing (Tarification et facturation de Service Bus)][Service Bus pricing and billing].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La version de l’API Service Bus du modèle.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Ressources à déployer

### <a name="service-bus-namespace"></a>Espace de noms Service Bus

Crée un espace de noms Service Bus standard de type **Messagerie**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Commandes pour l’exécution du déploiement

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé et déployé des ressources à l’aide d’Azure Resource Manager, découvrez comment gérer ces ressources en lisant les articles suivants :

* [Gestion de Service Bus avec PowerShell](service-bus-manage-with-ps.md)
* [Gérer les ressources Service Bus avec l'explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
