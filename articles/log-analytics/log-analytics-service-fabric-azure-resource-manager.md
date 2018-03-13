---
title: "Évaluer des applications Service Fabric avec Log Analytics à l’aide du portail Azure | Microsoft Docs"
description: "Vous pouvez utiliser la solution Service Fabric dans Log Analytics, à l’aide du portail Azure, pour évaluer les risques et l’intégrité de vos applications, micro-services, nœuds et clusters Service Fabric."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nini
ms.openlocfilehash: 10c5394935c59b21139c0e141c348ce20a7c0800
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Évaluer les micro-services et applications Service Fabric avec le portail Azure | Microsoft Docs

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Symbole Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Cet article décrit comment utiliser la solution Service Fabric dans Log Analytics pour identifier et résoudre les problèmes sur l’ensemble de votre cluster Service Fabric.

La solution Service Fabric utilise les données de diagnostic Azure provenant de vos machines virtuelles Fabric Service. Ces données sont collectées à partir de vos tables Azure WAD. Log Analytics lit ensuite les événements d’infrastructure Service Fabric, notamment les **événements de service fiable**, les **événements d’acteurs**, les **événements opérationnels** et les **événements ETW personnalisés**. Le tableau de bord de la solution vous montre les problèmes notables et les événements pertinents qui s’appliquent à votre environnement Service Fabric.

Pour commencer à utiliser la solution, connectez votre cluster Service Fabric à un espace de travail Log Analytics. Trois scénarios sont à envisager :

1. Si vous n’avez pas déployé votre cluster Service Fabric, effectuez les étapes de la section ***Déployer un cluster Service Fabric connecté à un espace de travail Log Analytics*** pour déployer un nouveau cluster et le configurer pour qu’il rende compte à Log Analytics.
2. Si vous avez besoin de collecter les compteurs de performances de vos hôtes pour utiliser d’autres solutions de gestion, telles que Sécurité, sur votre cluster Service Fabric, effectuez les étapes de la section ***Déployer un cluster Service Fabric connecté à un espace de travail Log Analytics avec l’extension de machine virtuelle installée***.
3. Si vous avez déjà déployé votre cluster Service Fabric et que vous souhaitez le connecter à Log Analytics, effectuez les étapes de la section ***Ajout d’un compte de stockage existant à Log Analytics***.

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Déployer un cluster Service Fabric connecté à un espace de travail Log Analytics
Ce modèle effectue les actions suivantes :

1. Déploie un cluster Azure Service Fabric déjà connecté à un espace de travail Log Analytics. Vous pouvez soit créer un espace de travail durant le déploiement du modèle, soit entrer le nom d’un espace de travail Log Analytics existant.
2. Ajoute le compte de stockage de diagnostic à l’espace de travail Log Analytics.
3. Active la solution Service Fabric dans votre espace de travail Log Analytics.

