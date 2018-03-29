---
title: Infrastructure Integration Runtime dans Azure Data Factory | Microsoft Docs
description: Découvrez l’infrastructure Integration Runtime dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 88475c169bd6cc8fc4d653801ec1af58ebc7620e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="integration-runtime-in-azure-data-factory"></a>Infrastructure Integration Runtime dans Azure Data Factory
Integration Runtime (IR) est l’infrastructure de calcul utilisée par Azure Data Factory pour fournir les fonctionnalités d’intégration de données suivantes entre différents environnements réseau :

- **Déplacement des données** : déplace des données entre les banques de données d’un réseau public et celles d’un réseau privé (sur un réseau privé local ou virtuel). Les connecteurs intégrés, la conversion de format, le mappage de colonnes, ainsi que les transferts de données performants et évolutifs sont pris en charge.
- **Répartition des activités** : répartit et analyse les activités de transformation exécutées sur un large éventail de services de calcul, tels qu’Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, etc.
- **Exécution des packages SSIS** : exécute en mode natif les packages SSIS (SQL Server Integration Services) dans un environnement Compute Azure géré.


> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

Dans Data Factory, une activité désigne l’action à effectuer. Un service lié désigne un magasin de données cible ou un service de calcul. Un runtime d’intégration permet de créer une passerelle entre l’activité et les services liés.  Il est référencé par le service lié et fournit l’environnement de calcul dans lequel l’activité s’exécute ou depuis lequel elle est envoyée.  L’activité peut être effectuée de la façon la plus efficace possible dans la région la plus proche du magasin de données cible ou du service de calcul, tout en respectant les exigences de conformité et de sécurité.

## <a name="integration-runtime-types"></a>Types de runtime d’intégration
Data Factory propose trois types de runtime d’intégration. Vous devez choisir le type qui répond le mieux à vos besoins en matière de fonctionnalités d’intégration de données et d’environnement réseau.  Ces trois types sont :

- Azure
- Auto-hébergé
- Azure-SSIS

Le tableau suivant décrit les fonctionnalités et l’environnement réseau pour chaque type de runtime d’intégration :

Type de runtime | Réseau public | Réseau privé
------- | -------------- | ---------------
Azure | Déplacement des données<br/>Répartition des activités | &nbsp;
Auto-hébergé | Déplacement des données<br/>Répartition des activités | Déplacement des données<br/>Répartition des activités
Azure-SSIS | Exécution des packages SSIS | Exécution des packages SSIS

Le schéma suivant montre comment les différents runtimes d’intégration peuvent être utilisés conjointement pour offrir des fonctionnalités d’intégration de données et un environnement réseau riches :

