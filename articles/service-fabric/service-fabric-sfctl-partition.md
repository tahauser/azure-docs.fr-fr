---
title: "CLI Azure Service Fabric : sfctl partition | Microsoft Docs"
description: "Décrit les commandes sfctl partition de l’interface de ligne de commande (CLI) Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 01dd1900fe765618e5da20bd289b9c3a021ea9a3
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-partition"></a>sfctl partition
Interroge et gère des partitions pour tout service.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    data-loss      | Cette API provoque une perte de données pour la partition spécifiée.|
|    data-loss-status  | Permet d’obtenir la progression d’une opération de perte de données dans une partition démarrée à l’aide de l’API StartDataLoss.|
|    health         | Permet d’obtenir l’intégrité de la partition Service Fabric spécifiée.|
|    info           | Permet d’obtenir des informations sur une partition Service Fabric.|
|    list           | Permet d’obtenir la liste des partitions d’un service Service Fabric.|
|    load           | Permet d’obtenir le chargement de la partition Service Fabric spécifiée.|
|    load-reset     | Réinitialise le chargement actuel d’une partition Service Fabric.|
|    quorum-loss    | Entraîne une perte de quorum pour une partition de service avec état.|
|    quorum-loss-status| Permet d’obtenir la progression d’une opération de perte de quorum dans une partition démarrée à l’aide de l’API StartQuorumLoss.|
|    recover        | Indique au cluster Service Fabric qu’il doit tenter de récupérer une partition spécifique qui est actuellement bloquée dans la perte de quorum.|
|    recover-all    | Indique au cluster Service Fabric qu’il doit tenter de récupérer les services (notamment les services système) qui sont actuellement bloqués dans la perte de quorum.|
|    report-health  | Envoie un rapport d’intégrité sur la partition Service Fabric.|
|    restart        | Cette API redémarre tout ou partie des réplicas ou instances de la partition spécifiée.|
|    restart-status | Permet d’obtenir la progression d’une opération de redémarrage de partition démarrée à l’aide de l’API StartPartitionRestart.|
|    svc-name       | Permet d’obtenir le nom du service Service Fabric d’une partition.|


## <a name="sfctl-partition-health"></a>sfctl partition health
Permet d’obtenir l’intégrité de la partition Service Fabric spécifiée.

Permet d’obtenir les informations d’intégrité de la partition spécifiée. EventsHealthStateFilter permet de filtrer la collection d’événements d’intégrité signalés dans le service en fonction de l’état d’intégrité.
ReplicasHealthStateFilter permet de filtrer la collection d’objets ReplicaHealthState de la partition. Si vous spécifiez une partition qui n’existe pas dans le magasin d’intégrité, cette cmdlet retourne une erreur.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --events-health-state-filter  | Permet de filtrer la collection d’objets HealthEvent retournés en fonction de l’état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants.                Seuls les événements qui correspondent au filtre sont retournés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8.                - All : filtre qui correspond à l’entrée ayant n’importe quelle valeur HealthState.                La valeur est égale à 65535.|
|--exclude-health-statistics   | Indique si les statistiques d’intégrité doivent être retournées en tant que partie du résultat de la requête. False par défaut. Les statistiques affichent le nombre d’entités enfants dans l’état d’intégrité OK, Warning et Error.|
| --replicas-health-state-filter| Permet de filtrer la collection d’objets ReplicaHealthState de la partition. La valeur peut être obtenue à partir des membres ou des opérations au niveau du bit sur les membres de HealthStateFilter. Seuls les réplicas qui correspondent au filtre sont retournés. Tous les réplicas sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est égale à 65535.|
| --timeout -t               | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                    | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                  | Affiche ce message d’aide et quitte.|
| --output -o                | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                Valeur par défaut : json.|
| --query                    | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/. |
| --verbose                  | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-partition-info"></a>sfctl partition info
Permet d’obtenir des informations sur une partition Service Fabric.

Le point de terminaison Partitions retourne des informations sur la partition spécifiée. La réponse inclut l’ID de la partition, les informations de schéma de partitionnement, les clés prises en charge par la partition, l’état, l’intégrité et d’autres détails sur la partition.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-partition-list"></a>sfctl partition list
Permet d’obtenir la liste des partitions d’un service Service Fabric.

Permet d’obtenir la liste des partitions d’un service Service Fabric. La réponse inclut l’ID de la partition, les informations de schéma de partitionnement, les clés prises en charge par la partition, l’état, l’intégrité et d’autres détails sur la partition.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --continuation-token| Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant.         Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --timeout -t        | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug             | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h           | Affiche ce message d’aide et quitte.|
| --output -o         | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query             | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose           | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-partition-load"></a>sfctl partition load
Permet d’obtenir le chargement de la partition Service Fabric spécifiée.

Retourne des informations sur la partition spécifiée. La réponse inclut une liste d’informations sur le chargement. Chaque information comprend un nom de métrique du chargement, une valeur et la dernière heure de rapport (UTC).

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Indique au cluster Service Fabric qu’il doit tenter de récupérer une partition spécifique qui est actuellement bloquée dans la perte de quorum.

Indique au cluster Service Fabric qu’il doit tenter de récupérer une partition spécifique qui est actuellement bloquée dans la perte de quorum. Cette opération doit être effectuée uniquement s’il est avéré que les réplicas arrêtés ne peuvent pas être récupérés. Une utilisation incorrecte de cette API peut entraîner une perte de données potentielle.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Cette API redémarre tout ou partie des réplicas ou instances de la partition spécifiée.

Cette API est particulièrement utile pour tester le basculement. Si elle est utilisée pour cibler une partition de service sans état, l’API RestartPartitionMode doit être définie sur AllReplicasOrInstances. Appelez l’API GetPartitionRestartProgress à l’aide du même identifiant OperationId pour obtenir la progression.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --operation-id [Requis]| GUID qui identifie un appel de cette API.  Celui-ci est transmis à l’API GetProgress correspondante.|
| --partition-id [Requis]| Identité de la partition.|
| --restart-partition-mode [Requis]| Définit les partitions à redémarrer.|
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --timeout -t                    | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                         | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                       | Affiche ce message d’aide et quitte.|
| --output -o                     | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                     Valeur par défaut : json.|
| --query                         | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                       | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
