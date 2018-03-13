---
title: "Architecture d’identité pour Azure Stack | Microsoft Docs"
description: "En savoir plus sur l’architecture d’identité que vous pouvez utiliser avec Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/28/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 7f2ec78da38f3c97fde810fb8fc965cfbb6fda08
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Architecture d’identité pour Azure Stack
Avant de choisir un fournisseur d’identité à utiliser avec Azure Stack, vous devez comprendre les différences importantes entre les options d’Azure Active Directory (Azure AD) et Azure Directory Federated Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Capacités et limitations 
Le fournisseur d’identité que vous choisissez peut limiter vos options, dont la prise en charge d’architecture mutualisée. 

  

|Capacité ou scénario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Connecté à Internet     |OUI       |Facultatif|
|Prise en charge d’architecture mutualisée     |OUI       |Non       |
|Syndication de Place de marché       |OUI       |Oui - Nécessite l’utilisation de l’outil [Syndication de Place de marché hors ligne](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity).|
|Prise en charge de la bibliothèque d’authentification Active Directory (ADAL) |OUI |OUI|
|Prise en charge d’outils tels qu’Azure CLI, Visual Studio (VS) et PowerShell  |OUI |OUI|
|Créer des principaux de service via le portail     |OUI |Non |
|Créer des principaux de service avec des certificats      |OUI |OUI|
|Créer des principaux de service avec des secrets (clés)    |OUI |Non |
|Les applications peuvent utiliser le service Graph           |OUI |Non |
|Les applications peuvent utiliser le fournisseur d’identité pour se connecter |OUI |Oui - Les applications doivent fédérer avec votre service AD FS local. |

## <a name="topologies"></a>Topologies
Les sections suivantes fournissent des informations sur les topologies d’identité que vous pouvez utiliser.

### <a name="azure-ad--single-tenant"></a>Azure AD - Client unique 
Par défaut, lorsque vous installez Azure Stack et utilisez Azure AD, Azure Stack utilise une topologie de client unique. 

Une topologie de client unique est utile quand :
- Tous les utilisateurs sont compris dans le même client.
- Un fournisseur de services héberge une instance Azure Stack pour une organisation.  

![Topologie Azure Stack utilisant une topologie de client unique avec Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Avec cette topologie :
- Azure Stack enregistre toutes les applications et services dans le même répertoire du client Azure AD. 
- Azure Stack n’authentifie que les utilisateurs et applications de ce répertoire, jetons compris. 
- Les identités pour administrateurs (opérateurs cloud) et les utilisateurs client sont dans le même répertoire de client. 
- Pour permettre à un utilisateur d’un autre répertoire d’accéder à cet environnement Azure Stack, vous devez [inviter l’utilisateur en tant qu’invité](azure-stack-identity-overview.md#guest-users) au répertoire de client.  

### <a name="azure-ad--multi-tenant"></a>Azure AD - architecture mutualisée
Les opérateurs cloud peuvent configurer Azure Stack pour autoriser l’accès aux applications par les clients d’une ou plusieurs organisations. Les utilisateurs accèdent aux applications via le portail utilisateur. Dans cette configuration, le portail administrateur (dont se sert l’opérateur cloud) est limité aux utilisateurs d’un répertoire unique. 

Une topologie d’architecture mutualisée est utile quand :
- Un fournisseur de services souhaite autoriser des utilisateurs de plusieurs organisations à accéder à Azure Stack.

![Topologie Azure Stack utilisant une topologie d’architecture mutualisée avec Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Avec cette topologie :
- L’accès aux ressources doit se faire pour chaque organisation. 
- Les utilisateurs d’une organisation ne devraient pas être capables d’autoriser l’accéder aux ressources à des utilisateurs qui ne font pas partie de leur organisation.  
- Les identités pour administrateurs (opérateurs cloud) peuvent se trouver dans des répertoires de client différents des identités pour utilisateurs. Cette séparation offre une isolation de compte au niveau du fournisseur d’identité. 
 
### <a name="ad-fs"></a>AD FS  
La topologie AD FS est nécessaire lorsqu’une des conditions suivantes s’applique :
- Azure Stack ne se connecte pas à Internet.
- Azure Stack peut se connecter à Internet, mais vous choisissez d’utiliser AD FS comme fournisseur d’identité.
  
![Topologie Azure Stack utilisant AD FS](media/azure-stack-identity-architecture/adfs.png)

Avec cette topologie :
- Pour prendre en charge l’utilisation en production, vous devez intégrer l’instance AD FS Azure Stack dans une instance AD FS existante sauvegardée dans Active Directory (AD), via une approbation de fédération. 
- Vous pouvez intégrer le service Graph dans Azure Stack avec le Active Directory existant.  Vous pouvez aussi utiliser le service API Graph basé sur OData qui prend en charge les API cohérentes avec l’API Graph Azure AD.  

  Pour interagir avec AD, l’API Graph nécessite des informations d’identification utilisateur d’AD pouvant seulement lire AD. 
  - Le service AD FS intégré est basé sur Server 2016. 
  - Vos services AD FS et AD doivent être basés sur Server 2012 ou une version ultérieure.  
  
  Entre vos services AD et ADFS intégré, les interactions ne sont pas limitées à OpenID Connect, et peuvent utiliser n’importe quel protocole mutuel pris en charge.  
  - Les comptes utilisateurs sont créés et gérés dans votre service AD local.
  - Les principaux de service et les inscriptions d’applications sont gérés dans le service AD intégré.



## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de l’identité](azure-stack-identity-overview.md)   
- [Intégration au centre de données - Identité](azure-stack-integrate-identity.md)