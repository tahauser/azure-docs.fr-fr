---
title: Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide du portail Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide du portail Azure

Cet article décrit les étapes de création d’une instance publique de [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) avec un frontend redondant dans une zone qui utilise une adresse IP publique Standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="register-for-availability-zones-preview"></a>S’inscrire pour un aperçu des Zones de disponibilité
 
Les zones de disponibilité sont en préversion et sont préparées pour vos scénarios de développement et de test. La prise en charge est fournie pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Créer un équilibreur de charge redondant dans une zone

1. Dans un navigateur, accédez au portail Azure [http://portal.azure.com](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibrage de charge**.
3. Dans Créer un équilibreur de charge, sous **Nom**, tapez **myPublicLB**.
4. Sous **Type**, sélectionnez **Public**.
5. Sous Référence SKU, cliquez sur **Standard (version préliminaire)**.
6. Cliquez sur **Adresse IP publique**, puis sur **Créer**. Dans la page **Créer une adresse IP publique**, sous Nom, tapez **myPublicIPStandard**, et pour **Zone de disponibilité (préversion)**, sélectionnez **Redondant dans une zone**.
7. Sous **Emplacement**, sélectionnez **Est des États-Unis 2**, puis cliquez sur **OK**. L’équilibreur de charge commence ensuite le déploiement, qui peut prendre plusieurs minutes.

    ![Créer une instance de Load Balancer Standard redondante dans une zone à l’aide du portail Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [créer une adresse IP publique dans une zone de disponibilité](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).



