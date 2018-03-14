---
title: "Configurer des appareils joints à Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer des appareils joints à Azure Active Directory."
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
ms.openlocfilehash: ac6c9224925e5bfd3cb056c6c8d9cf2a96b0eb2b
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Configurer des appareils joints à Azure Active Directory

La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que vos utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md).

Si vous souhaitez mettre des appareils Windows 10 d’entreprise sous le contrôle d’Azure AD, configurez-les comme des appareils joints Azure AD. Cette rubrique contient les étapes à suivre pour une telle configuration. 


## <a name="prerequisites"></a>Prérequis

Pour joindre un appareil Windows 10, le service d’inscription des appareils doit être configuré pour vous permettre d’inscrire des appareils. En plus d’avoir l’autorisation de joindre des appareils dans votre locataire Azure AD, vous devez avoir moins d’appareils inscrits que le nombre maximal configuré. Pour plus d’informations, consultez [Configurer les paramètres de l’appareil](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Ce que vous devez savoir


- Windows joint l’appareil à l’annuaire de l’entreprise dans Azure AD.

- Vous devrez peut-être répondre à une demande d’authentification multifacteur. Cette demande est configurable par votre administrateur informatique.

- Azure Active vérifie si l’appareil nécessite une inscription à la gestion des appareils mobiles, et l’inscrit si nécessaire.

- Si vous êtes un utilisateur géré, Windows vous dirige vers le bureau par le biais de la connexion automatique.

- Si vous êtes un utilisateur fédéré, vous devez vous connecter à l’aide de vos informations d’identification.


## <a name="joining-a-device"></a>Joindre un appareil

Cette section fournit les étapes permettant de joindre votre appareil Windows 10 à Azure AD. Si vous avez correctement joint votre appareil dans Azure AD, la boîte de dialogue **Accès Professionnel ou Scolaire** vous le confirme à l’aide de l’entrée **Connecté à \<votre annuaire Azure AD\>**.

![Connecté](./media/device-management-azuread-joined-devices-setup/13.png)


**Pour joindre votre appareil Windows 10 :**

1. Dans le menu **Démarrer**, cliquez sur **Paramètres**.

    ![Paramètres](./media/device-management-azuread-joined-devices-setup/01.png)

2. Cliquez sur **Comptes**.

    ![Comptes](./media/device-management-azuread-joined-devices-setup/02.png)


3. Cliquez sur **Accès Professionnel ou Scolaire**.

    ![Accès Professionnel ou Scolaire](./media/device-management-azuread-joined-devices-setup/03.png)

4. Dans la boîte de dialogue **Accès Professionnel ou Scolaire**, cliquez sur **Se connecter**.

    ![Connecter](./media/device-management-azuread-joined-devices-setup/04.png)


5. Dans la boîte de dialogue **Configurer un compte professionnel ou scolaire**, cliquez sur **Joindre cet appareil à Azure Active Directory**.

    ![Connecter](./media/device-management-azuread-joined-devices-setup/08.png)


6. Dans la boîte de dialogue **Let’s get you signed in** (Connectez-vous), entrez le nom de votre compte (par exemple, someone@example.com), puis cliquez sur **Suivant**.

    ![Boîte de dialogue Let’s get you signed in (Connectez-vous)](./media/device-management-azuread-joined-devices-setup/10.png)


6. Dans la boîte de dialogue **Saisie du mot de passe**, entrez votre mot de passe, puis cliquez sur **Se connecter**.

    ![Boîte de dialogue Saisie du mot de passe](./media/device-management-azuread-joined-devices-setup/05.png)


7. Dans la boîte de dialogue **Vérifiez qu’il s’agit de votre organisation**, cliquez sur **Joindre**.

    ![Boîte de dialogue Vérifiez qu’il s’agit de votre organisation](./media/device-management-azuread-joined-devices-setup/11.png)


8. Dans la boîte de dialogue **Vous avez terminé**, cliquez sur **Terminé**.

    ![Boîte de dialogue You’re all set (C’est terminé !)](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Vérification

Pour vérifier si un appareil est joint à un annuaire Azure AD, consultez la boîte de dialogue **Accès Professionnel ou Scolaire** sur votre appareil.

![Connecté](./media/device-management-azuread-joined-devices-setup/13.png)

Vous pouvez également exécuter la commande suivante : `dsregcmd /status`  
Sur un appareil correctement joint, **Joint à Azure AD** est défini sur **Oui**.

![Connecté](./media/device-management-azuread-joined-devices-setup/14.png)

Vous pouvez également examiner les paramètres de l’appareil dans le portail Azure AD.

![Connecté](./media/device-management-azuread-joined-devices-setup/15.png)

Pour plus d’informations, consultez [Localiser des appareils](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>étapes suivantes

Pour plus d'informations, consultez les pages suivantes : 

- [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)
- [Gestion des appareils via le portail Azure](device-management-azure-portal.md)
- 



