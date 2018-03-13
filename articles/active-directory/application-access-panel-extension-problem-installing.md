---
title: "Installer l’extension de navigateur du volet d’accès aux applications - Azure | Microsoft Docs"
description: "Corrigez les erreurs courantes rencontrées lorsque vous installez l’extension de navigateur du volet d’accès."
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
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: c49cfad5f362f4402be476066f0e8c0158f20d73
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="install-the-access-panel-browser-extension"></a>Installer l’extension de navigateur du volet d’accès

Le volet d’accès est un portail basé sur le web. Si vous disposez d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD), vous pouvez utiliser le volet d’accès pour afficher et démarrer des applications basées sur le cloud auxquelles un administrateur Azure AD vous a accordé un accès. 

Si vous utilisez des éditions Azure AD, vous pouvez également utiliser des fonctionnalités de gestion de groupes et d’applications en libre-service par le biais du volet d’accès. 

Le volet d’accès est séparé du portail Azure. Il ne nécessite pas de disposer d’un abonnement Azure.

## <a name="web-browser-requirements"></a>Configuration requise du navigateur web

Au minimum, le volet d’accès nécessite un navigateur prenant en charge JavaScript et dans lequel CSS est activée. Pour se connecter aux applications via l’authentification unique (SSO) dans le volet d’accès, l’extension du volet d’accès doit être installée dans votre navigateur. L’extension est téléchargée automatiquement lorsque vous sélectionnez une application configurée pour l’authentification unique (SSO) avec mot de passe.

Pour l’authentification unique avec mot de passe, vous pouvez utiliser un des navigateurs suivants :

- **Edge** : sur Windows 10 Édition anniversaire ou version ultérieure. 
- **Chrome** : sur Windows 7 ou version ultérieure, et sur Mac OS X ou version ultérieure.
- **Firefox 26.0 ou version ultérieure** : sur Windows XP SP2 ou version ultérieure, et sur Mac OS X 10.6 ou version ultérieure.
- **Internet Explorer 8, 9, 10, 11** : sur Windows 7 ou version ultérieure (prise en charge limitée).

## <a name="install-the-access-panel-browser-extension"></a>Installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, procédez comme suit :

1.  Dans l’un des navigateurs pris en charge, ouvrez le [volet d’accès](https://myapps.microsoft.com) et connectez-vous en tant qu’utilisateur dans votre compte Azure AD.

2.  Sélectionnez une application à authentification unique (SSO) avec mot de passe.

3.  Lorsque vous y êtes invité, sélectionnez **Installer maintenant**.  
    Vous êtes dirigé vers le lien de téléchargement de votre navigateur sélectionné. 
    
4.  Sélectionnez **Ajouter**.

5.  Si vous y êtes invité, **activez** ou **autorisez** l’extension.

6.  Une fois l’installation terminée, redémarrez votre navigateur.

7.  Connectez-vous au volet d’accès et vérifiez si vous pouvez démarrer vos applications à authentification unique (SSO) avec mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Edge directement à partir des sites suivants :

- [Extension Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extension Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Utiliser l’extension de connexion sécurisée à Mes applications
* Si vous utilisez une URL de Mes applications autre que `https://myapps.microsoft.com`, configurez votre URL par défaut en procédant comme suit :
   1. *Sans être* connecté à l’extension, cliquez avec le bouton droit sur l’icône de l’extension.
   2. Dans le menu, sélectionnez **My Apps URL** (URL de Mes applications).
   3. Sélectionnez votre URL par défaut.
   4. Sélectionnez l’icône de l’extension.
   5. Pour vous connecter à l’extension, sélectionnez **Connectez-vous pour commencer**.

* Pour vous connecter directement à une application à partir du navigateur, procédez comme suit :
   1. Après avoir installé l’extension, connectez-vous-y en sélectionnant **Connectez-vous pour commencer**.
   2. Connectez-vous à l’application avec l’URL de connexion.  
       L’URL de connexion est généralement l’URL de l’application qui affiche le formulaire de connexion.
      L’extension doit modifier l’état et vous indiquer qu’un mot de passe est disponible.
   3. Pour vous connecter, sélectionnez l’icône de l’extension.

* Pour démarrer une application à partir de l’extension, procédez comme suit :
   1. Après avoir installé l’extension, connectez-vous-y en sélectionnant **Connectez-vous pour commencer**.
   2. Sélectionnez l’icône de l’extension pour ouvrir son menu.
   3. Recherchez une application disponible dans le portail Mes applications.
   4. Dans la liste des résultats de la recherche, sélectionnez l’application.  
       Les trois dernières applications que vous avez utilisées sont affichées dans la liste de raccourcis **Récemment utilisé**.

> [!NOTE]
> Les options précédentes sont disponibles uniquement pour Edge, Chrome et Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurer une stratégie de groupe pour Internet Explorer

Vous pouvez configurer une stratégie de groupe pour installer à distance l’extension du volet d’accès pour Internet Explorer sur les machines de vos utilisateurs.

Avant de configurer une stratégie de groupe, vérifiez que :

-   Vous avez configuré les [services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)et vous avez joint les ordinateurs de vos utilisateurs à votre domaine.

-   Pour modifier l’objet de stratégie de groupe (GPO), vous devez disposer de l’autorisation *Modifier les paramètres*. Par défaut, cette autorisation est accordée aux membres des groupes de sécurité suivants : administrateurs de domaine, administrateurs d’entreprise et propriétaires créateurs de la stratégie de groupe.

Pour obtenir des instructions pas à pas sur la configuration et le déploiement d’une stratégie de groupe, consultez [Déployer l’extension du volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Résoudre les problèmes liés à l’extension du volet d’accès dans Internet Explorer

Pour accéder à un outil de diagnostic et pour obtenir des informations sur la configuration de l’extension pour Internet Explorer, consultez [Résoudre les problèmes liés à l’extension du volet d’accès pour Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Internet Explorer fait l’objet d’une prise en charge limitée et ne reçoit plus les nouvelles mises à jour logicielles. Edge est le navigateur recommandé.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Si les étapes précédentes ne permettent pas de résoudre le problème

Créez un ticket de support en fournissant les informations suivantes, si disponibles :

-   ID d’erreur de corrélation
-   UPN (adresse e-mail de l’utilisateur)
-   ID de locataire
-   Type de navigateur
-   Fuseau horaire et heure ou période à laquelle l’erreur s’est produite
-   Traces Fiddler

## <a name="next-steps"></a>Étapes suivantes
[Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
