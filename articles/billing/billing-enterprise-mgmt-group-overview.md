---
title: "Organiser vos ressources avec des groupes d’administration Azure - Azure | Microsoft Docs"
description: "Découvrez les groupes d’administration et comment les utiliser."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organiser vos ressources avec des groupes d’administration Azure 

Si vous avez plusieurs abonnements, vous pouvez les organiser en conteneurs appelés « groupes d’administration » pour vous aider à gérer l’accès, la stratégie, les coûts et la conformité sur l’ensemble de vos abonnements. Par exemple, vous pouvez appliquer des stratégies à un groupe d’administration qui limitent les types de ressources pouvant être créés.

> [!Note]
> Cette fonctionnalité est disponible en préversion privée. [Inscrivez-vous ici](https://aka.ms/MGPreviewSignup) pour joindre votre inscription à la préversion.   
 


Vous pouvez organiser les groupes d’administration en une hiérarchie. La structure illustrée est un exemple de représentation d’une hiérarchie de groupes d’administration concrète :


![Arborescence hiérarchique](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Comment les groupes d’administration sont liés à votre inscription à Azure Enterprise

L’introduction des groupes d’administration est une étape du processus de la transition des fonctionnalités du [portail Entreprise](https://ea.azure.com) vers le [portail Azure](https://portal.azure.com).

La structure des groupes d’administration est créée telle qu’elle était définie dans le portail Entreprise. La hiérarchie entière regroupant l’inscription, les services et les comptes est mappée à des groupes d’administration correspondants. 

Voici comment la structure actuelle EA est mappée à la hiérarchie de groupes d’administration. 

![Arborescence hiérarchique](media/billing-enterprise-mgmt-groups/tree2.png)

Le tableau ci-dessous montre comment les utilisateurs du portail Entreprise sont mappés à la hiérarchie de groupes d’administration.

|    Rôle EA                                       |    Rôle sur le nœud de groupe d’administration mappé    |    Autorisations sur le nœud de groupe d’administration                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    Administrateur EA                              |    Contributeur de la stratégie de ressource                   |    Peut afficher les coûts, gérer la stratégie de ressource et afficher la hiérarchie sur le nœud d’inscription et en dessous    |
|    Administrateur EA en mode lecture seule            |    Lecteur de facturation                                |    Peut lire les coûts et afficher la hiérarchie sur le nœud d’inscription et en dessous                              |
|    Administrateur de service                      |    Lecteur de facturation                                |    Peut lire les coûts et afficher la hiérarchie sous le nœud de service                                 |
|    Administrateur de service en mode lecture seule    |    Lecteur de facturation                                |    Peut lire les coûts et afficher la hiérarchie sous le nœud de service                                 |
|    Propriétaire du compte                                 |    Contributeur de la stratégie de ressource                   |    Peut afficher les coûts, gérer la stratégie de ressource et afficher la hiérarchie sur le nœud de compte et en dessous       |




## <a name="view-management-groups-in-the-azure-portal"></a>Afficher les groupes d’administration dans le portail Azure

Pour afficher une inscription, un service ou un compte au sein de la préversion, connectez-vous au portail Azure avec le lien fourni dans l’e-mail de bienvenue.   

![hiérarchie](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Affichage des coûts 
Les écrans des détails des groupes d’administration indiquent les coûts mensuels à la date du jour. Ces coûts sont basés sur l’utilisation et ne tiennent pas compte des montants prépayés, dépassements, quantités incluses, ajustements et taxes. Pour le groupe d’administration correspondant à votre inscription, la section des coûts vous montre l’engagement restant.  

![Détail de l’inscription](media/billing-enterprise-mgmt-groups/enrollment.png)

 Les « coûts facturés séparément » sont l’accumulation mensuelle des modifications distinctes, liées, par exemple, aux marketplace, dépassements et autres coûts, qui n’ont pas d’incidence sur l’engagement de votre inscription.  Pour plus d’informations sur le détail des coûts, consultez le [portail Entreprise](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Activation de l’accès aux coûts
Si vous ne voyez pas les coûts, consultez notre document [Résolution des problèmes d’affichage des coûts d’entreprise](https://aka.ms/enableazurecosts).  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Délais entre le portail Entreprise et le portail Azure 
* Pendant la préversion, les montants affichés dans le portail Azure peuvent être différés par rapport aux valeurs indiquées dans le portail Entreprise. Ce problème est temporaire et est en cours de traitement.
* La mise à jour des paramètres dans le portail Entreprise met plusieurs minutes à se répercuter dans le portail Azure. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Les groupes d’administration ont une relation avec votre annuaire   
Comme les abonnements, les groupes d’administration ont une relation d’approbation avec Azure AD. Une hiérarchie de groupes d’administration fait confiance à un annuaire unique pour authentifier les utilisateurs. Tous les administrateurs associés à une hiérarchie de groupes d’administration doivent appartenir au même annuaire. 

Quand votre hiérarchie d’inscription est mappée aux groupes d’administration, une relation d’approbation est établie avec un annuaire unique. Dans la mesure du possible, un annuaire existant associé à des comptes d’utilisateur de l’inscription est sélectionné. Dans certains cas, un nouvel annuaire est créé, puis tous les utilisateurs existants de l’inscription y sont invités. Les annuaires associés aux abonnements de l’inscription ne sont pas affectés. Ainsi, la hiérarchie peut être créée dans un annuaire différent des abonnements. [Découvrez-en plus](billing-enterprise-mgmt-grp-find.md) sur l’impact de ce processus sur l’expérience de navigation entre la hiérarchie et ses abonnements.

## <a name="administering-your-management-groups"></a>Administration de vos groupes d’administration
Les groupes d’administration dans le portail Azure sont disponibles en préversion et en lecture seule dans cette version initiale. Pour effectuer des mises à jour, accédez au portail Entreprise. Vos mises à jour sont automatiquement répercutées dans le portail Azure. Consultez la documentation disponible au sein du portail Entreprise pour obtenir de l’aide sur les modifications et les ajouts.   

## <a name="policy-management"></a>Gestion des stratégies
Resource Manager vous permet de créer des stratégies personnalisées pour gérer vos ressources. Vous pouvez utiliser des groupes d’administration pour affecter des stratégies à n’importe quel niveau de la hiérarchie ; les ressources héritent ensuite de ces stratégies.  [En savoir plus](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> La stratégie n’est pas appliquée aux annuaires. 


