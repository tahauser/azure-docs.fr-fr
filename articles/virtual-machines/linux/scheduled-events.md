---
title: "Événements planifiés pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Planifiez des événements en utilisant le service de métadonnées Azure pour vos machines virtuelles Linux."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: ericrad
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: e697a8f1160aff5774dc416c81819220c316707a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Service de métadonnées Azure : Événements planifiés pour les machines virtuelles Linux

Événements planifiés est un service de métadonnées Azure qui permet à votre application de disposer de suffisamment de temps pour se préparer à la maintenance des machines virtuelles. Il fournit des informations sur les événements de maintenance à venir (par exemple, les redémarrages), afin que votre application puisse s’y préparer et limiter les interruptions de service. Il est disponible pour tous les types de machines virtuelles Azure, notamment PaaS et IaaS sur Windows et Linux. 

Pour plus d’informations sur les événements planifiés sur Windows, consultez [Événements planifiés pour les machines virtuelles Windows](../windows/scheduled-events.md).

> [!Note] 
> Le service Événements planifiés est mis à la disposition générale dans toutes les régions Azure. Consultez la section [Version et disponibilité dans la région](#version-and-region-availability) pour obtenir des informations sur la version la plus récente.

## <a name="why-use-scheduled-events"></a>Pourquoi utiliser le service Événements planifiés ?

De nombreuses applications peuvent bénéficier d’un délai pour se préparer à la maintenance des machines virtuelles. Ce délai peut servir à effectuer des tâches propres aux applications qui améliorent la disponibilité, la fiabilité et la facilité de maintenance, notamment : 

- Point de contrôle et restauration
- Vidage des connexions
- Basculement du réplica principal
- Suppression à partir du pool d’équilibreur de charge
- Journalisation des événements
- Arrêt approprié

Avec le service Événements planifiés, votre application peut savoir quand une maintenance a lieu et déclencher des tâches pour limiter son impact.  

Le service Événements planifiés fournit des événements dans les cas d’usage suivants :

- La plateforme a lancé une maintenance (par exemple, la mise à jour du système d’exploitation hôte).
- L’utilisateur a lancé une maintenance (par exemple, un utilisateur redémarre ou redéploie une machine virtuelle).

## <a name="the-basics"></a>Concepts de base  

  Le service de métadonnées expose des informations sur les machines virtuelles en cours d’exécution en utilisant un point de terminaison REST accessible depuis la machine virtuelle. Ces informations sont disponibles via une adresse IP non routable, de façon à ce qu’elles ne soient pas exposées en dehors de la machine virtuelle.

### <a name="scope"></a>Étendue
Les événements planifiés sont remis à :

- Toutes les machines virtuelles d’un service cloud
- Toutes les machines virtuelles d’un groupe à haute disponibilité
- Toutes les machines virtuelles d’un groupe de placement de groupe identique 

Par conséquent, vérifiez le champ `Resources` de l’événement pour identifier les machines virtuelles concernées.

### <a name="endpoint-discovery"></a>Découverte de point de terminaison
Pour les machines virtuelles compatibles avec le réseau virtuel, le service de métadonnées est disponible à partir d’une adresse IP non routable statique, `169.254.169.254`. Le point de terminaison complet de la dernière version des événements planifiés est : 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Si la machine virtuelle n’est pas créée au sein d’un réseau virtuel, ce qui est habituellement le cas pour les services cloud et les machines virtuelles classiques, une logique supplémentaire est nécessaire pour découvrir l’adresse IP à utiliser. Reportez-vous à cet exemple pour savoir comment [découvrir le point de terminaison hôte](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Version et disponibilité dans la région
Les versions du service Événements planifiés sont gérées. Ces versions sont obligatoires et la version actuelle est `2017-08-01`.

| Version | Type de version | Régions | Notes de publication | 
| - | - | - | - | 
| 2017-08-01 | Disponibilité générale | Tous | <li> Suppression du trait de soulignement ajouté au début des noms de ressources pour les machines virtuelles IaaS<br><li>Spécification d’en-tête de métadonnées appliquée à toutes les requêtes | 
| 2017-03-01 | VERSION PRÉLIMINAIRE | Tous | <li>Version initiale


> [!NOTE] 
> Les préversions précédentes du service Evénements planifiés prenaient en charge {latest} en tant que version de l’api. Ce format n’est plus pris en charge et sera déconseillé à l’avenir.

### <a name="enabling-and-disabling-scheduled-events"></a>Activation et désactivation du service Événements planifiés
Le service Événements planifiés est activé pour votre service la première fois que vous faites une requête d’événements. Attendez-vous à ce que la réponse à votre première demande ait un retard pouvant atteindre deux minutes.

Le service Événements planifiés est désactivé pour votre service, s’il ne fait aucune requête pendant 24 heures.

### <a name="user-initiated-maintenance"></a>Maintenance lancée par l’utilisateur
La maintenance de machine virtuelle lancée par l’utilisateur via le portail Azure, l’API, l’interface de ligne de commande ou PowerShell entraîne un événement planifié. Cela vous permet de tester la logique de préparation de la maintenance dans votre application et à cette dernière de se préparer à la maintenance lancée par l’utilisateur.

Si vous redémarrez une machine virtuelle, un événement de type `Reboot` est planifié. Si vous redéployez une machine virtuelle, un événement de type `Redeploy` est planifié.

## <a name="use-the-api"></a>Utilisation de l’API

### <a name="headers"></a>headers
Quand vous interrogez le service de métadonnées, vous devez fournir l’en-tête `Metadata:true` pour garantir que la requête n’a pas été redirigée involontairement. L’en-tête `Metadata:true` est obligatoire pour toutes les requêtes d’événements planifiés. L’absence d’en-tête dans la requête génère une réponse « Requête incorrecte » du service de métadonnées.

### <a name="query-for-events"></a>Rechercher des événements
Vous pouvez rechercher des événements planifiés en effectuant l’appel suivant :

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Une réponse contient un tableau d’événements planifiés. Un tableau vide signifie qu’il n’y a actuellement aucun événement planifié.
S’il existe des événements planifiés, la réponse contient un tableau d’événements. 
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
| Type d’événement | Impact provoqué par cet événement. <br><br> Valeurs : <br><ul><li> `Freeze` : la machine virtuelle est planifiée pour être mise en pause pendant quelques secondes. Le processeur est mis en pause, mais cela n’a aucun impact sur la mémoire, les fichiers ouverts ou les connexions réseau. <li>`Reboot` : la machine virtuelle est planifiée pour redémarrer. (La mémoire non persistante est perdue.) <li>`Redeploy` : la machine virtuelle est planifiée pour être déplacée sur un autre nœud. (Les disques éphémères sont perdus.) |
| ResourceType | Type de ressource affecté par cet événement. <br><br> Valeurs : <ul><li>`VirtualMachine`|
| Ressources| Liste de ressources affectée par cet événement. Elle contient à coup sûr des machines d’au plus un [domaine de mise à jour](manage-availability.md), mais elle peut tout aussi bien ne pas contenir toutes les machines de ce domaine. <br><br> Exemple : <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | État de cet événement. <br><br> Valeurs : <ul><li>`Scheduled` : cet événement est planifié pour démarrer après l’heure spécifiée dans la propriété `NotBefore`.<li>`Started` : cet événement a démarré.</ul> Aucun état `Completed` ou similaire n’est fourni. L’événement n’est plus renvoyé lorsqu’il est terminé.
| NotBefore| Heure après laquelle cet événement peut démarrer. <br><br> Exemple : <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Planification d’événement
Chaque événement est planifié à un moment donné dans le futur (délai minimum), en fonction de son type. Cette heure est reflétée dans la propriété `NotBefore` d’un événement. 

|Type d’événement  | Préavis minimal |
| - | - |
| Freeze| 15 minutes |
| Reboot | 15 minutes |
| Redeploy | 10 minutes |

### <a name="start-an-event"></a>Démarrer un événement 

Une fois que vous avez pris connaissance d’un événement à venir et que vous avez mis en place une logique d’arrêt appropriée, vous pouvez approuver l’événement en suspens en effectuant un appel `POST` au service de métadonnées avec `EventId`. Cet appel indique à Azure qu’il peut raccourcir l’heure de notification minimale (quand c’est possible). 

Voici un exemple de code JSON attendu dans le corps de la requête `POST`. La requête doit contenir une liste de `StartRequests`. Chaque `StartRequest` contient le paramètre `EventId` correspondant à l’événement que vous souhaitez envoyer :
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Exemple Bash
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Le fait d’accuser réception d’un événement lui permet de poursuivre pour tous les éléments `Resources` qu’il contient, et pas seulement pour la machine virtuelle qui en accuse réception. Vous pouvez donc choisir de désigner une amorce de début pour coordonner l’accusé de réception, qui peut être simplement la première machine du champ `Resources`.

## <a name="python-sample"></a>Exemple de code Python 

L’exemple suivant interroge le service de métadonnées pour obtenir les événements planifiés et approuve chaque événement en suspens :

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Étapes suivantes 
- Regardez la vidéo sur le service [Événements planifiés sur Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) pour voir une démonstration. 
- Passez en revue les exemples de code d’événements planifiés disponibles dans le [référentiel Github d’événements planifiés de métadonnées d’instance Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Apprenez-en davantage sur les API disponibles dans le [service de métadonnées d’instance](instance-metadata-service.md).
- Découvrez plus d’informations sur la [maintenance planifiée pour les machines virtuelles Linux dans Azure](planned-maintenance.md).
