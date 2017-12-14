---
title: "Présentation de la gestion des appareils dans Azure Active Directory | Microsoft Docs"
description: "Découvrez comment la gestion des appareils peut vous aider à contrôler les appareils qui accèdent aux ressources de votre environnement."
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
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: faea960d2b6207aab5a2f5df8dc65fddc9ba5b54
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Présentation de la gestion des appareils dans Azure Active Directory

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory (Azure AD) autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Avec la prolifération des appareils, y compris des appareils Bring Your Own Device (BYOD), les professionnels de l’informatique sont confrontés à deux objectifs contradictoires :

- Permettre aux utilisateurs d’être productifs où et quand ils le veulent
- Protéger les ressources de l’entreprise à tout moment

Grâce aux appareils, les utilisateurs peuvent accéder aux ressources d’entreprise. Pour protéger vos ressources d’entreprise, en tant qu’administrateur informatique, vous souhaitez avoir le contrôle de ces appareils. Cela vous permet de vous assurer que vos utilisateurs ont accès à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. 

La gestion des appareils est également à la base de [l’accès conditionnel en fonction de l’appareil](active-directory-conditional-access-policy-connected-applications.md). Avec un accès conditionnel en fonction de l’appareil, vous pouvez garantir que l’accès aux ressources de votre environnement est uniquement possible pour les appareils de confiance.   

Cette rubrique explique comment fonctionne la gestion des appareils dans Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Donner le contrôle des appareils à Azure AD

Pour donner le contrôle d’un appareil à Azure AD, vous avez deux options :

- Inscription 
- Jonction

**L’inscription** d’un appareil sur Azure AD vous permet de gérer son identité. Lors de l’inscription d’un appareil, Azure AD Device Registration fournit une identité à l’appareil qui sera utilisée pour authentifier l’appareil lors de la connexion de l’utilisateur à Azure AD. Vous pouvez utiliser cette identité pour activer ou désactiver un appareil.

Quand ils sont associés à une solution de gestion des périphériques mobiles (GPM) comme Microsoft Intune, les attributs de l’appareil dans Azure AD sont mis à jour avec des informations supplémentaires sur l’appareil. Cela vous permet de créer des règles d’accès conditionnel qui imposent que l’accès à partir des appareils réponde à vos critères de sécurité et de conformité. Pour plus d’informations sur l’inscription d’appareils dans Microsoft Intune, consultez Inscrire des appareils pour la gestion dans Intune.

**La jonction** d’un appareil est une extension de l’inscription d’un appareil. En d’autres termes, cela vous offre tous les avantages de l’inscription d’un appareil et modifie également l’état local de celui-ci. Modifier l’état local permet à vos utilisateurs de se connecter à un appareil à l’aide d’un compte professionnel ou scolaire d’une organisation au lieu d’un compte personnel.

## <a name="azure-ad-registered-devices"></a>Appareils inscrits sur Azure AD   

L’objectif des appareils inscrits sur Azure AD est de permettre la prise en charge du scénario **Bring Your Own Device (BYOD)**. Dans ce scénario, un utilisateur peut accéder aux ressources contrôlées Azure Active Directory de votre organisation à l’aide d’un appareil personnel.  

![Appareils inscrits sur Azure AD](./media/device-management-introduction/03.png)

L’accès repose sur un compte professionnel ou scolaire saisi sur l’appareil.  
Par exemple, Windows 10 permet aux utilisateurs d’ajouter un compte professionnel ou scolaire à un ordinateur personnel, une tablette ou un téléphone.  
Lorsqu’un utilisateur ajoute un compte professionnel ou scolaire, l’appareil est automatiquement inscrit auprès d’Azure AD, voire éventuellement dans le système de gestion de périphériques mobiles (GPM) que votre organisation a configuré. Les utilisateurs de votre organisation peuvent facilement ajouter un compte professionnel ou scolaire à un appareil personnel :

- Pour votre premier accès à une application professionnelle
- Manuellement via le menu **Paramètres** dans le cas de Windows 10 

Vous pouvez configurer les appareils inscrits sur Azure AD pour Windows 10, iOS, Android et macOS.

## <a name="azure-ad-joined-devices"></a>Appareils joints Azure AD

Les appareils joints Azure AD ont pour objectif de simplifier :

- Les déploiements Windows des appareils professionnels 
- L’accès aux applications et aux ressources d’organisation à partir de n’importe quel appareil Windows

![Appareils inscrits sur Azure AD](./media/device-management-introduction/02.png)


Ces objectifs sont atteints en fournissant aux utilisateurs une expérience libre-service du contrôle des appareils professionnels par Azure AD.  
**Azure AD Join** est destiné aux organisations axées en priorité ou uniquement sur le cloud. Ce sont généralement les petites et moyennes entreprises qui ne possèdent pas d’infrastructure Windows Server Active Directory en local. 

L’implémentation d’appareils joints Azure AD vous offre les avantages suivants :

- **L’authentification unique** à vos applications et services SaaS gérés par Azure. Vos utilisateurs ne voient pas les invites d’authentification supplémentaires lorsqu’ils accèdent aux ressources de travail. La fonctionnalité d’authentification unique est disponible même lorsqu’ils ne sont pas connectés au réseau de domaine disponible.

