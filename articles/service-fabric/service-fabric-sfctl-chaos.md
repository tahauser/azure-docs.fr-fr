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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: dbea84511c37cf52c3d98f0247e5ce3c0b2a05c3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Permet de démarrer, d’arrêter et de créer des rapports sur le service de test chaos.

## <a name="commands"></a>Commandes

|Commande|DESCRIPTION|
| --- | --- |
|    report| Obtient le segment suivant du rapport Chaos sur la base du jeton de liaison passé ou l’intervalle de temps passé.|
|    start | Si Chaos n’est pas encore en cours d’exécution dans le cluster, commence par exécuter Chaos avec les paramètres spécifiés dans Chaos.|
|    stop  | Arrête Chaos dans le cluster s’il est déjà en cours d’exécution, sinon elle ne fait rien.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
Obtient le segment suivant du rapport Chaos sur la base du jeton de liaison passé ou l’intervalle de temps passé.

Vous pouvez soit spécifier le ContinuationToken pour obtenir le segment suivant du rapport Chaos soit spécifier l’intervalle de temps entre StartTimeUtc et EndTimeUtc, mais vous ne pouvez pas spécifier le ContinuationToken et l’intervalle de temps dans le même appel. Lorsqu’il y a plus de 100 événements Chaos, le rapport Chaos est retourné dans les segments ne contenant pas plus de 100 événements Chaos. 

### <a name="arguments"></a>Arguments

|Argument|DESCRIPTION|
| --- | --- |
| --continuation-token| Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --end-time-utc   | Nombre de cycles représentant l’heure de fin de la plage horaire pour laquelle un rapport Chaos doit être généré. Veuillez consulter [DateTime.Ticks, propriété](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) pour plus d’informations sur les cycles.|
| --start-time-utc | Nombre de cycles représentant l’heure de début de la plage horaire pour laquelle un rapport Chaos doit être généré. Veuillez consulter [DateTime.Ticks, propriété](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) pour plus d’informations sur les cycles.|
| --timeout -t     | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|DESCRIPTION|
| --- | --- |
| --debug          | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h        | Affiche ce message d’aide et quitte.|
| --output -o      | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query          | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose        | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Si Chaos n’est pas encore en cours d’exécution dans le cluster, commence par exécuter Chaos avec les paramètres spécifiés dans Chaos.

### <a name="arguments"></a>Arguments

|Argument|DESCRIPTION|
| --- | --- |
| --app-type-health-policy-map  | Liste encodée au format JSON avec pourcentage maximal d’applications défectueuses pour des types d’applications spécifiques. Chaque entrée spécifie sous forme de clé le nom du type d’application et sous forme de valeur un entier qui représente le pourcentage MaxPercentUnhealthyApplications permettant d’évaluer les applications du type d’application spécifié.|
| --disable-move-replica-faults | Permet de désactiver les erreurs MovePrimary et MoveSecondary.|
| --max-cluster-stabilization| Délai d’attente maximal avant que toutes les entités de cluster ne se stabilisent et deviennent intègres.  Valeur par défaut : 60.|
| --max-concurrent-faults    | Nombre maximal d’erreurs simultanées introduites dans chaque itération.           Valeur par défaut : 1.|
| --max-percent-unhealthy-apps  | Lors de l’évaluation de l’intégrité du cluster au cours de Chaos, pourcentage maximal autorisé d’applications pouvant être défectueuses avant signalement d’une erreur.|
| --max-percent-unhealthy-nodes | Lors de l’évaluation de l’intégrité du cluster au cours de Chaos, pourcentage maximal autorisé de nœuds pouvant être défectueux avant signalement d’une erreur.|
| --time-to-run              | Durée totale d’exécution (en secondes) de Chaos avant son arrêt automatique. La valeur maximale autorisée est de 4 294 967 295 (System.UInt32.MaxValue).  Valeur par défaut : 4294967295.|
| --timeout -t               | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|
| --wait-time-between-faults | Temps d’attente (en secondes) entre les erreurs consécutives au sein d’une seule itération.  Valeur par défaut : 20.|
| --wait-time-between-iterations| Temps d’attente (en secondes) entre deux itérations consécutives de Chaos.  Valeur par défaut : 30.|
| --warning-as-error         | Lors de l’évaluation de l’intégrité du cluster au cours de Chaos, considère les avertissements avec le même niveau de gravité que celui des erreurs.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|DESCRIPTION|
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

|Argument|DESCRIPTION|
| --- | --- |
| --timeout -t| Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|DESCRIPTION|
| --- | --- |
| --debug  | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h| Affiche ce message d’aide et quitte.|
| --output -o | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query  | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose| Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).