---
title: "Accéder aux applications proxy d’Azure AD App dans Teams | Microsoft Docs"
description: "Utilisez le proxy d’application Azure AD pour accéder à votre application locale via Microsoft Teams."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a8dca307bb97898f229b39710a3b566ee913fcca
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Accéder aux applications locales via Microsoft Teams

Le proxy d’application Azure Active Directory vous donne une authentification unique auprès des applications locales, où que vous soyez. Microsoft Teams rationalise vos efforts de collaboration à un seul emplacement. L’intégration de ces deux éléments signifie que vos utilisateurs peuvent être productifs avec leurs coéquipiers quelle que soit la situation. 

Vos utilisateurs peuvent ajouter des applications cloud à leurs canaux Teams [en utilisant des onglets](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), mais que se passe-t-il si les sites SharePoint ou l’outil de planification sont hébergés localement ? Le proxy d’application est la solution. Ils peuvent ajouter les applications publiées via le proxy d’application à leurs canaux en utilisant les mêmes URL externes qu’ils utilisent toujours pour accéder à leurs applications à distance. Et, comme le proxy d’application s’authentifie via Azure Active Directory, les utilisateurs obtiennent une expérience d’authentification unique.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Installer le connecteur du proxy d’application et publier votre application

Si vous ne l’avez pas déjà fait, [configurez le proxy d’application pour votre abonné et installez le connecteur](active-directory-application-proxy-enable.md). Ensuite, [publiez votre application locale](application-proxy-publish-azure-portal.md) pour l’accès à distance. Quand vous publiez l’application, notez l’URL externe, car elle est utilisée pour ajouter l’application à Teams.

Si vos applications ont déjà été publiées mais que vous avez oublié leurs URL externes, recherchez-les sur le [portail Azure](https://portal.azure.com). Connectez-vous, puis accédez à **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications** > sélectionnez votre application > **Proxy d’application**.

## <a name="add-your-app-to-teams"></a>Ajouter votre application dans Teams

Une fois que vous avez publié l’application via le proxy d’application, informez vos utilisateurs qu’ils peuvent l’ajouter sous la forme d’un onglet directement dans leurs canaux Teams. L’application est alors disponible pour tous les membres de l’équipe. Demandez-leur d’effectuer les trois étapes suivantes :

1. Accédez au canal Teams où vous souhaitez ajouter cette application et sélectionnez **+** pour ajouter un onglet.

   ![Sélectionner Ajouter un onglet](./media/application-proxy-teams/add-tab.png)

2. Sélectionnez **Site web** parmi les options d’onglet.

   ![Ajouter un site web](./media/application-proxy-teams/website.png)

3. Donnez un nom à l’onglet et définissez l’URL sur l’URL externe du proxy d’application. 

   ![Configurer le nom et l’URL de l’onglet](./media/application-proxy-teams/tab-name-url.png)

Une fois qu’un membre d’une équipe a ajouté l’onglet, celui-ci s’affiche pour tout le monde dans le canal. Tous les utilisateurs qui ont accès à l’application bénéficient de l’authentification unique avec les informations d’identification qu’ils utilisent pour Microsoft Teams. Les utilisateurs qui n’ont pas accès à l’application peuvent voir l’onglet dans Teams, mais sont bloqués jusqu’à ce que vous leur accordiez l’autorisation d’accéder à l’application locale et à la version de l’application publiée sur le portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [publier des sites SharePoint locaux](application-proxy-enable-remote-access-sharepoint.md) avec le proxy d’application.
- Configurez vos applications pour utiliser des [domaines personnalisés](active-directory-application-proxy-custom-domains.md) pour leur URL externe. 
