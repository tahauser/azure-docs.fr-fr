---
title: "Schéma d’événements du journal d’activité Azure | Documents Microsoft"
description: "Comprendre le schéma d’événement pour les données émises dans le journal d’activité"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: johnkem
ms.openlocfilehash: a5c05466b21184a73d08190856e00ae95ee3727f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-activity-log-event-schema"></a>Schéma d’événements du journal d’activité
Le **Journal d’activité Azure** est un journal qui fournit un aperçu de tous les événements de niveau d’abonnement qui se sont produits dans Azure. Cet article décrit le schéma d’événements par catégorie de données.

## <a name="administrative"></a>Administratif
Cette catégorie contient l’enregistrement de toutes les opérations de création, mise à jour, suppression et action effectuées par le biais du gestionnaire de ressources. Les exemples de types d’événements que vous pouvez voir dans cette catégorie incluent « créer une machine virtuelle » et « supprimer un groupe de sécurité réseau ». Toute mesure prise par un utilisateur ou une application utilisant le gestionnaire de ressources est modélisée comme une opération sur un type de ressources en particulier. Si le type d’opération est Écrire, Supprimer ou Action, les enregistrements de début et de réussite ou d’échec de cette opération sont enregistrés dans la catégorie Administrative. La catégorie Administrative inclut également toute modification apportée à un contrôle d’accès basé sur un rôle dans un abonnement.

### <a name="sample-event"></a>Exemple d’événement
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "dd042f02-6b3e-4f79-939a-6a381ffed3c0",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| autorisation |Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| caller |Adresse e-mail de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| channels |L’une des valeurs suivantes : « Admin », « Operation ». |
| réclamations |Le jeton JWT utilisé par Active Directory pour authentifier l’utilisateur ou l’application afin d’effectuer cette opération dans le gestionnaire de ressources. |
| correlationId |Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| description |Description textuelle statique d’un événement. |
| eventDataId |Identificateur unique d’un événement. |
| httpRequest |Objet blob décrivant la requête Http. Inclut généralement clientRequestId, clientIpAddress et la méthode (méthode HTTP. Par exemple, PUT). |
| level |Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose |
| nom_groupe_ressources |Nom du groupe de ressources de la ressource affectée. |
| resourceProviderName |Nom du fournisseur de ressources de la ressource affectée. |
| ResourceId |ID de ressource de la ressource affectée. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. |
| status |Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Il s’agit généralement du code d’état HHTP de l’appel REST correspondant, mais également d’autres chaînes décrivant un sous-état, comme ces valeurs courantes : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service Unavailable (Code d’état HTTP : 503), Gateway Timeout (Code d’état HTTP : 504). |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

## <a name="service-health"></a>État d’intégrité du service
Cette catégorie contient l’enregistrement de tout incident de l’état d’intégrité du service qui se sont produits dans Azure. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « SQL Azure dans l’est des États-Unis rencontre des temps d’arrêt. » Les événements de l’état d’intégrité du service se présentent sous cinq variétés : action requise, récupération assistée, incident, maintenance, informations ou sécurité et n’apparaissent que si une ressource de votre abonnement est affectée par l’événement.

### <a name="sample-event"></a>Exemple d’événement
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>Description des propriétés
Nom de l’élément | Description
-------- | -----------
channels | Est une des valeurs suivantes : « Admin », « Operation »
correlationId | Est généralement un GUID au format chaîne. Les événements qui appartiennent à la même action uber partagent généralement le même correlationId.
description | Description de l’événement.
eventDataId | L’identificateur unique d’un événement.
eventName | Le titre de l’événement.
level | Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose
resourceProviderName | Nom du fournisseur de ressources de la ressource affectée. S’il est inconnu, cette option sera nulle.
resourceType| Le type de ressource de la ressource affectée. S’il est inconnu, cette option sera nulle.
subStatus | Généralement nul pour les événements de l’état d’intégrité du service.
eventTimestamp | Horodatage lorsque l’événement du journal a été généré et envoyé au journal d’activité.
submissionTimestamp |   Horodatage lorsque l’événement est devenu disponible dans le journal d’activité.
subscriptionId | L’abonnement Azure dans lequel l’événement est enregistré.
status | Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : actif, résolu.
operationName | Le nom de l’opération. Généralement Microsoft.ServiceHealth/incident/action.
category | « ServiceHealth »
ResourceId | ID ressource de la ressource affectée, s’il est connu. L’ID d’abonnement est fourni dans le cas contraire.
Properties.title | Le titre localisé pour cette communication. La langue par défaut est l’anglais.
Properties.communication | Les détails localisés de la communication avec le balisage HTML. L’anglais est la langue par défaut.
Properties.incidentType | valeurs possibles : AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifie l’incident associé à cet événement. Cela permet de mettre en corrélation les événements liés à un incident.
Properties.impactedServices | Un échappement blob JSON qui décrit les services et régions qui sont affectés par l’incident. Une liste de services, chacun possédant un ServiceName et une liste d’ImpactedRegions, chacune ayant un RegionName.
Properties.defaultLanguageTitle | La communication en anglais
Properties.defaultLanguageContent | La communication en anglais en tant que HTML ou texte brut
Properties.stage | Les valeurs possibles pour AssistedRecovery, ActionRequired, Information, Incident et Security sont Active, Resolved. Pour Maintenance, les valeurs possibles sont Active, Planned, InProgress, Canceled, Rescheduled, Resolved et Complete
Properties.communicationId | La communication associée à cet événement.

