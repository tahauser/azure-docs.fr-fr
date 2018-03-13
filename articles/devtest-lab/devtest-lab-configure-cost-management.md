---
title: "Affichage des tendances de coût de laboratoire mensuelles estimées dans Azure DevTest Labs | Microsoft Docs"
description: "En savoir plus sur le graphique des tendances des coûts mensuels estimés Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: v-craic
ms.openlocfilehash: 660180fac4f4743bc543b1babf7dbe921bf8c26b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Affichage des tendances de coût de laboratoire mensuelles estimées dans Azure DevTest Labs
La fonctionnalité de gestion des coûts de DevTest Labs vous permet de suivre le coût de votre labo. Cet article explique comment utiliser le graphique **Tendance des coûts mensuels estimés** pour afficher le coût estimé à ce jour pour le mois civil en cours, ainsi que le coût projeté pour la fin du mois civil en cours. Cet article explique également comment mieux gérer les coûts de laboratoire en définissant des seuils et des dépenses cibles qui, une fois atteints, déclenchent la génération de résultats par DevTest Labs.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Affichage du graphique Tendance des coûts mensuels estimés
Pour afficher le graphique Tendance des coûts mensuels estimés, procédez comme suit : 

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Si nécessaire, sélectionnez **Autres services**, puis **DevTest Labs** dans la liste. (Votre lab peut déjà être affiché dans le Tableau de bord sous **Toutes les ressources**).
1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
1. Dans la zone **Vue d’ensemble** du laboratoire, sélectionnez **Configuration et stratégies**.   
1. À gauche, sous **SUIVI DES COÛTS**, sélectionnez **Tendance des coûts**.

   La capture d'écran suivante montre un exemple de graphique de coûts. 
   
    ![Graphique de coûts](./media/devtest-lab-configure-cost-management/graph.png)

La valeur **Coût estimé** est le coût estimé à ce jour pour le mois calendaire en cours. Le **Coût projeté** est le coût estimé pour le mois calendaire entier, calculé à l’aide du coût du laboratoire lors des cinq derniers jours.

Les montants des coûts sont arrondis à l’entier supérieur. Par exemple :  

* 5,01 est arrondi à 6 
* 5,50 est arrondi à 6
* 5,99 est arrondi à 6

Comme indiqué au-dessus du graphique, les coûts que vous voyez par défaut dans le graphique sont les coûts *estimés* avec les tarifs de l’offre [Paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Vous pouvez également définir vos propres dépenses cibles à afficher dans les graphiques en [gérant les coûts cibles de votre laboratoire](#managing-cost-targets-for-your-lab).

Par ailleurs, les éléments suivants ne sont *pas* inclus dans le calcul des coûts :

* Les abonnements CSP et Dreamspark ne sont pas pris en charge. En effet, Azure DevTest Labs utilise les [API de facturation Azure](../billing/billing-usage-rate-card-overview.md) pour calculer les coûts de laboratoire, et celles-ci ne prennent pas en charge les abonnements CSP et Dreamspark.
* Les tarifs de votre offre. Actuellement, vous ne pouvez pas utiliser les tarifs de l’offre (affichés sous votre abonnement) que vous avez négociés avec Microsoft ou les partenaires Microsoft. Seuls les tarifs du paiement à l'utilisation sont disponibles.
* Vos taxes
* Vos remises
* Votre devise de facturation. Actuellement, le coût du labo s'affiche uniquement dans la devise USD.

## <a name="managing-cost-targets-for-your-lab"></a>Gestion des coûts cibles de votre laboratoire
DevTest Labs vous aide à mieux gérer les coûts de votre laboratoire en définissant des dépenses cibles que vous pouvez ensuite afficher dans le graphique de tendance du coût estimé par mois. DevTest Labs peut également vous envoyer une notification quand le seuil ou les dépenses cibles spécifiés sont atteints. 

1. Dans le volet **Vue d’ensemble** de votre laboratoire, sélectionnez **Configuration et stratégies**.
1. À gauche, sous **SUIVI DES COÛTS**, sélectionnez **Tendance des coûts**.

    ![Bouton Gérer la cible](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. Dans le volet **Tendance des coûts**, sélectionnez **Gérer la cible**.

    ![Bouton Gérer la cible](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. Dans le volet Gérer la cible, spécifiez les dépenses cibles et les seuils souhaités. Vous pouvez également indiquer si chaque seuil sélectionné doit figurer sur le graphique de tendance du coût ou être communiqué via une notification Webhook.

    ![Volet Gérer la cible](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Sélectionnez la période de temps durant laquelle vos coûts cibles doivent être suivis.
      - **Tous les mois** : le suivi des coûts cibles est effectué mensuellement.
      - **Fixe** : le suivi des coûts cibles est effectué dans la plage de dates que vous spécifiez dans les champs Date de début et Date de fin. En règle générale, cette période peut correspondre à la durée d’exécution prévue de votre projet.
   - Spécifiez un **coût cible**. Par exemple, cela peut être le montant que vous prévoyez de dépenser pour ce laboratoire dans la période de temps que vous avez définie.
   - Sélectionnez l’option appropriée pour activer ou désactiver un seuil à rapporter (par incréments de 25 %), jusqu’à 125 % du **coût cible** spécifié.
      - **Notifier** : quand ce seuil est atteint, vous êtes averti par une URL Webhook que vous spécifiez.
      - **Tracer sur le graphique** : quand ce seuil est atteint, les résultats sont tracés sur le graphique de tendance du coût que vous pouvez afficher, comme décrit dans [Affichage du graphique de tendance du coût estimé mensuel](#viewing-the-monthly-estimated-cost-trend-chart).
   - Si vous choisissez l’option **Notifier** pour l’envoi d’une notification quand le seuil est atteint, vous devez spécifier une URL Webhook. Dans la zone Intégrations des coûts, sélectionnez **Cliquez ici pour ajouter une intégration**.

      Entrez une URL Webhook dans le volet Configurer la notification, puis sélectionnez **OK**.

       ![Volet Configurer la notification](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Si vous sélectionnez l’option **Notifier**, vous devez définir une URL Webhook.
      - De même, si vous définissez une URL Webhook, vous devez définir le paramètre **Notification** sur **Activé** dans le volet Seuil de coût.
      - Vous devez créer un Webhook avant de l’indiquer ici.  

      Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Two more things to keep your cost on track in DevTest Labs (Deux autres astuces pour maîtriser vos coûts dans DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds? (Pourquoi définir des seuils de coût ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Étapes suivantes
Voici quelques possibilités d’opérations pour la suite :

* [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md) : apprenez à définir les différentes stratégies utilisées pour gérer l’utilisation de votre laboratoire et de ses machines virtuelles. 
* [Créer une image personnalisée](devtest-lab-create-template.md) : quand vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
* [Configurer des images Marketplace](devtest-lab-configure-marketplace-images.md) : DevTest Labs prend en charge la création de machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de machines virtuelles dans un laboratoire.
* [Créer une machine virtuelle dans un laboratoire](devtest-lab-add-vm.md) : montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Place de marché) et comment utiliser des artefacts dans votre machine virtuelle.

