---
title: Introduction à Azure Data Factory | Microsoft Docs
description: Découvrez Azure Data Factory, un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données.
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
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 89f5391cfbfd85f9f0715a8c7d288cc3981cc618
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-azure-data-factory"></a>Présentation d'Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-introduction.md)
> * [Version 2 - Préversion](introduction.md)

Dans le monde des Big Data, les données brutes et désorganisées sont souvent enregistrées dans des systèmes de stockage relationnels, non-relationnels et autres systèmes. Toutefois, lorsqu’elles sont isolées, les données brutes n’ont pas le contexte ou la signification appropriés pour fournir des informations significatives aux analystes, aux scientifiques des données ou aux décideurs en entreprise. 

Les Big Data requièrent un service qui permet d’orchestrer et d’opérationnaliser les processus pour affiner ces gigantesques magasins de données brutes pour les transformer en informations exploitables. Azure Data Factory est un service cloud géré créé pour ces projets complexes d’extraction, de transformation et de chargement (ETL), d’extraction, de chargement et de transformation (ELT) et d’intégration des données.

Prenons l’exemple d’une société d’édition de jeux qui rassemble plusieurs pétaoctets de journaux sur les jeux générés par les jeux dans le cloud. La société souhaite analyser ces journaux pour obtenir des informations détaillées sur les préférences des clients, les données démographiques et le comportement d’utilisation. Elle souhaite également identifier les opportunités de vente incitative et de ventes croisées, développer de nouvelles fonctionnalités attrayantes, accroître son activité et fournir une meilleure expérience à ses clients.

Pour analyser ces journaux, l’entreprise doit utiliser des données de référence comme des informations sur le client, des informations sur les jeux et des informations sur la campagne marketing qui sont contenues dans un magasin de données local. L’entreprise souhaite utiliser ces données issues du magasin de données local et les combiner avec d’autres données de journal dont elle dispose dans un magasin de données dans le cloud. 

Pour extraire des informations, elle souhaite traiter les données combinées au moyen d’un cluster Spark dans le cloud (Azure HDInsight) et publier les données transformées dans un entrepôt de données cloud, tel qu’Azure SQL Data Warehouse, afin de pouvoir créer facilement un rapport à partir de celles-ci. Ils souhaitent automatiser ce flux de travail, et le surveiller et le gérer sur une base quotidienne. Ils veulent également l’exécuter lorsque les fichiers arrivent dans un conteneur de magasin d’objets blob.

Azure Data Factory est la plateforme qui résout les scénarios de données de ce type. Il s’agit d’un *service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement des données et la transformation des données*. À l’aide d’Azure Data Factory, vous pouvez créer et planifier des flux de travail pilotés par les données (appelés pipelines) qui peuvent ingérer des données provenant de différents magasins de données. Il peut traiter et transformer les données à l’aide de services de calcul tels que Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning. 

En outre, vous pouvez publier des données de sortie vers des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser. Enfin, via Azure Data Factory, les données brutes peuvent être organisées en magasins de données et lacs de données pertinents en vue d’améliorer les prises de décisions.

![Vue de niveau supérieur de Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Introduction à Data Factory version 1](v1/data-factory-introduction.md).

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?
Les pipelines (flux de travail orientés données) dans Azure Data Factory effectuent généralement les quatre étapes suivantes :

