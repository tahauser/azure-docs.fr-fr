---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
Lorsque vous déployez des ressources vers Azure, il est extrêmement simple de décider quels types de ressources déployer, leur emplacement et leur mode de configuration. Toutefois, cette flexibilité peut offrir plus d’options que vous ne souhaitez autoriser dans votre organisation. Si vous envisagez de déployer des ressources vers Azure, vous vous demandez peut-être :

* Comment respecter les obligations légales en matière de souveraineté des données dans certains pays ?
* Comment contrôler les coûts ?
* Comment vérifier que quelqu'un ne modifie pas par inadvertance un système critique ?
* Comment effectuer le suivi des coûts des ressources et les facturer précisément ?

Cet article répond à ces questions. Plus précisément, vous :

> [!div class="checklist"]
* assignez des utilisateurs aux rôles et les rôles à une étendue afin que les utilisateurs soient autorisés à effectuer des actions attendues, mais pas plus d’actions,
* appliquez des stratégies qui définissent les conventions pour les ressources dans votre abonnement,
* verrouillez les ressources importantes pour votre système,
* balisez les ressources afin de les suivre selon la valeur qu’elles ont pour votre organisation.

Cet article se concentre sur les tâches effectuez pour implémenter la gouvernance. Pour une description plus détaillée des concepts, consultez [Gouvernance dans Azure](../articles/security/governance-in-azure.md). 
