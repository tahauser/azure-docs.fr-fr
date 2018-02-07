---
title: "Répondre aux alertes Azure avec un runbook Automation | Microsoft Docs"
description: "Découvrez comment déclencher un runbook à exécuter quand des alertes Azure sont déclenchées."
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Répondre aux alertes Azure avec un runbook Automation

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) fournit des métriques de base et des journaux pour la plupart des services Microsoft Azure. Vous pouvez appeler les runbooks Azure Automation à l’aide de [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou à partir d’alertes classiques pour automatiser des tâches basées sur les alertes. Cet article explique comment configurer et exécuter un runbook basé sur ces alertes.

## <a name="alert-types"></a>Types d’alertes

Les runbooks sont des actions prises en charge sur les trois types d’alertes. Quand une alerte appelle le runbook, l’appel réel est une demande HTTP POST vers le Webhook. Le corps de la demande POST contient un objet au format JSON qui contient les propriétés utiles relatives à l’alerte. Le tableau suivant contient des liens vers le schéma de la charge utile pour chaque type d’alerte :

|Alerte  |DESCRIPTION|Schéma de la charge utile  |
|---------|---------|---------|
|[Alerte de métrique classique](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Réception d’une notification lorsqu’une métrique de plateforme répond à une condition définie (par exemple, si le pourcentage d’UC d’une machine virtuelle est supérieur à 90 depuis cinq minutes).| [Schéma de la charge utile](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerte du journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Réception d’une notification quand un nouvel événement du Journal d’activité Azure répond aux conditions définies (par exemple, si une opération « Supprimer la machine virtuelle » se produit dans myProductionResourceGroup ou si un nouvel événement État du service actif s’affiche).| [Schéma de la charge utile](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Alerte de métrique quasiment en temps réel](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Recevez une notification plus rapidement que les métriques alertes quand une ou plusieurs métriques au niveau de la plateforme répondent aux conditions spécifiées (par exemple, le pourcentage d’utilisation de l’unité centrale sur une machine virtuelle est supérieur à 90 et l’entrée réseau est supérieure à 500 Mo durant les 5 dernières minutes).| [Schéma de la charge utile](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Les données fournies par chaque alerte étant différentes, chaque alerte doit être gérée différemment. Dans la section suivante, vous allez apprendre à créer un runbook pour gérer ces différents types d’alertes.

## <a name="create-a-runbook-to-handle-alerts"></a>Créer un runbook pour gérer les alertes

Pour utiliser Automation avec des alertes, vous avez besoin d’un runbook qui intègre une logique permettant de gérer la charge utile JSON des alertes qui est passée au runbook. L’exemple de runbook suivant doit être appelé à partir d’une alerte Azure. Comme décrit dans la section précédente, chaque type d’alerte a un schéma différent. Le script prend les données de Webhook dans le paramètre d’entrée de runbook `WebhookData` à partir d’une alerte et évalue la charge utile JSON pour déterminer le type d’alerte utilisé. L’exemple suivant est utilisé sur une alerte à partir d’une machine virtuelle. Il récupère les données de la machine virtuelle à partir de la charge utile, puis utilise ces informations pour arrêter la machine virtuelle. La connexion doit être configurée dans le compte Automation sur lequel le runbook est exécuté.

Le runbook utilise le [compte d’identification](automation-create-runas-account.md) **AzureRunAsConnection** pour s’authentifier auprès d’Azure afin de pouvoir effectuer l’action de gestion sur la machine virtuelle.

Vous pouvez modifier le script PowerShell suivant en vue de l’utiliser avec de nombreuses ressources.

À partir de l’exemple suivant, créez un runbook appelé **Stop-AzureVmInResponsetoVMAlert** avec l’exemple PowerShell.

1. Ouvrez votre compte Automation.
1. Cliquez sur **Runbooks** sous **AUTOMATISATION DE PROCESSUS**. La liste des runbooks s’affiche.
1. Cliquez sur le bouton **Ajouter un runbook** situé en haut de la liste. Dans la page **Ajouter des runbooks**, sélectionnez **Création rapide**.
1. Entrez « Stop-AzureVmInResponsetoVMAlert » comme **nom** de runbook, puis sélectionnez **PowerShell** comme **type de runbook**. Cliquez sur **Créer**.
1. Copiez l’exemple PowerShell suivant dans le volet d’édition. Cliquez sur **Publier** pour enregistrer et publier le runbook.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

.NOTES
   AUTHOR: Azure Automation Team
   LASTEDIT: 2017-11-22
#>

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {
        # This is the near-real-time Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $ResourceName = $AlertContext.resourceName
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
        # This is the Activity Log Alert schema
        $AlertContext = [object] (($WebhookBody.data).context).activityLog
        $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq $null) {
        # This is the original Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $status = $WebhookBody.status
    }
    else {
        # Schema not supported
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    Write-Verbose "status: $status" -Verbose
    if ($status -eq "Activated")
    {
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId
        Write-Verbose "resourceType: $ResourceType" -Verbose
        Write-Verbose "resourceName: $ResourceName" -Verbose
        Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
        Write-Verbose "subscriptionId: $SubId" -Verbose

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>Créer un groupe d’actions

Un groupe d’actions est une collection d’actions qui sont effectuées en fonction d’une alerte. Un runbook n’est qu’une des nombreuses actions qui sont disponibles avec les groupes d’actions.

1. Dans le portail, sélectionnez **Moniteur**.

1. Sélectionnez **Groupes d’actions** sous **PARAMÈTRES**.

1. Sélectionnez **Ajouter un groupe d’actions** et renseignez les champs.

1. Entrez un nom dans la zone Nom du groupe d’actions et entrez un nom dans la zone Nom court. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

1. La boîte Abonnement est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est celui dans lequel est enregistré le groupe d’actions.
Sélectionnez le Groupe de ressources dans lequel le groupe d’actions est enregistré.

Pour cet exemple, vous créez deux actions : une action de runbook et une action de notification.

### <a name="runbook-action"></a>Action de runbook

La procédure suivante crée une action de runbook dans le groupe d’actions.

1. Sous **Actions**, donnez un nom à l’action.

1. Sélectionnez **Runbook Automation** pour le **Type d’action**.

1. Sous **Détails**, sélectionnez **Modifier les détails**.

1. Dans la page **Configurer un runbook**, sélectionnez **Utilisateur** sous **Source du runbook**.

1. Choisissez vos **Abonnement** et **Compte Automation** et enfin sélectionnez le runbook que vous avez créé à l’étape précédente, appelé « Stop-AzureVmInResponsetoVMAlert ».

1. Une fois que vous avez terminé, cliquez sur **OK**.

### <a name="notification-action"></a>Action de notification

La procédure suivante crée une action de notification dans le groupe d’actions.

1. Sous **Actions**, donnez un nom à l’action.

1. Sélectionnez **Adresse e-mail** pour le **Type d’action**.

1. Sous **Détails**, sélectionnez **Modifier les détails**.

1. Dans la page **Adresse e-mail**, entrez l’adresse e-mail à notifier, puis cliquez sur **OK**. Ajouter une adresse e-mail, ainsi que le runbook en tant qu’action est utile, car vous êtes averti quand le runbook est démarré. Votre groupe d’actions doit ressembler à l’image suivante :

   ![Page d’ajout d’un groupe d’actions](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Cliquez sur **OK** pour créer le groupe d’actions.

Une fois le groupe d’actions créé, vous pouvez créer des [alertes de journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) ou des [alertes quasiment en temps réel](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) et utiliser le groupe d’actions que vous avez créé.

## <a name="classic-alert"></a>Alerte classique

Les alertes classiques reposent sur des métriques et n’utilisent pas de groupes d’actions, mais elles servent de base pour des actions de runbook. Pour créer une alerte classique, effectuez les étapes suivantes :

1. Sélectionnez **+ Ajouter une alerte Métrique**.

1. Nommez votre alerte de métrique « myVMCPUAlert » et décrivez-la brièvement.

1. Définissez la Condition de l’alerte de métrique sur « Supérieur à », fixez le Seuil à « 10 », puis définissez la Période sur « Au cours des 5 dernières minutes ».

1. Sous **Entreprendre une action**, sélectionnez **Exécuter un runbook de cette alerte**.

1. Dans la page **Configurer un runbook** page, sélectionnez **Utilisateur** pour **Source du runbook**. Choisissez votre compte Automation et sélectionnez le runbook **Stop-AzureVmInResponsetoVMAlert**. Cliquez sur **OK**, puis recliquez sur **OK** pour enregistrer la règle d’alerte.

## <a name="next-steps"></a>étapes suivantes

* Pour plus d’informations sur le démarrage de runbooks Automation avec des Webhooks, consultez [Démarrer un runbook depuis un Webhook](automation-webhooks.md).
* Pour plus d’informations sur les différentes façons de démarrer un runbook, consultez l’article [Démarrage d’un Runbook](automation-starting-a-runbook.md).
* Pour découvrir comment créer une alerte de journal d’activité, consultez [Créer des alertes de journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Pour voir comment créer une alerte quasiment en temps réel, visitez [Créer une règle d’alerte avec le portail Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).