---
title: "Transfert de la propriété de l’abonnement Azure à un autre compte | Microsoft Docs"
description: "Décrit comment transférer un abonnement Azure à un autre utilisateur et Forum Aux Questions (FAQ) concernant la procédure"
keywords: "transférer abonnement azure, azure transfert abonnement, déplacer un abonnement azure vers un autre compte, changer abonnement azure, transférer abonnement azure à un autre compte"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/13/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff694ee7c2ecf7f8ee5ea89902fa77efad3f501c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Transfert de la propriété d’un abonnement Azure à un autre compte

Transférez votre abonnement à un autre utilisateur dans le Centre des comptes pour changer l’administrateur de compte et céder la propriété de facturation de l’abonnement. Pour modifier votre abonnement et changer d’offre, consultez la page [Changer d’offre pour votre abonnement Azure](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Si vous transférez un abonnement à un nouveau locataire Azure AD, toutes les attributions de rôle dans [Contrôle de l’accès basé sur les rôles (RBAC)](../active-directory/role-based-access-control-what-is.md) sont définitivement supprimées du locataire source et ne sont pas migrées vers le locataire cible.

## <a name="transfer-ownership-of-an-azure-subscription"></a>Transfert de la propriété d’un abonnement Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte. Pour déterminer qui est l’administrateur de compte de l’abonnement, consultez le [Forum aux questions](#faq).

1. Sélectionnez l’abonnement à transférer.

1. Vérifiez que votre abonnement est éligible au transfert en libre-service en recherchant l’**offre** et l’**ID de l’offre** dans la [liste des offres prises en charge](#supported).

   ![Vérifier l’ID de l’offre d’abonnement dans le Centre des comptes](./media/billing-subscription-transfer/image0.png)
1. Cliquez sur **Transférer la propriété de l’abonnement**.

   ![Onglet Abonnements du compte Azure](./media/billing-subscription-transfer/image1.png)
1. Spécifiez le destinataire.

   > [!IMPORTANT]
   > 
   > Si vous transférez un abonnement à un nouveau locataire Azure AD, toutes les attributions de rôle dans [Contrôle de l’accès basé sur les rôles (RBAC)](../active-directory/role-based-access-control-what-is.md) sont définitivement supprimées du locataire source et ne sont pas migrées vers le locataire cible.

   ![Boîte de dialogue Transférer un abonnement](./media/billing-subscription-transfer/image2.PNG)

1. Le destinataire reçoit automatiquement un e-mail contenant un lien d’acceptation.

   ![E-mail de transfert d’abonnement envoyé au destinataire](./media/billing-subscription-transfer/image3.png)
1. Le destinataire clique sur le lien et suit les instructions, notamment pour la saisie des informations de paiement.

   ![Page web de transfert du premier abonnement](./media/billing-subscription-transfer/image4.png)

   ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/image5.png)
1. Vous avez réussi ! L’abonnement est à présent transféré.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Transférer la propriété de l’abonnement pour les clients Contrat Entreprise (EA)

L’administrateur d’entreprise peut transférer la propriété des abonnements au sein d’une inscription. Pour commencer, consultez la rubrique [Transférer la propriété du compte](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) dans le portail EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Étapes suivantes après avoir accepté la propriété d’un abonnement

1. Vous êtes désormais l’administrateur de compte. Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les autres rôles RBAC. Pour plus d’informations, consultez [Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services](billing-add-change-azure-subscription-administrator.md).
1. Mettez à jour les informations d’identification associées aux services de cet abonnement, notamment :
   1. Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d’informations, consultez [Créer et télécharger un certificat de gestion pour Microsoft Azure](../cloud-services/cloud-services-certs-create.md)
   1. Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).
   1. Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure. 
