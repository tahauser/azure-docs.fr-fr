---
title: "Surveiller et gérer les pipelines à l’aide du portail Azure et de PowerShell | Microsoft Docs"
description: "Découvrez comment utiliser le portail Azure et Azure PowerShell pour surveiller et gérer les fabriques de données et les pipelines Azure que vous avez créés."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b361ca67ebece9ba1ced1010835eb90586dd7359
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Surveiller et gérer les pipelines Azure Data Factory à l’aide du portail Azure et de PowerShell
> [!div class="op_single_selector"]
> * [Utilisation du portail Azure/d’Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Utilisation de l’application de surveillance et gestion](data-factory-monitor-manage-app.md)

> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service Data Factory, qui est en préversion, consultez [monitor and manage Data Factory pipelines in version 2](../monitor-visually.md) (Surveiller et gérer des pipelines Data Factory dans la version 2).

> [!IMPORTANT]
> L’application de surveillance et gestion favorise la surveillance et la gestion de vos pipelines de données, ainsi que la résolution des problèmes. Pour en savoir plus sur l’utilisation de l’application, consultez [Surveiller et gérer les pipelines Azure Data Factory à l’aide de l’application de surveillance et gestion](data-factory-monitor-manage-app.md). 


Cet article décrit comment surveiller, gérer et déboguer vos pipelines à l’aide du Portail Azure et de PowerShell. Il offre également des informations sur la façon de créer des alertes et d’être averti en cas d’échec.

## <a name="understand-pipelines-and-activity-states"></a>Présentation des pipelines et des états d’activité
À l’aide du portail Azure, vous pouvez :

* Afficher votre fabrique de données sous forme de schéma.
* Afficher les activités à l’intérieur d’un pipeline.
* Afficher des jeux de données d’entrée et de sortie.

Cette section décrit également comment une tranche de jeu de données passe d’un état à un autre.   

