---
title: "Afficher les journaux d’activité Azure pour surveiller les ressources | Microsoft Docs"
description: "Utilisez les journaux d’activité pour passer en revue les actions et les erreurs des utilisateurs. Affiche le portail Azure, PowerShell, l’interface CLI Azure et REST."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
ms.openlocfilehash: fb6fb3f7172673be70b1a6dcfd77e42cd982e248
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Afficher les journaux d’activité pour auditer les actions sur les ressources
Les journaux d’activité vous permettent de déterminer :

* Les opérations qui ont été effectuées sur les ressources de votre abonnement
* Les utilisateurs qui ont lancé l’opération (même si les opérations lancées par un service principal ne retournent pas d’utilisateur en tant qu’appelant)
* Le moment où a eu lieu l’opération
* L’état de l’opération
* Les valeurs d’autres propriétés qui peuvent vous aider à effectuer des recherches sur l’opération

Le journal d’audit contient toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources. Il n’inclut pas les opérations de lecture (GET). Pour obtenir la liste des actions de ressource, consultez [Opérations du fournisseur de ressources Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md). Vous pouvez utiliser les journaux d’audit pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation modifie une ressource.

Les journaux d’activité sont conservés pendant 90 jours. Vous pouvez interroger une plage quelconque de dates, pour autant que la date de début ne remonte pas à plus de 90 jours dans le passé.



Vous pouvez récupérer des informations dans les journaux d’activité par le biais du portail, de PowerShell, de l’interface de ligne de commande Azure, de l’API REST Insights ou de [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portail
1. Pour afficher les journaux d’activité via le portail, sélectionnez **Surveiller**.
   
    ![sélectionner les journaux d’activité](./media/resource-group-audit/select-monitor.png)

   Pour afficher automatiquement le journal d’activité d’une ressource ou d’un groupe de ressources en particulier, sélectionnez **Journal d’activité**. Notez que le journal d’activité est automatiquement filtré sur la dernière ressource sélectionnée.
   
    ![filtrer par ressource](./media/resource-group-audit/filtered-by-resource.png)
2. Le **Journal d’activité** affiche un résumé des opérations récentes.
   
    ![afficher des actions](./media/resource-group-audit/audit-summary.png)
3. Pour limiter le nombre d’opérations affichées, sélectionnez d’autres conditions. Par exemple, l’illustration suivante indique les champs **Intervalle de temps** et **Événement lancé par** modifiés pour afficher les actions effectuées par un utilisateur ou une application au cours du mois passé. Sélectionnez **Appliquer** pour afficher les résultats de votre requête.
   
    ![définir des options de filtre](./media/resource-group-audit/set-filter.png)

4. Si vous avez besoin d’exécuter la requête ultérieurement, sélectionnez **Enregistrer** et attribuez un nom à votre requête.
   
    ![enregistrer la requête](./media/resource-group-audit/save-query.png)
5. Pour exécuter rapidement une requête, vous pouvez sélectionner une des requêtes intégrées, telles que les déploiements ayant échoué.

    ![sélectionner la requête](./media/resource-group-audit/select-quick-query.png)

   La requête sélectionnée définit automatiquement les valeurs de filtre requis.

    ![afficher les erreurs de déploiement](./media/resource-group-audit/view-failed-deployment.png)   

6. Sélectionnez l’une des opérations pour afficher un résumé de l’événement.

    ![afficher l’opération](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. Pour récupérer les entrées de journal, exécutez la commande **Get-AzureRmLog** . Vous spécifiez des paramètres supplémentaires pour filtrer la liste des entrées. Si vous ne spécifiez pas une heure de début et de fin, les entrées de la dernière heure sont retournées. Par exemple, pour récupérer les opérations d’un groupe de ressources pendant la dernière heure d’exécution :

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    L’exemple suivant montre comment utiliser le journal d’activité pour rechercher les opérations effectuées pendant une période spécifique. Les dates de début et de fin sont indiquées dans un format de date.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Vous pouvez également utiliser les fonctions de date pour spécifier la plage de dates, par exemple, les 14 derniers jours.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. En fonction de l’heure de début que vous spécifiez, les commandes précédentes peuvent retourner une longue liste d’opérations pour le groupe de ressources. Vous pouvez filtrer les résultats de votre recherche en fournissant des critères de recherche. Par exemple, si vous recherchez la manière dont une application web a été arrêtée, vous pouvez exécuter la commande suivante :

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Dans cet exemple, elle montre qu’une action d’arrêt a été effectuée par someone@contoso.com. 

  ```powershell 
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Vous pouvez rechercher les actions effectuées par un utilisateur particulier, même pour un groupe de ressources qui n’existe plus.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Vous pouvez filtrer les résultats sur les opérations ayant échoué.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Vous pouvez vous focaliser sur une erreur en examinant le message d’état pour cette entrée.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Résultat :
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Azure CLI
* Pour récupérer les entrées de journal, exécutez la commande **azure group log show** .

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>de l’API REST
Les opérations REST à utiliser avec le journal d’activité font partie de l’ [API REST Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Pour récupérer les événements du journal d’activité, consultez [Liste des événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Étapes suivantes
* Les journaux d’activité Azure sont utilisables avec Power BI pour obtenir des informations plus détaillées sur les actions de votre abonnement. Consultez le billet de blog sur [l’affichage et l’analyse des journaux d’activité Azure dans Power BI](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Pour en savoir plus sur la définition de stratégies de sécurité, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).
* Pour en savoir plus sur les commandes permettant d’afficher les opérations de déploiement, consultez [Voir les opérations de déploiement](resource-manager-deployment-operations.md).
* Pour savoir comment empêcher des suppressions sur une ressource pour tous les utilisateurs, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).
* Pour obtenir la liste des opérations disponibles pour chaque fournisseur Microsoft Azure Resource Manager, consultez [Opérations du fournisseur de ressources Azure Resource Manager](~/articles/active-directory/role-based-access-control-resource-provider-operations.md).

