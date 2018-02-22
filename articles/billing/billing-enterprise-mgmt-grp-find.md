---
title: "Rechercher un abonnements ou un groupe d’administration Azure | Microsoft Docs"
description: "Comment naviguer entre plusieurs répertoires pour voir vos groupes d’administration et abonnements"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: f1b9c1ec2af8240ff71f6907516d8894c36ac9c3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Rechercher un abonnement ou un groupe d’administration Azure

Si vous éprouvez des difficultés à trouver un abonnement ou un groupe d’administration dans Azure, il se peut que vous ne recherchiez pas dans le bon répertoire. Cette situation peut se produire lorsque votre compte existe dans plusieurs répertoires Azure Active Directory. Chaque [Active Directory est indépendant](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-directory-independence) et l’accès n’est pas hérité entre les répertoires.      

![Menu Changer de répertoire](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Changer de répertoires 
Vous pouvez facilement changer de répertoire dans le portail Azure.
1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Sélectionnez votre nom dans l’angle supérieur droit de l’écran. 
3.  Le bas du menu répertorie tous les répertoires auxquels vous avez accès.
4.  Sélectionnez le nom d’un répertoire pour accéder à celui-ci. 

![Menu Changer de répertoire](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>Une ressource est indisponible ? 
L’affichage du message d’erreur « La ressource n'est pas disponible » quand vous essayez d’accéder à un abonnement ou à un groupe d’administration signifie que vous n’avez pas le rôle approprié pour afficher cet élément.  

![ressource introuvable](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Contactez l’administrateur de l’abonnement ou du groupe d’administration pour obtenir l’accès.  
* Pour les abonnements, reportez-vous au document [Contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) afin de déterminer le rôle requis.
* Pour les groupes d’administration, l’accès RBAC sera bientôt disponible. Contactez l’administrateur de votre portail d’entreprise pour obtenir un accès.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Améliorer votre expérience avec des groupes d’administration et des abonnements dans le même répertoire 
Vous pouvez transférer vos abonnements ou groupes d’administration vers le répertoire de votre choix de façon à ce que tous les éléments se trouvent au même endroit.  La consolidation des abonnements et groupes d’administration dans le même répertoire réduit la nécessité de changer de répertoire et permet l’héritage de stratégies.  


### <a name="transfer-your-subscriptions"></a>Transférer vos abonnements 
Vous pouvez déplacer un abonnement vers le répertoire associé à vos groupes d’administration. Vous pouvez obtenir ce déplacement en demandant à votre administrateur d’inscription de transférer votre abonnement vers un compte dans le répertoire cible. Pour plus d’informations, connectez-vous au [Portail d’entreprise](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 






