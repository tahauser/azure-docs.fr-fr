---
title: À quoi correspondent les licences basées sur les groupes dans Azure Active Directory ? | Microsoft Docs
description: Description des licences basées sur les groupes Azure Active Directory, de leur fonctionnement et des bonnes pratiques
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 1d4151d6c00da618cc008257bcdad4607f8fec49
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Principes de base des licences basées sur les groupes dans Azure Active Directory

L’utilisation de services cloud Microsoft payants, comme Office 365, Enterprise Mobility + Security, Dynamics CRM et d’autres produits similaires, nécessite des licences. Ces licences sont affectées à chaque utilisateur qui a besoin d’accéder à ces services. Pour gérer les licences, les administrateurs utilisent l’un des portails de gestion (Office ou Azure) et des applets de commande PowerShell. Azure Active Directory (Azure AD) est l’infrastructure de base qui prend en charge la gestion de tous les services cloud Microsoft. Azure AD stocke des informations sur les états d’affectation de licence pour les utilisateurs.

Jusqu’à présent, les licences ne pouvaient être affectées qu’au niveau de chaque utilisateur, ce qui pouvait rendre difficile la gestion à grande échelle. Par exemple, pour ajouter ou supprimer des licences utilisateur en raison d’évolutions de l’organisation, par exemple l’arrivée ou le départ d’utilisateurs dans l’organisation ou dans un service, l’administrateur devait souvent écrire un script PowerShell complexe. Ce script permet d’effectuer des appels individuels au service cloud.

Pour relever ces défis, Azure AD inclut maintenant une gestion des licences par groupe. Vous pouvez affecter une ou plusieurs licences de produits à un groupe. Azure AD permet de garantir que les licences sont affectées à tous les membres du groupe. Tous les nouveaux membres qui rejoignent le groupe se voient affecter les licences appropriées. Lorsqu’ils quittent le groupe, ces licences sont supprimées. Ceci élimine toute nécessité d’automatiser la gestion des licences avec PowerShell pour refléter les évolutions de la structure de l’organisation et des services utilisateur par utilisateur.

## <a name="features"></a>Caractéristiques

Voici les principales fonctionnalités de la gestion des licences par groupe :

- Les licences peuvent être affectées à n’importe quel groupe de sécurité dans Azure AD. Les groupes de sécurité peuvent être synchronisés en local à l’aide d’Azure AD Connect. Vous pouvez également créer des groupes de sécurité directement dans Azure AD (ils sont également appelé groupes cloud purs) ou automatiquement avec la fonctionnalité de groupe dynamique d’Azure AD.

- Lorsqu’une licence de produit est affectée à un groupe, l’administrateur peut désactiver un ou plusieurs plans de services dans le produit. En règle générale, c’est le cas lorsque l’organisation n’est pas encore prête à utiliser un service inclus dans un produit. Par exemple, l’administrateur peut affecter Office 365 à un service, mais désactiver temporairement le service Yammer.

- Tous les services de cloud computing Microsoft nécessitant des licences au niveau des utilisateurs sont pris en charge. Cela comprend tous les produits Office 365, Enterprise Mobility + Security et Dynamics CRM.

- Les licences basées sur les groupes ne sont pour le moment disponibles que sur le [Portail Azure](https://portal.azure.com). Si vous utilisez principalement d’autres portails de gestion pour la gestion des utilisateurs et groupes, tels que le portail Office 365, vous pouvez continuer à le faire. Vous devez toutefois utiliser le portail Azure pour gérer les licences au niveau du groupe.

- Azure AD gère automatiquement les modifications de licences qui résultent de modifications de l’appartenance aux groupes. En règle générale, les modifications de licence sont effectives quelques minutes après une modification d’appartenance.

- Un utilisateur peut être membre de plusieurs groupes dans lesquels des stratégies de licences sont spécifiées. Un utilisateur peut également disposer de licences affectées directement, en dehors de groupes. L’état utilisateur qui en résulte est une combinaison de toutes les licences de produits et services affectées.

- Dans certains cas, des licences ne peuvent pas être affectées à un utilisateur. Par exemple, les licences disponibles dans le client peuvent ne pas être suffisantes, ou des services conflictuels peuvent avoir été affectés simultanément. Les administrateurs ont accès aux informations sur les utilisateurs pour lesquels Azure AD n’a pas pu traiter entièrement les licences de groupes. Ils peuvent prendre des mesures correctives en fonction de ces informations.

- Dans la préversion publique, un abonnement d’évaluation ou payant pour les éditions Azure AD De base ou Premium est obligatoire dans le locataire pour utiliser la gestion des licences par groupe.

## <a name="your-feedback-is-welcome"></a>Vos commentaires sont les bienvenus.

Si vous avez des commentaires ou des demandes de fonctionnalités, partagez-les avec nous via [ce forum](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des licences basées sur les groupes, consultez :

* [Bien démarrer avec les licences dans Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Affectation de licences à un groupe dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers les licences basées sur les groupes dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Autres scénarios de licences basées sur les groupes Azure Active Directory](active-directory-licensing-group-advanced.md)
