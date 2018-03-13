---
title: "Informations d’identification du déploiement d’Azure App Service | Microsoft Docs"
description: "Apprenez à utiliser les informations d’identification du déploiement d’Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurer les informations d’identification de déploiement pour Azure App Service
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) prend en charge deux types d’informations d’identification pour le [déploiement Git local](app-service-deploy-local-git.md) et le [déploiement FTP/S](app-service-deploy-ftp.md). Ils ne sont pas les mêmes que vos informations d’identification Azure Active Directory.

* **Informations d’identification au niveau de l’utilisateur** : un seul ensemble d’informations d’identification pour l’intégralité du compte Azure. Il peut être utilisé pour déployer sur App Service pour n’importe quelle application et dans n’importe quel abonnement auxquels le compte Azure est autorisé à accéder. Il s’agit de l’ensemble d’informations d’identification par défaut que vous configurez dans **App Services** > **&lt;nom_application>** > **Informations d’identification de déploiement**. C’est également l’ensemble par défaut qui est présenté dans l’interface utilisateur graphique du portail, comme la **vue d’ensemble** et les **propriétés** de la [page Ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) de votre application.

    > [!NOTE]
    > Lorsque vous déléguez l’accès aux ressources Azure au moyen du contrôle d’accès en fonction du rôle (RBAC) ou des autorisations de coadministrateur, chaque utilisateur Azure recevant l’accès à une application peut utiliser ses informations d’identification personnelles jusqu’à la révocation de l’accès. Ces informations d’identification de déploiement ne doivent pas être partagées avec d’autres utilisateurs Azure.
    >
    >

* **Informations d’identification au niveau de l’application** : un seul ensemble d’informations d’identification pour chaque application. Celui-ci peut être utilisé pour déployer sur cette application uniquement. Les informations d’identification de chaque application sont générées automatiquement lors de la création de l’application et se trouvent dans le profil de publication de l’application. Vous ne pouvez pas configurer manuellement les informations d’identification, mais vous pouvez les réinitialiser à tout moment pour une application.

    > [!NOTE]
    > Pour autoriser quelqu’un à accéder à ces informations d’identification via le contrôle d’accès en fonction du rôle (RBAC), vous devez lui accorder au moins le rôle Collaborateur sur l’application Web. Les lecteurs ne sont pas autorisés à publier et n’ont donc pas accès à ces informations d’identification.
    >
    >

## <a name="userscope"></a>Définir et réinitialiser les informations d’identification au niveau de l’utilisateur

Vous pouvez configurer vos informations d’identification au niveau de l’utilisateur dans la [page Ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) d’une application. Quelle que soit l’application dans laquelle vous configurez ces informations d’identification, ces dernières s’appliquent à toutes les applications et à tous les abonnements de votre compte Azure. 

Pour configurer les informations d’identification au niveau de l’utilisateur :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur App Service > **&lt;une application>** > **Informations d’identification de déploiement**.

    > [!NOTE]
    > Dans le portail, vous devez disposer d’au moins une application avant de pouvoir accéder à la page Informations d’identification de déploiement. Toutefois, avec l’[interface de ligne de commande Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set), vous pouvez configurer les informations d’identification au niveau de l’utilisateur sans application existante.

2. Configurez le nom d’utilisateur et le mot de passe, puis cliquez sur **Enregistrer**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Une fois que vous avez défini vos informations d’identification de déploiement, vous pouvez trouver le nom d’utilisateur du déploiement *Git* dans la **vue d’ensemble** de votre application

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

et le nom d’utilisateur de déploiement *FTP* dans les **propriétés** de votre application.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure n’affiche pas votre mot de passe du déploiement au niveau de l’utilisateur. Si vous oubliez le mot de passe, vous ne pouvez pas le récupérer. Toutefois, vous pouvez réinitialiser vos informations d’identification en suivant les étapes décrites dans cette section.
>
>  

## <a name="appscope"></a>Obtenir et réinitialiser les informations d’identification au niveau de l’application
Pour chaque application dans App Service, les informations d’identification au niveau de l’application sont stockées dans le profil de publication XML.

Pour obtenir les informations d’identification au niveau de l’application :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur App Service > **&lt;une application>** > **Vue d’ensemble**.

2. Cliquez sur **...Plus** > **Obtenir le profil de publication**. Le téléchargement du fichier .PublishSettings démarre.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Ouvrez le fichier .PublishSettings, puis recherchez la balise `<publishProfile>` avec l’attribut `publishMethod="FTP"`. Obtenez ensuite ses attributs `userName` et `password`.
Il s’agit des informations d’identification au niveau de l’application.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Comme pour les informations d’identification au niveau de l’utilisateur, le nom d’utilisateur du déploiement FTP est au format `<app_name>\<username>` et le nom d’utilisateur du déploiement Git est `<username>` sans `<app_name>\`.

Pour réinitialiser les informations d’identification au niveau de l’application :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur App Service > **&lt;une application>** > **Vue d’ensemble**.

2. Cliquez sur **...Plus** > **Réinitialiser le profil de publication**. Cliquez sur **Oui** pour confirmer la réinitialisation.

    L’action de réinitialisation invalide les fichiers .PublishSettings précédemment téléchargés.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces informations d’identification pour déployer votre application à partir de [Git local](app-service-deploy-local-git.md) ou à l’aide de [FTP/S](app-service-deploy-ftp.md).
