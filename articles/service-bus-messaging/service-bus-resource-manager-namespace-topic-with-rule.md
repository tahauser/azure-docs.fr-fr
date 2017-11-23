---
title: "Créer un abonnement Azure Service Bus ainsi qu’une règle à l’aide d’un modèle Azure Resource Manager | Microsoft Docs"
description: "Créer un espace de noms Service Bus avec rubrique, abonnement et règle à l’aide d’un modèle Azure Resource Manager"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/10/2017
ms.author: sethm;shvija
ms.openlocfilehash: 976c7b425dd17f8ed38f18b6ffa50b4368ab44b3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Créer un espace de noms Service Bus avec rubrique, abonnement et règle à l’aide d’un modèle Azure Resource Manager

Cet article montre comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Service Bus avec une rubrique, un abonnement et une règle (filtre). L’article explique comment spécifier les ressources à déployer et définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour plus d’informations sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates].

Pour plus d’informations sur les pratiques et les modèles des conventions d’affectation de noms des ressources Azure, consultez [Conventions d’affectation de noms recommandées pour les ressources Azure][Recommended naming conventions for Azure resources].

Pour obtenir le modèle complet, consultez le [modèle d’espace de noms Service Bus avec rubrique, abonnement et règle][Service Bus namespace with topic, subscription, and rule].

> [!NOTE]
> Les modèles Azure Resource Manager suivants sont disponibles au téléchargement et au déploiement.
> 
> * [Créer un espace de noms Service Bus avec file d'attente et règle d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
> * [Créer un espace de noms Service Bus avec file d’attente](service-bus-resource-manager-namespace-queue.md)
> * [Création d'un espace de noms Service Bus](service-bus-resource-manager-namespace.md)
> * [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-resource-manager-namespace-topic.md)
> 
> Pour rechercher les derniers modèles, recherchez « Service Bus » dans la galerie [Modèles de démarrage rapide Azure][Azure Quickstart Templates].
> 
> 

## <a name="what-will-you-deploy"></a>Qu'allez-vous déployer ?

Avec ce modèle, vous déployez un espace de noms Service Bus avec rubrique, abonnement et règle (filtre).

Les [rubriques et les abonnements Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fournissent une forme de communication de type un-à-plusieurs dans un modèle de *publication/abonnement*. Lorsque vous utilisez des rubriques et des abonnements, les composants d’une application distribuée ne communiquent pas directement les uns avec les autres : ils échangent des messages par le biais d’une rubrique qui fait office d’intermédiaire. Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Une filtre sur un abonnement vous permet de spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

## <a name="what-are-rules-filters"></a>Qu’est-ce qu’une règle (filtre) ?

Dans de nombreux scénarios, les messages ayant des caractéristiques spécifiques doivent être traités différemment. Pour activer ce traitement personnalisé, vous pouvez configurer des abonnements pour rechercher les messages présentant les propriétés spécifiques, puis apporter des modifications à ces propriétés. Bien que les abonnements Service Bus voient tous les messages envoyés à la rubrique, vous pouvez uniquement copier un sous-ensemble de ces messages dans la file d’attente d’abonnement virtuelle. Pour ce faire, il faut utiliser des filtres d’abonnement. Pour en savoir plus sur les règles (filtres), consultez [Règles et actions](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec Azure Resource Manager, vous devez définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs des paramètres. Vous devez définir un paramètre pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent inchangées. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Le modèle définit les paramètres suivants :

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nom de l’espace de noms Service Bus à créer.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Nom de la rubrique créée dans l’espace de noms Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Nom de l'abonnement créé dans l'espace de noms Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Nom de la règle (filtre) créée dans l’espace de noms Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
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
Crée un espace de noms Service Bus standard de type **Messagerie**, avec rubrique, abonnement et règles.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Interface de ligne de commande Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé et déployé des ressources à l’aide d’Azure Resource Manager, découvrez comment gérer ces ressources en consultant les articles suivants :

* [Gérer Azure Service Bus](service-bus-management-libraries.md)
* [Gestion de Service Bus avec PowerShell](service-bus-manage-with-ps.md)
* [Gérer les ressources Service Bus avec l'explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

