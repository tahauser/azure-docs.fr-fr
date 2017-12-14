---
title: Gestion des utilisateurs et des appareils par les administrateurs - Azure MFA | Microsoft Docs
description: "Cette section vous explique comment modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification."
documentationcenter: 
services: multi-factor-authentication
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 718ac36c8f4ca2367f90ad51edb175715194a17c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud
En tant qu’administrateur, vous pouvez gérer les paramètres des utilisateurs et des appareils :

* Demander aux utilisateurs de fournir à nouveau des méthodes de contact
* Supprimer des mots de passe d’application
* Exiger l’authentification MFA sur tous les appareils de confiance 

## <a name="require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs de fournir à nouveau des méthodes de contact
Ce paramètre force l’utilisateur à réexécuter le processus d’inscription. Les applications sans navigateur continuent de fonctionner si l’utilisateur dispose pour elles de mots de passe d’application.  Vous pouvez supprimer les mots de passe d’application des utilisateurs en choisissant **Supprimer tous les mots d'application existants, générés par les utilisateurs sélectionnés**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**. La page de l’authentification multifacteur s’affiche. 
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite. 
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact**.
   ![Fournir des méthodes de contact](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs
Ce paramètre supprime tous les mots de passe d’application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d’application cessent de fonctionner jusqu’à la création d’un nouveau mot de passe d’application.

### <a name="how-to-delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**. La page de l’authentification multifacteur s’affiche. 
6. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite. 
7. Sélectionnez **Gérer les paramètres utilisateur**.
8. Cochez la case **Supprimer tous les mots de passe d’application existants, générés par les utilisateurs sélectionnés**.
   ![Supprimer des mots de passe d'application](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Cliquez sur **save**.
10. Cliquez sur **Fermer**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurer MFA sur tous les appareils mémorisés d’un utilisateur
L’une des fonctionnalités configurables d’Azure Multi-Factor Authentication permet à vos utilisateurs de marquer les appareils comme approuvés. Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Les utilisateurs peuvent refuser la vérification en deux étapes pendant un nombre configurable de jours sur les appareils qu’ils utilisent régulièrement. Si un compte est compromis ou si un appareil de confiance est perdu, vous devez être en mesure de supprimer l’état approuvé et d’exiger à nouveau la vérification en deux étapes.

Le paramètre **Restaurer l’authentification multifacteur pour tous les appareils mémorisés** signifie que l’utilisateur sera invité à procéder à la vérification en deux étapes lors de sa prochaine connexion, qu’il ait choisi ou non de marquer son appareil comme approuvé. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Restaurer MFA sur tous les appareils interrompus d’un utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**. La page de l’authentification multifacteur s’affiche. 
6. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite. 
7. Sélectionnez **Gérer les paramètres utilisateur**.
8. Cochez la case **Restaurer Multi-Factor Authentication sur tous les appareils mémorisés**
   ![Supprimer les mots de passe d’application](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Cliquez sur **save**.
10. Cliquez sur **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)

- Si vos utilisateurs ont besoin d’aide, dirigez-les vers le [guide de l’utilisateur pour la vérification en deux étapes](./end-user/multi-factor-authentication-end-user.md)
