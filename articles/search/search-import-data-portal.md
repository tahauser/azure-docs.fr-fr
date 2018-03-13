---
title: "Importer des données dans Azure Search dans le portail | Documents Microsoft"
description: "Utilisez l’Assistant Importer des données de Recherche Azure dans le portail Azure pour analyser des données Azure à partir de NoSQL Azure Cosmos DB, de Stockage Blob, de Stockage Table, de SQL Database et de SQL Server sur des machines virtuelles Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: Azure Portal
ms.assetid: f40fe07a-0536-485d-8dfa-8226eb72e2cd
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 286408fb0c3aa2b83cf17a4646f6f7420b6884f4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="import-data-to-azure-search-using-the-portal"></a>Importer des données dans Azure Search à l’aide du portail
Le portail Azure propose, sur le tableau de bord Azure Search, un Assistant **Importer des données** qui vous permet de charger des données dans un index. 

  ![Importer des données dans la barre de commandes][1]

En interne, l’Assistant configure et appelle un *indexeur*, ce qui permet d’automatiser plusieurs étapes du processus d’indexation : 

* Connexion à une source de données externe dans le même abonnement Azure
* Génération d’un schéma d’index modifiable basé sur la structure des données source
* Chargement de documents JSON dans un index à l’aide d’un ensemble de lignes extrait de la source de données

Vous pouvez tester ce workflow à l’aide d’exemples de données dans Azure Cosmos DB. Pour obtenir des instructions, consultez la page [Prise en main d’Azure Search dans le portail Azure](search-get-started-portal.md) .

> [!NOTE]
> Vous pouvez lancer l’Assistant **Importation de données** sur le tableau de bord Azure Cosmos DB afin de simplifier l’indexation de cette source de données. Dans la navigation de gauche, accédez à **Collections** > **Ajouter la Recherche Azure** pour commencer.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Sources de données prises en charge par l’Assistant Importer des données
L’Assistant Importation de données prend en charge les sources de données suivantes : 

* Base de données SQL Azure
* Données relationnelles SQL Server sur une machine virtuelle Azure
* Azure Cosmos DB
* Stockage d'objets blob Azure
* Stockage de tables Azure

Un jeu de données aplati est requis. Vous pouvez uniquement effectuer vos importations à partir d’une seule table, vue de base de données ou structure de données équivalente. Vous devez créer cette structure de données avant d’exécuter l’Assistant.

## <a name="connect-to-your-data"></a>Se connecter aux données
1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez le tableau de bord du service. Vous pouvez cliquer sur **Tous les services** dans la barre d’index pour afficher les « services de recherche » existants dans l’abonnement actuel. 
2. Cliquez sur **Importer des données** dans la barre de commandes pour ouvrir le panneau d’importation des données.  
3. Cliquez sur **Se connecter à vos données** pour spécifier une définition de source de données utilisée par un indexeur. Pour les sources de données au sein de l’abonnement, l’Assistant est généralement en mesure de détecter et lire les informations de connexion, ce qui limite les exigences de configuration globale.

