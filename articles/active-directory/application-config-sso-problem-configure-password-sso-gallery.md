---
title: "Problème de configuration de l’authentification unique avec mot de passe pour une application de la galerie Azure AD | Microsoft Docs"
description: "Comprendre les problèmes courants auxquels les utilisateurs sont confrontés lors de la configuration d’une authentification unique avec mot de passe pour les applications qui figurent déjà dans la galerie d’applications Azure AD"
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
ms.openlocfilehash: 9550b28b373a59a6d41cee487aed9afc6c464479
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problème de configuration de l’authentification unique avec mot de passe pour une application de la galerie Azure AD

Cet article vous aide à comprendre les problèmes courants auxquels les utilisateurs sont confrontés lors de la configuration d’une **authentification unique avec mot de passe** pour une application de la galerie Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Les informations d’identification sont fournies, mais l’extension ne les envoie pas

Cela se produit généralement lorsque le fournisseur de l’application a modifié sa page de connexion pour ajouter un champ, pour modifier un identificateur sous-jacent que nous utilisions en vue de détecter les champs de nom d’utilisateur et de mot de passe ou pour modifier le fonctionnement de la connexion pour l’application. Heureusement, dans de nombreux cas, Microsoft peut collaborer avec les fournisseurs d’applications afin de résoudre rapidement les problèmes.

Bien que Microsoft dispose de technologies lui permettant de détecter automatiquement l’interruption de ces intégrations, nous ne sommes parfois pas en mesure d’identifier immédiatement ces problèmes, ou leur résolution prend un certain temps. Si l’une de ces intégrations ne fonctionne pas correctement, nous vous demandons de bien vouloir ouvrir un dossier de support afin que nous puissions résoudre le problème le plus rapidement possible.