- **L’itinérance compatible avec l’entreprise** des paramètres utilisateur sur les appareils joints. Les utilisateurs n’ont pas besoin de se connecter à un compte Microsoft (par exemple, Hotmail) pour afficher les paramètres sur les appareils.

- **Accéder au Windows Store pour Entreprises** à l’aide d’un compte AD. Les utilisateurs peuvent choisir parmi un inventaire d’applications présélectionnées par l’organisation.

- L’assistant **Windows Hello** fournit un accès sécurisé et pratique aux ressources de travail.

- **La restriction d’accès** aux applications ne s’applique qu’aux appareils qui répondent à la stratégie de conformité.

Bien qu’Azure AD Join soit principalement conçu pour les organisations qui ne disposent pas d’une infrastructure Windows Server Active Directory locale, vous pouvez également l’utiliser dans les scénarios où :

- Vous ne pouvez pas utiliser de jonction de domaine locale, par exemple, si vous avez besoin de contrôler des appareils mobiles, tels que des tablettes et des téléphones.

- Vos utilisateurs ont principalement besoin d’accéder à Office 365 ou d’autres applications SaaS intégrées à Azure AD.

- Vous souhaitez gérer un groupe d’utilisateurs dans Azure AD et non dans un répertoire Active Directory. Par exemple, cela peut concerner les travailleurs saisonniers, les prestataires ou les étudiants.

- Vous souhaitez fournir des fonctionnalités de jonction aux travailleurs dans des succursales distantes avec une infrastructure locale limitée.

Vous pouvez configurer des appareils joints Azure AD pour les appareils Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Appareils joints Azure AD hybrides

Pendant plus de dix ans, de nombreuses organisations ont utilisé la jonction de domaine dans leur répertoire Active Directory local pour permettre :

- Aux services informatiques de gérer les appareils professionnels à partir d’un emplacement central.

- Aux utilisateurs de se connecter à leurs appareils à partir de leurs comptes Active Directory professionnels ou scolaires. 

En règle générale, les organisations disposant d’empreintes locales s’appuient sur des méthodes de création d’images pour approvisionner les appareils. Ils utilisent souvent **System Center Configuration Manager (SCCM)** ou les **stratégies de groupe** pour les gérer.

Si votre environnement comporte une empreinte locale AD et vous souhaitez également profiter des fonctionnalités proposées par Azure Active Directory, vous pouvez implémenter les appareils joints Azure AD hybrides. Il s’agit d’appareils qui sont à la fois, joints à votre service Active Directory local et à Azure Active Directory.

![Appareils inscrits sur Azure AD](./media/device-management-introduction/01.png)


Vous devez utiliser des appareils joints Azure AD hybrides si :

- Vous disposez d’applications Win32 déployées sur ces appareils qui utilisent NTLM/Kerberos.

- Vous avez besoin de stratégies de groupe ou de SCCM/DCM pour gérer les appareils.

- Vous souhaitez continuer à utiliser des solutions de création d’images pour configurer les appareils de vos employés.

Vous pouvez configurer des appareils joints Azure AD hybrides pour Windows 10 et des appareils de bas niveau comme Windows 8 et Windows 7.

## <a name="summary"></a>Résumé

La gestion des périphériques dans Azure AD vous permet de : 

- Simplifier le processus consistant à donner le contrôle d’appareils à Azure AD

- Fournir à vos utilisateurs un accès facile aux ressources basées sur le cloud de votre organisation

En règle générale, vous devez utiliser :

- Des appareils inscrits sur Azure AD :

    - Pour les appareils personnels 

    - Pour inscrire manuellement des appareils auprès d’Azure AD

- Des appareils joints Azure AD : 

    - Pour les appareils qui sont détenus par votre organisation

    - Pour les appareils qui **ne sont pas** joints à un AD local

    - Pour inscrire manuellement des appareils auprès d’Azure AD

    - Pour changer l’état local d’un appareil

- Les appareils joints Azure AD hybrides pour les appareils joints à un AD local     

    - Pour les appareils qui sont détenus par votre organisation

    - Pour les appareils qui sont joints à un AD local

    - Pour inscrire automatiquement des appareils auprès d’Azure AD

    - Pour changer l’état local d’un appareil



## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des appareils dans le portail Azure, consultez [Gestion des appareils via le portail Azure](device-management-azure-portal.md).

- Pour en savoir plus sur l’accès conditionnel basé sur les appareils, consultez [Configurer les stratégies d’accès conditionnel basé sur les appareils Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- Pour configurer :
    - Des appareils Windows 10 inscrits à Azure Active Directory, consultez [Guide pratique pour configurer des appareils Windows 10 inscrits à Azure Active Directory](device-management-azuread-registered-devices-windows10-setup.md)
    - Des appareils joints à Azure Active Directory, consultez [Guide pratique pour configurer des appareils joints à Azure Active Directory](device-management-azuread-joined-devices-setup.md)
    - Des appareils hybrides joints à Azure AD, consultez [Guide pratique pour configurer des appareils hybrides joints à Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md).