![Différents types de runtime d’intégration](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Runtime d’intégration Azure
Un runtime d’intégration Azure peut :

- Exécuter des activités de copie entre les magasins de données cloud
- Répartir les activités de transformation suivantes dans un réseau public : activité Hive HDInsight, activité Pig HDInsight, activité MapReduce HDInsight, activité HDInsight Spark, activité de diffusion en continu HDInsight, activité d’exécution par lot Machine Learning, activités des ressources de mise à jour de Machine Learning, activité de procédure stockée, activité U-SQL Data Lake Analytics, activité personnalisée .NET, activité Web, activité de recherche et activité d’obtention des métadonnées.

### <a name="network-environment"></a>Environnement réseau
L’infrastructure Azure Integration Runtime prend en charge les connexions aux magasins de données et aux services de calcul sur les réseaux publics comprenant des points de terminaison accessibles publiquement. Utilisez un runtime d’intégration auto-hébergé pour l’environnement du réseau virtuel Azure.

### <a name="compute-resource-and-scaling"></a>Ressources de calcul et mise à l’échelle
Le runtime d’intégration Azure fournit une expérience de calcul entièrement gérée, sans serveur dans Azure.  Vous n’avez plus à vous soucier de l’approvisionnement de l’infrastructure, de l’installation du logiciel, des mises à jour correctives ou de la mise à l’échelle des besoins.  Par ailleurs, vous payez uniquement pour ce que vous utilisez.

Le runtime d’intégration Azure fournit le calcul natif pour déplacer des données entre les magasins de données cloud de manière sécurisée, fiable et efficace.  Vous pouvez définir le nombre d’unités de déplacement des données à utiliser sur l’activité de copie. Ainsi, la taille de calcul du runtime d’intégration Azure est mise à l’échelle sans que vous deviez ajuster la taille du runtime d’intégration Azure.

La répartition des activités est une opération légère pour acheminer l’activité vers le service de calcul cible. Par conséquent, vous n’avez pas besoin de mettre la taille de calcul à l’échelle pour ce scénario.

Pour en savoir plus sur la création et la configuration d’un runtime d’intégration Azure, consultez la rubrique « Comment créer et configurer Azure IR » sous Procédures. 

## <a name="self-hosted-integration-runtime"></a>Runtime d’intégration auto-hébergé
Un runtime d’intégration auto-hébergé peut :

- Exécuter une activité de copie entre des magasins de données cloud et un magasin de données situé sur un réseau privé.
- Répartir les activités de transformation suivantes par à des ressources de calcul dans un réseau virtuel local ou Azure : activité Hive HDInsight (BYOC), activité Pig HDInsight (BYOC), activité MapReduce HDInsight (BYOC), activité HDInsight Spark (BYOC), activité de diffusion en continu HDInsight (BYOC), activité d’exécution par lot Machine Learning, activités des ressources de mise à jour de Machine Learning, activité de procédure stockée, activité U-SQL Data Lake Analytics, activité personnalisée .NET, activité de recherche et activité d’obtention des métadonnées.

> [!NOTE] 
> Utilisez le runtime d’intégration auto-hébergé pour prendre en charge les magasins de données nécessitant l’utilisation de votre propre pilote tels que SAP, Hana, MySQL, etc.  Pour en savoir plus, consultez les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Environnement réseau
Si vous souhaitez intégrer vos données en toute sécurité dans un environnement réseau privé, qui n’a pas de ligne de vue directe depuis l’environnement cloud public, vous pouvez installer un runtime d’intégration auto-hébergé dans l’environnement local derrière votre pare-feu d’entreprise, ou à l’intérieur d’un réseau privé virtuel.  Le runtime d’intégration auto-hébergé établit uniquement des connexions HTTP sortantes pour l’accès à Internet.

### <a name="compute-resource-and-scaling"></a>Ressources de calcul et mise à l’échelle
Le runtime d’intégration auto-hébergé doit être installé sur un ordinateur local ou une machine virtuelle à l’intérieur d’un réseau privé. Actuellement, nous prenons uniquement en charge l’exécution du runtime d’intégration auto-hébergé sur un système d’exploitation Windows.  

Pour obtenir un runtime d’intégration hautement disponible et évolutif, vous pouvez augmenter la taille des instances du runtime d’intégration auto-hébergé en associant l’instance logique avec plusieurs ordinateurs locaux en mode actif/actif.  Pour en savoir plus, consultez l’article « Comment créer et configurer le runtime d’intégration auto-hébergé » sous Procédures.

## <a name="azure-ssis-integration-runtime"></a>Runtime d’intégration Azure SSIS
Pour effectuer une opération lift-and-shift sur la charge de travail SSIS existante, vous pouvez créer un runtime d’intégration Azure SSIS pour exécuter les packages SSIS en mode natif.

### <a name="network-environment"></a>Environnement réseau
Le runtime d’intégration Azure SSIS peut être configuré dans un réseau public ou un réseau privé.  L’accès aux données sur site est pris en charge en associant le runtime d’intégration Azure SSIS à un réseau virtuel connecté à votre réseau local.  

### <a name="compute-resource-and-scaling"></a>Ressources de calcul et mise à l’échelle
Le runtime d’intégration Azure SSIS est un cluster entièrement géré de machines virtuelles Azure qui est chargé d’exécuter vos packages SSIS. Vous pouvez demander à votre propre serveur Azure SQL Database ou Managed Instance (préversion privée) d’héberger le catalogue de projets/packages SSIS (SSISDB) qui y sera attaché. Vous pouvez monter en puissance le calcul en spécifiant la taille du nœud et augmenter la taille des instances en spécifiant le nombre de nœuds du cluster. Vous pouvez maîtriser le coût d’exécution de votre runtime d’intégration Azure SSIS en l’arrêtant et en le démarrant comme bon vous semble.

Pour en savoir plus, consultez l’article « Comment créer et configurer le runtime d’intégration Azure SSIS » sous Procédures.  Une fois votre runtime d’intégration créé, vous pouvez déployer et gérer vos packages SSIS existants, sans changement ou presque, à l’aide des outils SQL Server Data Tools (SSDT) et SQL Server Management Studio (SSMS), comme si vous utilisez SSIS en local.

Pour plus d’informations sur le runtime Azure-SSIS, voir les articles suivants : 

- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Procédures : Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Créer un runtime d’intégration Azure-SSIS). Cet article s’appuie sur le didacticiel et fournit des instructions sur la façon d’utiliser Azure SQL Managed Instance (préversion privée) et d’associer le runtime d’intégration à un VNet. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Attacher un runtime d’intégration Azure-SSIS à un VNet). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure (VNet). Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 

