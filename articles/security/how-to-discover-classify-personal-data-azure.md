---
title: Découvrir, identifier et classer des données personnelles dans Microsoft Azure | Microsoft Docs
description: des données de recherche, de classification, de découverte et d’identification dans Azure peuvent vous aider à être conforme au Règlement général de protection des données (RGPD)
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 21308faf5d3efd3908eccab4f37acb0cfccdcb8e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Découvrir, identifier et classer des données personnelles dans Microsoft Azure

Cet article donne des indications sur la façon de découvrir, identifier et classer des données personnelles dans plusieurs outils et services Azure, notamment en utilisant Azure Data Catalog, Azure Active Directory, SQL Database, Power Query pour clusters Hadoop dans Azure HDInsight, Azure Information Protection, la Recherche Azure et les requêtes SQL pour Azure Cosmos DB. Ces outils et processus peuvent être utilisés pour se conformer au Règlement général sur la protection des données (RGPD).

## <a name="scenario-problem-statement-and-goal"></a>Scénario, énoncé du problème et objectifs

Une société américaine d’articles de sport recueille diverses données de nature personnelle et autre. Il s’agit notamment de données sur les clients et les employés. La société les conserve dans plusieurs bases de données et les stocke à plusieurs emplacements dans son environnement Azure. En plus de commercialiser des articles de sport, elle héberge et gère les inscriptions à des événements sportifs de haut niveau dans le monde entier, notamment aux États-Unis. Dans certains cas, les données qu’elle recueille sur les clients contiennent des informations médicales.

Sachant que la société organise chaque année de nombreuses épreuves cyclistes internationales et qu’elle dispose d’un personnel contractuel partout dans le monde, elle compte des jeux de données assez volumineux. Par ailleurs, la société dispose d’applications créées par des développeurs que les clients et les employés utilisent.

La société souhaite s’attaquer aux problèmes suivants :

- Les données personnelles des clients et des employés doivent être classées/différenciées des autres données que la société collecte de façon à garantir un accès et une sécurité appropriés.
- L’administrateur des données doit pouvoir découvrir facilement l’emplacement des données personnelles des clients dans les différentes parties de l’environnement Azure.
- Les données personnelles des clients et des employés qui apparaissent dans les documents partagés et les e-mails doivent être étiquetées pour être sécurisées.
- Les développeurs d’applications de la société doivent pouvoir rechercher facilement les données personnelles des clients et des employés dans leurs applications web et mobiles.
- Les développeurs doivent aussi pouvoir rechercher les données personnelles dans leur base de données de documents.

### <a name="company-goals"></a>Objectifs de la société

