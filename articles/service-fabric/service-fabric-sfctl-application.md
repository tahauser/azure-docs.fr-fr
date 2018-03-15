---
title: "Interface de ligne de commande CLI Azure Service Fabric : sfctl application | Microsoft Docs"
description: "Décrit les commandes sfctl application de l’interface CLI Azure Service Fabric."
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
ms.openlocfilehash: 3a10437d0a2d680e586ada6a87750a69453c1f0c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-application"></a>sfctl application
Permet de créer, de supprimer et de gérer les applications et les types d’application.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| create       | Permet de créer une application Service Fabric à l’aide de la description spécifiée.|
| delete       | Supprime une application Service Fabric existante.|
| deployed     | Permet d’obtenir les informations relatives à une application déployée sur un nœud Service Fabric.|
| deployed-health | Permet d’obtenir les informations relatives à l’intégrité d’une application déployée sur un nœud Service Fabric.|
| deployed-list| Permet d’obtenir la liste des applications déployées sur un nœud Service Fabric.|
| health       | Permet d’obtenir l’intégrité de l’application Service Fabric.|
| info         | Permet d’obtenir des informations sur une application Service Fabric.|
| list         | Permet d’obtenir la liste des applications créées dans le cluster Service Fabric qui correspondent aux filtres spécifiés comme paramètre.|
| load | Permet d’obtenir les informations relatives à une application Service Fabric. |
| manifest     | Permet d’obtenir le manifeste qui décrit un type d’application.|
| provision    | Permet d’approvisionner ou d’inscrire un type d’application Service Fabric auprès du cluster à l’aide du package .sfpkg dans le magasin externe ou du package d’application dans le magasin d’images.|
| report-health| Permet d’envoyer un rapport d’intégrité sur l’application Service Fabric.|
| Type         | Permet d’obtenir la liste des types d’applications du cluster Service Fabric qui correspondent exactement au nom spécifié.|
| type-list    | Permet d’obtenir la liste des types d’applications du cluster Service Fabric.|
| unprovision  | Permet de supprimer ou d’annuler l’inscription d’un type d’application Service Fabric dans le cluster.|
| mettre à niveau      | Commence la mise à niveau d’une application dans le cluster Service Fabric.|
| upgrade-resume  | Commence la mise à niveau d’une application dans le cluster Service Fabric.|
| upgrade-rollback| Annule la mise à niveau en cours d’une application du cluster Service Fabric.|
| upgrade-status  | Permet d’obtenir les détails de la dernière mise à jour effectuée sur cette application.|
| upload       | Copie un package d’application Service Fabric dans le magasin d’images.|

## <a name="sfctl-application-create"></a>sfctl application create
Permet de créer une application Service Fabric à l’aide de la description spécifiée.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name    [Requis]| Nom de l’application, y compris le schéma d’URI « fabric: ».|
| --app-type    [Requis]| Nom du type d’application trouvé dans le manifeste d’application.|
| --app-version [Requis]| Version des types d’applications comme défini dans le manifeste d’application.|
| --max-node-count     | Nombre maximal de nœuds pour lesquels Service Fabric réserve de la capacité pour cette application. Cela ne signifie pas que les services de cette application sont placés sur tous les nœuds.|
| --metrics            | Liste encodée au format JSON des descriptions des mesures de capacité de l’application. Une mesure est définie comme un nom, associé à un ensemble de capacités pour chaque nœud sur lequel l’application existe.|
| --min-node-count     | Nombre minimal de nœuds pour lesquels Service Fabric réserve de la capacité pour cette application. Cela ne signifie pas que les services de cette application sont placés sur tous les nœuds.|
| --parameters         | Liste encodée au format JSON des remplacements de paramètres de l’application à appliquer lors de la création de l’application.|
| --timeout -t         | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug              | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h            | Affiche ce message d’aide et quitte.|
| --output -o          | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :     json.|
| --query              | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose            | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-delete"></a>sfctl application delete
Supprime une application Service Fabric existante.

