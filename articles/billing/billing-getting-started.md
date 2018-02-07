---
title: "Éviter les coûts imprévus, gérer la facturation - Azure | Microsoft Docs"
description: "Découvrez comment éviter des frais inattendus sur votre facture Azure. Utilisez les fonctionnalités de gestion et de suivi des coûts pour un abonnement Microsoft Azure."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: tonguyen
ms.openlocfilehash: 885dcbc0e0d3d9490d745a07ade5d9dece7ff3d6
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Éviter les charges inattendues avec la gestion de la facturation et des coûts dans Azure

Lorsque vous vous inscrivez à Azure, il y a plusieurs choses que vous pouvez faire pour avoir une meilleure idée de vos dépenses. La [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) peut fournir une estimation des coûts avant de créer une ressource Azure. Le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fournit la répartition des coûts et la prévision pour votre abonnement. Si vous souhaitez regrouper et comprendre les coûts associés à différents projets ou équipes,reportez-vous au [balisage des ressources](../azure-resource-manager/resource-group-using-tags.md). Si votre organisation dispose d’un système de création de rapports que vous préférez utiliser, regardez du côté des [API de facturation](billing-usage-rate-card-overview.md). 

- Si votre abonnement est un Contrat Entreprise (EA), la préversion publique pour l’affichage de vos coûts dans le portail Azure est disponible. Si votre abonnement s’effectue par l’intermédiaire d’un fournisseur de solutions cloud (CSP) ou Azure Sponsorship, certaines des fonctionnalités suivantes peuvent ne pas vous concerner. Consultez [Ressources supplémentaires pour les offres EA, CSP et Sponsorship](#other-offers) et pour obtenir plus d’informations.

- Si votre abonnement est une évaluation gratuite, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure dans Open (AIO) ou BizSpark, votre abonnement est automatiquement désactivé lorsque tous vos crédits sont utilisés. En savoir plus sur les [limites de dépense](#spending-limit) pour éviter que votre abonnement ne soit désactivé de façon inattendue.

- Si vous avez souscrit à un [compte Azure gratuit](https://azure.microsoft.com/en-us/free/), [vous pouvez utiliser des services Azure populaires gratuitement pendant 12 mois](billing-create-free-services-included-free-account.md). Outre les recommandations répertoriées ci-dessous, consultez [Éviter les frais pour votre compte gratuit](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obtenir les coûts estimés avant d’ajouter des services Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimez le coût en ligne à l’aide de la calculatrice de prix

Examinez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour obtenir une estimation mensuelle du coût de service qui vous intéresse. Vous pouvez ajouter tout premier tiers de ressources Azure pour obtenir une estimation de coût.

![Capture d’écran du menu de la calculatrice de prix](./media/billing-getting-started/pricing-calc.png)

Par exemple, le coût d’une machine virtuelle A1 Windows en heures de calcul est estimé à 66,96 USD par mois si vous la laissez s’exécuter en permanence :

![Capture d’écran de la calculatrice de prix montrant que le coût d’une machine virtuelle A1 Windows est estimé à 66,96 USD par mois](./media/billing-getting-started/pricing-calcVM.png)

Pour plus d’informations sur les prix, reportez-vous à cette [FAQ](https://azure.microsoft.com/pricing/faq/). Ou, si vous souhaitez parler à un vendeur Azure, appelez le 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

En général, lorsque vous ajoutez un service dans le portail Azure, une vue présentant une estimation similaire du coût par mois vous est proposée. Par exemple, lorsque vous choisissez la taille de votre machine virtuelle Windows, vous pouvez voir l’estimation du coût mensuel pour les heures de calcul :

![Exemple : le coût d’une machine virtuelle A1 Windows est estimé à 66,96 USD par mois](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Configurer des alertes de facturation

Configurez des alertes de facturation pour recevoir des e-mails lorsque les coûts d’utilisation dépassent un certain montant. Si vous disposez de crédits mensuels, configurez des alertes pour être averti lorsqu’un montant spécifique a été utilisé. Pour plus d’informations, consultez [Configurer des alertes de facturation pour vos abonnements Microsoft Azure](billing-set-up-alerts.md).

![Capture d’écran d’un e-mail d’alerte de facturation](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Cette fonctionnalité étant encore en version préliminaire, il est recommandé de vérifier régulièrement l’utilisation.

Pour configurer votre première alerte, vous pouvez vous baser sur l’estimation du coût obtenue à l’aide de la calculatrice de prix.

### <a name="spending-limit"></a> Vérifiez si la limite de dépense est activée

Si votre abonnement utilise des crédits, la limite de dépense est activée pour vous par défaut. De cette manière, lorsque vous dépensez tous vos crédits, votre carte bancaire n’est pas facturée. Pour plus d’informations, consultez la [liste complète des offres Azure et les informations sur la disponibilité de la limite de dépense](https://azure.microsoft.com/support/legal/offer-details/).

Toutefois, si vous atteignez votre limite de dépense, vos services sont désactivés. Cela signifie que vos machines virtuelles sont libérées. Pour éviter toute interruption de service, vous devez désactiver la limite de dépense. Tout dépassement est facturé sur la carte de crédit enregistrée. 

Pour voir si la limite de dépense est activée, accédez à la [vue Abonnements du Centre des comptes](https://account.windowsazure.com/Subscriptions). Une bannière s’affiche si votre limite de dépense est activée :

![Capture d’écran montrant l’avertissement affiché dans le Centre des comptes lorsque la limite de dépense est activée](./media/billing-getting-started/spending-limit-banner.PNG)

Cliquez sur la bannière et suivez les invites pour supprimer la limite de dépense. Si vous n’avez pas entré d’informations de carte bancaire lors de votre inscription, vous devez les saisir pour supprimer la limite de dépense. Pour plus d’informations, consultez [Limite de dépense Azure : fonctionnement et activation ou désactivation](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Méthodes d’analyse de vos coûts lors de l’utilisation des services Azure

### <a name="tags"></a> Ajoutez des balises à vos ressources pour regrouper vos données de facturation

Vous pouvez utiliser des balises pour regrouper les données de facturation associées aux services pris en charge. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes équipes, vous pouvez utiliser des balises pour classer les coûts par centre de coût (RH, marketing, service financier) ou par environnement (production, préproduction, test). 

![Capture d’écran illustrant la configuration de balises dans le portail](./media/billing-getting-started/tags.PNG)

Les balises apparaissent dans les différentes vues des rapports de coûts. Par exemple, elles sont visibles dans votre [vue d’analyse des coûts](#costs) immédiatement et dans le [fichier .csv d’utilisation détaillée](#invoice-and-usage) après votre première période de facturation.

Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Vérifiez régulièrement la répartition des coûts et le taux d’avancement dans le portail

Une fois que vos services sont actifs, vérifiez régulièrement combien ils vous coûtent. Vous pouvez consulter les dépenses et le taux d’avancement actuels dans le portail. 

1. Visitez le [panneau Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et sélectionnez un abonnement.

2. Vous devriez voir la répartition des coûts et le taux d’avancement dans le panneau contextuel qui s’affiche. Il se peut que votre offre ne donne pas accès à ces données (un avertissement est alors affiché en haut).

    ![Capture d’écran du taux d’avancement et de la répartition des coûts dans le portail Azure](./media/billing-getting-started/burn-rate.PNG)

3. Cliquez sur **Analyse des coûts** dans la liste figurant sur la gauche pour afficher la répartition des coûts par ressource. Après avoir ajouté un service, comptez un délai de 24 heures pour que les données soient remplies.

    ![Capture d’écran de la vue de l’analyse des coûts dans le portail Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Vous pouvez filtrer les données en fonction de différentes propriétés : [balises](#tags), groupe de ressources, intervalle de temps, etc. Cliquez sur **Appliquer** pour confirmer les filtres et sur **Télécharger** pour exporter la vue vers un fichier de valeurs séparées par des virgules (.csv).

5. En outre, vous pouvez cliquer sur une ressource pour afficher l’historique des dépenses quotidiennes et le coût des ressources chaque jour.

    ![Capture d’écran de la vue de l’historique des dépenses dans le portail Azure](./media/billing-getting-started/costhistory.PNG)

Nous vous recommandons de comparer les coûts affichés avec les estimations qui vous ont été données lors de la sélection des services. Si vous constatez une différence importante, vérifiez le plan de tarification (machine virtuelle A1 ou A0, par exemple) que vous avez sélectionné pour vos ressources. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Envisagez d’activer les fonctionnalités de réduction des coûts telles que l’arrêt automatique pour les machines virtuelles

Selon votre scénario, vous pouvez configurer l’arrêt automatique de vos machines virtuelles dans le portail Azure. Pour en savoir plus, consultez le billet de blog [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arrêt automatique des machines virtuelles à l’aide d’Azure Resource Manager).

![Capture d’écran de l’option d’arrêt automatique dans le portail](./media/billing-getting-started/auto-shutdown.PNG)

L’arrêt automatique n’a pas le même effet que l’arrêt à partir des options d’alimentation d’une machine virtuelle. L’arrêt automatique met un terme au fonctionnement de vos machines virtuelles et les libère pour éviter des frais d’utilisation supplémentaires. Pour plus d’informations, consultez les informations concernant les états des machines virtuelles dans le FAQ sur la facturation pour les [machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et les [machines Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Pour bénéficier d’autres fonctionnalités de réduction des coûts pour vos environnements de développement et de test, consultez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Activez et consultez les recommandations d’Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) est une fonctionnalité en version préliminaire qui vous permet de réduire les coûts en identifiant les ressources peu utilisées. Activez-la dans le portail Azure :

![Capture d’écran du bouton Azure Advisor dans le portail Azure](./media/billing-getting-started/advisor-button.PNG)

Vous pourrez alors accéder à des recommandations exploitables à partir de l’onglet **Coût** du tableau de bord d’Advisor :

![Exemple de recommandation en matière de coûts d’Advisor](./media/billing-getting-started/advisor-action.PNG)

Pour plus d’informations, consultez [Recommandations du conseiller en matière de coût](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Examen des coûts à la fin de votre cycle de facturation

À la fin de votre cycle de facturation, votre facture sera disponible. Vous pouvez également [télécharger les factures précédentes et détailler les fichiers d’utilisation](billing-download-azure-invoice-daily-usage-date.md) pour vous assurer que vous avez été facturé correctement. Pour plus d’informations sur la relation entre votre facture et votre utilisation quotidienne, consultez [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API de facturation

Utilisez nos API de facturation pour obtenir les données d’utilisation par programmation. En associant les API RateCard et Resource Usage, vous pouvez connaître l’utilisation qui vous est facturée. Pour plus d’informations, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Ressources supplémentaires te cas spéciaux

### <a name="ea-csp-and-sponsorship-customers"></a>Clients EA, CSP et Sponsoring
Contactez votre responsable de compte ou votre partenaire Azure pour commencer.

| Offre | Ressources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrat Entreprise (EA) | [Portail EA](https://ea.azure.com/), [documents d’aide](https://ea.azure.com/helpdocs), et [Rapport Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fournisseur de solutions cloud (CSP) | Contactez votre fournisseur |
| Azure Sponsorship | [Portail Sponsorship](https://www.microsoftazuresponsorships.com/) |

Si vous êtes responsable informatique d’une grande organisation, nous vous recommandons de lire l’article [Structure d’entreprise Azure](../azure-resource-manager/resource-manager-subscription-governance.md) et le document [entreprise livre blanc IT](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (téléchargement au format .pdf, disponible en anglais uniquement).

#### <a name="EA"></a> Prévisualiser les vues des coûts du Contrat Enterprise dans le portail Azure 

Les vues des coûts d’entreprise sont actuellement en préversion publique. Éléments à noter :
- Les coûts d’abonnement sont basés sur l’utilisation et ne tiennent pas compte des montants prépayés, dépassements, quantités incluses, ajustements et taxes. Les frais réels sont calculés au niveau de l’inscription. 
- Les montants affichés dans le portail Azure peuvent être différés par rapport aux valeurs indiquées dans le portail d’entreprise.  
- Si vous ne voyez pas les coûts, l’une des raisons suivantes peut expliquer cette situation :
    - Vous ne disposez pas de l’autorisation RBAC suffisante au niveau de l’abonnement. Pour afficher les vues de coût d’entreprise, vous devez être un lecteur de facturation, un lecteur, un contributeur ou un propriétaire au niveau de l’abonnement.
    - Vous êtes propriétaire de compte et votre administrateur en charge de l’inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de compte ».  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès aux coûts. 
    - Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de service ».  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès. 
    - Vous avez acheté Azure par le biais d’un partenaire et ce dernier n’a pas publié d’informations tarifaires.  
- Quand des paramètres liés à l’accès aux coûts sont mis à jour dans le portail d’entreprise, il existe un délai d’attente de quelques minutes avant que les modifications ne soient répercutées dans le portail Azure.
- La limite de dépense, les alertes de facturation et les conseils de facturation ne concernent pas les abonnements EA.

### <a name="check-your-subscription-and-access"></a>Vérifiez votre abonnement et votre accès

L’affichage des coûts nécessite un [accès de niveau abonnement aux informations de facturation](billing-manage-access.md), mais seul l’administrateur de compte peut accéder au [Centre des comptes](https://account.azure.com/Subscriptions), modifier les informations de facturation et gérer les abonnements. L’administrateur de compte est la personne qui a effectué le processus d’inscription. Pour plus d’informations, consultez [Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services](billing-add-change-azure-subscription-administrator.md).

Pour savoir si vous êtes l’administrateur de compte, accédez à l’[onglet Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et examinez la liste des abonnements auxquels vous avez accès. Regardez sous **Mon rôle**. S’il est indiqué *Administrateur de compte*, vous disposez bien de tous les droits associés. S’il est indiqué autre chose, par exemple *Propriétaire*, vous ne disposez pas de privilèges complets.

![Capture d’écran de votre rôle dans la vue Abonnements du portail Azure](./media/billing-getting-started/sub-blade-view.PNG)

Si vous n’êtes pas l’administrateur de compte, quelqu’un vous a sans doute octroyé un accès partiel via le [contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md) (RBAC). Pour gérer les abonnements et modifier les informations de facturation, [identifiez l’administrateur de compte](billing-subscription-transfer.md#whoisaa) et demandez-lui d’effectuer les tâches souhaitées ou de [vous transférer l’abonnement](billing-subscription-transfer.md).

Si votre administrateur de compte ne fait plus partie de votre organisation et vous avez besoin de gérer la facturation, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
