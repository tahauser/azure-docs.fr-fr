---
title: Azure Service Fabric - Configurer la surveillance avec OMS Log Analytics | Microsoft Docs
description: Découvrez comment configurer Operations Management Suite pour visualiser et analyser des événements afin de surveiller vos clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 98ac32b011744ce388762322edd538b467f93494
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-operations-management-suite-log-analytics-for-a-cluster"></a>Configurer Operations Management Suite Log Analytics pour un cluster

Vous pouvez configurer un espace de travail Operations Management Suite (OMS) avec Azure Resource Manager, PowerShell ou la Place de marché Azure. Si vous gérez un modèle Resource Manager mis à jour de votre déploiement, utilisez le même modèle pour configurer votre environnement OMS. Le déploiement via la Place de marché est plus facile si vous avez déjà déployé un cluster et activé les diagnostics. Si vous ne disposez pas d’un accès de niveau abonnement pour le compte sur lequel vous déployez OMS, effectuez le déploiement avec PowerShell ou le modèle Resource Manager.

> [!NOTE]
> Pour configurer OMS afin de surveiller votre cluster, vous avez besoin d’activer les diagnostics pour voir les événements au niveau du cluster ou de la plateforme.

## <a name="deploy-oms-by-using-azure-marketplace"></a>Déployer OMS à l’aide de la Place de marché Azure

Si vous voulez ajouter un espace de travail OMS après avoir déployé un cluster, accédez à la Place de marché Azure dans le portail, puis recherchez **Service Fabric Analytics** :

1. Sélectionnez **Nouveau** dans le menu de navigation gauche. 

2. Recherchez **Service Fabric Analytics**. Sélectionnez la ressource qui s’affiche.

