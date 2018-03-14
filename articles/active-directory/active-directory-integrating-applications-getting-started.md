---
title: "Prise en main de l’intégration d’Azure AD avec les applications | Microsoft Docs"
description: "Cet article est un guide de prise en main de l’intégration d’Azure Active Directory (AD) avec les applications locales et les applications cloud."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: db6d210d-c970-49e9-bd20-36d984bcd1c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: bf8ef28b49a3a12772965e7ebc85539c1a86e10f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guide de prise en main de l’intégration d’Azure Active Directory avec les applications
## <a name="overview"></a>Vue d’ensemble
Cette rubrique est conçue pour vous donner une feuille de route pour l’intégration des applications avec Azure Active Directory (AD). Chacune des sections ci-dessous contient un résumé d’une rubrique plus détaillée. Ainsi, vous pouvez identifier les parties de ce guide de prise en main qui vous intéressent.  Suivez les liens pour obtenir de plus amples informations sur chaque sujet.

## <a name="before-you-begin-take-inventory"></a>Inventaire avant de commencer
Avant de vous lancer dans l’intégration des applications avec Azure AD, il est important de savoir où vous êtes et où vous souhaitez aller.  Les questions suivantes sont destinées à vous aider à réfléchir à votre projet d’intégration des applications dans Azure AD.

### <a name="application-inventory"></a>Inventaire des applications
* Où sont toutes vos applications ? Qui en sont les propriétaires ?
* Quel genre d’authentification vos applications nécessitent-elles ?
* Qui a besoin d’accéder à quelles applications ?
* Souhaitez-vous déployer une nouvelle application ?
  * Allez-vous la générer en interne et la déployer sur une instance de calcul Azure ?
  * Allez-vous utiliser une application disponible dans la galerie d’applications Azure ?

### <a name="user-and-group-inventory"></a>Inventaire des utilisateurs et des groupes
* Où vos comptes d’utilisateurs résident-ils ?
  * Active Directory local
  * Azure AD
  * Dans une base de données d’applications distincte que vous possédez
  * Dans des applications non approuvées
  * Toutes les options ci-dessus
* De quelles autorisations et affectations de rôle les utilisateurs individuels disposent-ils actuellement ? Devez-vous examiner leur accès ou êtes-vous sûr que l’accès et les affectations de rôle de vos utilisateurs sont appropriés à l’heure actuelle ?
* Les groupes sont-ils déjà établis dans votre Active Directory local ?
  * Comment vos groupes sont-ils organisés ?
  * Qui sont les membres du groupe ?
  * De quelles autorisations/affectations de rôle les groupes disposent-ils actuellement ?
* Devrez-vous nettoyer les bases de données d’utilisateurs/de groupes avant l’intégration ?  (Cette question est très importante. Si les données sont inexactes, les résultats seront erronés.)

### <a name="access-management-inventory"></a>Inventaire de gestion de l’accès
* Comment gérez-vous actuellement l’accès des utilisateurs aux applications ? Cette situation doit-elle être modifiée ?  Avez-vous envisagé d’autres méthodes de gestion de l’accès, par exemple [RBAC](role-based-access-control-configure.md) ?
* Qui doit accéder à quoi ?

Vous n’avez peut-être pas les réponses à toutes ces questions à l’avance mais ce n’est pas grave.  Ce guide peut vous aider à répondre à certaines de ces questions et à prendre des décisions en connaissance de cause.

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure et un répertoire Azure Active Directory.  Si vous ne disposez pas d’un abonnement Azure, vous pouvez essayer Azure gratuitement pendant 30 jours. [Faites un essai.](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Intégration des applications avec Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Détection des applications cloud non approuvées avec Cloud App Discovery
Comme mentionné ci-dessus, il existe peut-être des applications qui n’ont pas été gérées par votre entreprise jusqu’à présent.  Dans le cadre du processus d’inventaire, il est possible de rechercher les applications cloud non approuvées. Consultez [Détection des applications cloud non approuvées avec Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Types d’authentification
Chacune de vos applications peut présenter des exigences d’authentification différentes. Avec Azure AD, la signature de certificats peut être utilisée avec des applications qui utilisent les protocoles SAML 2.0, WS-Federation ou OpenID Connect, ainsi que l’authentification unique par mot de passe. Pour plus d’informations sur les types d’authentification aux applications à utiliser avec Azure AD, consultez [Gestion des certificats pour l’authentification unique fédérée sur Azure Active Directory](active-directory-sso-certs.md) et [Authentification unique par mot de passe](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Activation de l’authentification unique avec le proxy d’application Azure AD
Le proxy d’application Microsoft Azure AD vous permet d’accéder de façon sécurisée aux applications situées à l’intérieur de votre réseau privé, en tout lieu et sur tout appareil. Après avoir installé un connecteur de proxy d'application dans votre environnement, il peut être facilement configuré avec Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Intégration des applications dans Azure AD
Les articles suivants traitent des différentes façons des applications de s’intégrer avec Azure AD et fournissent des conseils.

* [Détermination de l’Active Directory à utiliser](active-directory-administer.md)
* [Utilisation d’applications dans la galerie d’applications Azure](active-directory-appssoaccess-whatis.md)
* [Liste de didacticiels sur l’intégration d’applications SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gestion de l’accès aux applications
Les articles suivants décrivent des méthodes selon lesquelles vous pouvez gérer l’accès aux applications une fois qu’elles ont été intégrées avec Azure AD à l’aide des connecteurs Azure AD.

* [Gestion de l’accès aux applications à l’aide d’Azure AD](active-directory-managing-access-to-apps.md)
* [Automatisation avec les connecteurs Azure AD](active-directory-saas-app-provisioning.md)
* [Affectation d’utilisateurs à une application](active-directory-applications-guiding-developers-assigning-users.md)
* [Affectation de groupes à une application](active-directory-applications-guiding-developers-assigning-groups.md)
* [Partage de comptes](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Intégration des applications personnalisées
Si vous écrivez une nouvelle application et que vous souhaitez aider les développeurs à tirer parti de la puissance d’Azure AD, consultez [Guide pour les développeurs](active-directory-applications-guiding-developers-for-lob-applications.md).

Si vous souhaitez ajouter votre application personnalisée à la galerie d’applications Azure, consultez [« Apportez votre propre application » avec une configuration SAML libre-service Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="see-also"></a>Voir aussi
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

