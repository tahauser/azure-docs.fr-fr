---
title: "Comprendre l’utilisation de l’offre d’instance réservée Azure pour l’Entreprise | Microsoft Docs"
description: "Découvrez comment analyser votre utilisation pour comprendre l’application de l’offre d’instance réservée sur l’inscription de votre entreprise."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 515eae3c9a84a171bebc5213f5824e1b50336e34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise
Comprendre l’utilisation de l’offre d’instance réservée en utilisant l’ID de réservation dans la [page Réservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) et dans le fichier d’utilisation du [portail EA.](https://ea.azure.com) Vous pouvez également consulter l’utilisation de la réservation dans la section récapitulative de l’utilisation du [portail EA](https://ea.azure.com).

>[!NOTE]
>Si vous avez acheté la réservation dans un contexte de facturation avec paiement à l’utilisation, consultez [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)

Pour la section suivante, supposez que vous exécutez une machine virtuelle Windows Standard_D1_v2 dans la région États-Unis de l’Est, et que vos informations de réservation ressemblent au contenu du tableau suivant :

| Champ | Valeur |
|---| --- |
|ID de réservation |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantité |1|
|SKU | D1 standard|
|Région | eastus |

## <a name="reservation-application"></a>Application de la réservation

La partie matérielle de la machine virtuelle est traitée, car la machine virtuelle déployée correspond aux attributs de la réservation. Pour savoir quels logiciels Windows ne sont pas couverts par l’offre d’instance réservée, accédez à [Coûts des logiciels Windows dans Azure Reserved VM Instances](billing-reserved-instance-windows-software-costs.md).


### <a name="reservation-usage-in-csv"></a>Utilisation de la réservation dans le fichier csv
Vous pouvez télécharger le fichier csv de l’utilisation partagée du Contrat Entreprise à partir du portail EA. Dans le fichier csv téléchargé, filtrez sur des informations supplémentaires et tapez votre ID de réservation. La capture d’écran suivante affiche les champs associés à la réservation :

![Fichier csv Contrat Entreprise pour l’offre d’instance réservée](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. L’ID de réservation dans le champ Infos supplémentaires représente la réservation qui a été utilisée pour appliquer la remise à la machine virtuelle.
2. Le Compteur de consommation est l’Id du compteur de la machine virtuelle.
3. Il s’agit du Compteur de la réservation, avec un coût affiché de 0 dollars US puisque le coût d’exécution de la machine virtuelle est déjà payé par la réservation. 
4. Standard_D1 est une machine virtuelle à processeur virtuel qui est déployée sans Azure Hybrid Benefit. Par conséquent, ce compteur couvre les frais supplémentaires des logiciels Windows. Consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md) pour trouver le compteur correspondant à la machine virtuelle série D 1 cœur. Si Azure Hybrid Benefit est utilisé, ce coût supplémentaire n’est pas appliqué.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Utilisation de la réservation dans la page récapitulative de l’utilisation sur le portail EA

L’utilisation de l’offre d’instance réservée s’affiche également dans le portail EA, à la section du résumé de l’utilisation : ![Récapitulatif de l’utilisation EA](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Vous n’êtes pas facturé pour le composant matériel de la machine virtuelle, car il est couvert par l’offre d’instance réservée. 
2. Vous êtes facturé pour les logiciels Windows puisque Azure Hybrid Benefit n’est pas utilisé. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances de machine virtuelle réservées, voir les articles suivants.

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administrer les instances de machine virtuelle réservées Azure](billing-manage-reserved-vm-instance.md)
- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](billing-save-compute-costs-reservations.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.