1. [Mettez à jour les alertes de facturation pour cet abonnement](billing-set-up-alerts.md) dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions). 
1. Si vous collaborez avec un partenaire, envisagez de mettre à jour son ID de partenaire pour cet abonnement. Vous pouvez mettre à jour l’ID partenaire dans le [portail Azure](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Opérations prises en charge :

Le transfert d’abonnements en libre-service est disponible pour les offres ou types d’abonnements répertoriés dans le tableau suivant. Actuellement, vous ne pouvez pas transférer une version d’évaluation gratuite ou des abonnements [Azure dans Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Pour obtenir une solution de contournement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md). Pour transférer d’autres abonnements, comme [Sponsoring](https://azure.microsoft.com/offers/ms-azr-0036p/) ou des plans de support, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Nom de l’offre                                                                             | Numéro de l’offre |
|----------------------------------------------------------------------------------------|--------------|
| [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise : BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\*[Via le portail EA](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)

### <a name="whoisaa"></a> Qui est l’administrateur de compte de l’abonnement ?

L’administrateur de compte est la personne qui s’est inscrite ou a acheté l’abonnement Azure. Il est autorisé à accéder au [Centre des comptes](https://account.azure.com/Subscriptions) et à effectuer diverses tâches de gestion comme créer des abonnements, annuler des abonnements, modifier la facturation d’un abonnement et modifier l’administrateur de services fédérés. Si vous ne savez pas qui est l’administrateur de compte d’un abonnement, procédez comme suit pour le découvrir.

1. Accédez à la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tous les éléments sont-ils transférés ? Notamment les groupes de ressources, les machines virtuelles, les disques et les autres services en cours d’exécution ?

Toutes les ressources telles que les machines virtuelles, les disques, et les sites web sont transférées au nouveau propriétaire. Cependant, les [rôles d’administrateur](billing-add-change-azure-subscription-administrator.md) et les stratégies de [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) que vous avez configurés ne sont pas transférés dans les différents annuaires. Les [inscriptions des applications](../active-directory//develop/active-directory-integrating-applications.md) et autres services spécifiques du locataire ne sont pas non plus transférés.

### <a id="no-button"></a> Pourquoi le bouton Transférer un abonnement ne s’affiche-t-il pas ?

Malheureusement, le transfert en libre-service n’est pas disponible pour votre pays ou votre offre. Pour transférer votre abonnement, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Un transfert d’abonnement entraîne-t-il une interruption de service ?

Le transfert n’a aucune incidence sur le service. Le transfert de l’abonnement annule l’abonnement sous l’administrateur de compte actuel et crée un abonnement sous le compte du destinataire. Le nouvel abonnement est associé aux services Azure sous-jacents. L’ID de l’abonnement reste inchangé.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Comment utiliser ce processus pour modifier l’annuaire d’abonnement ?

Un abonnement Azure est créé dans le répertoire auquel appartient l’administrateur de compte. Pour modifier le répertoire, transférez l’abonnement sur un compte d’utilisateur situé dans le répertoire cible. Lorsque cet utilisateur exécute les étapes pour accepter le transfert, l’abonnement transite automatiquement vers le répertoire cible.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Si je deviens titulaire d’un abonnement d’une autre organisation, cette dernière a-t-elle toujours accès à mes ressources ?

Si l’abonnement est transféré à un autre client, les utilisateurs associés au client précédent perdent l’accès à l’abonnement. Même si un utilisateur n’est plus administrateur ou coadministrateur des services, il pourra toujours accéder à l’abonnement par le biais d’autres mécanismes de sécurité, notamment :

* Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure](../cloud-services/cloud-services-certs-create.md).
* Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez la rubrique [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).
* Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.

Le destinataire doit envisager la mise à jour des clés secrètes associées au service s’il doit restreindre l’accès aux ressources. La plupart des ressources peuvent être mises à jour en procédant comme suit :

  1. Accédez au [portail Azure](https://portal.azure.com).
  2. Dans le menu Hub, sélectionnez **Toutes les ressources**.
  3. Sélectionnez la ressource.
  4. Dans le panneau des ressources, cliquez sur **Paramètres**. Ici, vous pouvez afficher et mettre à jour les clés secrètes existantes.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Si je transfère l’abonnement au milieu d’un cycle de facturation, le destinataire paye-t-il pour l’intégralité du cycle ?

L’expéditeur est responsable du paiement pour toute opération déclarée avant l’achèvement du transfert. Le destinataire est responsable des opérations déclarées à compter de la réalisation effective du transfert. Certaines opérations peuvent avoir été effectuées avant le transfert mais déclarées ultérieurement. L’utilisation figure sur la facture du destinataire.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Le destinataire a-t-il accès à l’historique des opérations et de facturation ?

  Les seules informations accessibles au destinataire sont le montant de la dernière facture, ou le solde actuel si l’abonnement a été transféré avant l’émission de la première facture. L’utilisation restante et l’historique de facturation ne sont pas transférés avec l’abonnement.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>L’offre peut-elle être modifiée durant un transfert ?

L’offre doit rester inchangée. Pour modifier votre offre, consultez la page [Changer d’offre pour votre abonnement Azure](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Puis-je transférer un abonnement sur un compte d’utilisateur dans un autre pays ?

Non, le transfert d’un abonnement sur un compte d’utilisateur dans un autre pays n’est pas pris en charge. Le compte d’utilisateur du destinataire doit se trouver dans le même pays.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Le destinataire peut-il utiliser un autre mode de paiement ?

Oui. Mais l’historique de facturation de l’abonnement est désormais réparti entre deux comptes.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Le transfert d’un abonnement Azure a-t-il des conséquences sur le mode de paiement ?

Afin d’accepter un transfert d’abonnement, une carte de crédit ou un mode de paiement similaire doivent être fournis pour le paiement de l’abonnement. Par exemple, si Bob transfère un abonnement à Jane et que Jane accepte le transfert, Jane doit également fournir un mode de paiement qu’elle a utilisé pour payer l’abonnement. Une fois le transfert terminé, Jane reçoit une facture pour l’abonnement, et non Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Comment migrer des données et des services de mon abonnement Azure vers un nouvel abonnement ?

Si vous ne pouvez pas transférer la propriété de l’abonnement, vous pouvez migrer vos ressources manuellement. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.