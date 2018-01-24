---
title: "Modifier votre carte de crédit pour Azure | Microsoft Docs"
description: "Décrit comment modifier la carte de crédit utilisée pour payer un abonnement Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: 9cab81b6072c6f096f6f1a419cebcca9630ebde3
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/23/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Ajouter, mettre à jour ou supprimer une carte de crédit ou débit pour Azure

Dans le Centre des comptes, vous pouvez ajouter une nouvelle carte de crédit, mettre à jour une carte de crédit existante ou supprimer une carte de crédit que vous n’utilisez pas. Vous devez être [administrateur de compte](billing-subscription-transfer.md#whoisaa) pour apporter ces modifications.

**Vous voulez changer pour payer par facture ?** Consultez [Paiement des abonnements Azure par facture](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Ajouter une nouvelle carte de crédit ou débit

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte.
1. Sélectionnez un abonnement.
1. Dans la partie droite de la page, sélectionnez **Manage payment methods (Gérer les modes de paiement)**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Sélectionnez « + » pour ajouter une carte.

    ![Capture d’écran qui montre l’option de modification en regard du mode de paiement.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Entrez les détails de la carte de crédit ou débit.
1. Sélectionnez **Enregistrer**. 

Si vous obtenez une erreur après avoir ajouté la carte bancaire, consultez [Carte bancaire est refusée lors de l’inscription à Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Mettre à jour une carte de crédit ou débit existante

Si le numéro de votre carte bancaire reste le même en cas de renouvellement, mettez à jour les détails de la carte bancaire existante, tels que la date d’expiration. Si votre numéro de carte bancaire change, en cas de perte, de vol ou d’expiration, procédez comme indiqué dans la section [Ajouter une nouvelle carte bancaire à votre compte](#addcard). Vous n’avez pas besoin de mettre à jour le cryptogramme visuel.

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte.
1. Sélectionnez l’abonnement lié à la carte.
1. Sélectionnez **Gérer les modes de paiement**.
1. Sélectionnez **Modifier** en regard de la carte à mettre à jour.
1. Mettez à jour les détails de la carte de crédit ou débit.
1. Sélectionnez **Enregistrer**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Utiliser une autre carte de crédit pour l’abonnement Azure

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte.
1. Sélectionnez l’abonnement lié à la carte.
1. Dans la partie droite de la page, sélectionnez **Manage payment methods (Gérer les modes de paiement)**.
1. Cliquez sur **Utiliser à la place** en regard de la carte à utiliser. Cette opération met également à jour tous les autres abonnements actuellement associés à cette carte. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Supprimer une carte de crédit ou débit du compte

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions) en tant qu’administrateur de compte.
1. Sélectionnez l’abonnement lié à la carte.
3. Dans la partie droite de la page, sélectionnez **Manage payment methods (Gérer les modes de paiement)**.
4. Cliquez sur **Supprimer** en regard de la carte bancaire que vous souhaitez supprimer.

Vous ne pouvez pas supprimer une carte bancaire de votre compte Azure si elle est associée à d’autres abonnements Microsoft actifs. Supprimez la carte de crédit de tous les abonnements actifs avec Microsoft, puis réessayez.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Mon abonnement est désactivé. Pourquoi je ne peux pas supprimer ma carte de crédit ?

Une fois que votre abonnement est désactivé ou annulé, nous attendons 90 jours avant de supprimer définitivement votre abonnement. Nous conservons votre mode de paiement dans nos dossiers pendant la période de rétention au cas où vous souhaiteriez réactiver l’abonnement. Après cela, l’abonnement est complètement supprimé.

Si vous avez besoin de supprimer votre carte de crédit avant la fin de la période de rétention de 90 jours, [réactivez votre abonnement](billing-subscription-become-disable.md). Si vous ne pouvez pas le réactiver, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Pourquoi je reçois continuellement le message « Votre ouverture de session a expiré. Cliquez ici pour en ouvrir une nouvelle » ?

Si vous recevez continuellement ce message d’erreur alors que vous vous êtes déjà déconnecté puis reconnecté, réessayez avec une session de navigation privée.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Comment utiliser une carte différente pour chacun de mes abonnements ?

Malheureusement, si vos abonnements utilisent déjà la même carte, il n’est pas possible de les distinguer pour qu’ils utilisent des cartes différentes. Toutefois, quand vous souscrivez un nouvel abonnement, vous pouvez choisir d’utiliser un nouveau mode de paiement pour lui.

### <a name="how-do-i-make-payments"></a>Comment payer ?

Si vous configurez une carte de crédit comme mode de paiement, nous débitons automatiquement votre carte à l’issue de chaque période de facturation. Vous n’avez rien à faire.

Si vous [payez par facture](billing-how-to-pay-by-invoice.md), envoyez votre paiement à l’endroit indiqué au bas de celle-ci.

### <a name="how-do-i-make-a-one-time-payment"></a>Comment effectuer un paiement ponctuel ?

Malheureusement, Azure ne prend pas en charge les paiements ponctuels uniques par carte de crédit. 

### <a name="how-do-i-change-the-tax-id"></a>Comment modifier l’ID de taxe ?

Pour ajouter ou mettre à jour l’ID de taxe, accédez à la section[**Profil** dans le Centre des comptes Azure](https://account.azure.com/Profile), puis sélectionnez **Enregistrement de taxe**. Cet ID de taxe est utilisé pour les calculs d’exemption de taxe et figure sur votre facture.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