[![Déploiement sur Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Quand vous appuyez sur le bouton Déployer ci-dessus, le portail Azure s’affiche et vous pouvez y modifier des paramètres. Veillez à créer un groupe de ressources si vous entrez un nouveau nom d’espace de travail Log Analytics :

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Acceptez les conditions légales et cliquez sur **Créer** pour lancer le déploiement. Une fois le déploiement terminé, le nouvel espace de travail et le nouveau cluster doivent s’afficher, tandis que les tables WADServiceFabric*Event, WADWindowsEventLogs et WADETWEvent doivent être ajoutées :

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Déployez un cluster Service Fabric connecté à un espace de travail Log Analytics avec l’extension de machine virtuelle installée.

Ce modèle effectue les actions suivantes :

1. Déploie un cluster Azure Service Fabric déjà connecté à un espace de travail Log Analytics. Vous pouvez créer un espace de travail ou utiliser un espace de travail existant.
2. Ajoute les comptes de stockage de diagnostic à l’espace de travail Log Analytics.
3. Active la solution Service Fabric dans l’espace de travail Log Analytics.
4. Installe l’extension de l’agent MMA dans chaque groupe de machines virtuelles identiques de votre cluster Service Fabric. Une fois l’agent MMA installé, vous pouvez afficher les mesures de performances de vos nœuds.

[![Déployer sur Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

En suivant la même procédure que celle mentionnée ci-dessus, indiquez les paramètres nécessaires et lancez le déploiement. Là encore, l’espace de travail, le cluster et les tables WAD que vous venez de créer doivent s’afficher :

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Affichage des données de performances

Pour afficher les données de performances de vos nœuds :


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Lancez l’espace de travail Log Analytics à partir du portail Azure.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Accédez à Paramètres dans le volet gauche, puis sélectionnez Données >> Compteurs de performances Windows >> Ajouter les compteurs de performances sélectionnés : ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- Dans Recherche dans les journaux, utilisez les requêtes suivantes pour étudier les mesures clés sur vos nœuds :

    a. Comparez l’utilisation moyenne du processeur sur tous les nœuds au cours de l’heure écoulée pour identifier les nœuds qui ont des problèmes et l’intervalle de temps dans lequel un nœud a connu une pointe d’activité :

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Examinez des graphiques en courbes similaires pour la mémoire disponible sur chaque nœud avec cette requête :

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Pour répertorier tous les nœuds avec la valeur moyenne exacte de mégaoctets disponibles pour chaque nœud, utilisez cette requête :

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Pour explorer au niveau du détail un nœud spécifique, notamment l’utilisation moyenne, minimale, maximale et au 75e centile du processeur par heure, utilisez cette requête (en remplaçant le champ Computer) :

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Obtenez plus d’informations sur les métriques de performance dans Log Analytics dans le [blog Operations Management Suite](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Ajout d’un compte de stockage existant à Log Analytics

Ce modèle ajoute simplement vos comptes de stockage existant à un espace de travail Log Analytics, nouveau ou existant.

[![Déployer sur Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Si vous disposez déjà d’un espace de travail Log Analytics au moment de la sélection d’un groupe, sélectionnez Utiliser l’existant et recherchez le groupe de ressources contenant l’espace de travail Log Analytics. Dans le cas contraire, créez-en un.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Une fois ce modèle déployé, vous pouvez voir le compte de stockage connecté à votre espace de travail Log Analytics. Ici, j’ai ajouté un compte de stockage à l’espace de travail Exchange créé précédemment.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Afficher les événements Service Fabric

Une fois les déploiements terminés et la solution Service Fabric activée dans votre espace de travail, sélectionnez la vignette **Service Fabric** dans le portail Log Analytics pour lancer le tableau de bord Service Fabric. Le tableau de bord comprend les colonnes figurant dans le tableau suivant. Chaque colonne répertorie les 10 premiers événements, classés selon leur nombre, correspondant aux critères de cette colonne pour l’intervalle de temps spécifié. Vous pouvez exécuter une recherche dans les journaux qui fournit la liste complète. Pour cela, cliquez sur **Afficher tout** en bas à droite de chaque colonne ou cliquez sur l’en-tête de colonne.

| **Événement Service Fabric** | **description** |
| --- | --- |
| Problèmes notables |Affichage des problèmes tels que RunAsyncFailures RunAsynCancellations et des pannes de nœud. |
| Événements opérationnels |Événements opérationnels notables, tels que les déploiements et mises à niveau d’application. |
| Événements de service fiable |Événements de service fiable notables, comme Runasyncinvocations. |
| Événements des acteurs |Événements d’acteur notables générés par vos microservices, notamment les exceptions levées par une méthode d’acteur, les activations et désactivations d’acteur, etc. |
| Événements d’application |Tous les événements ETW personnalisés générés par vos applications. |

![Tableau de bord Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Tableau de bord Service Fabric](./media/log-analytics-service-fabric/sf4.png)

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte de la solution de données pour Service Fabric.

| plateforme | Agent direct | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minutes |

> [!NOTE]
> Vous pouvez modifier l’étendue de ces événements dans la solution Service Fabric en cliquant sur **Données basées sur les 7 derniers jours** dans la partie supérieure du tableau de bord. Vous pouvez également afficher les événements générés durant les sept derniers jours, la journée précédente ou les six dernières heures. Vous pouvez aussi sélectionner **Personnalisé** pour spécifier une plage de dates personnalisée.
>
>

## <a name="next-steps"></a>Étapes suivantes

* Utilisez [Recherches dans les journaux dans Log Analytics](log-analytics-log-searches.md) pour afficher des données détaillées sur les événements Service Fabric.
