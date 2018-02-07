---
title: Azure Service Fabric - Configuration de la surveillance avec OMS Log Analytics | Microsoft Docs
description: "Découvrez comment configurer OMS pour visualiser et analyser les événements dans le cadre de la surveillance de vos clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Configurer OMS Log Analytics pour un cluster

Vous pouvez configurer un espace de travail OMS à partir d’Azure Resource Manager, de PowerShell ou de la Place de marché Azure. Si vous gérez un modèle Resource Manager mis à jour de votre déploiement, utilisez le même modèle pour configurer votre environnement OMS pour plus tard. Le déploiement via la Place de marché est plus facile si vous avez déjà déployé un cluster et activé les diagnostics. Si vous ne disposez pas d’un accès de niveau abonnement pour le compte sur lequel vous déployez OMS, utilisez PowerShell ou effectuez le déploiement via le modèle Resource Manager.

> [!NOTE]
> Vous devez activer les diagnostics dans votre cluster pour voir les événements au niveau du cluster ou de la plateforme et pour configurer OMS de manière à surveiller correctement votre cluster.

## <a name="deploying-oms-using-azure-marketplace"></a>Déploiement d’OMS à l’aide de la Place de marché Azure

Si vous préférez ajouter un espace de travail OMS après avoir déployé un cluster, accédez à la Place de marché Azure (dans le portail), puis recherchez *« Service Fabric Analytics »*.

1. Cliquez sur **Nouveau** dans le menu de navigation gauche. 

2. Recherchez *Service Fabric Analytics*. Cliquez sur la ressource qui s’affiche.

