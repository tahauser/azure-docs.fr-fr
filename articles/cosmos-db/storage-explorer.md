---
title: "Gérer Azure Cosmos DB dans l’Explorateur Stockage Azure"
description: "Découvrez comment gérer Azure Cosmos DB dans l’Explorateur Stockage Azure."
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: 
author: jejiang
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: Jejiang
ms.openlocfilehash: baa7eee614159f9c6af493aff74b27773471f7d7
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Gérer Azure Cosmos DB dans l’Explorateur Stockage Azure (préversion)

L’utilisation d’Azure Cosmos DB dans l’Explorateur Stockage Azure permet aux utilisateurs de gérer des entités Azure Cosmos DB, de manipuler des données, de mettre à jour des procédures stockées et des déclencheurs, ainsi que d’autres entités Azure comme les files d’attente et les objets blob de stockage. À présent, vous pouvez utiliser le même outil pour gérer vos différentes entités Azure au même endroit. Pour le moment, l’Explorateur Stockage Azure prend en charge les comptes SQL <!--and MongoDB-->. L’Explorateur Stockage Azure ne fonctionne pas avec l’émulateur local Azure Cosmos DB. 

Dans cet article, vous apprenez à utiliser l’Explorateur Stockage pour gérer Azure Cosmos DB.


## <a name="prerequisites"></a>Prérequis

