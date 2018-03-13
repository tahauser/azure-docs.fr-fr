---
title: "Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide du portail Azure."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: b9b4275497798db5310f2a28aeb6c582b1a7429c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide du portail Azure

Cet article décrit les étapes de création d’une instance publique de [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) avec un frontend redondant dans une zone qui utilise une adresse IP publique Standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>S’inscrire pour bénéficier de la préversion des zones de disponibilité, de Load Balancer Standard et des adresses IP publiques Standard
 
>[!NOTE]
La [référence SKU de Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) est actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour vos services de production, utilisez la [référence SKU De base de Load Balancer](load-balancer-overview.md) de la version publique. 

> [!NOTE]
> Les zones de disponibilité sont en préversion et sont préparées pour vos scénarios de développement et de test. La prise en charge est fournie pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Avant de sélectionner une zone ou une option de redondance dans une zone pour l’adresse IP publique frontend de Load Balancer, vous devez d’abord suivre les étapes de la section [S’inscrire à la préversion des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure à l’adresse https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Créer un équilibreur de charge redondant dans une zone

1. Dans un navigateur, accédez au portail Azure : [http://portal.azure.com](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibrage de charge**.
3. Dans Créer un équilibreur de charge, sous **Nom**, tapez **myPublicLB**.
4. Sous **Type**, sélectionnez **Public**.
5. Sous Référence SKU, cliquez sur **Standard (version préliminaire)**.
6. Cliquez sur **Adresse IP publique**, puis sur **Créer**. Dans la page **Créer une adresse IP publique**, sous Nom, tapez **myPublicIPStandard**, et pour **Zone de disponibilité (préversion)**, sélectionnez **Redondant dans une zone**.
7. Sous **Emplacement**, sélectionnez **Est des États-Unis 2**, puis cliquez sur **OK**. L’équilibreur de charge commence ensuite le déploiement, qui peut prendre plusieurs minutes.

    ![Créer une instance de Load Balancer Standard redondante dans une zone à l’aide du portail Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [créer une adresse IP publique dans une zone de disponibilité](../virtual-network/create-public-ip-availability-zone-portal.md).



