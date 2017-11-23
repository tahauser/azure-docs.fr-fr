---
title: "Créer une instance d’Azure Database Migration Service à l’aide du portail Azure | Microsoft Docs"
description: "Utiliser le portail Azure pour créer une instance d’Azure Database Migration Service"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>Créer une instance de Database Migration Service à l’aide du portail Azure
Dans ce démarrage rapide, vous allez utiliser le portail Azure pour créer une instance d’Azure Database Migration Service.  Une fois le service créé, vous pouvez l’utiliser pour migrer des données entre une instance locale de SQL Server et une base de données SQL Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Ouvrez votre navigateur web et accédez au [portail Microsoft Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-azure-database-migration-service"></a>Créer une instance d’Azure Database Migration Service
1. Cliquez sur **+** pour créer un service.  Database Migration Service est encore en préversion.  

1. Dans la Place de marché, faites une recherche sur « migration », sélectionnez « Database Migration Service (préversion) », puis cliquez sur **Créer**.

    ![Créer un service de migration](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Choisissez un **nom de service** unique et facile à mémoriser pour votre instance d’Azure Database Migration Service Instance.
    - Sélectionnez **l’abonnement** Azure dans lequel vous voulez créer l’instance de Database Migration Service.
    - Créez un **réseau** et attribuez-lui un nom unique.
    - Choisissez **l’emplacement** le plus proche de votre serveur source ou cible.
    - Pour le **niveau tarifaire**, sélectionnez « De base : 1 vCore ».

1. Cliquez sur **Créer**.

Votre instance d’Azure Database Migration Service est créée et prête à l’emploi au bout de quelques instants.  Database Migration Service s’affiche alors comme indiqué dans l’image.

![Service de migration créé](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous pouvez nettoyer toutes les ressources que vous avez créées au cours de ce démarrage rapide en supprimant le [Groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).  Pour supprimer le groupe de ressources, accédez à l’instance de Database Migration Service que vous avez créée, cliquez sur le nom du **Groupe de ressources**, puis sélectionnez **Supprimer le groupe de ressources**.  Cette action supprime toutes les ressources du groupe, ainsi que le groupe lui-même.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migrer une instance locale de SQL Server vers SQL DB](tutorial-sql-server-to-azure-sql.md)