|  |  |
| --- | --- |
| **Source de données existante** |Si des indexeurs sont déjà définis dans votre service de recherche, vous pouvez sélectionner une définition de source de données existante pour une autre importation. |
| **Base de données SQL Azure** |Le nom du service, les informations d’identification d’un utilisateur de base de données avec autorisation de lecture, ainsi que le nom de la base de données peuvent être spécifiés sur la page ou par le biais d’une chaîne de connexion ADO.NET. Choisissez l’option de chaîne de connexion permettant d’afficher ou de personnaliser les propriétés. <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être spécifiée sur la page. Cette option s’affiche une fois que la connexion aboutit : vous pouvez alors faire votre choix dans une liste déroulante. |
| **SQL Server dans les machines virtuelles Azure** |Spécifiez un nom de service complet, un ID d’utilisateur et un mot de passe, ainsi qu’une base de données pour la chaîne de connexion. Afin d’utiliser cette source de données, vous devez avoir préalablement installé un certificat dans le magasin local pour chiffrer la connexion. Pour obtenir des instructions, reportez-vous à [Connexion de machines virtuelles SQL à Recherche Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être spécifiée sur la page. Cette option s’affiche une fois que la connexion aboutit : vous pouvez alors faire votre choix dans une liste déroulante. |
| **Azure Cosmos DB** |La configuration requise inclut le compte, la base de données et la collection. Tous les documents de la collection seront inclus dans l’index. Vous pouvez définir une requête pour aplatir ou filtrer l’ensemble de lignes, ou pour détecter les documents modifiés dans l’optique de l’actualisation ultérieure des données. |
| **Stockage Blob Azure** |La configuration requise inclut le compte de stockage et un conteneur. Si les noms d’objets blob suivent une convention d’affectation de noms virtuelle à des fins de regroupement, vous pouvez indiquer la partie de répertoire virtuel du nom comme dossier sous le conteneur. Consultez la page [Indexation de Stockage Blob](search-howto-indexing-azure-blob-storage.md) pour plus d’informations. |
| **Stockage Table Azure** |La configuration requise inclut le compte de stockage et un nom de table. Vous pouvez également spécifier une requête pour extraire un sous-ensemble des tables. Consultez la page [Indexation de Stockage Table](search-howto-indexing-azure-tables.md) pour plus d’informations. |

## <a name="customize-target-index"></a>Personnaliser l’index cible
Un index préliminaire est habituellement déduit du jeu de données. Ajoutez, modifiez ou supprimer des champs pour finaliser le schéma. En outre, définissez des attributs au niveau des champs pour déterminer le comportement des recherches ultérieures.

1. Dans **Personnaliser l’index cible**, spécifiez le nom et une **clé** utilisée pour identifier de façon unique chaque document. La clé doit être une chaîne. Si les valeurs de champ comportent des espaces ou tirets, définissez les options avancées dans **Importer vos données** de manière à supprimer la vérification de validation pour ces caractères.
2. Passez en revue et modifiez les champs restants. Le nom et le type de champ sont généralement renseignés automatiquement. Vous pouvez modifier le type de données jusqu'à ce que l’index soit créé. Si vous souhaitez le modifier par la suite, vous devrez alors le régénérer.
3. Définissez les attributs d’index de chaque champ :
   
   * « Récupérable » renvoie le champ dans les résultats de la recherche.
   * « Filtrable » permet de référencer le champ dans les expressions de filtre.
   * « Triable » permet d’utiliser le champ dans un tri.
   * « À choix multiples » active le champ pour la navigation à choix multiples.
   * « Possibilité de recherche » permet une recherche en texte intégral.
4. Cliquez sur l’onglet **Analyseur** si vous souhaitez spécifier un analyseur de langue au niveau du champ. Seuls les analyseurs de langage peuvent être spécifiés pour l’instant. L’utilisation d’un analyseur personnalisé ou d’un analyseur non dédié au langage, comme Keyword, Pattern, etc., nécessite du code.
   
   * Cliquez sur **Possibilité de recherche** pour désigner la recherche en texte intégral sur le champ et pour activer la liste déroulante de l’analyseur.
   * Cliquez sur l’analyseur de votre choix. Pour plus d’informations, voir [Création d’une définition d’index de document dans plusieurs langues dans Azure Search](search-language-support.md).
5. Cliquez sur le **générateur de suggestions** pour activer les suggestions de requête type-ahead sur les champs sélectionnés.

## <a name="import-your-data"></a>Importer vos données
1. Dans **Importer vos données**, donnez un nom à l’indexeur. Rappelez-vous que le produit de l’Assistant Importer des données est un indexeur. Par la suite, si vous souhaitez l’afficher ou le modifier, vous devrez le sélectionner à partir du portail au lieu de réexécuter l’Assistant. 
2. Spécifiez le calendrier, qui est basé sur le fuseau horaire régional dans lequel le service est configuré.
3. Définissez des options avancées pour spécifier les seuils sur lesquels l’indexation peut continuer en cas de suppression d’un document. En outre, vous pouvez spécifier si les champs **Clé** peuvent contenir des espaces et des barres obliques.  
4. Cliquez sur **OK** pour créer l’index et importer les données.

Vous pouvez surveiller l’indexation dans le portail. À mesure que les documents sont chargés, le nombre de documents augmente pour l’index que vous avez défini. Il suffit parfois de quelques minutes pour que la page du portail sélectionne les mises à jour les plus récentes.

Dès que tous les documents sont chargés, l’index est prêt à être utilisé pour effectuer des requêtes.

## <a name="query-an-index-using-search-explorer"></a>Interrogation d’un index à l’aide de l’Explorateur de recherche

Le portail comprend **l’Explorateur de recherche** afin que vous puissiez interroger un index sans avoir à écrire du code. Vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) sur n’importe quel index.

L’expérience de recherche est basée sur les paramètres par défaut, tels que la [syntaxe simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) et [le paramètre de requête par défaut searchMode (https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Les résultats sont retournés dans JSON, au format détaillé, afin que vous puissiez inspecter le document dans son intégralité.

## <a name="edit-an-existing-indexer"></a>Modification d’un indexeur existant
Comme indiqué, l’Assistant Importer des données crée un **indexeur**, que vous pouvez modifier en tant que construction autonome dans le portail.

Dans le tableau de bord du service, double-cliquez sur la vignette Indexeur afin d’extraire la liste de tous les indexeurs créés pour votre abonnement. Double-cliquez sur l’un des indexeurs pour l’exécuter, le modifier ou le supprimer. Vous pouvez remplacer l’index existant par un autre, modifier la source de données et définir les options de seuils d’erreur lors de l’indexation.

## <a name="edit-an-existing-index"></a>Modification d’un index existant
L’Assistant a également créé un **index**. Dans Recherche Azure, les mises à jour structurelles d’un index nécessitent la reconstruction de cet index. Une reconstruction entraîne la suppression de l’index, sa recréation à l’aide d’un schéma modifié qui comporte les modifications souhaitées et le rechargement des données. Les mises à jour structurelles incluent la modification d’un type de données et le renommage ou la suppression d’un champ.

L’ajout d’un nouveau champ, la modification des profils de score, la modification des générateurs de suggestions ou la modification des analyseurs de langue ne nécessitent pas de reconstruction. Consultez la page [Mettre à jour l’index](https://msdn.microsoft.com/library/azure/dn800964.aspx) pour plus de détails.


## <a name="next-steps"></a>Étapes suivantes
Suivez ces liens pour en savoir plus sur les indexeurs :

* [Indexation d’Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexation d’Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexation de Stockage Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexation de Stockage Table](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

