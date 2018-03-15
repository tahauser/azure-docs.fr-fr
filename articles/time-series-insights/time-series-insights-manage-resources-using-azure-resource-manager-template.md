---
title: "Guide pratique pour gérer un environnement Azure Time Series Insights à l’aide de modèles Azure Resource Manager | Microsoft Docs"
description: "Cet article explique comment gérer un environnement Azure Time Series Insights par programme à l’aide d’Azure Resource Manager."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: b09d4a1aea56a4e306f80a1b43d519d313fd73ab
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Créer des ressources Time Series Insights à l’aide de modèles Azure Resource Manager

Cet article explique comment créer et déployer des ressources Time Series Insights à l’aide de modèles Azure Resource Manager, de PowerShell et du fournisseur de ressources Time Series Insights.

Time Series Insights prend en charge les ressources suivantes :
   | Ressource | Description |
   | --- | --- |
   | Environnement | Un environnement Time Series Insights est un regroupement logique d’événements lus à partir de répartiteurs, stockés et rendus interrogeables. Pour plus d’informations, consultez la page [Planifier un environnement Azure Time Series Insights](time-series-insights-environment-planning.md). |
   | Source de l’événement | Une source d’événement est une connexion à un répartiteur d’événements à partir de laquelle Time Series Insights lit et ingère des événements dans l’environnement. Sont actuellement pris en charge IoT Hub et Event Hub. |
   | Jeu de données de référence | Les jeux de données de référence fournissent des métadonnées sur les événements de l’environnement. Les métadonnées des jeux de données de référence seront jointes à des événements au cours de l’entrée. Les jeux de données de référence sont définis comme des ressources par leurs propriétés de clé d’événement. Les métadonnées qui composent le jeu de données de référence sont chargées ou modifiées par le biais d’API de plan de données. |
   | Stratégie d’accès | Les stratégies d’accès accordent l’autorisation de générer des requêtes de données, de manipuler les données de référence dans l’environnement et de partager des requêtes enregistrées et des perspectives associées à l’environnement. Pour plus d’informations, consultez la page [Accorder l’accès aux données à un environnement Time Series Insights à l’aide du Portail Azure](time-series-insights-data-access.md). |

Un modèle Resource Manager est un fichier JSON qui définit l’infrastructure et la configuration de ressources dans un groupe de ressources. Pour plus d’informations, consultez les documents suivants :

