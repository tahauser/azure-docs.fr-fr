---
title: "Interface de ligne de commande CLI Azure Service Fabric - sfctl choas | Microsoft Docs"
description: "Décrit les commandes sfctl chaos de l’interface de ligne de commande CLI Service Fabric."
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
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Permet de démarrer, d’arrêter et de créer des rapports sur le service de test chaos.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    report| Obtient le segment suivant du rapport Chaos sur la base du jeton de liaison passé ou l’intervalle de temps passé.|
|    start | Démarre Chaos dans le cluster.|
|    stop  | Arrête Chaos dans le cluster s’il est déjà en cours d’exécution, sinon elle ne fait rien.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
Obtient le segment suivant du rapport Chaos sur la base du jeton de liaison passé ou l’intervalle de temps passé.

Vous pouvez soit spécifier le ContinuationToken pour obtenir le segment suivant du rapport Chaos soit spécifier l’intervalle de temps entre StartTimeUtc et EndTimeUtc, mais vous ne pouvez pas spécifier le ContinuationToken et l’intervalle de temps dans le même appel. Lorsqu’il y a plus de 100 événements Chaos, le rapport Chaos est retourné dans les segments ne contenant pas plus de 100 événements Chaos. 

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --continuation-token| Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --end-time-utc   | Heure de fichier Windows représentant l’heure de fin de l’intervalle de temps pour lequel un rapport Chaos doit être généré. Pour plus d’informations, consultez [DateTime.ToFileTimeUtc Method](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) (Méthode DateTime.ToFileTimeUtc).|
| --start-time-utc | Heure de fichier Windows représentant l’heure de début de l’intervalle de temps pour lequel un rapport Chaos doit être généré. Pour plus d’informations, consultez [DateTime.ToFileTimeUtc Method](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) (Méthode DateTime.ToFileTimeUtc).|
| --timeout -t     | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug          | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h        | Affiche ce message d’aide et quitte.|
| --output -o      | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query          | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose        | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Démarre Chaos dans le cluster.