![Quatre étapes d’un flux de travail orienté données](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Se connecter et collecter

Les entreprises disposent de données de différents types situées dans différentes sources locales, dans le cloud, structurées, non structurées et semi-structurées, toutes récupérées à des intervalles et à des vitesses variables. 

La première étape dans la création d'un système de production d’informations consiste à se connecter à toutes les sources nécessaires de données et de traitement des données comme les services Logiciel en tant que service (SaaS) et les services web de bases de données, de partage de fichiers et FTP. L’étape suivante consiste à déplacer les données souhaitées vers un emplacement centralisé pour un traitement ultérieur.

Sans Data Factory, les entreprises doivent concevoir des composants personnalisés chargés du déplacement des données ou écrire des services personnalisés pour intégrer ces sources de données et leur traitement. Il est coûteux et difficile d’intégrer et de gérer ces systèmes. En outre, ils sont souvent dépourvus des fonctionnalités de surveillance, d’alertes et de contrôles de niveau entreprise qu’un service entièrement géré peut offrir.

Avec Data Factory, vous pouvez utiliser l’[activité de copie](copy-activity-overview.md) dans un pipeline de données pour déplacer les données source des magasins de données locaux et dans le cloud vers un magasin de données centralisé dans le cloud, à des fins d’analyse ultérieure. Par exemple, vous pouvez collecter des données dans Azure Data Lake Store, puis les transformer ultérieurement à l’aide d’un service de calcul Azure Data Lake Analytics. Vous pouvez aussi collecter des données dans un stockage Blob Azure, puis les transformer ultérieurement à l’aide d’un cluster Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformer et enrichir
Une fois que les données sont présentes dans un magasin de données centralisé dans le cloud, traitez ou transformez les données collectées à l’aide de services de calcul tels que HDInsight Hadoop, Spark, Data Lake Analytics et Machine Learning. Vous souhaitez générer de manière fiable des données transformées selon une planification facile à gérer et contrôlée afin de fournir aux environnements de production des données approuvées.

### <a name="publish"></a>Publish
Une fois que les données brutes ont été affinées sous une forme utilisable par l’entreprise, chargez-les dans Azure Data Warehouse, Azure SQL Database, Azure CosmosDB ou n’importe quel moteur d’analyse auquel vos utilisateurs peuvent accéder à partir de leurs outils d’analyse décisionnelle.

### <a name="monitor"></a>Surveiller
Une fois que vous avez créé et déployé votre pipeline d’intégration de données afin de bénéficier de la valeur commerciale fournie par les données affinées, surveillez les activités planifiées et les pipelines pour connaître les taux de réussite et d’échec. Azure Data Factory intègre une prise en charge de la surveillance des pipelines via Azure Monitor, les API, PowerShell, Log Analytics et les panneaux de contrôle d’intégrité du portail Azure.

## <a name="top-level-concepts"></a>Concepts de niveau supérieur
Un abonnement Azure peut contenir une ou plusieurs instances Azure Data Factory (ou fabriques de données). Azure Data Factory s’articule autour de quatre composants clés. Ces composants fonctionnent ensemble et vous dotent de la plateforme sur laquelle composer des flux de travail orientés données constitués d’étapes de déplacement et de transformation des données.

### <a name="pipeline"></a>Pipeline
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline est un regroupement logique des activités nécessaires pour effectuer une unité de travail. Ensemble, les activités d’un pipeline effectuent une tâche. Par exemple, un pipeline peut contenir un groupe d’activités qui ingère des données à partir d’un objet Blob Azure, puis exécute une requête Hive sur un cluster HDInsight pour partitionner les données. 

L’avantage de cette opération, c’est que le pipeline vous permet de gérer les activités en tant qu’ensemble et non pas individuellement. Les activités d’un pipeline peuvent être chaînées pour fonctionner de manière séquentielle ou peuvent fonctionner en parallèle de façon indépendante.

### <a name="activity"></a>Activité
Les activités représentent une étape de traitement dans un pipeline. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’un magasin de données vers un autre. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge trois types d’activités : les activités de déplacement des données, les activités de transformation des données et les activités de contrôle.

### <a name="datasets"></a>Groupes de données
Les jeux de données représentent les structures des données dans les magasins. Ils pointent vers ou référencent simplement en tant qu’entrées ou sorties les données que vous voulez utiliser dans vos activités. 

### <a name="linked-services"></a>Services liés
Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Voyez les choses de la façon suivante : un service lié définit la connexion à la source de données et un jeu de données représente la structure des données. Par exemple, un service lié Stockage Azure spécifie la chaîne de connexion pour se connecter au compte de stockage Azure. De plus, un jeu de données blob Azure spécifie le conteneur d’objets blob et le dossier qui contient les données.

Data Factory fait appel aux services liés pour deux raisons :

- Pour représenter un **magasin de données** qui inclut, sans s’y limiter, une base de données SQL Server, une base de données Oracle, un partage de fichiers locaux ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des banques de données prises en charge, consultez l’article sur l’[activité de copie](copy-activity-overview.md).

- Pour représenter une **ressource de calcul** qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsightHive s’exécute sur un cluster HDInsight Hadoop. Pour obtenir la liste des activités de transformation et des environnements Compute pris en charge, consultez l’article sur la [transformation des données](transform-data.md).

### <a name="triggers"></a>Déclencheurs
Les déclencheurs correspondent à l’unité de traitement qui détermine le moment auquel une exécution de pipeline doit être lancée. Il existe différents types de déclencheurs pour différents types d’événements. Pour l’aperçu, Data Factory prend en charge le déclencheur par planificateur de temps horloge. 

### <a name="pipeline-runs"></a>Exécutions de pipeline
Une exécution du pipeline est une instance de l’exécution du pipeline. Les exécutions de pipeline sont généralement instanciées par la transmission des arguments aux paramètres définis dans les pipelines. Les arguments peuvent être transmis manuellement ou être inclus dans la définition du déclencheur.

### <a name="parameters"></a>parameters
Les paramètres sont des paires clé-valeur de configuration en lecture seule.  Les paramètres sont définis dans le pipeline. Les arguments des paramètres définis sont transmis au cours de l’exécution à partir du contexte d’exécution qui a été créé par un déclencheur ou un pipeline qui a été exécuté manuellement. Les activités contenues dans le pipeline utilisent les valeurs des paramètres.

Un jeu de données est un paramètre fortement typé et une entité réutilisable/pouvant être référencée. Une activité peut faire référence à des jeux de données et utiliser les propriétés définies dans la définition du jeu de données.

Un service lié est également un paramètre fortement typé qui contient les informations de connexion à un magasin de données ou à un environnement Compute. C’est également une entité réutilisable/pouvant être référencée.

### <a name="control-flow"></a>Flux de contrôle
Control flow est une orchestration des activités du pipeline, qui inclut le chaînage des activités en une séquence, la création de branches, la définition de paramètres au niveau du pipeline et la transmission des arguments lors de l’appel du pipeline à la demande ou à partir d’un déclencheur. Il inclut également la transmission d’états personnalisés et le bouclage des conteneurs, autrement dit, des itérateurs ForEach.


Pour plus d’informations sur les concepts de Data Factory, consultez les articles suivants :

- [Jeux de données et services liés](concepts-datasets-linked-services.md)
- [Pipelines et activités](concepts-pipelines-activities.md)
- [Runtime d’intégration](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Régions prises en charge

Actuellement, vous pouvez créer des fabriques de données aux États-Unis de l'Est, États-Unis de l'Est 2 et en Europe de l'Ouest. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul.

Azure Data Factory ne permet pas en soi de stocker des données. Il vous permet de créer des flux de travail pilotés par les données afin d’orchestrer le déplacement de données entre les magasins de données pris en charge, ainsi que le traitement des données à l’aide des services de calcul situés dans d’autres régions ou dans un environnement local. Il vous permet également de surveiller et gérer des flux de travail au moyen de programmes et à l’aide des mécanismes de l’interface utilisateur.

Même si Data Factory est disponible uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et en Europe de l'Ouest, le service de déplacement des données intégré à Data Factory est disponible mondialement dans plusieurs régions. Si un magasin de données se trouve derrière un pare-feu, le déplacement des données est assuré au moyen d’un runtime d’intégration auto-hébergé installé dans votre environnement local.

Supposons que vos environnements de calcul (cluster Azure HDInsight et Azure Machine Learning, par exemple) s’exécutent hors de la région Europe de l’ouest. Vous pouvez dans ce cas créer et utiliser une instance Azure Data Factory dans l’Est des États-Unis en ou l’Est des États-Unis 2 et l’utiliser pour planifier des tâches sur vos environnements de calcul en Europe de l’ouest. Quelques millisecondes suffisent à Data Factory pour déclencher la tâche dans votre environnement de calcul, mais l’heure d’exécution du travail dans votre environnement informatique ne change pas.

## <a name="compare-with-version-2"></a>Comparer avec la version 2
Pour obtenir la liste des différences entre les versions 1 et 2 du service Data Factory, consultez [Comparer avec la version 1](compare-versions.md). 

## <a name="next-steps"></a>Étapes suivantes
Prise en main de la création d’un pipeline Data Factory à l’aide d’un des outils/kits de développement logiciel suivants : 

- [IU Data Factory dans le portail Azure](quickstart-create-data-factory-portal.md)
- [Outil Copier les données dans le portail Azure](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Modèle Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md)
 
