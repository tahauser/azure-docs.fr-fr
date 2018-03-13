---
title: "Détecter les applications cloud non gérées avec Cloud App Discovery dans Azure Active Directory| Microsoft Docs"
description: Fournit une vue d'ensemble de la recherche et de la gestion d'applications avec Cloud App Discovery, ainsi que des informations sur ses avantages et son fonctionnement.
services: active-directory
keywords: "détection d'applications cloud, gestion d'applications"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Détecter les applications cloud non gérées avec Cloud App Discovery
## <a name="summary"></a>Résumé

Cloud App Discovery dans Azure Active Directory offre désormais une découverte sans agent améliorée grâce à Microsoft Cloud App Security. Pour utiliser Cloud App Discovery, connectez-vous simplement avec vos informations d’identification Azure AD Premium P1. Cette mise à jour est disponible sans frais supplémentaires pour tous les clients Azure AD Premium P1. Commencez avec l’article [Configurer Cloud App Discovery dans Azure AD](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started), puis essayez [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> L’expérience Azure AD Cloud App Discovery actuelle basée sur l’agent de découverte est sera désactivée le 5 mars 2018, après quoi les agents seront désactivés et les données supprimées. Veuillez prendre les mesures nécessaires avant le 5 mars pour passer à la nouvelle expérience et éviter toute interruption de service.  
 
**Avec Cloud App Discovery, vous pouvez :**

* détecter les applications cloud utilisées et mesurer l’utilisation par nombre d’utilisateurs, volume de trafic ou nombre de demandes web à l’application ;
* identifier les utilisateurs d’une application ;
* exporter des données pour effectuer une analyse hors ligne ;
* confier le contrôle de ces applications au service informatique et activer l’authentification unique pour la gestion des utilisateurs.

## <a name="how-it-works"></a>Fonctionnement
1. Des agents d'utilisation des applications sont installés sur les ordinateurs de l'utilisateur.
2. Les informations sur l’utilisation des applications capturées par les agents sont envoyées via un canal sécurisé et chiffré au service Cloud App Discovery.
3. Celui-ci évalue les données et génère des rapports.

![Diagramme Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Étapes suivantes
* [Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Paramètres de Registre de Cloud App Discovery pour les services de proxy avec ports personnalisés](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery - Agent Changelog ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