Supprime une application Service Fabric existante. Une application doit être créée avant de pouvoir être supprimée. En supprimant une application, vous supprimez également tous les services qui font partie de l’application. Par défaut, Service Fabric essaie de fermer les réplicas de service sans perte de données, puis supprime le service. Toutefois, si le service rencontre des problèmes de fermeture normale des réplicas, l’opération de suppression peut prendre un certain temps ou bloquer. Utilisez l’indicateur ForceRemove facultatif pour ignorer la séquence de fermeture normale et forcer la suppression de l’application et de tous ses services.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --force-remove          | Force la suppression d’une application ou d’un service Service Fabric, sans passer par la séquence d’arrêt normale. Ce paramètre permet de forcer la suppression d’une application ou d’un service pour lesquels le délai de suppression expire à cause de problèmes dans le code de service qui empêchent la fermeture normale des réplicas.|
| --timeout -t            | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :        json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Permet d’obtenir les informations relatives à une application déployée sur un nœud Service Fabric.

Permet d’obtenir les informations relatives à une application déployée sur un nœud Service Fabric.  Cette requête renvoie des informations sur l’application système si l’ID d’application fourni est celui de l’application système. Les résultats incluent les applications déployées dont l’état est défini sur active, activating (activation) ou downloading (téléchargement). Pour cette requête, le nom de nœud doit obligatoirement correspondre à un nœud du cluster. Si le nom de nœud fourni ne pointe pas vers un nœud Service Fabric actif du cluster, la requête échoue.
     
### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --node-name      [obligatoire]| Nom du nœud.|
| --timeout -t            | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :        json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-health"></a>sfctl application health
Permet d’obtenir l’intégrité de l’application Service Fabric.

