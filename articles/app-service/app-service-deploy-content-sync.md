---
title: "Synchronisation de contenu à partir d’un dossier cloud dans Azure App Service"
description: "Apprenez à déployer votre application dans Azure App Service via la synchronisation de contenu à partir d’un dossier cloud."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronisation de contenu à partir d’un dossier cloud dans Azure App Service
Ce didacticiel vous montre comment synchroniser votre contenu dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) à partir de services de stockage cloud populaires, tels que Dropbox et OneDrive. 

## <a name="overview"></a>Vue d’ensemble du déploiement de la synchronisation de contenu
Le déploiement à la demande de synchronisation de contenu est généré par le [moteur de déploiement Kudu](https://github.com/projectkudu/kudu/wiki) intégré à App Service. Dans le [portail Azure](https://portal.azure.com), vous pouvez désigner un dossier dans votre stockage cloud, travailler avec votre code d’application et votre contenu dans ce dossier et à synchroniser avec App Service sur un simple clic. La synchronisation de contenu utilise le processus Kudu pour la génération et le déploiement. 

## <a name="contentsync"></a>Activation du déploiement de la synchronisation de contenu
Pour activer la synchronisation de contenu à partir du [portail Azure](https://portal.azure.com), procédez comme suit :

1. Sur la page de votre application dans le portail Azure, dans le panneau de votre application, cliquez sur **Paramètres** > **Source du déploiement**. Cliquez sur **Choisir une source**, puis sélectionnez **OneDrive** ou **Dropbox** comme source pour le déploiement. 
   
    ![Synchronisation de contenu](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > En raison de différences sous-jacentes entre les API, **OneDrive Entreprise** n’est pas pris en charge pour l’instant. 
   > 
   > 
2. Complétez le flux de travail d’autorisation pour permettre à App Service d’accéder à un chemin spécifique désigné prédéfini pour OneDrive ou Dropbox, où votre contenu App Service est stocké. Après l’autorisation, la plateforme App Service vous donnera la possibilité de créer un dossier de contenu sous le chemin d’accès au contenu désigné ou de choisir un dossier de contenu existant sous ce chemin d’accès au contenu désigné. Les chemins d’accès de contenu désignés dans vos comptes de stockage cloud utilisés pour la synchronisation App Service sont les suivants :  
   
   * **OneDrive** : `Apps\Azure Web Apps` 
   * **Dropbox** : `Dropbox\Apps\Azure`
3. Après la synchronisation initiale du contenu, la synchronisation de contenu peut être lancée à la demande à partir du portail Azure. L’historique de déploiement est disponible sur la page **Déploiements**.
   
    ![Historique des déploiements](./media/app-service-deploy-content-sync/onedrive_sync.png)

Pour plus d’informations sur le déploiement de Dropbox, consultez le blog correspondant à l’adresse https://azure.microsoft.com/fr-fr/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/.