### <a name="navigate-to-your-data-factory"></a>Accédez à votre fabrique de données
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquer sur **Fabriques de données** dans le menu de gauche. Si vous ne voyez pas cette option, cliquez sur **Autres services >**, puis sur **Fabriques de données** dans la catégorie **INTELLIGENCE + ANALYSE**.

   ![Parcourir tout > Fabriques de données](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Dans le panneau **Fabriques de données**, sélectionnez la fabrique de données qui vous intéresse.

    ![Sélectionner une fabrique de données](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Vous devez voir la page d’accueil de la fabrique de données apparaître.

   ![Panneau Data Factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vue schématique de votre fabrique de données
La vue **schématique** d’une fabrique de données est un point unique de surveillance et de gestion de la fabrique de données et de ses ressources. Cliquez sur **Schématique** sur la page d’accueil de la fabrique de données ci-dessus pour afficher la vue **schématique** de votre fabrique de données.

![Vue schématique](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster l’affichage à la taille de l’écran, figer l’affichage schématique et positionner automatiquement les pipelines et les jeux de données. Vous pouvez également afficher le lignage (c.-à-d. mise en surbrillance des éléments en amont et en aval des éléments sélectionnés).

### <a name="activities-inside-a-pipeline"></a>Activités à l'intérieur d'un pipeline
1. Cliquez avec le bouton droit sur le pipeline de votre choix, puis cliquez sur **Ouvrir le pipeline** pour faire apparaître toutes les activités dans le pipeline, ainsi que les jeux de données d’entrée et de sortie des activités. Cette fonctionnalité est utile quand votre pipeline comprend plusieurs activités et que vous souhaitez comprendre le lignage opérationnel d’un seul pipeline.

    ![Menu Ouvrir un pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. Dans l’exemple suivant, vous voyez une activité de copie dans le pipeline avec une entrée et une sortie. 

    ![Activités à l'intérieur d'un pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Vous pouvez revenir à la page d’accueil de la fabrique de données en cliquant sur le lien **Data Factory** dans l’arborescence de navigation située dans le coin supérieur gauche.

    ![Revenir à la fabrique de données](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Afficher l’état de chaque activité à l’intérieur d’un pipeline
Vous pouvez afficher l’état actuel d’une activité en consultant l’état de l’un des jeux de données générés par l’activité.

En double-cliquant sur **OutputBlobTable** dans le **Diagramme**, vous pouvez voir toutes les tranches produites par les différentes exécutions de l’activité à l’intérieur d’un pipeline. Vous pouvez voir que l’activité de copie a été exécutée correctement au cours des huit dernières heures et qu’elle a produit les tranches dont l’état est **Prêt**.  

![État du pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Voici la liste des différents états possibles pour les tranches d’un jeu de données de la fabrique de données :

<table>
<tr>
    <th align="left">État</th><th align="left">Sous-état</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">En attente</td><td>ScheduleTime</td><td>L’heure n’est pas venue pour l’exécution de la tranche.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Les dépendances en amont ne sont pas prêtes.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Les ressources de calcul ne sont pas disponibles.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Toutes les instances d'activité sont occupées par l'exécution d'autres tranches.</td>
</tr>
<tr>
<td>ActivityResume</td><td>L’activité est mise en pause et ne peut pas exécuter les tranches jusqu’à sa reprise.</td>
</tr>
<tr>
<td>Retry</td><td>L’exécution de l’activité est retentée.</td>
</tr>
<tr>
<td>Validation</td><td>La validation n’a pas encore démarré.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Une nouvelle tentative de validation va avoir lieu.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validation</td><td>La validation est en cours.</td>
</tr>
<td>-</td>
<td>La tranche est en cours de traitement.</td>
</tr>
<tr>
<td rowspan="4">Échec</td><td>TimedOut</td><td>L'exécution de l’activité a pris plus de temps que l’activité ne l’autorise.</td>
</tr>
<tr>
<td>Canceled</td><td>La tranche a été annulée par l’action de l’utilisateur.</td>
</tr>
<tr>
<td>Validation</td><td>La validation a échoué.</td>
</tr>
<tr>
<td>-</td><td>La validation et/ou la génération de la tranche a échoué.</td>
</tr>
<td>Ready</td><td>-</td><td>La tranche est prête à être consommée.</td>
</tr>
<tr>
<td>Ignoré</td><td>Aucun</td><td>La tranche n’est pas en cours de traitement.</td>
</tr>
<tr>
<td>Aucun</td><td>-</td><td>Tranche qui a été réinitialisée alors qu’elle existait avec un état différent.</td>
</tr>
</table>



Vous pouvez afficher les détails relatifs à une tranche en cliquant sur une entrée de tranche dans le panneau **Tranches mises à jour récemment**.

![Détails de la tranche](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Si la tranche a été exécutée plusieurs fois, plusieurs lignes s’affichent dans la liste **Exécutions d’activité** . Vous pouvez afficher des détails sur une exécution d’activité en cliquant sur l’entrée d’exécution dans la liste **Exécutions de l’activité**. Tous les fichiers journaux, ainsi que le message d’erreur associé, le cas échéant, s’affichent. Cette fonctionnalité est utile et pour cause. Vous visualisez et déboguez les journaux sans le souci de quitter votre fabrique de données.

![Détails de l'exécution d'activité](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Si la tranche n’a pas l’état **Prêt**, vous pouvez voir les tranches en amont qui ne sont pas prêtes et qui empêchent l’exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**. Cette fonctionnalité est utile lorsque votre tranche présente l’état **En attente** et que vous voulez connaître les dépendances en amont à l’origine de cette attente.

![Tranches en amont qui ne sont pas prêtes](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Schéma d’état de jeux de données
Quand vous avez déployé une fabrique de données et que la période d’activation des pipelines est valide, les tranches de données des jeux de données passent d’un état à un autre. Actuellement, l’état de la tranche suit le schéma d’état suivant :

![Schéma d'état](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Le flux de transition d’états des jeux de données de la fabrique de données est le suivant : En attente -> En cours/En cours (Validation) -> Prête/Échec.

Au départ, la tranche a l’état **En attente**, en attente des conditions requises à respecter avant l’exécution. Ensuite, l’exécution de l’activité commence, et la tranche passe à l’état **En cours**. L’exécution de l’activité peut réussir ou échouer. Selon le résultat de l’exécution, l’état de la tranche est **Prête** ou **Échec**.

Vous pouvez réinitialiser la tranche pour revenir de l’état **Prête** ou **Échec** à l’état **En attente**. Vous pouvez également définir l’état de la tranche sur **Ignorer** pour empêcher l’exécution de l’activité et ne pas traiter la tranche.

## <a name="pause-and-resume-pipelines"></a>Suspension et reprise des pipelines
Vous pouvez gérer vos pipelines à l’aide d’Azure PowerShell. Par exemple, vous pouvez suspendre et reprendre les pipelines en exécutant les applets de commande Azure PowerShell. 

> [!NOTE] 
> La vue schématique ne prend pas en charge la suspension et la reprise des pipelines. Si vous souhaitez utiliser une interface utilisateur, utilisez l’application de surveillance et gestion. Pour en savoir plus sur l’utilisation de l’application, consultez l’article [Surveiller et gérer les pipelines Azure Data Factory à l’aide de l’application de surveillance et gestion](data-factory-monitor-manage-app.md). 

Vous pouvez suspendre l’exécution des pipelines à l’aide de l’applet de commande PowerShell **Suspend-AzureRmDataFactoryPipeline**. Cette applet de commande est utile lorsque vous ne voulez pas exécuter vos pipelines jusqu'à ce qu’un problème est résolu. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Par exemple : 

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Une fois le problème résolu au niveau du pipeline, vous pouvez reprendre le pipeline suspendu en exécutant la commande PowerShell suivante :

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Par exemple : 

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Débogage de pipelines
Azure Data Factory offre des fonctionnalités exceptionnelles pour déboguer et résoudre les problèmes des pipelines via le portail Azure et Azure PowerShell.

> [!Remarque} Il est beaucoup plus facile de résoudre les erreurs à l’aide de l’application de surveillance et gestion. Pour en savoir plus sur l’utilisation de l’application, consultez l’article [Surveiller et gérer les pipelines Azure Data Factory à l’aide de l’application de surveillance et gestion](data-factory-monitor-manage-app.md). 

### <a name="find-errors-in-a-pipeline"></a>Recherche d’erreurs dans un pipeline
En cas d’échec d’exécution de l’activité dans un pipeline, le jeu de données généré par celui-ci est alors en état d’erreur. Vous pouvez déboguer et corriger les erreurs dans Azure Data Factory à l’aide des méthodes suivantes.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Utiliser le portail Azure pour déboguer une erreur
1. Dans le panneau **Table**, cliquez sur la tranche qui pose problème, dont **l’état** est défini sur **Échec**.

   ![Panneau de table avec tranche problématique](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Dans le panneau **Tranche de données**, cliquez sur l’exécution d’activité qui a échoué.

   ![Tranche de données avec une erreur](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Dans le panneau **Détails sur l’exécution d’activité**, vous pouvez télécharger les fichiers associés au traitement HDInsight. Cliquez sur **Télécharger** pour que Status/stderr télécharge le fichier journal d’erreur qui contient les détails sur l’erreur.

   ![Panneau de détails sur l’exécution d’activité](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Utiliser PowerShell pour déboguer une erreur
1. Lancez **PowerShell**.
2. Exécutez la commande **Get-AzureRmDataFactorySlice** pour voir les tranches et leur état. Une tranche dont l’état est **Échec**devrait apparaître.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Par exemple : 

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Remplacez **StartDateTime** par l’heure de début de votre pipeline. 
3. Exécutez maintenant l’applet de commande **Get-AzureRmDataFactoryRun** pour obtenir des détails sur l’exécution de l’activité de la tranche.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Par exemple : 

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    La valeur de StartDateTime est l’heure de début de la tranche qui pose problème/l’erreur que vous avez notée à l’étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
4. Vous devez voir la sortie avec les détails sur l’erreur qui est semblable à ce qui suit :

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Vous pouvez exécuter l’applet de commande **Save-AzureRmDataFactoryLog** avec la valeur d’ID indiquée dans la sortie, et télécharger les fichiers journaux en utilisant le paramètre **-DownloadLogsoption** pour l’applet de commande.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Réexécuter des échecs dans un pipeline

> [!IMPORTANT]
> Il est plus facile de résoudre les erreurs et de réexécuter les tranches ayant échoué à l’aide de l’application de surveillance et gestion. Pour en savoir plus sur l’utilisation de l’application, consultez [Surveiller et gérer les pipelines Azure Data Factory à l’aide de l’application de surveillance et gestion](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure
Après avoir débogué et résolu les problèmes dans un pipeline, vous pouvez réexécuter ceux-ci en accédant à la tranche qui pose problème, puis en cliquant sur le bouton **Exécuter** dans la barre de commandes.

![Réexécuter une tranche de données ayant échoué](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

En cas d’échec de validation de la tranche à cause d’une erreur de stratégie (p. ex. données indisponibles), vous pouvez résoudre le problème et relancer la validation en cliquant sur le bouton **Valider** de la barre de commandes.

![Corriger les erreurs et valider](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Vous pouvez réexécuter des problèmes à l’aide de l’applet de commande **Set-AzureRmDataFactorySliceStatus**. Consultez la rubrique [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) pour la syntaxe et pour plus d’informations sur l’applet de commande.

**Exemple :**

L’exemple suivant définit l’état de toutes les tranches de la table « DAWikiAggregatedData » sur « En attente » dans la fabrique de données Azure « WikiADF ».

« UpdateType » est défini sur « UpstreamInPipeline ». Concrètement, l’état « En attente » est défini pour chaque tranche de la table et toutes les tables (en amont) dépendantes. Sinon, la valeur « Individuel » pour ce paramètre est également possible.

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Créez des alertes
Azure consigne les événements utilisateur lorsqu'une ressource Azure (par exemple, une fabrique de données) est créée, mise à jour ou supprimée. Vous pouvez créer des alertes relatives à ces événements. Vous pouvez utiliser Data Factory pour capturer différentes mesures et créer des alertes associées. Nous vous recommandons d’utiliser les événements pour obtenir une surveillance en temps réel et les mesures à des fins d’historique.

### <a name="alerts-on-events"></a>Alertes relatives à des événements
Les événements Azure fournissent des explications utiles sur ce qui se passe dans vos ressources Azure. Lorsque vous utilisez Azure Data Factory, les événements sont générés quand :

* Une fabrique de données est créée, mise à jour ou supprimée.
* Le traitement des données (les « exécutions ») est démarré ou terminé.
* Un cluster HDInsight à la demande est créé ou supprimé.

Vous pouvez créer des alertes relatives à ces événements utilisateur et les configurer pour envoyer des notifications par courrier électronique à l’administrateur et aux coadministrateurs de l’abonnement. De plus, vous pouvez spécifier des adresses de messagerie supplémentaires pour les utilisateurs devant recevoir des notifications par courrier électronique lorsque les conditions sont remplies. Cette fonctionnalité est très utile lorsque vous souhaitez être averti en cas d’échec et que vous ne souhaitez pas surveiller en continu votre fabrique de données.

> [!NOTE]
> Actuellement, le portail n’affiche pas les alertes sur les événements. Utilisez [l’Application de surveillance et gestion](data-factory-monitor-manage-app.md) pour afficher toutes les alertes.


#### <a name="specify-an-alert-definition"></a>Spécifier une définition d’alerte
Pour spécifier une définition d’alerte, vous devez créer un fichier JSON qui décrit les opérations pour lesquelles vous souhaitez être alerté. Dans l'exemple suivant, l'alerte envoie une notification par courrier électronique pour l’opération RunFinished. Pour être plus précis, une notification par courrier électronique est envoyée lorsqu'une exécution de la fabrique de données est terminée en ayant échoué (État = FailedExecution).

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

Si vous ne voulez pas recevoir d’alerte relative à un problème spécifique, vous pouvez supprimer **subStatus** de la définition JSON ci-dessus.

L'exemple ci-dessus définit l'alerte de toutes les fabriques de données de votre abonnement. Si vous souhaitez configurer l’alerte pour une fabrique de données particulière, vous pouvez spécifier la fabrique de données **resourceUri** dans le bloc **dataSource** :

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

Le tableau suivant dresse la liste des opérations et des états (et états secondaires) disponibles.

| Nom d’opération | Statut | État secondaire |
| --- | --- | --- |
| RunStarted |Démarré |Démarrage en cours |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandonné |
| OnDemandClusterCreateStarted |Démarré | |
| OnDemandClusterCreateSuccessful |Succeeded | |
| OnDemandClusterDeleted |Succeeded | |

Consultez [Create Alert Rule](https://msdn.microsoft.com/library/azure/dn510366.aspx) (Créer une règle d’alerte) pour plus d’informations sur les éléments JSON utilisés dans l’exemple.

#### <a name="deploy-the-alert"></a>Déployer l’alerte
Pour déployer l’alerte, utilisez l’applet de commande Azure PowerShell **New-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Une fois le déploiement du groupe de ressources terminé, les messages suivants s’affichent :

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> Vous pouvez utiliser l’API REST [Créer une règle d’alerte](https://msdn.microsoft.com/library/azure/dn510366.aspx) pour créer une règle d’alerte. La charge utile JSON est similaire à l’exemple JSON.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Récupérer la liste des déploiements de groupes de ressources Azure
Pour récupérer la liste des déploiements de groupes de ressources Azure, utilisez l’applet de commande **Get-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Résoudre les problèmes relatifs aux événements utilisateur
1. Vous pouvez voir tous les événements générés après avoir cliqué sur la mosaïque **Mesures et opérations**.

    ![Vignette Mesures et opérations](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Cliquez sur la mosaïque **Événements** pour afficher les événements.

    ![Vignette d'événements](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. Dans le panneau **Événements**, vous pouvez consulter des détails sur les événements, les événements filtrés, et ainsi de suite.

    ![Panneau Événements](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Dans la liste des opérations, cliquez sur une **opération** à l’origine d’une erreur.

    ![Sélectionner une opération](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Pour afficher des détails sur l’erreur, cliquez sur un événement **Erreur**.

    ![Événement erreur](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Consultez l’article [Azure Insight cmdlets](https://msdn.microsoft.com/library/mt282452.aspx) (Applets de commande Azure Insight) pour plus d’informations sur les applets de commande PowerShell que vous pouvez utiliser pour ajouter, obtenir ou supprimer des alertes. Voici quelques exemples d’utilisation de l’applet de commande **Get-AlertRule** :

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Exécutez les commandes get-help suivantes afinb d’afficher des détails et des exemples pour l'applet de commande Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Si des événements de génération d’alertes apparaissent sur le panneau du portail, mais que vous ne recevez pas de notification par courrier électronique, vérifiez que votre adresse e-mail est bien configurée de manière à recevoir des messages de la part d’expéditeurs externes. Les alertes par courrier électronique peuvent avoir été bloquées en raison de vos paramètres de messagerie.

### <a name="alerts-on-metrics"></a>Alertes relatives à des mesures
Dans Data Factory, vous pouvez capturer différentes mesures et créer des alertes associées. Vous pouvez surveiller et créer des alertes relatives aux mesures suivantes pour les tranches de votre fabrique de données :

* **Exécutions échouées**
* **Exécutions réussies**

Ces mesures sont utiles et vous permettent d’obtenir une vue d’ensemble globale des exécutions réussies et échouées dans la fabrique de données. Des mesures sont émises lors de chaque exécution de tranche de données. Toutes les heures, ces mesures sont agrégées et transférées dans votre compte de stockage. Pour activer les mesures, configurez un compte de stockage.

#### <a name="enable-metrics"></a>Activer les mesures
Pour activer les mesures, cliquez sur ce qui suit à partir du panneau **Data Factory** :

**Analyse** > **Mesures** > **Paramètres de diagnostic** > **Diagnostics**

![Lien Diagnostics](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

Dans le panneau **Diagnostics**, cliquez sur **Activé**, sélectionnez le compte de stockage, puis cliquez sur **Enregistrer**.

![Panneau Diagnostics](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Vous devrez peut-être attendre une heure maximum avant que les mesures ne soient visibles dans le panneau **Analyse**. En effet, l’agrégation des mesures s’effectue une fois par heure.

### <a name="set-up-an-alert-on-metrics"></a>Configurer une alerte relative à des mesures
Cliquez sur la mosaïque **Mesures de fabrique de données** :

![Vignette Mesures de fabrique de données](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

Dans le panneau **Mesures**, dans la barre d’outils, cliquez sur **+ Ajouter une alerte**.
![Panneau Mesures de fabrique de données &gt; Ajouter une alerte](./media/data-factory-monitor-manage-pipelines/add-alert.png)

Dans la page **Ajouter une règle d’alerte**, procédez comme suit, puis cliquez sur **OK**.

* Entrez un nom pour l’alerte (p. ex. « alerte d’échec »).
* Entrez une description pour l’alerte (p. ex. « envoyer un courrier électronique en cas d’échec »).
* Sélectionnez une mesure (« exécutions échouées » ou « exécutions réussies »).
* Spécifiez une condition et une valeur de seuil.   
* Spécifiez la période.
* Spécifiez si un courrier électronique doit être envoyé aux propriétaires, aux collaborateurs et aux lecteurs.

![Panneau Mesures de fabrique de données > Ajouter une règle d’alerte](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Une fois la règle d’alerte correctement ajoutée, le panneau se ferme et la nouvelle alerte s’affiche dans le panneau **Mesure**.

![Panneau Mesures de fabrique de données > Nouvelle alerte ajoutée](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Vous devez également voir le nombre d’alertes dans la mosaïque **Règles d’alerte**. Cliquez sur la mosaïque **Règles d’alerte**.

![Panneau Mesures de fabrique de données - Règles d’alerte](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

Dans le panneau **Règles d’alerte**, vous voyez les alertes existantes. Pour ajouter une alerte, dans la barre d’outils, cliquez sur **Ajouter une alerte**.

![Panneau Règles d’alerte](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notifications d’alerte
Une fois que la règle d’alerte correspond à la condition,vous devez recevoir un courrier électronique vous informant que l’alerte est activée. Une fois que le problème est résolu et que la condition d’alerte est caduque, vous recevez un courrier électronique signalant que l’alerte a été résolue.

Ce comportement se distingue des événements donnant lieu à l’envoi d’une notification dès qu’un échec correspondant à une règle d’alerte a lieu.

### <a name="deploy-alerts-by-using-powershell"></a>Déployer des alertes à l’aide de PowerShell
Vous pouvez déployer des alertes relatives à des mesures de la même façon que vous le faites pour les événements.

**Définition d’alerte**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

Remplacez les valeurs de *subscriptionId*, *resourceGroupName* et *dataFactoryName* figurant dans l’exemple par des valeurs appropriées.

*metricName* prend actuellement en charge deux valeurs :

* FailedRuns
* SuccessfulRuns

**Déployer l’alerte**

Pour déployer l’alerte, utilisez l’applet de commande Azure PowerShell **New-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Le message suivant devrait s’afficher après la réussite du déploiement :

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

Vous pouvez également utiliser l’applet de commande **Add-AlertRule** pour déployer une règle d’alerte. Consultez la rubrique [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) pour obtenir plus d’informations et des exemples.  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Déplacer une fabrique de données vers un autre groupe de ressources ou abonnement
Vous pouvez déplacer une fabrique de données vers un autre groupe de ressources ou abonnement à l’aide du bouton de la barre de commandes **Déplacer** situé sur la page d’accueil de votre fabrique de données.

![Déplacer la fabrique de données](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Vous pouvez également déplacer toutes les ressources associées (notamment les alertes associées à la fabrique de données) en même temps que la fabrique de données.

![Boîte de dialogue Déplacer des ressources](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
