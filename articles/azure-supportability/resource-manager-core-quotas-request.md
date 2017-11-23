---
title: "Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager | Microsoft Docs"
description: "Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Demandes d’augmentation des quotas de processeurs virtuels pour Resource Manager

Les quotas de processeurs virtuels pour Resource Manager sont appliqués au niveau de la région et de la famille de références (SKU).
Pour en savoir plus sur la façon dont les quotas sont appliqués, consultez la page [Abonnement Azure et limites, quotas et contraintes de service](http://aka.ms/quotalimits).
Pour en savoir plus sur les familles de références, vous pouvez comparer le coût et les performances sur la page [Tarification des machines virtuelles](http://aka.ms/pricingcompute).

Pour demander une augmentation, créez un cas de support de quota pour les processeurs virtuels dans le portail Azure, [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Découvrez comment [créer une demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) dans le portail Azure.

1. Dans la nouvelle page de demande de support, sélectionnez le type de problème « Quota » et le type de quota « Cœurs ».

    ![Panneau Informations de base du quota](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Sélectionnez le modèle de déploiement « Resource Manager » et sélectionnez un emplacement.

    ![Panneau Problème de quota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Sélectionnez les familles de références qui nécessitent une augmentation.

    ![Série de références sélectionnée](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement.

    ![Nouvelle demande de quota de référence](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ».
Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur « Suivant » sur la page de l’étape Problème pour poursuivre la création de demande de support.
