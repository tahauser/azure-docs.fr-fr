---
title: 'Azure Active Directory B2C : Résoudre les problèmes liés à la création de locataires | Microsoft Docs'
description: Problèmes et résolutions pour la création d’un locataire Azure Active Directory ou Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 3daf232d7fb1f95c390c1e6b8c168ec585484c65
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Résoudre les problèmes de création d’un locataire Azure Active Directory ou Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Créer un locataire Azure AD
Si vous ne pouvez pas créer de locataire Azure Active Directory (Azure AD) lors de votre première tentative, réessayez. Si le problème persiste, contactez le support Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C
Si vous rencontrez des problèmes quand vous [créez un locataire Azure Active Directory B2C (Azure AD B2C)](active-directory-b2c-get-started.md), essayez les options suivantes :

* Si le locataire Azure AD B2C n’apparaît pas dans votre liste de locataires, réessayez de le créer.
* Si le locataire Azure AD B2C apparaît dans la liste des locataires et que le message d’erreur suivant s’affiche, supprimez le locataire et recréez-le :

    « Impossible de terminer la création du locataire B2C ’contosob2c’. Visitez ce [lien](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) pour plus d’informations. »
* Il existe des problèmes connus liés à la suppression d’un locataire Azure AD B2C existant et à sa recréation à l’aide du même nom de domaine. Quand vous créez un locataire Azure AD B2C, vous devez utiliser un nom de domaine différent.
* Si aucune de ces solutions ne résout votre problème, contactez le support technique Azure. Pour plus d’informations, consultez [Prise en charge des demandes d’assistance pour Azure AD B2C](active-directory-b2c-support.md).

