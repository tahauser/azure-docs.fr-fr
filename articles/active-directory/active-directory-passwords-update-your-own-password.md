---
title: "Réinitialisation de votre mot de passe Azure AD | Microsoft Docs"
description: "Utiliser la fonction de réinitialisation de mot de passe en libre-service pour récupérer l’accès à votre compte professionnel ou scolaire"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 9595b318b8bd974f778dc12b38cccd949a4e7a1a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="reset-your-work-or-school-password"></a>Réinitialiser votre mot de passe professionnel ou scolaire

Si vous avez oublié votre mot de passe, que vous ne l’avez jamais reçu du support de votre entreprise, que l’accès à votre compte a été bloqué ou que vous souhaitez le changer, nous pouvons vous aider. Si vous connaissez votre mot de passe et que vous avez simplement besoin de le changer, continuez jusqu’à la section [Modifier mon mot de passe](#change-my-password) ci-dessous.

   > [!NOTE]
   > Si vous tentez de récupérer un compte personnel comme Xbox, hotmail.com ou outlook.com, essayez les [suggestions fournies dans cet article](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire

Si vous ne pouvez pas accéder à votre compte pour l’une des raisons suivantes :

* Votre mot de passe ne fonctionne pas et vous voulez le réinitialiser
* Vous connaissez votre mot de passe, mais votre compte est verrouillé et vous souhaitez le déverrouiller

Suivez les étapes ci-dessous pour accéder à la réinitialisation de mot de passe en libre-service Azure AD, puis revenez à votre compte.

1. Depuis n’importe quelle page de connexion professionnelle ou scolaire, cliquez sur le lien**Vous ne pouvez pas accéder à votre compte ?** puis cliquez sur **Compte professionnel ou scolaire**, ou accédez directement à la [page de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/).

    ![Vous ne pouvez pas accéder à votre compte ?][Login]

2. Entrez votre **identifiant utilisateur** professionnel ou scolaire, prouvez que vous n’êtes pas un robot en notant les caractères visibles à l’écran, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Si le personnel informatique n’a pas activé cette fonctionnalité, un lien « Contactez votre administrateur » s’affiche afin que le personnel puisse vous aider, par e-mail ou via leur propre portail web.
   >
   > Si vous avez besoin de déverrouiller votre compte, à ce stade choisissez l’option « Je connais mon mot de passe, mais je ne peux toujours pas me connecter ».
   >

3. Selon la façon dont votre personnel informatique a configuré la fonction SSPR, vous verrez apparaître une ou plusieurs des méthodes d'authentification ci-après. Vous ou votre personnel informatique avez déjà rempli certaines de ces informations avant d’utiliser l’article [S’inscrire à la réinitialisation de mot de passe en libre-service](active-directory-passwords-reset-register.md).

   * **Envoyer un e-mail sur mon adresse de messagerie de secours**
   * **Envoyer un SMS sur mon téléphone portable**
   * **Effectuer un appel sur mon téléphone portable**
   * **Effectuer un appel sur mon téléphone professionnel**
   * **Répondre à ma question de sécurité**

   Choisissez une option, répondez correctement et cliquez sur **Suivant**.

   ![Vérifier vos données d’authentification][Verification]

4. Votre personnel informatique pourra avoir besoin de plus d’éléments de vérification, vous devrez sans doute répéter l’étape 3 en sélectionnant une option différente.
5. Sur la page **Choisir un nouveau mot de passe**, entrez un nouveau mot de passe, confirmez votre choix, puis cliquez sur **Terminer**. Votre mot de passe professionnel ou scolaire peut présenter certaines exigences ; nous vous conseillons de choisir un mot de passe comprenant entre 8 et 16 caractères et incluant des majuscules, des minuscules, des nombres et des caractères spéciaux.
6. Lorsque vous voyez apparaître le message **Votre mot de passe a été réinitialisé**, cela signifie que vous pouvez vous connecter avec votre nouveau mot de passe.

    ![Votre mot de passe a été réinitialisé][Complete]

Vous devriez maintenant pouvoir accéder à votre compte. Le cas échéant, contactez le personnel informatique de votre organisation pour plus d’aide.

Vous pouvez recevoir un courrier de confirmation provenant d’un compte comme « Microsoft de la part de \<votre organisation> ». Si vous recevez ce type de message et que vous n’avez pas utilisé la fonction de réinitialisation du mot de passe en libre-service pour récupérer l’accès à votre compte, contactez le personnel informatique de votre entreprise.

## <a name="change-my-password"></a>Modifier mon mot de passe

Si vous connaissez déjà votre mot de passe et souhaitez le modifier, suivez les étapes suivantes afin de modifier votre mot de passe.

### <a name="change-your-password-from-the-office-365-portal"></a>Modifier votre mot de passe à partir du portail Office 365

Utilisez cette méthode si vous accédez généralement à vos applications à l’aide du portail Office

1. Connectez-vous à votre [compte Office 365](https://www.office.com) à l’aide de votre mot de passe existant.
2. Sur votre profil, cliquez dans le coin supérieur droit de la fenêtre, puis cliquez sur **Afficher le compte**
3. Cliquez sur **Sécurité et confidentialité** > **Mot de passe**
4. Saisissez votre ancien mot de passe, indiquez un nouveau mot de passe et confirmez-le, puis cliquez sur **Soumettre**

### <a name="change-your-password-from-the-azure-access-panel"></a>Modifier votre mot de passe à partir du volet d’accès Azure

Utilisez cette méthode si vous accédez généralement à vos applications à partir du portail d’accès Azure

1. Connectez-vous au [portail d’accès Azure](https://myapps.microsoft.com/) en utilisant votre mot de passe existant.
2. Sur votre profil, cliquez dans le coin supérieur droit de la fenêtre, puis cliquez sur **Profil**
3. Cliquez sur **Modifier le mot de passe**
4. Saisissez votre ancien mot de passe, indiquez un nouveau mot de passe et confirmez-le, puis cliquez sur **Soumettre**

## <a name="reset-password-at-login"></a>Réinitialiser le mot de passe à la connexion

Si votre administrateur a activé la fonctionnalité, vous pouvez maintenant voir un lien vers **Réinitialiser le mot de passe** sur votre écran de connexion Windows 10 Fall Creators Update.

![Écran de connexion][LoginScreen]

Cliquez sur le lien **Réinitialiser le mot de passe** pour accéder à l’expérience SSPR depuis l’écran de connexion ; vous pouvez ainsi réinitialiser votre mot de passe sans avoir à vous connecter pour accéder à l’expérience web normal.

1. Vérifiez votre nom d’utilisateur et cliquez sur **Suivant**.
2. Choisissez et confirmez une méthode de contact pour la vérification. Votre personnel informatique pouvant avoir besoin de plus d’éléments de vérification, vous devrez sans doute répéter cette étape en sélectionnant une option différente.

   ![Méthode de contact][ContactMethod]

3. Dans la page **Créer un mot de passe**, entrez un nouveau mot de passe, confirmez votre choix, puis cliquez sur **Suivant**. Nous vous conseillons de choisir un mot de passe comprenant 8 à 16 caractères, et incluant des majuscules, des minuscules, des nombres et des caractères spéciaux.

   ![Redéfinir le mot de passe][ResetPassword]

4. Quand vous voyez **Votre mot de passe a été réinitialisé**, cliquez sur **Terminer**.

Vous devriez maintenant pouvoir accéder à votre compte. Le cas échéant, contactez le personnel informatique de votre organisation pour plus d’aide.

## <a name="common-problems-and-their-solutions"></a>Problèmes courants et leurs solutions

 Voici quelques cas d'erreur courants et leurs solutions :

| Cas d’erreur| Quelle erreur voyez-vous ?| Solution |
| --- | --- | --- |
| J’obtiens une page « Veuillez contacter votre administrateur » après avoir entré mon identifiant utilisateur. | Veuillez contacter votre administrateur. <br> <br> Nous avons détecté que votre mot de passe de compte d'utilisateur n'est pas géré par Microsoft. Par conséquent, nous ne pouvons pas réinitialiser automatiquement votre mot de passe. <br> <br> Vous devez contacter votre équipe informatique pour une aide supplémentaire. | Ce message s’affiche parce que votre service informatique gère votre mot de passe dans votre environnement local et ne vous autorise pas à réinitialiser votre mot de passe à partir du lien Votre compte n’est pas accessible. <br> <br> Pour réinitialiser votre mot de passe, contactez directement votre service informatique pour obtenir de l’aide et informez-le que vous voulez réinitialiser votre mot de passe pour qu’il puisse activer cette fonctionnalité pour vous.|
| J'obtiens une erreur « Votre compte n'est pas activé pour la réinitialisation de mot de passe » après avoir entré mon identifiant utilisateur. | Votre compte n’est pas activé pour la réinitialisation du mot de passe <br> <br> Nous sommes désolés, mais votre service informatique n’a pas configuré votre compte pour utiliser ce service. <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Vous voyez ce message parce que votre service informatique n’a pas activé la réinitialisation de mot de passe pour votre organisation à partir du lien Votre compte n’est pas accessible ou ne vous a pas octroyé de licence vous permettant d’utiliser la fonctionnalité. <br> <br> Pour réinitialiser votre mot de passe, cliquez sur le lien Contacter un administrateur afin d’envoyer un courrier au service informatique de votre société : informez-le que vous voulez réinitialiser votre mot de passe pour qu’il puisse activer cette fonctionnalité pour vous. |
| J'obtiens une erreur « Impossible de vérifier votre compte » après avoir entré mon identifiant utilisateur. | Impossible de vérifier votre compte <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Vous voyez ce message parce que vous êtes autorisé à réinitialiser le mot de passe, mais que vous ne vous êtes pas inscrit pour utiliser le service. Pour demander une réinitialisation du mot de passe, accédez à http://aka.ms/ssprsetup une fois que vous avez à nouveau accès à votre compte. <br> <br> Pour réinitialiser votre mot de passe, cliquez sur le lien Contacter un administrateur pour envoyer un courrier au service informatique de votre société. |

## <a name="next-steps"></a>Étapes suivantes

* [Page relative à la procédure d’inscription pour utiliser la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
* [Page relative à l’inscription à la réinitialisation du mot de passe](http://aka.ms/ssprsetup)
* [Portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/)
* [Connexion impossible à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Page de connexion Vous ne pouvez pas accéder à votre compte ?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Vérifier vos données d’authentification"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Modifier votre mot de passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Votre mot de passe a été réinitialisé"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Lien de réinitialisation du mot de passe dans l’écran de connexion Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Vérifier vos données d’authentification"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Modifier votre mot de passe"
