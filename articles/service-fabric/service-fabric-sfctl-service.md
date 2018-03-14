---
title: "CLI Azure Service Fabric : sfctl service | Microsoft Docs"
description: "Décrit les commandes sfctl service de l’interface de ligne de commande (CLI) Service Fabric."
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
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 5b30d3732ff00e5bb79e2d58a9f0b3e5b29dedf8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-service"></a>sfctl service
Permet de créer, de supprimer et de gérer le service, les types de service et les packages de services.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    app-name       | Permet d’obtenir le nom de l’application Service Fabric d’un service.|
|    code-package-list | Permet d’obtenir la liste des packages de code déployés sur un nœud Service Fabric.|
|    create         | Crée le service Service Fabric spécifié à partir de la description.|
|    delete         | Supprime un service Service Fabric existant.|
|    deployed-type  | Permet d’obtenir les informations relatives à un type de service spécifié de l’application déployée sur un nœud dans un cluster Service Fabric.|
|    deployed-type-list| Permet d’obtenir la liste contenant les informations sur les types de service à partir des applications déployées sur un nœud d’un cluster Service Fabric.|
|    description    | Permet d’obtenir la description d’un service Service Fabric existant.|
|get-container-logs| Permet d’obtenir les journaux du conteneur déployé sur un nœud Service Fabric.|
|    health         | Permet d’obtenir l’intégrité du service Service Fabric spécifié.|
|    info           | Permet d’obtenir les informations sur le service spécifique faisant partie d’une application Service Fabric.|
|    list           | Permet d’obtenir les informations sur tous les services faisant partie de l’application spécifiée par l’ID d’application.|
|    manifest       | Permet d’obtenir le manifeste qui décrit un type de service.|
|    package-deploy | Télécharge les packages associés au manifeste de service spécifié dans le cache d’images du nœud spécifié.|
|    package-health | Permet d’obtenir les informations sur l’intégrité d’un package de services d’une application spécifique déployée pour un nœud et une application Service Fabric.|
|    package-info   | Permet d’obtenir la liste des packages de services déployés sur un nœud Service Fabric correspondant exactement au nom spécifié.|
|    package-list   | Permet d’obtenir la liste des packages de services déployés sur un nœud Service Fabric.|
|    recover        | Indique au cluster Service Fabric qu’il doit tenter de récupérer le service spécifié qui est actuellement bloqué dans la perte de quorum.|
|    report-health  | Envoie un rapport d’intégrité sur le service Service Fabric.|
|    resolve        | Résout une partition Service Fabric.|
|    type-list      | Permet d’obtenir la liste contenant les informations sur les types de service pris en charge par un type d’application approvisionnée dans un cluster Service Fabric.|
|    update         | Met à jour le service spécifié à l’aide de la description de la mise à jour donnée.|


