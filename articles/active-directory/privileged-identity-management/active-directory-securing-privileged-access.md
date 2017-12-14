---
title: "Sécurisation de l’accès privilégié dans Azure AD | Microsoft Docs"
description: "Une rubrique qui décrit les approches de sécurisation de l’accès privilégié dans Azure, Azure Active Directory et les Microsoft Online Services."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: e1bc0f27b14beef91b4deb68dc625d75195445fb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="securing-privileged-access-in-azure-ad"></a>Sécurisation de l’accès privilégié dans Azure AD
La sécurisation de l’accès privilégié est une première étape essentielle pour protéger les ressources d’entreprise dans une organisation moderne. Les comptes privilégiés sont ceux qui administrent et gèrent des systèmes informatiques. Les pirates informatiques ciblent ces comptes pour accéder aux données et aux systèmes d’une organisation. Pour sécuriser l’accès privilégié, vous devez isoler les comptes et les systèmes contre les risques d’exposition à un utilisateur malveillant.

De plus en plus d’utilisateurs obtiennent un accès privilégié par le biais de services cloud. Il peut s’agit d’administrateurs généraux d’Office 365, d’administrateurs d’abonnements Azure et d’utilisateurs qui disposent d’un accès administrateur à des machines virtuelles ou à des applications SaaS.

Microsoft vous recommande de suivre la feuille de route établie dans la rubrique [Sécurisation de l’accès privilégié](https://technet.microsoft.com/library/mt631194.aspx)(en anglais).

Pour les clients utilisant Azure Active Directory, Office 365 ou d’autres services et applications Microsoft, ces principes s’appliquent, que les comptes d’utilisateurs soient gérés et authentifiés par Active Directory ou dans Azure Active Directory. Les sections suivantes fournissent plus d’informations sur les fonctionnalités d’Azure AD pour la prise en charge de la sécurisation de l’accès privilégié.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
Pour renforcer la sécurité de l’authentification de l’administrateur, vous devez exiger une authentification en deux étapes avant d’accorder des privilèges. La vérification en deux étapes est une méthode permettant de vérifier votre identité qui requiert l'utilisation d'autres méthodes que le nom d'utilisateur et le mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions et les transactions de l'utilisateur.

Azure Multi-Factor Authentication (MFA) est une solution de vérification en deux étapes qui contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Elle fournit une authentification renforcée via un éventail d’options de vérification simples, dont :

- appels téléphoniques
- messages texte
- notifications d’application mobile
- codes de vérification d’application mobile
- jetons OATH tiers

Pour une vue d'ensemble du fonctionnement d'Azure Multi-Factor Authentication, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

Pour plus d’informations, consultez [MFA pour Office 365 et MFA pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilèges limités dans le temps
Certaines organisations peuvent estimer qu’elles ont trop d’utilisateurs ayant des rôles à privilèges élevés. Un utilisateur peut avoir été ajouté au rôle pour une activité donnée, comme par exemple l’inscription à un service, et ne pas avoir utilisé ces privilèges fréquemment par la suite.

Pour réduire le temps d’exposition des privilèges et mieux contrôler leur utilisation, limitez les utilisateurs à prendre leurs privilèges uniquement « juste à temps » (JIT) ou attribuez ces rôles pour une durée plus courte avec la garantie que les privilèges seront automatiquement révoqués. Pour Azure Active Directory, Azure Resources (préversion) et Microsoft Online Services, vous pouvez utiliser [Azure AD Privileged Identity Management (PIM)](http://aka.ms/AzurePIM).

![Tableau de bord PIM][2]

## <a name="attack-detection"></a>Détection des attaques
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) offre une vue consolidée des événements à risque et des vulnérabilités potentielles qui affectent les identités de votre organisation. En fonction des événements à risque, Identity Protection calcule le niveau de risque des utilisateurs pour chaque utilisateur, ce qui vous permet de configurer des stratégies basées sur les risques pour protéger automatiquement les identités de votre organisation. Ces stratégies, en plus des autres contrôles d’accès conditionnel fournis par Azure Active Directory et EMS, peuvent automatiquement bloquer l’utilisateur ou suggérer d’autres solutions incluant notamment la réinitialisation de mot de passe et la mise en œuvre l’authentification multifacteur.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Accès conditionnel
Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur, avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.

## <a name="related-articles"></a>Articles connexes
* Activation [d’Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Activation [d’Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Activation [d’Azure AD Identity Protection](../active-directory-identityprotection.md)
* Activation des [contrôles d’accès conditionnel](../active-directory-conditional-access-azure-portal.md)

Pour plus d’informations sur la création d’une feuille de route de sécurité complète, consultez la section concernant les « responsabilités du client et la feuille de route » du document [Microsoft Cloud Security pour Enterprise Architects](http://aka.ms/securecustomer) . Pour plus d’informations sur l’aide que peuvent apporter les services Microsoft sur ces différents points, contactez votre représentant Microsoft ou visitez notre [page de solutions de cyber-sécurité](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
