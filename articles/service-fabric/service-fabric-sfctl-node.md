---
title: "CLI Azure Service Fabric : sfctl node | Microsoft Docs"
description: "Décrit les commandes sfctl node de l’interface de ligne de commande (CLI) Service Fabric."
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
ms.openlocfilehash: 50c7fe38d8bf7b14adf437f85c758e465e7d231d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-node"></a>sfctl node
Permet de gérer les nœuds qui forment un cluster.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    disable       | Désactive un nœud de cluster Service Fabric avec l’intention de désactivation spécifiée.|
|    enable        | Active un nœud de cluster Service Fabric actuellement désactivé.|
|    health        | Permet d’obtenir l’intégrité d’un nœud Service Fabric.|
|    info          | Permet d’obtenir des informations sur un nœud spécifique du cluster Service Fabric.|
|    list          | Permet d’obtenir la liste des nœuds du cluster Service Fabric.|
|    load          | Permet d’obtenir les informations de chargement d’un nœud Service Fabric.|
|    remove-state  | Informe Service Fabric que l’état persistant d’un nœud a été définitivement supprimé ou perdu.|
|    report-health | Envoie un rapport d’intégrité sur le nœud Service Fabric.|
|    restart       | Redémarre un nœud de cluster Service Fabric.|
|    transition    | Démarre ou arrête un nœud de cluster.|
|    transition-status| Permet d’obtenir la progression d’une opération démarrée à l’aide de StartNodeTransition.|


## <a name="sfctl-node-disable"></a>sfctl node disable
Désactive un nœud de cluster Service Fabric avec l’intention de désactivation spécifiée.

Désactive un nœud de cluster Service Fabric avec l’intention de désactivation spécifiée. Une fois la désactivation en cours, son intention peut être augmentée, mais pas réduite (par exemple, un nœud qui est désactivé avec l’intention Pause peut continuer à être désactivé avec Restart, mais l’inverse n’est pas vrai). Des nœuds peuvent être réactivés à l’aide de l’opération d’activation de nœud à tout moment une fois qu’ils sont désactivés. Si la désactivation n’est pas terminée, cette commande l’annule. Un nœud qui tombe en panne et redevient opérationnel en cours de désactivation doit toujours être réactivé pour que des services puissent être placés sur ce nœud.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --deactivation-intent | Décrit l’intention ou le motif de la désactivation du nœud. |
| --timeout -t       | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug            | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h          | Affiche ce message d’aide et quitte.|
| --output -o        | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query            | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose          | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-enable"></a>sfctl node enable
Active un nœud de cluster Service Fabric actuellement désactivé.

Active un nœud de cluster Service Fabric actuellement désactivé. Une fois activé, le nœud redevient une cible viable pour placer de nouveaux réplicas, et tout réplica désactivé restant sur ce nœud est réactivé.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --timeout -t       | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug            | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h          | Affiche ce message d’aide et quitte.|
| --output -o        | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query            | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose          | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-health"></a>sfctl node health
Permet d’obtenir l’intégrité d’un nœud Service Fabric.

Permet d’obtenir l’intégrité d’un nœud Service Fabric. EventsHealthStateFilter permet de filtrer la collection d’événements d’intégrité signalés dans le nœud en fonction de l’état d’intégrité. Si vous spécifiez un nœud qui n’existe pas dans le magasin d’intégrité, cette cmdlet retourne une erreur.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --events-health-state-filter| Permet de filtrer la collection d’objets HealthEvent retournés en fonction de l’état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les événements qui correspondent au filtre sont retournés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est égale à 65535.|
| --timeout -t             | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                  | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                | Affiche ce message d’aide et quitte.|
| --output -o              | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query                  | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-info"></a>sfctl node info
Permet d’obtenir des informations sur un nœud spécifique du cluster Service Fabric.

Permet d’obtenir des informations sur un nœud spécifique du cluster Service Fabric. La réponse comprend le nom, l’état, l’ID, le niveau d’intégrité et d’autres détails sur le nœud.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --timeout -t       | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug            | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h          | Affiche ce message d’aide et quitte.|
| --output -o        | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query            | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose          | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-list"></a>sfctl node list
Permet d’obtenir la liste des nœuds du cluster Service Fabric.

Permet d’obtenir la liste des nœuds du cluster Service Fabric. La réponse comprend le nom, l’état, l’ID, le niveau d’intégrité, la durée de fonctionnement et d’autres détails sur le nœud.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --continuation-token| Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse.      Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --node-status-filter| Permet de filtrer les nœuds en fonction de NodeStatus. Seuls les nœuds qui correspondent à la valeur de filtre spécifiée sont retournés. Les valeurs possibles sont les suivantes. Default : valeur par défaut.|
| --timeout -t     | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug          | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h        | Affiche ce message d’aide et quitte.|
| --output -o      | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query          | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose        | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-load"></a>sfctl node load
Permet d’obtenir les informations de chargement d’un nœud Service Fabric.

Permet de récupérer les informations sur le chargement d’un nœud Service Fabric pour toutes les mesures dont la charge ou la capacité est définie.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --timeout -t       | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug            | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h          | Affiche ce message d’aide et quitte.|
| --output -o        | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query            | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose          | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-restart"></a>sfctl node restart
Redémarre un nœud de cluster Service Fabric.

Redémarre un nœud de cluster Service Fabric déjà démarré.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-name [Requis]| Nom du nœud.|
| --create-fabric-dump  | Spécifiez True pour créer une image mémoire du processus du nœud Fabric. Cette valeur respecte la casse.  Valeur par défaut : False.|
| --node-instance-id | ID d’instance du nœud cible. Si un ID d’instance est spécifié, le nœud est redémarré uniquement s’il correspond à l’instance actuelle du nœud. La valeur par défaut 0 correspond à tout ID d’instance. L’ID d’instance peut être obtenu à l’aide de la requête d’obtention de nœud.  Valeur par défaut : 0.|
| --timeout -t       | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug            | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h          | Affiche ce message d’aide et quitte.|
| --output -o        | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query            | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose          | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-node-transition"></a>sfctl node transition
Démarre ou arrête un nœud de cluster.

Démarre ou arrête un nœud de cluster.  Un nœud de cluster est un processus, pas l’instance de système d’exploitation proprement dite.
Pour démarrer un nœud, définissez le paramètre NodeTransitionType sur « Start ». Pour arrêter un nœud, définissez le paramètre NodeTransitionType sur « Stop ». Cette API démarre l’opération (lorsque l’API retourne du contenu, il se peut que le nœud n’ait pas encore terminé la transition). Appelez l’API GetNodeTransitionProgress avec le même identifiant OperationId pour obtenir la progression de l’opération. 

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --node-instance-id [Requis]| ID d’instance du nœud cible. Il peut être déterminé via l’API GetNodeInfo.|
| --node-name [Requis]| Nom du nœud.|
| --node-transition-type [Requis]| Indique le type de transition à effectuer.                       NodeTransitionType.Start démarre un nœud arrêté.                       NodeTransitionType. Stop arrête un nœud opérationnel. |
| --operation-id [Requis]| GUID qui identifie un appel de cette API.  Celui-ci est transmis à l’API GetProgress correspondante.|
| --stop-duration-in-seconds [Requis]| Durée, en secondes, pendant laquelle conserver le nœud arrêté.  La valeur minimale est égale à 600 ; la valeur maximale est égale à 14400. À l’expiration de ce délai, le nœud redevient automatiquement opérationnel.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                         | Affiche ce message d’aide et quitte.|
| --output -o                       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                       Valeur par défaut : json.|
| --query                           | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).