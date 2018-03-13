---
title: "Versions Azure MFA et plans de consommation | Microsoft Docs"
description: "Informations sur le client Multi-Factor Authentication (MFA) et les différentes méthodes et versions disponibles. Détails sur chaque plan de consommation"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: 67456fa865a7bc1057194d577cd79ce6378a7ac9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Comment obtenir Azure Multi-Factor Authentication ?

Lorsqu’il s’agit de protéger vos comptes, la vérification en deux étapes doit être la norme dans votre organisation. Cette fonctionnalité est particulièrement importante pour les comptes administratifs qui disposent d’un accès privilégié aux ressources. Pour cette raison, Microsoft propose des fonctionnalités de vérification en deux étapes de base aux administrateurs Office 365 et Azure sans surcoût. Si vous souhaitez mettre à niveau les fonctionnalités de vos administrateurs ou étendre la vérification en deux étapes au reste de vos utilisateurs, vous pouvez acheter Azure Multi-Factor Authentication. 

Cet article explique la différence entre les versions offertes aux administrateurs et la version complète d’Azure MFA. Si vous êtes prêt à déployer l’offre Azure MFA complète, la section ultérieure traite des options d’implémentation et de la façon dont Microsoft calcule la consommation.


>[!IMPORTANT]
>Cet article est destiné à vous guider pour vous aider à comprendre les différentes manières d’acheter Azure Multi-Factor Authentication. Pour obtenir des détails spécifiques sur la tarification et la facturation, vous devez toujours consulter la [page de tarification Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versions disponibles d’Azure Multi-Factor Authentication

Le tableau suivant décrit les différences entre trois versions de l’authentification multifacteur :

| Version | Description |
| --- | --- |
| Authentification multifacteur pour Office 365 |Cette version fonctionne exclusivement avec les applications Office 365 et est gérée à partir du portail Office 365. Les administrateurs peuvent [sécuriser les ressources Office 365 avec la vérification en deux étapes](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Cette version est fournie dans le cadre d’un abonnement à Office 365. |
| Authentification multifacteur pour administrateurs Azure AD | Les utilisateurs avec le rôle d’administrateur général dans les locataires Azure AD peuvent activer la vérification en deux étapes pour leurs comptes d’administrateurs généraux Azure AD sans coût supplémentaire.|
| Azure Multi-Factor Authentication | Souvent désigné comme version « complète », Azure Multi-Factor Authentication offre un riche éventail de fonctionnalités. Il fournit des options de configuration supplémentaires via le [portail Azure](https://portal.azure.com), des fonctions de rapports avancées et la prise en charge d’une sélection d’applications locales et dans le cloud. Azure Multi-Factor Authentication est inclus dans les [plans Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) et les plans [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing), et peut être déployé localement ou dans le cloud. |

## <a name="feature-comparison-of-versions"></a>Comparaison des fonctionnalités suivant les versions
Le tableau suivant fournit la liste des fonctionnalités qui sont disponibles dans les différentes versions d’Azure Multi-Factor Authentication.

> [!NOTE]
> Ce tableau de comparaison répertorie les fonctionnalités de chaque version de Multi-Factor Authentication. Si vous disposez du service Azure Multi-Factor Authentication complet, certaines fonctionnalités peuvent être disponibles ou non, selon que vous utilisez [MFA dans le cloud ou localement](multi-factor-authentication-get-started.md).


| Fonctionnalité | Authentification multifacteur pour Office 365 | Authentification multifacteur pour administrateurs Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Protection des comptes Administrateur Azure AD avec MFA |● |● (Comptes d’administrateurs généraux Azure AD uniquement) |● |
| Application mobile comme second facteur |● |● |● |
| Appel téléphonique comme second facteur |● |● |● |
| SMS comme second facteur |● |● |● |
| Mots de passe d'application pour les clients qui ne prennent pas en charge MFA |● |● |● |
| Contrôle d’administration sur les méthodes de vérification |● |● |● |
| Protection des comptes non administrateurs avec MFA |● (Uniquement pour les applications Office 365) | |● |
| Mode du code PIN | | |● |
| Alerte de fraude | | |● |
| Rapports MFA | | |● |
| Contournement à usage unique | | |● |
| Messages de bienvenue personnalisés pour les appels téléphoniques | | |● |
| ID d’appelant personnalisé pour les appels téléphoniques | | |● |
| Adresses IP approuvées | | |● |
| Mémoriser MFA pour les appareils fiables |● |● |● |
| SDK MFA | | |● (Déprécié) | 
| MFA pour les applications locales | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Comment activer Azure Multi-Factor Authentication pour les administrateurs Azure AD
Les utilisateurs avec le rôle d’administrateur général dans les locataires Azure AD peuvent activer la vérification en deux étapes pour leurs comptes d’administrateurs généraux Azure AD sans coût supplémentaire. Si vous utilisez un compte Microsoft, vous pouvez vous inscrire pour l’authentification multifacteur à [cette adresse](https://support.microsoft.com/en-us/help/12408/microsoft-account-about-two-step-verification). Si vous n’utilisez pas de comptes Microsoft, activez l’authentification multifacteur pour les administrateurs généraux à [cette adresse](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).

## <a name="how-to-get-azure-multi-factor-authentication"></a>Comment obtenir Azure Multi-Factor Authentication ?
Si vous souhaitez bénéficier de toutes les fonctionnalités offertes par Azure Multi-Factor Authentication, plusieurs options s’offrent à vous :

### <a name="option-1---mfa-licenses"></a>Option 1 : licences MFA

Achetez des licences Azure Multi-Factor Authentication et attribuez-les à vos utilisateurs dans Azure Active Directory. 

Si vous utilisez cette option, créez uniquement un fournisseur Azure Multi-Factor Authentication si vous devez fournir la vérification en deux étapes pour certains utilisateurs dépourvus de licences. Sinon, vous risquez d’être facturé à deux reprises.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Option 2 : licences regroupées qui incluent MFA

Achetez des licences qui incluent Azure Multi-Factor Authentication, comme Azure Active Directory Premium ou Enterprise Mobility + Security, puis attribuez-les à vos utilisateurs dans Azure Active Directory. 

Si vous utilisez cette option, vous devez créer un fournisseur Azure Multi-Factor Authentication uniquement si vous devez également fournir la vérification en deux étapes pour certains utilisateurs dépourvus de licences. Sinon, vous risquez d’être facturé à deux reprises. 

### <a name="option-3---mfa-consumption-based-model"></a>Option 3 : modèle basé sur la consommation MFA

Créez un fournisseur Azure Multi-Factor Authentication dans un abonnement Azure. Les fournisseurs Azure MFA sont des ressources Azure qui sont facturées sur votre Accord Entreprise, votre engagement monétaire Azure ou votre carte de crédit comme toutes les autres ressources Azure. Ces fournisseurs ne peuvent être créés que dans les abonnements Azure complets, et non dans les abonnements Azure pour lesquels une limite de dépense de 0 $ est définie. Des abonnements limités sont créés lorsque vous activez des licences, comme dans les options 1 et 2. 

Quand vous utilisez un fournisseur Azure Multi-Factor Authentication, vous avez le choix entre deux modèles d’utilisation qui sont facturés dans le cadre de votre abonnement Azure :  

1. **Par utilisateur activé** : pour les entreprises qui souhaitent activer la vérification en deux étapes sur un nombre fixe d’employés qui s’authentifient régulièrement. La facturation par utilisateur est basée sur le nombre d’utilisateurs activés pour MFA dans votre client Azure AD et sur votre serveur Azure MFA. Si les utilisateurs sont activés pour MFA à la fois dans Azure AD et sur le serveur Azure MFA, et si la synchronisation de domaines (Azure AD Connect) est activée, le plus grand ensemble d’utilisateurs est alors pris en compte. Si la synchronisation de domaines n’est pas activée, nous comptons la somme de tous les utilisateurs activés pour MFA dans Azure AD et sur le serveur Azure MFA. La facturation est calculée au prorata et consignée quotidiennement dans le système Commerce. 

  > [!NOTE]
  > Exemple de facturation 1 : 5 000 utilisateurs sont activés pour MFA à la date du jour. Le système MFA divise ce nombre par 31 et indique 161,29 utilisateurs pour ce jour. Le lendemain, vous activez 15 utilisateurs supplémentaires. Le système MFA indique alors 161,77 utilisateurs pour ce jour. À la fin du cycle de facturation, le nombre total d’utilisateurs facturés sur votre abonnement Azure se monte à environ 5 000. 
  >
  > Exemple de facturation 2 : certains de vos utilisateurs disposent de licences tandis que d’autres en sont dépourvus. Vous disposez donc d’un fournisseur Azure MFA par utilisateur pour compenser la différence. Il existe 4 500 licences Enterprise Mobility + Security sur votre client, mais 5 000 utilisateurs sont activés pour MFA. Votre abonnement Azure est facturé pour 500 utilisateurs, calculé au prorata et indiqué quotidiennement sous la forme de 16,13 utilisateurs. 

2. **Par authentification** : pour les entreprises qui souhaitent activer la vérification en deux étapes pour un nombre important d’utilisateurs qui s’authentifient ponctuellement. La facturation est basée sur le nombre de demandes de vérification en deux étapes, que ces vérifications réussissent ou soient refusées. Cette facturation apparaît sur votre relevé d’utilisation Azure dans des packs de 10 authentifications. Elle est consignée quotidiennement. 

  > [!NOTE]
  > Exemple de facturation 3 : aujourd’hui, le service Azure MFA a reçu 3 105 demandes de vérification en deux étapes. Votre abonnement Azure est facturé pour 310,5 packs d’authentification. 

Il est important de noter que vous pouvez posséder des licences Azure MFA, mais être toujours facturé en vertu d’une configuration basée sur la consommation. Si vous configurez un fournisseur Azure MFA par authentification, vous êtes facturé pour toutes les demandes de vérification en deux étapes, y compris pour celles qui ont été effectuées par les utilisateurs disposant de licences. Si vous configurez un fournisseur Azure MFA par utilisateur sur un domaine qui n’est pas lié à votre client Azure AD, vous êtes facturé par utilisateur activé même si vos utilisateurs possèdent des licences sur Azure AD. 

## <a name="next-steps"></a>étapes suivantes

- Pour plus d’informations sur la tarification, consultez la [tarification d’Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Choisissez de déployer Azure MFA [dans le cloud ou en local](multi-factor-authentication-get-started.md)