Permet d’obtenir l’état d’intégrité de l’application Service Fabric. La réponse indique l’état d’intégrité Ok, Error ou Warning. Si l’entité est introuvable dans le magasin d’intégrité, il renvoie des erreurs.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id                 [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application, sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --deployed-applications-health-state-filter| Permet de filtrer, par état d’intégrité, les objets d’état d’intégrité des applications déployées qui sont retournés dans le résultat de la requête d’intégrité de l’application. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seules les applications déployées qui correspondent au filtre sont retournées. Toutes les applications déployées sont utilisées pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, l’état d’intégrité des applications déployées dont la valeur HealthState est OK (2) et Warning (4) est retourné. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant n’importe quelle valeur HealthState. La valeur est égale à 65535.|
| --events-health-state-filter            | Permet de filtrer la collection d’objets HealthEvent retournés en fonction de leur état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les événements qui correspondent au filtre sont renvoyés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant n’importe quelle valeur HealthState. La valeur est égale à 65535.|
| --exclude-health-statistics | Indique si les statistiques d’intégrité doivent être retournées comme faisant partie du résultat de la requête. False par défaut. Les statistiques affichent le nombre d’entités enfants dont l’état d’intégrité est OK, Warning et Error.|
| --services-health-state-filter          | Permet de filtrer, par état d’intégrité, les objets d’état d’intégrité des services qui sont retournés dans le résultat de la requête d’intégrité des services. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les services qui correspondent au filtre sont retournés. Tous les services sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, la requête renvoie l’état d’intégrité des services dont la valeur HealthState est OK (2) et Warning (4). - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant n’importe quelle valeur HealthState. La valeur est égale à 65535.|
| --timeout -t                            | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                               | Affiche ce message d’aide et quitte.|
| --output -o                             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query                                 | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-info"></a>sfctl application info
Permet d’obtenir des informations sur une application Service Fabric.

Renvoie les informations sur l’application qui a été créée ou en cours de création dans le cluster Service Fabric et dont le nom correspond à celui spécifié comme paramètre. La réponse comprend le nom, le type, l’état, les paramètres et d’autres détails sur l’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id      [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --exclude-application-parameters| Indicateur qui spécifie si les paramètres de l’application doivent être exclus du résultat.|
| --timeout -t                 | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                      | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                    | Affiche ce message d’aide et quitte.|
| --output -o                  | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.             Valeur par défaut : json.|
| --query                      | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                    | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-list"></a>sfctl application list
Permet d’obtenir la liste des applications créées dans le cluster Service Fabric qui correspondent aux filtres spécifiés comme paramètre.

Renvoie les informations sur les applications qui ont été créées ou sont en cours de création dans le cluster Service Fabric et qui correspondent aux filtres spécifiés comme paramètre. La réponse comprend le nom, le type, l’état, les paramètres et d’autres détails sur l’application. Si les applications ne tiennent pas dans une page, une page de résultats est renvoyée, ainsi que d’un jeton de liaison, qui peut être utilisé pour obtenir la page suivante. Les filtres ApplicationTypeName et ApplicationDefinitionKindFilter ne peuvent pas être spécifiés en même temps.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
|--application-definition-kind-filter| Permet de filtrer sur ApplicationDefinitionKind, qui est le mécanisme utilisé pour définir une application Service Fabric. - Default - Valeur par défaut, qui a le même effet que la sélection de « All ». La valeur est égale à 0. - All - Filtre qui correspond aux entrées ayant n’importe quelle valeur ApplicationDefinitionKind. La valeur est égale à 65535. - ServiceFabricApplicationDescription - Filtre qui correspond aux entrées dont l’argument ApplicationDefinitionKind présente la valeur ServiceFabricApplicationDescription. La valeur est égale à 1. - Compose - Filtre qui correspond aux entrées dont la valeur d’ApplicationDefinitionKind est égale à Compose. La valeur est égale à 2.|
| --application-type-name      | Nom du type d’application utilisé pour filtrer les applications à interroger. Cette valeur ne doit pas contenir la version du type d’application.|
| --continuation-token         | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être encodée URL.|
| --exclude-application-parameters| Indicateur qui spécifie si les paramètres de l’application doivent être exclus du résultat.|
| --max-results|Nombre maximal de résultats à renvoyer dans le cadre des requêtes paginées. Ce paramètre définit la limite supérieure du nombre de résultats renvoyés. Le nombre de résultats renvoyés peut être inférieur au nombre maximal de résultats spécifié s’ils ne tiennent pas dans le message conformément aux restrictions de taille maximale définies dans la configuration. Si ce paramètre est défini sur zéro ou n’est pas spécifié, les requêtes paginées comprennent le nombre maximal de résultats pouvant tenir dans le message renvoyé.|
| --timeout -t                 | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                      | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                    | Affiche ce message d’aide et quitte.|
| --output -o                  | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.             Valeur par défaut : json.|
| --query                      | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                    | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-load"></a>sfctl application load
Permet d’obtenir les informations relatives à une application Service Fabric.

Retourne des informations de chargement concernant l’application qui a été créée ou est en cours de création dans le cluster Service Fabric, et dont le nom correspond à celui spécifié comme paramètre. La réponse comprend le nom, le nombre minimal de nœuds, le nombre maximal de nœuds, le nombre de nœuds que l’application occupe actuellement, ainsi que les métriques de chargement relatives à l’application.

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|--application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application, sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes. |
| --timeout -t               | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux
|Argument|Description|
| --- | --- |
|--debug                    | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
    --help -h                  | Affiche ce message d’aide et quitte.|
    --output -o                | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
    --query                    | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
    --verbose                  | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Permet d’obtenir le manifeste qui décrit un type d’application.
        
Permet d’obtenir le manifeste qui décrit un type d’application. La réponse contient le code XML du manifeste de l’application sous forme de chaîne.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name [Requis]| Nom du type d’application.|
| --application-type-version [Requis]| Version du type d’application.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                         | Affiche ce message d’aide et quitte.|
| --output -o                       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                  Valeur par défaut : json.|
| --query                           | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-provision"></a>sfctl application provision
Permet d’approvisionner ou d’inscrire un type d’application Service Fabric auprès du cluster à l’aide du package SFPKG dans le magasin externe ou du package d’application dans le magasin d’images.

Permet d’approvisionner un type d’application Service Fabric auprès du cluster. Nécessaire avant toute instanciation d’une nouvelle application. L’opération d’approvisionnement peut être effectuée sur le package d’application spécifié par relativePathInImageStore, ou à l’aide de l’URI du package SFPKG externe. À moins que --external-provision soit défini, cette commande exige un approvisionnement

à partir du magasin d’images.
        


### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-package-download-uri| Chemin d’accès du package d’application .sfpkg à partir duquel le package d’application peut être téléchargé à l’aide des protocoles HTTP ou HTTPS. Pour l’approvisionnement à partir d’un magasin externe uniquement. Le package d’application peut être stocké dans un magasin externe qui fournit l’opération GET pour télécharger le fichier. Les protocoles pris en charge sont HTTP et HTTPS, et le chemin d’accès doit autoriser l’accès en lecture.|
| --application-type-build-path       | Pour l’approvisionnement à partir d’un magasin d’images uniquement. Chemin d’accès relatif du package d’application dans le magasin d’image spécifié lors de l’opération de chargement préalable. |
| --application-type-name| Pour l’approvisionnement à partir d’un magasin externe uniquement. Le nom du type d’application correspond à celui indiqué dans le manifeste de l’application.|
| --application-type-version| Pour l’approvisionnement à partir d’un magasin externe uniquement. La version du type d’application correspond à celle indiquée dans le manifeste de l’application.|
| --external-provision| Emplacement à partir duquel le package d’application peut être inscrit ou approvisionné. Indique que l’approvisionnement concerne un package d’application précédemment chargé dans un magasin externe. Le package d’application présente l’extension *.sfpkg.|
| --no-wait| Indique si l’approvisionnement doit se faire de façon asynchrone.  Lorsque la valeur est définie sur true, l’opération d’approvisionnement s’exécute quand la requête est acceptée par le système et l’opération d’approvisionnement se poursuit sans délai d’expiration. La valeur par défaut est false. Pour les packages d’application volumineux, nous vous recommandons de définir la valeur sur true.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                              | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                            | Affiche ce message d’aide et quitte.|
| --output -o                          | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query                              | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                            | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-type"></a>sfctl application type

Permet d’obtenir la liste des types d’applications du cluster Service Fabric qui correspondent exactement au nom spécifié.

Renvoie les informations sur les types d’application qui sont approvisionnés ou en cours d’approvisionnement dans le cluster Service Fabric. Ces résultats sont des types d’applications dont le nom correspond exactement à celui spécifié comme paramètre et qui sont conformes aux paramètres de requête donnés. Toutes les versions du type d’application correspondant au nom de type d’application sont renvoyées avec chaque version renvoyée sous la forme d’un type d’application. La réponse comprend le nom, la version, l’état et d’autres informations sur le type d’application. Il s’agit d’une requête paginée, ce qui signifie que si les applications ne tiennent pas dans une page, une page de résultats est renvoyée, ainsi que d’un jeton de liaison, qui peut être utilisé pour obtenir la page suivante. Par exemple, si 10 types d’application ne tiennent pas sur une page qui ne peut en contenir que 3 ou si le maximum de résultats est défini sur 3, alors la valeur 3 est renvoyée. Pour accéder au reste des résultats, récupérez les pages suivantes en utilisant le jeton de liaison renvoyé dans la requête suivante. Un jeton de liaison vide est renvoyé s’il n’existe aucune autre page.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name [Requis]| Nom du type d’application.|
| --application-type-version        | Version du type d’application.|
| --continuation-token           | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --exclude-application-parameters  | Indicateur qui spécifie si les paramètres de l’application doivent être exclus du résultat.|
| --max-results                  | Nombre maximal de résultats à renvoyer dans le cadre des requêtes paginées. Ce paramètre définit la limite supérieure du nombre de résultats renvoyés. Le nombre de résultats renvoyés peut être inférieur au nombre maximal de résultats spécifié s’ils ne tiennent pas dans le message conformément aux restrictions de taille maximale définies dans la configuration. Si ce paramètre est défini sur zéro ou n’est pas spécifié, la requête paginée comprend le nombre maximal de résultats pouvant tenir dans le message renvoyé.|
| --timeout -t                   | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                        | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                      | Affiche ce message d’aide et quitte.|
| --output -o                    | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.               Valeur par défaut : json.|
| --query                        | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                      | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Permet de supprimer ou d’annuler l’inscription d’un type d’application Service Fabric dans le cluster.

Permet de supprimer ou d’annuler l’inscription d’un type d’application Service Fabric dans le cluster. Cette opération n’est possible que si toutes les instances d’application du type d’application ont été supprimées. Une fois l’enregistrement du type d’application annulé, aucune nouvelle instance de l’application ne peut être créée pour ce type d’application particulier.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name [Requis]| Nom du type d’application.|
| --application-type-version [Requis]| Version du type d’application indiquée dans le manifeste de l’application.|
|--async-parameter                    | Indique si l’annulation de l’approvisionnement doit se faire de façon asynchrone. Lorsque la valeur est définie sur true, l’opération d’annulation de l’approvisionnement s’exécute quand la requête est acceptée par le système et l’opération d’annulation de l’approvisionnement se poursuit sans délai d’expiration. La valeur par défaut est false. Toutefois, nous vous recommandons de définir cette valeur sur true pour les packages d’application volumineux qui ont été approvisionnés.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                         | Affiche ce message d’aide et quitte.|
| --output -o                       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                  Valeur par défaut : json.|
| --query                           | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Commence la mise à niveau d’une application dans le cluster Service Fabric.

Valide les paramètres de mise à niveau d’application fournis et commence la mise à niveau de l’application si les paramètres sont valides. La description de la mise à niveau remplace la description de l’application existante. Par conséquent, si les paramètres ne sont pas spécifiés, les paramètres existants sur les applications sont remplacés par la liste de paramètres vide. Cela se traduit par l’utilisation par l’application de la valeur par défaut des paramètres à partir du manifeste d’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-id             [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Par exemple, si une application est nommée « fabric://mon_app/app1 », son identité est « mon_app~app1 » dans les versions 6.0 et supérieures, et « mon_app/app1 » dans les versions précédentes.|
| --app-version        [obligatoire]| Version de l’application cible.|
| --parameters         [obligatoire]| Liste JSON des remplacements de paramètres d’application à appliquer lors de la création de l’application.|
| --default-service-health-policy| Spécification JSON de la stratégie de contrôle d’intégrité utilisée par défaut pour évaluer l’intégrité d’un type de service.|
| --failure-action            | Action à effectuer lorsqu’une mise à niveau surveillée détecte des violations de stratégie de surveillance ou de stratégie d’intégrité.|
| --force-restart             | Force le redémarrage des processus pendant la mise à jour, même si la version du code n’a pas changé.|
| --health-check-retry-timeout| Durée pendant laquelle effectuer des tentatives d’évaluation d’intégrité lorsque l’application ou le cluster ne sont pas sains, avant qu’une action d’échec ne soit exécutée. Mesurée en millisecondes.  Par défaut : PT0H10M0S.|
| --health-check-stable-duration | Durée pendant laquelle l’application ou le cluster doivent rester sains avant que la mise à niveau ne passe au domaine de mise à niveau suivant.            Mesurée en millisecondes.  Par défaut : PT0H2M0S.|
| --health-check-wait-duration| Délai d’attente entre l’achèvement d’un domaine de mise à niveau et l’application des stratégies d’intégrité. Mesurée en millisecondes.            Valeur par défaut : 0.|
| --max-unhealthy-apps        | Pourcentage maximal autorisé d’applications déployées non saines. Représenté par un nombre compris entre 0 et 100.|
| --mode                      | Mode utilisé pour surveiller l’intégrité pendant une mise à niveau propagée.            Valeur par défaut : UnmonitoredAuto.|
| --replica-set-check-timeout | Durée maximale pendant laquelle bloquer le traitement d’un domaine de mise à niveau et éviter la perte de disponibilité en cas de problèmes inattendus. Mesurée en secondes.|
| --service-health-policy     | Mappage JSON avec stratégie d’intégrité de type de service par nom de type de service. Par défaut, le mappage est vide.|
| --timeout -t                | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|
| --upgrade-domain-timeout    | Durée d’exécution de chaque domaine de mise à niveau avant l’exécution de FailureAction. Mesurée en millisecondes.  Par défaut : P10675199DT02H48M05.4775807S.|
| --upgrade-timeout           | Durée d’exécution de l’ensemble de la mise à niveau avant exécution de FailureAction. Mesurée en millisecondes.  Par défaut : P10675199DT02H48M05.4775807S.|
| --warning-as-error          | Les avertissements de l’évaluation d’intégrité doivent être considérés comme des erreurs.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                     | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                   | Affiche ce message d’aide et quitte.|
| --output -o                 | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.            Valeur par défaut : json.|
| --query                     | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                   | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Copie un package d’application Service Fabric dans le magasin d’images.

Permet d’afficher éventuellement la progression du chargement pour chaque fichier du package. La progression du chargement est envoyée à `stderr`.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --path [Requis]| Chemin d’accès au package d’application local.|
|--imagestore-string| Magasin d’images de destination dans lequel charger le package d’application.  Par défaut : fabric:ImageStore.|
| --show-progress  | Permet d’afficher la progression du chargement pour les packages volumineux.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug       | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h     | Affiche ce message d’aide et quitte.|
| --output -o   | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query       | Chaîne de requête JMESPath. Pour plus d’informations, consultez le site à l’adresse http://jmespath.org/.|
| --verbose     | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
