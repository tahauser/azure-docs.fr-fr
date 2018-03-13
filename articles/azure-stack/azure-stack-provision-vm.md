---
title: "Créer une machine virtuelle de test dans Azure Stack | Microsoft Docs"
description: "Découvrez comment approvisionner une machine virtuelle dans Azure Stack en tant qu’opérateur cloud."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 41096f68e5e7d9e31098d1e8919101418abe4c03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Créer une machine virtuelle de test dans Azure Stack

*S’applique au Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, vous pouvez créer une machine virtuelle de test pour valider votre déploiement du kit de développeur [Azure Stack](azure-stack-poc.md).

> [!NOTE]
> Avant de pouvoir approvisionner des machines virtuelles, vous devez [ajouter l’image d’évaluation de Windows Server 2016 à la Place de marché Azure Stack](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
1. Sur l’hôte du Kit de développement Azure Stack, [connectez-vous](azure-stack-connect-azure-stack.md) au portail d’administration (`https://adminportal.local.azurestack.external`), puis cliquez sur **Nouveau** > **Calcul** > **Windows Server 2016 Datacenter Evaluation** > **Créer**.  
2. Dans le panneau **Informations de base**, entrez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**. Choisissez un **Abonnement**. Créez un **Groupe de ressources** ou sélectionnez un groupe existant, puis cliquez sur **OK**.  
3. Dans le panneau **Choisir une taille**, cliquez sur **A1 Standard**, puis cliquez sur **Sélectionner**.  
4. Dans le panneau **Paramètres**, acceptez les valeurs par défaut et cliquez sur **OK**.
5. Dans le panneau **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.  
6. Pour voir votre nouvelle machine virtuelle, cliquez sur **Toutes les ressources**, puis recherchez la machine virtuelle et cliquez sur son nom.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>Étapes suivantes
[Utilisation des portails administrateur et utilisateur dans Azure Stack](azure-stack-manage-portals.md)
