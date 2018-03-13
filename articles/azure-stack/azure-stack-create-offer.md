---
title: "Créer une offre dans Azure Stack | Microsoft Docs"
description: "En tant qu’administrateur cloud, apprenez à créer une offre pour vos utilisateurs dans Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 2666aacbbaf44ae9b3bcf2df480e835457e6f449
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Créer une offre dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les [offres](azure-stack-key-features.md) sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à l’achat ou à l’abonnement aux utilisateurs. Ce document montre comment créer une offre comprenant le [plan créé](azure-stack-create-plan.md) à la dernière étape. Cette offre donne aux abonnés la possibilité d’approvisionner des machines virtuelles.

1. Connectez-vous au portail d’administration Azure Stack (https://adminportal.local.azurestack.external) > cliquez sur **Nouveau** > **Plans + offres clients** > **Offre**.

   ![](media/azure-stack-create-offer/image01.png)
2. Sur le volet **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources** nouveau ou existant. Le nom d’affichage correspond au nom convivial de l’offre. Ce nom convivial est la seule information sur l’offre que les utilisateurs verront au moment de l’abonnement. Par conséquent, veillez à utiliser un nom intuitif qui aide l’utilisateur à comprendre en quoi elle consiste. Seul l’administrateur peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer l’offre en tant que ressource Azure Resource Manager.

   ![](media/azure-stack-create-offer/image01a.png)
3. Cliquez sur **Plans de base** pour ouvrir le volet **Plan**, sélectionnez les plans que vous souhaitez inclure à l’offre, puis cliquez sur **Sélectionnez**. Cliquez sur **Créer** pour créer l’offre.

   ![](media/azure-stack-create-offer/image02.png)
4. Cliquez sur **Toutes les ressources**, recherchez votre nouvelle offre, cliquez dessus, puis sur **Changer d’état** et enfin sur **Public**.

   ![](media/azure-stack-create-offer/image03.png)

Les offres doivent être rendues publiques pour permettre aux utilisateurs d’avoir une vue d’ensemble lors de l’abonnement. Les offres peuvent être :

* **Public** : ils sont visibles pour les utilisateurs.
* **Privé** : visibles uniquement par les administrateurs cloud. Utile lors de l’élaboration du plan ou de l’offre, ou si l’administrateur cloud souhaite [créer chaque abonnement pour les utilisateurs](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
* **Retiré**: ils sont fermés aux nouveaux abonnés. L’administrateur cloud peut utiliser cet état pour empêcher tout abonnement futur, sans que cela affecte les abonnés actuels.

Les modifications apportées à l’offre ne sont pas immédiatement visibles par l’utilisateur. Vous risquez d’avoir à vous déconnecter puis à vous reconnecter pour voir le nouvel abonnement dans le « sélecteur d’abonnement » lors de la création de ressources ou de groupes de ressources.

> [!NOTE]
>Vous pouvez également créer des offres, des plans et des quotas par défaut avec PowerShell, comme l’explique le [Lisez-moi de l’administrateur de services fédérés Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>Étapes suivantes
[Création d’abonnements](azure-stack-subscribe-plan-provision-vm.md)      
[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
