---
title: Dépanner des appareils hybrides de bas niveau joints à Azure Active Directory | Microsoft Docs
description: Dépannez des appareils hybrides de bas niveau joints à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0d21a8848222c4b09723e22d2d51ec43b2154553
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Dépanner des appareils hybrides de bas niveau joints à Azure Active Directory 

Cette rubrique s’applique uniquement aux appareils suivants : 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Pour Windows 10 ou Windows Server 2016, consultez la page [Dépanner des appareils hybrides Windows 10 et Windows Server 2016 joints à Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-current.md).

Cette rubrique suppose que vous avez [configuré les appareils hybrides joints à Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) de façon à prendre en charge les scénarios suivants :

- Accès conditionnel basé sur les appareils

- [Itinérance d’entreprise des paramètres](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello Entreprise](active-directory-azureadjoin-passport-deployment.md) 





Cette rubrique vous fournit des conseils sur la façon de résoudre les problèmes potentiels.  

**Bon à savoir :** 

- Le nombre maximal d’appareils par utilisateur dépend de l’appareil. Par exemple, si *jdoe* et *jharnett* se connectent à un appareil, une inscription (DeviceID) distincte est créée pour chacun d’eux dans l’onglet d’informations **UTILISATEUR**.  

- L’inscription / jointure d’appareils initiale est configurée pour effectuer une tentative à l’ouverture de session ou au verrouillage / déverrouillage. Un délai de cinq minutes peut être déclenché par une tâche du Planificateur de tâches. 

- La réinstallation du système d’exploitation de même qu’une désinscription / réinscription manuelle sont susceptibles de créer une nouvelle inscription sur Azure AD et d’aboutir à plusieurs entrées sous l’onglet d’informations UTILISATEUR sur le Portail Azure. 

## <a name="step-1-retrieve-the-registration-status"></a>Étape 1 : Récupérer l’état de l’inscription 

**Pour vérifier l’état de l’inscription :**  

1. Ouvrez une invite de commandes en tant qu’administrateur. 

2. Saisissez `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

Cette commande affiche une boîte de dialogue qui vous donne plus de détails sur l’état de la jonction.

![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Étape 2 : Évaluer l’état de la jointure Azure AD hybride 

Si la jointure Azure AD hybride n’a pas réussi, la boîte de dialogue vous fournit des informations sur le problème qui s’est produit.

**Les tâches les plus courantes sont :**

- Une mauvaise configuration d’AD FS ou d’Azure AD

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Vous n’êtes pas connecté en tant qu’utilisateur du domaine

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Cette situation peut se produire pour différentes raisons :
    
    1. Si l’utilisateur connecté n’est pas un utilisateur de domaine (par exemple, un utilisateur local). La jointure Azure AD hybride sur les appareils de bas niveau est uniquement prise en charge pour les utilisateurs de domaine.
    
    2. Si, pour une raison quelconque, Autoworkplace.exe ne peut pas s’authentifier sans assistance auprès d’Azure AD ou d’AD FS. Cette impossibilité peut avoir plusieurs motifs, par exemple s’il existe des problèmes de connectivité réseau sortante vers les URL Azure AD (vérifier les prérequis), ou lorsque MFA est activé/configuré pour l’utilisateur, mais que WIAORMUTLIAUTHN n’est pas configuré au niveau du serveur de fédération (vérifier les étapes de configuration). Ce problème peut également découler du fait que la page de découverte du domaine d’accueil (HRD) attend une intervention de la part de l’utilisateur, empêchant ainsi Autoworkplace.exe d’obtenir un jeton sans assistance.
    
    3. Si l’organisation utilise l’authentification unique transparente Azure AD, l’URL ci-après ne figure pas dans les paramètres intranet Internet Explorer de l’appareil :
    
       - https://autologon.microsoftazuread-sso.com

    
       et le paramètre « Autoriser les mises à jour de la barre d’état via le script » doit être activé pour la zone Intranet.

- Un quota a été atteint.

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Le service ne répond pas. 

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Vous pouvez également trouver les informations d’état dans le journal des événements sous **Applications and Services Log\Microsoft-Workplace Join**.
  
**Voici les causes les plus courantes d’échec d’une jointure Azure AD hybride :** 

- Votre ordinateur n’est pas sur le réseau interne de l’entreprise ou un réseau privé virtuel sans connexion à un contrôleur de domaine Active Directory local.

- Vous êtes connecté à votre ordinateur avec un compte d’ordinateur local. 

- Problèmes de configuration du service : 

  - Le serveur de fédération a été configuré pour prendre en charge **WIAORMULTIAUTHN**. 

  - Il n’existe aucun objet de point de connexion de service qui pointe vers le nom de votre domaine vérifié dans Azure AD dans la forêt Active Directory à laquelle l’ordinateur appartient.

  - Un utilisateur a atteint la limite d’appareils. 

## <a name="next-steps"></a>Étapes suivantes

Pour toute question, consultez [FAQ sur la gestion des appareils](device-management-faq.md)  
