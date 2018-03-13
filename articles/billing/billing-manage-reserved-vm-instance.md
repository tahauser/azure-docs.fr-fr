---
title: "Gérer Azure Reserved Virtual Machine Instances | Microsoft Docs"
description: "Découvrez comment modifier l’étendue de l’abonnement et gérer l’accès à Azure Reserved VM Instances."
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
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: f3f5f974630c4bf1c68599e26612ed729b55bcfc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-reserved-virtual-machine-instances"></a>Administrer les instances de machine virtuelle réservées Azure

Après avoir acheté une instance de machine virtuelle réservée Azure, vous voudrez peut-être appliquer cette réservation à un autre abonnement que celui qui a été spécifié lors de l’achat. Si vos machines virtuelles correspondantes sont en cours d’exécution sur plusieurs abonnements, vous avez peut-être envie également de modifier l’étendue de la réservation pour la partager. Afin d’optimiser cette remise de réservation, assurez-vous que le nombre d’instances achetées correspond aux attributs et au nombre de machines virtuelles qui sont en cours d’exécution. Pour en savoir plus sur les instances de machine virtuelle réservées, consultez [Réaliser des économies en prépayant des machines virtuelles Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Modifier l’étendue d’une réservation
 Votre remise de réservation s’applique aux machines virtuelles qui correspondent à votre réservation et qui s’exécutent dans les limites de l’étendue de la réservation. L’étendue d’une réservation peut être un abonnement unique ou tous les abonnements de votre contexte de facturation. Si vous définissez l’étendue à un seul abonnement, la réservation est mise en correspondance avec les machines virtuelles exécutées dans l’abonnement sélectionné. Si vous définissez l’étendue pour qu’elle soit partagée, Azure met en correspondance la réservation avec les machines virtuelles exécutées dans tous les abonnements du contexte de facturation. Le contexte de facturation dépend de l’abonnement utilisé pour acheter la réservation. Pour en savoir plus, consultez [Prépayer des machines virtuelles avec des instances de machine virtuelle réservées](https://go.microsoft.com/fwlink/?linkid=861721).

Pour mettre à jour l’étendue d’une réservation : 
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez la réservation.
4. Sélectionnez **Paramètres** > **Configuration**.
5. Modifiez l’étendue. Si vous passez de l’étendue partagée à une étendue unique, vous ne pouvez sélectionner que les abonnements dont vous êtes le propriétaire. Seuls peuvent être sélectionnés les abonnements présents dans le même contexte de facturation que celui de la réservation. Le contexte de facturation est déterminé par l’abonnement que vous avez sélectionné au moment de l’achat de la réservation. L’étendue s’applique uniquement aux abonnements MS-AZR-0003P de l’offre avec paiement à l’utilisation, et MS-AZR-0017P de l’offre Entreprise. Pour les contrats d’entreprise, les abonnements de développement et de test ne peuvent pas bénéficier de la remise de réservation.

## <a name="split-a-single-reservation-into-two-reservations"></a>Diviser une réservation unique en deux réservations
 Après avoir acheté plusieurs instances, vous voudrez peut-être affecter les instances d’une réservation à différents abonnements. Par défaut, toutes les instances (quantité spécifiée lors de l’achat) ont une étendue, qu’elle soit sur un abonnement unique ou partagée sur plusieurs abonnements. Par exemple, vous avez acheté 10 machines virtuelles Standard D2 et spécifié l’étendue à l’abonnement A. Vous pouvez à présent modifier l’étendue pour 7 instances de machine virtuelle réservées sur l’abonnement A, et les 3 instances restantes sur l’abonnement B. La division d’une réservation vous permet de répartir les instances selon une gestion de l’étendue plus minutieuse. Vous pouvez simplifier la répartition sur les abonnements en choisissant une étendue partagée. Toutefois, pour des raisons de gestion de coûts et de budgétisation, vous pouvez affecter des quantités à des abonnements spécifiques.

 Vous pouvez diviser une réservation en deux réservations par l’intermédiaire de PowerShell, de CLI ou de l’API.

### <a name="split-a-reservation-by-using-powershell"></a>Diviser une réservation à l’aide de PowerShell
1. Récupérez l’ID de l’ordre de réservation en exécutant la commande suivante :

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Récupérez les détails d’une réservation :

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Divisez la réservation en deux et répartissez les instances :

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Vous pouvez mettre à jour l’étendue en exécutant la commande suivante :

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Ajouter ou modifier les utilisateurs qui peuvent gérer une réservation
Vous pouvez déléguer la gestion d’une réservation en ajoutant des utilisateurs aux rôles de la réservation. Par défaut, la personne qui a acheté la réservation et l’administrateur de compte disposent tous les deux du rôle de propriétaire sur la réservation. 

Vous pouvez gérer l’accès aux réservations, indépendamment des abonnements qui bénéficient de la remise de réservation. Lorsque vous accordez des autorisations de gestion pour une réservation, cela ne veut pas dire que vous octroyez des droits sur la gestion de l’abonnement. Et si vous accordez des autorisations de gestion d’un abonnement dans les limites de l’étendue de la réservation, vous n’octroyez aucun droit permettant de gérer la réservation.
 
Pour déléguer la gestion de l’accès à une réservation : 
1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des réservations auxquelles vous avez accès.
3.  Sélectionnez la réservation pour laquelle vous souhaitez déléguer l’accès à d’autres utilisateurs.
4.  Sélectionnez **Contrôle d’accès (IAM)** dans le menu.
5.  Sélectionnez **Ajouter** > **Rôle** > **Propriétaire** (ou un rôle différent si vous souhaitez accorder un accès limité). 
6. Entrez l’adresse e-mail de l’utilisateur à ajouter en tant que propriétaire. 
7. Sélectionnez l’utilisateur, puis **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les instances de machine virtuelle réservées, voir les articles suivants.

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Réaliser des économies sur les machines virtuelles avec les instances de machine virtuelle réservées](billing-save-compute-costs-reservations.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