## <a name="alert"></a>Alerte
Cette catégorie contient l’enregistrement de toutes les activations des alertes Azure. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « % du processeur sur myVM a été supérieur à 80 pour les 5 dernières minutes. » Une variété de systèmes Azure possèdent un concept d’alertes : vous pouvez définir une règle quelconque et recevoir une notification lorsque les conditions correspondent à cette règle. Chaque fois qu’un type d’alerte Azure pris en charge « s’active » ou si les conditions sont remplies pour générer une notification, un enregistrement de l’activation est également envoyé à cette catégorie du journal d’activité.

### <a name="sample-event"></a>Exemple d’événement

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| caller | Toujours Microsoft.Insights/alertRules |
| channels | Toujours « Admin, opération » |
| réclamations | Objet blob JSON avec le type de SPN (nom principal de service) ou de ressource du moteur d’alertes. |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement d’alerte. |
| eventDataId |Identificateur unique de l'événement d’alerte. |
| level |Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose |
| nom_groupe_ressources |Nom du groupe de ressources de la ressource affectée s’il s’agit d’une alerte métrique. Pour les autres types d’alertes, ceci est le nom du groupe de ressources qui contient l’alerte elle-même. |
| resourceProviderName |Nom du fournisseur de ressources de la ressource affectée s’il s’agit d’une alerte métrique. Pour les autres types d’alertes, ceci est le nom du fournisseur de ressources pour l’alerte elle-même. |
| ResourceId | Nom de l’ID de ressource de la ressource affectée s’il s’agit d’une alerte métrique. Pour les autres types d’alertes, ceci est le nom de l’ID de ressource pour l’alerte elle-même. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. |
| status |Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Généralement, nul pour les alertes. |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

### <a name="properties-field-per-alert-type"></a>Champ Propriétés par type d’alerte
Le champ Propriétés contient des valeurs différentes en fonction de la source de l’événement d’alerte. Deux fournisseurs d’événements d’alertes courants sont les alertes du journal d’activité et les alertes métriques.

#### <a name="properties-for-activity-log-alerts"></a>Propriétés pour les alertes du journal d’activité
| Nom de l’élément | Description |
| --- | --- |
| properties.subscriptionId | L’ID d’abonnement depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.eventDataId | L’ID des données de l’événement depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.resourceGroup | Le groupe de ressources depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.resourceId | L’ID de ressource depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.eventTimestamp | L’horodatage des événements de l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.operationName | Le nom de l’opération depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| Properties.Status | L’état depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité.|

#### <a name="properties-for-metric-alerts"></a>Propriétés des alertes métriques
| Nom de l’élément | Description |
| --- | --- |
| Propriétés. RuleUri | L’ID de ressource de la règle d’alerte métrique elle-même. |
| Propriétés. Nom_règle | Le nom de la règle d’alerte métrique. |
| Propriétés. RuleDescription | La description de la règle d’alerte métrique (telle que définie dans la règle d’alerte). |
| Propriétés. Seuil | La valeur de seuil utilisée dans l’évaluation de la règle d’alerte métrique. |
| properties.WindowSizeInMinutes | La taille de la fenêtre utilisée dans l’évaluation de la règle d’alerte métrique. |
| properties.Aggregation | Le type d’agrégation défini dans la règle d’alerte métrique. |
| properties.Operator | L’opérateur conditionnel utilisé dans l’évaluation de la règle d’alerte métrique. |
| properties.MetricName | Le nom métrique utilisé dans l’évaluation de la règle d’alerte métrique. |
| properties.MetricUnit | L’unité métrique utilisée dans l’évaluation de la règle d’alerte métrique. |

