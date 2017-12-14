---
title: " Résoudre les alertes de machine virtuelle Azure avec les runbooks Automation | Microsoft Docs"
description: "Cet article montre comment intégrer des alertes de machine virtuelle Azure à des runbooks Azure Automation et corriger automatiquement les problèmes"
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: ef18a2de8ce62de945c49b91dc74aca7d7f408b0
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Scénario Azure Automation - Résoudre des alertes de machine virtuelle Azure
Azure Automation et Azure Virtual Machines disposent d’une nouvelle fonctionnalité qui vous permet de configurer des alertes de machine virtuelle (VM) afin d’exécuter des runbooks Automation. Cette nouvelle fonctionnalité vous permet de réaliser automatiquement une correction standard en réponse aux alertes de machine virtuelle, comme le redémarrage ou l’arrêt de la machine virtuelle.

Auparavant, lors de la création d’une règle d’alerte de machine virtuelle, vous pouviez [spécifier un webhook Automation](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) sur un runbook pour exécuter le runbook dès le déclenchement de l’alerte. Toutefois, cela vous oblige à créer le runbook, à créer le webhook pour le runbook, puis à copier et coller le webhook lors de la création de la règle d’alerte. Cette nouvelle version simplifie davantage le processus, car vous pouvez choisir directement un runbook dans une liste lors de la création d’une règle d’alerte, et choisir un compte Automation qui exécute le runbook ou créer facilement un compte.

Dans cet article, nous vous montrons à quel point il est simple de configurer une alerte de machine virtuelle Azure et un runbook Automation à exécuter chaque fois que l’alerte se déclenche. Les exemples de scénarios incluent le redémarrage d’une machine virtuelle lorsque l’utilisation de la mémoire dépasse un certain seuil en raison de la fuite de mémoire d’une application de la machine virtuelle, ou l’arrêt d’une machine virtuelle lorsque le temps utilisateur processeur se trouve sous les 1 % pendant plus d’une heure et n’est pas utilisé. Nous expliquerons également dans quelle mesure la création automatique d’un principal du service dans votre compte Automation simplifie l’utilisation des runbooks pour la correction des alertes Azure.

## <a name="create-an-alert-on-a-vm"></a>Créer une alerte sur une machine virtuelle
Procédez comme suit pour configurer une alerte permettant de lancer un runbook lorsque son seuil a été dépassé.

> [!NOTE]
> Cette version ne prend en charge que les machines virtuelles V2. La prise en charge des machines virtuelles classiques sera bientôt disponible.  
> 
> 

1. Connectez-vous au portail Azure et cliquez sur **Virtual Machines**.  
2. Sélectionnez une de vos machines virtuelles.  
3. Dans l’écran de la machine virtuelle, dans la section **Surveillance**, cliquez sur **Règles d’alerte**.
4. Dans le volet **Règles d’alerte**, cliquez sur **Ajouter une alerte**.

La page **Ajouter une règle d’alerte** s’affiche. Vous pouvez y configurer les conditions de l’alerte et choisir une ou plusieurs des options suivantes : envoyer un e-mail à une personne, utiliser un Webhook pour transférer l’alerte à un autre système et/ou exécuter un runbook Automation dans la tentative de réponse visant à résoudre le problème.

## <a name="configure-a-runbook"></a>Configurer un runbook
Pour configurer un runbook à exécuter lorsque le seuil d’alerte de la machine virtuelle est atteint, sélectionnez **Runbook Automation**. Dans le volet **Configurer le runbook**, vous pouvez sélectionner le runbook à exécuter et le compte Automation avec lequel exécuter le runbook.

