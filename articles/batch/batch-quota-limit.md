---
title: Quotas et limites du service Azure Batch | Microsoft Docs
description: En savoir plus sur les contraintes, les limites et les quotas par défaut d’Azure Batch, et comment demander une augmentation de quota
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3cc833e456571b63fa03574808529c8c501d7ab5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="batch-service-quotas-and-limits"></a>Quotas et limites du service Batch

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées au service Batch. La plupart de ces limites représentent des quotas par défaut appliqués par Azure au niveau de l’abonnement ou du compte. Cet article décrit ces paramètres par défaut, et comment vous pouvez demander une augmentation de ces quotas.

Gardez ces quotas à l’esprit quand vous concevez et que vous augmentez vos charges de travail Batch. Par exemple, si votre pool n’atteint pas le nombre cible de nœuds de calcul que vous avez spécifié, vous avez peut-être atteint la limite du quota de cœurs de votre compte Batch.

Vous pouvez exécuter plusieurs charges de travail Batch dans un compte Batch ou répartir vos charges de travail entre plusieurs comptes Batch se trouvant dans le même abonnement mais dans différentes régions Azure.

Si vous envisagez d’exécuter des charges de travail de production dans Batch, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Si vous souhaitez augmenter un quota, vous pouvez ouvrir une [demande de service clientèle](#increase-a-quota) en ligne gratuitement.

> [!NOTE]
> Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.
> 
> 

## <a name="resource-quotas"></a>Quotas de ressources
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de cœurs en mode Abonnement utilisateur

Si vous avez créé un compte Batch avec le mode d’allocation de pool défini sur **abonnement utilisateur**, les quotas sont appliqués de manière différente. Dans ce mode, les machines virtuelles Batch et les autres ressources sont créées directement dans votre abonnement quand un pool est créé. Les quotas de cœurs Azure Batch ne s’appliquent pas à un compte créé dans ce mode. Seuls s’appliquent les quotas de votre abonnement imposés aux cœurs de calcul régionaux et aux autres ressources. Pour en savoir plus sur ces quotas, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Autres limites
| **Ressource** | **Limite maximale** |
| --- | --- |
| [Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul |4 x nombre de cœurs de nœud |
| [Applications](batch-application-packages.md) par compte Batch |20 |
| Packages d’applications par application |40 |
| Taille de package d’application (individuel) |Environ 195 Go<sup>1</sup> |
| Taille maximale de la tâche de début | 32 768 caractères<sup>2</sup> |
| Durée de vie maximale de la tâche | 7 jours<sup>3</sup> |
| Nœuds de calcul dans un pool prenant en charge la communication entre nœuds | 100 |

<sup>1</sup> Limite Azure Storage pour la taille d’objet blob de blocs maximale<br />
<sup>2</sup> Inclut les fichiers de ressources et les variables d’environnement<br />
<sup>3</sup> La durée de vie maximale d’une tâche, entre le moment où elle est ajoutée au travail et la fin de son exécution, est de 7 jours. Les tâches terminées sont conservées indéfiniment ; les données de tâches non terminées pendant la durée de vie maximale ne sont pas accessibles.


## <a name="view-batch-quotas"></a>Afficher les quotas Batch
Affichez vos quotas de compte Batch dans le [portail Azure][portal].

1. Sélectionnez **Comptes Batch** dans le portail, puis sélectionnez le compte Batch qui vous intéresse.
2. Sélectionnez **Quotas** dans le menu du compte Batch.
3. Afficher les quotas actuellement appliqués au compte Batch
   
    ![Quotas de compte Batch][account_quotas]



## <a name="increase-a-quota"></a>Augmenter un quota
Suivez ces étapes pour demander une augmentation de quota pour votre compte Batch ou votre abonnement à l’aide du [portail Azure][portal]. Le type d’augmentation de quota varie selon le mode d’allocation de pool de votre compte Batch.

### <a name="increase-a-batch-cores-quota"></a>Augmenter un quota de cœurs Batch 

1. Sélectionnez la mosaïque **Aide + Support** dans le tableau de bord du portail, ou le point d’interrogation (**?**) dans le coin supérieur droit du portail.
2. Sélectionnez **Nouvelle demande de support** > **De base**.
3. Dans **De base** :
   
    a. **Type de problème** > **Quota**
   
    b. Sélectionnez votre abonnement.
   
    c. **Type de quota** > **Batch**
   
    d. **Plan de support** > **Support quota - Inclus**
   
    Cliquez sur **Suivant**.
4. Dans **Problème** :
   
    a. Sélectionnez un **niveau de gravité** en fonction de [l’impact sur votre activité][support_sev].
   
    b. Dans **Détails**, spécifiez chaque quota que vous souhaitez modifier, le nom du compte Batch et la nouvelle limite.
   
    Cliquez sur **Suivant**.
5. Dans **Informations de contact** :
   
    a. Sélectionnez une **méthode de contact préférée**.
   
    b. Vérifiez et entrez les informations de contact requises.
   
    Cliquez sur **Créer** pour envoyer la demande de support.

Une fois que vous avez envoyé votre demande de support, le support Azure vous contactera. Notez que le traitement de la demande peut prendre jusqu’à 2 jours ouvrés.


## <a name="related-topics"></a>Rubriques connexes
* [Création et gestion d’un compte Azure Batch dans le portail Azure](batch-account-create-portal.md)
* [Vue d'ensemble des fonctionnalités d'Azure Batch](batch-api-basics.md)
* [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
