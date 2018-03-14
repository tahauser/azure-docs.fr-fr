---
title: "Événements planifiés pour les machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Événements planifiés avec le service de métadonnées Azure sur vos machines virtuelles Windows."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: ericrad
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 8f78f476e28ec04acfea9fe45d57a4c18d5db678
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Service de métadonnées Azure : Événements planifiés pour les machines virtuelles Windows

Événements planifiés est un service de métadonnées Azure qui permet à votre application de disposer de suffisamment de temps pour se préparer à la maintenance des machines virtuelles. Il fournit des informations sur les événements de maintenance à venir (par exemple les redémarrages), afin que votre application puisse s’y préparer et limiter les interruptions de service. Il est disponible pour tous les types de machines virtuelles Azure, notamment PaaS et IaaS sur Windows et Linux. 

Pour plus d’informations sur les événements planifiés sur Linux, consultez [Événements planifiés pour les machines virtuelles Linux](../linux/scheduled-events.md).

> [!Note] 
> Le service Événements planifiés est mis à la disposition générale dans toutes les régions Azure. Consultez la section [Version et disponibilité dans la région](#version-and-region-availability) pour obtenir des informations sur la version la plus récente.

## <a name="why-scheduled-events"></a>Pourquoi choisir les événements planifiés ?

De nombreuses applications peuvent bénéficier d’un délai pour se préparer à la maintenance des machines virtuelles. Ce délai peut servir à effectuer des tâches propres à l’application qui améliorent la disponibilité, la fiabilité et la facilité de maintenance, notamment : 

- Point de contrôle et restauration
- Vidage des connexions
- Basculement du réplica principal 
- Suppression à partir du pool d’équilibreur de charge
- Journalisation des événements
- Arrêt approprié 

Avec Événements planifiés, votre application peut savoir quand une maintenance aura lieu et déclencher des tâches pour limiter son impact.  

Le service Événements planifiés fournit des événements dans les cas d’usage suivants :
- Maintenance lancée par la plateforme (par exemple, mise à jour du système d’exploitation hôte)
- Maintenance lancée par l’utilisateur (par exemple, un utilisateur redémarre ou redéploie une machine virtuelle)

## <a name="the-basics"></a>Concepts de base  

Le service de métadonnées Azure expose des informations sur les machines virtuelles en cours d’exécution en utilisant un point de terminaison REST accessible depuis la machine virtuelle. Les informations sont disponibles via une adresse IP non routable, de façon à ce qu’elles ne soient pas exposées en dehors de la machine virtuelle.

### <a name="endpoint-discovery"></a>Point de terminaison de découverte
Pour les machines virtuelles compatibles avec le réseau virtuel, le service de métadonnées est disponible à partir d’une adresse IP non routable statique, `169.254.169.254`. Le point de terminaison complet de la dernière version des événements planifiés est : 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Si la machine virtuelle n’est pas créée au sein d’un réseau virtuel, les cas par défaut pour les services cloud et les machines virtuelles classiques, une logique supplémentaire est nécessaire pour découvrir l’adresse IP à utiliser. Reportez-vous à cet exemple pour savoir comment [découvrir le point de terminaison hôte](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Version et disponibilité dans la région
Les versions du service Événements planifiés sont gérées. Les versions sont obligatoires et la version actuelle est `2017-08-01`.

| Version | Type de version | Régions | Notes de publication | 
| - | - | - | - |
| 2017-08-01 | Disponibilité générale | Tous | <li> Suppression du trait de soulignement ajouté au début des noms de ressources pour les machines virtuelles IaaS<br><li>Spécification d’en-tête de métadonnées appliquée à toutes les requêtes | 
| 2017-03-01 | VERSION PRÉLIMINAIRE | Tous |<li>Version initiale

> [!NOTE] 
> Les préversions précédentes des événements planifiés prenaient en charge {dernière version} en tant que version de l’api. Ce format n’est plus pris en charge et sera déconseillé à l’avenir.

### <a name="enabling-and-disabling-scheduled-events"></a>Activation et désactivation du service Événements planifiés
Le service Événements planifiés est activé pour votre service la première fois que vous faites une requête d’événements. Attendez-vous à ce que la réponse à votre première demande ait un retard pouvant atteindre deux minutes.

Le service Événements planifiés est désactivé pour votre service, s’il ne fait aucune requête pendant 24 heures.

### <a name="user-initiated-maintenance"></a>Maintenance initiée par l’utilisateur
La maintenance de machine virtuelle initiée par l’utilisateur via le portail Azure, l’API, l’interface CLI ou PowerShell entraîne un événement planifié. Cela vous permet de tester la logique de préparation de la maintenance dans votre application et permet à votre application de se préparer à la maintenance initiée par l’utilisateur.

Le redémarrage d’une machine virtuelle planifie un événement de type `Reboot`. Le redéploiement d’une machine virtuelle planifie un événement de type `Redeploy`.

## <a name="using-the-api"></a>Utilisation de l’API

### <a name="headers"></a>headers
Quand vous interrogez le service de métadonnées, vous devez fournir l’en-tête `Metadata:true` pour garantir que la demande n’a pas été redirigée involontairement. L’en-tête `Metadata:true` est obligatoire pour toutes les requêtes d’événements planifiés. L’absence d’en-tête dans la requête génère une réponse « Requête incorrecte » du service de métadonnées.

### <a name="query-for-events"></a>Rechercher des événements
Vous pouvez rechercher des événements planifiés simplement en effectuant l’appel suivant :

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Une réponse contient un tableau d’événements planifiés. Un tableau vide signifie qu’il n’y a actuellement aucun événement planifié.
S'il existe des événements planifiés, la réponse contient un tableau d’événements : 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Propriétés de l’événement
|Propriété  |  Description |
| - | - |
| EventId | GUID pour cet événement. <br><br> Exemple : <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Type d’événement | Impact provoqué par cet événement. <br><br> Valeurs : <br><ul><li> `Freeze` : une pause de quelques secondes est planifiée pour la machine virtuelle. Le processeur est mis en pause, mais cela n’a aucun impact sur la mémoire, les fichiers ouverts ou les connexions réseau. <li>`Reboot` : un redémarrage est planifié pour la machine virtuelle (la mémoire non persistante est effacée). <li>`Redeploy` : un déplacement vers un autre nœud est planifié pour la machine virtuelle (le contenu des disques éphémères est perdu). |
| ResourceType | Type de ressource affecté par cet événement. <br><br> Valeurs : <ul><li>`VirtualMachine`|
| Ressources| Liste des ressources affectées par cet événement. Elle contient à coup sûr des machines d’au plus un [domaine de mise à jour](manage-availability.md), mais elle peut ne pas contenir toutes les machines du domaine utilisateur. <br><br> Exemple : <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Event Status | État de cet événement. <br><br> Valeurs : <ul><li>`Scheduled` : cet événement est planifié pour démarrer après l’heure spécifiée dans la propriété `NotBefore`.<li>`Started` : cet événement a démarré.</ul> Aucun état `Completed` ou similaire n’est jamais fourni, car l’événement n’est plus retourné une fois qu’il est terminé.
| NotBefore| Heure après laquelle cet événement peut démarrer. <br><br> Exemple : <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Planification d’événement
Chaque événement est planifié à un minimum de temps dans le futur, en fonction du type d’événement. Cette heure est reflétée dans la propriété `NotBefore` d’un événement. 

|Type d’événement  | Préavis minimal |
| - | - |
| Freeze| 15 minutes |
| Reboot | 15 minutes |
| Redeploy | 10 minutes |

### <a name="event-scope"></a>Portée de l’événement     
Les événements planifiés sont remis à :        
 - Toutes les machines virtuelles d’un service cloud      
 - Toutes les machines virtuelles d’un groupe à haute disponibilité      
 - Toutes les machines virtuelles d’un groupe de placement de groupe identique.         

Par conséquent, vous devez vérifier le champ `Resources` de l’événement pour identifier les machines virtuelles qui seront affectées. 

### <a name="starting-an-event"></a>Démarrage d’un événement 

Une fois que vous avez pris connaissance d’un événement à venir et effectué votre logique d’arrêt appropriée, vous pouvez approuver l’événement en suspens en effectuant un appel de `POST` au service de métadonnées avec `EventId`. Ceci indique à Azure qu’il peut raccourcir l’heure de notification minimale (quand c’est possible). 

Voici le code json attendu dans le corps de la requête `POST`. La requête doit contenir une liste de `StartRequests`. Chaque `StartRequest` contient le `EventId` pour l’événement que vous souhaitez envoyer :
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Le fait d’accuser réception d’un événement lui permet de poursuivre pour tous les éléments `Resources` dans l’événement, et pas seulement pour la machine virtuelle qui en accuse réception. Vous pouvez donc choisir de désigner un leader pour coordonner l’accusé-réception, qui peut être simplement la première machine du champ `Resources`.


## <a name="powershell-sample"></a>Exemple de code PowerShell 

L’exemple suivant interroge le serveur de métadonnées pour obtenir les événements planifiés et approuve chaque événement en suspens.

```PowerShell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Étapes suivantes 

- Regardez une [démo sur le service Événements planifiés](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) sur Azure Friday. 
- Passez en revue les exemples de code d’Événements planifiés disponibles dans le [dépôt Github Événements planifiés de métadonnées d’instance Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Découvrez plus d’informations sur les API disponibles dans le [service de métadonnées d’instance](instance-metadata-service.md).
- Découvrez plus d’informations sur la [maintenance planifiée pour les machines virtuelles Windows dans Azure](planned-maintenance.md).
