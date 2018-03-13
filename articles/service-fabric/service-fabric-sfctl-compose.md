---
title: Interface de ligne de commande CLI Azure Service Fabric - sfctl compose| Microsoft Docs
description: "Décrit les commandes sfctl compose de l’interface de ligne de commande CLI Service Fabric."
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
ms.openlocfilehash: 19afd35248cc0796eddbb50db4f38b813f5d568e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-compose"></a>sfctl compose
Permet de créer, de supprimer et de gérer les déploiements Docker Compose.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    create| Permet de déployer une application Service Fabric à partir d’un fichier Compose.|
|    list  | Permet d’obtenir la liste des déploiements compose créés dans le cluster Service Fabric.|
|   remove| Permet de supprimer un déploiement compose Service Fabric existant du cluster.|
|   status| Permet d’obtenir des informations sur un déploiement compose Service Fabric.|
|mettre à niveau       | Permet de commencer la mise à niveau d’un déploiement compose dans le cluster Service Fabric.|
|    upgrade-status| Permet d’obtenir des informations sur la dernière mise à niveau effectuée sur le déploiement Compose Service Fabric.|


## <a name="sfctl-compose-create"></a>sfctl compose create
Permet de créer un déploiement compose Service Fabric.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --file-path [Requis]| Chemin d’accès au fichier Docker Compose cible.|
 |   --deployment-name [Requis]| Le nom du déploiement.|
|    --encrypted-pass             | Au lieu de demander un mot de passe de Registre de conteneur, utilisez une phrase secrète déjà chiffrée.|
|    --has-pass                   | Demande un mot de passe pour accéder au Registre du conteneur.|
|    --timeout -t                 | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|
 |   --user                       | Nom d’utilisateur pour se connecter au Registre du conteneur.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :             json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-compose-list"></a>sfctl compose list
Permet d’obtenir la liste des déploiements compose créés dans le cluster Service Fabric.

Permet d’obtenir l’état des déploiements compose créés ou qui sont en cours de création dans le cluster Service Fabric. La réponse comprend le nom, l’état et d’autres détails sur le déploiement compose. Si les applications ne tiennent pas dans une page, une page de résultats est renvoyée, ainsi qu’un jeton de liaison, qui peut être utilisé pour obtenir la page suivante.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --continuation-token| Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse.      Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --max-results    | Nombre maximal de résultats à renvoyer dans le cadre des requêtes paginées.      Ce paramètre définit la limite supérieure du nombre de résultats renvoyés.      Le nombre de résultats renvoyés peut être inférieur au nombre maximal de résultats spécifié s’ils ne tiennent pas dans le message conformément aux restrictions de taille maximale définies dans la configuration. Si ce paramètre est égal à zéro ou n’est pas spécifié, les requêtes paginées comprennent le nombre maximal de résultats pouvant tenir dans le message renvoyé.|
| --timeout -t     | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug          | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h        | Affiche ce message d’aide et quitte.|
| --output -o      | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query          | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose        | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-compose-remove"></a>sfctl compose remove
Permet de supprimer un déploiement compose Service Fabric existant du cluster.

Permet de supprimer un déploiement compose Service Fabric existant. 

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --deployment-name [Requis]| Identité du déploiement. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ».|
| --timeout -t            | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :             json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-compose-status"></a>sfctl compose status
Permet d’obtenir des informations sur un déploiement compose Service Fabric.

Renvoie l’état du déploiement compose créé ou en cours de création dans le cluster Service Fabric et dont le nom correspond à celui spécifié comme paramètre. La réponse comprend le nom, l’état et d’autres informations sur l’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --deployment-name [Requis]| Identité du déploiement. |
| --timeout -t            | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :             json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Permet de commencer la mise à niveau d’un déploiement compose dans le cluster Service Fabric.

Permet de valider les paramètres de mise à niveau fournis et de commencer la mise à niveau du déploiement.

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|    --file-path        [Requis]| Chemin d’accès au fichier Docker Compose cible.|
|    --deployment-name [Requis]| Le nom du déploiement.|
|    --default-svc-type-health-map| Dictionnaire encodé JSON qui décrit la stratégie d’intégrité utilisée pour évaluer l’intégrité des services.|
|    --encrypted-pass             | Au lieu de demander un mot de passe de Registre de conteneur, utilisez une phrase secrète déjà chiffrée.|
 |   --failure-action             | Les valeurs possibles sont : « Invalid », « Rollback », « Manual ».|
|    --force-restart              | Force le redémarrage.|
 |   --has-pass                   | Demande un mot de passe pour accéder au Registre du conteneur.|
|    --health-check-retry         | Délai d’expiration de la nouvelle tentative de contrôle d’intégrité mesurée en millisecondes.|
|    --health-check-stable        | Durée de la stabilité du contrôle d’intégrité mesurée en millisecondes.|
|    --health-check-wait          | Durée d’attente du contrôle d’intégrité mesurée en millisecondes.|
|    --replica-set-check          | Délai d’expiration de la vérification du jeu de réplicas mesuré en secondes.|
|    --svc-type-health-map        | Liste encodée JSON d’objets décrivant les stratégies d’intégrité utilisées pour évaluer l’intégrité de différents types de services.|
|    --timeout -t                 | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|
|    --unhealthy-app              | Pourcentage maximal autorisé d’applications pouvant être défectueuses avant signalement d’une erreur.        Par exemple, pour autoriser 10 % des applications pouvant être défectueuses, cette valeur serait de 10. Il s’agit du pourcentage maximum toléré d’applications pouvant être défectueuses avant que l’intégrité du cluster ne soit considérée comme étant à l’état Error. Si le pourcentage est respecté mais qu’il existe au moins une application pouvant être défectueuse, l’état d’intégrité est Warning. Ce pourcentage est calculé en divisant le nombre d’applications pouvant être défectueuses par le nombre total d’instances d’application du cluster.|
|    --upgrade-domain-timeout     | Délai d’expiration du domaine de mise à niveau mesuré en millisecondes.|
|    --upgrade-kind               | Valeur par défaut : Rolling.|
|    --upgrade-mode               | Les valeurs possibles sont : « Invalid », « UnmonitoredAuto », « UnmonitoredManual », « Monitored ».  Valeur par défaut : UnmonitoredAuto.|
|    --upgrade-timeout            | Délai d’expiration de la mise à niveau mesuré en millisecondes.|
|    --user                       | Nom d’utilisateur pour se connecter au Registre du conteneur.|
|    --warning-as-error           | Les avertissements sont traités avec le même niveau de gravité que les erreurs.|

### <a name="global-arguments"></a>Arguments globaux
 |Argument|Description|
| --- | --- |
|   --debug                      | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
|    --help -h                    | Affiche ce message d’aide et quitte.|
|   --output -o                  | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv. Valeur par défaut : json.|
|   --query                      | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
|   --verbose                    | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).