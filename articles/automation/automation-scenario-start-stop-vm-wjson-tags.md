---
title: "Utiliser des balises au format JSON pour planifier l’état des machines virtuelles Azure | Microsoft Docs"
description: "Cet article explique comment utiliser des chaînes JSON sur des balises pour automatiser la planification du démarrage et de l’arrêt des machines virtuelles."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
ms.openlocfilehash: 9855921f4a3aa9cda8497b400d50a186d7162dc3
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Scénario Azure Automation : utilisation de balises au format JSON afin de créer une planification pour le démarrage et l’arrêt de machines virtuelles Azure
Dans la plupart des cas, les clients souhaitent planifier le démarrage et l’arrêt des machines virtuelles pour alléger les frais d’abonnement ou pour prendre en charge les exigences professionnelles et techniques.

Le scénario ci-après vous permet d’automatiser le démarrage et l’arrêt de vos machines virtuelles à l’aide d’une balise appelée Schedule au niveau d’un groupe de ressources ou d’une machine virtuelle dans Azure. Cette planification peut être configurée du dimanche au samedi, avec une heure de démarrage et une heure d’arrêt.

Nous proposons certaines options prêtes à l’emploi. Vous avez notamment vu les points suivants :

* [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) avec des paramètres de mise à l’échelle automatique qui vous permettent de diminuer ou d’augmenter la taille des instances ;
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) intégrant la fonctionnalité de planification des opérations de démarrage et d’arrêt.

Toutefois, ces options prennent uniquement en charge des scénarios spécifiques et ne sont pas applicables aux machines virtuelles IaaS (infrastructure as a service).

Lorsque la balise Schedule est appliquée à un groupe de ressources, elle s’applique également à toutes les machines virtuelles figurant dans ce groupe. En outre, si une planification est appliquée directement à une machine virtuelle, la dernière planification est prioritaire dans l’ordre suivant :

1. Planification appliquée à un groupe de ressources
2. Planification appliquée à un groupe de ressources et à une machine virtuelle dans le groupe de ressources
3. Planification appliquée à une machine virtuelle

Ce scénario sélectionne une chaîne JSON dans un format spécifié et l’ajoute en tant que valeur d’une balise appelée Schedule. Ensuite, un Runbook répertorie l’ensemble des groupes de ressources et des machines virtuelles, puis identifie les planifications pour chaque machine virtuelle basée sur les scénarios indiqués ci-dessus. Ce Runbook itère ensuite sur les machines virtuelles associées à des planifications et évalue l’action à entreprendre. Par exemple, il détermine les machines virtuelles qui doivent être interrompues, arrêtées ou ignorées.