![Configurer un runbook Automation et créer un nouveau compte Automation](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> Pour cette version, vous pouvez choisir un des trois runbooks fournis par le service : redémarrage, arrêt ou suppression de la machine virtuelle.  La possibilité de sélectionner d’autres runbooks ou un de vos propres runbooks sera disponible dans une version ultérieure.
> 
> 

![Runbooks à sélectionner](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

Après avoir sélectionné un des trois runbook disponibles, la liste déroulante **Compte Automation** à partir de laquelle vous pouvez sélectionner un compte Automation dans le contexte duquel le runbook s’exécutera, s’affiche. Les runbooks doivent s’exécuter dans le contexte d’un [compte Automation](automation-security-overview.md) qui se trouve dans votre abonnement Azure. Vous pouvez sélectionner un compte Automation que vous avez déjà créé, ou avoir un nouveau compte Automation créé pour vous.

Les runbooks fournis s’authentifient sur Azure à l’aide d’un principal du service. Si vous choisissez d’exécuter le runbook dans un de vos comptes Automation existants, nous créons automatiquement le principal du service pour vous. Si vous choisissez de créer un compte Automation, nous créons automatiquement le compte et le principal du service. Dans les deux cas, deux ressources sont également créées dans le compte Automation : une ressource de certificat nommée **AzureRunAsCertificate** et une ressource de connexion nommée **AzureRunAsConnection**. Les runbooks utilisent **AzureRunAsConnection** pour s’authentifier auprès d’Azure pour pouvoir effectuer l’action de gestion sur la machine virtuelle.

> [!NOTE]
> Le principal du service est créé dans le cadre de l’abonnement et attribué au rôle Collaborateur. Ce rôle est nécessaire pour que le compte soit autorisé à exécuter des runbooks Automation pour gérer les machines virtuelles Azure.  La création d’un compte Automation et/ou du principal du service est un événement unique. Une fois qu’ils sont créés, vous pouvez utiliser ce compte pour exécuter des runbooks pour d’autres alertes de machine virtuelle Azure.
> 
> 

Quand vous cliquez sur **OK**, l’alerte est configurée. Si vous avez sélectionné l’option pour créer un nouveau compte Automation, celui-ci est créé avec le principal du service.  Cette opération peut prendre quelques secondes.  

![Runbook en cours de configuration](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

Une fois la configuration terminée, vous voyez le nom du runbook dans la page **Ajouter une règle d’alerte**.

![Runbook configuré](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Cliquez sur **OK** dans la page **Ajouter une règle d’alerte**.  La règle d’alerte est créée et activée si la machine virtuelle est en cours d’exécution.

### <a name="enable-or-disable-a-runbook"></a>Activer ou désactiver un runbook
Si un runbook est configuré pour une alerte, vous pouvez le désactiver sans supprimer la configuration du runbook. Cela permet de laisser l’alerte s’exécuter et peut-être de tester certaines des règles d’alerte, puis de réactiver ultérieurement le runbook.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Créer un runbook fonctionnant avec une alerte Azure
Lorsque vous choisissez un runbook dans le cadre d’une règle d’alerte Azure, le runbook doit contenir une logique pour gérer les données d’alerte qui lui sont transmises.  Lorsqu’un runbook est configuré dans une règle d’alerte, un webhook est créé pour le runbook : ce webhook est ensuite utilisé pour démarrer le runbook chaque fois que l’alerte se déclenche.  L’appel réel pour démarrer le runbook est une demande HTTP POST vers l’URL du webhook. Le corps de la demande POST contient un objet au format JSON qui contient les propriétés utiles relatives à l’alerte.  Comme vous pouvez le voir ci-dessous, les données d’alerte contiennent des détails comme subscriptionID, resourceGroupName, resourceName, et resourceType.

### <a name="example-of-alert-data"></a>Exemple de données d’alerte
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Lorsque le service webhook Automation reçoit la requête HTTP POST, il extrait les données d’alerte et les transmet au runbook dans le paramètre d’entrée WebhookData du runbook.  Voici un exemple de runbook qui montre comment utiliser le paramètre WebhookData et extraire les données d’alerte et les utiliser pour gérer la ressource Azure qui a déclenché l’alerte.

### <a name="example-runbook"></a>Exemple de runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Résumé
Lorsque vous configurez une alerte sur une machine virtuelle Azure, vous avez maintenant la possibilité de configurer facilement un runbook Automation pour qu’il effectue automatiquement une action corrective au déclenchement de l’alerte. Pour cette version, vous pouvez choisir à partir des runbooks, de redémarrer, d’arrêter ou de supprimer une machine virtuelle en fonction de votre scénario d’alerte. Ceci n’est que le début de l’activation de scénarios où vous contrôlez les actions (notification, dépannage, correction) qui sont effectuées automatiquement au déclenchement d’une alerte.

## <a name="next-steps"></a>Étapes suivantes
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)

