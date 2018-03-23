---
title: Utilisation du portail Azure Stack | Microsoft Docs
description: Découvrez comment accéder au portail utilisateur et l’utiliser dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 7c34d7a225be63da95f664525b0366ff89b28838
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-azure-stack-portal"></a>Utilisation du portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant que consommateur des services Azure Stack, vous pouvez utiliser le portail Azure Stack pour vous abonner à des offres publiques et utiliser les services dispensés via ces offres. Si vous avez déjà utilisé le portail Azure, l’interface utilisateur vous semblera familière.

## <a name="access-the-portal"></a>Accéder au portail

Votre opérateur Azure Stack (fournisseur de services ou administrateur au sein de votre organisation) vous permet de connaître l’URL correcte pour accéder au portail. 

- Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, et son format est https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
- Si vous utilisez le Kit de développement Azure Stack, l’adresse du portail est https://portal.local.azurestack.external.

![Capture d’écran du portail utilisateur d’Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord

Le tableau de bord contient une série de vignettes par défaut. Vous pouvez cliquer sur **Modifier le tableau de bord** pour modifier le tableau de bord par défaut, ou sur **Nouveau tableau de bord** pour ajouter des tableaux de bord personnalisés. Vous pouvez facilement ajouter des vignettes au tableau de bord. Par exemple, vous pouvez cliquer sur **Nouveau**, cliquer avec le bouton droit sur **Compute**, puis cliquer sur **Épingler au tableau de bord**.

## <a name="create-subscription-and-browse-available-resources"></a>Créer un abonnement et parcourir les ressources disponibles
 
Si vous n’avez pas déjà un abonnement, la première chose à faire est de s’abonner à une offre. Après cela, vous pouvez parcourir les ressources à votre disposition. Pour rechercher et créer des ressources, effectuez l’une des opérations suivantes :

- Cliquez sur la vignette **Place de marché** du tableau de bord. 
- Dans la vignette **Toutes les ressources**, cliquez sur **Créer des ressources**.
- Dans le volet de navigation gauche, cliquez sur **Nouveau**.

## <a name="learn-how-to-use-available-services"></a>Découvrir comment utiliser les services disponibles

Si vous avez besoin d’aide pour savoir comment utiliser les services disponibles, différentes options peuvent être à votre disposition.

- Votre organisation ou votre fournisseur de services peut offrir sa propre documentation. Cela est particulièrement vrai s’ils offrent des services ou applications personnalisés.
- Les applications tierces ont leur propre documentation.
- Pour les services cohérents avec Azure, nous recommandons vivement de consulter la documentation d’Azure Stack. Pour accéder à la documentation d’Azure Stack, cliquez sur l’icône Aide, puis sur **Aide + support**.
 
    ![Capture d’écran de l’option Aide + support dans l’interface utilisateur](media/azure-stack-use-portal/HelpAndSupport.png)

    Nous vous suggérons en particulier de consulter les articles suivants pour commencer :

    - [Considérations importantes : utilisation de services ou création d’applications pour Azure Stack](azure-stack-considerations.md)
    - La section « Utiliser les services » de la documentation répertorie les services cohérents avec Azure. Il existe une rubrique « Considérations » pour chaque service, qui décrit les écarts entre le service proposé dans Azure et le service équivalent dans Azure Stack. Pour obtenir un exemple, voir [Considérations relatives aux machines virtuelles](azure-stack-vm-considerations.md). D’autres informations peuvent figurer dans la section « Utiliser les services » propre à Azure Stack. 
     
      Vous pouvez utiliser la documentation Azure en tant que référence générale pour un service, mais devez avoir conscience de ces différences. Comprenez que les liens de documentation dans la vignette **Tutoriels de prise en main** pointent sur la documentation Azure.

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez besoin d’une assistance supplémentaire, contactez votre organisation ou votre fournisseur de services pour obtenir de l’aide. 

Si vous utilisez le Kit de développement Azure Stack, le [Forum sur Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) est la seule source d’assistance.

## <a name="next-steps"></a>Étapes suivantes

[Considérations importantes : utilisation de services ou création d’applications pour Azure Stack](azure-stack-considerations.md)