Ces Runbooks s’authentifient à l’aide du [compte d’identification Azure](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Télécharger les Runbooks associés au scénario
Ce scénario se compose de quatre Runbooks de flux de travail PowerShell que vous pouvez télécharger à partir de la [Galerie TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) ou du référentiel [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) pour ce projet.

| Runbook | Description |
| --- | --- |
| Test-ResourceSchedule |Vérifie la planification de chaque machine virtuelle et arrête ou démarre les machines virtuelles selon la planification. |
| Add-ResourceSchedule |Ajoute la balise Schedule à une machine virtuelle ou à un groupe de ressources. |
| Update-ResourceSchedule |Modifie la balise Schedule existante en la remplaçant par une nouvelle balise. |
| Remove-ResourceSchedule |Supprime la balise Schedule d’une machine virtuelle ou d’un groupe de ressources. |

## <a name="install-and-configure-this-scenario"></a>Installer et configurer ce scénario
### <a name="install-and-publish-the-runbooks"></a>Installer et publier des Runbook
Après avoir téléchargé les Runbooks, vous pouvez les importer à l’aide de la procédure décrite dans [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Publiez chaque Runbook une fois qu’il a été correctement importé dans votre compte Automation.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Ajouter une planification au Runbook Test-ResourceSchedule
Procédez comme suit pour activer la planification relative au Runbook Test-ResourceSchedule. Il s’agit du Runbook qui vérifie quelles machines virtuelles doivent être démarrées, arrêtées ou laissées telles quelles.

1. Dans le Portail Azure, ouvrez votre compte Automation, puis cliquez sur la vignette **Runbooks** .
2. Dans le panneau **Test-ResourceSchedule**, cliquez sur la vignette **Planifications**.
3. Dans le panneau **Planifications**, cliquez sur **Ajouter une planification**.
4. Dans le panneau **Planifications**, sélectionnez **Associer une planification à votre Runbook**. Puis sélectionnez **Créer une planification**.
5. Dans le panneau **Nouvelle planification** , tapez le nom de cette planification, par exemple : *HourlyExecution*.
6. Pour la planification **Démarrer**, définissez l’heure de début sur un incrément d’heure.
7. Sélectionnez **Périodicité**, puis, dans le champ d’intervalle **Survenir chaque**, sélectionnez **1 heure**.
8. Vérifiez que l’option **Définir l’expiration** est définie sur **Non**, puis cliquez sur **Créer** pour enregistrer votre nouvelle planification.
9. Dans le panneau d’options **Planifier un Runbook**, sélectionnez **Paramètres et valeurs pour l’exécution**. Dans le panneau **Paramètres** de Test-ResourceSchedule, entrez le nom de votre abonnement dans le champ **SubscriptionName**.  Il s’agit du seul paramètre requis pour le Runbook.  Lorsque vous avez terminé, cliquez sur **OK**.

Une fois terminée, la planification du Runbook devrait ressembler à ce qui suit :

![Runbook Test-ResourceSchedule configuré](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Formater la chaîne JSON
Cette solution sélectionne une chaîne JSON avec un format spécifié et l’ajoute en tant que valeur d’une balise appelée Schedule. Ensuite, un Runbook répertorie l’ensemble des groupes de ressources et des machines virtuelles, puis identifie les planifications pour chaque machine virtuelle.

Ce Runbook itère ensuite sur les machines virtuelles associées à des planifications et vérifie les actions à entreprendre. Le code ci-après illustre un exemple de la façon dont les solutions doivent être formatées :

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

Voici certaines informations détaillées concernant cette structure :

1. Le format de cette structure JSON est optimisé pour contourner la limite de 256 caractères applicable à une valeur de balise unique dans Azure.
2. *TzId* représente le fuseau horaire de la machine virtuelle. Cet ID peut être obtenu à l’aide de la classe .NET TimeZoneInfo dans une session PowerShell :**[System.TimeZoneInfo]::GetSystemTimeZones()**.

   ![GetSystemTimeZones dans PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Les jours de la semaine sont représentés par une valeur numérique comprise entre zéro et six. La valeur zéro correspond à dimanche.
   * L’heure de début est représentée par l’attribut **S** , et sa valeur est exprimée au format 24 heures.
   * L’heure de fin ou d’arrêt est représentée par l’attribut **E** , et sa valeur est exprimée au format 24 heures.

     Si les attributs **S** et **E** présentent tous deux la valeur zéro (0), la machine virtuelle conserve l’état dans lequel elle se trouve au moment de l’évaluation.
3. Si vous souhaitez ignorer l’évaluation un jour spécifique de la semaine, n’ajoutez pas la section correspondant à ce jour-là. Dans l’exemple ci-après, l’évaluation se limite au lundi, et les autres jours de la semaine sont ignorés :

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Baliser des groupes de ressources ou des machines virtuelles
Pour arrêter des machines virtuelles, vous devez baliser ces machines ou les groupes de ressources dans lesquels elles se trouvent. Les machines virtuelles dépourvues de balise Schedule ne sont pas évaluées. Par conséquent, elles ne sont pas démarrées ni arrêtées.

Vous disposez de deux méthodes pour baliser des groupes de ressources ou des machines virtuelles avec cette solution. Vous pouvez le faire directement à partir du portail. L’autre possibilité consiste à utiliser les Runbooks Add-ResourceSchedule, Update-ResourceSchedule et Remove-ResourceSchedule.

### <a name="tag-through-the-portal"></a>Baliser par le biais du portail
Pour baliser une machine virtuelle ou un groupe de ressources dans le portail, procédez comme suit :

1. Aplatissez la chaîne JSON et vérifiez qu’elle ne contient pas d’espaces.  La chaîne JSON devrait ressembler à ceci :

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Sélectionnez l’icône **Balise** pour une machine virtuelle ou un groupe de ressources afin d’y appliquer cette planification.

   ![Option de balisage de machine virtuelle](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. Les balises sont définies à l’aide d’une paire clé/valeur. Tapez **Schedule** dans le champ **Clé**, puis collez la chaîne JSON dans le champ **Valeur**. Cliquez sur **Save**. Votre nouvelle balise doit maintenant apparaître dans la liste des balises pour votre ressource.

   ![Balise de planification de machine virtuelle](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Baliser à partir de PowerShell
Tous les Runbooks importés contiennent des informations d’aide au début du script qui décrivent comment exécuter les Runbooks directement à partir de PowerShell. Vous pouvez appeler les Runbooks Add-ScheduleResource et Update-ScheduleResource à partir de PowerShell. Pour ce faire, vous transmettez les paramètres requis qui vous permettent de créer ou de mettre à jour la balise Schedule sur une machine virtuelle ou un groupe de ressources en dehors du portail.

Pour créer, ajouter et supprimer des balises par le biais de PowerShell, vous devez commencer par [configurer votre environnement PowerShell pour Azure](/powershell/azure/overview). Après avoir effectué cette configuration, vous pouvez passer aux étapes suivantes.

### <a name="create-a-schedule-tag-with-powershell"></a>Créer une balise Schedule avec PowerShell
1. Ouvrez une session PowerShell. Puis utilisez l’exemple ci-après pour vous authentifier avec votre compte d’identification et pour spécifier un abonnement :

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Définissez une table de hachage de planification. Voici un exemple montrant comment cette table devrait être créée :

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Définissez les paramètres requis par le Runbook. Dans l’exemple suivant, nous balisons une machine virtuelle :

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Si vous balisez un groupe de ressources, supprimez le paramètre *VMName* de la table de hachage $params comme suit :

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Exécutez le Runbook Add-ResourceSchedule avec les paramètres ci-après pour créer la balise Schedule :

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Pour mettre à jour la balise d’un groupe de ressources ou d’une machine virtuelle, exécutez le Runbook **Update-ResourceSchedule** avec les paramètres suivants :

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>Supprimer une balise Schedule avec PowerShell
1. Ouvrez une session PowerShell et exécutez la commande ci-après pour vous authentifier avec votre compte d’identification et pour sélectionner et spécifier un abonnement :

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Définissez les paramètres requis par le Runbook. Dans l’exemple suivant, nous balisons une machine virtuelle :

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Si vous supprimez une balise d’un groupe de ressources, supprimez le paramètre *VMName* de la table de hachage $params comme suit :

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Exécutez le Runbook Remove-ResourceSchedule pour supprimer la balise Schedule :

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Pour mettre à jour la balise d’un groupe de ressources ou d’une machine virtuelle, exécutez le Runbook Remove-ResourceSchedule avec les paramètres suivants :

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> Nous vous recommandons de surveiller de façon proactive ces Runbooks (et les états de machine virtuelle) pour vérifier que vos machines virtuelles s’arrêtent et démarrent en conséquence.
>

Pour visualiser les détails du travail du Runbook Test-ResourceSchedule dans le Portail Azure, sélectionnez la vignette **Travaux** du Runbook. Le résumé du travail affiche les paramètres d’entrée et le flux de sortie, en plus des informations générales sur le travail et des éventuelles exceptions.

Le **Résumé du travail** inclut les messages des flux de sortie, des flux d’avertissements et des flux d’erreurs. Pour visualiser les résultats détaillés de l’exécution du Runbook, sélectionnez la vignette **Sortie** .

![Sortie Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations sur la prise en main des Runbooks de workflow PowerShell, voir [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md).
* Pour plus d’informations sur les types de Runbooks, sur leurs avantages et sur leurs limites, voir [Types de Runbooks Azure Automation](automation-runbook-types.md).
* Pour plus d’informations sur les fonctionnalités de prise en charge des scripts PowerShell, voir [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)(Prise en charge de scripts PowerShell natifs dans Azure Automation).
* Pour plus d’informations sur la journalisation et la sortie de Runbook, voir [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md).
* Pour plus d’informations sur un compte d’identification Azure et sur l’authentification de vos Runbooks à l’aide de ce compte, voir [Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md).
