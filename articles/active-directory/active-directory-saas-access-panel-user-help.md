---
title: "Obtenir de l’aide sur l’accès et l’utilisation du portail Mes applications dans Azure Active Directory | Microsoft Docs"
description: "Obtenez de l’aide sur la connexion et l’exécution de tâches courantes dans le volet d’accès."
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
ms.openlocfilehash: 285ee0c4e7f963c372c4f95face73864ba2a4f68
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Résoudre les problèmes liés à l’accès et à l’utilisation du portail Mes applications

Si vous rencontrez des problèmes liés à la connexion ou à l’utilisation du portail Mes applications, essayez ces conseils de résolution des problèmes avant de demander de l’aide au support technique ou à votre administrateur.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Je n’arrive pas à me connecter au portail Mes applications

Essayez ces conseils généraux :

- Vérifiez tout d’abord que vous utilisez l’URL correcte : [https://myapps.microsoft.com](https://myapps.microsoft.com).
- Essayez d’ajouter l’URL aux sites de confiance de votre navigateur.
- Vérifiez que votre mot de passe est correct et qu’il n’a pas expiré. Pour plus d’informations, consultez [Réinitialiser votre mot de passe professionnel ou scolaire](active-directory-passwords-update-your-own-password.md).
- Vérifiez que vos informations d’authentification sont à jour et qu’elles ne bloquent pas l’accès. Pour plus d’informations, consultez [Présentation concrète de Multi-Factor Authentication Azure](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Essayez d’effacer les cookies de votre navigateur, puis réessayez de vous connecter.

Si vous ne parvenez toujours pas à vous connecter, contactez votre administrateur.


## <a name="i-seem-to-be-having-password-issues"></a>Je pense rencontrer des problèmes de mot de passe

Si vous avez oublié votre mot de passe, si le service informatique ne vous en a jamais fourni, si l’accès à votre compte a été bloqué, ou si vous souhaitez modifier votre mot de passe, consultez [J’ai oublié mon mot de passe Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Je dois m’inscrire pour réinitialiser mon mot de passe

Vous pouvez réinitialiser votre mot de passe ou déverrouiller votre compte sans avoir à contacter qui que ce soit au moyen de la réinitialisation de mot de passe en libre-service (SSPR). Pour pouvoir utiliser cette fonctionnalité, vous devez inscrire vos méthodes d’authentification ou confirmer les méthodes d’authentification prédéfinies et demandées par votre administrateur. Pour plus d’informations, consultez [S’inscrire à la réinitialisation de mot de passe en libre-service](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Je n’arrive pas à installer l’extension de connexion sécurisée à Mes applications

Le portail Mes applications nécessite un navigateur qui prend en charge JavaScript et dans lequel CSS est activée. Si vous utilisez des applications à authentification unique par mot de passe, l’extension doit également être installée. Cette extension est téléchargée automatiquement lorsque vous démarrez une application configurée pour l’authentification unique par mot de passe.

Vérifiez que vous respectez les exigences de navigateur suivantes :
- **Edge** : sur Windows 10 Édition anniversaire ou version ultérieure.
- **Chrome** : sur Windows 7 ou version ultérieure, et sur Mac OS X ou version ultérieure.
- **Firefox 26.0 ou version ultérieure** : sur Windows XP SP2 ou version ultérieure, et sur Mac OS X 10.6 ou version ultérieure.
- **Internet Explorer 8, 9, 10, 11** : sur Windows 7 ou version ultérieure (prise en charge limitée).

Vous pouvez également télécharger l’extension pour Chrome et Edge directement à partir des sites suivants :

- [Extension Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extension Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Si vous avez installé l’extension et que vous rencontrez toujours des problèmes, essayez ce qui suit :

- Vérifiez vos paramètres d’extension de navigateur que vous assurer que l’extension est activée.
- Redémarrez votre navigateur et connectez-vous au portail Mes applications.
- Effacez les cookies de votre navigateur et connectez-vous au portail Mes applications.
- Pour accéder à un outil de diagnostic et pour obtenir des instructions pas à pas sur la configuration de l’extension pour Internet Explorer, consultez [Résoudre les problèmes liés à l’extension du volet d’accès pour Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

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
> Ces options sont disponibles uniquement pour Edge, Chrome et Firefox.

## <a name="how-do-i-add-a-new-app"></a>Comment ajouter une nouvelle application ?

1.  Dans la page **Applications**, sélectionnez **Ajouter une application**.
2.  Recherchez l’application que vous souhaitez ajouter, puis sélectionnez **Ajouter**.

   > [!NOTE]
   > * Vous ne pouvez accéder à cette option que si votre administrateur l’a activée pour votre compte.
   > * Si l’application nécessite une autorisation, vous devrez peut-être attendre l’approbation de l’administrateur.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>Comment gérer l’appartenance aux groupes ?

Sélectionnez la vignette **Groupes**, puis effectuez l’une des actions suivantes : 
* Pour créer un groupe, sous **Groupes dont je suis propriétaire**, sélectionnez **Créer un groupe**, puis suivez les instructions.
* Pour rejoindre un groupe, sous **Groupes auxquels j’appartiens**, sélectionnez **Rejoindre le groupe**, puis suivez les instructions.

   > [!NOTE]
   > * Vous ne pouvez accéder à cette option que si votre administrateur l’a activée pour votre compte.
   > * Si vous êtes membre d’un groupe, vous pouvez afficher les détails et quitter le groupe.
   > * Si vous êtes propriétaire d’un groupe, vous pouvez afficher les détails, ajouter ou supprimer des membres et quitter le groupe.
   >


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations relatives à la résolution des problèmes, consultez [Problèmes lors de l’utilisation du site web ou de l’application mobile du volet d’accès aux applications](active-directory-application-access-panel-content-map.md).

