---
title: "Page « Besoin d’aide avec le portail Mes applications ? » dans Azure Active Directory | Microsoft Docs"
description: "Obtenez des instructions pour effectuer des tâches courantes lors de l’utilisation du Panneau d’accès."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9bec51e1d49308baecc76143ec80902d2da418e8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Besoin d’aide avec le portail Mes applications ?

Si cette page s’affiche, c’est que vous avez dû rencontrer un problème avec le portail Mes applications. Avant de contacter le support technique ou votre administrateur pour résoudre votre problème, lisez d’abord les sections de dépannage ci-dessous.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Je n’arrive pas à me connecter au portail Mes applications

Problèmes d’ordre général à vérifier en premier :

- Vérifiez que l’URL est correcte : [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Essayez d’ajouter l’URL aux sites de confiance de votre navigateur.

- Assurez-vous que votre mot de passe n’a pas expiré ou que vous ne l’avez pas oublié. Pour plus d’informations sur la modification des mots de passe, [cliquez ici](active-directory-passwords-update-your-own-password.md).

- Vérifiez que vos informations d’authentification sont à jour. Pour plus d’informations sur la configuration de vos informations d’authentification, [cliquez ici](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).

- Essayez d’effacer les cookies de votre navigateur, puis réessayez de vous connecter.

Si vous ne parvenez toujours pas à vous connecter, contactez votre administrateur.


## <a name="how-do-i-update-my-password"></a>Comment modifier mon mot de passe ?

Si vous avez oublié votre mot de passe, si le service informatique ne vous en a jamais fourni, si l’accès à votre compte a été bloqué, ou si vous souhaitez le modifier, consultez [J’ai oublié mon mot de passe Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="how-do-i-register-for-password-reset"></a>Comment m’inscrire pour réinitialiser mon mot de passe ?

En tant qu’utilisateur final, vous pouvez réinitialiser votre mot de passe ou déverrouiller votre compte, sans avoir à contacter qui que ce soit au moyen de la réinitialisation de mot de passe en libre-service (SSPR). Pour pouvoir utiliser cette fonctionnalité, il vous faut inscrire les méthodes d’authentification ou confirmer les méthodes d’authentification prédéfinies et remplies par votre administrateur. Pour plus d’informations, consultez [S’inscrire à la réinitialisation de mot de passe en libre-service](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Je n’arrive pas à installer l’extension de connexion sécurisée à Mes applications

Vérifiez que la configuration requise du navigateur est respectée :

- Le portail nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé. Si vous utilisez des applications à authentification unique par mot de passe, l’extension doit également être installée. Cette extension est téléchargée automatiquement lorsque vous lancez une application configurée pour l’authentification unique par mot de passe.

- La configuration requise du navigateur pour l’extension est la suivante :
    - Edge sur Windows 10 Édition anniversaire ou version ultérieure
    - Chrome sur Windows 7 ou version ultérieure, et sur Mac OS X ou version ultérieure
    - Firefox 26.0 ou version ultérieure sur Windows XP SP2 ou version ultérieure, et sur Mac OS X 10.6 ou version ultérieure
    - Internet Explorer 8, 9, 10, 11 sur Windows 7 ou version ultérieure (prise en charge limitée)

Vous pouvez également télécharger l’extension pour Chrome et Edge à partir des liens directs ci-dessous :

- [Extension Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Extension Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Si vous rencontrez des problèmes après l’installation, effectuez les étapes suivantes :

- Vérifiez dans les paramètres d’extension de navigateur que l’extension est activée.

- Redémarrez votre navigateur et connectez-vous au portail Mes applications.

- Effacez les cookies de votre navigateur et connectez-vous au portail Mes applications.
- Consultez le guide [Troubleshoot the Access Panel Extension for Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) (Résolution des problèmes liés à l’extension du volet d’accès pour Internet Explorer) pour accéder à un outil de diagnostic et pour obtenir des instructions pas à pas sur la configuration de l’extension pour Internet Explorer.

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
3. L’extension doit modifier l’état et vous indiquer qu’un mot de passe est disponible. Cliquez sur **l’icône de l’extension** pour vous connecter

Lancer une application à partir de l’extension
1. Après avoir installé l’extension, connectez-vous-y en sélectionnant **Connectez-vous pour commencer**.
2. Cliquez sur l’icône de l’extension pour ouvrir son menu.
3. **Recherchez** une application disponible dans le portail Mes applications.
4. Cliquez sur l’application à partir des **résultats de recherche** pour la lancer.
5. Les trois dernières applications lancées seront également affichées dans la liste de raccourcis **Récemment utilisé**

> [!NOTE]
> Ces options sont uniquement disponibles pour Edge, Chrome, Firefox.

## <a name="how-do-i-add-a-new-app"></a>Comment ajouter une nouvelle application ?

1.  Dans la page **Applications**, cliquez sur **Ajouter une application**.

2.  Recherchez l’application que vous souhaitez ajouter, puis cliquez sur **Ajouter**.

**Remarques :**

- Vous n’avez accès à cette option que si votre administrateur l’a activée pour votre compte.

- Si l’application nécessite une autorisation, vous devrez peut-être attendre l’approbation de l’administrateur.


## <a name="how-do-i-manage-my-group-memberships"></a>Comment gérer l’appartenance aux groupes ?

1. Cliquez sur la vignette **Groupes**. 
2. Pour créer un groupe, sous Groupes dont je suis propriétaire, cliquez sur **Créer un groupe**, puis suivez les instructions.
3. Pour rejoindre un groupe, sous Groupes auxquels j’appartiens, cliquez sur **Rejoindre le groupe**, puis suivez les instructions.

**Remarques :**

- Vous n’avez accès à cette option que si votre administrateur l’a activée pour votre compte.

- Vous pouvez afficher des informations détaillées sur les groupes auxquels vous appartenez, et vous pouvez aussi quitter ces groupes.

- Vous pouvez afficher des informations détaillées sur les groupes dont vous êtes propriétaire, ainsi que quitter ces groupes. Vous pouvez également y ajouter des membres ou en supprimer.


## <a name="next-steps"></a>étapes suivantes

Pour obtenir des informations relatives à la résolution des problèmes, consultez [Problems using the application access panel website or mobile application](active-directory-application-access-panel-content-map.md) (Problèmes lors de l’utilisation du site web ou de l’application mobile du volet d’accès aux applications).

