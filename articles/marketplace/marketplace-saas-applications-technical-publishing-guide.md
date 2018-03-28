---
title: Guide de publication technique de l’application SaaS Place de marché Microsoft Azure
description: Guide étape par étape et listes de contrôle de publication pour la publication des applications SaaS sur la Place de marché Azure
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: 64becc80192e69bd332d6657637c845acf93748b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Guide de publication technique de l’application SaaS

Bienvenue dans le guide de publication technique de l’application SaaS Place de marché Microsoft Azure. Ce guide est conçu pour aider les éditeurs candidats et existants à répertorier leurs applications et services dans la Place de marché Azure en utilisant l’offre des applications SaaS.  
Vous pouvez utiliser l’offre des applications SaaS quand votre solution est déployée dans votre propre abonnement Azure et que les clients se connectent via une interface que vous concevez et gérez pour tester l’application. Pour cela, elle utilise [Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) pour tirer parti de votre environnement d’essai existant. En d’autres termes, il s’agit d’une version d’évaluation gratuite menée par le client et hébergée par un partenaire. Il est essentiel d’exposer votre solution d’une façon qui permette aux acheteurs du cloud de l’essayer de manière indépendante et gratuitement, et c’est pourquoi ce type d’offre propose une expérience d’évaluation correspondant à la manière dont les clients recherchent des solutions cloud.  

Pour obtenir une vue d’ensemble de toutes les autres offres de la Place de marché, reportez-vous au [Guide de publication relatif à la Place de marché](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Guide technique de l’application SaaS
Les exigences techniques pour les applications SaaS sont simples. Les éditeurs doivent uniquement être intégrés à Azure AD pour être publiés.  L’intégration d’Azure AD aux applications est bien documentée et Microsoft fournit plusieurs kits SDK et ressources pour effectuer cette opération.  

Pour démarrer, nous vous recommandons d’avoir un abonnement dédié pour la publication sur la Place de marché Azure, ce qui vous permet d’isoler la tâche des autres initiatives. En outre, s’ils ne sont pas déjà installés, nous vous recommandons d’avoir les outils suivants dans le cadre de votre environnement de développement : 
- [interface de ligne de commande Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-5.0.0)  
- [Outils de développement Azure (voir les outils disponibles)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Ressources
Les listes suivantes fournissent des liens vers les meilleures ressources Azure AD pour vous aider à démarrer : 

**Documentation**

- [Guide du développeur Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

- [Intégration à Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

- [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

- [Feuille de route Azure - Sécurité et identité](https://azure.microsoft.com/roadmap/?category=security-identity)

**Vidéos**

- [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Vidéos Microsoft Azure axées sur Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Formation**  
- [Série de formations sur Microsoft Azure destinées aux professionnels de l’informatique : Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Mises à jour du service Azure Active Directory**  
- [Mises à jour du service Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Pour le support, vous pouvez utiliser les ressources suivantes :
- [Forums MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [Stackoverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Galerie Azure Active Directory
En plus d’avoir votre application répertoriée dans la Place de marché Azure/AppSource, elle peut l’être aussi dans la galerie d’applications Azure AD qui fait partie de l’App Store de la Place de marché Azure. Les clients qui utilisent Azure AD comme fournisseur d’identité peuvent y trouver les connecteurs d’applications SaaS publiés. Les administrateurs informatiques peuvent ajouter des connecteurs à partir de la galerie d’applications, puis configurer et utiliser ces connecteurs pour l’authentification unique et le provisionnement. Azure AD prend en charge tous les principaux protocoles de fédération pour l’authentification unique, notamment SAML 2.0, OpenID Connect, OAuth et WS-Fed.  

Une fois que vous avez testé le bon fonctionnement de l’intégration de votre application dans Azure AD, envoyez votre demande d’accès à notre portail Application Network. Si vous avez un compte Office 365, utilisez-le pour vous connecter au portail. Si vous n’avez pas de compte Office 365, vous pouvez utiliser votre compte Microsoft (comme Outlook ou Hotmail) pour vous connecter.

## <a name="program-benefits"></a>Avantages du programme
- Propose la meilleure expérience d’authentification unique possible aux clients
- Configuration minimale et simple de l’application
- Les clients peuvent rechercher l’application dans la galerie
- Tous les clients peuvent utiliser cette intégration, quelle que soit la référence SKU Azure AD utilisée : Gratuit, De base ou Premium
- Tutoriel de configuration pas à pas pour nos clients mutuels
- Autorise le provisionnement des utilisateurs pour la même application si vous utilisez SCIM

## <a name="prerequisites"></a>Prérequis

Pour répertorier une application dans la galerie Azure AD, l’application doit tout d’abord implémenter l’un des protocoles de fédération pris en charge par Azure AD. Veuillez lire les conditions générales de la galerie d’applications Azure AD qui figurent dans [Informations légales de Microsoft Azure](https://azure.microsoft.com/support/legal/).  

La section suivante décrit d’autres informations sur la configuration requise selon le protocole que vous utilisez :

**OpenID Connect** : créez l’application multilocataire dans Azure AD et implémentez un framework de consentement pour votre application. Envoyez la demande de connexion au point de terminaison commun afin que n’importe quel client puisse donner son consentement à l’application. Vous pouvez contrôler l’accès utilisateur du client en fonction de l’ID de locataire et de l’UPN de l’utilisateur, reçus dans le jeton.  
**SAML 2.0 ou WS-Fed** : votre application doit avoir la possibilité d’effectuer l’intégration de l’authentification unique SAML ou WS-Fed en mode SP ou IdP.
