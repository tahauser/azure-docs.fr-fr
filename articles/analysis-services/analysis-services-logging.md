---
title: Journalisation des diagnostics pour Azure Analysis Services | Microsoft Docs
description: En savoir plus sur la configuration de journalisation des diagnostics pour Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: cadd47d2e5f490f82846ea562803fcd60f5405a7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="setup-diagnostic-logging"></a>Configurer la journalisation des diagnostics

Une des fonctions importantes d’une solution Analysis Services est d’analyser les performances de vos serveurs. Avec les [journaux de diagnostic de ressources Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), vous pouvez effectuer une surveillance et envoyer des journaux au [Stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et les exporter au format [Log Analytics](https://azure.microsoft.com/services/log-analytics/) qui fait partie d’[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Journalisation des diagnostics dans le Stockage, Event Hubs ou Operations Management Suite via Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Éléments journalisés :

Vous pouvez sélectionner les catégories **Moteur**, **Service** et **Métriques**.

### <a name="engine"></a>Engine (Moteur)

L’option **Moteur** enregistre toutes les événements [xEvent](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Vous ne pouvez pas sélectionner des événements individuels. 

|Catégories de XEvent |Nom de l'événement  |
|---------|---------|
|Audit de la sécurité    |   Audit Login      |
|Audit de la sécurité    |   Audit Logout      |
|Audit de la sécurité    |   Audit Server Starts And Stops      |
|Rapports d’avancement     |   Progress Report Begin      |
|Rapports d’avancement     |   Progress Report End      |
|Rapports d’avancement     |   Progress Report Current      |
|Requêtes     |  Query Begin       |
|Requêtes     |   Query End      |
|Commandes     |  Command Begin       |
|Commandes     |  Command End       |
|Erreurs et avertissement     |   Error      |
|Découvrez     |   Discover End      |
|Notification     |    Notification     |
|session     |  Session Initialize       |
|Verrous    |  Deadlock       |
|Traitement des requêtes     |   VertiPaq SE Query Begin      |
|Traitement des requêtes     |   VertiPaq SE Query End      |
|Traitement des requêtes     |   VertiPaq SE Query Cache Match      |
|Traitement des requêtes     |   Direct Query Begin      |
|Traitement des requêtes     |  Direct Query End       |

### <a name="service"></a>de diffusion en continu

|Nom d’opération  |Survient lorsque  |
|---------|---------|
|CreateGateway     |   L’utilisateur configure une passerelle sur le serveur      |
|ResumeServer     |    Reprendre un serveur     |
|SuspendServer    |   Suspendre un serveur      |
|DeleteServer     |    Supprimer un serveur     |
|RestartServer    |     L’utilisateur redémarre un serveur via SSMS ou PowerShell    |
|GetServerLogFiles    |    L’utilisateur exporte le journal du serveur via PowerShell     |
|ExportModel     |   L’utilisateur exporte un modèle dans le portail à l’aide de la commande Ouvrir dans Visual Studio     |

### <a name="all-metrics"></a>Toutes les métriques

La catégorie Métriques journalise les mêmes [métriques serveur](analysis-services-monitor.md#server-metrics) que celles affichées dans les Métriques.

## <a name="setup-diagnostics-logging"></a>Configurer la journalisation des diagnostics

### <a name="azure-portal"></a>Portail Azure

1. Dans [Portail Azure](https://portal.azure.com) > Serveur, cliquez sur **Journaux de diagnostic** dans le volet de navigation de gauche, puis sur **Activer les diagnostics**.

    ![Activer la journalisation des diagnostics pour Azure Cosmos DB dans le portail Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Dans **Paramètres de diagnostic**, spécifiez les options suivantes : 

    * **Nom**. Entrez un nom pour les journaux à créer.

    * **Archive vers un compte de stockage**. Pour utiliser cette option, vous avez besoin d’un compte de stockage existant auquel vous connecter. Voir [Créer un compte de stockage](../storage/common/storage-create-storage-account.md). Suivez les instructions pour créer un compte Resource Manager à usage général, puis sélectionnez votre compte de stockage en retournant sur cette page du portail. L’affichage des comptes de stockage nouvellement créés dans le menu déroulant peut prendre quelques minutes.
    * **Transmettre à un Event Hub**. Pour utiliser cette option, vous avez besoin d’un espace de noms Event Hub existant et d’un Event Hub auquel vous connecter. Pour plus d’informations, consultez [Créer un espace de noms Event Hubs et un concentrateur d’événements avec le portail Azure](../event-hubs/event-hubs-create.md). Puis revenez à cette page dans le portail pour sélectionner l’espace de noms Event Hub et le nom de la stratégie.
    * **Envoyer à Log Analytics**. Pour utiliser cette option, utilisez un espace de travail existant ou créez un espace de travail Log Analytics en suivant les étapes permettant de [créer un espace de travail](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) dans le portail. Pour plus d’informations sur l’affichage de vos journaux dans Log Analytics, consultez la section [Afficher les journaux dans Log Analytics](#view-in-loganalytics).

    * **Moteur**. Sélectionnez cette option pour journaliser les événements XEvent. Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention.
    * **Service**. Sélectionnez cette option pour journaliser les événements de niveau Service. Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention.
    * **Métriques**. Sélectionnez cette option pour stocker des données détaillées dans [Métriques](analysis-services-monitor.md#server-metrics). Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention.

3. Cliquez sur **Enregistrer**.

    Si vous recevez une erreur indiquant « Échec de la mise à jour des diagnostics pour \<nom de l’espace de noms>. L’abonnement \<id de l’abonnement> n’est pas inscrit pour utiliser microsoft.insights. », suivez les instructions de la page [Résoudre les problèmes d’Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pour inscrire le compte, puis recommencez cette procédure.

    Si vous souhaitez modifier la façon dont vos journaux de diagnostic seront enregistrés à l’avenir, vous pouvez revenir à cette page à tout moment et modifier les paramètres.

### <a name="powershell"></a>PowerShell

Voici les commandes de base qui vous seront utiles. Si vous souhaitez obtenir une aide détaillée sur la configuration de la journalisation dans un compte de stockage à l’aide de PowerShell, consultez le didacticiel plus loin dans cet article.

Pour activer la journalisation des métriques et diagnostics à l’aide de PowerShell, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage où vous souhaitez envoyer les journaux.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L’ID de règle Azure Service Bus est une chaîne au format suivant :

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="rest-api"></a>de l’API REST

Découvrez comment [modifier les paramètres de diagnostic à l’aide de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modèle Resource Manager

Découvrez comment [activer les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Gérer vos journaux

En règle générale, les journaux sont disponibles deux heures après la configuration de la journalisation. C’est à vous de gérer vos journaux dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux en limitant l’accès à ces derniers.
* Supprimez les journaux que vous ne souhaitez plus conserver dans votre compte de stockage.
* Veillez à définir une période de rétention qui supprime les très anciens journaux de votre compte de stockage.

## <a name="view-logs-in-log-analytics"></a>Afficher les journaux dans Log Analytics

Les événements Métriques et Serveur sont intégrés aux événements XEvent dans Log Analytics, afin de permettre une analyse côte à côte. Il est également possible de configurer Log Analytics pour qu’il reçoive des événements d’autres services Azure, afin d’obtenir une vue globale des données de journalisation des diagnostics sur votre architecture.

Pour afficher vos données de diagnostic dans Log Analytics, ouvrez la page Recherche dans les journaux dans le menu de gauche ou la zone Gestion, comme indiqué ci-dessous.

![Options de recherche dans les journaux dans le portail Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Maintenant que vous avez activé la collecte de données, dans **Recherche dans les journaux**, cliquez sur **Toutes les données collectées**.

Dans **Type**, cliquez sur **AzureDiagnostics** puis sur **Appliquer**. AzureDiagnostics inclut les événements Moteur et Service. Notez qu’une requête Log Analytics est créée à la volée. Le champ EventClass\_s contient des noms xEvent, qui peuvent vous paraître familiers si vous avez utilisé des xEvent pour la journalisation locale.

Cliquez sur **EventClass\_s** ou sur un des noms d’événement. Log Analytics poursuit la création d’une requête. Veillez à enregistrer vos requêtes pour les réutiliser ultérieurement.

Consultez le site Operations Management Suite, qui fournit des requêtes améliorées, des tableaux de bord et des fonctionnalités d’alerte sur les données Log Analytics.

### <a name="queries"></a>Requêtes

Il existe des centaines de requêtes que vous pouvez utiliser. En voici quelques-unes pour vous aider à démarrer.
Pour plus d’informations sur l’utilisation du nouveau langage de requête Rechercher dans les journaux, consultez [Présentation des recherches dans les journaux dans Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Cette requête renvoie les requêtes envoyées à Azure Analysis Services et dont l’exécution a dépassé cinq minutes (300 000 millisecondes).

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identifiez les réplicas Scale-out.

    ```
    search * | summarize count() by ServerName_s
    ```
    Lorsque vous utilisez Scale-out, vous pouvez identifier des réplicas en lecture seule, car les valeurs du champ ServerName\_s se terminent par le numéro de l’instance de réplica. Le champ de ressource contient le nom de ressource Azure, qui correspond au nom de serveur que les utilisateurs voient. Le champ IsQueryScaleoutReadonlyInstance_s affiche la valeur True pour les réplicas.



> [!TIP]
> Vous voulez partager une requête Log Analytics que vous aimez particulièrement ? Si vous avez un compte GitHub, vous pouvez l’ajouter à cet article. Il vous suffit de cliquer sur **Modifier** en haut et à droite de cette page.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Didacticiel - Activer la journalisation à l’aide de PowerShell
Dans ce bref didacticiel, vous créez un compte de stockage dans le même abonnement et le même groupe de ressources que votre serveur Analysis Services. Ensuite, vous utilisez la requête Set-AzureRmDiagnosticSetting pour activer la journalisation des diagnostics et envoyer la sortie au nouveau compte de stockage.

### <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous avez besoin des ressources suivantes :

* Un serveur Azure Analysis Services. Pour plus d’informations sur la création d’une ressource du serveur, consultez [Création d’un serveur Azure Analysis Services dans le portail Azure](analysis-services-create-server.md) ou [Créer un serveur Azure Analysis Services à l’aide de PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Se connecter à vos abonnements

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :  

```powershell
Login-AzureRmAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre Azure Key Vault. Tapez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzureRmSubscription
```

Ensuite, pour spécifier l’abonnement associé au compte Azure Analysis Services que vous journalisez, tapez :

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si plusieurs abonnements sont associés à votre compte, il est important d’en spécifier un.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Créer un nouveau compte de stockage pour vos journaux

Vous pouvez utiliser un compte de stockage pour vos journaux, à condition qu’il figure dans le même abonnement que votre serveur. Pour ce didacticiel, vous créez un compte de stockage dédié aux journaux Analysis Services. Pour plus de commodité, vous stockez les détails du compte de stockage dans une variable nommée **sa**.

Vous utilisez également le même groupe de ressources que celui qui contient votre serveur Analysis Services. Remplacez les valeurs de `awsales_resgroup`, `awsaleslogs` et `West Central US` par les vôtres :

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifier le compte de serveur pour les journaux

Définissez le nom du compte dans une variable nommée **account**, où ResourceName est le nom du compte.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Activation de la journalisation

Pour activer la journalisation, utilisez l’applet de commande Set-AzureRmDiagnosticSetting avec les variables du nouveau compte de stockage, du compte du serveur et de la catégorie. Exécutez la commande suivante, en définissant l’indicateur **-Enabled** sur **$true** :

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Le résultat suivant doit ressembler à ce qui suit :

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

La journalisation est maintenant activée pour le serveur et enregistre les informations dans le compte de stockage.

Vous pouvez également définir une stratégie de rétention qui supprime automatiquement vos anciens journaux. Par exemple, définissez une stratégie de rétention en attribuant à l’indicateur **-RetentionEnabled** la valeur **$true** et en réglant le paramètre **-RetentionInDays** sur **90**. Ainsi, les journaux de plus de 90 jours sont automatiquement supprimés.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [journalisation des diagnostics de ressources Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Consultez [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) dans l’aide de PowerShell.