Un compte Azure Cosmos DB pour l’API SQL <!--or MongoDB API-->. Si vous n’avez pas de compte, vous pouvez en créer un dans le portail Azure, comme décrit dans [Azure Cosmos DB : Développer une application web API SQL avec .NET et le portail Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Installation

Installez les derniers composants de l’Explorateur Stockage Microsoft Azure ici : [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), avec maintenant la prise en charge de la version Windows, Linux et Mac.

## <a name="connect-to-an-azure-subscription"></a>Connexion à un abonnement Azure

1. Après avoir installé l’**Explorateur Stockage Azure**, cliquez sur l’icône de **plug-in** à gauche, comme illustré dans l’image suivante.
       
   ![Icône de plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Sélectionnez **Ajouter un compte Azure**, puis cliquez sur **Connexion**.

   ![Se connecter à un abonnement Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. Dans la boîte de dialogue **Connexion à Azure**, sélectionnez **Se connecter** et entrez vos informations d’identification Azure.

    ![Se connecter](./media/storage-explorer/sign-in.png)

3. Sélectionnez votre abonnement dans la liste, puis cliquez sur **Appliquer**.

    ![Appliquer](./media/storage-explorer/apply-subscription.png)

    Le volet Explorateur se met à jour et affiche les comptes de l’abonnement sélectionné.

    ![Liste de comptes](./media/storage-explorer/account-list.png)

    Vous êtes connecté à votre **compte Azure Cosmos DB** dans votre abonnement Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Se connecter à Azure Cosmos DB à l’aide d’une chaîne de connexion

Une autre façon de se connecter à Azure Cosmos DB est d’utiliser une chaîne de connexion. Utilisez les étapes suivantes pour vous connecter à l’aide d’une chaîne de connexion.

1. Rechercher **Locaux et joints** dans l’arborescence de gauche, cliquez avec le bouton droit sur **Comptes Azure Cosmos DB**, choisissez **Se connecter à Azure Cosmos DB...**

    ![Se connecter à Azure Cosmos DB avec une chaîne de connexion](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Choisissez le type de compte **Expérience par défaut** approprié, <!--either--> **DocumentDB** <!--or **MongoDB**-->, collez-le dans votre **Chaîne de connexion**, puis cliquez sur **OK** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion, consultez [Obtenir la chaîne de connexion](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Connection-string](./media/storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Gestion des ressources Azure Cosmos DB

Vous pouvez gérer un compte Azure Cosmos DB en effectuant les opérations suivantes :
* Ouvrir le compte dans le portail Azure
* Ajouter la ressource à la liste d’accès rapide
* Rechercher et actualiser les ressources
* Créer et supprimer les bases de données
* Créer et supprimer les collections
* Créer, modifier, supprimer et filtrer les documents
* Gérer les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur

### <a name="quick-access-tasks"></a>Tâches d’accès rapide

En cliquant avec le bouton droit sur un abonnement dans le volet Explorateur, vous pouvez effectuer de nombreuses tâches d’action rapide :

* Cliquez avec le bouton droit sur un compte ou une base de données Azure Cosmos DB, vous pouvez choisir **Ouvrir dans le portail** et gérer la ressource dans le navigateur sur le portail Azure.

     ![Ouvrir dans le portail](./media/storage-explorer/open-in-portal.png)

* Vous pouvez également ajouter un compte, une base de données ou une collection Azure Cosmos DB à l’**Accès rapide**.
* **Rechercher à partir d’ici** vous permet d’effectuer une recherche par mot clé dans le chemin sélectionné.

    ![rechercher à partir d’ici](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gestion des bases de données et des collections
#### <a name="create-a-database"></a>Créer une base de données 
Cliquez avec le bouton droit sur le compte Azure Cosmos DB, choisissez **Créer une base de données**, entrez le nom de la base de données, puis appuyez sur **Entrée** pour terminer.

![Création d’une base de données](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Supprimer une base de données
Cliquez avec le bouton droit sur la base de données, cliquez sur **Supprimer la base de données**, puis sur **Oui** dans la fenêtre contextuelle. Le nœud de base de données est supprimé et le compte Azure Cosmos DB s’actualise automatiquement.

![Supprimer database1](./media/storage-explorer/delete-database1.png)  

![Supprimer database2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Création d'une collection
Cliquez avec le bouton droit sur votre base de données, choisissez **Créer une collection**, puis fournissez les informations suivantes de type **ID de collection**, **Capacité de stockage**, etc. Cliquez sur **OK** pour terminer. Pour plus d’informations sur les paramètres de clé de partition, consultez [Conception axée sur le partitionnement](partition-data.md#designing-for-partitioning).

Si une clé de partition est utilisée pendant la création d’une collection, une fois la création terminée, la valeur de la clé de partition ne peut pas être changée sur la collection.

![Créer collection1](./media/storage-explorer/create-collection.png)

![Créer collection2](./media/storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Supprimer une collection
Cliquez avec le bouton droit sur la collection, cliquez sur **Supprimer la collection**, puis sur **Oui** dans la fenêtre contextuelle. 

Le nœud de collection est supprimé et la base de données s’actualise automatiquement.  

![Supprimer une collection](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gestion de documents

#### <a name="create-and-modify-documents"></a>Créer et modifier des documents
Pour créer un document, ouvrez **Documents** dans la fenêtre de gauche, cliquez sur **Nouveau document**, modifiez le contenu dans le volet droit, puis cliquez sur **Enregistrer**. Vous pouvez également mettre à jour un document existant, puis cliquer sur **Enregistrer**. Les changements peuvent être ignorés en cliquant sur **Ignorer**.

![Document](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Supprimer un document
Cliquez sur le bouton **Supprimer** pour supprimer le document sélectionné.
#### <a name="query-for-documents"></a>Rechercher des documents
Modifiez le filtre de document en entrant une [requête SQL](sql-api-sql-query.md), puis cliquez sur **Appliquer**.

![Filtrer](./media/storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gérer les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur
* Pour créer une procédure stockée, dans l’arborescence de gauche, cliquez sur **Procédure stockée**, choisissez **Créer une procédure stockée**, entrez un nom à gauche, tapez les scripts de procédure stockée dans la fenêtre de droite, puis cliquez sur **Créer**. 
* Si vous voulez modifier des procédures stockées existantes, double-cliquez dessus, effectuez la mise à jour, puis cliquez sur **Mettre à jour** pour enregistrer, ou sur **Ignorer** pour annuler la modification.

![Procédure stockée](./media/storage-explorer/stored-procedure.png)

* Les opérations pour les **déclencheurs** et les **fonctions définies par l’utilisateur** sont similaires à celles des **procédures stockées**.

## <a name="next-steps"></a>étapes suivantes

* Regardez la vidéo suivante pour savoir comment utiliser Azure Cosmos DB dans l’Explorateur Stockage Azure : [Utiliser Azure Cosmos DB dans l’Explorateur Stockage Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Pour en savoir plus sur l’Explorateur de stockage et se connecter à plusieurs services, consultez [Prise en main de l’Explorateur de stockage (préversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

