---
title: "Créer une règle d’autorisation Service Bus à l’aide d’un modèle Azure Resource Manager | Microsoft Docs"
description: "Créer une règle d’autorisation Service Bus pour l’espace de noms et la file d’attente à l’aide d’un modèle Azure Resource Manager"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/10/2017
ms.author: sethm;shvija
ms.openlocfilehash: 384a2fce4bf338ffc4ab6690980c12ad7ff34a6e
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Créer une règle d’autorisation Service Bus pour l’espace de noms et la file d’attente à l’aide d’un modèle Azure Resource Manager

Cet article montre comment utiliser un modèle Azure Resource Manager qui crée une [règle d’autorisation](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) pour un espace de noms Service Bus et une file d’attente. L’article explique comment spécifier les ressources à déployer et définir les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates].

Pour obtenir le modèle complet, consultez le [modèle de règle d’autorisation d’espace de noms Service Bus][Service Bus auth rule template] sur GitHub.

> [!NOTE]
> Les modèles Azure Resource Manager suivants sont disponibles au téléchargement et au déploiement.
> 
> * [Création d'un espace de noms Service Bus](service-bus-resource-manager-namespace.md)
> * [Créer un espace de noms Service Bus avec file d’attente](service-bus-resource-manager-namespace-queue.md)
> * [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-resource-manager-namespace-topic.md)
> * [Créer un modèle d’espace de noms Service Bus avec rubrique, abonnement et règle](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Pour rechercher les derniers modèles, recherchez **Service Bus** dans la galerie de [modèles de démarrage rapide Azure][Azure Quickstart Templates].
> 
> 

## <a name="what-will-you-deploy"></a>Qu'allez-vous déployer ?

Avec ce modèle, vous déployez une règle d’autorisation Service Bus pour un espace de noms et une entité de messagerie (une file d’attente, dans le cas présent).

Ce modèle utilise la [signature d’accès partagé (SAP)](service-bus-sas.md) pour l’authentification. SAP permet aux applications de s’authentifier auprès de Service Bus à l’aide d’une clé d’accès sur l’espace de noms, ou sur l’entité de messagerie (file d’attente ou rubrique) avec des droits spécifiques associés. Vous pouvez ensuite utiliser cette clé pour générer un jeton SAS que les clients peuvent ensuite utiliser pour s’authentifier auprès de Service Bus.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déployer sur Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Le modèle définit les paramètres suivants.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nom de l’espace de noms Service Bus à créer.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
Nom de la règle d'autorisation pour l'espace de noms.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Nom de la file d’attente dans l’espace de noms Service Bus.

```json
"serviceBusQueueName": {
"type": "string"
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
Crée un espace de noms Service Bus standard de type **Messagerie**et une règle d'autorisation Service Bus pour l'espace de noms et l'entité.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Interface de ligne de commande Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé et déployé des ressources à l’aide d’Azure Resource Manager, découvrez comment gérer ces ressources en consultant les articles suivants :

* [Gestion de Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
* [Gérer les ressources Service Bus avec l'explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Authentification et de autorisation Service Bus](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
