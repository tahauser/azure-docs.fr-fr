---
title: Application Microsoft Authenticator pour les téléphones mobiles | Microsoft Docs
description: Découvrez comment effectuer une mise à niveau vers la dernière version d’Azure Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 1532054a9463d710685d3f865d2e26ee7ff5014f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Prise en main de l’application Microsoft Authenticator
L’application Microsoft Authenticator fournit un niveau supplémentaire de sécurité dans votre compte professionnel ou scolaire (p. ex. bsimon@contoso.com) ou votre compte Microsoft (p. ex. bsimon@outlook.com).

L’application fonctionne de l’une des deux façons suivantes :

* **Notification**. L’application peut aider à empêcher tout accès non autorisé aux comptes et à arrêter les transactions frauduleuses en envoyant une notification à votre smartphone ou tablette. Affichez simplement la notification et si elle est légitime, sélectionnez **Vérifier**. Sinon, vous pouvez sélectionner **Refuser**.
* **Code de vérification**. L’application peut être utilisée comme jeton logiciel pour générer un code de vérification OAuth. Après avoir entré votre nom d’utilisateur et votre mot de passe, vous entrez le code fourni par l’application dans l’écran de connexion. Le code de vérification fournit un deuxième formulaire d’authentification.

L’application Microsoft Authenticator remplace l’application Azure Authenticator. L’application Azure Authenticator continue de fonctionner, mais cet article peut vous aider si vous décidez d’adopter la nouvelle application Azure Authenticator.  

## <a name="opt-in-for-two-step-verification"></a>Abonnement à la vérification en deux étapes

L’application Microsoft Authenticator ne fonctionne pas toute seule. Configurez chacun de vos comptes pour être invité à exécuter une deuxième méthode de vérification après vous être connecté avec votre nom d’utilisateur et votre mot de passe.

En général, pour un compte professionnel ou scolaire, vous ne pouvez pas prendre cette décision vous-même. Un administrateur de sécurité s’abonne à votre place et vous avertit que vous devez inscrire les méthodes de vérification pour votre compte. Si ce scénario vous concerne, reportez-vous à [Présentation concrète de Multi-Factor Authentication Azure](multi-factor-authentication-end-user.md).

Pour un compte personnel, vous devez configurer la vérification en deux étapes vous-même. Si vous avez un compte Microsoft, ces étapes sont disponibles dans [À propos de la vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Vous pouvez également utiliser Microsoft Authenticator avec des comptes non-Microsoft. La fonction peut ne pas s’appeler « vérification en deux étapes », mais vous devez pouvoir la trouver dans les paramètres de sécurité ou de connexion.

## <a name="install-the-app"></a>Installer l’application
L’application Microsoft Authenticator est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) et [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Ajouter des comptes à l’application
Pour chaque compte que vous souhaitez ajouter à l’application Microsoft Authenticator, utilisez l’une des procédures suivantes :

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Ajouter un compte Microsoft personnel à l’application

Pour un compte Microsoft personnel (celui qui vous permet de vous connecter à Outlook.com, Xbox, Skype, etc.), il vous suffit de vous connecter à votre compte dans l’application Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Ajouter un compte professionnel ou scolaire à l’application à l’aide du scanneur de code QR
1. Accédez à l’écran des paramètres de vérification de la sécurité.  Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Cochez la case **Application Authenticator**, puis sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/authenticator-app-how-to/azureauthe.png)

    Cela fait apparaître un écran avec un code QR dessus.

    ![Écran qui fournit le code QR](./media/authenticator-app-how-to/barcode2.png)
3. Ouvrez l’application Microsoft Authenticator Sur l’écran des **comptes**, sélectionnez **+**, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire.
4. Utilisez l’appareil photo pour scanner le code QR, puis sélectionnez **Terminé** pour fermer l’écran de code QR.

    Si votre appareil photo ne fonctionne pas correctement, vous pouvez [entrer manuellement le code QR et l’URL](#add-an-account-to-the-app-manually).

5. Lorsque l’application affiche le nom de votre compte souligné d’un code à six chiffres, c’est que vous avez terminé.

    ![Écran de comptes](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ajouter manuellement un compte à l’application
1. Accédez à l’écran des paramètres de vérification de la sécurité.  Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md).
2. Sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/authenticator-app-how-to/azureauthe.png)

    Cela fait apparaître un écran avec un code QR dessus.  Notez le code et l’URL.

    ![Écran qui fournit le code QR et l’URL](./media/authenticator-app-how-to/barcode2.png)
3. Ouvrez l’application Microsoft Authenticator Sur l’écran des **comptes**, sélectionnez **+**, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire.

4. Dans le scanner, sélectionnez **enter code manually**(entrer le code manuellement).

    ![Écran pour scanner un code QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Entrez le code et l’URL dans les zones appropriées de l’application, puis sélectionnez **Terminer**.

    ![Écran pour saisir le code et de l’URL](./media/authenticator-app-how-to/manual.png)

6. Lorsque l’application affiche le nom de votre compte souligné d’un code à six chiffres, c’est que vous avez terminé.

    ![Écran de comptes](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Ajouter un compte à l’application à l’aide des fonctionnalités d’identification par empreinte digitale ou reconnaissance faciale de votre appareil
Votre organisation peut exiger un code PIN pour effectuer le test de vérification. Au lieu de demander un code PIN, l’application Microsoft Authenticator peut utiliser les fonctionnalités d’identification par empreinte digitale ou reconnaissance faciale de votre appareil. Pour configurer ces fonctionnalités lors de la première vérification dans l’application, choisissez d’utiliser Touch ID (pour iOS) ou l’identification par empreinte digitale. 

Pour configurer Touch ID pour Microsoft Authenticator, vous devez effectuer un test de vérification normal avec un code PIN. Microsoft Authenticator configure automatiquement Touch ID pour les appareils qui le prennent en charge. 

![Vérification de la configuration de Touch ID](./media/authenticator-app-how-to/touchid1.png)

À partir de là, lorsque vous êtes invité à vérifier votre connexion, vous sélectionnez la notification Push reçue et vous scannez votre empreinte digitale au lieu d’entrer votre code confidentiel.

![Notification Push](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Utilisation de l’application lors de la connexion

Une fois que votre compte est ajouté à l’application, vous pouvez être invité à effectuer une vérification du test pour vous assurer que tout a été correctement configuré. Après cela, vous avez terminé ! Vous n’avez rien d’autre à faire avant la prochaine connexion.

Si vous avez choisi d’utiliser des codes de vérification dans l’application, ils s’affichent sur la page d’accueil. Ils changent toutes les 30 secondes, de sorte que vous ayez toujours un nouveau code lorsque vous en avez besoin. Mais vous n’avez rien à faire tant que vous n’êtes pas connecté et invité à saisir un code de vérification.  
