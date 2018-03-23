---
title: Connexion par téléphone Microsoft Authenticator - Comptes Microsoft et Azure | Microsoft Docs
description: Utilisez votre téléphone pour vous connecter à votre compte Microsoft au lieu de taper votre mot de passe. Cet article répond aux questions fréquentes concernant cette fonctionnalité.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 86cf6ba2f2ec86a609b4ec60cc143b07d9e542b3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Connectez-vous avec votre téléphone, et non votre mot de passe

L’application Microsoft Authenticator vous permet de sécuriser vos comptes en effectuant une vérification en deux étapes après avoir entré votre mot de passe. Mais saviez-vous qu’elle peut remplacer complètement le mot de passe de votre compte Microsoft personnel ?

Cette fonctionnalité est disponible sur les appareils iOS et Android, et est compatible avec les comptes Microsoft personnels.

## <a name="how-it-works"></a>Fonctionnement

Vous êtes nombreux à utiliser l’application Microsoft Authenticator pour accomplir le processus de vérification en deux étapes lorsque vous vous connectez à votre compte Microsoft. Vous tapez votre mot de passe, puis accédez à l’application pour approuver une notification ou obtenir un code de vérification. Avec la connexion par téléphone, tout le processus de vérification d’identité se fait sur votre téléphone, sans nécessiter de mot de passe. La connexion par téléphone nécessite une vérification en deux étapes, qui fait appel à un élément que vous connaissez et à un autre que vous possédez, pour prouver votre identité. Le téléphone est toujours l’élément que vous possédez, et le code PIN du téléphone ou votre clé biométrique est l’élément que vous connaissez.

## <a name="how-to-get-started"></a>Pour commencer

Pour vous connecter à votre compte Microsoft personnel avec votre téléphone, procédez comme suit :

1. Activez la connexion par téléphone pour votre compte.

  - Si vous ne disposez pas encore de l’application Microsoft Authenticator, installez-la et ajoutez votre compte Microsoft personnel en suivant la procédure présentée dans l’article [Microsoft Authenticator](microsoft-authenticator-app-how-to.md). Les comptes qui viennent d’être ajoutés sont automatiquement activés. Vous n’avez donc rien d’autre à faire.

  - Si vous utilisez déjà Microsoft Authenticator pour la vérification en deux étapes, sélectionnez votre compte dans la page d’accueil de l’application, puis choisissez **Activer la connexion par téléphone** dans le menu déroulant.

  >[!NOTE]
  >Pour protéger votre compte, nous exigeons l’utilisation d’un code PIN ou d’un verrou biométrique sur votre appareil. Si vous laissez votre téléphone déverrouillé, une demande vous invitant à configurer un verrou avant d’activer la connexion par téléphone s’affiche dans l’application.

3. La plupart des pages où vous entrez habituellement le mot de passe de votre compte Microsoft comportent un lien indiquant **Utiliser une application à la place**. Sélectionnez ce lien pour vous connecter avec votre téléphone.

4. Microsoft envoie une notification à votre téléphone. Approuvez la notification pour vous connecter à votre compte.   

## <a name="faq"></a>Forum Aux Questions

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>En quoi la connexion par téléphone est-elle plus sécurisée que la saisie d’un mot de passe ?  

Aujourd’hui, la plupart des gens se connectent aux sites web ou aux applications à l’aide d’un nom d’utilisateur et d’un mot de passe.  Malheureusement, les mots de passe sont souvent perdus, volés ou devinés par des pirates. Lorsque vous configurez l’application Microsoft Authenticator pour vous connecter, nous générons sur votre téléphone une clé permettant de déverrouiller votre compte. Nous protégeons cette clé à l’aide du code PIN ou du verrou biométrique que vous utilisez déjà sur votre téléphone.  Lorsque vous vous connectez avec votre téléphone, cette clé permet de valider votre identité en toute sécurité avec deux facteurs : le téléphone lui-même et votre capacité à le déverrouiller. 

