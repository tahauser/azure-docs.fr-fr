---
title: "Configurer des appareils inscrits à Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer des appareils inscrits à Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2560e51d61506389e84288bf983b0ebcb5776ff2
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurer des appareils Windows 10 inscrits à Azure Active Directory

La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que vos utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. Pour plus d’informations, voir [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md).

Pour le scénario **BYOD (Bring Your Own Device)**, configurez les appareils inscrits à Azure AD. Dans Azure AD, vous pouvez configurer les appareils inscrits à Azure AD pour Windows 10, iOS, Android et macOS. Cette rubrique contient les étapes à suivre pour les appareils Windows 10. 


## <a name="before-you-begin"></a>Avant de commencer

Pour inscrire un appareil Windows 10, le service d’inscription des appareils doit être configuré pour vous permettre d’inscrire des appareils. En plus d’avoir l’autorisation de joindre des appareils dans votre locataire Azure AD, vous devez avoir moins d’appareils inscrits que le nombre maximal configuré. Pour plus d’informations, consultez [Configurer les paramètres de l’appareil](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Ce que vous devez savoir

Lorsque vous inscrivez un appareil, gardez à l’esprit les éléments suivants :

- Windows inscrit l’appareil à l’annuaire de l’entreprise dans Azure AD

- Vous devrez peut-être répondre à une demande d’authentification multifacteur. Cette demande est configurable par votre administrateur informatique.

- Azure Active vérifie si l’appareil nécessite une inscription à la gestion des appareils mobiles, et l’inscrit si nécessaire.

- Si vous êtes un utilisateur géré, Windows vous dirige vers le bureau par le biais de la connexion automatique.

- Si vous êtes un utilisateur fédéré, vous accédez à l’écran d’ouverture de session Windows pour entrer vos informations d’identification.


## <a name="registering-a-device"></a>Inscription d’un appareil

Cette section fournit les étapes permettant d’inscrire votre appareil Windows 10 dans Azure AD. Si vous avez correctement inscrit votre appareil dans Azure AD, la boîte de dialogue **Accès Professionnel ou Scolaire** vous le confirme avec l’entrée **Compte professionnel ou scolaire**.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Pour inscrire votre appareil Windows 10 :**

1. Dans le menu **Démarrer**, cliquez sur **Paramètres**.

    ![Paramètres](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Cliquez sur **Comptes**.

    ![Comptes](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Cliquez sur **Accès Professionnel ou Scolaire**.

    ![Accès Professionnel ou Scolaire](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Dans la boîte de dialogue **Accès Professionnel ou Scolaire**, cliquez sur **Se connecter**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Dans la boîte de dialogue **Configurer un compte professionnel ou scolaire**, entrez le nom de votre compte (par exemple, someone@example.com), puis cliquez sur **Suivant**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Dans la boîte de dialogue **Saisie du mot de passe**, entrez votre mot de passe, puis cliquez sur **Suivant**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Dans la boîte de dialogue **Vous avez terminé**, cliquez sur **Terminé**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Vérification

Pour vérifier si un appareil est joint à un annuaire Azure AD, consultez la boîte de dialogue **Accès Professionnel ou Scolaire** sur votre appareil.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Vous pouvez également examiner les paramètres de l’appareil dans le portail Azure AD.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md).

- Pour plus d’informations sur la gestion des appareils dans le portail Azure AD, consultez [Gestion des appareils via le portail Azure](device-management-azure-portal.md).




