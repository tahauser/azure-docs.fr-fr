---
title: "Comprendre l’utilisation de l’offre d’instance réservée Azure sur votre abonnement avec paiement à l’utilisation | Microsoft Docs"
description: "Découvrez comment analyser votre utilisation pour comprendre l’application de l’offre d’instance réservée sur l’abonnement de paiement à l’utilisation."
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
ms.openlocfilehash: 29f153803d5eb74e2d287d97cf9436e81b2a3e20
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation

Comprendre l’utilisation de l’offre d’instance réservée en utilisant l’ID de réservation dans la [page Réservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) et dans le fichier d’utilisation du [portail des comptes Azure](https://account.azure.com).


>[!NOTE]
>Cet article ne s’applique pas aux clients Contrat Entreprise. Si vous êtes un client Contrat Entreprise, consultez [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise.](billing-understand-reserved-instance-usage-ea.md) Cet article suppose également que la réservation s’applique à un seul abonnement. Si la réservation s’applique à plusieurs abonnements, la remise de réservation peut s’étendre à plusieurs fichiers csv d’utilisation. 

Pour la section suivante, supposez que vous exécutez une machine virtuelle Windows Standard_DS1_v2 dans la région États-Unis de l’Est, et que vos informations de réservation ressemblent au contenu du tableau suivant :

| Champ | Valeur |
|---| :---: |
|ID de réservation |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantité |1|
|SKU | Standard_DS1_v2|
|Région | eastus |

## <a name="reservation-application"></a>Application de la réservation

La partie matérielle de la machine virtuelle est traitée, car la machine virtuelle déployée correspond aux attributs de la réservation. Pour savoir quels logiciels Windows ne sont pas couverts par l’offre d’instance réservée, accédez à [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv"></a>Section Relevés du fichier csv
Cette section de votre fichier csv affiche l’utilisation totale de votre réservation. Appliquez le filtre sur le champ Sous-catégorie du compteur qui contient « Réservation- » pour que vos données ressemblent à la capture d’écran suivante : ![Relevé direct Réservation](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

La ligne Instances réservées-Machine virtuelle de base indique le nombre total d’heures qui sont couvertes par la réservation. Cette ligne affiche 0,00 dollars US, car l’Instance réservée la couvre. La ligne Réservation-Windows Server (1 cœur) couvre le coût des logiciels Windows.

### <a name="daily-usage-section-of-csv"></a>Section Utilisation quotidienne du fichier csv
Filtrez sur des informations supplémentaires et tapez votre ID de réservation. La capture d’écran suivante affiche les champs associés à la réservation. 

![Frais liés à l’utilisation quotidienne](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. L’ID de réservation dans le champ Infos supplémentaires représente la réservation qui a été utilisée pour appliquer la remise à la machine virtuelle.
2. Le Compteur de consommation est l’Id du compteur de la machine virtuelle.
3. La ligne Instances réservées-Machine virtuelle de base dans Sous-catégorie du compteur représente la ligne du coût 0 dollars US dans la section du relevé. Le coût d’exécution de cette machine virtuelle est déjà payé par la réservation.
4. Il s’agit de l’Id du compteur de la réservation. Le coût de ce compteur est de 0 dollars US. Toute machine virtuelle bénéficiant d’une offre d’instance réservée affiche cet ID de compteur dans le fichier csv afin de renseigner sur le coût. 
5. Standard_DS1_v2 est une machine virtuelle à processeur virtuel qui est déployée sans Azure Hybrid Benefit. Par conséquent, ce compteur couvre les frais supplémentaires des logiciels Windows. Consultez [Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure](billing-reserved-instance-windows-software-costs.md) pour trouver le compteur correspondant à la machine virtuelle série D 1 cœur. Si Azure Hybrid Benefit est utilisé, ce coût supplémentaire n’est pas appliqué. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances de machine virtuelle réservées, voir les articles suivants.

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administrer les instances de machine virtuelle réservées Azure](billing-manage-reserved-vm-instance.md)
- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](billing-save-compute-costs-reservations.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.