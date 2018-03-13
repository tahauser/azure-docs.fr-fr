---
title: "Ajout d’un répertoire Azure Active Directory à l’aide de services connectés dans Visual Studio | Microsoft Docs"
description: "Ajouter un annuaire Azure Active Directory à l’aide de la boîte de dialogue Visual Studio Ajouter des services connectés"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Ajout d’un annuaire Azure Active Directory à l’aide de services connectés dans Visual Studio
En utilisant Azure Active Directory (Azure AD), vous pouvez prendre en charge l’authentification unique (SSO) pour les applications web MVC ASP.NET ou l’authentification Active Directory dans les services API web. Avec l’authentification Azure Active Directory, vos utilisateurs peuvent se servir de leur compte sur Azure Active Directory pour se connecter à vos applications web. Grâce à l’authentification Azure Active Directory avec l’API Web, la sécurité des données pendant l’exposition d’une API à partir d’une application web est renforcée. Avec Azure AD, vous n’avez pas besoin de gérer un système d’authentification distinct doté de ses propres dispositifs de gestion de comptes et d’utilisateurs.

## <a name="prerequisites"></a>Composants requis
- Compte Azure : si vous ne possédez pas de compte Azure, vous pouvez [vous inscrire à un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Se connecter à Azure Active Directory à l’aide de la boîte de dialogue Services connectés
1. Dans Visual Studio, créez ou ouvrez un projet d’API Web ASP.NET ou un projet MVC ASP.NET.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nœud **Services connectés**, puis sélectionnez **Ajouter des services connectés** dans le menu contextuel.

1. Sur la page **Services connectés**, sélectionnez **Authentification avec Azure Active Directory**.
   
    ![Page des services connectés](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Sur la page **Introduction** de l’Assistant **Configurer l’authentification Azure AD**, sélectionnez **Suivant**.
   
    ![Page d’introduction](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. Sur la page **Authentification unique** de l’Assistant **Configurer l’authentification Azure AD**, sélectionnez un domaine dans la liste déroulante **Domaine**. La liste des domaines contient tous les domaines accessibles aux comptes répertoriés dans la boîte de dialogue Paramètres du compte. Vous pouvez également entrer un nom de domaine si vous ne trouvez pas celui que vous recherchez, par exemple `mydomain.onmicrosoft.com`. Vous pouvez opter pour la création d’une application Azure Active Directory ou utiliser les paramètres d’une application Azure Active Directory existante. Sélectionnez **Suivant** lorsque vous avez terminé.
   
    ![Page de l’authentification unique](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. Sur la page **Accès à l’annuaire** de l’Assistant **Configurer l’authentification Azure AD**, vérifiez que l’option **Lire les données d’annuaire** est activée. 
   
    ![Page d’accès à l’annuaire](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Sélectionnez **Terminer** afin d’ajouter le code de configuration et les références nécessaires pour activer votre projet à des fins d’authentification Azure AD. Vous pouvez voir le domaine Active Directory sur le [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Visual Studio affiche un article [Que s’est-il passé ?](#how-your-project-is-modified) pour vous montrer les modifications apportées à votre projet. Pour savoir si tout s’est bien passé, ouvrez un des fichiers de configuration modifiés et vérifiez que les paramètres mentionnés dans l’article sont présents. 

## <a name="how-your-project-is-modified"></a>Modifications apportées à votre projet
Quand vous exécutez l’Assistant, Visual Studio ajoute Azure Active Directory et les références associées à votre projet. Les fichiers de configuration et les fichiers de code dans votre projet sont également modifiés pour prendre en charge Azure AD. Les modifications spécifiques que Visual Studio apporte dépendent du type de projet. Pour plus d’informations sur la façon dont les projets ASP.NET MVC sont modifiés, consultez [Que s’est-il passé : projets ASP.NET MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Pour les projets d’API web, consultez [Que s’est-il passé : projets d’API web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Étapes suivantes
* [Forum MSDN pour Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Documentation Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Billet de blog : Introduction à Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

