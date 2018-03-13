---
title: "Résolution des problèmes d’accès aux ressources sur le portail Azure depuis un appareil Windows | Microsoft Docs"
description: "Découvrez d’où proviennent les problèmes d’accès et quels éléments vérifier pour éviter de rencontrer cette boîte de dialogue."
services: active-directory
keywords: "accès conditionnel en fonction de l’appareil, inscription de l’appareil, activer l’inscription de l’appareil, inscription de l’appareil et GPM"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 5ad9b01d3821b481fe3255c821e8674dcb26b322
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Problèmes d’accès aux ressources sur un appareil Windows

Par exemple, lors d’une tentative d’accès à l’intranet SharePoint Online de votre organisation, vous pouvez rencontrer une page indiquant que *vous ne pouvez pas y accéder à partir de votre emplacement*. Or, cette page s’affiche parce que votre administrateur a configuré une stratégie d’accès conditionnel, qui empêche l’accès aux ressources de votre organisation sous certaines conditions. Il peut être nécessaire de contacter le support technique ou votre administrateur pour résoudre ce problème. Toutefois, vous pouvez d’abord essayer de le faire vous-même.

Si vous utilisez un appareil **Windows**, vérifiez les éléments suivants :

- Le navigateur que vous utilisez est-il pris en charge ?

- Votre appareil exécute-t-il une version prise en charge de Windows ?

- Votre appareil est-il conforme ?






## <a name="supported-browser"></a>Navigateur pris en charge

Si votre administrateur a configuré une stratégie d’accès conditionnel, vous pouvez uniquement accéder aux ressources de votre organisation à l’aide d’un navigateur pris en charge. Sur un appareil Windows, seuls **Internet Explorer** et **Edge** sont pris en charge.

Vous pouvez facilement savoir si le fait que vous ne puissiez pas accéder à une ressource est lié à l’utilisation d’un navigateur non pris en charge, en consultant la section des détails de la page d’erreur :

![Messages d’accès refusé aux navigateurs non pris en charge](./media/active-directory-conditional-access-device-remediation/02.png "Scénario")

La seule possibilité consiste à utiliser un navigateur pris en charge par l’application sur la plateforme de votre appareil. Pour obtenir une liste complète des navigateurs pris en charge, consultez la liste des [navigateurs pris en charge](active-directory-conditional-access-supported-apps.md).  


## <a name="supported-versions-of-windows"></a>Versions de Windows prises en charge

Les conditions suivantes doivent exister sur le système d’exploitation Windows de votre appareil : 

- Si vous exécutez un système d’exploitation Windows sur votre appareil, il doit s’agir de Windows 7 ou d’une version ultérieure.
- Si vous exécutez un système d’exploitation Windows sur votre appareil, il doit s’agir de Windows Server 2008 R2 ou d’une version ultérieure. 


## <a name="compliant-device"></a>Conformité de l’appareil

Il se peut que l’administrateur ait configuré une stratégie d’accès conditionnel qui restreint l’accès aux ressources de l’organisation aux seuls appareils conformes. Pour être conforme, votre appareil doit être joint à votre annuaire Active Directory local ou à votre système Azure Active Directory.

Vous pouvez facilement savoir si le fait que vous ne puissiez pas accéder à une ressource est lié à l’utilisation d’un appareil non conforme, en consultant la section des détails de la page d’erreur :
 
![Messages d’accès refusé aux appareils non enregistrés](./media/active-directory-conditional-access-device-remediation/01.png "Scénario")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>Votre appareil est joint à un annuaire Active Directory local ?

**Si la réponse est oui, procédez comme suit :**

1. Vérifiez que vous êtes connecté à Windows à l’aide de votre compte professionnel (votre compte Active Directory).
2. Connectez-vous à votre réseau d’entreprise via un réseau privé virtuel (VPN) ou DirectAccess.
3. Une fois connecté, appuyez sur la touche Windows + la touche L pour verrouiller votre session Windows.
4. Déverrouillez votre session Windows en saisissant les informations d’identification de votre compte de travail.
5. Attendez une minute, puis essayez d’accéder à nouveau à l’application ou au service.
6. Si la même page s’affiche, cliquez sur **More details (Plus d'informations)** puis contactez votre administrateur et fournissez les informations demandées.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>Votre appareil n’est pas joint à un annuaire Active Directory local.

Dans ce cas, si votre appareil exécute Windows 10, vous avez deux possibilités :

* Exécuter Azure AD Join
* Ajouter votre compte professionnel ou scolaire à Windows

Pour plus d’informations sur les différences entre ces options, consultez la section [Utilisation d’appareils Windows 10 sur votre lieu de travail](active-directory-azureadjoin-windows10-devices.md).  
Ainsi :

- Si votre appareil appartient à l’organisation, exécutez l’option Azure AD Join.
- S’il s’agit d’un appareil personnel ou Windows Phone, ajoutez votre compte professionnel ou scolaire à Windows. 



#### <a name="azure-ad-join-on-windows-10"></a>Exécution de l’option Azure AD Join sur Windows 10

Les étapes permettant de joindre votre appareil à Azure AD sont liées à la version de Windows 10 en cours d’exécution sur ce système. Pour déterminer la version du système d’exploitation Windows 10, exécutez la commande **winver** : 

![Version de Windows](./media/active-directory-conditional-access-device-remediation/03.png )


**Mise à jour anniversaire Windows 10 (version 1607) :**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Comptes** > **Access work or school** (Accès professionnel ou scolaire).
3. Cliquez sur **Connecter**.
4. Cliquez sur **Joindre cet appareil à Azure AD**.
5. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
6. Déconnectez-vous puis reconnectez-vous à l’aide de votre compte professionnel.
7. Essayez d’accéder à nouveau à l’application.

**Mise à jour Windows 10 de novembre 2015 (version 1511) :**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Système** > **À propos de**.
3. Cliquez sur **Joindre Azure AD**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Déconnectez-vous et reconnectez-vous à l’aide de votre compte professionnel (votre compte Azure AD).
6. Essayez d’accéder à nouveau à l’application.


#### <a name="workplace-join-on-windows-81"></a>Workplace Join pour Windows 8.1

Si votre appareil n’est pas joint au domaine et exécute Windows 8.1, vous pouvez procéder à une jonction d’espace de travail et vous inscrire auprès de Microsoft Intune en appliquant la procédure suivante :

1. Ouvrez **Paramètres du PC**.
2. Cliquez sur **Réseau** > **Espace de travail**.
3. Cliquez sur **Joindre**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Cliquez sur **Activer**.
6. Essayez d’accéder à nouveau à l’application.



#### <a name="add-your-work-or-school-account-to-windows"></a>Ajouter votre compte professionnel ou scolaire à Windows 


**Mise à jour anniversaire Windows 10 (version 1607) :**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Comptes** > **Access work or school** (Accès professionnel ou scolaire).
3. Cliquez sur **Connecter**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Essayez d’accéder à nouveau à l’application.


**Mise à jour Windows 10 de novembre 2015 (version 1511) :**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Comptes** > **Vos comptes**.
3. Cliquez sur **Ajouter un compte professionnel ou scolaire**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Essayez d’accéder à nouveau à l’application.





## <a name="next-steps"></a>Étapes suivantes
[Accès conditionnel Azure Active Directory](active-directory-conditional-access-azure-portal.md)