La clé utilisée est similaire à celles utilisées dans Windows Hello et les spécifications UAF de la FIDO Alliance. Vos données biométriques servent uniquement à pour protéger la clé localement et ne sont jamais envoyées ou stockées dans le cloud. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Où puis-je utiliser mon téléphone à la place de mon mot de passe, et où aurai-je encore besoin de ce mot de passe ?  

À l’heure actuelle, la fonctionnalité de connexion par téléphone ne fonctionne qu’avec les applications et les services web s’appuyant sur des comptes Microsoft personnels et avec les applications iOS, Android ou de Windows 10 qui utilisent un compte Microsoft personnel. Lorsque vous vous connectez à ces sites web ou applications, la page où vous entrez habituellement votre mot de passe comporte un lien indiquant **Utiliser une application à la place**. 

Pour le moment, la connexion par téléphone ne peut pas être utilisée pour déverrouiller un PC Windows, une XBOX ou la version de bureau des applications Microsoft telles que les applications Office.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Cette fonctionnalité remplace-t-elle la vérification en deux étapes ? Dois-je désactiver cette dernière ?   

Parfois. Nous sommes en train d’élargir le champ d’application de la connexion par téléphone, mais elle n’est pas encore prise en charge dans l’ensemble de l’écosystème Microsoft. Lorsqu’elle n’est pas disponible, nous utilisons toujours la vérification en deux étapes pour sécuriser la connexion. C’est pourquoi vous ne devez pas désactiver la vérification en deux étapes pour votre compte.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Si je laisse la vérification en deux étapes activée pour mon compte, dois-je approuver deux notifications ?

Non. La connexion à votre compte Microsoft avec votre téléphone est considérée comme un processus de vérification en deux étapes. Au lieu de taper votre mot de passe, puis d’approuver une notification, vous confirmez votre identité en accomplissant le déverrouillage de votre téléphone, puis en approuvant une notification. Nous ne vous envoyons pas de deuxième notification à approuver.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Et si je perds mon téléphone ou je ne l’ai pas sur moi, comment puis-je accéder à mon compte ?  

Vous pouvez toujours cliquer sur **Utiliser votre mot de passe à la place** sur la page de connexion pour revenir à la saisie du mot de passe. Notez bien que si vous utilisez la vérification en deux étapes, vous avez toujours besoin d’une deuxième méthode pour vérifier votre connexion. C’est pourquoi nous vous recommandons vivement d’avoir des informations de sécurité supplémentaires à jour dans votre compte. Vous pouvez gérer vos informations de sécurité sous https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Comment faire pour arrêter d’utiliser cette fonctionnalité et revenir à la saisie du mot de passe ?

Cliquez sur **Utiliser votre mot de passe à la place** sur la page de connexion. Votre choix le plus récent est mémorisé et vous est proposé par défaut lors de votre connexion suivante. Pour revenir à tout moment à la connexion par téléphone, cliquez sur **Utiliser une application à la place**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Puis-je utiliser l’application pour me connecter à tous mes comptes Microsoft ?   
Pour le moment, cette fonctionnalité est uniquement disponible pour les comptes Microsoft personnels. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Puis-je me connecter à mon PC avec mon téléphone ?  
Pour votre PC, nous vous recommandons de vous connecter à Windows Hello sur Windows 10 à l’aide de la reconnaissance des empreintes digitales ou faciale, ou d’un code PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Puis-je me connecter avec mon Windows Phone ?  
Pour l’instant, cette fonctionnalité n’est pas encore développée pour Microsoft Authenticator sur Windows Phone. 

## <a name="next-steps"></a>Étapes suivantes
Si vous n’avez pas téléchargé l’application Microsoft Authenticator, découvrez-la par vous-même. Cette application est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), et la connexion par téléphone est disponible sur l’application Microsoft Authenticator pour [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Si vous avez des questions concernant l’application en général, consultez le [Forum aux questions sur l’application Microsoft Authenticator](microsoft-authenticator-app-faq.md).
