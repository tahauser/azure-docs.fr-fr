---
title: 'Authentification avec les API REST Azure Mobile Engagement : configuration manuelle'
description: Décrit comment configurer manuellement l’authentification pour les API REST Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Azure Mobile Engagement - Configuration manuelle de l’utilisation des API pour l’authentification
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Ce document est une annexe de l’article [Authentification avec les API REST Azure Mobile Engagement](mobile-engagement-api-authentication.md). Assurez-vous de lire d’abord cet article pour comprendre le contexte. Il décrit également une autre méthode de configuration de l’authentification unique pour les API REST Azure Mobile Engagement à l’aide du portail Azure.

> [!NOTE]
> Les instructions suivantes sont basées sur [ce guide Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Elles sont personnalisées en fonction des conditions d’authentification pour les API Azure Mobile Engagement. Consultez-les pour bien comprendre les étapes décrites en détail ci-dessous.

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Active Directory** dans le volet gauche.

   ![Sélectionner Active Directory][1]

3. Pour afficher les applications dans votre répertoire, sélectionnez **Inscriptions des applications**.

   ![Afficher les applications][3]

4. Sélectionnez **Nouvelle inscription d’application**.

   ![Ajouter l’application][4]

5. Entrez le nom de l’application. Laissez le type d’application sur **Application web/API**, puis sélectionnez le bouton **Suivant**. Vous pouvez fournir des URL factices pour **l’URL DE CONNEXION**. Ces URL ne sont pas utilisées pour ce scénario et les URL elles-mêmes ne sont pas validées.

   Une fois que vous avez terminé, vous disposez d’une application Azure Active Directory (Azure AD) portant le nom fourni. Il s’agit de votre **AD\_APP\_NAME**. Prenez-en note.

   ![Nom de l’application][8]

7. Sélectionnez le nom de l’application.

8. Recherchez l’**ID de l’application** et notez-le. Il s’agit de l’ID CLIENT qui sera utilisé comme **CLIENT\_ID** pour les appels d’API.

   ![Rechercher l’ID de l’application][10]

9. Recherchez la section **Clés** sur la droite.

   ![Section Clés][11]

10. Créez une nouvelle clé et copiez-la immédiatement. Elle ne s’affichera plus.

    ![Volet Clés avec les détails de la clé][12]

    > [!IMPORTANT]
    > Cette clé expire à la fin de la durée que vous avez spécifiée. Veillez à la renouveler le moment venu, sinon l’authentification de votre API ne fonctionnera plus. Si vous pensez que cette clé a été compromise, vous pouvez la supprimer et la recréer.
    >
    
11. Sélectionnez le bouton **Points de terminaison** en haut de la page. Copiez ensuite le **POINT DE TERMINAISON DE JETON OAUTH 2.0**.

    ![Copier le point de terminaison][14]

16. Ce point de terminaison se présente comme indiqué ci-dessous, le GUID de l’URL étant votre **TENANT_ID** : `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Vous devez ensuite configurer les autorisations pour cette application. Pour démarrer le processus, accédez au [portail Azure](https://portal.azure.com).

18. Sélectionnez **Groupes de ressources**, puis recherchez le groupe de ressources **MobileEngagement**.

    ![Rechercher MobileEngagement][15]

19. Sélectionnez le groupe de ressources **MobileEngagement**, puis **Tous les paramètres**.

    ![Accéder aux paramètres de MobileEngagement][16]

20. Sélectionnez **Utilisateurs** dans la section **Paramètres**. Ensuite, pour ajouter un utilisateur, sélectionnez **Ajouter**.

    ![Ajouter un utilisateur][17]

21. Cliquez sur **Sélectionner un rôle**.

    ![Sélectionnez un rôle][18]

22. Sélectionnez **Propriétaire**.

    ![Sélectionner le rôle Propriétaire][19]

23. Recherchez le nom de votre application, **AD\_APP\_NAME**, dans la zone de recherche. Par défaut, ce nom n’est pas ici. Une fois que vous l’avez trouvé, sélectionnez-le. Cliquez ensuite sur **Sélectionner** au bas de la section.

    ![Sélectionner le nom][20]

24. Dans la section **Ajouter un accès**, il s’affiche sous la forme **1 utilisateur, 0 groupe**. Pour confirmer la modification, sélectionnez **OK**.

    ![Confirmer l’ajout de l’utilisateur][21]

Vous avez maintenant terminé la configuration Azure AD requise et êtes prêt à appeler les API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



