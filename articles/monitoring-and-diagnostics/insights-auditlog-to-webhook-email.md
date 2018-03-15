---
title: "Appeler un Webhook sur une alerte de journal d’activité Azure | Microsoft Docs"
description: "Découvrez comment rediriger les événements du journal d’activité vers d’autres services pour définir des actions personnalisées, par exemple, envoyer des SMS, consigner des bogues ou notifier une équipe dans un service de conversation instantanée/messagerie."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 9872c30d123f0a7443e28dc58ee0d4e16572a390
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Appeler un Webhook sur une alerte de journal d’activité Azure
Les Webhooks permettent de rediriger une notification d’alerte Azure vers d’autres systèmes pour effectuer un post-traitement ou des actions personnalisées. Vous pouvez utiliser un Webhook sur une alerte pour acheminer cette dernière vers des services qui envoient des SMS, consignent des bogues, avertissent une équipe dans des services de conversation instantanée/messagerie ou effectuent d’autres actions. Il est également possible de configurer une alerte de journal d’activité afin d’envoyer un e-mail à chaque fois qu’une alerte est activée.

Cet article explique comment définir le Webhook à appeler quand une alerte de journal d’activité Azure se déclenche. Il montre également à quoi ressemble le contenu d’une requête HTTP POST à un webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte de métrique Azure, consultez la page [Configurer un Webhook sur une alerte de métrique Azure](insights-webhooks-alerts.md). 

> [!NOTE]
> La fonctionnalité qui prend en charge l’appel d’un Webhook sur une alerte de journal d’activité Azure est actuellement en préversion.
>
>

Vous pouvez configurer une alerte de journal d’activité à l’aide des [cmdlets Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), d’une [interface de ligne de commande multiplateforme](insights-cli-samples.md#work-with-alerts) ou des [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). Il n’est pas possible pour le moment d’utiliser le Portail Azure pour configurer une alerte de journal d’activité.

## <a name="authenticate-the-webhook"></a>Authentifier le Webhook
Le Webhook peut s’authentifier de deux façons :

* **Authentification par jeton**. L’URI du Webhook est enregistré avec un ID de jeton. Par exemple : `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Autorisation de base**. L’URI du Webhook est enregistré avec un nom d’utilisateur et un mot de passe. Par exemple : `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schéma de la charge utile
L’opération POST contient le schéma et la charge utile JSON suivants pour toutes les alertes portant sur le journal d’activité. Ce schéma est semblable à celui des alertes basées sur des métriques.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Nom de l'élément | Description |
| --- | --- |
| status |Utilisé pour les alertes de métrique. Pour les alertes de journal d’activité, toujours défini sur Activé.|
| context |Contexte de l’événement. |
| activityLog | Propriétés de journal l’événement.|
| autorisation |Propriétés de contrôle d’accès en fonction du rôle (RBAC) de l’événement. Il s’agit généralement des propriétés **action**, **role** et **scope**. |
| action | Action capturée par l’alerte. |
| scope | Étendue de l’alerte (autrement dit, la ressource).|
| channels | Opération. |
| réclamations | Collection d’informations liées aux revendications. |
| caller |GUID ou nom de l’utilisateur ayant effectué l’opération, la revendication de nom d’utilisateur principal (UPN) ou la revendication de nom de principal du service (SPN) en fonction de la disponibilité. Peut avoir la valeur Null pour certains appels système. |
| correlationId |Généralement un GUID au format chaîne. Les événements possédant un **correlationId** appartiennent à la même action globale. En général, ils ont la même valeur **correlationId**. |
| description |Description de l’alerte définie lors de sa création. |
| eventSource |Nom de l’infrastructure ou du service Azure ayant généré l’événement. |
| eventTimestamp |Moment où l’événement s’est produit. |
| eventDataId |Identificateur unique de l’événement. |
| level |L’une des valeurs suivantes : Critical (Critique), Error (Erreur), Warning (Avertissement), Informational (Information) ou Verbose (Détaillé). |
| operationName |Nom de l’opération. |
| operationId |Généralement, GUID partagé entre les événements. Il correspond la plupart du temps à une seule opération. |
| ResourceId |ID de la ressource affectée. |
| nom_groupe_ressources |Nom du groupe de ressources de la ressource affectée. |
| resourceProviderName |Fournisseur de ressources de la ressource affectée. |
| status |Valeur de type chaîne indiquant l’état de l’opération. Les valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active et Resolved. |
| subStatus |Inclut généralement le code d’état HTTP de l’appel REST correspondant. Peut également inclure d’autres chaînes décrivant un sous-état. Les valeurs courantes sont : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No Content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not Found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service Unavailable (Code d’état HTTP : 503) et Gateway Timeout (Code d’état HTTP : 504). |
| subscriptionId |ID d’abonnement Azure. |
| submissionTimestamp |Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande. |
| resourceType | Type de ressource qui a généré l’événement.|
| properties |Ensemble de paires clé/valeur contenant les détails de l’événement. Par exemple : `Dictionary<String, String>`. |

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus en détail le [journal d’activité](monitoring-overview-activity-logs.md).
* Découvrez comment [exécuter des scripts Azure Automation (runbooks) sur des alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* Découvrez comment [utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
* Découvrez comment [utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
* Découvrez comment [utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
