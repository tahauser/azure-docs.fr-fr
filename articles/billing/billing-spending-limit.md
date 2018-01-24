---
title: "Comprendre la limite de dépense d’Azure | Microsoft Docs"
description: "Décrit le fonctionnement de la limite de dépense d’Azure et comment la désactiver"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: d222300a591bcba556be5813ada2de555d25a1f0
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/23/2017
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Comprendre la limite de dépense d’Azure et comment la désactiver

Dans Azure, il existe une limite de dépense pour vous empêcher de dépasser la quantité de vos crédits. Pour tout nouveau client optant pour un essai ou une offre incluant des crédits s’étalant sur plusieurs mois, la limite de dépense est activée par défaut. La limite de dépense est de 0 $. Elle ne peut pas être modifiée. La limite de dépense n’est pas disponible pour les abonnements faisant l’objet d’un paiement à l’utilisation et les plans d’engagement. Pour plus d’informations, consultez la [liste complète des offres Azure et la disponibilité de la limite de dépense](https://azure.microsoft.com/support/legal/offer-details/).

**Vous recherchez des alertes de facturation ?** Consultez [Configurer des alertes de facturation ou de crédit pour vos abonnements Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Que se passe-t-il lorsque j’atteins la limite de dépense ?

Si votre utilisation entraîne des frais dépassant les quantités mensuelles incluses dans votre abonnement, les services que vous avez déployés sont désactivés jusqu’à la fin de cette période de facturation. 

Par exemple, quand vous dépensez tous les crédits inclus dans votre abonnement, les services cloud que vous avez déployés sont supprimés de la production et vos machines virtuelles Azure sont arrêtées et désallouées. Les données de vos comptes et bases de données de stockage sont accessibles en lecture seule.

Au début de la période de facturation suivante, si votre offre d’abonnement inclut des crédits sur plusieurs mois, votre abonnement est automatiquement réactivé. Vous pouvez redéployer vos services cloud et disposer d’un accès complet à vos comptes de stockage et bases de données.

Nous envoyons des notifications par e-mail quand vous atteignez la limite de dépense de votre abonnement. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions) pour voir les notifications concernant les abonnements qui ont atteint leur limite de dépense.

Si vous utilisez l’essai gratuit et que vous atteignez la limite de dépense, vous pouvez [passer à un abonnement avec paiement à l’utilisation](billing-upgrade-azure-subscription.md) pour supprimer la limite de dépense et réactiver automatiquement l’abonnement.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Supprimer la limite de dépense dans le Centre des comptes

Vous pouvez supprimer la limite de dépense à tout moment, pour autant qu’un mode de paiement valide soit associé à votre abonnement. Pour les offres bénéficiant d’un crédit s’étalant sur plusieurs mois, vous pouvez également réactiver la limite de dépense au début de votre période de facturation suivante.

Pour supprimer votre limite de dépense, procédez comme suit :

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions).
1. Sélectionnez un abonnement.
. Si l’abonnement est désactivé parce que la limite de dépense a été atteinte, cliquez sur la notification suivante : « L’abonnement a atteint la limite de dépense et a été désactivé pour empêcher les frais. » Autrement, cliquez sur **Supprimer la limite de dépense** dans la zone **ÉTAT DE L’ABONNEMENT**.
1. Sélectionnez une option adaptée à votre situation.

![Sélection d’une option de suppression de la limite de dépense](./media/billing-spending-limit/remove-spending-limit.PNG)

|Option|Résultat|
|-------|-----|
|Supprimer la limite de dépense pour une durée indéterminée|Supprime la limite de dépense sans l'activer automatiquement au début de la période de facturation suivante.|
|Supprimer la limite de dépense pour la période de facturation en cours|Supprime la limite de dépense et l’active automatiquement au début de la période de facturation suivante.|

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Quel est l’intérêt de supprimer la limite de dépense ?

La limite de dépense peut vous empêcher de déployer ou d’utiliser certains services Microsoft et tiers. Voici les situations dans lesquelles vous devez lever la limite de dépense liée à votre abonnement.

* Vous prévoyez de déployer des images internes telles qu’Oracle et des services tels que Visual Studio Team Services. Cette situation entraîne presque immédiatement un dépassement de votre limite de dépense, qui a pour effet de désactiver votre abonnement.
* Vous avez des services dont le fonctionnement ne peut pas être interrompu.
* Vous avez des services et ressources utilisant des paramètres tels que des adresses IP virtuelles que vous ne voulez pas perdre. Ces paramètres sont perdus en cas de désallocation des services et ressources.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Comment faire pour activer la limite de dépense après l’avoir supprimée ?

Cette fonctionnalité est disponible uniquement lorsque la limite de dépense a été supprimée indéfiniment. Modifiez cette option pour l’activer automatiquement au début de la prochaine période de facturation.

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions).
1. Cliquez sur la bannière jaune pour modifier l’option de limite de dépense.
1. Choisissez **Activer la limite de dépense de la prochaine période de facturation \<date de début de la période de facturation\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Comment faire pour définir une limite de dépense personnalisée ?

Nous ne proposons pas de limites de dépense personnalisées pour le moment. Toutefois, vous pouvez choisir d’[utiliser des alertes de facturation pour contrôler vos dépenses](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>La limite de dépense empêche-t-elle tous les frais facturés par Azure ?

[Certains services externes publiés sur la Place de marché Azure](billing-understand-your-azure-marketplace-charges.md) ne sont pas utilisables avec des crédits d’abonnement et risquent d’occasionner des frais distincts même si une limite de dépense est définie. C’est le cas, par exemple, des licences Visual Studio, d’Azure Active Directory Premium, des plans de support et de la plupart des services vendus sous marque de tiers. Lors de l’approvisionnement d’un nouveau service externe, un avertissement indique que les services sont facturés séparément :

![Avertissement d’achat Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
