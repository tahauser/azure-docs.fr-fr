---
title: Activer un fournisseur de services cloud pour gérer votre abonnement Azure Stack | Microsoft Docs
description: Active le fournisseur de services pour accéder à un abonnement dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 4bc5644425aa11fb210d81095e4166baefc6432e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Activer un fournisseur de services cloud pour gérer votre abonnement Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Si vous utilisez Azure Stack avec un fournisseur de services cloud, vous pouvez gérer votre accès aux ressources dans votre abonnement Azure et dans Azure Stack. Vous pouvez aussi gérer votre propre abonnement. Cet article détaille comment vous pouvez activer votre fournisseur de services pour accéder à votre abonnement en votre nom, ou pour vous assurer que le fournisseur de service peut gérer votre service.

> [!Note]  
>  Si vous ignorez les étapes suivantes, et que le fournisseur de services cloud ne gère pas déjà votre compte, il ne sera alors pas en mesure de gérer votre abonnement Azure Stack pour vous.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gérer votre abonnement avec un fournisseur de services cloud

1. Ajoutez votre fournisseur de services cloud en tant qu’utilisateur invité avec le rôle d’utilisateur dans votre répertoire d’abonné.  Pour savoir comment ajouter un utilisateur, voir [Ajouter de nouveaux utilisateurs dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Le fournisseur de services cloud crée ensuite un abonnement Azure Stack local pour vous.
3. Vous êtes prêt à utiliser Azure Stack.
3. Votre fournisseur de services cloud doit ensuite créer une ressource dans votre abonnement pour vérifier s’ils sont en mesure de gérer vos ressources. Par exemple, ils peuvent [Créer une machine virtuelle Windows avec le portail Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Activer le fournisseur de services cloud pour gérer votre abonnement avec les droits du contrôle d’accès en fonction du rôle

Ajoutez le fournisseur de services cloud en tant que propriétaire dans votre abonnement. 

1. Ajoutez votre fournisseur de services cloud en tant qu’utilisateur invité. avec le rôle de propriétaire dans votre répertoire d’abonné.  Pour savoir comment ajouter un utilisateur, voir [Ajouter de nouveaux utilisateurs dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Ajoutez le rôle Propriétaire à l’utilisateur invité du fournisseur de services cloud. Pour savoir comment ajouter l’utilisateur du fournisseur de services cloud à votre abonnement, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).
3. Le fournisseur de services cloud crée ensuite un abonnement Azure Stack local pour vous.
4. Vous êtes prêt à utiliser Azure Stack.
5. Votre fournisseur de services cloud doit ensuite créer une ressource dans votre abonnement pour vérifier s’ils sont en mesure de gérer vos ressources. 

## <a name="next-steps"></a>Étapes suivantes

  - Pour en savoir plus sur la récupération d’informations d’utilisation de ressources depuis Azure Stack, consultez [Utilisation et facturation dans Azure Stack](../azure-stack-billing-and-chargeback.md).
