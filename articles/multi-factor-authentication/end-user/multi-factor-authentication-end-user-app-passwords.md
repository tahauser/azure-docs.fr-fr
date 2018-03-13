---
title: "Comment utiliser les mots de passe d’application dans Azure MFA ? | Microsoft Docs"
description: "Cette page permet aux utilisateurs de comprendre ce que sont les mots de passe d’application et leur utilisation avec Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 55ca5ada0db30440e4599c77b7a6834ef671c7a4
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Que sont les mots de passe d’application dans Azure Multi-Factor Authentication ?
Actuellement, certaines applications sans navigateur telles que le client de messagerie native Apple qui utilise Exchange Active Sync ne prennent pas en charge l’authentification multifacteur. L’authentification multifacteur est activée par l’utilisateur. Cela signifie que si un utilisateur a été activé pour l’authentification multifacteur et qu’il tente d’utiliser les applications sans navigateur, il ne peut le faire. Un mot de passe d’application permet de remédier à ce problème. Si vous appliquez l’authentification multifacteur via des stratégies d’accès conditionnel et non via l’authentification multifacteur par utilisateur, vous ne pouvez pas créer des mots de passe d’application. Les applications qui utilisent des stratégies d’accès conditionnel pour contrôler l’accès n’ont pas besoin de mots de passe d’application.

Une fois que vous disposez d’un mot de passe d’application, vous l’utilisez à la place de votre mot de passe d’origine avec ces applications sans navigateur. Cela s’explique par le fait que lorsque vous vous inscrivez pour la vérification en deux étapes, vous demandez à Microsoft de ne laisser personne se connecter avec votre mot de passe si cette personne ne peut pas également effectuer la deuxième vérification. Le client de messagerie natif d’Apple sur votre téléphone ne peut pas se connecter sous votre identité, car il n’existe pas de vérification en deux étapes. Pour résoudre ce problème, vous devez créer un mot de passe d’application plus sécurisé que vous n’utilisez pas quotidiennement, mais uniquement pour les applications qui ne prennent pas en charge la vérification en deux étapes. Le mot de passe d’application permet à celles-ci de contourner l’authentification multifacteur et de continuer à fonctionner.

> [!NOTE]
> Les clients Office 2013 (y compris Outlook) prennent en charge de nouveaux protocoles d’authentification et peuvent être utilisés dans le cadre de la vérification en deux étapes.  Ainsi, une fois activés, les mots de passe d’application ne sont pas obligatoires avec les clients Office 2013.  Pour plus d’informations, consultez [Version préliminaire publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Utilisation de mots de passe d’application
Voici quelques points à retenir sur l’utilisation de mots de passe d’application.

* Vous ne pouvez pas créer vos propres mots de passe d’application. En effet, ils sont générés automatiquement. Étant donné que vous ne devez entrer le mot de passe qu’une seule fois par application, il est préférable d’utiliser un mot de passe plus complexe, généré automatiquement, plutôt que d’en créer un que vous pouvez mémoriser.
* À l’heure actuelle, un utilisateur peut posséder jusqu’à 40 mots de passe. Si vous essayez d’en créer un une fois que vous avez atteint la limite, vous devrez supprimer l’un de vos mots de passe d’application existants avant de pouvoir en créer un autre.
* Il est recommandé d’utiliser un mot de passe d’application par appareil, et non par application. Par exemple, vous pouvez créer un mot de passe d’application pour votre ordinateur portable et utiliser ce mot de passe pour toutes vos applications sur cet ordinateur. Ensuite, créez un second mot de passe à utiliser pour toutes les applications sur votre bureau.
* Vous obtenez un mot de passe d’application la première fois que vous vous inscrivez pour la vérification en deux étapes.  Si vous avez besoin d’autres mots de passe, vous pouvez les créer.



## <a name="creating-and-deleting-app-passwords"></a>Création et suppression des mots de passe d’application
À la première connexion, vous obtenez un mot de passe d’application que vous pouvez utiliser.  En outre, vous pouvez également créer et supprimer des mots de passe d’application par la suite.  La procédure à suivre dépend de l’utilisation de l’authentification multifacteur. Répondez aux questions suivantes pour déterminer où vous devez aller pour gérer les mots de passe d’application :

1. Utilisez-vous la vérification en deux étapes pour votre compte Microsoft personnel ? Si oui, reportez-vous à l’article [Mots de passe d’application et vérification en deux étapes](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) pour obtenir de l’aide. Si non, passez à la question 2.

2. Vous utilisez la vérification en deux étapes pour votre compte professionnel ou scolaire. Est-ce que vous l’utilisez pour vous connecter à des applications Office 365 ? Si oui, reportez-vous à l’article [Créer un mot de passe d’application pour Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) pour obtenir de l’aide. Si non, passez à la question 3.

3. Utilisez-vous la vérification en deux étapes avec Microsoft Azure ? Si oui, reportez-vous à la section [Gérer les mots de passe d’application dans le portail Azure](#manage-app-passwords-in-the-Azure-portal) de cet article. Si non, passez à la question 4.

4. Vous ne savez pas exactement dans quels cas vous utilisez la vérification en deux étapes ? Consultez la section [Gérer les mots de passe d’application à l’aide du portail Myapps](#manage-app-passwords-with-the-myapps-portal) de cet article.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Gérer les mots de passe d’application dans le portail Azure
Si vous utilisez la vérification en deux étapes avec Azure, vous devrez créer des mots de passe d’application par le biais du portail Azure.



## <a name="manage-app-passwords-with-the-myapps-portal"></a>Gérer les mots de passe d’application à l’aide du portail Myapps
Si vous n’êtes pas sûr des cas dans lesquels vous utilisez l’authentification multifacteur, vous pouvez toujours créer et supprimer des mots de passe d’application via le portail Myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Pour créer un mot de passe d’application à l’aide du portail MyApps
1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Cliquez sur votre nom en haut à droite, puis choisissez **Profil**.
3. Sélectionnez **Vérification de sécurité supplémentaire**.
   ![Capture d’écran : Vérification de sécurité supplémentaire](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Sélectionnez **Mots de passe d’application**.
   ![Capture d’écran : sélection de Mots de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Cliquez sur **Créer**.
6. Saisissez un nom pour le mot de passe d’application et cliquez sur **Suivant**.
7. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.
   ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Pour supprimer un mot de passe d’application à l’aide du portail MyApps
1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. En haut de la page, sélectionnez le profil.
3. Sélectionnez **Vérification de sécurité supplémentaire**.

   ![Capture d’écran : Vérification de sécurité supplémentaire](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Sélectionnez **Mots de passe d’application**.

   ![Capture d’écran : sélection de Mots de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. En regard du mot de passe d’application à supprimer, cliquez sur **Supprimer**.

   ![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Confirmez que vous voulez supprimer ce mot de passe en cliquant sur **Oui**.
7. Une fois le mot de passe d’application supprimé, vous pouvez cliquer sur **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md)

- Essayez l’[application Microsoft Authenticator](microsoft-authenticator-app-how-to.md) pour vérifier vos connexions grâce aux notifications d’application, plutôt que de recevoir des SMS ou des appels.
