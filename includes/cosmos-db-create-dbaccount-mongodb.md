---
title: Créer un compte d’API MongoDB Azure Cosmos DB
description: Décrit comment créer un compte d’API MongoDB Azure Cosmos DB dans le portail Azure
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Créer une ressource**, sur **Bases de données**, puis sous **Azure Cosmos DB**, cliquez sur **Créer**.
   
   ![Capture d’écran du portail Azure, mettant en surbrillance l’option Plus de services, et Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez **MongoDB** comme API et effectuez la configuration souhaitée pour le compte Azure Cosmos DB.
 
    ![Capture d’écran du nouveau Panneau Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **ID** doit être un nom unique que vous voulez utiliser pour identifier votre compte Azure Cosmos DB. Il ne peut contenir que des lettres minuscules, des nombres, le caractère « - », et doit présenter entre 3 et 50 caractères.
    * **Abonnement** correspond à votre abonnement Azure. Il est rempli automatiquement.
    * **Groupe de ressources** correspond au nom du groupe de ressources pour votre compte Azure Cosmos DB.
    * **Emplacement** correspond à l’emplacement géographique de votre instance Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs.

4. Cliquez sur **Créer** pour créer le compte.
5. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

    ![Notification du début de déploiement](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Lorsque le déploiement est terminé, ouvrez le nouveau compte à partir de la mosaïque Toutes les ressources. 

    ![Compte Azure Cosmos DB sur la vignette Toutes les ressources](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
