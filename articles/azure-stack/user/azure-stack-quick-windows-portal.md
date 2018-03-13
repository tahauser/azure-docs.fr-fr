---
title: "Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Windows"
description: "Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Windows avec le portail"
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/15/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: 9df976fd26318038ab4152863638e262d7f284aa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Créer une machine virtuelle Windows avec le portail Azure Stack

Vous pouvez créer une machine virtuelle Windows dans le portail Azure Stack. Le portail est une interface utilisateur basé sur le navigateur grâce à laquelle vous pouvez créer, configurer et gérer les ressources.

## <a name="sign-in-to-the-azure-stack-portal"></a>Se connecter au portail Azure Stack

Connectez-vous au portail Azure Stack. L’adresse du portail Azure Stack varie en fonction du produit Azure Stack auquel vous vous connectez :

* Pour le Kit de développement Azure Stack (ASDK), accédez à https://portal.local.azurestack.external.
* Pour un système intégré Azure Stack, accédez à l’URL fournie par votre opérateur Azure Stack.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Dans le portail, cliquez sur **Nouveau** > **Calcul** > **Windows Server 2016 Datacenter Eval** > **Créer**. Si vous ne voyez pas l’entrée **Windows Server 2016 Datacenter Eval**, contactez votre opérateur Azure Stack. Demandez-lui de l’ajouter à la Place de Marché comme expliqué dans l’article [Ajouter l’image de machine virtuelle Windows Server 2016 à la Place de Marché Azure Stack](../azure-stack-add-default-image.md). 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. Sous **De base**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**. Choisissez un **Abonnement**. Créez un **Groupe de ressources** ou sélectionnez un groupe existant, sélectionnez un **Emplacement**, puis cliquez sur **OK**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. Sous **Choisir une taille**, cliquez sur **D1 Standard** > **Sélectionner**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. Sous **Paramètres**, acceptez les valeurs par défaut et cliquez sur **OK**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. Sous **Résumé**, cliquez sur **OK** pour créer la machine virtuelle. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Pour voir votre nouvelle machine virtuelle, cliquez sur **Toutes les ressources**, puis recherchez la machine virtuelle et cliquez sur son nom.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin de la machine virtuelle, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir de la page de la machine virtuelle, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows simple. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
