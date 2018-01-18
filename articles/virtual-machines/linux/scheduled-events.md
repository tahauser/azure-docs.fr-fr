---
title: "Événements planifiés pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Planifiez des événements en utilisant le service de métadonnées Azure pour vos machines virtuelles Linux."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: ae9955253647f3277729e7905baf7bb07645de42
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Service de métadonnées Azure : Événements planifiés (préversion) pour les machines virtuelles Linux

> [!NOTE] 
> Les préversions sont à votre disposition, à condition que vous acceptiez les conditions d’utilisation. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Événements planifiés sont un sous-service du service de métadonnées Azure. Il permet à votre application de disposer de suffisamment de temps pour se préparer à la maintenance des machines virtuelles. Il fournit des informations sur les événements de maintenance à venir (par exemple, les redémarrages), afin que votre application puisse s’y préparer et limiter les interruptions de service. Il est disponible pour tous les types de machines virtuelles Azure, notamment PaaS et IaaS sur Windows et Linux. 

Pour plus d’informations sur les événements planifiés sur Windows, consultez [Événements planifiés pour les machines virtuelles Windows](../windows/scheduled-events.md).

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

### <a name="discover-the-endpoint"></a>Découvrir le point de terminaison
Pour les machines virtuelles compatibles avec les réseaux virtuels, le point de terminaison complet de la dernière version des événements planifiés est : 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Si une machine virtuelle est créée au sein d’un réseau virtuel, le service de métadonnées est disponible à partir d’une adresse IP statique non routable, `169.254.169.254`.
Si la machine virtuelle n’est pas créée au sein d’un réseau virtuel, ce qui est habituellement le cas pour les services cloud et les machines virtuelles classiques, une logique supplémentaire est nécessaire pour découvrir l’adresse IP à utiliser. Reportez-vous à cet exemple pour savoir comment [découvrir le point de terminaison hôte](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Contrôle de version 
Les versions du service Événements planifiés sont gérées. Ces versions sont obligatoires et la version actuelle est `2017-08-01`.

| Version | Notes de publication | 
| - | - | 
| 2017-08-01 | <li> Suppression du trait de soulignement ajouté au début des noms de ressources pour les machines virtuelles IaaS<br><li>Spécification d’en-tête de métadonnées appliquée à toutes les requêtes | 
| 2017-03-01 | <li>Préversion publique


> [!NOTE] 
> Les préversions précédentes du service Evénements planifiés prenaient en charge {latest} en tant que version de l’api. Ce format n’est plus pris en charge et sera déconseillé à l’avenir.

### <a name="use-headers"></a>Utilisation des en-têtes
Quand vous interrogez le service de métadonnées, vous devez fournir l’en-tête `Metadata:true` pour garantir que la requête n’a pas été redirigée involontairement. L’en-tête `Metadata:true` est obligatoire pour toutes les requêtes d’événements planifiés. L’absence d’en-tête dans la requête génère une réponse « Requête incorrecte » du service de métadonnées.

### <a name="enable-scheduled-events"></a>Activer des événements planifiés
La première fois que vous effectuez une requête d’événements planifiés, Azure active implicitement la fonctionnalité sur votre machine virtuelle. Par conséquent, prévoyez un retard pouvant atteindre deux minutes dans la réponse à votre premier appel.

> [!NOTE]
> Les événements planifiés sont désactivés automatiquement pour votre service si celui-ci n’appelle pas le point de terminaison pendant une journée. Une fois les événements planifiés désactivés pour votre service, aucun événement ne sera créé pour la maintenance lancée par l’utilisateur.

### <a name="user-initiated-maintenance"></a>Maintenance lancée par l’utilisateur
La maintenance de machine virtuelle lancée par l’utilisateur via le portail Azure, l’API, l’interface de ligne de commande ou PowerShell entraîne un événement planifié. Cela vous permet de tester la logique de préparation de la maintenance dans votre application et à cette dernière de se préparer à la maintenance lancée par l’utilisateur.

Si vous redémarrez une machine virtuelle, un événement de type `Reboot` est planifié. Si vous redéployez une machine virtuelle, un événement de type `Redeploy` est planifié.

> [!NOTE] 
> Actuellement, vous pouvez planifier simultanément au plus 100 opérations de maintenance lancées par l’utilisateur.

> [!NOTE] 
> Actuellement, la maintenance lancée par l’utilisateur qui conduit à des événements planifiés n’est pas configurable. La possibilité de configuration est prévue pour une version ultérieure.

## <a name="use-the-api"></a>Utilisation de l’API

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

### <a name="event-properties"></a>Propriétés d’événement
|Propriété  |  DESCRIPTION |
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

## <a name="next-steps"></a>étapes suivantes 
- Passez en revue les exemples de code d’événements planifiés disponibles dans le [référentiel Github d’événements planifiés de métadonnées d’instance Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Apprenez-en davantage sur les API disponibles dans le [service de métadonnées d’instance](instance-metadata-service.md).
- Découvrez plus d’informations sur la [maintenance planifiée pour les machines virtuelles Linux dans Azure](planned-maintenance.md).
