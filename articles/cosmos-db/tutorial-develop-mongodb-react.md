---
title: Didacticiels MongoDB, React et Node pour Azure | Microsoft Docs
description: "Découvrez comment créer une application MongoDB avec React et Node sur Azure Cosmos DB à l’aide des mêmes API que celles utilisées pour MongoDB en suivant cette série de didacticiels vidéo."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 96cd397a7f04fe579b9c6331423abe107ad1c6d9
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Créer une application MongoDB avec React et Azure Cosmos DB  

Ce didacticiel vidéo en plusieurs parties montre comment créer une application de suivi de héros avec un serveur frontal React. L’application se sert de Node et Express pour le serveur, se connecte à Azure Cosmos DB avec l’[API MongoDB](mongodb-introduction.md) puis connecte le serveur frontal React à la partie serveur de l’application. Le didacticiel montre également comment effectuer une mise à l’échelle d’Azure Cosmos DB en point-and-click dans le portail Azure et comment déployer l’application sur Internet pour que tout le monde puisse suivre ses héros préférés. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) prend en charge les connexions au client MongoDB pour vous permettre d’utiliser Azure Cosmos DB à la place, tout en utilisant le même code utilisé pour les applications MongoDB, avec des avantages supplémentaires, comme une simplification des déploiements cloud, des mises à l’échelle, une sécurité accrue, une réplication mondiale des données, des supports multi-modèles et des fonctions d’écriture et de lecture extrêmement rapides.  

Ce didacticiel en plusieurs parties décrit les tâches suivantes :

> [!div class="checklist"]
> * Introduction
> * Configurer le projet
> * Générer l’interface utilisateur avec React
> * Créer un compte Azure Cosmos DB à l’aide du portail Azure
> * Utiliser Mongoose pour se connecter à Azure Cosmos DB
> * Ajouter les opérations Réagir, Créer, Mettre à jour et Supprimer à l’application

Vous souhaitez générer cette même application avec Angular ? Consultez la [série de didacticiels vidéo pour Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Composants requis
* [Node.JS](https://www.nodejs.org)

### <a name="finished-project"></a>Projet terminé
Obtenez l’application finale [sur GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introduction 

Dans cette vidéo, Burke Holland présente Azure Cosmos DB et vous fait découvrir l’application créée dans cette série de vidéos. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Installation du projet

Cette vidéo montre comment configurer Express et React dans le même projet. Burke détaille ensuite le code dans le projet.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Créer l’interface utilisateur

Cette vidéo montre comment créer l’interface utilisateur (UI) de l’application avec React. 

> [!NOTE]
> Le code CSS présenté dans cette vidéo se trouve dans le [référentiel GitHub react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Se connecter à Azure Cosmos DB

Cette vidéo montre comment créer un compte Azure Cosmos DB dans le portail Azure, comment installer les packages MongoDB et Mongoose et comment connecter l’application et le compte créé à l’aide de chaîne de connexion Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Afficher et créer des héros dans l’application

Cette vidéo montre comment afficher et créer des héros dans la base de données Cosmos DB et comment tester ces méthodes à l’aide de Postman et de l’interface utilisateur de React. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Supprimer et mettre à jour des héros dans l’application

Cette vidéo montre comment supprimer et mettre à jour des héros à partir de l’application et comment afficher les mises à jour dans l’interface utilisateur. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Compléter l’application

Cette vidéo montre comment compléter l’application et terminer le raccordement de l’interface utilisateur à l’API principale. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, utilisez les étapes suivantes pour supprimer toutes les ressources créées par ce didacticiel dans le portail Azure. 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’une application avec React, Node, Express et Azure Cosmos DB 
> * Création d’un compte Azure Cosmos DB
> * Connexion de l’application au compte Azure Cosmos DB
> * Test de l’application à l’aide de Postman
> * Exécution de l’application et ajout de héros à la base de données

Revenez prochainement pour consulter une vidéo de la série de didacticiels qui abordera le déploiement de l’application et la réplication à l’échelle mondiale de vos données.

Vous pouvez passer au didacticiel suivant et découvrir comment importer des données MongoDB dans Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md)
 