## <a name="autoscale"></a>Autoscale
Cette catégorie contient l’enregistrement de tous les événements liés au fonctionnement du moteur de mise à l’échelle automatique selon les paramètres d’échelle automatique définis dans votre abonnement. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « Échec de l’action de monter en puissance de la mise à l’échelle automatique. » À l’aide de la mise à l’échelle automatique, vous pouvez automatiquement augmenter ou diminuer la taille des instances dans un type de ressource pris en charge basé sur l’heure du jour et/ou les données de charge (métriques) à l’aide d’un paramètre de mise à l’échelle automatique. Lorsque les conditions sont remplies pour monter ou descendre en puissance, les événements de démarrage réussis ou échoués sont enregistrés dans cette catégorie.

### <a name="sample-event"></a>Exemple d’événement
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| caller | Always Microsoft.Insights/autoscaleSettings |
| channels | Toujours « Admin, opération » |
| réclamations | Objet blob JSON avec le type de SPN (nom principal de service) ou de ressource du moteur de mise à l’échelle automatique. |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement de mise à l’échelle automatique. |
| eventDataId |Identificateur unique de l'événement de mise à l’échelle automatique. |
| level |Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose |
| nom_groupe_ressources |Nom du groupe de ressources du paramètre de mise à l’échelle automatique. |
| resourceProviderName |Nom du fournisseur de ressources du paramètre de mise à l’échelle automatique. |
| ResourceId |ID de ressource du paramètre de mise à l’échelle automatique. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. |
| properties.Description | Description détaillée de ce que fait le moteur de mise à l’échelle automatique. |
| properties.ResourceName | ID de ressource de la ressource affectée (la ressource sur laquelle l’action de mise à l’échelle a été effectuée) |
| properties.OldInstancesCount | Le nombre d’instances avant la prise d’effet de l’action de mise à l’échelle automatique. |
| properties.NewInstancesCount | Le nombre d’instances après la prise d’effet de l’action de mise à l’échelle automatique. |
| properties.LastScaleActionTime | Horodatage de lorsque l’action de mise à l’échelle s’est produite. |
| status |Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Généralement, nul pour la mise à l’échelle automatique. |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

## <a name="security"></a>Sécurité
Cette catégorie contient l’enregistrement de toutes les alertes générées par Azure Security Center. Voici un exemple du type d’événement que vous pouvez voir dans cette catégorie : « Suspicious double extension file executed. » (Fichier à extension double suspect exécuté.).

### <a name="sample-event"></a>Exemple d’événement
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "d4742bb8-c279-4903-9653-9858b17d0c2e",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| channels | Toujours Operation (Opération). |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement de sécurité. |
| eventDataId |Identificateur unique de l’événement de sécurité. |
| eventName |Nom convivial de l’événement de sécurité. |
| id |URI (Unique Resource Identifier) de l’événement de sécurité. |
| level |Niveau de l’événement. L’une des valeurs suivantes : Critical (Critique), Error (Erreur), Warning (Avertissement), Informational (Information) ou Verbose (Détaillé). |
| nom_groupe_ressources |Nom du groupe de ressources de la ressource. |
| resourceProviderName |Nom du fournisseur de ressources pour Azure Security Center. Toujours Microsoft.Security. |
| resourceType |Type de ressource qui a généré l’événement de sécurité, par exemple « Microsoft.Security/locations/alerts ». |
| ResourceId |ID de ressource de l’alerte de sécurité. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. Ces propriétés varient selon le type d’alerte de sécurité. Pour obtenir une description des types d’alertes qui proviennent de Security Center, consultez [cette page](../security-center/security-center-alerts-type.md). |
| properties.Severity |Niveau de gravité. Les valeurs possibles sont High (Élevé), Medium (Moyen) ou Low (Bas). |
| status |Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Généralement nul pour les événements de sécurité. |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur le journal d’activité (autrefois appelé journal d’audit)](monitoring-overview-activity-logs.md)
* [Stream the Azure Activity Log to Event Hubs (Diffuser en continu le journal d’activités Azure vers Event Hubs)](monitoring-stream-activity-logs-event-hubs.md)
