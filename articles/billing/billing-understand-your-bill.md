---
title: Comprendre votre facture Azure | Microsoft Docs
description: "Apprenez à lire et à comprendre votre utilisation et la facturation de votre abonnement Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: ade40f8937d56cc561741e251095c4f14b4bb304
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendre votre facture Microsoft Azure
Pour comprendre votre facture Azure, comparez-la avec le fichier détaillé sur l’utilisation quotidienne et les rapports de gestion des coûts dans le portail Azure.

>[!NOTE]
>Cet article ne s’applique pas aux clients Contrat Entreprise (EA). Si vous êtes client EA, [la documentation de facturation est disponible sur le portail d’entreprise.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Pour obtenir un fichier PDF de votre facture et une copie du fichier CSV détaillant votre utilisation quotidienne, consultez [Obtenir votre facture Azure et vos données sur l’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md). 

Pour les termes et descriptions détaillés de votre facture et de votre fichier détaillé sur l’utilisation quotidienne, consultez [Comprendre les termes indiqués sur votre facture Microsoft Azure](billing-understand-your-invoice.md) et [Comprendre les termes indiqués sur le fichier Microsoft Azure sur l’utilisation détaillée](billing-understand-your-usage.md). 

Pour plus d’informations sur les rapports de gestion des coûts, consultez [Gestion des coûts sur le portail Azure](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="charges"></a>Comment m’assurer que les frais indiqués sur ma facture sont corrects ?
<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/3YegFD769Pk" frameborder="0" allowfullscreen></iframe>
</div>

Si vous voulez plus de détails sur des frais indiqués sur votre facture, vous disposez de deux options.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Option 1 : passez en revue votre facture et comparez l’utilisation et les coûts avec le fichier CSV sur l’utilisation détaillée.

Le fichier CSV sur l’utilisation détaillée indique vos frais par période de facturation et l’utilisation quotidienne. Pour obtenir le fichier CSV détaillant l’utilisation, consultez [Obtenir votre facture Azure et vos données d’utilisation quotidienne](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Vos frais d’utilisation sont affichés au niveau du compteur. Les termes suivants ont la même signification sur la facture et sur le fichier sur l’utilisation détaillée. Par exemple, le cycle de facturation sur la facture équivaut à la période de facturation indiquée dans le fichier sur l’utilisation détaillée.

 | Facture (PDF) | Utilisation détaillée (CSV)|
 | --- | --- |
|Cycle de facturation | Période de facturation |
 |Nom |Catégorie du compteur |
 |Type |Sous-catégorie du compteur |
 |Ressource |Nom du compteur |
 |Région |Région du compteur |
 |Consommé |Quantité consommée |
 |Inclus |Quantité incluse |
 |Facturable |Quantité de dépassement |

La section **Frais d’utilisation** de votre facture affiche la valeur totale de chaque compteur consommé pendant la période de facturation. Par exemple, la capture d’écran suivante montre les frais d’utilisation du service Azure Scheduler.

![Frais d’utilisation indiqués sur la facture](./media/billing-understand-your-bill/1.png)

La section **Relevé** de votre fichier CSV sur l’utilisation détaillée indique les mêmes frais. Le montant *Consommé* et la *Valeur* correspondent à la facture.

![Frais d’utilisation sur le fichier CSV](./media/billing-understand-your-bill/2.png)

Pour afficher une répartition quotidienne de ces frais, accédez à la section **Utilisation quotidienne** du fichier CSV. Filtrer sur « Scheduler » sous *Catégorie du compteur* ; vous pouvez ainsi voir quels jours le compteur a été utilisé et la quantité qui a été consommée. Les informations *Ressource* et *Groupe de ressources* sont également répertoriées à des fins de comparaison. La somme des valeurs *Consommé* doit être égale à ce qui est indiqué sur la facture.

![Section Utilisation quotidienne dans le fichier CSV](./media/billing-understand-your-bill/3.png)

Pour obtenir le coût par jour, multipliez les montants *Consommé* par la valeur *Tarif* dans la section **Relevé**.

Pour plus d’informations sur la facture, consultez [Comprendre votre facture Azure](billing-understand-your-invoice.md).

Pour en savoir plus sur chacune des colonnes du fichier CSV, consultez [Comprendre votre utilisation détaillée d’Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Option 2 : passez en revue votre facture et comparez-la avec l’utilisation et les coûts indiqués dans le portail Azure.

Le portail Azure peut également vous aider à vérifier vos frais. Il fournit des graphiques sur la gestion des coûts qui offrent une vue d’ensemble rapide de votre utilisation et des frais sur votre facture.

Pour continuer avec l’exemple ci-dessus, visitez la [page Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), sélectionnez votre abonnement, puis sélectionnez **Analyse du coût**. À partir de là, vous pouvez spécifier l’intervalle de temps et voir les frais d’utilisation du service Azure Scheduler.

![Vue de l’analyse des coûts dans le portail Azure](./media/billing-understand-your-bill/4.png)

Pour afficher la répartition quotidienne des coûts dans **Historique des coûts**, cliquez sur la ligne.

![Affichage de l’historique des coûts dans le portail Azure](./media/billing-understand-your-bill/5.png)

Pour en savoir plus, cliquez sur [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md#costs).

## <a name="external"></a>Quels sont les frais associés aux services externes ?
Les services externes (également appelés commandes de la Place de marché Microsoft Azure) sont fournis par les fournisseurs de services indépendants et sont facturés séparément. Les frais ne s’affichent pas sur votre facture Azure. Pour en savoir plus, consultez [Présentation de vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Comment effectuer un paiement ?

Si vous avez configuré une carte de crédit ou une carte bancaire comme moyen de paiement, le paiement est débité automatiquement dans les 10 jours qui suivent la fin de la période de facturation. Sur votre relevé de carte de crédit, le poste doit indiquer **MSFT Azure**.

Si vous [payez par facture](billing-how-to-pay-by-invoice.md), envoyez votre paiement à l’endroit indiqué au bas de celle-ci. Pour plus d’informations, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Comment vérifier le statut d’un paiement effectué par carte de crédit ?

[Créer un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour demander l’état de votre paiement. 

## <a name="tips-for-cost-management"></a>Conseils pour la gestion des coûts
- Estimez les coûts en utilisant la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) et la [calculatrice de coût total de possession](https://aka.ms/azure-tco-calculator), et obtenez les [informations de tarification détaillées pour chaque service](https://azure.microsoft.com/en-us/pricing/).
- [Configurez des alertes de facturation](billing-set-up-alerts.md).
- [Passez en revue régulièrement votre utilisation et vos coûts dans le portail Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
