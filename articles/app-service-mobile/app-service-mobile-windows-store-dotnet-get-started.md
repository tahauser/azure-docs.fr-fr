---
title: "Créer une plateforme Windows universelle sur Mobile Apps| Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser des services principaux d’applications mobiles Azure pour le développement d’applications UWP en C#, Visual Basic ou JavaScript."
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 508989e0e2daeac7922d61ff692ee4579139cfc8
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-windows-app"></a>Créer une application Windows
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel présente l’ajout d’un service principal cloud à une application de plateforme Windows universelle (UWP). Pour plus d’informations, consultez [Que sont les applications Mobile Apps ?](app-service-mobile-value-prop.md). Voici les captures d’écran générées à partir de l’application terminée :

![Application de bureau terminée](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
En cours d’exécution sur un ordinateur de bureau.

![Application de téléphone terminée](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
En cours d’exécution sur un téléphone

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile App pour les applications UWP.

## <a name="prerequisites"></a>Conditions préalables
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio Community 2015] ou version ultérieure.

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Télécharger et exécuter le projet client
Une fois que vous avez configuré le serveur principal de votre application Mobile App, vous pouvez créer une application cliente ou modifier une application existante pour se connecter à Azure. Dans cette section, vous téléchargez un projet de modèle d’application UWP, personnalisé pour se connecter à votre serveur principal Mobile App.

1. De retour dans le panneau **Démarrage rapide** pour votre serveur principal Mobile App, cliquez sur **Créer une application** > **Télécharger**, puis extrayez les fichiers de projet compressés sur votre ordinateur local.

    ![Télécharger le projet de démarrage rapide Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (Facultatif) Ajoutez le projet d’application UWP à la solution avec le projet de serveur. Cela rend plus facile le débogage et les tests de l'application et du backend dans la même solution Visual Studio, si vous choisissez de le faire. Pour ajouter un projet d’application UWP à la solution, vous devez utiliser Visual Studio 2015 ou une version ultérieure.
3. Avec l’application UWP comme projet de démarrage, appuyez sur la touche F5 pour déployer et exécuter l’application.
4. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel*, dans la zone de texte **Insérer un TodoItem**, puis cliquez sur **Enregistrer**.

    ![Démarrage rapide Windows - Bureau terminé](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Ceci envoie une demande POST vers le nouveau backend d'application mobile qui est hébergé dans Azure.
5. (Facultatif) Arrêtez l’application et redémarrez-la sur un autre périphérique ou un émulateur mobile.

    ![Démarrage rapide Windows - Téléphone terminé](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Notez que les données enregistrées à l’étape précédente sont chargées à partir d’Azure après le démarrage de l’application UWP.

## <a name="next-steps"></a>Étapes suivantes
* [Ajout de l’authentification à votre application](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Ajouter des notifications Push à votre application](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Apprenez à ajouter la prise en charge des notifications Push à votre application et à configurer le serveur principal d’applications mobiles pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile App. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
