---
title: "Réaliser des économies en prépayant des machines virtuelles - Azure | Microsoft Docs"
description: "Découvrez l’instance de machine virtuelle réservée Azure pour baisser vos coûts de machines virtuelles."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 799abddc4894bc090d860e7fe100ee65d4d085ab
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées 
Les instances de machine virtuelle réservées vous permettent de payer à l’avance la capacité de calcul pendant un an ou trois ans et d’obtenir une remise sur les machines virtuelles que vous utilisez. Avec cet engagement initial d’une durée de 1 an ou de 3 ans, les coûts de vos machines virtuelles sont considérablement réduits, jusqu’à 72 % par rapport au tarif du paiement à l’utilisation. Cette offre d’instances de machine virtuelle réservées représente une remise sur facturation et n’a pas d’incidence sur l’état d’exécution des machines virtuelles.

Vous pouvez acheter une instance de machine virtuelle réservée sur le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez [Prépayer des machines virtuelles et réaliser des économies avec les instances de machine virtuelle réservées](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Pourquoi acheter une instance de machine virtuelle réservée ?
Si vous avez des machines virtuelles qui s’exécutent sur de longues périodes, l’achat d’une instance de machine virtuelle réservée représente une offre très avantageuse pour vous. Par exemple, si vous exécutez en permanence quatre instances de Standard D2 dans la région États-Unis de l’Ouest, sans réservation vous êtes facturé au tarif du paiement à l’utilisation. Si vous achetez une instance de machine virtuelle réservée pour ces quatre machines virtuelles, elles bénéficient immédiatement de cette remise. Les machines virtuelles ne sont plus facturées au tarif du paiement à l’utilisation. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Quels sont les frais couverts par une instance de machine virtuelle réservée ?
Une réservation couvre uniquement les frais d’infrastructure de machine virtuelle pour vos machines virtuelles Windows ou Linux. Une réservation ne couvre pas les frais de logiciels, de mise en réseau ou de stockage supplémentaires. Pour les machines virtuelles Windows, vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Qui est susceptible d’acheter une instance de machine virtuelle réservée ?
Les clients Azure ayant souscrit à ces types d’abonnements peuvent acheter une instance de machine virtuelle réservée :
-   Offre d’abonnement de type contrat Entreprise (MS-AZR-0017P).
-   Offre d’abonnement de type [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P).
Vous devez avoir le rôle « Propriétaire » de l’abonnement pour acheter une instance réservée. Pour l’achat de réservations dans le cadre d’une inscription pro, l’administrateur de l’entreprise doit activer les achats de réservation dans le portail EA ; par défaut ce paramètre est activé.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Comment un achat d’instances de machine virtuelle réservées est-il facturé ?
L’achat de réservations est facturé selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût des réservations est déduit de votre solde d’engagement. Si ce solde ne couvre pas le coût des réservations, le dépassement vous est facturé.
Si vous avez souscrit un abonnement avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Comment la remise d’instance de machine virtuelle réservée que vous avez achetée est-elle appliquée ?
La remise d’instance de machine virtuelle réservée s’applique aux machines virtuelles qui correspondent aux attributs que vous sélectionnez lorsque vous achetez la réservation. Ces attributs englobent l’étendue où les machines virtuelles correspondantes sont exécutées. Par exemple, si vous souhaitez une remise d’instance de machine virtuelle réservée sur les quatre machines virtuelles Standard D2 de la région États-Unis de l’Ouest, sélectionnez l’abonnement dans lequel ces machines virtuelles sont en cours d’exécution. Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue partagée permet d’appliquer la remise de réservation à différents abonnements.
Vous pouvez modifier l’étendue après avoir acheté une instance de machine virtuelle réservée. Pour modifier l’étendue, consultez la documentation sur l’administration des réservations.

La remise de réservation s’applique uniquement aux machines virtuelles dans les offres d’abonnement de type Contrat Entreprise ou Paiement à l’utilisation. Les machines virtuelles qui relèvent d’autres types d’abonnements ne profitent pas de la remise de réservation. En ce qui concerne les inscriptions pro, les abonnements Enterprise Dev/Test ne peuvent pas prétendre aux remises d’instances réservées.

L’impact de la réservation sur la facturation de la machine virtuelle est expliqué dans [Compréhension de l’application de la remise de réservation](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>Que se passe-t-il lorsque le terme de la réservation expire ?
À la fin de la période de réservation, la remise cesse d’être appliquée et l’infrastructure de machine virtuelle est de nouveau facturée au tarif du paiement à l’utilisation. Les réservations ne se renouvellent pas automatiquement. Pour continuer à bénéficier de la remise, vous devez acheter une nouvelle instance de machine virtuelle réservée. 

## <a name="sizes-and-regional-availability"></a>Tailles et disponibilités régionales
Les réservations sont disponibles pour la plupart des tailles de machine virtuelle, à quelques exceptions près :
- Tailles de machine virtuelle en préversion : aucune taille existant en préversion n’est disponible pour l’achat d’instances de machine virtuelle réservées.
- Clouds : les instances de machine virtuelle réservées ne sont pas disponibles à l’achat dans les régions Azure US Government, Allemagne ou Chine. 
- Quota insuffisant : une instance de machine virtuelle réservée qui s’étend sur un seul abonnement doit avoir le quota de processeurs virtuels disponibles dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a une limite de quota de 10 processeurs virtuels pour la famille de la série D, vous ne pouvez pas acheter d’instance de machine virtuelle réservée pour 11 instances Standard_D1. La vérification du quota pour les réservations inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, prenons un abonnement avec un quota de 10 processeurs virtuels pour la famille de la série D. Si cet abonnement a deux instances de standard_D1 déployées, vous pouvez acheter une instance de machine virtuelle réservée pour 10 instances standard_D1 dans cet abonnement. 
- Restrictions de capacité : dans de rares cas, Azure limite l’achat de nouvelles réservations pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région.

## <a name="next-steps"></a>Étapes suivantes
Commencer à économiser sur vos machines virtuelles en achetant une [instance de machine virtuelle réservée](https://go.microsoft.com/fwlink/?linkid=861721). 

Pour plus d’informations sur les instances de machine virtuelle réservées, voir les articles suivants.

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administrer les instances de machine virtuelle réservées Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
