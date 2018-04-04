---
title: Configurer la vérification en deux étapes pour mon compte professionnel ou scolaire | Microsoft Docs
description: 'Quand votre entreprise configure Azure Multi-Factor Authentication, vous êtes invité à vous inscrire au processus de vérification en deux étapes. Voici les étapes de configuration à suivre. '
services: multi-factor-authentication
keywords: utilisation du répertoire azure, active directory dans le cloud, didacticiel sur active directory
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: ec96243a880272a67ed960d50baba43dba8ec207
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="set-up-my-account-for-two-step-verification"></a>Configurer mon compte pour la vérification en deux étapes
La vérification en deux étapes est une mesure de sécurité supplémentaire qui renforce la protection de votre compte contre les tentatives d’intrusion. Si vous lisez cet article, c’est que vous avez probablement reçu un e-mail de la part de l’administrateur de votre entreprise ou établissement scolaire concernant Multi-Factor Authentication. Ou peut-être avez-vous reçu un message vous invitant à configurer la vérification de sécurité supplémentaire à la suite d’une tentative de connexion. Si tel est le cas, **vous devez compléter le processus d’inscription automatique avant de pouvoir vous connecter**.

Cet article vous aide à configurer votre **compte professionnel ou scolaire**. Si vous souhaitez activer la vérification en deux étapes pour votre compte Microsoft personnel, consultez [À propos de la vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Configurer votre compte

Si le support de votre entreprise vous impose d’utiliser la vérification en deux étapes, un écran indiquant **Votre administrateur exige que ce compte soit configuré pour une vérification de sécurité supplémentaire** apparaît :

![Paramétrage](./media/multi-factor-authentication-end-user-first-time/first.png)

Pour commencer, sélectionnez **Le configurer maintenant**.

Si vous ne voyez pas un écran semblable à celui-ci quand vous vous connectez, suivez les instructions indiquées dans [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) pour accéder à la page de paramètres où vous pouvez gérer vos options de vérification. 

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Décider du mode de vérification de vos connexions

La première question du processus d’inscription a trait à la façon dont vous souhaitez être contacté. Passez en revue les options dans le tableau et utilisez les liens pour accéder aux étapes de configuration de chaque méthode.

| Méthode de contact | Description |
| --- | --- |
| [Application mobile](#use-a-mobile-app-as-the-contact-method) |- **Recevoir des notifications pour la vérification.** Cette option envoie une notification à l’application d’authentification sur votre smartphone ou tablette. Examinez la notification et, si elle est légitime, sélectionnez **Authentifier** dans l’application. Votre entreprise ou établissement scolaire peut vous demander d’entrer un code confidentiel avant de vous authentifier.<br>- **Utiliser le code de vérification.** Dans ce mode, l’application d’authentification génère un code de vérification mis à jour toutes les 30 secondes. Entrez le code de vérification le plus récent dans l’interface de connexion.<br>L’application Microsoft Authenticator est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) et [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). |
| [Appel ou SMS sur téléphone mobile](#use-your-mobile-phone-as-the-contact-method) |- **Appel téléphonique** passe un appel vocal automatisé au numéro de téléphone indiqué. Répondez à l’appel et appuyez sur la touche # du clavier du téléphone pour vous authentifier.<br>- **SMS** envoie un SMS contenant un code de vérification. Selon l’invite contenue dans le SMS, répondez au SMS ou entrez le code de vérification fourni dans l’interface de connexion. |
| [Appel téléphonique au bureau](#use-your-office-phone-as-the-contact-method) |Passe un appel vocal automatisé au numéro de téléphone indiqué. Répondez à l’appel et appuyez sur la touche # du clavier du téléphone pour vous authentifier. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utiliser une application mobile comme méthode de contact
Cette méthode nécessite l’installation d’une application d’authentification sur votre téléphone ou tablette. Les étapes décrites dans cet article sont basées sur l’application Microsoft Authenticator, disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Dans la liste déroulante, sélectionnez **Application mobile**.
2. Sélectionnez **Recevoir des notifications pour la vérification** ou **Utiliser le code de vérification**, puis sélectionnez **Configurer**.

   ![Écran de vérification de sécurité supplémentaire](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Sur votre téléphone ou tablette, ouvrez l’application et sélectionnez **+** pour ajouter un compte. (Sur les appareils Android, sélectionnez les trois points, puis **Ajouter un compte**.)
4. Spécifiez que vous souhaitez ajouter un compte professionnel ou scolaire. Le scanner de code QR de votre téléphone s’ouvre. Si votre appareil photo ne fonctionne pas correctement, vous pouvez choisir d’entrer manuellement les informations de votre société. Pour plus d’informations, consultez [Ajouter manuellement un compte](#add-an-account-manually).  
5. Numérisez l’image du code QR apparaissant à l’écran de configuration de l’application mobile.  Sélectionnez **Terminer** pour fermer l’écran de code QR.  

   ![Écran de code QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Une fois l’activation terminée sur le téléphone, sélectionnez **Me contacter**.  Cette étape envoie une notification ou un code de vérification sur votre téléphone. Sélectionnez **Vérifier**.  
7. Si votre entreprise requiert un code confidentiel pour approuver la vérification de l’identification, entrez-le.

   ![Zone permettant d’entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Une fois le code confidentiel entré, sélectionnez **Fermer**. À ce stade, la vérification doit réussir.
9. Nous vous recommandons d’entrer votre numéro de téléphone mobile en cas de perte d’accès à votre application mobile. Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable dans la zone située en regard du nom de pays. Sélectionnez **Suivant**.
10. À ce stade, vous êtes invité à configurer les mots de passe d’application pour les applications sans navigateur, comme Outlook 2010 ou version antérieure, ou l’application de messagerie native sur les appareils Apple. Cette action s’explique par le fait que certaines applications ne prennent pas en charge la vérification en deux étapes. Si vous n’utilisez pas ces applications, cliquez sur **Terminé** et ignorez ces étapes restantes.
11. Si vous utilisez ces applications, copiez le mot de passe d’application fourni et collez-le dans votre application à la place de votre mot de passe habituel. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, [aide sur les mots de passe d’application].
12. Cliquez sur **Done**.

### <a name="add-an-account-manually"></a>Ajouter manuellement un compte
Si vous souhaitez ajouter manuellement un compte à l’application mobile sans passer par le lecteur de codes QR, procédez comme suit.

1. Sélectionnez le bouton **Entrer un compte manuellement** .  
2. Entrez le code et l’URL fournis dans la même page que celle présentant le code-barres. Ces informations sont destinées aux zones **Code** et **URL** de l’application mobile.

    ![Paramétrage](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Une fois l’activation terminée, sélectionnez **Me contacter**. Cette étape envoie une notification ou un code de vérification sur votre téléphone. Sélectionnez **Vérifier**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utiliser votre téléphone mobile comme méthode de contact
1. Sélectionnez **Téléphone d’authentification** dans la liste déroulante.  

    ![Paramétrage](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Choisissez votre pays dans la liste déroulante et entrez votre numéro de téléphone mobile.
3. Sélectionnez la méthode que vous préférez utiliser avec votre téléphone mobile : SMS ou appel.
4. Sélectionnez **Me contacter** pour vérifier votre numéro de téléphone. Selon la méthode sélectionnée, nous vous envoyons un SMS ou nous vous appelons. Suivez les instructions affichées à l’écran, puis sélectionnez **Vérifier**.
5. À ce stade, vous êtes invité à configurer les mots de passe d’application pour les applications sans navigateur, comme Outlook 2010 ou version antérieure, ou l’application de messagerie native sur les appareils Apple. Cette action s’explique par le fait que certaines applications ne prennent pas en charge la vérification en deux étapes. Si vous n’utilisez pas ces applications, cliquez sur **Terminé** et ignorez les étapes restantes.
6. Si vous utilisez ces applications, copiez le mot de passe d’application fourni et collez-le dans votre application à la place de votre mot de passe habituel. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, [aide sur les mots de passe d’application].
7. Cliquez sur **Done**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Utiliser votre téléphone de bureau comme méthode de contact
1. Sélectionnez **Téléphone (bureau)** dans la liste déroulante.  

    ![Paramétrage](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. La zone du numéro de téléphone est automatiquement renseignée avec les informations de contact de votre entreprise. Si le nombre est erroné ou manquant, demandez à votre administrateur d’apporter les modifications nécessaires.
3. Sélectionnez **Me contacter** pour vérifier votre numéro de téléphone. Nous vous appelons alors à ce numéro. Suivez les instructions affichées à l’écran, puis sélectionnez **Vérifier**.
4. À ce stade, vous êtes invité à configurer les mots de passe d’application pour les applications sans navigateur, comme Outlook 2010 ou version antérieure, ou l’application de messagerie native sur les appareils Apple. Cette action s’explique par le fait que certaines applications ne prennent pas en charge la vérification en deux étapes. Si vous n’utilisez pas ces applications, cliquez sur **Terminé** et ignorez les étapes restantes.
5. Si vous utilisez ces applications, copiez le mot de passe d’application fourni et collez-le dans votre application à la place de votre mot de passe habituel. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, consultez [Définition des mots de passe d’application](multi-factor-authentication-end-user-app-passwords.md).
6. Cliquez sur **Done**.

## <a name="next-steps"></a>étapes suivantes
* Modifier les options préférées et [gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md)
* Configurer des [mots de passe d’application](multi-factor-authentication-end-user-app-passwords.md) pour les applications natives sur des appareils qui ne prennent pas en charge la vérification en deux étapes
* Découvrir l’[application Microsoft Authenticator](microsoft-authenticator-app-how-to.md) pour mettre en place une authentification rapide et sécurisée, même sans accès au réseau de téléphonie mobile

