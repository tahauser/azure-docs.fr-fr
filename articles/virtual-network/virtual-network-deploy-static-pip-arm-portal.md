---
title: "Créer une machine virtuelle avec une adresse IP publique statique - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 53e9b55a82e7750393dfb6c4818681028c5c4d2f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Créer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [interface de ligne de commande Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modèle](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (classique)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Créer une machine virtuelle avec une adresse IP publique statique

Pour créer une machine virtuelle avec une adresse IP publique statique dans le portail Azure, procédez comme suit :

1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Dans le coin supérieur gauche du portail, cliquez sur **Créer une ressource**>>**Compute**>**Windows Server 2012 R2 Datacenter**.
3. Dans la liste **Sélectionner un modèle de déploiement**, sélectionnez **Resource Manager**, puis cliquez sur **Créer**.
4. Dans le volet **Informations de base**, entrez les informations de la machine virtuelle comme suit, puis cliquez sur **OK**.
   
    ![Portail Azure - De base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. Dans le volet **Choisir une taille**, cliquez sur **A1 Standard** de la façon suivante, puis cliquez sur **Sélectionner**.
   
    ![Portail Azure - Choisir une taille](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. Dans le volet **Paramètres**, cliquez sur **Adresse IP publique** puis, dans le volet **Créer une adresse IP publique**, sous **Affectation**, cliquez sur **Statique** comme suit. Cliquez ensuite sur **OK**.
   
    ![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. Dans le volet **Paramètres**, cliquez sur **OK**.
8. Examinez le volet **Résumé**, en procédant comme suit, puis cliquez sur **OK**.
   
    ![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Notez la nouvelle vignette dans votre tableau de bord.
   
    ![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Lorsque la machine virtuelle est créée, le volet **Paramètres** s’affiche de la façon suivante :
    
    ![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

