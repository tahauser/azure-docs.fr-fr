---
title: "CLI Azure Service Fabric : sfctl rpm | Microsoft Docs"
description: "Décrit les commandes sfctl rpm de l’interface de ligne de commande (CLI) Service Fabric."
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
ms.openlocfilehash: 34e7693ea40df2bf12fd6e9be2ef627f30748bcd
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-rpm"></a>sfctl rpm
Interroge et envoie des commandes vers le service gestionnaire de réparation.

## <a name="commands"></a>Commandes
|Commande|Description|
| --- | --- |
|    approve-force| Force l’approbation de la tâche de réparation donnée.|
|    delete       | Supprime une tâche de réparation terminée.|
|    list         | Permet d’obtenir une liste de tâches de réparation correspondant aux filtres donnés.|

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Supprime une tâche de réparation terminée.

Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code. 

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|    --task-id [Requis]| ID de la tâche de réparation terminée à supprimer.|
|    --version           | Numéro de version actuelle de la tâche de réparation. Si autre que zéro, la demande aboutit uniquement si cette valeur correspond à la version actuelle réelle de la tâche de réparation. Si égale à zéro, aucune vérification de version n’est effectuée.|

### <a name="global-arguments"></a>Arguments globaux
|Argument|Description|
| --- | --- |
|    --debug             | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
|    --help -h           | Affiche ce message d’aide et quitte.|
|    --output -o         | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.
|    --query             | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
|    --verbose           | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|


## <a name="sfctl-rpm-list"></a>sfctl rpm list
Permet d’obtenir une liste de tâches de réparation correspondant aux filtres donnés.

Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code. 

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|    --executor-filter| Nom de l’Exécuteur de réparations dont les tâches revendiquées doivent être incluses dans la liste.|
|    --state-filter   | OR au niveau du bit des valeurs suivantes, qui spécifie les états de tâches qu’il convient d’inclure dans la liste des résultats. - 1 : créé - 2 : revendiqué - 4 : préparation - 8 : approuvé - 16 : exécution - 32 : restauration - 64 : terminé.|
|    --task-id-filter | Préfixe d’ID de tâche de réparation à mettre en correspondance.|

### <a name="global-arguments"></a>Arguments globaux
|Argument|Description|
| --- | --- |
|    --debug          | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
|    --help -h        | Affiche ce message d’aide et quitte.|
|    --output -o      | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Default| json.|
|    --query          | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
|    --verbose        | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).