3. Sélectionnez **Créer**.

    ![SF Analytics OMS dans la Place de marché](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Dans la fenêtre de création Service Fabric Analytics, sélectionnez **Sélectionner un espace de travail** pour le champ **Espace de travail OMS**, puis **Créer un espace de travail**. Renseignez les entrées nécessaires. Ici, la seule exigence est que l’abonnement pour le cluster Service Fabric et celui pour l’espace de travail OMS soient identiques. Quand vos entrées ont été validées, le déploiement de votre espace de travail OMS commence. Ce déploiement ne prend que quelques minutes.

5. Une fois le déploiement terminé, sélectionnez une nouvelle fois **Créer** au bas de la fenêtre de création Service Fabric Analytics. Vérifiez que le nouvel espace de travail s’affiche sous **Espace de travail OMS**. Cette action ajoute la solution à l’espace de travail que vous avez créé.

Si vous utilisez Windows, passez aux étapes suivantes pour connecter OMS au compte de stockage où sont stockés vos événements de cluster. 

>[!NOTE]
>Cette expérience pour les clusters Linux n’est pas encore disponible. 

### <a name="connect-the-oms-workspace-to-your-cluster"></a>Connecter l’espace de travail OMS à votre cluster 

1. L’espace de travail a besoin d’être connecté aux données de diagnostic provenant de votre cluster. Accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics. Sélectionnez **ServiceFabric\<nomEspaceTravailOMS\>** et accédez à sa page de présentation. À partir de là, vous pouvez modifier les paramètres de la solution, ceux de l’espace de travail et accéder au portail OMS.

2. Dans le menu de navigation gauche, sous **Sources de données de l’espace de travail**, sélectionnez **Journaux de comptes de stockage**.

3. Dans la page **Journaux de comptes de stockage**, sélectionnez **Ajouter** en haut pour ajouter les journaux de votre cluster à l’espace de travail.

4. Sélectionnez **Compte de stockage** pour ajouter le compte approprié créé dans votre cluster. Si vous avez utilisé le nom par défaut, le compte de stockage est nommé **sfdg\<nomGroupeRessources\>**. Vous pouvez également le confirmer avec le modèle Azure Resource Manager utilisé pour déployer votre cluster, en vérifiant la valeur utilisée pour **applicationDiagnosticsStorageAccountName**. Si le nom n’apparaît pas, faites défiler vers le bas et sélectionnez **Charger plus**. Sélectionnez le nom du compte de stockage.

5. Spécifiez le type de données. Affectez-y la valeur **Événements de Service Fabric**.

6. Vérifiez que la source a automatiquement la valeur **WADServiceFabric\*EventTable**.

7. Sélectionnez **OK** pour connecter votre espace de travail aux journaux de votre cluster.

    ![Ajouter des journaux de compte de stockage à OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Le compte apparaît maintenant dans le cadre des journaux de votre compte de stockage dans les sources de données de votre espace de travail.

Vous avez ajouté la solution Service Fabric Analytics dans un espace de travail OMS Log Analytics qui est à présent correctement connecté à la plateforme de votre cluster et la table du journal des applications. Vous pouvez ajouter des sources supplémentaires à l’espace de travail de la même façon.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Déployer OMS à l’aide d’un modèle Resource Manager

Quand vous déployez un cluster à l’aide d’un modèle Resource Manager, le modèle crée un espace de travail OMS, y ajoute la solution Service Fabric et le configure pour lire des données provenant des tables de stockage appropriées.

Vous pouvez utiliser et modifier [cet exemple de modèle](https://azure.microsoft.com/resources/templates/service-fabric-oms/) pour répondre à vos besoins. Pour obtenir des modèles proposant plusieurs options de configuration pour les espaces de travail OMS, consultez [Modèles Service Fabric et OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Apportez les modifications suivantes :
1. Ajoutez `omsWorkspaceName` et `omsRegion` à vos paramètres en ajoutant l’extrait de code suivant aux paramètres définis dans le fichier *template.json*. N’hésitez pas à modifier les valeurs par défaut selon vos besoins. Ajoutez aussi les deux nouveaux paramètres du fichier *parameters.json* pour définir leurs valeurs lors du déploiement de ressources :
    
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

    Les valeurs `omsRegion` doivent se conformer à un ensemble spécifique de valeurs. Choisissez celle qui est la plus proche du déploiement de votre cluster.

2. Si vous envoyez des journaux des applications à OMS, vérifiez que `applicationDiagnosticsStorageAccountType` et `applicationDiagnosticsStorageAccountName` sont inclus en tant que paramètres dans votre modèle. Si ce n’est pas le cas, ajoutez-les à la section des variables, puis modifiez leurs valeurs selon vos besoins. Vous pouvez également les inclure en tant que paramètres en suivant le format précédent.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Ajoutez la solution Service Fabric OMS aux variables de votre modèle :

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Ajoutez le code suivant à la fin de la section des ressources, après la déclaration de la ressource de cluster Service Fabric :

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

5. Déployez le modèle en tant que mise à niveau Resource Manager sur votre cluster à l’aide de l’API `New-AzureRmResourceGroupDeployment` dans le module AzureRM PowerShell. Voici un exemple de commande :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager détecte que cette commande est une mise à jour d’une ressource existante. Il traite uniquement les modifications qui existent entre le modèle du déploiement existant et le nouveau modèle fourni.

## <a name="deploy-oms-by-using-azure-powershell"></a>Déployer OMS en utilisant Azure PowerShell

Vous pouvez également déployer votre ressource OMS Log Analytics par le biais de PowerShell à l’aide de la commande `New-AzureRmOperationalInsightsWorkspace`. Pour utiliser cette méthode, vérifiez que vous avez installé [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Utilisez ce script pour créer un espace de travail OMS Log Analytics et lui ajouter la solution Service Fabric : 

```PowerShell

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

Quand vous avez terminé, suivez les étapes décrites dans la section précédente pour connecter OMS Log Analytics au compte de stockage approprié.

Vous pouvez également ajouter d’autres solutions ou apporter d’autres modifications à votre espace de travail OMS à l’aide de PowerShell. Pour en savoir plus, consultez [Gérer Log Analytics à l’aide de PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Étapes suivantes
* [Déployez l’agent OMS](service-fabric-diagnostics-oms-agent.md) sur vos nœuds pour collecter les compteurs de performances, ainsi que les statistiques et les journaux Docker de vos conteneurs.
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* [Utiliser le Concepteur de vues pour créer des vues personnalisées dans Log Analytics](../log-analytics/log-analytics-view-designer.md)
