---
title: "Ressources, rôles et contrôle d’accès dans Azure Application Insights | Microsoft Docs"
description: "Propriétaires, collaborateurs et lecteurs des perspectives de votre organisation."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 6e811c9b427469fa781cf1f5b7c7deff3a8e6eb3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Contrôle d’accès, rôles et ressources dans Application Insights
Vous pouvez contrôler qui a lu et mis à jour l’accès à vos données dans Azure [Application Insights][start], à l’aide du [Contrôle d’accès basé sur les rôles dans Microsoft Azure](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Accordez l’accès aux utilisateurs dans le **groupe de ressources ou l’abonnement** auquel appartient votre ressource d’application et non dans la ressource elle-même. Affectez le rôle de **collaborateur de composants Application Insights** . Cela garantit un contrôle d’accès uniforme aux tests et aux alertes Web, ainsi qu’aux ressources de votre application. [En savoir plus](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Ressources, groupes et abonnements
Quelques définitions pour commencer :

* **Ressource** : une instance d’un service Microsoft Azure. Votre ressource Application Insights collecte, analyse et affiche les données de télémétrie envoyées par votre application.  Les autres types de ressources Azure incluent des applications Web, des bases de données et des machines virtuelles.
  
    Pour voir vos ressources, ouvrez le [portail Azure][portal], connectez-vous, puis cliquez sur Toutes les ressources. Pour trouver une ressource, tapez une partie de son nom dans le champ filtre.
  
    ![Liste des ressources Azure](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Groupe de ressources**][group] : chaque ressource appartient à un groupe. Un groupe est un moyen pratique de gérer les ressources apparentées, en particulier pour le contrôle d’accès. Par exemple, vous pouvez placer dans un groupe de ressources une application Web, une ressource Application Insights pour surveiller l’application et une ressource de stockage pour conserver les données exportées.

    ![Cliquez sur Parcourir, Groupes de ressources, puis choisissez un groupe](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Abonnement**](https://portal.azure.com) : pour utiliser Application Insights ou d’autres ressources Azure, vous vous connectez à un abonnement Azure. Chaque groupe de ressources appartient à un abonnement Azure, où vous choisissez votre package de prix et, s’il s’agit d’un abonnement d’organisation, sélectionnez les membres et leurs autorisations d’accès.
* [**Compte Microsoft**][account] : le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter aux abonnements Microsoft Azure, XBox Live, Outlook.com et autres services Microsoft.

## <a name="access"></a> Contrôle de l’accès dans le groupe de ressources
Il est important de comprendre qu’en plus de la ressource que vous avez créée pour votre application, il existe également des ressources distinctes masquées pour les alertes et les tests Web. Elles sont associées au même [groupe de ressources](#resource-group) que votre application. Vous pouvez également placer d’autres services Azure ici, comme des sites Web ou du stockage.

![Ressources dans Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Pour contrôler l’accès à ces ressources, il est donc recommandé de :

* contrôler l’accès au niveau du **groupe de ressources ou de l’abonnement** .
* affecter le rôle de **collaborateur de composants Application Insights** . Cela leur permet de modifier les tests Web, les alertes et les ressources d’Application Insights, sans donner accès aux autres services dans le groupe.

## <a name="to-provide-access-to-another-user"></a>Pour fournir l’accès à un autre utilisateur
Vous devez disposer des droits du propriétaire de l’abonnement ou du groupe de ressources.

L’utilisateur doit avoir un [compte Microsoft][account] ou disposer d’un accès à son [compte professionnel Microsoft](../active-directory/sign-up-organization.md). Vous pouvez fournir l’accès aux personnes et aux groupes d’utilisateurs définis dans Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Accédez au groupe de ressources
Ajoutez l’utilisateur à cet endroit.

![Dans le panneau de ressources de votre application, ouvrez Essentials, puis le groupe de ressources et sélectionnez Paramètres/Utilisateurs. Cliquez sur Ajouter.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Vous pouvez également monter d’un niveau supplémentaire et ajouter l’utilisateur à l’abonnement.

#### <a name="select-a-role"></a>Sélectionnez un rôle
![Sélectionnez un rôle pour le nouvel utilisateur](./media/app-insights-resources-roles-access-control/03-role.png)

| Rôle | Dans le groupe de ressources |
| --- | --- |
| Propriétaire |Peut tout modifier, y compris l’accès utilisateur |
| Collaborateur |Peut tout modifier, y compris l’ensemble des ressources |
| collaborateur de composants Application Insights |Peut modifier les ressources, les tests Web et les alertes Application Insights |
| Lecteur |Peut afficher les éléments, mais ne peut rien modifier |

Une « modification » inclut la création, la suppression et la mise à jour des éléments suivants :

* Ressources
* Tests web
* Alertes
* Exportation continue

#### <a name="select-the-user"></a>Sélectionnez l’utilisateur

Si l’utilisateur n’est pas dans le répertoire, vous pouvez inviter toute personne disposant d’un compte Microsoft.
(Si elle utilise des services comme Outlook.com, OneDrive, Windows Phone ou XBox Live, elle dispose d’un compte Microsoft.)

## <a name="related-content"></a>Contenu connexe

* [Contrôle d’accès en fonction du rôle dans Azure](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