- [Vue d’ensemble d’Azure Resource Manager – Template deployment](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

Le modèle de démarrage rapide [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) est publié sur GitHub. Il crée un environnement Time Series Insights, une source d’événement enfant configurée pour consommer des événements à partir d’un hub d’événements, et des stratégies qui accordent l’accès aux données de l’environnement. Si aucun hub d’événements existant n’est spécifié, il en sera créé un avec le déploiement.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Déployer le modèle de démarrage rapide localement avec PowerShell

La procédure suivante explique comment utiliser PowerShell pour déployer un modèle Azure Resource Manager qui crée un environnement Time Series Insights, une source d’événement enfant configurée pour consommer des événements à partir d’un hub d’événements, et des stratégies qui accordent l’accès aux données de l’environnement. Si aucun hub d’événements existant n’est spécifié, il en sera créé un avec le déploiement.

Le flux de travail est approximativement le suivant :

1. Installez PowerShell.
1. Créez le modèle et un fichier de paramètres.
1. Dans PowerShell, connectez-vous à votre compte Azure.
1. Créez un groupe de ressources s'il n'en existe pas.
1. Testez le déploiement.
1. Déployez le modèle.

### <a name="install-powershell"></a>Installer PowerShell

Installez Azure PowerShell en suivant les instructions disponibles dans [Prise en main d’Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Créer un modèle

Clonez ou copiez le modèle [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) sur GitHub.

### <a name="create-a-parameters-file"></a>Créer un fichier de paramètres

Pour créer un fichier de paramètres, copiez le fichier [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Paramètres obligatoires

   | Paramètre | Description |
   | --- | --- |
   | eventHubNamespaceName | Espace de noms du hub de la source de l’événement. |
   | eventHubName | Nom du hub de la source de l’événement. |
   | consumerGroupName | Nom du groupe de consommation que le service Time Series Insights utilisera pour lire les données à partir du hub d’événements. **REMARQUE :** Pour éviter la contention de ressources, ce groupe de consommation doit être dédié au service Time Series Insights et non partagé avec d’autres lecteurs. |
   | environmentName | Nom de l’environnement. Il ne peut pas inclure : « < », « > », « % », « & », « : », « \\ », « ? », « / », ni aucun caractère de contrôle. Tous les autres caractères sont autorisés.|
   | eventSourceName | Nom de la ressource enfant de la source de l’événement. Il ne peut pas inclure : « < », « > », « % », « & », « : », « \\ », « ? », « / », ni aucun caractère de contrôle. Tous les autres caractères sont autorisés. |

#### <a name="optional-parameters"></a>Paramètres facultatifs

   | Paramètre | Description |
   | --- | --- |
   | existingEventHubResourceId | ID de ressource facultatif d’un hub d’événements existant qui sera connecté à l’environnement Time Series Insights par le biais de la source de l’événement. **REMARQUE :** L’utilisateur qui déploie le modèle doit avoir des privilèges pour réaliser l’opération listkeys sur le hub d’événements. Si aucune valeur n’est transmise, un nouveau hub d’événements sera créé par le modèle. |
   | environmentDisplayName | Nom convivial facultatif à afficher dans les outils et les interfaces utilisateur à la place du nom de l’environnement. |
   | environmentSkuName | Nom du la référence SKU. Pour plus d’informations, consultez la page [Prix de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Capacité unitaire de la référence SKU. Pour plus d’informations, consultez la page [Prix de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Période minimale durant laquelle les événements de l’environnement seront interrogeables. La valeur doit être spécifiée au format ISO 8601, par exemple, « P30D » pour une stratégie de rétention de 30 jours. |
   | eventSourceDisplayName | Nom convivial facultatif à afficher dans les outils et les interfaces utilisateur à la place du nom de la source de l’événement. |
   | eventSourceTimestampPropertyName | Propriété de l’événement qui sera utilisée comme horodateur de la source de l’événement. Si aucune valeur n’est spécifiée pour timestampPropertyName, ou si la valeur Null ou une chaîne vide est spécifiée, l’heure de création de l’événement sera utilisée. |
   | eventSourceKeyName | Nom de la clé d’accès partagé que le service Time Series Insights utilisera pour se connecter au hub d’événements. |
   | accessPolicyReaderObjectIds | Liste des ID objet des utilisateurs et des applications d’Azure AD qui doivent avoir l’accès Lecteur à l’environnement. L’ID objet du principal du service s’obtient en appelant la cmdlet **Get-AzureRMADUser** ou la cmdlet **Get-AzureRMADServicePrincipal**. La création d’une stratégie d’accès n’est pas encore prise en charge pour les groupes Azure AD. |
   | accessPolicyContributorObjectIds | Liste des ID objet des utilisateurs ou des applications d’Azure AD qui doivent avoir l’accès Collaborateur à l’environnement. L’ID objet du principal du service s’obtient en appelant la cmdlet **Get-AzureRMADUser** ou la cmdlet **Get-AzureRMADServicePrincipal**. La création d’une stratégie d’accès n’est pas encore prise en charge pour les groupes Azure AD. |

Par exemple, le fichier de paramètres suivant serait utilisé pour créer un environnement et une source d’événement qui lirait les événements à partir d’un hub d’événements existant. Il crée également deux stratégies qui accordent l’accès Collaborateur à l’environnement.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Pour plus d’informations, consultez l’article [Paramètres](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Se connecter à Azure et définir l’abonnement Azure

À partir d’une invite de commandes PowerShell, exécutez la commande suivante :

```powershell
Login-AzureRmAccount
```

Vous êtes invité à ouvrir une session sur votre compte Azure. Une fois connecté, exécutez la commande suivante pour afficher vos abonnements disponibles :

```powershell
Get-AzureRMSubscription
```

Cette commande renvoie la liste des abonnements Azure disponibles. Choisissez un abonnement pour la session en cours en exécutant la commande suivante. Remplacez `<YourSubscriptionId>` par le GUID de l’abonnement Azure que vous souhaitez utiliser :

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Définir le groupe de ressources

Si vous n’avez pas de groupe de ressources, créez-en un avec la commande **New-AzureRmResourceGroup**. Indiquez le nom du groupe de ressources et l'emplacement que vous souhaitez utiliser. Par exemple : 

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

En cas de réussite, un résumé du nouveau groupe de ressources s’affiche.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>test du déploiement

Validez votre déploiement en exécutant l’applet de commande `Test-AzureRmResourceGroupDeployment`. Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Créer le déploiement

Pour créer le déploiement, exécutez l’applet de commande `New-AzureRmResourceGroupDeployment` et indiquez les paramètres nécessaires quand vous y êtes invité. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du fichier de modèle. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut **Incremental** est utilisée. Pour plus d’informations, consultez [Déploiements incrémentiels et complets](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

La commande suivante vous invite à entrer les cinq paramètres requis dans la fenêtre PowerShell :

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Pour spécifier un fichier de paramètres à la place, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Vous pouvez également utiliser des paramètres inclus lorsque vous exécutez l'applet de commande de déploiement. La commande est la suivante :

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Pour exécuter un déploiement [complet](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments), affectez la valeur **Complet** au paramètre **Mode** :

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Si les ressources sont déployées avec succès, un résumé du déploiement s’affiche dans la fenêtre PowerShell :

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Déployer le modèle de démarrage rapide sur le Portail Azure

La page d’accueil du modèle de démarrage rapide sur GitHub comporte également un bouton **Déployer sur Azure**. Il ouvre une page Déploiement personnalisé sur le Portail Azure. Sur cette page, vous pouvez entrer ou sélectionner des valeurs pour chacun des paramètres des tables [Paramètres requis](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) et [Paramètres facultatifs](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters). Après avoir rempli les paramètres, cliquez sur le bouton **Acheter** pour lancer le déploiement du modèle.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la gestion par programme de ressources Time Series Insights à l’aide d’API REST, consultez la page [Gestion de Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
