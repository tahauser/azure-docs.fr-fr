---
title: "Démarrage d’un runbook Azure Automation avec un webhook | Microsoft Docs"
description: "Webhook qui permet à un client de démarrer un runbook dans Azure Automation à partir d’un appel HTTP.  Cet article décrit comment créer un webhook et l’appeler pour démarrer un runbook."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9b20237c-a593-4299-bbdc-35c47ee9e55d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: 03d1617eb64c48b6a90925ae76e1ab3ce0312ff1
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Démarrage d’un runbook Azure Automation avec un webhook
Un *webhook* vous permet de démarrer un Runbook dans Azure Automation via une simple requête HTTP. Les services externes, tels que Visual Studio Team Services, GitHub, Microsoft Operations Management Suite Log Analytics ou les applications personnalisées peuvent ainsi démarrer les runbooks sans avoir à implémenter une solution complète à l’aide de l’API Azure Automation.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Vous pouvez comparer les webhooks à d'autres méthodes de démarrage d'un Runbook dans [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Détails d'un webhook
Le tableau suivant décrit les propriétés que vous devez configurer pour un webhook.

| Propriété | Description |
|:--- |:--- |
| NOM |Vous pouvez attribuer le nom de votre choix à un webhook, car il n'apparaît pas au client.  Il est uniquement utilisé pour que vous puissiez identifier le Runbook dans Azure Automation. <br>  À titre de meilleure pratique, nommez le webhook d'après le client qui l'utilise. |
| URL |L'URL du webhook est l'adresse unique qu'un client appelle avec une méthode HTTP POST pour démarrer le Runbook lié au webhook.  Elle est générée automatiquement lorsque vous créez le webhook.  Vous ne pouvez pas spécifier d'URL personnalisée. <br> <br>  L'URL contient un jeton de sécurité qui permet que le Runbook soit appelé par un système tiers sans authentification supplémentaire. Pour cette raison, elle doit être traitée comme un mot de passe.  Pour des raisons de sécurité, vous pouvez uniquement afficher l’URL dans le portail Azure au moment de la création du webhook. Notez l'URL dans un emplacement sécurisé en vue d'une utilisation ultérieure. |
| Date d'expiration |Comme un certificat, chaque webhook possède une date d'expiration à partir de laquelle il ne peut plus être utilisé.  Cette date d’expiration peut être modifiée après la création du webhook. |
| activé |Un webhook est activé par défaut lorsqu'il est créé.  Si vous le définissez sur Disabled, aucun client ne sera en mesure de l'utiliser.  Vous pouvez définir la propriété **Enabled** lorsque vous créez le webhook ou à tout moment après qu'il a été créé. |

### <a name="parameters"></a>parameters
Un webhook peut définir les valeurs des paramètres du Runbook qui sont utilisées lorsque le Runbook est démarré par ce webhook. Le webhook doit inclure les valeurs de tous les paramètres obligatoires du Runbook et peut inclure les valeurs des paramètres optionnels. Une valeur de paramètre configurée pour un Webhook peut être modifiée même après la création du Webhook. Plusieurs webhooks liés à un même Runbook peuvent utiliser différentes valeurs de paramètres.

Lorsqu'un client démarre un Runbook à l'aide d'un webhook, il ne peut pas remplacer les valeurs de paramètres définies dans le webhook.  Pour recevoir les données du client, le Runbook peut accepter un paramètre unique appelé **$WebhookData** de type [object] qui contient les données que le client inclut dans la requête POST.

![Propriétés Webhookdata](media/automation-webhooks/webhook-data-properties.png)

L'objet **$WebhookData** possède les propriétés suivantes :

| Propriété | Description |
|:--- |:--- |
| WebhookName |Nom du webhook. |
| RequestHeader |Table de hachage contenant les en-têtes de la requête POST entrante. |
| RequestBody |Corps de la requête POST entrante.  Ceci conserve les mises en forme telles que les chaînes, JSON, XML ou les données de formulaire codées. Le Runbook doit être écrit pour fonctionner avec le format de données qui est attendu. |

Il n'existe aucune configuration du webhook requise pour prendre en charge le paramètre **$WebhookData** et le Runbook n'est pas obligé de l'accepter.  Si le Runbook ne définit pas le paramètre, tous les détails de la demande envoyée depuis le client sont ignorés.

Si vous spécifiez une valeur de $WebhookData lors de la création du Webhook, elle sera remplacée lorsque le Webhook démarrera le runbook avec les données de la requête POST du client, même si celui-ci ne fournit aucune donnée dans le corps de la requête.  Si vous démarrez un Runbook ayant $WebhookData à l'aide d'une méthode autre qu'un webhook, vous pouvez fournir une valeur pour $Webhookdata qui sera reconnue par le Runbook.  Cette valeur doit être un objet avec les mêmes [propriétés](#details-of-a-webhook) que $Webhookdata afin que le Runbook puisse l'utiliser correctement comme s’il s’agissait d’une valeur WebhookData réelle transmise par un webhook.

Par exemple, si vous démarrez le runbook suivant à partir du portail Azure et que vous souhaitez passer certains exemples WebhookData à des fins de test, étant donné que WebhookData est un objet, ils doivent être transmis en tant que valeur JSON dans l'interface utilisateur.

![Paramètre WebhookData à partir de l'interface utilisateur](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Pour le runbook ci-dessus, si vous avez les propriétés suivantes pour le paramètre WebhookData :

1. WebhookName: *MyWebhook*
2. RequestHeader: *From=Test User*
3. RequestBody: *[“VM1”, “VM2”]*

Vous pouvez alors transmettre la valeur JSON suivante dans l'interface utilisateur pour le paramètre WebhookData :  

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Test User"}, "RequestBody":"[\"VM1\",\"VM2\"]"}

![Démarrage du paramètre WebhookData à partir de l'interface utilisateur](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Les valeurs de tous les paramètres d'entrée sont enregistrés avec la tâche du Runbook.  Cela signifie qu'une entrée fournie par le client dans la requête webhook sera enregistrée et accessible à toute personne ayant accès à la tâche Automation.  Pour cette raison, soyez prudent lorsque vous incluez des informations sensibles dans les appels du webhook.
>

## <a name="security"></a>Sécurité
La sécurité d'un webhook s'appuie sur la confidentialité de son URL, laquelle contient un jeton de sécurité lui permettant d'être appelée. Azure Automation n'effectue pas d'authentification de la demande tant qu'elle est adressée à la bonne URL. Pour cette raison, les webhooks ne doivent pas être utilisés pour les Runbooks qui exécutent des fonctions très sensibles sans recourir à un autre moyen de validation de la demande.

Vous pouvez inclure une logique dans le Runbook pour déterminer qu'il a été appelé par un webhook en vérifiant la propriété **WebhookName** du paramètre $WebhookData. Le Runbook peut effectuer une validation supplémentaire en recherchant des informations spécifiques dans les propriétés **RequestHeader** ou **RequestBody**.

Une autre stratégie consiste à ce que le Runbook effectue la validation d'une condition externe lorsqu'il reçoit une demande du webhook.  Par exemple, envisagez un Runbook appelé par GitHub chaque fois qu'il est procédé à une nouvelle validation d'un référentiel GitHub.  Le Runbook peut se connecter à GitHub pour s'assurer qu'une nouvelle validation vient juste de se produire avant de continuer.

## <a name="creating-a-webhook"></a>Création d'un webhook
Utilisez la procédure suivante pour créer un webhook lié à un Runbook dans le portail Azure.

1. Sur la page **Runbooks** du Portail Azure, cliquez sur le runbook que le Webhook démarrera pour afficher sa page Détails.
2. Cliquez sur **Webhook** en haut de la page pour ouvrir la page **Ajouter un Webhook**. <br>
   ![Bouton Webhooks](media/automation-webhooks/webhooks-button.png)
3. Cliquez sur **Créer un Webhook** pour ouvrir la page **Créer un Webhook**.
4. Spécifiez un **Nom** et une **Date d’expiration** pour le webhook, ainsi que s’il doit être activé. Pour plus d'informations sur ces propriétés, consultez [Détails d'un webhook](#details-of-a-webhook) .
5. Cliquez sur l'icône de copie et appuyez sur Ctrl + C pour copier l'URL du webhook.  Puis enregistrez-la dans un endroit sûr.  **Une fois que vous avez créé le webhook,vous ne pouvez pas récupérer l’URL à nouveau.** <br>
   ![URL du webhook](media/automation-webhooks/copy-webhook-url.png)
6. Cliquez sur **Paramètres** pour fournir les valeurs des paramètres du Runbook.  Si le Runbook possède des paramètres obligatoires, vous ne pourrez pas créer le webhook, sauf si des valeurs sont fournies.
7. Cliquez sur **Créer** pour créer le webhook.

## <a name="using-a-webhook"></a>Utilisation d'un webhook
Pour utiliser un webhook après sa création, votre application cliente doit émettre une requête HTTP POST avec l'URL du webhook.  La syntaxe du webhook sera au format suivant.

    http://<Webhook Server>/token?=<Token Value>

Le client reçoit l'un des codes de réponse suivants à la requête POST.  

| Code | Texte | Description |
|:--- |:--- |:--- |
| 202 |Acceptée |La requête a été acceptée et le Runbook a été mis en file d'attente avec succès. |
| 400 |Demande incorrecte |La demande a été refusée pour l'une des raisons suivantes. <ul> <li>Le webhook a expiré.</li> <li>Le webhook est désactivé.</li> <li>Le jeton de l’URL n’est pas valide.</li>  </ul> |
| 404 |Introuvable |La demande a été refusée pour l'une des raisons suivantes. <ul> <li>Le webhook est introuvable.</li> <li>Le Runbook est introuvable.</li> <li>Le compte est introuvable.</li>  </ul> |
| 500 |Erreur interne du serveur |L'URL est valide, mais une erreur s'est produite.  Soumettez à nouveau la demande. |

En supposant que la requête aboutisse, la réponse webhook contient l'ID de travail au format JSON comme suit. Elle contient un ID de tâche unique, mais le format JSON permet des améliorations ultérieures potentielles.

    {"JobIds":["<JobId>"]}  

Le client ne peut pas déterminer l'issue du travail du Runbook ou de son état d'achèvement à partir du webhook.  Il peut déterminer ces informations à l’aide de l’ID de travail avec une autre méthode telle que [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) ou [API d’Azure Automation](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Exemple
L'exemple suivant utilise Windows PowerShell pour démarrer un Runbook avec un webhook.  Notez que n'importe quel langage qui peut effectuer une requête HTTP peut utiliser un webhook ; Windows PowerShell est uniquement utilisé ici à titre d'exemple.

Le Runbook s'attend à une liste de machines virtuelles au format JSON dans le corps de la requête. Nous allons également inclure des informations sur qui démarre le Runbook, ainsi que la date et l'heure de son démarrage dans l'en-tête de la requête.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


L'illustration suivante montre les informations d'en-tête (à l'aide d'un suivi [Fiddler](http://www.telerik.com/fiddler) ) à partir de cette requête. Cela inclut les en-têtes standard d'une requête HTTP en plus des en-têtes Date et De personnalisés que nous avons ajoutés.  Chacune de ces valeurs est disponible pour le Runbook dans la propriété **RequestHeaders** de **WebhookData**.

![Bouton Webhooks](media/automation-webhooks/webhook-request-headers.png)

L’illustration suivante montre le corps de la requête (à l’aide d’un suivi [Fiddler](http://www.telerik.com/fiddler)) qui est disponible pour le Runbook dans la propriété **RequestBody** de **WebhookData**. Il est au format JSON car il s'agit du format qui a été inclus dans le corps de la requête.     

![Bouton Webhooks](media/automation-webhooks/webhook-request-body.png)

L'illustration suivante montre la requête envoyée à partir de Windows PowerShell et sa réponse.  L'ID de travail est extrait de la réponse et converti en une chaîne.

![Bouton Webhooks](media/automation-webhooks/webhook-request-response.png)

L’exemple suivant de runbook accepte la requête de l’exemple précédent et démarre les machines virtuelles spécifiées dans le corps de la requête.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Démarrage de runbooks en réponse aux alertes Azure
Les runbooks webhook peuvent être utilisés pour réagir aux [alertes Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Les ressources dans Azure peuvent être surveillées en collectant, au moyen d’alertes Azure, des statistiques telles que les performances, la disponibilité et l’utilisation. Vous pouvez recevoir une alerte basée sur des métriques ou événements de surveillance pour vos ressources Azure ; actuellement les comptes Automation prennent uniquement en charge les métriques. Quand la valeur d’une métrique spécifiée dépasse le seuil attribué ou si l’événement configuré est déclenché, une notification est envoyée à l’administrateur ou aux co-administrateurs du service pour résoudre l’alerte. Pour plus d’informations sur les métriques et les événements, reportez-vous aux [alertes Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Outre l’utilisation des alertes Azure comme système de notification, vous pouvez aussi lancer des runbooks en réponse aux alertes. Azure Automation vous permet d’exécuter des runbooks webhook compatibles avec les alertes Azure. Quand une métrique dépasse la valeur configurée du seuil, la règle d’alerte devient active et déclenche le webhook Automation qui, à son tour, exécute le runbook.

![Webhooks](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Contexte de l’alerte
Prenons une ressource Azure, par exemple, une machine virtuelle. L’utilisation du processeur de cette machine est l’une des principales métriques de performance. Si l’utilisation du processeur est de 100 % ou qu’elle est supérieure à une certaine quantité pendant une longue période de temps, vous voudrez probablement redémarrer la machine virtuelle pour résoudre le problème. Pour ce faire, vous pouvez configurer une règle d’alerte sur la machine virtuelle avec le pourcentage UC en guise de métrique. Dans ce scénario, le pourcentage UC est choisi à titre d’exemple, mais vous pouvez configurer beaucoup d’autres métriques pour vos ressources Azure. De même, le redémarrage de la machine virtuelle est l’action choisie pour résoudre ce problème, mais vous pouvez configurer le runbook pour exécuter d’autres actions.

Quand cette règle d’alerte devient active et déclenche le runbook webhook, elle envoie le contexte de l’alerte au runbook. Le [contexte de l’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) contient des informations, notamment **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** et **Timestamp**, qui sont utilisées par le Runbook pour identifier la ressource sur laquelle il doit exécuter une action. Le contexte de l’alerte est incorporé dans le corps de l’objet **WebhookData** envoyé au runbook, et il est accessible avec la propriété **Webhook.RequestBody**

### <a name="example"></a>Exemple
Créez une machine virtuelle Azure dans votre abonnement, puis associez une [alerte pour surveiller la métrique du pourcentage UC](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Quand vous créez l’alerte, veillez à remplir le champ webhook par l’URL du webhook qui a été générée pendant la création de celui-ci.

L’exemple de runbook suivant est déclenché quand la règle d’alerte devient active et collecte les paramètres de contexte de l’alerte qui servent au runbook pour identifier la ressource sur laquelle il devra exécuter une action.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les différentes façons de démarrer un runbook, consultez l’article [Démarrage d’un Runbook](automation-starting-a-runbook.md).
* Pour plus d’informations sur l’affichage de l’état d’un travail de runbook, consultez l’article [Exécution d’un Runbook dans Azure Automation](automation-runbook-execution.md).
* Pour découvrir comment utiliser Azure Automation pour gérer les alertes Azure, consultez l’article [Résoudre des alertes de machine virtuelle Azure avec des Runbooks Automation](automation-azure-vm-alert-integration.md).
