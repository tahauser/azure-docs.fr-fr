---
title: Obtention d’un client Azure AD | Microsoft Docs
description: Obtention d’un client Azure Active Directory pour l'inscription et la génération d'applications.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: ab7db49fa07f260de6ebbe4b2cee943b64cab7fe
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Obtention d’un client Azure Active Directory
Dans Azure Active Directory (Azure AD), un [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) est représentatif d'une organisation.  Il s’agit d’une instance dédiée du service Azure AD qu’une organisation reçoit et possède lorsqu’elle établit une relation avec Microsoft, comme en s’inscrivant à un service cloud de Microsoft tel qu’Azure, Microsoft Intune ou Office 365.  Chaque client Azure AD est distinct et indépendant des autres clients Azure AD.  

Un client héberge les utilisateurs d'une entreprise et leurs informations, c’est-à-dire les mots de passe, les données de profil utilisateur, les autorisations, etc.  Il contient également des groupes, des applications et d’autres informations relatives à une organisation et à sa sécurité.

Pour permettre aux utilisateurs d'Azure AD de se connecter à votre application, vous devez enregistrer votre application dans un de vos clients.  La création d’un locataire Azure AD et la publication d’une application dans celui-ci sont **totalement gratuites** (bien que vous puissiez choisir de payer pour des fonctionnalités premium dans votre locataire).  En fait, de nombreux développeurs créent plusieurs locataires et applications à des fins d’expérimentation, de développement, d’organisation et de test.

## <a name="use-an-existing-azure-ad-tenant"></a>Utiliser un locataire Azure AD existant

De nombreux développeurs comptent déjà des locataires via des services ou des abonnements qui sont liés aux locataires Azure AD (par exemple : abonnements Office 365 ou Azure).  Pour vérifier si vous disposez déjà d’un locataire, connectez-vous au [portail Azure](https://portal.azure.com) avec le compte que vous voulez utiliser pour gérer votre application et vérifiez dans le coin supérieur droit où les informations de votre compte sont affichées.  Si vous disposez d’un locataire, vous êtes automatiquement connecté à celui-ci, et vous verrez le nom du locataire directement sous le nom de votre compte.  Si votre compte est associé à plusieurs locataires, vous pouvez cliquer sur le nom de votre compte pour ouvrir un menu dans lequel vous pouvez basculer entre les locataires.

Si vous ne disposez pas d’un locataire existant associé à votre compte, vous verrez un GUID sous le nom de votre compte et vous ne pourrez pas effectuer des actions telles que l’inscription d’applications tant que vous n’aurez pas [créé de nouveau locataire](#create-a-new-azure-ad-tenant).

## <a name="create-a-new-azure-ad-tenant"></a>Créer un nouveau locataire Azure AD

Si vous ne disposez pas déjà d’un locataire Azure AD ou si vous voulez en créer un nouveau, vous pouvez le faire à l’aide de l’[expérience de création de répertoire](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) dans le [portail Azure](https://portal.azure.com).  Le processus prendra une minute environ et, une fois terminé, vous serez invité à accéder à votre nouveau locataire.