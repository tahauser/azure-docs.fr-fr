---
title: "Problèmes de connexion à une application de la galerie Azure AD configurée pour l’authentification unique par mot de passe | Microsoft Docs"
description: "Fournit des instructions sur la manière de résoudre les problèmes de connexion aux applications de la galerie Azure AD configurées pour l’authentification unique par mot de passe"
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
ms.openlocfilehash: c01bf353c9617811e14a3d60f91584b57ac0b5e7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problèmes de connexion à une application de la galerie Azure AD configurée pour l’authentification unique par mot de passe

Le volet d’accès est un portail Web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de lancer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Un utilisateur disposant d’éditions Azure AD peut également utiliser des fonctionnalités de gestion de groupes et d’applications en libre-service par le biais du volet d’accès. Le volet d’accès est distinct du portail Azure et n’exige pas des utilisateurs qu’ils aient un abonnement Azure.

Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Configuration requise du navigateur pour le volet d’accès

Le volet d’accès nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé. Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

Pour l’authentification unique par mot de passe, les navigateurs de l’utilisateur final peuvent être :

-   Internet Explorer 8, 9, 10, 11 -- sur Windows 7 ou version ultérieure

-   Chrome -- sur Windows 7 ou ultérieur, et sur Mac OS X ou ultérieur

-   Firefox 26.0 ou ultérieur -- sur Windows XP SP2 ou ultérieur, et sur Mac OS X 10.6 ou ultérieur

>[!NOTE]
>L’extension de l’authentification unique par mot de passe sera disponible pour Edge dans Windows 10 quand les extensions de navigateur seront prises en charge pour Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Comment installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, effectuez les étapes suivantes :

1.  Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans l’un des navigateurs pris en charge et connectez-vous en tant **qu’utilisateur** dans Azure AD.

2.  Cliquez sur une **application avec authentification unique par mot de passe** dans le volet d’accès.

3.  Dans l’invite vous demandant d’installer le logiciel, sélectionnez **Installer maintenant**.

4.  Vous êtes redirigé vers le lien de téléchargement selon votre navigateur. **Ajoutez** l’extension à votre navigateur.

5.  Si votre navigateur vous le demande, sélectionnez l’option **Activer** ou **Autoriser** pour l’extension.

6.  Une fois l’extension installée, **redémarrez** votre session de navigateur.

7.  Connectez-vous au volet d’accès et essayez de **lancer** vos applications à authentification unique par mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Firefox à partir des liens directs ci-dessous :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configuration d’une stratégie de groupe pour Internet Explorer

Vous pouvez configurer une stratégie de groupe pour installer à distance l’extension du volet d’accès pour Internet Explorer sur les ordinateurs de vos utilisateurs.

Vous devez respecter certaines conditions préalables, notamment les suivantes :

-   Vous avez configuré les [services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)et vous avez joint les ordinateurs de vos utilisateurs à votre domaine.

-   Vous devez disposer de l’autorisation « Modifier les paramètres » pour modifier l’objet de stratégie de groupe (GPO). Par défaut, les membres des groupes de sécurité suivants jouissent de cette autorisation : administrateurs de domaine, administrateurs d’entreprise et propriétaires créateurs de la stratégie de groupe. [Plus d’informations](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

Suivez le didacticiel [Déploiement de l’extension du volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) pour obtenir des instructions pas à pas sur la configuration et le déploiement d’une stratégie de groupe.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Résolution des problèmes liés au volet d’accès dans Internet Explorer

Consultez le guide [Résolution des problèmes liés à l’extension Volet d’accès pour Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot) pour accéder à un outil de diagnostic et pour obtenir des instructions pas à pas sur la configuration de l’extension pour Internet Explorer.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Comment configurer l’authentification unique par mot de passe pour une application ne figurant pas dans la galerie

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application ne figurant pas dans la galerie](#add-a-non-gallery-application)

-   [Configurer l’application pour l’authentification unique par mot de passe](#configure-the-application-for-password-single-sign-on)

-   [Affecter des utilisateurs à l’application](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Ajouter une application ne figurant pas dans la galerie

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Cliquez sur **Application ne figurant pas dans la galerie**.

7.  Entrez le nom de votre application dans la zone de texte **Nom**. Sélectionnez **Ajouter.**

Après un court délai, vous pouvez voir apparaître le volet de configuration de l’application.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  Entrez **l’URL de connexion**. Il s’agit de l’URL où les utilisateurs entrent leurs nom d’utilisateur et mot de passe pour se connecter. Vérifiez que les champs de connexion sont visibles dans l’URL.

10. Affectez des utilisateurs à l’application.

11. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

### <a name="assign-users-to-the-application"></a>Affecter des utilisateurs à l’application

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes :

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

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après une courte période, les utilisateurs que vous avez sélectionnés sont en mesure de lancer ces applications dans le volet d’accès.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Si ces étapes de dépannage ne résolvent pas le problème

Créez un ticket de support en fournissant les informations suivantes, si disponibles :

-   ID d’erreur de corrélation

-   UPN (adresse e-mail de l’utilisateur)

-   ID de locataire

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur se produit

-   Traces Fiddler

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)