3. Cliquez sur **Créer**

    ![SF Analytics OMS dans la Place de marché](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Dans la fenêtre de création Service Fabric Analytics, cliquez sur **Sélectionner un espace de travail** pour le champ *Espace de travail OMS*, puis sur **Créer un espace de travail**. Renseignez les entrées obligatoires. Ici, la seule exigence est que l’abonnement pour le cluster Service Fabric et celui pour l’espace de travail OMS soient identiques. Une fois vos entrées validées, le déploiement de votre espace de travail OMS commence. Ce processus doit prendre quelques minutes seulement.

5. Une fois le déploiement terminé, cliquez une nouvelle fois sur **Créer** au bas de la fenêtre de création Service Fabric Analytics. Vérifiez que le nouvel espace de travail s’affiche sous *Espace de travail OMS*. Cette opération ajoute la solution à l’espace de travail que vous venez de créer.

Si vous utilisez Windows, passez aux étapes suivantes pour raccorder OMS au compte de stockage où sont stockés vos événements de cluster. Cette expérience est toujours en cours de développement pour les clusters Linux. En attendant, continuez à ajouter l’agent OMS à votre cluster.  

1. L’espace de travail doit néanmoins être connecté aux données de diagnostic provenant de votre cluster. Accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics. Vous devez voir s’afficher *ServiceFabric(\<nomEspacedetravailOMS\>)*. Cliquez sur la solution pour accéder à sa page de présentation d’où vous pouvez modifier les paramètres de la solution et les paramètres de l’espace de travail ainsi qu’accéder au portail OMS.

2. Dans le menu de navigation gauche, cliquez sur **Journaux de comptes de stockage**, sous *Sources de données de l’espace de travail*.

3. Dans la page *Journaux de compte de stockage*, cliquez sur **Ajouter** en haut pour ajouter les journaux de votre cluster à l’espace de travail.

4. Cliquez dans **Compte de stockage** pour ajouter le compte approprié créé dans votre cluster. Si vous avez utilisé le nom par défaut, le compte de stockage est nommé *sfdg\<nomGroupeRessources\>*. Vous pouvez également le confirmer en examinant le modèle Azure Resource Manager utilisé pour déployer votre cluster, en vérifiant la valeur utilisée pour `applicationDiagnosticsStorageAccountName`. Vous devrez peut-être également faire défiler vers le bas et cliquer sur **Charger plus** si le nom n’apparaît pas. Cliquez sur le nom de compte de stockage approprié pour le sélectionner.

5. Ensuite, vous devrez spécifier le *Type de données*, qui doit être défini sur **Événements de Service Fabric**.

6. La *Source* doit avoir automatiquement pour valeur *WADServiceFabric\*EventTable*.

7. Cliquez sur **OK** pour connecter votre espace de travail aux journaux de votre cluster.

    ![Ajouter des journaux de compte de stockage à OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Le compte doit maintenant apparaître dans le cadre des *journaux de compte de stockage* dans les sources de données de votre espace de travail.

Vous avez ainsi maintenant ajouté la solution Service Fabric Analytics dans un espace de travail OMS Log Analytics qui est à présent correctement connecté à la plateforme de votre cluster et la table du journal des applications. Vous pouvez ajouter des sources supplémentaires à l’espace de travail de la même façon.


## <a name="deploying-oms-using-a-resource-manager-template"></a>Déploiement d’OMS à l’aide du modèle Resource Manager

Lors du déploiement d’un cluster à l’aide d’un modèle Resource Manager, le modèle doit également créer un espace de travail OMS, lui ajouter la solution Service Fabric et le configurer pour lire des données provenant des tables de stockage appropriées.

Vous trouverez [ici](https://azure.microsoft.com/resources/templates/service-fabric-oms/) un exemple de modèle que vous pouvez utiliser et modifier selon vos besoins. Pour obtenir des modèles proposant plusieurs options de configuration pour les espaces de travail OMS, consultez [Modèles Service Fabric et OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Les principales modifications à apporter sont les suivantes :

1. Ajoutez `omsWorkspaceName` et `omsRegion` à vos paramètres. Pour cela, vous devez ajouter l’extrait de code suivant aux paramètres définis dans le fichier *template.json*. N’hésitez pas à modifier les valeurs par défaut selon vos besoins. Vous devez également ajouter les deux nouveaux paramètres du fichier *parameters.json* pour définir leurs valeurs lors du déploiement de ressources :
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    Les valeurs `omsRegion` doivent se conformer à un ensemble spécifique de valeurs. Vous devez choisir celle qui est la plus proche du déploiement de votre cluster.

2. Si vous prévoyez d’envoyer les journaux des applications à OMS, vérifiez que `applicationDiagnosticsStorageAccountType` et `applicationDiagnosticsStorageAccountName` sont inclus en tant que paramètres dans votre modèle. Si ce n’est pas le cas, ajoutez-les à la section des variables de la façon suivante, puis modifiez leurs valeurs selon vos besoins. Vous pouvez également les inclure en tant que paramètres, en respectant le format ci-dessus.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Ajoutez la solution Service Fabric OMS aux variables de votre modèle :

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Ajoutez le code suivant à la fin de la section des ressources, après la déclaration de la ressource de cluster Service Fabric.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Si vous avez ajouté `applicationDiagnosticsStorageAccountName` en tant que variable, veillez à remplacer chaque référence à celui-ci par `variables('applicationDiagnosticsStorageAccountName')` au lieu de `parameters('applicationDiagnosticsStorageAccountName')`.

5. Déployez le modèle comme une mise à niveau Resource Manager sur votre cluster. Pour cela, utilisez l’API `New-AzureRmResourceGroupDeployment` dans le module AzureRM PowerShell. Voici un exemple de commande :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager va détecter qu’il s’agit d’une mise à jour d’une ressource existante. Il va uniquement traiter les modifications qui existent entre le modèle du déploiement existant et le nouveau modèle fourni.

## <a name="deploying-oms-using-azure-powershell"></a>Déploiement d’OMS à l’aide d’Azure PowerShell

Vous pouvez également déployer votre ressource OMS Log Analytics via PowerShell. Pour cela, utilisez la commande `New-AzureRmOperationalInsightsWorkspace`. Pour pouvoir effectuer cette opération, vérifiez que vous avez installé [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Utilisez ce script pour créer un espace de travail OMS Log Analytics et lui ajouter la solution Service Fabric : 

```ps

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Une fois cette opération effectuée, si votre cluster est un cluster Windows, suivez les étapes décrites dans la section ci-dessus pour raccorder OMS Log Analytics au compte de stockage approprié.

Vous pouvez également ajouter d’autres solutions ou apporter d’autres modifications à votre espace de travail OMS à l’aide de PowerShell. Pour en savoir plus à ce sujet, consultez [Gérer Log Analytics à l’aide de PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>Étapes suivantes
* [Déployez l’agent OMS](service-fabric-diagnostics-oms-agent.md) sur vos nœuds pour collecter les compteurs de performances, ainsi que les statistiques et les journaux Docker de vos conteneurs.
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* [Utiliser le Concepteur de vues pour créer des vues personnalisées dans Log Analytics](../log-analytics/log-analytics-view-designer.md)
