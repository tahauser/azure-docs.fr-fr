---
title: "Migration d’un compte et de ressources Automation | Microsoft Docs"
description: "Cet article décrit comment déplacer un compte Automation dans Azure Automation et les ressources associées d’un abonnement vers un autre."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 40567d1b1f450c499715682cf8c22f0c79c7d0c7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-automation-account-and-resources"></a>Migration d’un compte et de ressources Automation
Pour les comptes Automation et les ressources associées (composants, Runbooks, modules, etc.) que vous avez créées dans le Portail Azure et que vous souhaitez migrer d’un groupe de ressources à un autre ou d’un abonnement à un autre, vous pouvez utiliser en toute simplicité la fonctionnalité [déplacer des ressources](../azure-resource-manager/resource-group-move-resources.md) disponible dans le Portail Azure. Toutefois, avant d’exécuter cette action, vous devez tout d’abord examiner cette [liste de vérification avant de déplacer des ressources](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) ainsi que la liste suivante, propre à Automation.  

1. Le groupe de ressources/abonnement de destination doit figurer dans la même région que la source. Cela signifie que les comptes Automation ne peuvent pas être déplacés entre des régions.
2. Lorsque vous déplacez des ressources (par exemple, Runbooks, journaux, etc.), le groupe source et le groupe cible sont verrouillés pendant la durée de l'opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes tant que le déplacement n’est pas terminé. 
3. La totalité des Runbooks ou variables faisant référence à une ressource à un ID d’abonnement de l’abonnement existant devrait être mise à jour une fois la migration terminée.  

> [!NOTE]
> Cette fonctionnalité ne prend pas en charge les ressources Automation classiques.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Pour déplacer le compte Automation à l’aide du portail
1. À partir de votre compte Automation, cliquez sur **Déplacer** en haut de la page.<br> ![option Déplacer](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Choisissez cette option pour déplacer le compte Automation vers un autre groupe de ressources ou un autre abonnement.
3. Notez que le volet **Déplacer des ressources** présente les ressources liées à la fois à votre compte Automation et à vos groupes de ressources. Sélectionnez **l’abonnement** et le **groupe de ressources** dans les listes déroulantes, ou sélectionnez l’option **créer un groupe de ressources** et entrez un nouveau nom de groupe de ressources dans le champ fourni. 
4. Vérifiez les informations et cochez la case pour confirmer que vous *comprenez que les outils et scripts devront être mis à jour afin d’utiliser les nouveaux ID de ressources une fois les ressources déplacées*, puis cliquez sur **OK**.<br> ![Déplacer le volet des ressources](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Cette opération peut prendre plusieurs minutes. Le panneau **Notifications** affiche l’état de chaque action effectuée : validation, migration, puis confirmation que l’opération est terminée.    

## <a name="to-move-the-automation-account-using-powershell"></a>Pour déplacer le compte Automation à l’aide de PowerShell
Pour déplacer des ressources Automation existantes vers un autre groupe de ressources ou abonnement, utilisez l’applet de commande **Get-AzureRmResource** afin de récupérer le compte Automation en question, puis l’applet de commande**AzureRmResource de déplacement** afin d’effectuer le déplacement.

Le premier exemple indique comment déplacer un compte Automation vers un nouveau groupe de ressources.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

Une fois que vous exécutez l’exemple de code ci-dessus, vous devez confirmer que vous souhaitez effectuer cette action. Une fois que vous cliquez sur **Oui** et autorisez l’exécution du script, vous ne recevez plus d’autres notifications pendant la migration. 

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre *DestinationSubscriptionId* .

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Comme dans l’exemple précédent, vous devez confirmer le déplacement. 

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur le déplacement de ressources vers un nouveau groupe de ressources ou un nouvel abonnement, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md)
* Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Pour plus d’informations sur les applets de commande PowerShell permettant de gérer votre abonnement, consultez [Utilisation d’Azure PowerShell avec Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Pour plus d’informations sur les fonctionnalités du portail permettant de gérer votre abonnement, consultez [Utilisation du Portail Azure pour gérer les ressources](../azure-resource-manager/resource-group-portal.md).