## <a name="determining-which-ir-to-use"></a>Choix du runtime d’intégration
Chaque activité de transformation a un service de calcul cible lié qui pointe vers un runtime d’intégration. Cette instance du runtime d’intégration se trouve au point d’envoi de l’activité de transformation.

Dans le cas d’une activité de copie, les services liés source et récepteur doivent être indiqués pour définir la direction du flux de données. La logique suivante est utilisée pour déterminer l’instance de runtime d’intégration qui effectue la copie : 

- **Copie entre deux sources de données cloud** : lorsque les services liés source et récepteur utilisent le runtime d’intégration Azure, le runtime utilisé par le service lié récepteur exécute l’activité de copie.
- **Copie entre une source de données cloud et une source de données d’un réseau privé** : si le service lié source ou récepteur pointe vers un runtime d’intégration auto-hébergé, l’activité de copie est exécutée sur ce runtime d’intégration.
- **Copie entre deux sources de données d’un réseau privé** : les services liés source et récepteur doivent tous deux pointer vers la même instance du runtime d’intégration. Ce runtime d’intégration est utilisé pour exécuter l’activité de copie.

Le schéma suivant représente deux activités de copie :

- La source de l’activité de copie 1 est un service lié SQL Server faisant référence à un runtime d’intégration auto-hébergé A, tandis que son récepteur est un service lié Stockage Azure faisant référence à un runtime d’intégration Azure B. Par conséquent, l’activité de copie est exécutée sur le runtime d’intégration A auto-hébergé.
- Pour l’activité de copie 2, sa source est un service lié Azure SQL Database faisant référence à un runtime d’intégration Azure C, et son récepteur est un service lié Azure Storage faisant référence à un runtime d’intégration Azure B. Par conséquent, l’activité de copie est exécutée sur le runtime d’intégration Azure B puisqu’il s’agit du runtime d’intégration utilisé par le service lié récepteur.

