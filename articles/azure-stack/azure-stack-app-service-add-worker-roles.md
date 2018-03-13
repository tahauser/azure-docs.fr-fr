---
title: "Augmenter la taille des instances des rôles de travail dans App Services - Azure Stack  | Microsoft Docs"
description: "Instructions détaillées pour la mise à l’échelle d’App Services Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: ddd9820715e964218db8b88fb5211b3725c808b9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service sur Azure Stack : ajouter des rôles d’infrastructure ou de travail
*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*  

Ce document fournit des instructions sur la mise à l’échelle d’App Service sur des rôles d’infrastructure et de travail Azure Stack. Il contient les étapes de création de rôles de travail supplémentaires pour prendre en charge des applications de n’importe quelle taille.

> [!NOTE]
> Si votre environnement Azure Stack n’a pas plus de 96 Go de RAM, l’ajout de capacité supplémentaire peut se révéler difficile.

App Service sur Azure Stack, par défaut, prend en charge les niveaux Worker gratuits et partagés. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

Si vous n’êtes pas sûr de ce qui a été déployé avec l’installation App Service sur Azure Stack par défaut, vous pouvez consulter des informations supplémentaires dans la [vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).

Azure App Service sur Azure Stack déploie tous les rôles à l’aide de groupes de machines virtuelles identiques et en tant que tel tire parti des fonctionnalités de mise à l’échelle de cette charge de travail. Par conséquent, toute la mise à l’échelle des niveaux Worker s’effectue via l’administrateur App Service.

Ajouter des Workers supplémentaires directement à partir de l’administrateur du fournisseur de ressources App Service.

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur du service.

2. Parcourir **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Cliquez sur **Rôles**. La répartition de tous les rôles App Service déployés s’affiche ici.

4. Cliquez avec le bouton droit sur la ligne du type que vous voulez mettre à l’échelle, puis cliquez sur **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Cliquez sur **Mise à l’échelle**, sélectionnez le nombre d’instances vers lequel vous voulez faire la mise à l’échelle, puis cliquez sur **Enregistrer**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service sur Azure Stack ajoute ensuite les machines virtuelles supplémentaires, les configure, installe tous les logiciels requis et les marque comme Prêt lorsque ce processus est terminé. Ce processus peut prendre environ 80 minutes.

7. Vous pouvez surveiller la progression de la préparation des nouveaux rôles en affichant les Workers dans le panneau **Rôles**.

Une fois qu’ils sont entièrement déployés et prêts, les utilisateurs peuvent déployer leur charge de travail sur les Workers. Voici un exemple des différents niveaux tarifaires disponibles par défaut. S’il n’existe aucun Worker disponible pour un niveau Worker spécifique, l’option permettant de choisir le niveau tarifaire correspondant est indisponible.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pour monter en charge la gestion, les rôles de serveur frontal ou serveur de publication vous devez monter en charge le groupe de machines virtuelles identiques correspondant. Nous allons ajouter la possibilité de monter en charge ces rôles via la gestion App Service dans une future version.

Pour la montée en charge des rôles Gestion, Frontal ou Serveur de publication, suivez les mêmes étapes en sélectionnant le type de rôle approprié. Les contrôleurs ne sont pas déployés comme groupes de machines virtuelles identiques : par conséquent, vous devez déployer deux contrôleurs au moment de l’installation pour tous les déploiements de production.

### <a name="next-steps"></a>Étapes suivantes

[Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)
