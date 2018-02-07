---
title: "Appeler un webhook sur une alerte de journal d’activité Azure | Microsoft Docs"
description: "Routez les événements du journal d’activité à d’autres services pour les actions personnalisées. Par exemple envoyer des SMS, journaliser des bogues ou notifier une équipe via un service de chat/messagerie."
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
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Appeler un webhook sur une alerte de journal d’activité Azure
Les webhooks vous permettent d’acheminer une notification d’alerte Azure vers d’autres systèmes à des fins de post-traitement ou d’exécution d’actions personnalisées. Vous pouvez utiliser un webhook sur une alerte pour acheminer cette dernière vers des services qui envoient un SMS, consignent les bogues, avertissent une équipe par le biais de services de conversation instantanée/messagerie ou exécutent diverses autres actions. Cet article explique comment définir le webhook qui doit être appelé quand une alerte du journal d’activité Azure est déclenchée. Il montre également à quoi ressemble le contenu d’une requête HTTP POST à un webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte de métrique Azure, [consultez plutôt cette page](insights-webhooks-alerts.md). Vous pouvez également configurer une alerte de journal d’activité pour l’envoi d’un e-mail lors de l’activation.

> [!NOTE]
> Cette fonctionnalité est actuellement en version préliminaire et sera bientôt supprimée.
>
>

Vous pouvez configurer une alerte de journal d’activité à l’aide des [applets de commande Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), de [l’interface de ligne de commande multiplateforme](insights-cli-samples.md#work-with-alerts) ou de [l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). Il n’est pas possible de configurer un webhook à l’aide du portail Azure.

## <a name="authenticating-the-webhook"></a>Authentification du webhook
Le webhook peut s’authentifier à l’aide de l’une des méthodes suivantes :

1. **Autorisation par jeton** : l’URI du webhook est enregistré avec un ID de jeton, par exemple, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorisation de base** : l’URI du webhook est enregistré avec un nom d’utilisateur et un mot de passe, par exemple, `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schéma de la charge utile
L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes basées sur un journal d’activité. Ce schéma est semblable à celui utilisé par les alertes basées sur des métriques.

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

| Nom de l’élément | DESCRIPTION |
| --- | --- |
| status |Utilisé pour les alertes de métrique. Toujours défini sur « activated » pour les alertes de journal d’activité. |
| context |Contexte de l’événement. |
| activityLog | Propriétés de journal l’événement.|
| authorization |Propriétés de contrôle d’accès en fonction du rôle (RBAC) de l’événement. Il s’agit généralement des propriétés « action », « role » et « scope ». |
| action | Action capturée par l’alerte. |
| scope | Étendue de l’alerte (autrement dit, ressource).|
| channels | Opération |
| claims | Collection d’informations liées aux revendications. |
| caller |GUID ou nom de l’utilisateur ayant effectué l’opération, la revendication de nom d’utilisateur principal (UPN) ou la revendication de nom de principal du service (SPN) basée sur la disponibilité. Peut être null pour certains appels système. |
| correlationId |Généralement un GUID au format chaîne. Les événements avec correlationId appartiennent à la même action et partagent généralement un correlationId. |
| description |Description de l’alerte telle que définie lors de la création de l’alerte. |
| eventSource |Nom de l’infrastructure ou du service Azure ayant généré l’événement. |
| eventTimestamp |Heure à laquelle l’événement s’est produit. |
| eventDataId |Identificateur unique de l’événement. |
| level |L’une des valeurs suivantes : « Critical », « Error », Warning », « Informational » et « Verbose ». |
| operationName |Nom de l’opération. |
| operationId |Généralement, GUID partagé par les événements correspondant à une opération unique. |
| ResourceId |ID de ressource de la ressource affectée. |
| nom_groupe_ressources |Nom du groupe de ressources de la ressource affectée. |
| resourceProviderName |Fournisseur de la ressource affectée. |
| status |Chaîne. État de l’opération. Les valeurs courantes sont : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ». |
| subStatus |Inclut généralement le code d’état HTTP de l’appel REST correspondant. Il peut également inclure d’autres chaînes décrivant un sous-état. Les valeurs courantes sont : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No Content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not Found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service Unavailable (Code d’état HTTP : 503), Gateway Timeout (Code d’état HTTP : 504). |
| subscriptionId |ID d’abonnement Azure. |
| submissionTimestamp |Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande. |
| resourceType | Type de ressource qui a généré l’événement.|
| properties |Ensemble de paires `<Key, Value>` (par exemple, `Dictionary<String, String>`) incluant des détails sur l’événement. |

## <a name="next-steps"></a>étapes suivantes
* [En savoir plus sur le journal d’activité](monitoring-overview-activity-logs.md)
* [Exécuter des scripts Azure Automation (Runbooks) sur des alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
* [Utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
* [Utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
