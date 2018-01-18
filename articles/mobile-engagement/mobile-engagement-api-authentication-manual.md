---
title: "Azure Mobile Engagement - Configuration manuelle de l’utilisation des API pour l’authentification"
description: "Décrit comment configurer manuellement l’authentification pour les API REST Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Azure Mobile Engagement - Configuration manuelle de l’utilisation des API pour l’authentification
Cette documentation est une annexe de l’article [Authentifier avec l’API REST de Mobile Engagement](mobile-engagement-api-authentication.md). Veillez à le lire en premier pour comprendre le contexte.
Elle décrit une autre méthode d’installation unique afin de configurer l’authentification pour les API REST Mobile Engagement à l’aide du portail Azure.

> [!NOTE]
> Les instructions suivantes sont fondées sur ce [guide Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) et personnalisées en fonction des besoins propres à l’authentification pour les API Mobile Engagement. Par conséquent, consultez ce guide pour bien comprendre les étapes décrites en détail ci-dessous.

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Active Directory** dans le volet gauche.

     ![sélectionner Active Directory][1]

3. Pour afficher les applications dans votre annuaire, cliquez sur **Inscriptions des applications**.

     ![afficher les applications][3]

4. Cliquez sur **Nouvelle inscription d’application**.

     ![Ajouter une application][4]

5. Renseignez le nom de l’application en conservant le type d’application **Application/API web**, puis cliquez sur le bouton Suivant. Vous pouvez fournir des URL factices comme **URL de connexion** car elles ne sont pas utilisées pour ce scénario, et les URL elles-mêmes ne sont pas validées.
6. Lorsque vous avez terminé, vous disposez d’une application Azure AD avec le nom que vous avez spécifié. Il s’agit de votre **AD\_APP\_NAME**. Prenez-en note.

     ![nom de l’application][8]

7. Cliquez sur le nom de l’application.
8. Recherchez **l’ID de l’application**, notez cette information car il s’agit de l’ID client qui sera utilisé comme **CLIENT\_ID** pour les appels d’API.

     ![configurer l’application][10]

9. Recherchez la section **Clés** sur la droite.

     ![configurer l’application][11]

10. Créez une nouvelle clé puis copiez et enregistrez-la immédiatement afin de l’utiliser. Elle ne s’affichera plus.

     ![configurer l’application][12]

    > [!IMPORTANT]
    > Cette clé expire à la fin de la durée que vous avez spécifiée. Vous devrez donc veiller à la renouveler le moment venu, sans quoi l’authentification de votre API ne fonctionnera plus. Vous pouvez également supprimer et recréer cette clé si vous pensez qu’elle a été compromise.
    >
    >
11. Cliquez sur le bouton **Points de terminaison** en haut de la page puis copiez **OAUTH 2.0 TOKEN ENDPOINT**.

    ![][14]

16. Ce point de terminaison se présente comme indiqué ci-dessous, le GUID de l’URL étant votre **TENANT_ID**. Prenez-en note : `https://login.microsoftonline.com/<GUID>/oauth2/token`
17. Nous allons maintenant configurer les autorisations pour cette application. Pour cela, vous devez ouvrir le [portail Azure](https://portal.azure.com). 
18. Cliquez sur **Groupes de ressources** et recherchez le groupe de ressources **Mobile Engagement**.

    ![][15]

19. Cliquez sur le groupe de ressources **Mobile Engagement** et accédez à la section **Paramètres** ici.

    ![][16]

20. Cliquez sur **Utilisateurs** dans la section Paramètres, puis cliquez sur **Ajouter** pour ajouter un utilisateur.

    ![][17]

21. Cliquez sur **Sélectionner un rôle**.

    ![][18]

22. Cliquez sur **Propriétaire**.

    ![][19]

23. Recherchez le nom de votre application **AD\_APP\_NAME** dans la zone de recherche. Il n’est pas affiché par défaut ici. Une fois que vous l’avez trouvé, sélectionnez-le, puis cliquez sur **Sélectionner** en bas de la section.

    ![][20]

24. Dans la section **Ajouter un accès**, il s’affiche sous la forme **1 utilisateur, 0 groupe**. Cliquez sur **OK** dans cette section pour confirmer la modification.

    ![][21]

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