En outre, **si vous êtes en contact avec le fournisseur de l’application,** **mettez-le en relation avec nous** afin que nous puissions collaborer pour intégrer nativement son application avec Azure Active Directory. Pour commencer, vous pouvez rediriger le fournisseur vers la page [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Les informations d’identification sont fournies et envoyées, mais la page indique qu’elles sont incorrectes

Pour résoudre ce problème, vérifiez les points suivants :

-   En premier lieu, invitez l’utilisateur à **se connecter directement au site web de l’application** avec les informations d’identification qu’il a enregistrées.

  * Si cela fonctionne, demandez à l’utilisateur de cliquer sur le bouton **Mettre à jour les informations d’identification** sur la **vignette de l’application** dans la section **Applications** du [volet d’accès à l’application](https://myapps.microsoft.com/), afin de les mettre à jour avec les derniers nom d’utilisateur et mot de passe fonctionnels connus.

   * Si vous ou un autre administrateur avez attribué les informations d’identification à cet utilisateur, recherchez l’affectation de l’application à l’utilisateur ou au groupe en accédant à l’onglet **Utilisateurs et groupes** de l’application, sélectionnez l’affectation, puis cliquez sur le bouton **Mettre à jour les informations d’identification**.

-   Si l’utilisateur s’est affecté ses propres informations d’identification, demandez-lui de **vérifier que son mot de passe pour l’application n’a pas expiré** et, le cas échéant, de **mettre à jour le mot de passe expiré** en se connectant directement à l’application.

   * Une fois le mot de passe mis à jour dans l’application, demandez à l’utilisateur de cliquer sur le bouton **Mettre à jour les informations d’identification** sur la **vignette de l’application** dans la section **Applications** du [volet d’accès à l’application](https://myapps.microsoft.com/), afin de les mettre à jour avec les derniers nom d’utilisateur et mot de passe fonctionnels connus.

   * Si vous ou un autre administrateur avez attribué les informations d’identification à cet utilisateur, recherchez l’affectation de l’application à l’utilisateur ou au groupe en accédant à l’onglet **Utilisateurs et groupes** de l’application, sélectionnez l’affectation, puis cliquez sur le bouton **Mettre à jour les informations d’identification**.

-   Invitez l’utilisateur à mettre à jour l’extension de navigateur du volet d’accès en suivant les étapes ci-dessous dans la section [Comment installer l’extension de navigateur du volet d’accès](#how-to-install-the-access-panel-browser-extension).

-   Vérifiez que l’extension de navigateur du volet d’accès est en cours d’exécution et activée dans le navigateur de l’utilisateur.

-   Vérifiez que vos utilisateurs ne tentent pas de se connecter à l’application à partir du volet d’accès alors qu’ils sont en **mode incognito, inPrivate ou privé**. Ces modes ne prennent pas en charge l’extension du volet d’accès.

Si cela ne fonctionne pas, il se pourrait qu’une modification ait eu lieu dans l’application et ait endommagé temporairement l’intégration de l’application avec Azure AD. Cela peut par exemple se produire lorsque le fournisseur de l’application introduit sur sa page un script qui se comporte différemment pour la saisie manuelle et pour la saisie automatique, ce qui engendre l’interruption des intégrations automatisées comme la nôtre. Heureusement, dans de nombreux cas, Microsoft peut collaborer avec les fournisseurs d’applications afin de résoudre rapidement les problèmes.

Bien que Microsoft dispose de technologies lui permettant de détecter automatiquement l’interruption de ces intégrations, nous ne sommes parfois pas en mesure d’identifier immédiatement ces problèmes, ou leur résolution prend un certain temps. Si l’une de ces intégrations ne fonctionne pas correctement, nous vous demandons de bien vouloir ouvrir un dossier de support afin que nous puissions résoudre le problème le plus rapidement possible.

En outre, **si vous êtes en contact avec le fournisseur de l’application,** **mettez-le en relation avec nous** afin que nous puissions collaborer pour intégrer nativement son application avec Azure Active Directory. Pour commencer, vous pouvez rediriger le fournisseur vers la page [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>L’extension fonctionne avec Chrome et Firefox, mais pas avec Internet Explorer

Il existe deux causes principales à ce problème :

-   Selon les paramètres de sécurité activés dans Internet Explorer, si le site web ne fait pas partie d’une **zone approuvée**, il arrive parfois que l’exécution de l’application par notre script soit bloquée.

  *  Pour résoudre ce problème, demandez à l’utilisateur d’**ajouter le site web de l’application** à la liste des **sites de confiance** dans **les paramètres de sécurité Internet Explorer**. Pour obtenir des instructions détaillées, vos utilisateurs peuvent consulter l’article [Comment ajouter un site à la liste des sites de confiance](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5).

-   Dans de rares cas, la validation de la sécurité d’Internet Explorer peut provoquer un chargement de la page plus lent que l’exécution de notre script.

   * Malheureusement, cette situation peut varier en fonction de la version du navigateur, de la vitesse de l’ordinateur ou du site visité. Dans ce cas, nous vous suggérons de contacter le support afin que nous puissions résoudre le problème d’intégration de cette application en particulier.

En outre, **si vous êtes en contact avec le fournisseur de l’application,** **mettez-le en relation avec nous** afin que nous puissions collaborer pour intégrer nativement son application avec Azure Active Directory. Pour commencer, vous pouvez rediriger le fournisseur vers la page [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Vérifiez si la page de connexion à l’application a été modifiée récemment ou requiert un champ supplémentaire.

Si la page de connexion à l’application a considérablement changé, cela provoque parfois l’interruption de nos intégrations. Par exemple, cela peut être le cas lorsque le fournisseur d’une application ajoute un champ de connexion, un captcha ou une authentification multifacteur à son système. Heureusement, dans de nombreux cas, Microsoft peut collaborer avec les fournisseurs d’applications afin de résoudre rapidement les problèmes.

Bien que Microsoft dispose de technologies lui permettant de détecter automatiquement l’interruption de ces intégrations, nous ne sommes parfois pas en mesure d’identifier immédiatement ces problèmes. Il se peut également que leur résolution prenne un certain temps. Si l’une de ces intégrations ne fonctionne pas correctement, nous vous demandons de bien vouloir ouvrir un dossier de support afin que nous puissions résoudre le problème le plus rapidement possible.

En outre, **si vous êtes en contact avec le fournisseur de l’application,** **mettez-le en relation avec nous** afin que nous puissions collaborer pour intégrer nativement son application avec Azure Active Directory. Pour commencer, vous pouvez rediriger le fournisseur vers la page [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

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

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)

