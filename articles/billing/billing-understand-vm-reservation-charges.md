---
title: "Comprendre l’application de la remise de l’offre Azure Reserved Virtual Machine Instances | Microsoft Docs"
description: "Découvrez comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée aux machines virtuelles en cours d’exécution."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 2a3854077c7c8bdb20804c6b3e77500659c3c484
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée
Dès que vous achetez une instance de machine virtuelle réservée, la remise de réservation est automatiquement appliquée aux machines virtuelles correspondant aux attributs et à la quantité de la réservation. Une réservation couvre les coûts d’infrastructure de vos machines virtuelles. Le tableau suivant illustre les coûts de votre machine virtuelle après l’achat d’une réservation. Dans tous les cas, vous êtes facturé pour le stockage et la mise en réseau selon les tarifs normaux.

| Type de machine virtuelle  | Frais associés à la réservation |    
|-----------------------|--------------------------------------------| 
|Machines virtuelles Linux sans logiciel supplémentaire | La réservation couvre les coûts d’infrastructure des machines virtuelles.|
|Machines virtuelles Linux avec frais de logiciel (par exemple, Red Hat) | La réservation couvre les coûts d’infrastructure. Vous êtes facturé pour les logiciels supplémentaires.|
|Machines virtuelles Windows sans logiciel supplémentaire |La réservation couvre les coûts d’infrastructure. Vous êtes facturé pour les logiciels Windows.|
|Machines virtuelles Windows avec logiciels supplémentaires (par exemple, SQL server) | La réservation couvre les coûts d’infrastructure. Vous êtes facturé pour les logiciels Windows et les logiciels supplémentaires.|
|Machines virtuelles Windows avec [Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | La réservation couvre les coûts d’infrastructure. Les coûts des logiciels Windows sont couverts par Azure Hybrid Benefit. Tout logiciel supplémentaire est facturé séparément.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Application de la remise de réservation aux machines virtuelles non Windows
 La remise de réservation est appliquée aux instances de machine virtuelle en cours d’exécution sur une base horaire. Les réservations que vous avez achetées sont mises en correspondance avec l’utilisation émise par les machines virtuelles en cours d’exécution pour appliquer la remise de réservation. Pour les machines virtuelles qui ne peuvent pas s’exécuter pendant une heure entière, la réservation est remplie à partir d’autres machines virtuelles n’utilisant pas de réservation, incluant des machines virtuelles s’exécutant simultanément. À la fin de l’heure, l’application de réservation pour les machines virtuelles dans l’heure est verrouillée. Quand une machine virtuelle ne s’exécute pas pendant une heure ou quand des machines virtuelles s’exécutant simultanément dans l’heure ne remplissent pas l’heure de réservation, la réservation est sous-utilisée pour cette heure. Le graphique suivant illustre l’application d’une réservation à l’utilisation de machines virtuelles facturables. La représentation est basée sur l’achat d’une réservation et sur deux instances de machine virtuelle correspondantes.

![Application de l’instance de machine virtuelle réservée](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Toute utilisation se situant au-dessus de la ligne de l’instance de machine virtuelle réservée est facturée selon le tarif standard de paiement à l’utilisation. Aucune facturation n’est appliquée à l’utilisation située sous cette ligne, car elle a déjà été payée dans le cadre de l’achat de la réservation.
2.  Dans l’heure 1, l’instance 1 s’exécute pendant 0,75 heure, et l’instance 2 pendant 0,5 heure. L’utilisation totale de l’heure 1 est 1,25 heure. Vous payez 0,25 heure restante au tarif du paiement à l’utilisation.
3.  Pour les heures 2 et 3, les deux instances se sont exécutées chacune pendant 1 heure. Une instance est couverte par la réservation, et l’autre est facturée au tarif du paiement à l’utilisation.
4.  Pour l’heure 4, l’instance 1 s’exécute pendant 0,5 heure tandis que l’instance 2 s’exécute pendant 1 heure. L’instance 1 est entièrement couverte par la réservation, et la durée de 0,5 heure de l’instance 2 est couverte. Vous êtes facturé au tarif du paiement à l’utilisation pour cette durée de 0,5 heure restante.

Pour comprendre et voir l’application de vos réservations dans les rapports d’utilisation pour la facturation, consultez [Comprendre l’utilisation de l’instance de machine virtuelle réservée](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Application de la remise de réservation aux machines virtuelles Windows
Lorsque vous exécutez les instances de machine virtuelle Windows, la réservation est appliquée pour couvrir les coûts d’infrastructure. L’application de la réservation aux coûts d’infrastructure de machine virtuelle pour les machines virtuelles Windows est identique à celle qui est appliquée pour les machines virtuelles non Windows. Vous payez séparément, par processeur virtuel, pour les logiciels Windows. Consultez [Coûts des logiciels Windows avec les réservations](https://go.microsoft.com/fwlink/?linkid=862756). Vous pouvez couvrir vos coûts de licence avec [Azure Hybrid Benefit pour Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances de machine virtuelle réservées, voir les articles suivants.

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administrer les instances de machine virtuelle réservées Azure](billing-manage-reserved-vm-instance.md)
- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](billing-save-compute-costs-reservations.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
