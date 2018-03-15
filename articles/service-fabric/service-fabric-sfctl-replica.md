---
title: "CLI Azure Service Fabric : sfctl replica | Microsoft Docs"
description: "Décrit les commandes sfctl replica de l’interface de ligne de commande (CLI) Service Fabric."
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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: ba67a2a20d3f3e8e9fbccb2674cea500bfbde3fb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-replica"></a>sfctl replica
Permet de gérer les réplicas qui font partie des partitions de service.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    deployed  | Permet d’obtenir les détails d’un réplica déployé sur un nœud Service Fabric.|
|    deployed-list| Permet d’obtenir la liste des réplicas déployés sur un nœud Service Fabric.|
|    health    | Permet d’obtenir l’intégrité d’une instance de service sans état ou d’un réplica de service avec état Service Fabric.|
|    info      | Permet d’obtenir les informations sur un réplica d’une partition Service Fabric.|
|    list      | Permet d’obtenir les informations sur des réplicas d’une partition de service Service Fabric.|
|    remove    | Supprime un réplica de service exécuté sur un nœud.|
|    report-health| Envoie un rapport d’intégrité sur le réplica Service Fabric.|
|    restart   | Redémarre un réplica de service d’un service persistant exécuté sur un nœud.|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Permet d’obtenir les détails d’un réplica déployé sur un nœud Service Fabric.

Permet d’obtenir les détails du réplica déployé sur un nœud Service Fabric. Les informations incluent le type de service, le nom de service, l’opération de service en cours, l’heure/la date de démarrage de l’opération de service en cours, l’ID de partition, l’ID de réplica/instance, le chargement signalé ainsi que d’autres informations.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --partition-id [Requis]| Identité de la partition.|
| --replica-id [Requis]| Identificateur du réplica.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-replica-health"></a>sfctl replica health
Permet d’obtenir l’intégrité d’une instance de service sans état ou d’un réplica de service avec état Service Fabric.

Permet d’obtenir l’intégrité d’un réplica Service Fabric. EventsHealthStateFilter permet de filtrer la collection d’événements d’intégrité signalés dans le réplica en fonction de l’état d’intégrité.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --replica-id [Requis]| Identificateur du réplica.|
| --events-health-state-filter| Permet de filtrer la collection d’objets HealthEvent retournés en fonction de l’état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les événements qui correspondent au filtre sont retournés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est égale à 65535.|
| --timeout -t             | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                  | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                | Affiche ce message d’aide et quitte.|
| --output -o              | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query                  | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-replica-info"></a>sfctl replica info
Permet d’obtenir les informations sur un réplica d’une partition Service Fabric.

La réponse inclut l’ID, le rôle, l’état, l’intégrité, le nom du nœud, la durée de fonctionnement et d’autres détails sur le réplica.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --replica-id [Requis]| Identificateur du réplica.|
| --continuation-token  | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de continuation ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-replica-list"></a>sfctl replica list
Permet d’obtenir les informations sur des réplicas d’une partition de service Service Fabric.

Le point de terminaison GetReplicas retourne des informations sur la partition spécifiée.
La réponse inclut l’ID, le rôle, l’état, l’intégrité, le nom du nœud, la durée de fonctionnement et d’autres détails sur le réplica.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --partition-id [Requis]| Identité de la partition.|
| --continuation-token  | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Supprime un réplica de service exécuté sur un nœud.

Cette API simule un échec de réplica Service Fabric en supprimant un réplica dans un cluster Service Fabric. Cette suppression ferme le réplica, effectue la transition du réplica vers le rôle Aucun, puis supprime toutes les informations sur l’état du réplica dans le cluster. Cette API teste le chemin d’accès de suppression d’état du réplica, puis simule le chemin d’accès permanent du rapport d’erreurs via les API clientes. Avertissement : aucune vérification de sécurité n’est effectuée lorsque cette API est utilisée. Une utilisation incorrecte de cette API peut entraîner une perte de données pour les services avec état. En outre, l’indicateur forceRemove a un impact sur tous les autres réplicas hébergés dans le même processus.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --partition-id [Requis]| Identité de la partition.|
| --replica-id [Requis]| Identificateur du réplica.|
| --force-remove        | Supprime de force une application ou un service Service Fabric sans passer par la séquence d’arrêt normale. Ce paramètre permet de forcer la suppression d’une application ou d’un service pour lesquels le délai de suppression est arrivé à expiration en raison de problèmes intervenant dans le code de service qui empêchent la fermeture normale des réplicas.|
| --timeout -t          | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h             | Affiche ce message d’aide et quitte.|
| --output -o           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Redémarre un réplica de service d’un service persistant exécuté sur un nœud.

Redémarre un réplica de service d’un service persistant exécuté sur un nœud. Avertissement : aucune vérification de sécurité n’est effectuée lorsque cette API est utilisée. Une utilisation incorrecte de cette API peut entraîner une perte de disponibilité des services avec état.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --partition-id [Requis]| Identité de la partition.|
| --replica-id [Requis]| Identificateur du réplica.|
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
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
