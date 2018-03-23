---
title: Utiliser une alerte pour déclencher un runbook Azure Automation
description: Découvrez comment déclencher un runbook à exécuter quand une alerte Azure est déclenchée.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: e1fc357e654aa60b94944a93118431b944898bff
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Utiliser une alerte pour déclencher un runbook Azure Automation

Vous pouvez utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) pour surveiller des métriques de base et des journaux pour la plupart des services Azure. Vous pouvez appeler les runbooks Azure Automation à l’aide de [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou à partir d’alertes classiques pour automatiser des tâches basées sur les alertes. Cet article explique comment configurer et exécuter un runbook en utilisant des alertes.

## <a name="alert-types"></a>Types d’alertes

Vous pouvez utiliser des runbooks Automation avec trois types d’alerte :
* Alertes de métrique classiques
* Alertes de journal d’activité
* Alerte de métrique quasiment en temps réel

Quand une alerte appelle un runbook, l’appel réel est une demande HTTP POST vers le Webhook. Le corps de la demande POST contient un objet au format JSON qui contient les propriétés utiles relatives à l’alerte. Le tableau suivant contient des liens vers le schéma de la charge utile pour chaque type d’alerte :

|Alerte  |Description|Schéma de la charge utile  |
|---------|---------|---------|
|[Alerte de métrique classique](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envoie une notification lorsqu’une mesure au niveau de la plateforme remplit une condition spécifique. Par exemple, lorsque la valeur **CPU %** d’une machine virtuelle est supérieure à **90** lors des 5 dernières minutes.| [Schéma de la charge utile et alerte de métrique classique](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerte du journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envoie une notification lorsqu’un nouvel événement du journal d’activité remplit des conditions spécifiques. Par exemple, lorsqu’une opération `Delete VM` est effectuée dans **myProductionResourceGroup** ou lorsqu’un nouvel événement Azure Service Health avec un statut **Actif** apparaît.| [Schéma de la charge utile et alerte de journal d’activité](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Alerte de métrique quasiment en temps réel](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envoie une notification plus rapidement que des alertes de métrique lorsqu’une ou plusieurs mesures au niveau de la plateforme remplissent des conditions spécifiques. Par exemple, lorsque la valeur **CPU %** d’une machine virtuelle est supérieure à **90**, et que la valeur **Network In** est supérieure à **500 Mo** lors des 5 dernières minutes.| [Schéma de la charge utile et alerte de métrique quasiment en temps réel](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Les données fournies par chaque type d’alerte étant différentes, chaque type d’alerte doit être gérée différemment. Dans la section suivante, vous allez apprendre à créer un runbook pour gérer différents types d’alertes.

## <a name="create-a-runbook-to-handle-alerts"></a>Créer un runbook pour gérer les alertes

Pour utiliser Automation avec des alertes, vous avez besoin d’un runbook qui intègre une logique permettant de gérer la charge utile JSON des alertes qui est passée au runbook. L’exemple de runbook suivant doit être appelé à partir d’une alerte Azure. 

Comme décrit dans la section précédente, chaque type d’alerte a un schéma différent. Le script recueille les données du wekbhook dans le paramètre d’entrée du runbook `WebhookData` à partir d’une alerte. Il évalue ensuite la charge utile JSON pour déterminer le type d’alerte utilisé. 

Dans cet exemple, il s’agit d’une alerte depuis une machine virtuelle. Il récupère les données de la machine virtuelle à partir de la charge utile, puis utilise ces informations pour arrêter la machine virtuelle. La connexion doit être configurée dans le compte Automation sur lequel le runbook est exécuté.

Le runbook utilise le [compte d’identification](automation-create-runas-account.md) **AzureRunAsConnection** pour s’authentifier auprès d’Azure pour effectuer l’action de gestion sur la machine virtuelle.

Utilisez cet exemple pour créer un runbook appelé **Stop-AzureVmInResponsetoVMAlert**. Vous pouvez modifier le script PowerShell et l’utiliser avec de nombreuses ressources différentes.

1. Allez sur votre compte Azure Automation.
1. Sélectionnez **Runbooks** sous **AUTOMATISATION DE PROCESSUS**.
1. En haut de la liste de runbooks, sélectionnez **Ajouter un runbook**. 
1. Sur la page **Ajouter des runbooks**, sélectionnez **Création rapide**.
1. Nommez le runbook **Stop-AzureVmInResponsetoVMAlert**. Pour le type de runbook, sélectionnez **PowerShell**. Sélectionnez ensuite **Create** (Créer).  
1. Copiez l’exemple PowerShell suivant dans le volet **Modifier**. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

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
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
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
            # The schema isn't supported.
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

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
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

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Sélectionnez **Publier** pour enregistrer et publier le runbook.

## <a name="create-an-action-group"></a>Créer un groupe d’actions

Un groupe d’actions est une collection d’actions qui sont déclenchées par une alerte. Un runbook n’est qu’une des nombreuses actions que vous pouvez utiliser avec les groupes d’actions.

1. Dans le portail Azure, sélectionnez **Surveiller** > **PARAMÈTRES** > **Groupes d’actions**.
1. Sélectionnez **Ajouter un groupe d’actions** puis entrez les informations nécessaires :  
    1. Entrez un nom dans la zone **Nom du groupe d’actions**.
    1. Entrez un nom dans la zone **Nom court**. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.
    1. La zone **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Il s’agit de l’abonnement dans lequel est enregistré le groupe d’actions.
    1. Sélectionnez le Groupe de ressources dans lequel le groupe d’actions est enregistré.

Pour cet exemple, vous créez deux actions : une action de runbook et une action de notification.

### <a name="runbook-action"></a>Action de runbook

Pour créer une action de runbook dans le groupe d’action :

1. Sous **Actions**, entrez un nom d’action dans **NOM D’ACTION**. Sélectionnez **Runbook Automation** comme **TYPE D’ACTION**.
1. Sous **Détails**, sélectionnez **Modifier les détails**.  
1. Dans la page **Configurer un runbook**, sélectionnez **Utilisateur** sous **Source du runbook**.  
1. Sélectionnez votre **Abonnement** et votre **Compte Automation**, puis le runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Quand vous avez terminé, cliquez sur **OK**.

### <a name="notification-action"></a>Action de notification

Pour créer une action de notification dans le groupe d’action :

1. Sous **Actions**, entrez un nom d’action dans **NOM D’ACTION**. Sélectionnez **E-mail** comme **TYPE D’ACTION**.  
1. Sous **Détails**, sélectionnez **Modifier les détails**.  
1. Sur la page **E-mail**, entrez l’adresse e-mail à utiliser pour les notifications, puis cliquez sur **OK**. L’ajout d’une adresse e-mail en plus du runbook en tant qu’action peut être utile. Vous êtes notifié lorsque le runbook démarre.  

    Votre groupe d’actions doit ressembler à l’image suivante :

   ![Page d’ajout d’un groupe d’actions](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Sélectionnez **OK** pour créer le groupe d’actions.

Vous pouvez utiliser ce groupe d’actions dans les [alertes du journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) et les [alertes quasiment en temps réel](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) que vous avez créées.

## <a name="classic-alert"></a>Alerte classique

Les alertes classiques reposent sur des métriques et n’utilisent pas de groupes d’actions. Toutefois, elles peuvent servir de base pour des actions de runbook. 

Pour créer une alerte classique :

1. Sélectionnez **Ajouter une alerte Métrique**.
1. Nommez votre alerte de métrique **myVMCPUAlert**. Entrez une brève description de l’alerte.
1. Sélectionnez **Supérieure à** comme condition de l’alerte de métrique. Sélectionnez **10** comme valeur du **Seuil**. Sélectionnez **Au cours des cinq dernières minutes** comme valeur de **Période**.
1. Sous **Entreprendre une action**, sélectionnez **Exécuter un runbook de cette alerte**.
1. Dans la page **Configurer un runbook** page, sélectionnez **Utilisateur** pour **Source du runbook**. Choisissez votre compte Automation et sélectionnez le runbook **Stop-AzureVmInResponsetoVMAlert**. Sélectionnez **OK**.
1. Pour enregistrer la règle d’alerte, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le démarrage d’un runbook Automation avec un Webhook, consultez [Démarrer un runbook depuis un Webhook](automation-webhooks.md).
* Pour plus d’informations sur les différentes façons de démarrer un Runbook, voir [Démarrage d’un Runbook](automation-starting-a-runbook.md).
* Pour découvrir comment créer une alerte de journal d’activité, consultez [Créer des alertes de journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Pour apprendre à créer une alerte quasiment en temps réel, consultez [Créer une règle d’alerte avec le portail Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