![Runtimes d’intégration à utiliser](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Emplacement du runtime d’intégration
L’emplacement de Data Factory contient les métadonnées de la fabrique de données. C’est également à cet endroit que le pipeline est déclenché. Actuellement, les emplacements de Data Factory pris en charge se trouvent dans les régions suivantes : Est des États-Unis, Est des États-Unis 2, Asie du Sud-Est et Europe de l’Ouest. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul. Ce comportement se réalise grâce au runtime d’intégration, qui est disponible dans plusieurs régions du monde pour garantir la conformité des données et l’efficacité, et réduire les frais de sortie de réseau.

L’emplacement du runtime d’intégration définit l’emplacement de son calcul principal, mais aussi l’emplacement où le déplacement des données, la répartition des activités et l’exécution des packages SSIS sont effectués. L’emplacement du runtime d’intégration peut être différent de l’emplacement de la fabrique de données à laquelle il appartient. Le schéma suivant représente les paramètres d’emplacement de Data Factory et de ses runtimes d’intégration :

![Emplacement du runtime d’intégration](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Runtime d’intégration Azure
Pour déplacer les données, Data Factory utilise un runtime d’intégration Azure qui se trouve dans la région la plus proche du récepteur et dans la même zone géographique. Pour connaître le mappage, reportez-vous au tableau suivant :

Zone géographique du magasin de données récepteur | Emplacement du magasin de données récepteur | Emplacement utilisé pour le runtime d’intégration Azure
-------------------------------| ----------------| ------------------
États-Unis | Est des États-Unis | Est des États-Unis
&nbsp; | Est des États-Unis 2 | Est des États-Unis 2
&nbsp; | Centre des États-Unis | Centre des États-Unis
&nbsp; | Centre-Nord des États-Unis | Centre-Nord des États-Unis
&nbsp; | États-Unis - partie centrale méridionale | États-Unis - partie centrale méridionale
&nbsp; | Centre-Ouest des États-Unis | Centre-Ouest des États-Unis
&nbsp; | États-Unis de l’Ouest | États-Unis de l’Ouest
&nbsp; | Ouest des États-Unis 2 | Ouest des États-Unis 2
Canada | Est du Canada | Centre du Canada
&nbsp; | Centre du Canada | Centre du Canada
Brésil | Sud du Brésil | Sud du Brésil
Europe | Europe du Nord | Europe du Nord
&nbsp; | Europe de l'Ouest | Europe de l'Ouest
Royaume-Uni | Ouest du Royaume-Uni | Sud du Royaume-Uni
&nbsp; | Sud du Royaume-Uni | Sud du Royaume-Uni
Asie-Pacifique | Asie du Sud-Est | Asie du Sud-Est
&nbsp; | Est de l'Asie | Asie du Sud-Est
Australie | Est de l’Australie | Est de l’Australie
&nbsp; | Sud-est de l’Australie | Sud-est de l’Australie
Japon | Est du Japon | Est du Japon
&nbsp; | Ouest du Japon | Est du Japon
Corée du Sud | Centre de la Corée | Centre de la Corée
&nbsp; | Corée du Sud | Centre de la Corée
Inde | Inde centrale | Inde centrale
&nbsp; | Inde occidentale | Inde centrale
&nbsp; | Inde du Sud | Inde centrale

Vous pouvez également faire en sorte que l’emplacement d’un runtime d’intégration Azure se définisse automatiquement. Ainsi, Data Factory détectera automatiquement le meilleur emplacement à utiliser en fonction de la définition du service lié.

> [!NOTE] 
> Pour des raisons de conformité, si la région du magasin de données de destination ne figure pas dans la liste ou n’est pas détectable, l’activité de copie échoue au lieu de passer par une autre région. Dans ce cas, indiquez explicitement l’autre emplacement à utiliser pour effectuer la copie.
 
L’image suivante montre un exemple d’emplacement effectif lorsque l’emplacement du runtime d’intégration Azure est défini automatiquement. Lorsqu’une activité de copie est exécutée, il détecte l’emplacement de destination des données. Dans cet exemple, il s’agit de l’Ouest du Japon.  Selon le tableau, un runtime d’intégration Azure dans l’Est du Japon est utilisé pour copier les données. Lorsque le même runtime d’intégration est utilisé pour se connecter à HDInsight et réaliser une activité Spark, l’application Spark est envoyée depuis l’emplacement de Data Factory. Dans cet exemple, il s’agit de l’Est des États-Unis et l’exécution de l’application Spark se produit à l’emplacement du serveur HDInsight. 

![Emplacement effectif](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>Runtime d’intégration auto-hébergé
Le runtime d’intégration auto-hébergé est logiquement enregistré auprès de la fabrique de données. Quant à vous, il vous revient de fournir le calcul utilisé pour prendre en charge ses fonctionnalités. Par conséquent, il n’existe aucune propriété d’emplacement explicite pour le runtime d’intégration auto-hébergé. 

Lorsqu’il est utilisé pour procéder au déplacement des données, le runtime d’intégration auto-hébergé extrait des données de la source et les écrit dans la destination.

### <a name="azure-ssis-ir"></a>Runtime d’intégration Azure SSIS
Le choix de l’emplacement pour votre runtime d’intégration Azure SSIS est essentiel pour parvenir à un niveau de performance élevé dans vos flux de travail ETL (extraction, transformation et chargement).  Six emplacements sont initialement disponibles en version préliminaire (Est des États-Unis, Est des États-Unis 2, Centre des États-Unis, Est de l’Australie, Europe du Nord et Europe de l’Ouest).

- L’emplacement de votre runtime d’intégration Azure SSIS ne doit pas nécessairement être identique à l’emplacement de votre fabrique de données, mais il doit être le même que l’emplacement de votre serveur de l’Instance Azure SQL Database/Managed Instance (préversion privée) où SSISDB doit être hébergé. De cette manière, le runtime d’intégration Azure SSIS peut facilement accéder au SSISDB sans être entravé par le trafic entre les différents emplacements.
- Si vous n’avez pas de serveur Azure SQL Database/Managed Instance (préversion privée) pour héberger SSISDB, mais que vous avez des sources/destinations de données locales, vous devez créer un serveur Azure SQL Database/Managed Instance (préversion privée) là où un réseau virtuel est connecté à votre réseau local.  Ainsi, vous pouvez créer votre runtime d’intégration Azure SSIS en utilisant le nouveau serveur Azure SQL Database/Managed Instance (préversion privée) et en associant ce réseau virtuel, au même endroit, afin de réduire efficacement les déplacements de données entre les différents emplacements.
- Si l’emplacement de votre serveur Azure SQL Database/Managed Instance (préversion privée) où SSISDB est hébergé n’est pas le même que l’emplacement où un réseau virtuel est connecté à votre réseau local, créez d’abord votre runtime d’intégration Azure SSIS en utilisant Azure SQL Database/Managed Instance (préversion privée) et en associant un autre réseau virtuel situé au même emplacement. Ensuite, configurez une connexion entre deux réseaux virtuels situés à différents emplacements.


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [Créer un runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md)
- [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel et fournit des instructions sur la façon d’utiliser Azure SQL Managed Instance (préversion privée) et d’associer le runtime d’intégration à un VNet. 
