---
title: "Coûts des logiciels Windows dans les instances de machine virtuelle réservées Azure | Microsoft Docs"
description: "Découvrez les compteurs de logiciels Windows qui ne sont pas inclus dans les coûts d’instance de machine virtuelle réservée."
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
ms.openlocfilehash: a0bb559369877e1cc5333394102bfb85d3f0bb11
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>Coûts des logiciels Windows non inclus dans les instances réservées

Si vous n’utilisez pas Azure Hybrid Use Benefit sur votre machine virtuelle d’instance réservée, vous êtes facturé pour les compteurs de logiciels Windows répertoriés dans la section suivante.

## <a name="windows-software-meters-not-included-in-reserved-instance-cost"></a>Compteurs de logiciels Windows non inclus dans le coût des instances réservées

| ID du compteur | Nom du compteur dans le fichier d’utilisation | Utilisé par la machine virtuelle |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Réservation-Windows Server Rafale (1 cœur) | Série B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Réservation-Windows Server Rafale (2 cœurs) | Série B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Réservation-Windows Server Rafale (4 cœurs) | Série B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Réservation-Windows Server Rafale (8 cœurs) | Série B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Réservation-Windows Server (1 cœur) | Tout sauf la série B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Réservation-Windows Server (2 cœurs) | Tout sauf la série B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Réservation-Windows Server (4 cœurs) | Tout sauf la série B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Réservation-Windows Server (6 cœurs) | Tout sauf la série B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Réservation-Windows Server (8 cœurs) | Tout sauf la série B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Réservation-Windows Server (12 cœurs) | Tout sauf la série B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Réservation-Windows Server (16 cœurs) | Tout sauf la série B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Réservation-Windows Server (20 cœurs) | Tout sauf la série B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Réservation-Windows Server (24 cœurs) | Tout sauf la série B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Réservation-Windows Server (32 cœurs) | Tout sauf la série B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Réservation-Windows Server (40 cœurs) | Tout sauf la série B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Réservation-Windows Server (64 cœurs) | Tout sauf la série B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Réservation-Windows Server (72 cœurs) | Tout sauf la série B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Réservation-Windows Server (128 cœurs) | Tout sauf la série B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Réservation-Windows Server (256 cœurs) | Tout sauf la série B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Réservation-Windows Server (96 cœurs) | Tout sauf la série B |

Vous pouvez obtenir le coût de chacun de ces compteurs via l’API Azure RateCard. Pour en savoir plus sur la façon de se procurer les tarifs d’un compteur Azure, consultez [Obtenir des informations sur les prix et les métadonnées des ressources utilisées dans un abonnement Azure](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances de machine virtuelle réservées, voir les articles suivants.

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administrer les instances de machine virtuelle réservées Azure](billing-manage-reserved-vm-instance.md)
- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](billing-save-compute-costs-reservations.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
