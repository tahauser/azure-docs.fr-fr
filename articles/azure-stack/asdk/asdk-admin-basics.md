---
title: Principes de base du kit de développement Azure Stack | Microsoft Docs
description: Explique comment effectuer des tâches d’administration de base pour le kit de développement Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: cb169c2d2a5aa918fb6d330ebc4677d6c16d308d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="asdk-administration-basics"></a>Principes de base de l’administration du kit ASDK 
Si vous débutez avec l’administration du kit de développement Azure Stack, vous devez prendre connaissance de plusieurs choses. Ce guide présente le rôle d’opérateur Azure Stack dans l’environnement d’évaluation, et explique comment permettre aux utilisateurs de test de devenir productifs plus rapidement.

Vous devez d’abord lire l’article [Qu’est-ce que le kit de développement Azure Stack ?](asdk-what-is.md) pour bien comprendre le rôle du kit de développement Azure Stack et ses limitations. Vous devez utiliser le kit de développement comme un « bac à sable », dans lequel vous pouvez évaluer Azure Stack, ainsi que développer et tester vos applications dans un environnement hors production. 

Comme Azure, Azure Stack innove constamment. De nouvelles builds du kit ASDK seront donc régulièrement publiées. Toutefois, vous ne pouvez pas mettre à niveau le kit ASDK comme vous le feriez pour les systèmes intégrés Azure Stack. De fait, si vous souhaitez passer à la version la plus récente, vous devez entièrement [redéployer Azure Stack](asdk-redeploy.md). Vous ne pouvez pas appliquer les packages de mises à jour. Ce processus prend du temps, mais il vous permet toutefois d’essayer les fonctionnalités dès leur publication. 

## <a name="what-tools-do-i-use-to-manage"></a>Quels outils dois-je utiliser pour la gestion ?
Vous pouvez utiliser le [portail Administrateur Azure Stack ](https://adminportal.local.azurestack.external) ou PowerShell pour gérer Azure Stack. Le moyen le plus simple de découvrir les concepts de base est d’utiliser le portail. Si vous souhaitez utiliser PowerShell, vous devez installer [PowerShell pour Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) et [télécharger les outils Azure Stack à partir de GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack utilise Azure Resource Manager comme mécanisme de déploiement, de gestion et d’organisation sous-jacent. Si vous comptez gérer Azure Stack et assister les utilisateurs, vous devez connaître Azure Resource Manager. Pour plus d’informations, consultez le livre blanc [Getting Started with Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Vos responsabilités classiques
Vos utilisateurs souhaitent utiliser des services. De leur point de vue, votre rôle principal est de mettre ces services à leur disposition. Le kit ASDK vous permet de connaître les services proposés et de savoir comment les rendre accessibles [en créant des plans, des offres et des quotas](asdk-offer-services.md). Vous devez également ajouter des éléments à la Place de Marché, tels que des images de machines virtuelles. Le moyen le plus simple est de [télécharger des éléments de la Place de marché](asdk-marketplace-item.md) à partir d’Azure dans Azure Stack.

> [!NOTE]
> Si vous souhaitez tester vos plans, vos offres et vos services, vous devez utiliser le [portail Utilisateur](https://portal.local.azurestack.external), et non le [portail Administrateur](https://adminportal.local.azurestack.external).

En plus de fournir des services, vous devez effectuer toutes les tâches standard d’un opérateur Azure Stack pour veiller au bon fonctionnement du kit ASDK. Il s’agit notamment des tâches suivantes :
- Ajouter des comptes d’utilisateur pour le déploiement d’Azure Active Directory (Azure AD) ou d’Active Directory Federation Services (AD FS)
- Affecter des rôles de contrôle d’accès en fonction du rôle (RBAC) (cela ne se limite pas aux administrateurs)
- Surveiller l’intégrité de l’infrastructure
- Gérer les ressources réseau et les ressources de stockage
- Remplacer l’ordinateur hôte du kit de développement en cas de panne 

## <a name="where-to-get-support"></a>Où obtenir un support technique ?
Pour le kit de développement, votre seule option de support est de poser des questions sur le [forum Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Si vous cliquez sur l’icône d’aide et de support (point d’interrogation) dans le coin supérieur droit du portail administrateur, puis sur **Nouvelle demande de support**, le site des forums s’ouvre directement. Ces forums sont consultés régulièrement. 

> [!IMPORTANT]
> Le kit de développement étant un environnement d’évaluation, aucune prise en charge officielle n’est proposée par les services de support technique Microsoft.

## <a name="next-steps"></a>Étapes suivantes
[Déployer le kit ASDK](asdk-deploy.md)

