---
title: Testez votre offre de machine virtuelle pour Marketplace | Microsoft Docs
description: Découvrez comment tester votre offre de machine virtuelle pour Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mbaldwin
ms.openlocfilehash: 7f225f113dd465f79d5d195b59a809fe3be6a1f8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Test de votre offre de machine virtuelle pour Azure Marketplace en mode intermédiaire
Dans le cadre du déploiement dans un environnement intermédiaire, vous déployez votre référence SKU dans un « bac à sable » (sandbox) privé dans lequel vous pouvez tester et vérifier ses fonctionnalités avant son déploiement dans Marketplace. La référence apparaît avec le statut Intermédiaire, comme pour un client l’ayant déployée. Votre image de machine virtuelle doit être certifiée pour passer en mode Intermédiaire.

## <a name="step-1-push-your-offer-to-staging"></a>Étape 1 : déployez votre offre dans un environnement intermédiaire
1. Dans l'onglet **Publish**, cliquez sur **Push to Staging**.
   
    ![drawing](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Si le portail de publication vous signale des erreurs, corrigez-les.
3. Dans la boîte de dialogue **Who can access your staged offer?** , saisissez la liste des abonnements Azure que vous allez utiliser pour afficher un aperçu de votre offre dans le [portail Azure en version préliminaire](https://portal.azure.com).
   
   > [!NOTE]
   > Dans le cas de machines virtuelles et de modèles de solutions, veuillez **ne pas** mettre sur liste approuvée des abonnements de type CSP, DreamSpark ou Azure dans Open.
   > 
   > 

    > Dans le cas de machines virtuelles, lorsque vous cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**, les étapes suivantes sont effectuées en arrière-plan. Vous pourrez voir la progression de chaque étape sous l’onglet PUBLIER du portail de publication. Vous devez vérifier cette page à intervalles réguliers (jusqu’à ce que l’état affiche EN MODE INTERMÉDIAIRE) en cas d’apparition d’informations d’échec nécessitant une correction de votre part.

    > - Dans un premier temps, votre demande de déploiement dans un environnement intermédiaire est transmise à l’équipe de certification, qui valide le disque dur virtuel. Toutefois, si votre demande ne contient que des modifications d’ordre marketing, l’étape de certification est ignorée.
    > - Une fois la certification terminée, la réplication de l’offre commence sur tous les centres de données Azure. En règle générale, il faut 24 à 48 heures pour que la réplication soit complète, mais elle peut prendre jusqu’à une semaine selon la taille du disque dur virtuel. Toutefois, si votre demande ne contient que des modifications d’ordre marketing, la réplication est plus rapide.
    > - Une fois la réplication terminée, l’offre sera répertoriée sur le [portail Azure](http:/portal.azure.com). À ce stade, l’état devient EN MODE INTERMÉDIAIRE dans le portail de publication. Une offre en mode intermédiaire est visible dans le [portail Azure](http:/portal.azure.com) en utilisant uniquement le(s) identifiant(s) de messagerie associé(s) à l’abonnement avec lequel l’offre est mise en mode intermédiaire.

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com) à l'aide de l'un des abonnements Azure répertoriés dans l'étape précédente.
2. Recherchez votre offre et validez vos points d'image de machine virtuelle :
   
   * Assurez-vous que le contenu marketing s’affiche correctement sur Marketplace.
   * Déploiement de bout en bout de l'image de machine virtuelle.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Votre offre restera en mode Intermédiaire jusqu’à ce que vous informiez Microsoft au moyen du portail de publication [onglet **Publier** > cliquez sur le bouton **« Demander une approbation pour lancer la production »**] que vous êtes prêt à lancer la production. À ce stade, il est souhaitable que les membres de votre équipe procèdent à des vérifications en vue de la préparation du listing de votre offre.
> 
> La plateforme intermédiaire est conçue pour tester l’offre en mode Aperçu par l’éditeur. Nous déconseillons fortement d’utiliser cette plateforme à des fins commerciales.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre offre est en mode Intermédiaire et que vous avez testé sa fonctionnalité et son contenu marketing, vous pouvez passer à la phase de publication finale, **Étape 4**: [déploiement de votre offre sur Marketplace](marketplace-publishing-push-to-production.md)

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

