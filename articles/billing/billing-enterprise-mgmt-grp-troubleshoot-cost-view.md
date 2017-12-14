---
title: "Résoudre les problèmes liés aux vues des coûts d’entreprise - Azure | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes que vous pourriez rencontrer avec les vues des coûts d’organisation dans le portail Azure."
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
ms.openlocfilehash: acdcf5b772cbaca1827663b49d5cc8cafa238108
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="troubleshoot-enterprise-cost-views"></a>Résoudre les problèmes liés aux vues des coûts d’entreprise 

Dans les inscriptions d’entreprise, plusieurs paramètres peuvent empêcher les utilisateurs au sein de l’inscription d’afficher les coûts.  Ces paramètres sont gérés par l’administrateur en charge de l’inscription, ou par le partenaire si l’inscription n’a pas été achetée directement auprès de Microsoft.  Cet article vous aide à comprendre quels sont les paramètres et dans quelle mesure ils impactent l’inscription. Ces paramètres sont indépendants des [rôles RBAC Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

> [!Note]
> Cette fonctionnalité est disponible en préversion privée. [Inscrivez-vous ici](https://forms.office.com/Pages/DesignPage.aspx#FormId=v4j5cvGGr0GRqy180BHbR0YtfU6ham9OsGsPPYdu2xdUNk1BQUwzTkUyOVc5NUpCTFcwR0pIOVFETS4u) pour joindre votre inscription à la préversion.     

## <a name="enabling-access-to-costs"></a>Activation de l’accès aux coûts

Voyez-vous un message « Non autorisé » ou *« Les vues des coûts sont désactivées dans votre inscription. »* quand vous recherchez des informations sur les coûts ? ![Non autorisé](media/billing-enterprise-mgmt-groups/unauthorized.png)

Cela peut être dû à l’une des raisons suivantes :

1. Vous avez acheté Azure par le biais d’un partenaire d’entreprise qui n’a toujours pas publié de tarifs. Pour que les tarifs soient publiés, contactez votre partenaire afin qu’il mette à jour le paramètre dans le [portail Entreprise](https://ea.azure.com).
2. Ou bien, si vous êtes client EA Direct, les possibilités sont les suivantes :
    * Vous êtes propriétaire de compte et votre administrateur en charge de l’inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de compte ».  
    * Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de service ».
    * Contactez l’administrateur en charge de votre inscription pour obtenir l’accès. L’administrateur en charge de l’inscription peut visiter le [portal Entreprise](https://ea.azure.com/manage/enrollment) et mettre à jour le paramètre, comme illustré ci-après :

![Paramètres du portail Entreprise](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>En cas d’indisponibilité d’une ressource 
L’affichage du message d’erreur « La ressource n'est pas disponible » quand vous essayez d’accéder à un abonnement ou à un groupe d’administration signifie que vous n’avez pas le rôle approprié pour afficher cet élément.  

![ressource introuvable](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Contactez l’administrateur de l’abonnement ou du groupe d’administration pour obtenir l’accès.  
* Pour les abonnements, reportez-vous au document [Contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) afin de déterminer le rôle requis.
* Pour les groupes d’administration, l’accès RBAC sera bientôt disponible. Contactez l’administrateur de votre portail d’entreprise pour obtenir un accès.   
