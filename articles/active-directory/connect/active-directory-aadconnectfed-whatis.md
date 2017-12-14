---
title: "Fédération avec Azure AD Connect | Microsoft Docs"
description: "Cette page centralise toute la documentation portant sur les opérations AD FS avec Azure AD Connect"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: mtillman
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: anandy
ms.openlocfilehash: 04516e38e72405ca797a0d748d9ed825ae452966
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-and-federation"></a>Fédération avec Azure AD Connect
Azure Active Directory (Azure AD) Connect vous permet de configurer la fédération avec Active Directory Federation Services (AD FS) et Azure AD au niveau local. Avec l’authentification de fédération, vous pouvez autoriser les utilisateurs à se connecter aux services Azure AD avec leurs mots de passe locaux sans avoir à les saisir de nouveau, et ce, alors qu’ils sont sur le réseau d’entreprise. En utilisant l’option de fédération AD FS, vous pouvez déployer un nouveau service AD FS ou spécifier une installation existante dans une batterie de serveurs Windows Server 2012 R2.

Cette rubrique présente les fonctions associées à la fédération pour Azure AD Connect. Elle répertorie les liens vers toutes les autres rubriques qui lui sont associées. Pour consulter les liens vers Azure AD Connect, voir [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect : rubriques sur la fédération
| Rubrique | Sujet qu’elle aborde et quand la consulter |
|:--- |:--- |
| **Options de connexion de l’utilisateur via Azure AD Connect** | |
| [Présentation des options de connexion de l’utilisateur](active-directory-aadconnect-user-signin.md) |Découvrez les options de connexion de l’utilisateur et leur impact sur l’expérience de connexion à Azure. |
| **Installation d’AD FS avec Azure AD Connect** | |
| [Configuration requise](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Consultez les conditions préalables à l’installation d’AD FS via Azure AD Connect |
| [Configurer une batterie de serveurs AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Installation d’une nouvelle batterie de serveurs AD FS avec Azure AD Connect |
| [Fédérer avec Azure AD à l’aide d’un ID de connexion de substitution](active-directory-aadconnect-federation-management.md#alternateid) | Configurer la fédération à l’aide d’un ID de connexion de substitution  |
| **Modifier la configuration AD FS** | |
| [Réparation de l’approbation](active-directory-aadconnect-federation-management.md#repairthetrust) |Rétablissement de l’approbation actuelle entre AD FS et Office 365/Azure au niveau local. |
| [Ajouter un nouveau serveur AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Extension d’une batterie de serveurs AD FS avec l’ajout d’un serveur AD FS après l’installation initiale |
| [Ajouter un nouveau serveur de proxy d’application web AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Extension d’une batterie de serveurs AD FS avec l’ajout d’un serveur proxy d’application web après l’installation initiale. |
| [Ajouter un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#addfeddomain) |Ajoutez un domaine à fédérer avec Azure AD. |
| [Mettre à jour le certificat SSL](active-directory-aadconnectfed-ssl-update.md)| Mettez à jour le certificat SSL d’une batterie de serveurs AD FS. |
| [Renouvellement des certificats de fédération pour Office 365 et Azure AD](active-directory-aadconnect-o365-certs.md)|Renouvelez votre certificat O365 avec Azure AD.|
| **Autre configuration de fédération** | |
| [Fédérer plusieurs instances d’Azure AD avec une seule instance d’AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Fédérer plusieurs instances d’Azure AD avec une seule batterie de serveurs AD FS| 
| [Ajouter un logo/une illustration personnalisée de la société](active-directory-aadconnect-federation-management.md#customlogo) |Modifiez l’expérience de connexion en spécifiant le logo personnalisé qui apparaît sur la page de connexion AD FS. |
| [Ajout d’une description de connexion](active-directory-aadconnect-federation-management.md#addsignindescription) |Modifiez la description de la connexion sur la page de connexion AD FS. |
| [Modification des règles de revendication AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modifiez ou ajoutez des règles de revendication dans AD FS pour configurer la synchronisation Azure AD Connect. |


## <a name="additional-resources"></a>Ressources supplémentaires
* [Fédérer deux Azure AD avec un seul AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Déploiement des services AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)
* [Déploiement des services AD FS haute disponibilité par-delà les frontières dans Azure avec Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
