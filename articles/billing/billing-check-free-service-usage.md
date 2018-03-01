---
title: "Surveiller et suivre l’utilisation des services gratuits - Azure | Microsoft Docs"
description: "Découvrez comment vérifier l’utilisation des services gratuits. Utilisez le portail Azure et le fichier csv d’utilisation."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 8bc63091dfba822f9839f61dd12c212154ba695d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Vérifier l’utilisation des services gratuits inclus avec votre compte gratuit Azure 

Vous n’êtes pas facturé pour les services inclus gratuitement avec un compte gratuit Azure, sauf si vous dépassez les limites de ces services. Pour rester dans les limites, vous pouvez utiliser le portail Azure ou votre fichier d’utilisation afin de surveiller et de suivre l’utilisation des services gratuits. 

## <a name="check-usage-on-the-azure-portal"></a>Vérifier l’utilisation dans le portail Azure

1.  Connectez-vous au [portail Azure]( http://portal.azure.com).

2.  Dans la zone de navigation de gauche, sélectionnez **Tous les services**.

3.  Sélectionnez **Abonnements**.

4.  Sélectionnez l’abonnement que vous avez créé lors de votre inscription pour un compte gratuit.

    ![Capture d’écran qui montre tous les abonnements](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  La section Vue d’ensemble contient des informations essentielles sur votre abonnement, telles que l’ID d’abonnement, le type d’offre et le nom de l’abonnement. Vous y trouverez également des informations sur l’expiration du crédit de votre compte gratuit.

    ![Capture d’écran qui montre des informations essentielles sur l’abonnement](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Faites défiler vers le bas jusqu’à trouver des informations sur les coûts actuels et prévus. Le coût comprend l’utilisation des services non inclus avec votre compte gratuit et l’utilisation dépassant les limites des services gratuits. 

    ![Capture d’écran qui montre des informations sur les coûts d’abonnement](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  La dernière partie de la section Vue d’ensemble comporte un tableau sur l’utilisation des services gratuits. 

    ![Capture d’écran qui montre l’utilisation des services gratuits](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Le tableau contient les colonnes suivantes :

* **Nom du compteur :** identifie l’unité de mesure du compteur consommé. Pour en savoir plus sur le mappage entre service et compteur, consultez [Présentation du mappage entre les services gratuits et les compteurs](billing-understand-free-service-meter-mapping.md). 
* **Utilisation/Limite :** utilisation et limite du mois en cours pour le compteur. Ces informations figurent aussi dans la barre d’état.
* **État :** état d’utilisation du compteur. En fonction de votre modèle d’utilisation, vous pouvez avoir l’un des états suivants :
  * **Non utilisé :** vous n’avez pas utilisé le compteur, ou l’utilisation pour le compteur n’a pas atteint le système de facturation.
  * **Dépassement le \<Date> :** vous avez dépassé la limite pour le compteur le \<Date>.
  * **Dépassement peu probable :** il est peu probable que vous dépassiez la limite pour le compteur.
  * **Dépassement le \<Date> :** il est probable que vous dépasserez la limite pour le compteur le \<Date>.


## <a name="check-usage-through-the-usage-file"></a>Vérifier l’utilisation par le biais du fichier d’utilisation

Votre fichier d’utilisation fournit des informations précises sur votre abonnement Azure. Vous pouvez télécharger votre fichier d’utilisation mensuelle et quotidienne à partir du Centre des comptes Azure. Pour découvrir comment télécharger le fichier d’utilisation et comprendre l’accès requis, consultez [Télécharger ou afficher votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md). Pour en savoir plus sur les colonnes du fichier d’utilisation, consultez [Comprendre les termes figurant sur le fichier détaillant les frais d’utilisation Microsoft Azure](billing-understand-your-usage.md). 

Le fichier d’utilisation contient des informations sur l’utilisation pour les services gratuits et payants. La mention **Gratuit** est ajoutée à la fin du nom du compteur pour les services gratuits. Pour rechercher les compteurs des services gratuits, ouvrez le fichier dans Excel et filtrez la colonne **Catégorie du compteur** pour afficher les cellules qui contiennent le texte **- Gratuit** (utilisez Filtres textuels &rarr; filtre Contient). &nbsp;

![Capture d’écran qui montre l’utilisation des services gratuits](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
