---
title: "Problèmes de connexion à une application à l’aide d’un lien profond | Microsoft Docs"
description: "Comment résoudre les problèmes d’accès à une application à partir d’une URL de lien profond à l’aide d’Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: f4c67961316c4b78b691a46b35bd0c19bb409fd8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problèmes de connexion à une application à l’aide d’un lien profond

Le volet d’accès est un portail web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. 

Ces applications sont configurées pour le compte de l’utilisateur dans le portail Azure AD. Pour que l’application soit visible dans le volet d’accès, elle doit être correctement configurée et affectée à l’utilisateur ou à un groupe dont est membre l’utilisateur.

Les liens profonds ou URL d’accès utilisateur sont des liens que vos utilisateurs peuvent utiliser pour accéder à leurs applications à authentification unique par mot de passe directement à partir des barres d’URL de leurs navigateurs. En accédant à ce lien, les utilisateurs sont automatiquement connectés à l’application sans devoir passer par le volet d’accès. Il s’agit du lien dont les utilisateurs se servent pour accéder à ces applications à partir du lanceur d’applications Office 365.

## <a name="general-issues-to-check-first"></a>Problèmes d’ordre général à vérifier en premier

-   Vérifiez que votre **navigateur** répond à la configuration minimale requise pour le volet d’accès.

-   Vérifiez que l’URL de l’application figure dans la liste des **sites de confiance** du navigateur de l’utilisateur.

-   Vérifiez que l’application est **configurée** correctement.

-   Vérifiez que les connexions sont **activées** dans le compte de l’utilisateur.

-   Vérifiez que le compte d’utilisateur **n’est pas verrouillé**.

-   Vérifiez que le **mot de passe de l’utilisateur n’a pas expiré ou qu’il n’a pas été oublié**.

-   Vérifiez que **Multi-Factor Authentication** ne bloque pas l’accès utilisateur.

-   Vérifiez qu’une **stratégie d’accès conditionnel** ou **stratégie de protection d’identité** ne bloque pas l’accès utilisateur.

-   Vérifiez que les **informations de contact d’authentification** de l’utilisateur sont à jour et permettent d’appliquer les stratégies d’accès conditionnel ou de Multi-Factor Authentication.

-   Essayez également d’effacer les cookies de votre navigateur, puis réessayez de vous connecter.

## <a name="checking-the-deeplink"></a>Vérification du lien profond

Pour vérifier si vous disposez du lien ciblé correct, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

7.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

8.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

9.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

10. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

11. Sélectionnez l’application pour laquelle vous souhaitez vérifier le lien profond.

12. Recherchez l’étiquette **URL d’accès utilisateur**. Votre lien ciblé doit correspondre à cette URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Comment installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, procédez comme suit :

1.  Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans l’un des navigateurs pris en charge et connectez-vous en tant **qu’utilisateur** dans Azure AD.

2.  Cliquez sur une **application avec authentification unique par mot de passe** dans le volet d’accès.

3.  Dans l’invite vous demandant d’installer le logiciel, sélectionnez **Installer maintenant**.

4.  Vous êtes redirigé vers le lien de téléchargement selon votre navigateur. **Ajoutez** l’extension à votre navigateur.

5.  Si votre navigateur vous le demande, sélectionnez l’option **Activer** ou **Autoriser** pour l’extension.

6.  Une fois l’extension installée, **redémarrez** votre session de navigateur.

7.  Connectez-vous au volet d’accès et essayez de **lancer** vos applications à authentification unique par mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Firefox à partir des liens directs suivants :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Comment configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-Azure-AD-gallery)

-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, procédez comme suit :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur **Ajouter**.

Après un court délai, vous pouvez voir apparaître le volet de configuration de l’application.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  [Affectez des utilisateurs à l’application](#how-to-assign-a-user-to-an-application-directly).

10. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Comment configurer l’authentification unique par mot de passe pour une application ne figurant pas dans la galerie

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application ne figurant pas dans la galerie](#add-a-non-gallery-application)

-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Ajouter une application ne figurant pas dans la galerie

Pour ajouter une application à partir de la galerie Azure AD, procédez comme suit :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Cliquez sur **Application ne figurant pas dans la galerie**.

7.  Entrez le nom de votre application dans la zone de texte **Nom**. Sélectionnez **Ajouter.**

Après un court délai, vous pouvez voir apparaître le volet de configuration de l’application.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

    1.  Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  Spécifiez **l’URL de connexion**, autrement dit l’URL au niveau de laquelle les utilisateurs entrent leur nom d’utilisateur et leur mot de passe pour se connecter. Vérifiez que les champs de connexion sont visibles dans l’URL.

10. Affectez des utilisateurs à l’application.

11. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Comment affecter un utilisateur directement à une application

Pour affecter un ou plusieurs utilisateurs directement à une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

10. Tapez **le nom complet** ou **l’adresse de messagerie** de l’utilisateur souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Pour ajouter votre utilisateur à la liste **Sélectionné**, cochez la case en regard de la photo de profil ou du logo de l’utilisateur.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après une courte période, les utilisateurs que vous avez sélectionnés sont en mesure de lancer ces applications dans le volet d’accès.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Si ces étapes de dépannage ne résolvent pas le problème, 

créez un ticket de support en fournissant les informations suivantes, si disponibles :

-   ID d’erreur de corrélation

-   UPN (adresse e-mail de l’utilisateur)

-   ID de locataire

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur se produit

-   Traces Fiddler

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)
