---
title: 'App Service sur Azure Stack : domaines d’erreur | Microsoft Docs'
description: Comment redistribuer Azure App Service sur Azure Stack dans les domaines d’erreur
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 851747263879aa89fabe8b168876238a004ea8b2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Comment redistribuer Azure App Service sur Azure Stack dans les domaines d’erreur

*S’applique à : systèmes intégrés Azure Stack*

Depuis la mise à jour 1802, Azure Stack prend en charge la distribution des charges de travail sur les domaines d’erreur, ce qui est essentiel à la haute disponibilité.

> [!IMPORTANT]
> Pour bénéficier de la prise en charge des domaines d’erreur, vous devez installer la mise à jour 1802 sur votre système intégré Azure Stack.  Ce document s’applique uniquement aux déploiements du fournisseur de ressources App Service qui ont été effectués avant la publication de la mise à jour 1802.  Si vous avez déployé App Service sur Azure Stack après avoir appliqué la mise à jour 1802 à Azure Stack, le fournisseur de ressources aura déjà été distribué sur les domaines d’erreur.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Rééquilibrer un fournisseur de ressources App Service sur les domaines d’erreur

Pour redistribuer les groupes identiques déployés pour le fournisseur de ressources App Service, vous devez effectuer les étapes suivantes pour chacun d’eux.  Par défaut, les noms des groupes identiques sont les suivants :

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Si aucune instance n’est déployée dans vos groupes identiques de niveau worker, il n’est pas utile de rééquilibrer ces groupes.  Les groupes identiques seront correctement équilibrés lorsque vous effectuerez une montée en charge (scale out).
>
>

1. Accédez aux groupes de machines virtuelles identiques dans le portail Administrateur Azure Stack.  Les groupes identiques existants déployés dans le cadre du déploiement d’App Service sont listés avec leur nombre d’instances.

    ![Groupes identiques Azure App Service listés dans l’interface utilisateur de Virtual Machine Scale Sets][1]

2. Ensuite, effectuez une montée en charge pour chaque groupe.  Par exemple, si le groupe identique comprend trois instances, vous devez effectuer une montée en charge pour atteindre 6 instances. Ainsi, les trois nouvelles instances seront provisionnées sur des domaines d’erreur.
    a. [Configurer l’environnement d’administration Azure Stack dans PowerShell](azure-stack-powershell-configure-admin.md) b. Utilisez cet exemple pour monter en puissance le groupe identique de machines virtuelles :
        ```powershell
                Login-AzureRMAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> L’exécution de cette étape peut prendre plusieurs heures en fonction du type de rôle et du nombre d’instances.
>
>

3. Surveillez l’état des nouvelles instances de rôle dans le panneau Rôles du portail Administration App Service.  Pour vérifier l’état d’une instance de rôle, cliquez sur le type de rôle dans la liste.

    ![Rôles d’Azure App Service sur Azure Stack][2]

4. Lorsque les nouvelles instances sont à l’état **Prêt**, revenez au panneau Groupe de machines virtuelles identiques, puis **supprimez** les anciennes instances.

5. Répétez ces étapes pour **chacun** des groupes de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

* [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
