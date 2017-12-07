---
title: "Guide pratique pour exporter des bases de données U-SQL à l’aide d’Azure Data Lake Tools pour Visual Studio | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio pour exporter une base de données U-SQL et l’importer dans un compte local en même temps."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>Comment exporter une base de données U-SQL

Dans ce document, vous allez apprendre à utiliser [Azure Data Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs) pour exporter une base de données U-SQL sous la forme d’un seul script U-SQL et de ressources téléchargées. L’importation de la base de données exportée dans le compte local est également prise en charge dans le même processus.

Les clients gèrent généralement plusieurs environnements de développement, de test et de production. Ces environnements sont hébergés à la fois sur un compte local de l’ordinateur local des développeurs et sur un compte Azure Data Lake Analytics sur Azure. Quand ils développent et ajustent des requêtes U-SQL sur des environnements de développement et de test, les développeurs ont souvent besoin de tout recréer dans la base de données de production. L’**Assistant Exportation de base de données** contribue à accélérer ce processus. À l’aide de l’Assistant, les développeurs peuvent cloner l’environnement de base de données existant et échantillonner des données vers d’autres comptes Azure Data Lake Analytics.

## <a name="export-steps"></a>Opérations d’exportation

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>Étape 1 : Cliquer avec le bouton droit sur la base de données dans l’Explorateur de serveurs et cliquer sur « Exporter... »

Tous les comptes Azure Data Lake Analytics auxquels vous avez accès sont répertoriés dans l’Explorateur de serveurs. Développez celui qui contient la base de données que vous voulez exporter, cliquez avec le bouton droit sur la base de données pour choisir **Exporter...** Si vous ne trouvez pas le menu contextuel, vous devez [mettre à jour l’outil avec la dernière version](http://aka.ms/adltoolsvs).

![Exportation de base de données par les outils Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>Étape 2 : Configurer les objets à exporter

Parfois, une base de données est volumineuse, alors que vous avez simplement besoin d’une petite partie de celle-ci. Vous pouvez alors configurer la partie des objets à exporter dans l’Assistant Exportation. Notez que l’action d’exportation s’achève en exécutant un travail U-SQL, et par conséquent, une exportation à partir d’un compte Azure implique un coût.

![Assistant Exportation de base de données des outils Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>Étape 3 : Vérifier la liste des objets et d’autres configurations

Dans cette étape, vous pouvez vérifier soigneusement les objets sélectionnés en haut de la boîte de dialogue. S’il existe des erreurs, vous pouvez cliquer sur Précédent pour revenir en arrière et reconfigurer les objets que vous voulez exporter.

Vous pouvez également effectuer d’autres configurations sur la cible de l’exportation, les descriptions de ces configurations sont répertoriées dans tableau ci-dessous :

|Configuration|Description|
|-------------|-----------|
|Nom de la destination|Ce nom indique où vous souhaitez enregistrer les ressources de base de données exportées, comme des assemblys, des fichiers supplémentaires et des exemples de données. Un dossier portant ce nom est créé sous le dossier racine de vos données locales.|
|Répertoire du projet|Ce chemin définit l’emplacement où vous souhaitez enregistrer le script U-SQL exporté, lequel inclut toutes les définitions d’objets de base de données.|
|Schéma uniquement|La sélection de cette option entraîne l’exportation des définitions de base de données et des ressources (comme les assemblys et les fichiers supplémentaires) uniquement.|
|Schéma et données|La sélection de cette option entraîne l’exportation des définitions de base de données, des ressources et des données. Les N premières lignes des tables sont exportées.|
|Importer automatiquement dans la base de données locale|La vérification de cette configuration signifie que la base de données exportée est importée dans votre base de données locale automatiquement une fois que l’exportation est terminée.|

![Configuration de l’Assistant Exportation de base de données des outils Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Étape 4 : Vérifier les résultats de l’exportation

Une fois que tous ces paramètres sont configurés et l’exportation effectuée, vous pouvez consulter les résultats exportés dans la fenêtre de journal de l’Assistant. Dans le journal marqué par un rectangle rouge dans la capture d’écran ci-dessous, vous pouvez trouver l’emplacement du script U-SQL exporté et des ressources de base de données, notamment les assemblys, les fichiers supplémentaires et les exemples de données.

![Assistant Exportation de base de données des outils Data Lake Analytics terminé](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Comment importer la base de données exportée dans le compte local

La manière la plus pratique d’effectuer cette importation consiste à cocher la case **Importer automatiquement dans la base de données locale** pendant la progression de l’exportation à l’étape 3. Si vous avez oublié de le faire, vous pouvez rechercher le script U-SQL exporté dans le journal d’exportation et exécuter le script U-SQL localement pour importer la base de données dans votre compte local.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Comment importer la base de données exportée dans le compte Azure Data Lake Analytics

Pour importer la base de données dans un autre compte Azure Data Lake Analytics, vous avez besoin de deux étapes :

1. Chargez les ressources exportées, notamment les assemblys, les fichiers supplémentaires et les exemples de données dans le compte Azure Data Lake Store par défaut du compte Azure Data Lake Analytics dans lequel vous voulez effectuer l’importation. Vous pouvez rechercher le dossier des ressources exportées sous le dossier racine des données locales, puis charger la totalité du dossier à la racine du compte de stockage par défaut.
2. Envoyez le script U-SQL exporté au compte Azure Data Lake Analytics dans lequel vous souhaitez importer la base de données une fois le chargement terminé.

## <a name="known-limitation"></a>Limitation connue

Actuellement, si vous avez sélectionné **Schéma et données** dans l’Assistant, l’outil exécute un travail U-SQL pour exporter les données stockées dans des tables. C’est pourquoi le processus d’exportation des données peut être lent et entraîner des frais. 

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre la base de données U-SQL](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Comment tester et déboguer des travaux U-SQL à l’aide d’une exécution locale et du SDK Azure Data Lake U-SQL](data-lake-analytics-data-lake-tools-local-run.md)


