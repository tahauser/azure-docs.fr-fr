---
title: "Présentation du manuel de preuve de concept Azure Active Directory | Microsoft Docs"
description: "Explorer et implémenter rapidement des scénarios de gestion des identités et des accès"
services: active-directory
keywords: azure active directory, manuel, preuve de concept, PoC
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Manuel de preuve de concept Azure Active Directory : Présentation

Cet article fournit des instructions pour explorer différentes fonctionnalités d’Azure AD dans une preuve de concept (PoC). Le public concerné est celui des architectes d’identité, des professionnels de l’informatique et des intégrateurs de systèmes

## <a name="how-to-use-this-playbook"></a>Comment utiliser ce manuel

1. Utilisez la section [Thème](active-directory-playbook-ingredients.md#theme) pour choisir les domaines qui vous intéressent en fonction de vos besoins.  
2. Définissez l’étendue de cette preuve de concept en choisissant les scénarios correspondant aux objectifs de votre entreprise. Plus le scénario est court, mieux c’est. Nous vous recommandons donc d’être aussi concis que possible pour transmettre la valeur aux parties prenantes tout en minimisant la complexité de la réalisation.  
3. Utilisez la section [Implémentation](active-directory-playbook-implementation.md) pour comprendre les différents scénarios et ce qu’ils signifient pour votre environnement. Dans chaque scénario, nous décrivons comment le configurer (ce que nous appelons les [blocs de construction](active-directory-playbook-building-blocks.md)) et comment naviguer dans les scénarios. 
4. Chaque bloc de construction explique les conditions préalables requises, ainsi que le temps approximatif d’accomplissement. Cela peut vous aider pendant le processus de planification. 
5. Sur la base des points 1 à 3 ci-dessus, définissez l’[environnement](active-directory-playbook-ingredients.md#environment) d’exécution. Nous vous encourageons à recourir à un environnement de production afin que vos utilisateurs aient une bonne perception de l’expérience. 
6. En cas d’exigences contradictoires, servez-vous de cette matrice de compromis très utile : 
   * Démonstration de valeur centrée sur un thème  
   * Fluidité de préparation, de configuration et d’exécution des scénarios 
   * Minimum de temps pour l’exécution des scénarios cibles 
   * Proximité maximale de la production compte tenu de vos contraintes 

>[!NOTE]
> Cet article présente certains produits et applications tiers spécifiques mentionnés en temps qu’exemples pour des raisons pratiques. Azure AD prend en charge des milliers d’applications dans notre [Galerie d’applications](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), que vous pouvez utiliser en fonction de vos besoins et de votre environnement. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]