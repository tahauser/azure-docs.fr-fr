---
title: "Sécurisation des applications mobiles et web PaaS avec Azure App Service | Microsoft Docs"
description: " Découvrez les bonnes pratiques de sécurité d’Azure App Service pour protéger vos applications mobiles et web PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: dec45d91ad1a73306b3e2656dd9bf7fdbe456720
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Sécurisation des applications mobiles et web PaaS avec Azure App Service

Dans cet article, nous abordons un ensemble de bonnes pratiques de sécurité [Azure App Service](https://azure.microsoft.com/services/app-service/) pour protéger vos applications mobiles et web PaaS. Ces bonnes pratiques sont issues de notre expérience d’Azure, mais également de celle des clients, comme vous.

## <a name="azure-app-service"></a>Azure App Service
[Azure App Service](../app-service/app-service-web-overview.md) est une offre PaaS qui vous permet de créer des applications mobiles et web pour tout type d’appareil ou de plateforme, et de vous connecter à des données en tout lieu, dans le cloud ou localement. App Service inclut les fonctionnalités web et mobiles qui étaient précédemment fournies séparément dans Sites Web Azure et Azure Mobile Services. Il inclut également de nouvelles fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud. App Service est un service intégré unique qui apporte un ensemble complet de fonctionnalités pour les scénarios web, mobiles et d’intégration.

## <a name="best-practices"></a>Meilleures pratiques

Quand vous utilisez App Service, respectez les bonnes pratiques suivantes :

- [S’authentifier par le biais d’Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). App Service fournit un service OAuth 2.0 pour votre fournisseur d’identité. OAuth 2.0 privilégie la simplicité du développement client tout en fournissant des flux d’autorisation spécifiques pour les applications web, les applications de bureau et les téléphones mobiles. Azure AD utilise OAuth 2.0 pour vous permettre d’autoriser l’accès aux applications mobiles et web.
- Restreindre l’accès en fonction des principes du besoin de connaître et du privilège minimum. La restriction de l’accès est indispensable pour les organisations qui veulent appliquer des stratégies de sécurité portant sur l’accès aux données. La fonction de contrôle d’accès en fonction du rôle (RBAC) peut être utilisée pour affecter des autorisations aux utilisateurs, groupes et applications à une certaine étendue. Pour en savoir plus sur l’octroi aux utilisateurs du droit d’accès aux applications, consultez [Prise en main de la gestion des accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md).
- Protégez vos clés. Si vous perdez vos clés d’abonnement, qu’importe la qualité de votre stratégie de sécurité ! Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Key Vault, vous pouvez chiffrer les clés et les secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM. Pour en savoir plus, consultez [Azure Key Vault](../key-vault/key-vault-whatis.md). Vous pouvez également utiliser Key Vault pour gérer vos certificats TLS avec renouvellement automatique.
- Limitez les adresses IP source entrantes. L’[environnement App Service](../app-service/environment/intro.md) propose une fonctionnalité d’intégration de réseau virtuel qui vous permet de limiter les adresses IP sources entrantes par le biais de groupes de sécurité réseau (NSG). Si vous n’êtes pas familiarisé avec les réseaux Azure Virtual Network, cette fonctionnalité vous permet de placer la plupart de vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Pour en savoir plus, consultez [Intégrer une application à un réseau Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté un ensemble de bonnes pratiques de sécurité App Service pour protéger vos applications mobiles et web PaaS. Pour en savoir plus sur la sécurisation de vos déploiements PaaS, consultez :

- [Sécurisation des déploiements PaaS](security-paas-deployments.md)
- [Sécurisation des applications mobiles et web PaaS à l’aide d’Azure SQL Database et de SQL Data Warehouse](security-paas-applications-using-sql.md)
