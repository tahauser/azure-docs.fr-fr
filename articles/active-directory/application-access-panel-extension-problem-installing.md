---
title: "Problèmes lors de l’installation de l’extension de navigateur du volet d’accès aux applications | Microsoft Docs"
description: "Comment corriger les erreurs courantes rencontrées lors de l’installation de l’extension de navigateur du volet d’accès"
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
ms.openlocfilehash: 26dc5d5ffce84206450123132c0633c2aa323e9f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>Problèmes lors de l’installation de l’extension de navigateur du volet d’accès aux applications

Le volet d’accès est un portail web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de lancer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Un utilisateur disposant d’éditions Azure AD peut également utiliser des fonctionnalités de gestion de groupes et d’applications en libre-service par le biais du volet d’accès. Le volet d’accès est distinct du portail Azure et n’exige pas des utilisateurs qu’ils aient un abonnement Azure.

Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Configuration requise du navigateur pour le volet d’accès

Le volet d’accès nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé. Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

Pour l’authentification unique par mot de passe, les navigateurs de l’utilisateur final peuvent être :

-   Edge sur Windows 10 Édition anniversaire ou version ultérieure 

-   Chrome -- sur Windows 7 ou ultérieur, et sur Mac OS X ou ultérieur

-   Firefox 26.0 ou ultérieur -- sur Windows XP SP2 ou ultérieur, et sur Mac OS X 10.6 ou ultérieur

-   Internet Explorer 8, 9, 10, 11 -- sur Windows 7 ou version ultérieure (prise en charge limitée)
## <a name="how-to-install-the-access-panel-browser-extension"></a>Comment installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, effectuez les étapes suivantes :

1.  Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans l’un des navigateurs pris en charge et connectez-vous en tant **qu’utilisateur** dans Azure AD.

2.  Cliquez sur une **application avec authentification unique par mot de passe** dans le volet d’accès.

3.  Dans l’invite vous demandant d’installer le logiciel, sélectionnez **Installer maintenant**.

4.  Vous êtes redirigé vers le lien de téléchargement selon votre navigateur. **Ajoutez** l’extension à votre navigateur.

5.  Si votre navigateur vous le demande, sélectionnez l’option **Activer** ou **Autoriser** pour l’extension.

6.  Une fois l’extension installée, **redémarrez** votre session de navigateur.

7.  Connectez-vous au volet d’accès et essayez de **lancer** vos applications à authentification unique par mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Edge à partir des liens directs ci-dessous :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>Comment utiliser l’extension de connexion sécurisée à Mes applications ?
Modification de l’URL par défaut de Mes applications pour l’extension

Si vous utilisez une autre URL que https://myapps.microsoft.com pour Mes applications, vous devez configurer l’URL par défaut à travers les étapes suivantes :
1. Sans être connecté à l’extension, **cliquez avec le bouton droit** sur l’icône d’extension.
2. Cliquez sur **Sélectionner une URL Mes applications** à partir du menu.
3. **Sélectionnez** votre URL par défaut.
4. Cliquez sur l’icône de l’extension.
5. Connectez-vous à l’extension en sélectionnant **Connectez-vous pour commencer**.

Se connecter directement à une application à partir du navigateur
1. Après avoir installé l’extension, connectez-vous-y en sélectionnant **Connectez-vous pour commencer**.
2. Accédez à **l’URL d’authentification** de l’application à laquelle vous souhaitez vous connecter. Il s’agit généralement l’URL de l’application qui affiche le formulaire de connexion.
3. L’extension doit modifier l’état et vous indiquer qu’un mot de passe est disponible. Cliquez sur **l’icône de l’extension** pour vous connecter.

Lancer une application à partir de l’extension
1. Après avoir installé l’extension, connectez-vous-y en sélectionnant **Connectez-vous pour commencer**.
2. Cliquez sur l’icône de l’extension pour ouvrir son **menu**.
3. **Recherchez** une application disponible dans le portail Mes applications.
4. Cliquez sur l’application à partir des **résultats de recherche** pour la lancer.
5. Les trois dernières applications lancées seront également affichées dans la liste de raccourcis **Récemment utilisé**

> [!NOTE]
> Ces options sont uniquement disponibles pour Edge, Chrome, Firefox.

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configuration d’une stratégie de groupe pour Internet Explorer

Vous pouvez configurer une stratégie de groupe pour installer à distance l’extension du volet d’accès pour Internet Explorer sur les ordinateurs de vos utilisateurs.

Vous devez respecter certaines conditions préalables, notamment les suivantes :

-   Vous avez configuré les [services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)et vous avez joint les ordinateurs de vos utilisateurs à votre domaine.

-   Vous devez disposer de l’autorisation « Modifier les paramètres » pour modifier l’objet de stratégie de groupe (GPO). Par défaut, les membres des groupes de sécurité suivants jouissent de cette autorisation : administrateurs de domaine, administrateurs d’entreprise et propriétaires créateurs de la stratégie de groupe. [Plus d’informations](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

Suivez le didacticiel [Déploiement de l’extension du volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md) pour obtenir des instructions pas à pas sur la configuration et le déploiement d’une stratégie de groupe.

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Résolution des problèmes liés à l’extension du volet d’accès dans Internet Explorer

Consultez le guide [Troubleshoot the Access Panel Extension for Internet Explorer](active-directory-saas-ie-troubleshooting.md) (Résolution des problèmes liés à l’extension du volet d’accès pour Internet Explorer) pour accéder à un outil de diagnostic et pour obtenir des instructions pas à pas sur la configuration de l’extension pour Internet Explorer.

> [!NOTE]
> Internet Explorer fait l’objet d’une prise en charge limitée et ne reçoit plus les nouvelles mises à jour logicielles. Edge est le navigateur recommandé.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Si ces étapes de dépannage ne résolvent pas le problème

Ouvrez un ticket de support en fournissant les informations suivantes, dans la mesure du possible :

-   ID d’erreur de corrélation

-   UPN (adresse e-mail de l’utilisateur)

-   ID de locataire

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur se produit

-   Traces Fiddler

## <a name="next-steps"></a>étapes suivantes
[Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
