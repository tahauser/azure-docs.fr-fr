---
title: Monitorage et diagnostics des conteneurs Windows dans Azure Service Fabric | Microsoft Docs
description: "Dans ce didacticiel, vous configurez le monitorage et les diagnostics du conteneur Windows orchestré sur Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: de77d10e4875173c7a067e945e473887d3cc7422
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-oms"></a>Didacticiel : surveiller des conteneurs Windows sur Service Fabric avec OMS

Il s’agit de la troisième partie d’un didacticiel, qui vous guide dans la configuration d’OMS pour effectuer le monitorage de vos conteneurs Windows orchestrés sur Service Fabric.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * configurer OMS pour votre cluster Service Fabric ;
> * utiliser un espace de travail OMS pour afficher et interroger des journaux à partir de vos conteneurs et de vos nœuds ;
> * configurer l’Agent OMS pour collecter des mesures sur les conteneurs et les nœuds.

## <a name="prerequisites"></a>configuration requise
Avant de commencer ce didacticiel, vous devez :
- disposer d’un cluster sur Azure, ou en [créer un avec ce didacticiel](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ;
- [y déployer une application en conteneur](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>Configurer OMS avec un cluster dans le modèle Resource Manager

Si vous avez utilisé le [modèle fourni](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) dans la première partie de ce didacticiel, il devrait normalement inclure les ajouts suivants à un modèle Azure Resource Manager Service Fabric générique. Si, au contraire, vous cherchez à configurer votre propre cluster pour effectuer le monitorage des conteneurs avec OMS :
* Apportez les modifications suivantes à votre modèle Resource Manager.
* Déployez-le avec PowerShell pour mettre à niveau votre cluster en [déployant le modèle](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager se rend compte que la ressource existe, et par conséquent la déploie comme une mise à niveau.

### <a name="adding-oms-to-your-cluster-template"></a>Ajouter OMS à un modèle de cluster

Apportez les modifications suivantes à votre *template.json* :

1. Ajoutez le nom et l’emplacement de l’espace de travail OMS à votre section *paramètres* :
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
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

    Pour modifier l’une des deux valeurs utilisées, ajoutez les mêmes paramètres à votre *template.parameters.json* et modifiez-y les valeurs utilisées.

2. Ajouter le nom de la solution et la solution à vos *variables* : 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Ajoutez OMS Microsoft Monitoring Agent comme extension de machine virtuelle. Recherchez la ressource des groupes de machines virtuelles identiques : *resources* > *"apiVersion": "[variables(’vmssApiVersion’)]"*. Sous *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions*, ajoutez la description de l’extension suivante sous l’extension *ServiceFabricNode* : 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Ajoutez l’espace de travail OMS en tant que ressource individuelle. Dans *resources*, après la ressource des groupes de machines virtuelles identiques, ajoutez le code suivant :
    
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
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

Vous trouverez [ici](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) un exemple de modèle (utilisé dans la première partie de ce didacticiel) comportant toutes ces modifications, que vous pouvez référencer selon vos besoins. Ces modifications ajouteront un espace de travail OMS Log Analytics à votre groupe de ressources. L’espace de travail sera configuré pour collecter les événements de la plateforme Service Fabric à partir des tables de stockage configurées avec l’agent de [diagnostics Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md). L’Agent OMS (Microsoft Monitoring Agent) a également été ajouté à chaque nœud de votre cluster comme extension de machine virtuelle, ce qui signifie que, lorsque vous faites évoluer votre cluster, l’Agent est automatiquement configuré sur chaque ordinateur et raccordé au même espace de travail.

Déployez le modèle avec vos nouvelles modifications pour mettre à niveau votre cluster actuel. Vous devriez voir les ressources OMS dans votre groupe de ressources une fois le processus terminé. Lorsque le cluster est prêt, déployez-y votre application en conteneur. À l’étape suivante, nous allons configurer le monitorage des conteneurs.

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>Ajouter la solution de monitorage des conteneurs à un espace de travail OMS

Pour configurer la solution Conteneur dans votre espace de travail, recherchez *Solution de monitorage des conteneurs* et créez une ressource Conteneurs (sous la catégorie Monitorage + gestion).

![Ajout de la solution Conteneurs](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Lorsque vous êtes invité à choisir *l’Espace de travail OMS*, sélectionnez l’espace de travail créé dans votre groupe de ressources, puis cliquez sur **Créer**. Cette opération ajoute une *Solution de monitorage des conteneurs* à votre espace de travail ; de ce fait, l’Agent OMS déployé par le modèle commence automatiquement à collecter les journaux et les statistiques de Docker. 

Revenez à votre *groupe de ressources*, où vous devriez à présent voir la solution de monitorage qui vient d’être ajoutée. Si vous cliquez dessus, la page de destination devrait afficher le nombre d’images conteneurs en cours d’exécution. 

*Notez que j’ai exécuté cinq instances de mon conteneur fabrikam à partir de la [deuxième partie](service-fabric-host-app-in-a-container.md) du didacticiel*.

![Page de destination de la solution Conteneur](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Si vous cliquez sur la **Solution de monitorage des conteneurs**, vous accéderez à un tableau de bord plus détaillé, ce qui vous permettra de parcourir plusieurs panneaux et d’exécuter des requêtes dans Log Analytics. 

*Notez que, depuis septembre 2017, la solution est en cours de mise à jour : ignorez les erreurs que vous pourriez rencontrer sur les événements Kubernetes, car nous travaillons à l’intégration de plusieurs orchestrateurs dans la même solution.*

Étant donné que l’agent collecte des journaux Docker, il affiche par défaut *stdout* et *stderr*. Si vous faites défiler vers la droite, vous pouvez voir l’inventaire des images conteneurs, l’état, les mesures et des exemples de requêtes que vous pouvez exécuter pour obtenir d’autres données utiles. 

![Tableau de bord de la solution Conteneur](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Si vous cliquez sur l’un de ces panneaux, vous accéderez à la requête Log Analytics qui génère la valeur affichée. Modifiez la requête avec *\** pour afficher les différents types de journaux collectés. À ce stade, vous pouvez interroger ou filtrer sur les performances du conteneur ou les journaux, ou bien examiner les événements de la plateforme Service Fabric. Vos agents émettent en permanence une pulsation sur chaque nœud, que vous pouvez examiner pour vérifier que les données sont toujours collectées sur toutes vos machines si votre configuration de clusters change.   

![Requête de conteneur](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Configurer l’Agent OMS de façon à collecter des compteurs de performances

Autre avantage de l’Agent OMS, vous pouvez modifier les compteurs de performances que vous souhaitez collecter par le biais de l’expérience utilisateur OMS, sans avoir à configurer l’agent de diagnostics Azure ni à effectuer une mise à niveau par le modèle Resource Manager à chaque fois. Pour cela, cliquez sur le **Portail OMS** sur la page de destination de votre solution de monitorage des conteneurs (ou Service Fabric).

![Portail OMS](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Vous accéderez ainsi à votre espace de travail sur le portail OMS, où vous pourrez consulter vos solutions, créer des tableaux de bord personnalisés et configurer l’Agent OMS. 
* Cliquez sur la **roue dentée** dans l’angle supérieur droit de votre écran pour ouvrir le menu *Paramètres*.
* Cliquez sur **Sources connectées** > **Serveurs Windows** pour vérifier que vous avez *cinq ordinateurs Windows connectés*.
* Cliquez sur **Données** > **Compteurs de performances Windows** pour rechercher et ajouter de nouveaux compteurs de performances. Vous verrez une liste de recommandations, fournie par OMS, de compteurs de performances que vous pouvez recueillir ; vous aurez également la possibilité de rechercher d’autres compteurs. Cliquez sur **Ajouter les compteurs de performances sélectionnés** pour commencer à collecter les mesures suggérées.

    ![Perf counters](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

Revenez sur le Portail Azure et **actualisez** votre solution de monitorage des conteneurs après quelques minutes : vous devriez commencer à voir arriver des données *Performances de l’ordinateur*. Cela vous aidera à comprendre l’utilisation des ressources. Vous pouvez également utiliser ces mesures pour prendre les décisions adaptées quant à la mise à l’échelle de votre cluster, ou pour vérifier qu’un cluster équilibre votre charge comme prévu.

*Remarque : Vérifiez que vos filtres temporels sont définis de façon à vous permettre d’utiliser ces mesures.* 

![Compteurs de performances 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * configurer OMS pour votre cluster Service Fabric ;
> * utiliser un espace de travail OMS pour afficher et interroger des journaux à partir de vos conteneurs et de vos nœuds ;
> * configurer l’Agent OMS pour collecter des mesures sur les conteneurs et les nœuds.

Maintenant que vous avez configuré le monitorage de votre application en conteneur, essayez les opérations suivantes :

* Configurez OMS pour un cluster Linux, en suivant les étapes ci-dessus. Référencez [ce modèle](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) pour apporter des modifications à votre modèle Resource Manager.
* Configurez OMS pour paramétrer [l’alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics.
* Explorez la liste Service Fabric de [compteurs de performances recommandés](service-fabric-diagnostics-event-generation-perf.md) à configurer pour vos clusters.
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) proposées par Log Analytics.