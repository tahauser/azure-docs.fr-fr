---
title: "Vue d’ensemble des plans, des offres, des quotas et des abonnements Azure Stack | Microsoft Docs"
description: "En tant qu’opérateur cloud, je souhaite comprendre les plans, les offres, les quotas et les abonnements Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 7c395dfcdfcd509d8b32028badd1c1310ea67657
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Vue d’ensemble des plans, des offres, des quotas et des abonnements

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

[Azure Stack](azure-stack-poc.md) permet de fournir un large éventail de services, comme des machines virtuelles, des bases de données SQL Server, SharePoint, Exchange et même des [articles de la Place de marché Azure](azure-stack-marketplace-azure-items.md). En tant qu’opérateur Azure Stack, vous configurez et fournissez ces services dans Azure Stack avec des plans, des offres et des quotas.

Les offres contiennent un ou plusieurs plans, et chaque plan inclut un ou plusieurs services. En créant des plans et en les regroupant dans différentes offres, on peut contrôler :
- les services et ressources auxquels les utilisateurs ont accès ;
- la quantité de ressources que les utilisateurs peuvent consommer ;
- les régions qui ont accès aux ressources.

Pour fournir un service, vous suivrez les grandes étapes suivantes :

1. Ajoutez un service que vous souhaitez fournir à vos utilisateurs.
2. Créez un plan qui contient un ou plusieurs services. Ce faisant, vous sélectionnerez ou créerez des quotas qui définissent les limites de ressources de chacun des services du plan.
3. Créez une offre qui contient un ou plusieurs plans (plans de base et plans d’extension facultatifs compris).

Une fois l’offre créée, vos utilisateurs peuvent s’y abonner pour accéder aux services et aux ressources qu’elle fournit. Ils peuvent s’abonner à autant d’offres qu’ils le souhaitent. Le diagramme suivant montre l’exemple simple d’un utilisateur qui s’est abonné à deux offres. Chaque offre comprend un ou deux plans, et chacun leur donne accès à des services.

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Abonnements

Les plans regroupent un ou plusieurs services. En tant qu’opérateur Azure Stack, vous [créez des plans](azure-stack-create-plan.md) à proposer à vos utilisateurs. En retour, ceux-ci s’abonnent à vos offres pour utiliser les plans et les services qu’elles comprennent. Lors de la création des plans, veillez à définir vos quotas et vos plans de base et à envisager d’inclure des plans d’extension facultatifs.

### <a name="quotas"></a>Quotas

Pour mieux gérer votre capacité cloud, vous pouvez sélectionner ou créer un quota pour chacun des services d’un plan. Les quotas définissent les limites de ressources supérieures qu’un abonnement utilisateur peut approvisionner ou consommer. Par exemple, un quota peut autoriser un utilisateur à créer jusqu’à cinq machines virtuelles. Les quotas peuvent limiter différentes ressources, notamment les machines virtuelles, la RAM et l’UC.

Les quotas peuvent être configurés région par région. Par exemple, un plan comprenant des services de calcul de la région A peut avoir un quota de deux machines virtuelles, 4 Go de RAM et 10 cœurs de processeur. Dans le Kit de développement Azure Stack, une seule région (nommée *local*) est disponible.

### <a name="base-plan"></a>Plan de base

Lors de la création d’une offre, l’administrateur de services fédérés peut inclure un plan de base. Ces plans de base sont inclus par défaut lorsqu’un utilisateur s’abonne à cette offre : il a alors accès à tous les fournisseurs de ressources spécifiés dans ces plans (avec les quotas correspondants).

### <a name="add-on-plans"></a>Plans d’extension

Vous pouvez également inclure des plans d’extension facultatifs dans une offre. Les plans additionnels ne sont pas inclus par défaut dans l’abonnement. Les plans d’extension sont des plans supplémentaires (avec quotas), disponibles dans une offre, qu’un abonné peut ajouter à ses abonnements. Par exemple, vous pouvez proposer un plan de base avec des ressources limitées pour un essai, et un plan d’extension avec des ressources plus importantes pour les clients qui décident d’adopter le service.

## <a name="offers"></a>Offres

Les offres sont des groupes d’un ou plusieurs plans créés pour que les utilisateurs puissent s’y abonner. Par exemple, une offre Alpha peut contenir un plan A comportant un ensemble de services de calcul et un plan B comportant un ensemble de services de stockage et réseau. 

Lorsque vous [créez une offre](azure-stack-create-offer.md), vous devez inclure au moins un plan de base, mais vous avez également la possibilité de créer des plans d’extension que les utilisateurs pourront ajouter à leur abonnement.


## <a name="subscriptions"></a>Abonnements

L’abonnement est la forme sous laquelle les utilisateurs accèdent aux offres. Si vous êtes un opérateur Azure Stack chez un fournisseur de services, vos utilisateurs (clients) achètent vos services en s’abonnant à vos offres. Si vous êtes un opérateur Azure Stack au sein d’une organisation, vos utilisateurs (employés) peuvent s’abonner gratuitement aux services que vous proposez. Chaque combinaison entre un utilisateur et une offre correspond à un abonnement unique. Par conséquent, un utilisateur peut avoir des abonnements à plusieurs offres, mais chaque abonnement s’applique à une seule offre. Les plans, les offres et les quotas s’appliquent uniquement à un abonnement donné ; ils ne sont pas partageables entre différents abonnements. Chaque ressource créée par un utilisateur est associée à un seul abonnement.


### <a name="default-provider-subscription"></a>Abonnement au fournisseur par défaut

L’abonnement au fournisseur par défaut est automatiquement créé lors du déploiement du Kit de développement Azure Stack. Il permet de gérer Azure Stack, de déployer d’autres fournisseurs de ressources et de créer des plans et des offres pour les utilisateurs. Pour des raisons de sécurité et de gestion des licences, il ne doit pas être utilisé pour exécuter des applications et des charges de travail de clients. 

## <a name="next-steps"></a>Étapes suivantes

[Créer un plan](azure-stack-create-plan.md)