- Toutes les données personnelles des clients et des employés doivent être balisées/annotées dans Azure Data Catalog pour faciliter leur recherche. Dans l’idéal, les données personnelles des clients et des employés doivent être balisés/annotées séparément.
- Les données personnelles tirées des informations professionnelles et de profil utilisateur des clients et des employés résidant dans Azure Active Directory doivent pouvoir être localisées facilement.
- Les données personnelles résidant dans plusieurs bases de données SQL doivent pouvoir être interrogées facilement. 
- Certains jeux de données volumineux de la société sont gérés via Azure HDInsight et stockés dans Hadoop. Ils doivent être importés dans Excel pour pouvoir faire l’objet de requêtes relatives aux données personnelles.
- Les données personnelles partagées dans les documents et les e-mails doivent être classées, étiquetées et sécurisées avec Azure Information Protection.
- Les développeurs d’applications de la société doivent pouvoir découvrir les données personnelles des clients et des employés dans les applications qu’ils ont créées, et ce notamment avec le service Recherche Azure.
- Les développeurs doivent pouvoir rechercher les données personnelles dans leur base de données de documents.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory : découverte de données

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le service Microsoft de gestion d’annuaires et d’identités multilocataire basé sur le cloud. Vous pouvez localiser les informations professionnelles et de profil utilisateur des clients et des employés qui contiennent des données personnelles dans votre environnement [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) via le [portail Azure](https://portal.azure.com/).

C’est particulièrement utile si vous voulez rechercher ou modifier les données personnelles d’un utilisateur déterminé. Vous pouvez aussi ajouter ou modifier des informations professionnelles et de profil utilisateur. Vous devez vous connecter avec un compte d’administrateur général de l’annuaire.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Comment localiser ou afficher des informations professionnelles et de profil utilisateur ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Tous les services**, entrez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

   ![comment localiser des informations professionnelles et de profil utilisateur](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Utilisateurs**.

      ![Ouverture du panneau Utilisateurs et groupes](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. Dans le panneau **Utilisateurs et groupes - Utilisateurs**, sélectionnez un utilisateur dans la liste puis, dans le panneau de l’utilisateur sélectionné, sélectionnez **Profil** pour afficher les informations de profil utilisateur susceptibles de contenir des données personnelles.

      ![sélectionner un utilisateur](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Si vous avez besoin d’ajouter ou de modifier des informations de profil utilisateur, sélectionnez **Enregistrer** dans la barre de commandes après avoir effectué cette opération.
6. Dans le panneau de l’utilisateur sélectionné, sélectionnez **Informations professionnelles** pour afficher les informations professionnelles utilisateur susceptibles de contenir des données personnelles.

     ![affichage d’informations professionnelles](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Si vous avez besoin d’ajouter ou de modifier des informations professionnelles utilisateur, sélectionnez **Enregistrer** dans la barre de commandes après avoir effectué cette opération.

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database : découverte de données

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) est une base de données cloud qui permet aux développeurs de créer et de tenir à jour des applications. Il est possible de rechercher des données personnelles dans [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) à l’aide de requêtes SQL standard. La fonctionnalité de requête élastique SQL Azure (préversion) permet aux utilisateurs d’exécuter des requêtes entre plusieurs bases de données.

Un didacticiel détaillé [SQL Database](../sql-database/sql-database-technical-overview.md) explique l’utilisation d’une base de données SQL sous divers angles, notamment la création d’une base de données et l’exécution de requêtes de données. Vous trouverez ci-dessous un récapitulatif des informations disponibles dans le didacticiel avec des liens vers des sections spécifiques.

### <a name="how-do-i-build-a-sql-database"></a>Comment créer une base de données SQL ?

Il existe trois façons de procéder :

- Une base de données Azure SQL Database peut être créée dans le [portail Azure](https://portal.azure.com/). Dans le didacticiel, vous allez utiliser un ensemble spécifique de ressources de calcul et de stockage au sein d’un groupe de ressources et d’un serveur logique. Vous allez utiliser les données d’exemple d’une société fictive appelée AdventureWorks. Par ailleurs, vous allez créer une règle de pare-feu au niveau du serveur. Pour savoir comme faire, consultez le didacticiel [Création d’une base de données SQL Azure à l’aide du portail Azure](../sql-database/sql-database-get-started-portal.md).

  ![Créer une base de données Azure SQL Database](media/how-to-discover-classify-personal-data-azure/create-database.png)
- Une base de données SQL peut aussi être créée dans l’interface CLI [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), qui est un outil en ligne de commande basé sur un navigateur. L’outil est disponible dans le portail Azure et peut être exécuté directement de cet emplacement. Dans ce didacticiel, vous allez lancer l’outil, définir des variables de script, créer un groupe de ressources et un serveur logique, puis configurer une règle de pare-feu serveur. Vous créerez ensuite une base de données à partir de données d’exemple. Pour savoir comment créer une base de données de cette manière, consultez le didacticiel [Créer une base de données SQL Azure à l’aide de l’interface de ligne de commande Azure](../sql-database/sql-database-get-started-cli.md).

  ![Didacticiel CLI](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Azure CLI est couramment utilisé par les administrateurs et les développeurs Linux. Certains utilisateurs trouvent cet outil plus simple et plus intuitif que PowerShell, qui constitue la troisième option.

- Enfin, vous pouvez créer une base de données SQL à l’aide de PowerShell, qui est un outil en ligne de commande/de script qui permet de créer et de gérer Azure et d’autres ressources. Dans ce didacticiel, vous allez lancer l’outil, définir des variables de script, créer un groupe de ressources et un serveur logique, puis configurer une règle de pare-feu serveur. Vous créerez ensuite une base de données à partir de données d’exemple.

Ce didacticiel nécessite le module Azure PowerShell version 4.0 ou ultérieure. Exécutez Get-Module - ListAvailable AzureRM pour trouver votre version. Si vous avez besoin de procéder à une installation ou à une mise à niveau, consultez Installer le module Azure PowerShell.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Pour savoir comment créer une base de données de cette manière, consultez le didacticiel [Créer une base de données SQL Azure unique à l’aide de PowerShell](../sql-database/sql-database-get-started-powershell.md).

>[!Note]
Les administrateurs Windows ont tendance à utiliser PowerShell, mais certains d’entre eux préfèrent Azure CLI.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Comment rechercher des données personnelles dans une base de données SQL dans le portail Azure ?

Vous pouvez utiliser l’outil Éditeur de requêtes intégré au portail Azure pour rechercher des données personnelles. Pour cela, vous devez vous connecter avec votre compte de connexion et votre mot de passe d’administrateur SQL Server, puis entrer une requête.

  ![recherche sql à partir du portail](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

L’étape 5 du didacticiel montre un exemple de requête dans le volet de l’Éditeur de requêtes, mais elle ne se limite pas aux informations personnelles ou sensibles (elle combine aussi les données de deux tables et crée des alias pour la colonne source du jeu de données retourné). La capture d’écran suivante présente la requête de l’étape 5, ainsi que le volet de résultats retourné :

  ![query editor](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Si votre base de données s’intitule MaTable, un exemple de requête portant sur des informations personnelles pourrait inclure le nom, le numéro de sécurité sociale et le numéro d’identification et ressembler à ceci :

“SELECT Nom, SSN, Numero_identification FROM MaTable”

Vous exécuteriez la requête et les résultats s’afficheraient dans le volet **Résultats**.

Pour plus d’informations sur la façon d’interroger une base de données SQL dans le portail Azure, consultez la section [Interroger la base de données SQL](../sql-database/sql-database-get-started-portal.md) du didacticiel.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Comment rechercher des données dans plusieurs bases de données ?

La fonctionnalité de requête élastique SQL (préversion) permet d’exécuter des requêtes dans plusieurs bases de données et de retourner un résultat unique. Dans la [présentation du didacticiel](../sql-database/sql-database-elastic-query-overview.md), vous trouverez une description détaillée des scénarios et une explication de ce qui distingue un partitionnement vertical d’un partitionnement horizontal de base de données. Le partitionnement horizontal est appelé « sharding ».

  ![Partitionnement vertical](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![partitionnement horizontal](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Pour commencer, consultez la page [Vue d’ensemble de la requête élastique Azure SQL Database (préversion)](../sql-database/sql-database-elastic-query-overview.md).

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (pour l’importation de clusters Hadoop Azure HDInsight) : découverte de données pour les jeux de données volumineux

Hadoop est un service de stockage et de traitement Apache open source conçu pour les jeux de données volumineux, qui sont analysés et stockés dans des clusters Hadoop. [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) permet aux utilisateurs d’utiliser des clusters Hadoop dans Azure. Power Query est un complément Excel qui permet notamment aux utilisateurs de découvrir des données issues de différentes sources.

Les données personnelles associées aux clusters Hadoop dans Azure HDInsight peuvent être importées dans Excel à l’aide de Power Query. Une fois que les données se trouvent dans Excel, vous pouvez utiliser une requête pour les identifier.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Comment utiliser Excel Power Query pour importer des clusters Hadoop d’Azure HDInsight vers Excel ?

Il existe un didacticiel HDInsight qui vous guidera tout au long de ce processus. Vous y trouverez les prérequis, ainsi qu’un lien vers le didacticiel [Bien démarrer avec Azure HDInsight](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md). Les instructions portent sur Excel 2016 et sur les versions 2013 et 2010 (les étapes sont légèrement différentes pour les anciennes versions d’Excel). Si vous ne disposez pas du complément Excel Power Query, le didacticiel vous indique comment vous le procurer. Vous démarrez le didacticiel dans Excel et devez disposer d’un compte de stockage Blob Azure associé à votre cluster.

  ![Interroger dans Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Pour savoir comment procéder, consultez le didacticiel [Connexion d’Excel à Hadoop à l’aide de Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md).

Source : [Connexion d’Excel à Hadoop à l’aide de Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection : classification des données personnelles pour les documents et les e-mails

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) permet aux clients Azure d’appliquer des étiquettes pour classer et protéger les documents partagés en interne ou en externe, ainsi que les e-mails. Certains de ces éléments peuvent contenir des informations personnelles sur les clients ou les employés. Les règles et les conditions peuvent être définies automatiquement ou manuellement par les administrateurs ou les utilisateurs. Par exemple, si un utilisateur enregistre un document qui contient des informations de carte de crédit, il obtient une recommandation qui a été configurée par l’administrateur sous forme d’étiquette.

### <a name="how-do-i-try-it"></a>Comment tester le produit ?

Si vous voulez tester Azure Information Protection pour voir si cette solution peut convenir à votre organisation, consultez le [didacticiel de démarrage rapide](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). Il vous guide tout au long des cinq étapes de base (de l’installation jusqu’à la configuration d’une stratégie, en passant par la présentation concrète des étapes de classification, d’étiquetage et de partage) et vous prend moins d’une demi-heure.

### <a name="how-do-i-deploy-it"></a>Comment déployer le produit ?

Si vous voulez déployer Azure Information Protection pour votre organisation, consultez [Feuille de route du déploiement pour la classification, l’étiquetage et la protection](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>Y a-t-il autre chose à savoir ?

Pour obtenir des informations complémentaires qui vous aideront à configurer la solution, consultez le billet de blog [Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
. Consultez également les liens En savoir plus présentés ci-après pour en savoir plus sur Azure Information Protection.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Recherche Azure : découverte de données pour les applications de développeurs

Le service [Recherche Azure](https://azure.microsoft.com/services/search/) est une solution de recherche cloud qui s’adresse aux développeurs et offre une expérience de recherche de données avancée pour vos applications. Recherche Azure vous permet de localiser des données dans des index définis par l’utilisateur, en provenance d’Azure Cosmo DB, d’Azure SQL Database, du Stockage Blob Azure, du stockage Table Azure ou de données JSON personnalisées de clients. Vous pouvez aussi structurer des requêtes Lucene à l’aide de l’API REST Recherche Azure pour rechercher des types de données personnelles ou les données personnelles de personnes spécifiques. Elle intègre les fonctionnalités suivantes : recherche en texte intégral, syntaxe de requête simple et syntaxe de requête Lucene. 

## <a name="how-do-i-use-sql-to-query-data"></a>Comment utiliser SQL pour interroger des données ?

Pour vous initier aux principes de base, consultez le didacticiel [Azure Cosmos DB : Comment effectuer des interrogations avec le langage SQL ?](../cosmos-db/tutorial-query-documentdb.md). Ce didacticiel présente un exemple de document et deux exemples de requêtes SQL et de résultats.

Pour obtenir des instructions détaillées sur la création de requêtes SQL, consultez [Requêtes SQL pour l’API DocumentDB Azure Cosmos DB](../cosmos-db/sql-api-sql-query.md).

Si vous débutez avec Azure Cosmos DB et que vous voulez apprendre à créer une base de données, ajouter une collection et ajouter des données, consultez le didacticiel de démarrage rapide [Azure Cosmos DB : Développer une application web API SQL](../cosmos-db/create-sql-api-dotnet.md). Si vous préférez utiliser un autre langage que .NET, par exemple Java ou Python, choisissez simplement votre langage préféré une fois que vous êtes sur le site.

## <a name="next-steps"></a>Étapes suivantes

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[Définition de la base de données SQL](../sql-database/sql-database-technical-overview.md)

[Éditeur de requêtes SQL Database disponible dans le portail Azure] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Qu’est-ce qu’Azure Information Protection ?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[En quoi consiste Azure Rights Management ?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
