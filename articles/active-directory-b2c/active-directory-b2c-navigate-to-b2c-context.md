---
title: "Azure Active Directory B2C : Basculement vers un locataire B2C | Microsoft Docs"
description: "Comment basculer dans le contexte de votre locataire Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.openlocfilehash: a9b9f71d0c11aee238661d57fd2e5246f1bf0842
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Basculement vers votre locataire Azure AD B2C

Pour configurer Azure AD B2C, vous devez vous trouver dans le contexte de votre locataire Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Se connecter au locataire Azure AD B2C

Pour accéder à votre locataire Azure AD B2C, vous devez être connecté au portail Azure en tant qu’administrateur général du locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](http://portal.azure.com).
1. Basculez les locataires en cliquant sur votre adresse de messagerie ou sur l’image dans le coin supérieur droit.
1. Dans la liste `Directory` qui s’affiche, sélectionnez le locataire Azure AD B2C que vous souhaitez gérer.

Le portail Azure est actualisé.  Vous êtes à présent connecté au portail Azure dans le contexte de votre client Azure AD B2C.

## <a name="navigate-to-the-b2c-features-pane"></a>Accéder au panneau de fonctionnalités B2C

1. Cliquez sur **Parcourir** dans le volet de navigation de gauche.
1. Cliquez sur **Tous les services**, puis recherchez `Azure AD B2C` dans le volet de navigation de gauche.  (Pour l’épingler à votre Tableau d’accueil sur la gauche, cliquez sur l’étoile située à gauche d’Azure AD B2C.)
1. Cliquez sur **Azure AD B2C** pour accéder au panneau de fonctionnalités B2C.
   
    ![Capture d’écran du bouton Parcourir du panneau de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Vous devez être administrateur général du client B2C pour accéder au volet des fonctionnalités B2C. L’administrateur général ou l’utilisateur de tout autre client ne pourra pas y accéder.  Vous pouvez basculer vers votre client B2C en utilisant le sélecteur de client dans le coin supérieur droit du portail Azure.
