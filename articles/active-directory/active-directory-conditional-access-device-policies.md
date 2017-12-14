---
title: "Stratégies d’accès conditionnel d’Azure Active Directory basées sur les appareils pour les services Office 365 | Microsoft Docs"
description: "Découvrez comment approvisionner des stratégies d’accès conditionnel basées sur les appareils pour rendre les ressources d’entreprise mieux sécurisées, tout en conservant la conformité des utilisateurs et l’accès aux services."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2b586aee9559b62d4cc8b21ab88ab193e61e7c14
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Stratégies d’accès conditionnel d’Active Directory basées sur les appareils pour les services Office 365

L’accès conditionnel nécessite le fonctionnement de plusieurs éléments. Il implique entre autres un utilisateur authentifié avec une authentification multifacteur, un appareil authentifié et un appareil conforme. Dans cet article, nous mettons l’accent sur les conditions basées sur les appareils que votre organisation peut utiliser pour vous aider à contrôler l’accès aux services Office 365. 

Les utilisateurs de l’entreprise veulent accéder à des services Office 365 comme Exchange et SharePoint Online au travail ou à l’école à partir de leurs appareils personnels. Vous voulez que l’accès soit sécurisé. Vous pouvez approvisionner des stratégies d’accès conditionnel basées sur les appareils pour rendre les ressources d’entreprise mieux sécurisées, tout en accordant un accès aux services pour les utilisateurs qui utilisent des appareils conformes. Vous pouvez définir des stratégies d’accès conditionnel à Office 365 dans le portail d’accès conditionnel de Microsoft Intune.

Azure Active Directory applique les stratégies d’accès conditionnel pour sécuriser l’accès aux services Office 365. Vous pouvez créer une stratégie d’accès conditionnel qui empêche un utilisateur utilisant un appareil non conforme d’accéder à un service Office 365. L’utilisateur doit se conformer aux stratégies d’appareil de l’entreprise avant d’obtenir l’accès au service. Vous pouvez aussi créer une stratégie qui impose aux utilisateurs d’inscrire leurs appareils pour pouvoir accéder à un service Office 365. Les stratégies peuvent être appliquées à tous les utilisateurs d’une organisation ou bien limitées à quelques groupes cibles. Vous pouvez ajouter au fil du temps d’autres groupes cibles à une stratégie.

Un prérequis à l’application des stratégies d’appareil est que les utilisateurs doivent inscrire leurs appareils auprès du service d’inscription des appareils d’Azure AD. Vous pouvez choisir d’activer l’authentification multifacteur pour les appareils qui s’inscrivent auprès du service d’inscription des appareils d’Azure AD. L’authentification multifacteur est recommandée pour le service d’inscription des appareils d’Azure AD. Quand l’authentification multifacteur est activée, les utilisateurs qui inscrivent leurs appareils auprès du service d’inscription des appareils d’Azure AD sont invités à s’authentifier avec un deuxième facteur.

## <a name="how-does-a-conditional-access-policy-work"></a>Fonctionnement d’une stratégie d’accès conditionnel

Quand un utilisateur demande l’accès à un service Office 365 à partir d’une plateforme d’appareil prise en charge, Azure AD authentifie l’utilisateur et l’appareil. Azure AD accorde l’accès au service seulement si l’utilisateur se conforme à la stratégie définie pour le service. Les utilisateurs sur des appareils non inscrits reçoivent des instructions sur la façon de s’inscrire et de devenir conformes pour accéder aux services Office 365 de l’entreprise. Les utilisateurs d’appareils iOS et Android doivent inscrire ces appareils via l’application Portail d’entreprise Intune. Quand un utilisateur inscrit un appareil, celui-ci est inscrit auprès d’Azure AD pour la conformité et la gestion des appareils. Vous devez utiliser le service d’inscription des appareils d’Azure AD avec Microsoft Intune pour activer la gestion des appareils mobiles pour les services Office 365. L’inscription de l’appareil est obligatoire pour accéder aux services Office 365 quand des stratégies d’appareil sont appliquées.

Quand un utilisateur inscrit son appareil, cet appareil devient approuvé. Azure AD donne accès aux applications d’entreprise à l’utilisateur authentifié avec l’authentification unique. Azure AD applique une stratégie d’accès conditionnel pour accorder l’accès à un service non seulement la première fois que l’utilisateur demande l’accès, mais aussi chaque fois que l’utilisateur renouvelle une demande d’accès. L’utilisateur se voit refuser l’accès aux services quand des informations d’identification de connexion sont modifiées, quand l’appareil est perdu ou volé, ou quand les conditions de la stratégie ne sont pas satisfaites au moment de la demande de renouvellement.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Vous devez utiliser le service d’inscription des appareils d’Azure AD pour inscrire des appareils.

Quand des utilisateurs locaux sont sur le point d’être authentifiés, les services AD FS (Active Directory Federation Services) (version 1.0 et ultérieures) sont nécessaires. L’authentification multifacteur pour la jonction d’espace de travail échoue si le fournisseur d’identité ne prend pas en charge l’authentification multifacteur. Par exemple, vous ne pouvez pas utiliser l’authentification multifacteur avec AD FS 2.0. Vérifiez qu’AD FS local fonctionne avec l’authentification multifacteur, et qu’une méthode d’authentification multifacteur valide est en place avant d’activer l’authentification multifacteur pour le service d’inscription des appareils d’Azure AD. Par exemple, AD FS sur Windows Server 2012 R2 a des fonctionnalités d’authentification multifacteur. Vous devez également définir une méthode d’authentification valide supplémentaire (authentification multifacteur) sur le serveur AD FS avant d’activer l’authentification multifacteur pour le service d’inscription des appareils d’Azure AD. Pour plus d’informations sur les méthodes d’authentification multifacteur prises en charge dans AD FS, consultez [Configurer des méthodes d’authentification supplémentaires pour AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Étapes suivantes

*   Pour obtenir des réponses aux questions courantes, consultez [FAQ sur l’accès conditionnel d’Azure Active Directory](active-directory-conditional-faqs.md).
