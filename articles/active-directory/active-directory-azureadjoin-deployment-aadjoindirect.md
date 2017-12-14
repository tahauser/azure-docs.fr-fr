---
title: "Scénarios d’utilisation et considérations relatives au déploiement pour Azure AD Join | Microsoft Docs"
description: "Explique comment les administrateurs peuvent configurer « Azure AD Join » pour leurs utilisateurs finaux (employés, étudiants, autres utilisateurs). Elle présente également les différents scénarios réels pour l'utilisation de Azure AD Join."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 173ad6f07699ca6bfa534dedc053663bba571382
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scénarios d’utilisation et considérations relatives au déploiement pour Azure AD Join
## <a name="usage-scenarios-for-azure-ad-join"></a>Scénarios d’utilisation pour Azure AD Join
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scénario 1 : entreprises dont l’activité a lieu en grande partie dans le cloud
Azure Active Directory Join (Azure AD Join) possède de nombreux avantages si vous utilisez et gérez actuellement votre activité dans le cloud ou si vous envisagez bientôt de passer au cloud. Vous pouvez utiliser un compte que vous avez créé dans Azure AD pour vous connecter à Windows 10. À l’aide de [l’introduction de l’interface logicielle lors de la première utilisation](active-directory-azureadjoin-user-frx.md) ou en rejoignant Azure AD via le [menu des paramètres](active-directory-azureadjoin-user-upgrade.md), vos utilisateurs peuvent joindre leurs ordinateurs à Azure AD.  Ils bénéficient également d’un accès via l’authentification unique (SSO) à des ressources cloud comme Office 365 soit dans le navigateur, soit dans les applications Office.

### <a name="scenario-2-educational-institutions"></a>Scénario 2 : établissements d’enseignement
Les établissements d’enseignement ont généralement deux types d’utilisateurs : les enseignants et les étudiants. Les enseignants sont considérés comme des membres à plus long terme de l’organisation. Il est souhaitable de créer des comptes locaux pour eux. Mais les étudiants sont des membres à plus court terme de l’organisation et leurs comptes peuvent être gérés dans Azure AD. Cela signifie que la mise à l'échelle de répertoire peut être transmise vers le cloud au lieu d'être stockée en local. Cela signifie également que ces étudiants pourront se connecter à Windows avec leur compte Azure AD et accéder aux ressources Office 365 dans les applications Office.

### <a name="scenario-3-retail-businesses"></a>Scénario 3 : entreprises de vente au détail
Les entreprises de vente au détail font appel à des saisonniers et à des salariés à long terme. En général, vous créez des comptes locaux et utilisez des ordinateurs joints au domaine pour les employés à plein temps. À l’inverse, les saisonniers sont présents pour une période plus courte et il est préférable de gérer leurs comptes là où les licences utilisateur peuvent être plus facilement déplacées. Lorsque vous créez leurs comptes utilisateur dans le cloud avec des licences Office 365, cela leur permet de bénéficier des avantages de la connexion à Windows et aux applications Office avec un compte Azure AD, tout en conservant la mobilité de leurs licences une fois qu’ils ont quitté l’établissement.

### <a name="scenario-4-additional-scenarios"></a>Scénario 4 : autres cas de figure
En plus des avantages décrits antérieurement, le fait que les utilisateurs joignent leurs appareils à Azure AD offre les avantages suivants : expérience de jonction au domaine simplifiée, gestion automatique des appareils mobiles et authentification unique auprès d’Azure AD et des ressources locales.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Considérations relatives au déploiement pour Azure AD Join
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permettre aux utilisateurs de joindre un appareil appartenant à l’entreprise directement à Azure AD
Les entreprises peuvent fournir des comptes gérés sur le cloud uniquement aux organisations et sociétés partenaires. Ces partenaires peuvent ensuite accéder facilement aux applications et aux ressources d’entreprise avec l’authentification unique. Ce scénario s’applique aux utilisateurs qui accèdent aux ressources principalement dans le cloud, comme Office 365 ou les applications SaaS qui s’appuient sur Azure AD pour l’authentification.

### <a name="prerequisites"></a>Composants requis
**Au niveau de l’entreprise (administrateur)**

* Abonnement Azure avec Azure Active Directory  

**Au niveau de l’utilisateur**

* Windows 10 (éditions Professionnel et Entreprise)

### <a name="administrator-tasks"></a>Tâches de l’administrateur
* [Configuration de l’inscription des appareils](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tâches de l’utilisateur
* [Configuration d’un nouvel appareil Windows 10 avec Azure AD lors de l’installation](active-directory-azureadjoin-user-frx.md)
* [Configuration d’un appareil Windows 10 avec Azure AD à partir du menu des paramètres](active-directory-azureadjoin-user-upgrade.md)
* [Association d’un appareil Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Activation de BYOD (Apportez votre propre appareil) dans votre organisation pour Windows 10
Vous pouvez configurer vos employés et utilisateurs afin qu’ils utilisent leurs appareils personnels Windows (BYOD) pour accéder aux applications et ressources de l’entreprise. Vos utilisateurs peuvent ajouter leurs comptes Azure AD (professionnels ou scolaires) à un appareil Windows personnel pour accéder aux ressources de manière sécurisée et conforme.

### <a name="prerequisites"></a>Composants requis
**Au niveau de l’entreprise (administrateur)**

* Abonnement Azure AD

**Au niveau de l’utilisateur**

* Windows 10 (éditions Professionnel et Entreprise)

### <a name="administrator-tasks"></a>Tâches de l’administrateur
* [Configuration de l’inscription des appareils](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tâches de l’utilisateur
* [Association d’un appareil Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

