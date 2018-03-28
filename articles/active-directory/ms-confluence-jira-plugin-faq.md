---
title: Questions fréquentes (FAQ) sur le plug-in d’authentification unique Microsoft Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et l’authentification unique Microsoft Azure Active Directory pour JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Questions fréquentes (FAQ) sur le plug-in d’authentification unique Microsoft Azure Active Directory 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Qu’est-ce que le module complémentaire d’authentification unique Microsoft ?

Ce module complémentaire fournit une fonctionnalité d’authentification unique pour les logiciels locaux JIRA (notamment JIRA Core, JIRA Software et JIRA Service Desk) et Confluence d’Atlassian. Le module complémentaire fonctionne avec Azure AD en tant que fournisseur d’identité.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. Avec quels produits de la société Atlassian le module complémentaire fonctionne-t-il ?

À ce jour, le module complémentaire fonctionne avec les versions locales de JIRA et Confluence.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Ce module complémentaire fonctionne-t-il sur la version cloud ?

Non. Seules les versions locales de JIRA et Confluence sont prises en charge.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. Quelles sont les versions de JIRA et Confluence prises en charge ?

Voici la liste des versions prises en charge :

* JIRA Core et Software : 6.0 à 7.2.2 
* JIRA Service Desk : 3.0 à 3.2 
* Confluence : 5.0 à 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. Ce module complémentaire est-il gratuit ou payant ?

Il s’agit d’un module complémentaire gratuit, que vous pouvez installer à partir de la Place de marché Atlassian.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. Ai-je besoin de redémarrer JIRA/Confluence après avoir déployé le module complémentaire ?

Le redémarrage n’est pas nécessaire après le déploiement du module complémentaire. Vous pouvez commencer à utiliser le module complémentaire immédiatement après le déploiement.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Comment obtenir du support pour le module complémentaire ?

Écrivez-nous à l’adresse : <email>. Nous vous répondrons sous <> heures. Vous pouvez également créer un ticket de support auprès de Microsoft par le biais du portail Azure. Vous pouvez également nous appeler au : <Number> entre <> et <> en semaine.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. Ce module complémentaire fonctionne-t-il avec une installation de JIRA et Confluence sur Mac ou Ubuntu ?

Nous avons testé ce module complémentaire uniquement sur des installations de JIRA et Confluence sur des serveurs Windows 64 bits.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Ce module complémentaire fonctionne-t-il avec des fournisseurs d’identités autres qu’Azure AD ?

Non. Ce module complémentaire fonctionne uniquement avec Azure AD.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. Avec quelle version de SAML le module complémentaire fonctionne-t-il ?

Ce module complémentaire fonctionne avec SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. Le module complémentaire utilise-t-il également le provisionnement ?

Non. À ce jour, le module complémentaire fournit uniquement l’authentification unique basée sur SAML 2.0. L’utilisateur doit être provisionné dans l’application avant la connexion avec authentification unique.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. Les versions cluster de JIRA et Confluence sont-elles prises en charge par le module complémentaire ?

Non. Le module complémentaire fonctionne avec les versions locales de JIRA et Confluence.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. Ce plug-in fonctionne-t-il avec la version HTTP de JIRA et Confluence ?

Non. Le module complémentaire fonctionne uniquement avec les installations HTTPS.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. Dois-je acheter des licences pour ce module complémentaire ?

Il s’agit d’un module complémentaire gratuit.