---
title: Mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack | Microsoft Docs
description: Didacticiel pour installer le fournisseur de ressources SQL Server et de créer des offres permettant aux utilisateurs d’Azure Stack de créer des bases de données SQL.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2017
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: f8d2dd65d9d427872fe78508ed0bcc61e644fdb0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack
En tant qu’administrateur de cloud Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs de créer des bases de données SQL qu’ils peuvent utiliser avec leurs applications cloud natives, sites web et charges de travail. En fournissant à vos utilisateurs ces bases de données cloud à la demande et personnalisées, vous pouvez leur faire gagner du temps et économiser des ressources. Pour effectuer cette configuration, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources SQL Server
> * Créer une offre
> * Tester l’offre

## <a name="deploy-the-sql-server-resource-provider"></a>Déployer le fournisseur de ressources SQL Server

Le processus de déploiement est décrit en détail dans l’[article Utiliser des bases de données SQL sur Azure Stack](azure-stack-sql-resource-provider-deploy.md), et se compose des étapes principales suivantes :

1. [Déployez un fournisseur de ressources SQL]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [Vérifiez le déploiement]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Fournissez de la capacité par le biais d’une connexion à un serveur SQL d’hébergement.

## <a name="create-an-offer"></a>Créer une offre

1.  [Définissez un quota](azure-stack-setting-quotas.md) et nommez-le *SQLServerQuota*. Sélectionnez **Microsoft.SQLAdapter** pour le champ **Namespace**.
2.  [Créer un plan](azure-stack-create-plan.md). Nommez-le *TestSQLServerPlan*, puis sélectionnez le service **Microsoft.SQLAdapter** et le quota **SQLServerQuota**.

    > [!NOTE]
    > Pour permettre aux utilisateurs de créer d’autres applications, il est possible que d’autres services soient exigés dans le plan. Par exemple, Azure Functions exige que le plan inclue le service **Microsoft.Storage**, tandis que Wordpress exige **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Créez une offre](azure-stack-create-offer.md), nommez-la **TestSQLServerOffer**, puis sélectionnez le plan **TestSQLServerPlan**.

## <a name="test-the-offer"></a>Tester l’offre

Maintenant que vous avez déployé le fournisseur de ressources SQL Server et créé une offre, vous pouvez vous connecter en tant qu’utilisateur, vous abonner à l’offre, puis créer une base de données.

### <a name="subscribe-to-the-offer"></a>S’abonner à l’offre
1. Connectez-vous au portail Azure Stack (https://portal.local.azurestack.external) en tant que locataire.
2. Cliquez sur **Prendre un abonnement**, puis tapez **TestSQLServerSubscription** sous **Nom d’affichage**.
3. Cliquez sur **Sélectionner une offre** > **TestSQLServerOffer** > **Créer**.
4. Cliquez sur **Plus de services** > **Abonnements** > **TestSQLServerSubscription** > **Fournisseurs de ressources**.
5. Cliquez sur **Inscrire** en regard du fournisseur **Microsoft.SQLAdapter**.

### <a name="create-a-sql-database"></a>Créer une base de données SQL

1. Cliquez sur **+** > **Données et stockage** > **Base de données SQL**.
2. Conservez les valeurs par défaut pour les champs ou utilisez les exemples suivants :
    - **Nom de la base de données** : SQLdb
    - **Taille maximale (en Mo)** : 100
    - **Abonnement** : TestSQLOffer
    - **Groupe de ressources** : SQL-RG
3. Cliquez sur **Paramètres de connexion**, entrez les informations d’identification pour la base de données, puis cliquez sur **OK**.
4. Cliquez sur **Référence** > sélectionnez le SKU SQL que vous avez créé pour le serveur d’hébergement SQL > **OK**.
5. Cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources SQL Server
> * Créer une offre
> * Tester l’offre

Passez au didacticiel suivant pour savoir comment :

> [!div class="nextstepaction"]
> [Rendre les applications web, mobiles et API disponibles pour vos utilisateurs]( azure-stack-tutorial-app-service.md)