## <a name="sfctl-service-create"></a>sfctl service create
Crée le service Service Fabric spécifié à partir de la description.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-id [Requis]| Identité de l’application parente. Il s’agit généralement de l’ID complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --name [Requis]| Nom du service. Ce doit être un enfant de l’ID d’application.           Il doit s’agir du nom complet incluant l’URI `fabric:`. Par exemple, le service `fabric:/A/B` est un enfant de l’application `fabric:/A`.|
| --service-type [Requis]| Nom du type de service.|
| --activation-mode     | Mode d’activation pour le package de services.|
| --constraints         | Contraintes de placement sous forme de chaîne. Les contraintes de placement sont des expressions booléennes sur les propriétés de nœud et permettent de restreindre un service à des nœuds particuliers en fonction des besoins du service. Par exemple, pour placer un service sur des nœuds où NodeType est bleu, spécifiez : « NodeColor == blue ».|
| --correlated-service  | Nom du service cible avec lequel effectuer une mise en corrélation.|
| --correlation         | Met le service en corrélation avec un service existant à l’aide d’une affinité d’alignement.|
| --dns-name            | Nom DNS du service à créer. Le service système DNS de Service Fabric doit être activé pour ce paramètre.|
| --instance-count      | Nombre d’instances. Cela s’applique uniquement aux services sans état.|
| --int-scheme          | Indique que le service doit être partitionné de manière uniforme dans une plage d’entiers non signés.|
| --int-scheme-count    | Nombre de partitions dans la plage de clés de type entier (pour un schéma de partition d’entier uniforme) à créer.|
| --int-scheme-high     | Fin de la plage de clés de type entier, si vous utilisez un schéma de partition d’entier uniforme.|
| --int-scheme-low      | Début de la plage de clés de type entier, si vous utilisez un schéma de partition d’entier uniforme.|
| --load-metrics        | Liste de métriques, encodée au format JSON, utilisée lors de l’équilibrage des services entre les nœuds.|
| --min-replica-set-size| Taille minimale du jeu de réplicas en tant que nombre. Cela s’applique uniquement aux services avec état.|
| --move-cost           | Spécifie le coût du déplacement du service. Valeurs possibles : « Zero », « Low », « Medium », « High ».|
| --named-scheme        | Indique que le service doit comporter plusieurs partitions nommées.|
| --named-scheme-list   | Liste de noms, encodée au format JSON, de partition du service si vous utilisez le schéma de partition nommé.|
| --no-persisted-state  | Si cet argument est défini sur True, cela indique que le service n’a pas d’état persistant stocké sur le disque local ou qu’il stocke uniquement l’état en mémoire.|
| --placement-policy-list  | Liste encodée au format JSON des stratégies de placement pour le service et les noms de domaine associés. Les stratégies peuvent être les suivantes (une ou plusieurs) : `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | Durée maximale, en secondes, pendant laquelle une partition est autorisée à être à l’état de perte de quorum. Cela s’applique uniquement aux services avec état.|
| --replica-restart-wait| Durée, en secondes, entre l’arrêt d’un réplica et la création d’un nouveau réplica. Cela s’applique uniquement aux services avec état.|
| --singleton-scheme    | Indique que le service doit avoir une seule partition ou être un service non partitionné.|
| --stand-by-replica-keep  | Durée maximale, en secondes, pendant laquelle les réplicas StandBy sont conservés avant d’être supprimés. Cela s’applique uniquement aux services avec état.|
| --stateful            | Indique qu’il s’agit d’un service avec état.|
| --stateless           | Indique qu’il s’agit d’un service sans état.|
| --target-replica-set-size| Taille cible du jeu de réplicas en tant que nombre. Cela s’applique uniquement aux services avec état.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-delete"></a>sfctl service delete
Supprime un service Service Fabric existant.

Supprime un service Service Fabric existant. Un service doit être créé avant de pouvoir être supprimé. Par défaut, Service Fabric essaie de fermer les réplicas de service sans perte de données, puis supprime le service. Toutefois, si le service rencontre des problèmes de fermeture normale des réplicas, l’opération de suppression peut prendre un certain temps ou bloquer. Utilisez l’indicateur ForceRemove facultatif pour ignorer la séquence de fermeture normale et forcer la suppression du service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --force-remove      | Supprime de force une application ou un service Service Fabric sans passer par la séquence d’arrêt normale. Ce paramètre permet de forcer la suppression d’une application ou d’un service pour lesquels le délai de suppression est arrivé à expiration en raison de problèmes intervenant dans le code de service qui empêchent la fermeture normale des réplicas.|
| --timeout -t        | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug             | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h           | Affiche ce message d’aide et quitte.|
| --output -o         | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query             | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose           | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-description"></a>sfctl service description
Permet d’obtenir la description d’un service Service Fabric existant.

Permet d’obtenir la description d’un service Service Fabric existant. Vous devez créer un service avant de pouvoir obtenir sa description.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --timeout -t        | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug             | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h           | Affiche ce message d’aide et quitte.|
| --output -o         | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query             | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose           | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-health"></a>sfctl service health
Permet d’obtenir l’intégrité du service Service Fabric spécifié.

Permet d’obtenir les informations d’intégrité du service spécifié. EventsHealthStateFilter permet de filtrer la collection d’événements d’intégrité signalés dans le service en fonction de l’état d’intégrité. Utilisez PartitionsHealthStateFilter pour filtrer la collection de partitions retournée. Si vous spécifiez un service qui n’existe pas dans le magasin d’intégrité, cette cmdlet retourne une erreur.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --events-health-state-filter | Permet de filtrer la collection d’objets HealthEvent retournés en fonction de l’état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les événements qui correspondent au filtre sont retournés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est égale à 65535.|
|--exclude-health-statistics     | Indique si les statistiques d’intégrité doivent être retournées en tant que partie du résultat de la requête. False par défaut. Les statistiques affichent le nombre d’entités enfants dans l’état d’intégrité OK, Warning et Error.|
| --partitions-health-state-filter| Permet de filtrer les objets d’état d’intégrité des partitions retournés dans le résultat de la requête d’intégrité du service en fonction de leur état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seules les partitions qui correspondent au filtre sont retournées. Toutes les partitions sont utilisées pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, l’état d’intégrité des partitions dont la valeur HealthState est OK (2) et Warning (4) est retourné. - Default : valeur par défaut. Correspond à toute valeur HealthState.                  La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est égale à 65535.|
| --timeout -t                 | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                      | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                    | Affiche ce message d’aide et quitte.|
| --output -o                  | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                  Valeur par défaut : json.|
| --query                      | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                    | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-info"></a>sfctl service info
Permet d’obtenir les informations sur le service spécifique faisant partie d’une application Service Fabric.

Retourne les informations sur le service spécifique faisant partie de l’application Service Fabric spécifiée.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures et « mon_app/app1/svc1 » dans les versions précédentes.|
| --timeout -t            | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :             json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-list"></a>sfctl service list
Permet d’obtenir les informations sur tous les services faisant partie de l’application spécifiée par l’ID d’application.

Retourne les informations sur tous les services faisant partie de l’application spécifiée par l’ID d’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --continuation-token    | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --service-type-name     | Nom du type de service utilisé pour filtrer les services correspondant à la requête.|
| --timeout -t            | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :             json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Permet d’obtenir le manifeste qui décrit un type de service.

Permet d’obtenir le manifeste qui décrit un type de service. La réponse contient le code XML du manifeste de service sous forme de chaîne.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name [Requis]| Nom du type d’application.|
| --application-type-version [Requis]| Version du type d’application.|
| --service-manifest-name [Requis]| Nom d’un manifeste de service enregistré dans le cadre d’un type d’application dans un cluster Service Fabric.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                         | Affiche ce message d’aide et quitte.|
| --output -o                       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                       Valeur par défaut : json.|
| --query                           | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-recover"></a>sfctl service recover
Indique au cluster Service Fabric qu’il doit tenter de récupérer le service spécifié qui est actuellement bloqué dans la perte de quorum.

Indique au cluster Service Fabric qu’il doit tenter de récupérer le service spécifié qui est actuellement bloqué dans la perte de quorum. Cette opération doit être effectuée uniquement si les réplicas qui sont arrêtés ne peuvent pas être récupérés. Une utilisation incorrecte de cette API peut entraîner une perte de données potentielle.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --timeout -t        | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug             | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h           | Affiche ce message d’aide et quitte.|
| --output -o         | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query             | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose           | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Résout une partition Service Fabric.

Résout une partition de service Service Fabric pour obtenir les points de terminaison des réplicas de service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Identité du service. Il s’agit généralement du nom complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --partition-key-type| Type de clé de la partition. Ce paramètre est obligatoire si le schéma de partition du service est Int64Range ou Named. Les valeurs possibles sont les suivantes. - None (1) : indique que le paramètre PartitionKeyValue n’est pas spécifié. Cela concerne les partitions dont le schéma de partitionnement est Singleton. Il s’agit de la valeur par défaut. La valeur est égale à 1. - Int64Range (2) : indique que le paramètre PartitionKeyValue est une clé de partition int64. Cela concerne les partitions dont le schéma de partitionnement est Int64Range. La valeur est égale à 2. - Named (3) : indique que le paramètre PartitionKeyValue est un nom de la partition. Cela concerne les partitions dont le schéma de partitionnement est Named. La valeur est égale à 3.|
| --partition-key-value  | Clé de partition. Ce paramètre est obligatoire si le schéma de partition du service est Int64Range ou Named.|
| --previous-rsp-version | Valeur du champ Version de la réponse qui a été reçue précédemment. Cet argument est obligatoire si l’utilisateur sait que le résultat obtenu précédemment est périmé.|
| --timeout -t        | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug             | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h           | Affiche ce message d’aide et quitte.|
| --output -o         | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query             | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose           | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-service-update"></a>sfctl service update
Met à jour le service spécifié à l’aide de la description de la mise à jour donnée.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --service-id [Requis]| Service cible à mettre à jour. Il s’agit généralement de l’ID complet du service sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si un service est nommé « fabric://mon_app/app1/svc1 », son identité est « mon_app~app1~svc1 » dans les versions 6.0 et supérieures, et « mon_app/app1/svc1 » dans les versions précédentes.|
| --constraints         | Contraintes de placement sous forme de chaîne. Les contraintes de placement sont des expressions booléennes sur les propriétés de nœud et permettent de restreindre un service à des nœuds particuliers en fonction des besoins du service. Par exemple, pour placer un service sur des nœuds où NodeType est bleu, spécifiez : « NodeColor == blue ».|
| --correlated-service  | Nom du service cible avec lequel effectuer une mise en corrélation.|
| --correlation         | Met le service en corrélation avec un service existant à l’aide d’une affinité d’alignement.|
| --instance-count      | Nombre d’instances. Cela s’applique uniquement aux services sans état.|
| --load-metrics        | Liste de métriques, encodée au format JSON, utilisée lors de l’équilibrage de charge des services entre les nœuds.|
| --min-replica-set-size| Taille minimale du jeu de réplicas en tant que nombre. Cela s’applique uniquement aux services avec état.|
| --move-cost           | Spécifie le coût du déplacement du service. Valeurs possibles : « Zero », « Low », « Medium », « High ».|
| --placement-policy-list  | Liste encodée au format JSON des stratégies de placement pour le service et les noms de domaine associés. Les stratégies peuvent être les suivantes (une ou plusieurs) : `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | Durée maximale, en secondes, pendant laquelle une partition est autorisée à être à l’état de perte de quorum. Cela s’applique uniquement aux services avec état.|
| --replica-restart-wait| Durée, en secondes, entre l’arrêt d’un réplica et la création d’un nouveau réplica. Cela s’applique uniquement aux services avec état.|
| --stand-by-replica-keep  | Durée maximale, en secondes, pendant laquelle les réplicas StandBy sont conservés avant d’être supprimés. Cela s’applique uniquement aux services avec état.|
| --stateful            | Indique que le service cible est un service avec état.|
| --stateless           | Indique que le service cible est un service sans état.|
| --target-replica-set-size| Taille cible du jeu de réplicas en tant que nombre. Cela s’applique uniquement aux services avec état.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
