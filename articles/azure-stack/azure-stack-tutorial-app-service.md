---
title: Mettre des applications web et API à la disposition des utilisateurs Azure Stack | Microsoft Docs
description: Didacticiel pour installer le fournisseur de ressources App Service et créer des offres qui donnent la possibilité aux utilisateurs Azure Stack de créer des applications web et API.
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: e2f15ca3a46af51ab6228e772298c51ad33fd49c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Mettre des applications web et API à la disposition des utilisateurs Azure Stack
En tant qu’administrateur cloud Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs (locataires) de créer des fonctions Azure Functions et des applications web et API. En permettant à vos utilisateurs d’accéder à ces applications cloud à la demande, vous pouvez leur faire gagner du temps et économiser des ressources. Pour effectuer cette configuration, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources App Service
> * Créer une offre
> * Tester l’offre

## <a name="deploy-the-app-service-resource-provider"></a>Déployer le fournisseur de ressources App Service

1. [Préparez l’hôte du Kit de développement Azure Stack](azure-stack-app-service-before-you-get-started.md). Cette opération inclut le déploiement du fournisseur de ressources SQL Server, qui est nécessaire pour la création de certaines applications.
2. [Téléchargez le programme d’installation et les scripts d’assistance](azure-stack-app-service-deploy.md).
3. [Exécutez le script d’assistance pour créer des certificats nécessaires](azure-stack-app-service-deploy.md).
4. [Installez le fournisseur de ressources App Service](azure-stack-app-service-deploy.md). (Plusieurs heures sont nécessaires pour effectuer l’installation et afficher tous les rôles de travail.)
5. [Validez l’installation](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Créer une offre

Vous pouvez créer une offre qui, par exemple, permet aux utilisateurs de créer des systèmes de gestion de contenu web DNN. Cette opération nécessite le service SQL Server que vous est déjà activé en installant le fournisseur de ressources SQL Server.

1.  [Définissez un quota](azure-stack-setting-quotas.md) et nommez-le *AppServiceQuota*. Sélectionnez **Microsoft.Web** pour le champ **Namespace**.
2.  [Créer un plan](azure-stack-create-plan.md). Nommez-le *TestAppServicePlan*, puis sélectionnez le service **Microsoft.SQL** et le quota **AppService Quota**.

    > [!NOTE]
    > Pour permettre aux utilisateurs de créer d’autres applications, il est possible que d’autres services soient exigés dans le plan. Par exemple, Azure Functions exige que le plan inclue le service **Microsoft.Storage**, tandis que Wordpress exige **Microsoft.MySQL**.
    > 
    >

3.  [Créez une offre](azure-stack-create-offer.md), nommez-la **TestAppServiceOffer**, puis sélectionnez le plan **TestAppServicePlan**.

## <a name="test-the-offer"></a>Tester l’offre

Maintenant que vous avez déployé le fournisseur de ressources App Service et créé une offre, vous pouvez vous connecter en tant qu’utilisateur, vous abonner à l’offre, puis créer une application. Pour cet exemple, nous allons créer un système de gestion de contenu de plateforme DNN. Vous devez d’abord créer une base de données SQL, puis l’application web DNN.

### <a name="subscribe-to-the-offer"></a>S’abonner à l’offre
1. Connectez-vous au portail Azure Stack (https://portal.local.azurestack.external) en tant que locataire.
2. Cliquez sur **Prendre un abonnement** > tapez **TestAppServiceSubscription** sous **’** > **Sélectionner une offre** > **TestAppServiceOffer** > **Créer**.

### <a name="create-a-sql-database"></a>Créer une base de données SQL

1. Cliquez sur **+** > **Données et stockage** > **Base de données SQL**.
2. Conservez les valeurs par défaut pour les champs, sauf pour ce qui suit :
    - **Nom de la base de données** : DNNdb
    - **Taille maximale (en Mo)** : 100
    - **Abonnement**: TestAppServiceOffer
    - **Groupe de ressources** : DNN-RG
3. Cliquez sur **Paramètres de connexion**, entrez les informations d’identification pour la base de données, puis cliquez sur **OK**. Vous allez utiliser ces informations d’identification plus tard dans cette procédure.
4. Cliquez sur **Référence** > sélectionnez le SKU SQL que vous avez créé pour le serveur d’hébergement SQL > **OK**.
5. Cliquez sur **Créer**.

### <a name="create-a-dnn-app"></a>Créer une application DNN    

1. Cliquez sur **+** > **Afficher tout** > **Aperçu de la plateforme DNN** > **Créer**.
2. Tapez *DNNapp* sous **Nom de l’application**, puis sélectionnez **TestAppServiceOffer** sous **Abonnement**.
3. Cliquez sur **Configurer les paramètres obligatoires** > **Créer** > tapez un nom de**Plan App Service**.
4. Cliquez sur **Niveau tarifaire** > **F1 Gratuit** > **Sélectionner** > **OK**.
5. Cliquez sur **Base de données**, puis entrez les informations relatives à la base de données SQL créée précédemment.
6. Cliquez sur **Créer**.

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources App Service
> * Créer une offre
> * Tester l’offre

Passez au didacticiel suivant pour savoir comment :

> [!div class="nextstepaction"]
> [Déployer des applications sur Azure et Azure Stack](user/azure-stack-solution-pipeline.md)