Si Chaos n’est pas encore exécuté dans le cluster, Chaos est démarré avec les paramètres Chaos transmis. Si Chaos est déjà en cours d’exécution au moment de cet appel, l’appel échoue avec le code d’erreur FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-type-health-policy-map  | Liste encodée au format JSON avec pourcentage maximal d’applications défectueuses pour des types d’applications spécifiques. Chaque entrée spécifie sous forme de clé le nom du type d’application et sous forme de valeur un entier qui représente le pourcentage MaxPercentUnhealthyApplications permettant d’évaluer les applications du type d’application spécifié. Définit un mappage avec le pourcentage maximal d’applications défectueuses pour des types d’applications spécifiques. Chaque entrée spécifie sous forme de clé le nom du type d’application et sous forme de valeur un entier qui représente le pourcentage MaxPercentUnhealthyApplications permettant d’évaluer les applications du type d’application spécifié. Le mappage de stratégie d’intégrité de type application peut être utilisé lors de l’évaluation de l’intégrité du cluster pour décrire les types d’applications particuliers. Les types d’applications inclus dans le mappage sont évalués par rapport au pourcentage spécifié dans le mappage et non par rapport au pourcentage MaxPercentUnhealthyApplications global défini dans la stratégie de contrôle d’intégrité du cluster. Les applications correspondant aux types spécifiés dans le mappage ne sont pas comptées dans le pool global d’applications. Par exemple, si certaines applications d’un type sont critiques, l’administrateur du cluster peut ajouter une entrée dans le mappage pour ce type d’application et lui attribuer une valeur de 0 % (autrement dit, aucun échec n’est toléré). Toutes les autres applications peuvent être évaluées avec une valeur MaxPercentUnhealthyApplications définie sur 20 % pour tolérer un certain nombre d’échecs parmi les milliers d’instances d’applications. Le mappage de stratégie de contrôle d’intégrité du type d’application est utilisé uniquement si le manifeste du cluster permet l’évaluation de l’intégrité du type d’application à l’aide de l’entrée de configuration pour HealthManager/EnableApplicationTypeHealthEvaluation.|
|--chaos-target-filter         | Dictionnaire encodé au format JSON avec deux clés de type chaîne. Les deux clés sont NodeTypeInclusionList et ApplicationInclusionList. Les valeurs pour ces deux clés sont des listes de chaînes. chaos_target_filter définit tous les filtres pour des erreurs Chaos ciblées, provoquant par exemple des erreurs pour seulement certains types de nœuds ou certaines applications. Si chaos_target_filter n’est pas utilisé, Chaos provoque des erreurs pour toutes les entités de cluster. Si chaos_target_filter est utilisé, Chaos provoque des erreurs uniquement pour les entités qui répondent aux critères de chaos_target_filter. NodeTypeInclusionList et ApplicationInclusionList autorisent uniquement une sémantique d’union. Il n’est pas possible de spécifier une intersection de NodeTypeInclusionList et d’ApplicationInclusionList. Par exemple, il n’est pas possible de spécifier « provoquer une erreur sur cette application uniquement si elle se trouve sur ce type de nœud ». Une fois qu’une entité est incluse dans NodeTypeInclusionList ou ApplicationInclusionList, elle ne peut pas être exclue à l’aide de ChaosTargetFilter. Même si applicationX n’apparaît pas dans ApplicationInclusionList, dans certaines itérations de Chaos, applicationX peut faire l’objet d’une erreur si elle se trouve sur un nœud de nodeTypeY qui est inclus dans NodeTypeInclusionList. Si NodeTypeInclusionList et ApplicationInclusionList sont vides, une exception ArgumentException est levée. Tous les types d’erreurs (redémarrage du nœud, redémarrage du package de code, suppression du réplica, redémarrage du réplica, déplacement du réplica principal et déplacement du réplica secondaire) sont activés pour les nœuds de ces types de nœuds. Si un type de nœud (par exemple NodeTypeX) ne figure pas dans NodeTypeInclusionList, les erreurs au niveau du nœud (comme NodeRestart) ne seront jamais activées pour les nœuds de NodeTypeX, mais les erreurs de package de code et de réplica peuvent toujours être activées pour NodeTypeX si une application figurant dans ApplicationInclusionList réside sur un nœud de NodeTypeX. Cette liste peut inclure au maximum 100 noms de types de nœuds. Pour augmenter ce nombre, vous devez mettre à niveau la configuration de MaxNumberOfNodeTypesInChaosEntityFilter. Chaos peut provoquer des erreurs (redémarrage du réplica, déplacement du réplica, déplacement du réplica principal et déplacement du réplica secondaire) dans tous les réplicas appartenant aux services de ces applications. Chaos peut redémarrer un package de code uniquement si celui-ci héberge uniquement des réplicas de ces applications. Si une application ne figure pas dans cette liste, elle peut toujours faire l’objet d’une erreur dans une itération de Chaos si elle se termine sur un nœud dont le type est inclus dans NodeTypeInclusionList. Toutefois, si applicationX est liée à nodeTypeY par l’intermédiaire de contraintes de placement, qu’applicationX est absente de ApplicationInclusionList et que nodeTypeY est absent de NodeTypeInclusionList, applicationX ne fait jamais l’objet d’une erreur. Cette liste peut inclure au maximum 1 000 noms d’applications. Pour augmenter ce nombre, vous devez mettre à niveau la configuration de MaxNumberOfApplicationsInChaosEntityFilter.|
|--context                     | Mappage encodé au format JSON de paires clé-valeur de type (string, string). La carte peut être utilisée pour enregistrer des informations relatives à l’exécution de Chaos. Il ne peut pas y avoir plus de 100 de ces paires et chaque chaîne (clé ou valeur) peut comporter au maximum 4095 caractères. Ce mappage est défini par le démarrage de l’exécution de Chaos afin d’éventuellement stocker le contexte de l’exécution spécifique.|
| --disable-move-replica-faults | Permet de désactiver les erreurs MovePrimary et MoveSecondary.|
| --max-cluster-stabilization| Délai d’attente maximal avant que toutes les entités de cluster ne se stabilisent et deviennent intègres.  Valeur par défaut : 60. Chaos s’exécute en itérations, et au début de chaque itération, il valide l’intégrité des entités de cluster. Lors de la validation, si une entité de cluster n’est pas stable et intègre dans le délai spécifié par MaxClusterStabilizationTimeoutInSeconds, Chaos génère un événement d’échec de validation.|
| --max-concurrent-faults    | Nombre maximal d’erreurs simultanées introduites dans chaque itération.           Valeur par défaut : 1. Chaos s’exécute en itérations, deux itérations consécutives étant séparées par une phase de validation. Plus la concurrence est élevée, plus l’injection d’erreurs est agressive, provoquant des séries d’états plus complexes pour détecter des bogues. Il est recommandé de démarrer avec une valeur de 2 ou 3 et de faire preuve de prudence lors de l’augmentation de cette valeur.|
| --max-percent-unhealthy-apps  | Lors de l’évaluation de l’intégrité du cluster au cours de Chaos, pourcentage maximal autorisé d’applications pouvant être défectueuses avant signalement d’une erreur. Pourcentage maximal autorisé d’applications non saines avant signalement d’une erreur. Par exemple, pour autoriser 10 % des applications pouvant être défectueuses, cette valeur serait de 10. Il s’agit du pourcentage maximum toléré d’applications pouvant être défectueuses avant que l’intégrité du cluster ne soit considérée comme étant à l’état Error. Si le pourcentage est respecté mais qu’il existe au moins une application pouvant être défectueuse, l’état d’intégrité signalé est Warning. Ce calcul est effectué en divisant le nombre d’applications pouvant être défectueuses par le nombre total d’instances d’applications dans le cluster, à l’exception des types d’applications inclus dans ApplicationTypeHealthPolicyMap. Le calcul est arrondi pour tolérer la défaillance d’un petit nombre d’applications. Le pourcentage par défaut est zéro.|
| --max-percent-unhealthy-nodes | Lors de l’évaluation de l’intégrité du cluster au cours de Chaos, pourcentage maximal autorisé de nœuds pouvant être défectueux avant signalement d’une erreur. Pourcentage maximal autorisé de nœuds non sains avant signalement d’une erreur. Par exemple, pour autoriser 10 % de nœuds défectueux, cette valeur serait de 10. Il s’agit du pourcentage maximum toléré de nœuds pouvant être défectueux avant que l’intégrité du cluster ne soit considérée comme étant à l’état Error. Si le pourcentage est respecté mais qu’il existe au moins un nœud pouvant être défectueux, l’état d’intégrité est Warning. Le pourcentage est calculé en divisant le nombre de nœuds défectueux par le nombre total de nœuds du cluster. Le calcul est arrondi pour tolérer une défaillance sur un petit nombre de nœuds. Le pourcentage par défaut est zéro. Dans les clusters de grande taille, certains nœuds sont toujours inactifs ou en réparation. Ce pourcentage doit donc être configuré pour tolérer cette condition.|
| --time-to-run              | Durée totale d’exécution (en secondes) de Chaos avant son arrêt automatique. La valeur maximale autorisée est de 4 294 967 295 (System.UInt32.MaxValue).  Valeur par défaut : 4294967295.|
| --timeout -t               | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|
| --wait-time-between-faults | Temps d’attente (en secondes) entre les erreurs consécutives au sein d’une seule itération.  Valeur par défaut : 20. Plus la valeur est grande, plus le chevauchement entre les erreurs est faible et plus la séquence de transitions d’état dont fait l’objet le cluster est simple. Il est recommandé de démarrer avec une valeur comprise entre 1 et 5 et de faire preuve de prudence lors de l’augmentation de cette valeur.|
| --wait-time-between-iterations| Temps d’attente (en secondes) entre deux itérations consécutives de Chaos. Plus la valeur est grande, plus le taux d’injection d’erreurs est faible. Valeur par défaut : 30.|
| --warning-as-error         | Lors de l’évaluation de l’intégrité du cluster au cours de Chaos, considère les avertissements avec le même niveau de gravité que celui des erreurs.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                    | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                  | Affiche ce message d’aide et quitte.|
| --output -o                | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.           Valeur par défaut : json.|
| --query                    | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                  | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Arrête Chaos dans le cluster s’il est déjà en cours d’exécution, sinon elle ne fait rien.

Empêche Chaos de planifier d’autres erreurs. Toutefois, les erreurs en cours ne sont pas affectées.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --timeout -t| Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug  | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h| Affiche ce message d’aide et quitte.|
| --output -o | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query  | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose| Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).