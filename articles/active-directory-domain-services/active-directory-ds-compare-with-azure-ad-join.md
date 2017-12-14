---
title: "Comparer la jonction à Azure AD avec Azure Active Directory Domain Services | Microsoft Docs"
description: Choix entre la jonction Azure AD et Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: fb28c7a66d26119444dd720c2e134f2818d46cfc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Choisir entre la jonction Azure Active Directory et Azure Active Directory Domain Services
Cet article explique les différences qui existent entre la jonction Azure Active Directory (AD) et Azure AD Domain Services, afin de vous aider à choisir en fonction de vos cas d’usage.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Appareils inscrits à Azure AD et appareils joints à Azure AD
Azure AD vous permet de gérer l’identité des appareils utilisés par votre organisation et de contrôler l’accès aux ressources de l’entreprise depuis ces appareils. Les utilisateurs peuvent inscrire leur appareil personnel (bring-your-own) sur Azure AD, ce qui fournit une identité à l’appareil. Par la suite, Azure AD peut authentifier l’appareil lorsqu’un utilisateur se connecte à Azure AD et utilise cet appareil pour accéder aux ressources sécurisées. De plus, vous pouvez gérer l’appareil à l’aide de logiciels de gestion des appareils mobiles (MDM), tels que Microsoft Intune. Cette fonctionnalité vous permet de restreindre l’accès aux ressources sensibles à partir d’appareils gérés et conformes aux stratégies.

Vous pouvez également joindre à Azure AD des appareils appartenant à l’organisation. Ce mécanisme offre les mêmes avantages que l’inscription d’un appareil personnel à Azure AD. Et les utilisateurs peuvent également se connecter à l’appareil avec leurs informations d’identification professionnelles. Les appareils joints à Azure AD vous offrent les avantages suivants :
* Authentification unique (SSO) pour les applications sécurisées par Azure AD
* Itinérance compatible avec la stratégie de l’entreprise pour les paramètres utilisateur sur les appareils.
* Accès au Windows Store pour Entreprises avec vos informations d’identification professionnelles.
* Windows Hello Entreprise
* Accès restreint aux applications et aux ressources sur les appareils conformes à la stratégie d’entreprise.

| **Type d’appareil** | **Plateformes d’appareils** | **Mécanisme** |
|:---| --- | --- |
| Appareils personnels | Windows 10, iOS, Android, Mac OS | Appareils inscrits sur Azure AD |
| Appareil appartenant à l’organisation et non joint à Active Directory local | Windows 10 | Appareil joints Azure AD |
| Appareil appartenant à l’organisation et joint à Active Directory local | Windows 10 | Appareils joints Azure AD hybrides |

Sur un appareil inscrit ou joint à Azure AD, l’authentification de l’utilisateur s’effectue par le biais des protocoles modernes OAuth/OpenID Connect. Ces protocoles sont conçus pour fonctionner sur internet et sont très utiles dans les scénarios de mobilité, où les utilisateurs accèdent aux ressources d’entreprise depuis n’importe quel endroit.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Jonction de domaine pour les domaines managés Azure AD Domain Services
Azure AD Domain Services fournit un domaine Active Directory managé dans un réseau virtuel Azure. À l’aide de mécanismes de jonction de domaine traditionnels, vous pouvez joindre des machines à ce domaine managé. Les machines Windows Server et clientes Windows (Windows 7, Windows 10) peuvent être jointes au domaine managé. De plus, vous pouvez aussi joindre des machines Linux et Mac OS au domaine managé. Lorsque vous joignez une machine à un domaine AD, les utilisateurs peuvent se connecter à la machine avec leurs informations d’identification professionnelles. Ces machines peuvent être managées à l’aide de la stratégie de groupe, et ainsi se mettre en conformité par rapport aux stratégies de sécurité de votre organisation.

Sur une machine jointe au domaine, l’authentification de l’utilisateur s’effectue avec les protocoles d’authentification NTLM ou Kerberos. Pour que l’authentification utilisateur fonctionne, la machine jointe au domaine a besoin d’une visibilité sur les contrôleurs de domaine du domaine managé. Elle doit donc se trouver sur le même réseau virtuel que le domaine managé. Sinon, la machine jointe au domaine doit être connectée au domaine managé sur un réseau virtuel apparié ou via une connexion ExpressRoute/VPN de site à site. Ce mécanisme n’est donc pas idéal pour les appareils mobiles ni pour se connecter aux ressources depuis l’extérieur du réseau d’entreprise.

> [!NOTE]
> Techniquement, il est possible de joindre une station de travail cliente locale au domaine managé via une connexion ExpressRoute ou VPN de site à site. Toutefois, pour les utilisateurs finaux, nous vous recommandons vivement d’utiliser l’inscription de leur appareil à Azure AD (appareil personnel) ou la jonction de leur appareil à Azure AD (appareil de l’entreprise). Ce mécanisme fonctionne mieux sur Internet et permet aux utilisateurs de travailler où qu’ils soient. Azure AD Domain Services se révèle parfait pour les machines virtuelles Windows ou Linux Server déployées sur vos réseaux virtuels Azure, là où vos applications sont déployées.


## <a name="summary---key-differences"></a>Résumé - Différences fondamentales
| **Aspect** | **Azure AD Join** | **Services de domaine Azure AD** |
|:---| --- | --- |
| Appareil contrôlé par | Azure AD | Domaine managé par Azure AD Domain Services |
| Représentation dans l’annuaire | Objets appareil dans l’annuaire Azure AD. | Objets ordinateur dans le domaine managé AAD-DS. |
| Authentification | Protocoles OAuth/OpenID Connect | Protocoles Kerberos, NTLM |
| Gestion | Logiciels de gestion des appareils mobiles (GAM) tels qu’Intune | Stratégie de groupe |
| Mise en réseau | Fonctionne sur Internet | Exige que les machines soient sur le même réseau virtuel que le domaine managé.|
| Idéal pour... | Appareils mobiles ou de bureau des utilisateurs finaux | Machines virtuelles en serveur déployées sur Azure |


## <a name="next-steps"></a>Étapes suivantes
### <a name="learn-more-about-azure-ad-domain-services"></a>En savoir plus sur Azure AD Domain Services
* [Vue d’ensemble d’Azure AD Domain Services](active-directory-ds-overview.md)
* [Caractéristiques](active-directory-ds-features.md)
* [Scénarios de déploiement](active-directory-ds-scenarios.md)
* [Déterminer si la solution Azure AD Domain Services répond aux besoins de vos cas d’usage](active-directory-ds-comparison.md)
* [Comprendre comment la solution Azure AD Domain Services est synchronisée avec votre annuaire Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>En savoir plus sur la jonction à Azure AD
* [Présentation de la gestion des appareils dans Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Prise en main des services de domaine Azure AD
* [Activer Azure AD Domain Services à l’aide du portail Azure](active-directory-ds-getting-started.md)
