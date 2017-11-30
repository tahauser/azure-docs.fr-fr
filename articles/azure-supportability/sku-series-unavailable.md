---
title: "Séries de références (SKU) non disponibles | Microsoft Docs"
description: "Certaines séries de références (SKU) ne sont pas disponibles pour l’abonnement sélectionné pour cette région."
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>Région ou référence (SKU) non disponible
Cet article décrit comment résoudre le problème quand un abonnement Azure n’a pas accès à une région ou à un SKU de machine virtuelle.

## <a name="symptoms"></a>Symptômes

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Quand vous déployez une machine virtuelle, vous recevez l’un des messages d’erreur suivants :
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Quand vous achetez des instances de machine virtuelle réservées, vous recevez l’un des messages d’erreur suivants :

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Quand vous créez une demande de support pour augmenter le quota de cœurs de calcul, une région ou une famille de références (SKU) n’est pas disponible pour la sélection.

## <a name="solution"></a>Solution
Tout d’abord, nous vous recommandons d’utiliser une autre région ou référence qui répond aux besoins de votre entreprise. Si vous ne parvenez pas à trouver une région ou référence appropriée, créez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) « Gestion de l’abonnement » en suivant les étapes ci-dessous :


- Dans la page Paramètres de base, sélectionnez le type de problème « Gestion de l’abonnement », sélectionnez l’abonnement, puis cliquez sur « Suivant ».

![Panneau Informations de base](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Dans la page Problème, sélectionnez le type de problème « Autres questions générales ».
- Dans la section Détails :
  - Indiquez si vous cherchez à déployer des machines virtuelles ou à acheter des instances de machine virtuelle réservées.
  - Spécifiez la région, la référence et le nombre d’instances de machine virtuelle que vous envisagez de déployer ou d’acheter.


![Problème](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Entrez vos informations de contact, puis cliquez sur « Créer ».

![Informations de contact](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Commentaires
Nous sommes ouverts aux commentaires et suggestions ! Envoyez-nous vos [suggestions](https://feedback.azure.com/forums/266794-support-feedback). Vous pouvez aussi nous contacter via [Twitter](https://twitter.com/azuresupport) ou via les [forums MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>En savoir plus
[FAQ du support Azure](https://azure.microsoft.com/support/faq)

