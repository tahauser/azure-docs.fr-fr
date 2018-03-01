---
title: "Réinitialisation de votre mot de passe Azure AD | Microsoft Docs"
description: "Utiliser la fonction de réinitialisation de mot de passe en libre-service pour récupérer l’accès à votre compte professionnel ou scolaire"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 5c850fe9eaf78ab5ee6a175e5433998e1f15ab2e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="reset-your-work-or-school-password"></a>Réinitialiser votre mot de passe professionnel ou scolaire

Si vous avez oublié votre mot de passe, que le support de votre entreprise ne vous l’a jamais envoyé, que l’accès à votre compte a été bloqué ou que vous souhaitez le changer, nous pouvons vous aider. Si vous connaissez votre mot de passe et souhaitez simplement le changer, continuez jusqu’à la section [Modifier mon mot de passe](#change-my-password).

   > [!NOTE]
   > Si vous tentez de récupérer un compte personnel comme Xbox, hotmail.com ou outlook.com, essayez les suggestions fournies dans l’article [Lorsque vous n’avez pas accès à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire

Vous n’avez peut-être pas accès à votre compte Azure Active Directory (Azure AD) pour l’une des raisons suivantes :

* Votre mot de passe ne fonctionne pas et vous souhaitez le réinitialiser.
* Vous connaissez votre mot de passe, mais votre compte est verrouillé et vous souhaitez le déverrouiller.

Suivez les étapes ci-dessous pour accéder à la fonction de réinitialisation de mot de passe en libre-service Azure AD (SSPR) et récupérer votre compte.

1. Depuis une page de **Connexion** professionnelle ou scolaire, sélectionnez le lien **Vous ne pouvez pas accéder à votre compte ?**, puis sélectionnez **Compte professionnel ou scolaire**, ou accédez directement à la [page de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/).

    ![Vous ne pouvez pas accéder à votre compte ?][Login]

2. Entrez votre **identifiant utilisateur** professionnel ou scolaire, prouvez que vous n’êtes pas un robot en entrant les caractères visibles à l’écran, puis sélectionnez **Suivant**.

   > [!NOTE]
   > Si votre service informatique n’a pas activé cette fonctionnalité, le lien « Contactez votre administrateur » s’affiche afin que votre service informatique puisse vous aider, par e-mail ou via son propre portail web.
   >
   > Si vous avez besoin de déverrouiller votre compte, sélectionnez l’option **Je connais mon mot de passe, mais je ne parviens pas à me connecter**.
   >

3. Selon la façon dont votre service informatique a configuré la fonction SSPR, vous voyez apparaître une ou plusieurs des méthodes d’authentification ci-dessous. Vous ou votre service informatique avez normalement déjà fourni certaines de ces informations avant de suivre les étapes de l’article [S’inscrire à la réinitialisation de mot de passe en libre-service](active-directory-passwords-reset-register.md).

   * **Envoyer un e-mail sur mon adresse de messagerie de secours**
   * **Envoyer un SMS sur mon téléphone portable**
   * **Effectuer un appel sur mon téléphone portable**
   * **Effectuer un appel sur mon téléphone professionnel**
   * **Répondre à ma question de sécurité**

   Choisissez une option, répondez correctement aux questions, puis sélectionnez **Suivant**.

   ![Vérifier vos données d’authentification][Verification]

4. Si votre service informatique a besoin d’éléments de vérification supplémentaires, vous serez peut-être amené à répéter l’étape 3 en sélectionnant une option différente.
5. Sur la page **Choisir un nouveau mot de passe**, entrez un nouveau mot de passe, confirmez-le, puis sélectionnez **Terminer**. Votre mot de passe professionnel ou scolaire peut être soumis à certaines exigences que vous devez respecter. Nous vous conseillons de choisir un mot de passe de 8 à 16 caractères incluant des majuscules et des minuscules, un chiffre et un caractère spécial.
6. Lorsque vous voyez apparaître le message **Votre mot de passe a été réinitialisé**, cela signifie que vous pouvez vous connecter avec votre nouveau mot de passe.

    ![Votre mot de passe a été réinitialisé][Complete]

Vous devez maintenant être en mesure d’accéder à votre compte. Si ce n’est pas le cas, contactez le service informatique de votre entreprise pour obtenir de l’aide.

Vous recevrez peut-être un courrier de confirmation provenant d’un compte tel que « Microsoft de la part de \<votre entreprise> ». Si vous recevez ce type de message alors que vous n’avez pas utilisé la fonction de réinitialisation de mot de passe en libre-service pour récupérer l’accès à votre compte, contactez le service informatique de votre entreprise.

## <a name="change-my-password"></a>Modifier mon mot de passe

Si vous connaissez votre mot de passe et souhaitez le modifier, suivez les étapes ci-dessous.

### <a name="change-your-password-from-the-office-365-portal"></a>Modifier votre mot de passe à partir du portail Office 365

Utilisez cette méthode si vous avez l’habitude d’accéder à vos applications à l’aide du portail Office :

1. Connectez-vous à votre [compte Office 365](https://www.office.com) à l’aide de votre mot de passe actuel.
2. Sélectionnez votre profil en haut à droite de la fenêtre, puis sélectionnez **Afficher le compte**.
3. Sélectionnez **Sécurité et confidentialité** > **Mot de passe**.
4. Saisissez votre ancien mot de passe, entrez un nouveau mot de passe et confirmez-le, puis cliquez sur **Soumettre**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Modifier votre mot de passe à partir du volet d’accès Azure

Utilisez cette méthode si vous avez l’habitude d’accéder à vos applications à partir du volet d’accès Azure (MyApps) :

1. Connectez-vous au [volet d’accès Azure](https://myapps.microsoft.com/) à l’aide de votre mot de passe actuel.
2. Sélectionnez votre profil en haut à droite de la fenêtre, puis sélectionnez **Profil**.
3. Sélectionnez **Modifier le mot de passe**.
4. Saisissez votre ancien mot de passe, entrez un nouveau mot de passe et confirmez-le, puis cliquez sur **Soumettre**.

## <a name="reset-password-at-sign-in"></a>Réinitialiser le mot de passe à la connexion

Si votre administrateur a activé la fonctionnalité, vous pouvez maintenant voir un lien d’accès à **Réinitialiser le mot de passe** sur votre écran de connexion Windows 10 Fall Creators Update.

![Écran de connexion][LoginScreen]

Sélectionnez le lien **Réinitialiser le mot de passe** pour accéder à la fonctionnalité SSPR depuis l’écran de connexion ; vous pouvez ainsi réinitialiser votre mot de passe sans avoir à vous connecter pour accéder à la fonctionnalité web normale.

1. Vérifiez votre nom d’utilisateur et sélectionnez **Suivant**.
2. Sélectionnez la méthode de contact à utiliser pour la vérification et confirmez. Si votre service informatique a besoin d’éléments de vérification supplémentaires, vous serez peut-être amené à répéter cette étape en sélectionnant une option différente.

   ![Méthode de contact][ContactMethod]

3. Sur la page **Créer un mot de passe**, entrez un nouveau mot de passe, confirmez-le, puis sélectionnez **Suivant**. Nous vous conseillons de choisir un mot de passe de 8 à 16 caractères incluant des majuscules, des minuscules, des nombres et des caractères spéciaux.

   ![Réinitialiser le mot de passe][ResetPassword]

4. Lorsque vous voyez le message **Votre mot de passe a été réinitialisé**, sélectionnez **Terminer**.

Vous devez maintenant être en mesure d’accéder à votre compte. Si ce n’est pas le cas, contactez le service informatique de votre entreprise pour obtenir de l’aide.

## <a name="common-problems-and-their-solutions"></a>Problèmes courants et leurs solutions

 Voici quelques cas d'erreur courants et leurs solutions :

| Cas d’erreur| Quelle erreur voyez-vous ?| Solution |
| --- | --- | --- |
| Je vois une erreur quand j’essaie de changer mon mot de passe. | Malheureusement, votre mot de passe contient un mot, une expression ou un modèle qui rend votre mot de passe facile à deviner. Réessayez avec un autre mot de passe. | Choisissez un mot de passe qui est plus difficile à deviner. |
| La page « Contactez votre administrateur. » s’affiche lorsque j’entre mon identifiant utilisateur | Contactez votre administrateur. <br> <br> Nous avons détecté que votre mot de passe de compte d'utilisateur n'est pas géré par Microsoft. Par conséquent, nous ne pouvons pas réinitialiser automatiquement votre mot de passe. <br> <br> Vous devez contacter votre service informatique pour obtenir de l’aide. | Ce message s’affiche parce que votre service informatique gère votre mot de passe dans votre environnement local. Vous ne pouvez pas réinitialiser votre mot de passe à partir du lien « Vous ne pouvez pas accéder à votre compte ». <br> <br> Pour réinitialiser votre mot de passe, contactez directement un membre de votre service informatique afin d’obtenir de l’aide et informez-le que vous souhaitez réinitialiser votre mot de passe pour qu’il puisse activer cette fonctionnalité pour vous.|
| L’erreur « Votre compte n’est pas activé pour la réinitialisation de mot de passe » s’affiche lorsque j’entre mon identifiant utilisateur | Votre compte n’est pas activé pour la réinitialisation du mot de passe. <br> <br> Nous sommes désolés, mais votre service informatique n’a pas configuré votre compte pour utiliser ce service. <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Ce message s’affiche parce que votre service informatique n’a pas activé la réinitialisation de mot de passe pour votre entreprise à partir du lien « Vous ne pouvez pas accéder à votre compte » ou ne vous a pas octroyé de licence vous permettant d’utiliser la fonctionnalité. <br> <br> Pour réinitialiser votre mot de passe, sélectionnez le lien « Contacter un administrateur » afin d’envoyer un e-mail au service informatique de votre entreprise. Informez-le que vous souhaitez réinitialiser votre mot de passe afin qu’il puisse activer cette fonctionnalité pour vous. |
| L’erreur « Nous n'avons pas pu vérifier votre compte » s’affiche lorsque j’entre mon identifiant utilisateur | Nous n'avons pas pu vérifier votre compte. <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Ce message s’affiche parce que vous êtes autorisé à réinitialiser le mot de passe, mais que vous ne vous êtes pas inscrit pour utiliser le service. Pour demander une réinitialisation du mot de passe, accédez à https://aka.ms/ssprsetup une fois que vous avez de nouveau accès à votre compte. <br> <br> Pour réinitialiser votre mot de passe, sélectionnez le lien « Contacter un administrateur » afin d’envoyer un e-mail au service informatique de votre entreprise. |

## <a name="next-steps"></a>Étapes suivantes

* [Page relative à la procédure d’inscription pour utiliser la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
* [Page relative à l’inscription à la réinitialisation du mot de passe](https://aka.ms/ssprsetup)
* [Portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/)
* [Connexion impossible à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Page de connexion Vous ne pouvez pas accéder à votre compte ?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Vérifier vos données d’authentification"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Modifier votre mot de passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Votre mot de passe a été réinitialisé"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Lien de réinitialisation du mot de passe dans l’écran de connexion Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Vérifier vos données d’authentification"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Modifier votre mot de passe"
