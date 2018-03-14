---
title: "Déplacer des ressources Azure vers un nouveau groupe de ressources ou abonnement | Microsoft Docs"
description: "Utilisez Azure Resource Manager ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources ou abonnement."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6a1d56a270ae0c44db18f26091cc3c550a37e420
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cet article vous montre comment déplacer des ressources vers un nouvel abonnement ou un nouveau groupe de ressources dans le même abonnement. Vous pouvez utiliser le portail, PowerShell, Azure CLI ou l’API REST pour déplacer des ressources. Les opérations de déplacement de cet article sont disponibles sans assistance du support technique Azure.

Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant l’opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes de ressources tant que le déplacement n’est pas terminé. Ce verrou signifie que vous ne pouvez pas ajouter, mettre à jour ou supprimer des ressources dans les groupes de ressources, mais il ne signifie pas que les ressources sont figées. Par exemple, si vous déplacez un serveur SQL Server et sa base de données vers un nouveau groupe de ressources, une application qui utilise la base de données ne rencontre aucune interruption de service. Elle peut toujours lire et écrire dans la base de données.

Vous ne pouvez pas modifier l’emplacement de la ressource. Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.

> [!NOTE]
> Cet article décrit le déplacement des ressources dans une offre de compte Azure. Si vous souhaitez en fait modifier votre offre de compte Azure (par exemple, en procédant à une mise à niveau d’une version par paiement à l’utilisation vers une version par prépaiement) tout en continuant à utiliser vos ressources existantes, consultez [Changer d’offre pour votre abonnement Azure](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Liste de contrôle avant le déplacement de ressources

Plusieurs étapes importantes doivent être effectuées avant de déplacer une ressource. Vérifiez ces conditions pour prévenir d'éventuelles erreurs.

1. Les abonnements source et de destination doivent exister dans le même [client Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Pour vérifier que les deux abonnements ont le même ID client, utilisez Azure PowerShell ou Azure CLI.

  Pour Azure PowerShell, utilisez :

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Pour l’interface de ligne de commande Azure, consultez :

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Si les ID client pour les abonnements source et de destination ne sont pas identiques, utilisez les méthodes suivantes pour rapprocher les ID client : 

  * [Transfert de la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)
  * [Associer ou ajouter un abonnement Azure à Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)

2. Le service doit activer la possibilité de déplacer des ressources. Cet article répertorie les services permettant de déplacer des ressources et les services qui ne le permettent pas.
3. L’abonnement de destination doit être inscrit pour le fournisseur de la ressource déplacée. Sinon, vous recevez une erreur indiquant que **l’abonnement n’est pas inscrit pour un type de ressource**. Vous pouvez rencontrer ce problème lors du déplacement d’une ressource vers un nouvel abonnement qui n’a jamais été utilisé avec ce type de ressource.

  Pour PowerShell, utilisez les commandes suivantes pour obtenir l’état de l’inscription :

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Pour inscrire un fournisseur de ressources, utilisez :

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Pour l’interface CLI d’Azure, utilisez les commandes suivantes pour obtenir l’état de l’inscription :

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Pour inscrire un fournisseur de ressources, utilisez :

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

## <a name="when-to-call-support"></a>Quand appeler le support technique

Vous pouvez déplacer la plupart des ressources via les opérations en libre-service présentées dans cet article. Utilisez les opérations en libre-service pour :

* Déplacer des ressources Resource Manager.
* Déplacer des ressources classiques conformément aux [limitations du déploiement classique](#classic-deployment-limitations).

Contactez le [support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) quand vous devez :

* Déplacer vos ressources vers un nouveau compte Azure (et client Azure Active Directory) et que vous avez besoin d’aide concernant les instructions de la section précédente.
* Déplacer des ressources classiques, mais que vous rencontrez des problèmes avec les limitations.

## <a name="services-that-enable-move"></a>Services permettant le déplacement

Les services qui permettent le déplacement vers un nouveau groupe de ressources et un nouvel abonnement sont les suivants :

* API Management
* Applications App Service (applications web) : consultez [Limitations d’App Service](#app-service-limitations)
* Application Insights
* Automatisation
* Azure Cosmos DB
* Batch
* Bing Maps
* CDN
* Cloud Services : consultez [Limitations relatives au déploiement Classic](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Hubs
* Clusters HDInsight - voir [Limitations de HDInsight](#hdinsight-limitations)
* IoT Hubs
* Key Vault
* Équilibreurs de charge
* Logic Apps
* Machine Learning
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Power BI
* Cache Redis
* Scheduler
* action
* Gestion de serveur
* Service Bus
* Service Fabric
* Stockage
* Storage (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Stream Analytics - Les tâches Stream Analytics ne peuvent pas être déplacées lorsqu’elles sont en cours d’exécution.
* Serveur de base de données SQL : la base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées.
* Traffic Manager
* Machines virtuelles : les machines virtuelles avec des disques gérés ne peuvent pas être déplacées. Voir [Limitations relatives aux machines virtuelles](#virtual-machines-limitations)
* Virtual Machines (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Groupes identiques de machines virtuelles : consultez [Limitations relatives aux machines virtuelles](#virtual-machines-limitations)
* Réseaux virtuels : consultez [Limitations relatives aux réseaux virtuels](#virtual-networks-limitations)
* Passerelle VPN

## <a name="services-that-do-not-enable-move"></a>Services qui ne permettent pas le déplacement

Les services qui ne permettent pas actuellement le déplacement d’une ressource sont les suivants :

* Services de domaine AD
* Service de contrôle d’intégrité hybride Active Directory
* Application Gateway
* BizTalk Services
* Service de conteneur
* ExpressRoute
* Laboratoires DevTest : le déplacement vers un nouveau groupe de ressources dans le même abonnement est activé, mais le déplacement entre abonnements n’est pas activé.
* Dynamics LCS
* Applications gérées
* Disques gérés : consultez [Limitations relatives aux machines virtuelles](#virtual-machines-limitations)
* Coffre Recovery Services : par ailleurs, ne déplacez pas les ressources de calcul, de réseau et de stockage associées au coffre Recovery Services. Consultez [Limitations de Recovery Services](#recovery-services-limitations).
* Sécurité
* StorSimple Device Manager
* Réseaux virtuels (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Limitations relatives aux machines virtuelles

Les disques gérés ne prennent pas en charge le déplacement. Cette restriction signifie également que plusieurs ressources associées ne peuvent pas être déplacées. Vous ne pouvez pas déplacer les éléments suivants :

* Disques gérés
* Machines virtuelles avec des disques gérés
* Images créées à partir de disques gérés
* Instantanés créés à partir de disques gérés
* Groupes à haute disponibilité comprenant des machines virtuelles avec des disques gérés

Les machines virtuelles auxquelles des plans sont associés créées à partir de ressources de la Place de marché ne peuvent pas être déplacées entre des groupes de ressources ou des abonnements. Déprovisionnez la machine virtuelle dans l’abonnement actuel, puis redéployez-la dans le nouvel abonnement.

Les machines virtuelles avec un certificat stocké dans Key Vault peuvent être déplacées vers un nouveau groupe de ressources dans le même abonnement, mais pas entre abonnements.

## <a name="virtual-networks-limitations"></a>Limitations de réseaux virtuels

Pour déplacer un réseau virtuel homologué, vous devez d’abord désactiver l’homologation du réseau virtuel. Une fois l’homologation désactivée, vous pouvez déplacer le réseau virtuel. Après le déplacement, réactivez l’homologation du réseau virtuel.

Vous ne pouvez pas déplacer un réseau virtuel vers un autre abonnement s’il contient un sous-réseau avec des liens de navigation dans les ressources. Par exemple, si une ressource Cache Redis est déployée dans un sous-réseau, ce sous-réseau possède un lien de navigation dans les ressources.

## <a name="app-service-limitations"></a>limitations d’App Service

Les limitations imposées diffèrent selon que les ressources App Service sont déplacées au sein d’un abonnement ou vers un nouvel abonnement.

### <a name="moving-within-the-same-subscription"></a>Déplacement au sein d’un même abonnement

Lorsque vous déplacez une application web _au sein du même abonnement_, vous ne pouvez pas déplacer les certificats SSL téléchargés. Toutefois, vous pouvez déplacer une application web vers le nouveau groupe de ressources sans emporter son certificat SSL téléchargé, et la fonctionnalité SSL de votre application peut continuer de fonctionner. 

Si vous souhaitez déplacer le certificat SSL avec l’application web, suivez ces étapes :

1.  Supprimer le certificat chargé à partir de l’application web
2.  Déplacer l’application web
3.  Charger le certificat sur l’application web déplacée

### <a name="moving-across-subscriptions"></a>Déplacement entre différents abonnements

Lors du déplacement d’une application Web _entre des abonnements_, les limites suivantes s’appliquent :

- Le groupe de ressources de destination ne doit avoir aucune ressource App Service existante. Les ressources App Service comprennent :
    - Web Apps
    - Plans App Service
    - Certificats SSL chargés ou importés
    - Environnements App Service
- Toutes les ressources App Service du groupe de ressources doivent être déplacées simultanément.
- Les ressources App Service ne peuvent être déplacées qu’à partir du groupe de ressources dans lequel elles ont été créées à l’origine. Si une ressource App Service n’est plus dans son groupe de ressources d’origine, elle doit d’abord réintégrer ce groupe avant de pouvoir être déplacée entre les abonnements. 

## <a name="classic-deployment-limitations"></a>Limitations relatives au déploiement Classic

Les options de déplacement des ressources déployées avec le modèle classique diffèrent selon que vous déplaciez les ressources au sein d’un abonnement ou vers un nouvel abonnement.

### <a name="same-subscription"></a>Même abonnement

Lors du déplacement de ressources d’un groupe de ressources vers un autre au sein du même abonnement, les restrictions suivantes s’appliquent :

* Les réseaux virtuels (classiques) ne peuvent pas être déplacés.
* Les machines virtuelles (classiques) doivent être déplacées avec le service cloud.
* Le service cloud ne peut être déplacé que lorsque le déplacement comprend toutes ses machines virtuelles.
* Un seul service cloud peut être déplacé à la fois.
* Un seul compte de stockage (classique) peut être déplacé à la fois.
* Vous ne pouvez pas déplacer un compte de stockage (classique) dans la même opération avec une machine virtuelle ou un service cloud.

Pour déplacer des ressources classiques vers un nouveau groupe de ressources dans le même abonnement, les opérations de déplacement standard via le [portail](#use-portal), [Azure PowerShell](#use-powershell), [l’interface CLI Azure](#use-azure-cli) ou [l’API REST](#use-rest-api). Vous utilisez les mêmes opérations que vous celles que vous utilisez pour déplacer des ressources Resource Manager.

### <a name="new-subscription"></a>Nouvel abonnement

Lors du déplacement de ressources vers un nouvel abonnement, les restrictions suivantes s’appliquent :

* Toutes les ressources classiques de l’abonnement doivent être déplacées au cours de la même opération.
* L’abonnement cible ne doit pas contenir d’autres ressources classiques.
* Le déplacement peut uniquement être demandé par le biais d’une API REST distincte pour les déplacements classiques. Les commandes de déplacement standard de Resource Manager ne fonctionnent pas lors du déplacement de ressources classiques vers un nouvel abonnement.

Pour déplacer des ressources classiques vers un nouvel abonnement, utilisez des opérations REST spécifiques aux ressources classiques. Pour utiliser REST, procédez comme suit :

1. Vérifiez si l’abonnement source peut participer à un déplacement entre abonnements. Utilisez l’opération suivante :

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     Dans le corps de la demande, spécifiez :

  ```json
  {
    "role": "source"
  }
  ```

     La réponse pour l’opération de validation est au format suivant :

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Vérifiez si l’abonnement de destination peut participer à un déplacement entre abonnements. Utilisez l’opération suivante :

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     Dans le corps de la demande, spécifiez :

  ```json
  {
    "role": "target"
  }
  ```

     La réponse est dans le même format que la validation de l’abonnement source.
3. Si les deux abonnements sont validés, déplacez toutes les ressources classiques d’un abonnement à l’autre via l’opération suivante :

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    Dans le corps de la demande, spécifiez :

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Cette opération peut prendre plusieurs minutes.

## <a name="recovery-services-limitations"></a>Limitations de Recovery Services

Le déplacement n’est pas possible pour les ressources de stockage, de réseau ou de calcul utilisées pour configurer la récupération d’urgence avec Azure Site Recovery.

Par exemple, supposons que vous avez configuré la réplication de vos machines locales vers un compte de stockage (Storage1) et que vous souhaitez que la machine protégée apparaisse après le basculement vers Azure comme une machine virtuelle (VM1) connectée à un réseau virtuel (Network1). Vous ne pouvez pas déplacer ces ressources Azure (Storage1, VM1 et Network1) sur différents groupes de ressources dans le même abonnement ou sur différents abonnements.

Pour déplacer une machine virtuelle inscrite dans la **sauvegarde Azure** entre les groupes de ressources :
 1. Interrompre temporairement la sauvegarde et conserver les données de sauvegarde
 2. Déplacer la machine virtuelle vers le groupe de ressources cible
 3. Reprotégez-la sous le même coffre ou sous un nouveau coffre. Les utilisateurs peuvent effectuer des restaurations à partir des points de restauration créés avant le déplacement.
Si l’utilisateur déplace la machine virtuelle sauvegardée entre des abonnements, les étapes 1 et 2 restent les mêmes. À l’étape 3, l’utilisateur doit protéger la machine virtuelle sous un nouveau coffre existant/créé dans l’abonnement cible. Le coffre Recovery Services ne prend pas en charge les sauvegardes lors du passage d’un abonnement à un autre.

## <a name="hdinsight-limitations"></a>Limitations de HDInsight

Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.

Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

## <a name="search-limitations"></a>Limitations de recherche

Vous ne pouvez pas déplacer simultanément plusieurs ressources de recherche placés dans des régions différentes.
Dans ce cas, vous devez les déplacer séparément.

## <a name="use-portal"></a>Utilisation du portail

Pour déplacer des ressources, sélectionnez le groupe de ressources contenant ces ressources, puis sélectionnez le bouton **Déplacer**.

![Déplacer des ressources](./media/resource-group-move-resources/select-move.png)

Indiquez si vous déplacez les ressources vers un nouveau groupe de ressources ou vers un nouvel abonnement.

Sélectionnez les ressources à déplacer et le groupe de ressources de destination. Confirmez que vous devez mettre à jour les scripts de ces ressources et sélectionnez **OK**. Si vous avez sélectionné l’icône Modifier l’abonnement à l’étape précédente, vous devez également sélectionner l’abonnement de destination.

![Sélectionner la destination](./media/resource-group-move-resources/select-destination.png)

Dans **Notifications**, vous voyez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsqu’elle est terminée, vous êtes informé du résultat.

![afficher les résultats du déplacement](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Utiliser PowerShell

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) . L’exemple suivant vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre `DestinationSubscriptionId`.

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande [az resource move](/cli/azure/resource?view=azure-cli-latest#az_resource_move) . Fournissez les ID des ressources à déplacer. L’exemple suivant vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources. Dans le paramètre `--ids`, spécifiez une liste séparée par des espaces des ID des ressources à déplacer.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pour déplacer des ressources vers un nouvel abonnement, spécifiez le paramètre `--destination-subscription-id`.

## <a name="use-rest-api"></a>Avec l’API REST

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération REST de déplacement, consultez [Déplacer des ressources](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>étapes suivantes

* Pour plus d’informations sur les applets de commande PowerShell permettant de gérer votre abonnement, consultez [Utilisation d’Azure PowerShell avec Resource Manager](powershell-azure-resource-manager.md).
* Pour plus d’informations sur les commandes de l’interface de ligne de commande Azure permettant de gérer votre abonnement, consultez [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Pour plus d’informations sur les fonctionnalités du portail permettant de gérer votre abonnement, consultez [Utilisation du Portail Azure pour gérer les ressources](resource-group-portal.md).
* Pour plus d’informations sur l’application d’une organisation logique à vos ressources, consultez [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
