---
title: "Migrer des machines virtuelles vers Resource Manager à l’aide d’Azure CLI | Microsoft Docs"
description: "Cet article décrit pas à pas la procédure de migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 607ab59dbeb414c69a6272d0aeb00299296bca6a
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure
Ces étapes vous montrent comment utiliser les commandes de l’interface de ligne de commande Azure (CLI) pour migrer des ressources d’infrastructure en tant que service (IaaS) à partir du modèle de déploiement Classic vers le modèle de déploiement Azure Resource Manager. Cet article nécessite [Azure CLI 1.0](../../cli-install-nodejs.md). Étant donné qu’Azure CLI 2.0 est uniquement applicable pour les ressources Azure Resource Manager, il ne peut pas être utilisé pour cette migration.

> [!NOTE]
> Toutes les opérations décrites ici sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme tentera une nouvelle fois l’opération.
> 
> 

<br>
Voici un diagramme de flux permettant d’identifier l’ordre dans lequel les étapes doivent être exécutées lors d’un processus de migration.

![Screenshot that shows the migration steps](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Étape 1 : préparation de la migration
Voici quelques bonnes pratiques recommandées lorsque vous évaluez la migration de ressources IaaS d’un environnement Classic vers Resource Manager.

* Parcourez la liste des [configurations ou fonctionnalités non prises en charge](../windows/migration-classic-resource-manager-overview.md). Si vous avez des machines virtuelles qui utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
* Si vous avez déjà des scripts automatisés qui déploient votre infrastructure et vos applications, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Vous pouvez également configurer des environnements de test à l’aide du portail Azure.

> [!IMPORTANT]
> Les passerelles d’application ne sont actuellement pas prises en charge pour la migration de déploiement classique vers Resource Manager. Pour migrer un réseau virtuel classique avec une passerelle d’application, supprimez la passerelle avant d’exécuter une opération de préparation pour déplacer le réseau. Après avoir effectué la migration, reconnectez la passerelle dans Azure Resource Manager. 
>
>Les passerelles ExpressRoute se connectant à des circuits ExpressRoute dans un autre abonnement ne peuvent pas être migrées automatiquement. Dans ce cas, supprimez la passerelle ExpressRoute, migrez le réseau virtuel et recréez la passerelle. Pour plus d’informations, cnsultez [Migrer des circuits ExpressRoute et les réseaux virtuels associés du modèle de déploiement classique au modèle de déploiement Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Étape 2 : définition de votre abonnement et inscription du fournisseur
Pour les scénarios de migration, vous devez configurer votre environnement à la fois pour l’environnement Classic et pour Resource Manager. [Installez l’interface de ligne de commande Azure](../../cli-install-nodejs.md) et [sélectionnez votre abonnement](/cli/azure/authenticate-azure-cli).

Connectez-vous à votre compte.

    azure login

Sélectionnez votre abonnement Azure à l’aide de la commande suivante.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> L’inscription constitue une étape unique, mais elle doit être effectuée une seule fois avant de tenter la migration. Sans vous inscrire, vous verrez le message suivant 
> 
> *Requête invalide : l’abonnement n’est pas inscrit pour la migration.* 
> 
> 

Inscrivez-vous auprès du fournisseur de ressources de migration à l’aide de la commande suivante. Notez que cette commande expire dans certains cas. Toutefois, l’inscription sera réussie.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Veuillez patienter cinq minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Assurez-vous que RegistrationState est `Registered` avant de continuer.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Passez ensuite l’interface de ligne de commande en mode `asm` .

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Étape 3 : vérification du nombre de processeurs virtuels de machines virtuelles Azure Resource Manager dans la région Azure de votre déploiement ou réseau virtuel actuel
Pour cette étape, vous devrez passer au mode `arm` . Pour ce faire, utilisez la commande suivante.

```
azure config mode arm
```

Vous pouvez utiliser la commande CLI suivante pour vérifier la quantité de processeurs virtuels dont vous disposez actuellement dans Azure Resource Manager. Pour en savoir plus sur les quotas de processeurs virtuels, consultez [Limites et Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Une fois que vous avez procédé à la vérification de cette étape, vous pouvez revenir au mode `asm` .

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Étape 4 : Option 1 - Migration de machines virtuelles dans un service cloud
Obtenez la liste des services cloud à l’aide de la commande suivante, puis choisissez le service cloud que vous voulez migrer. Notez que vous obtiendrez un message d’erreur si les machines virtuelles du service cloud sont dans un réseau virtuel ou si elles ont des rôles Web/de travail.

    azure service list

Exécutez la commande suivante pour obtenir le nom du déploiement du service cloud à partir d’une sortie détaillée. Dans la plupart des cas, le nom du déploiement est le même que le nom de service Cloud.

    azure service show <serviceName> -vv

La première étape consiste à valider si vous pouvez migrer le service cloud à l’aide des commandes suivantes :

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Préparez les machines virtuelles dans le service cloud pour la migration. Vous disposez de deux options.

Si vous voulez migrer les machines virtuelles vers un réseau virtuel créé à partir d’une plateforme, utilisez la commande suivante.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Si vous voulez procéder à leur migration vers un réseau virtuel existant dans le modèle de déploiement Resource Manager, utilisez la commande suivante.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Une fois l’opération de préparation réussie, vous pouvez consulter la sortie détaillée pour voir l’état de la migration des machines virtuelles et vous assurer que leur état est `Prepared` .

    azure vm show <vmName> -vv

Vérifiez la configuration pour les ressources préparées à l’aide de l’interface de ligne de commande ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

    azure service deployment abort-migration <serviceName> <deploymentName>

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Étape 4 : Option 2 - Migration de machines virtuelles dans un réseau virtuel
Sélectionnez le réseau virtuel dont vous souhaitez effectuer la migration. Sachez que, si le réseau virtuel contient des rôles Web/de travail ou des machines virtuelles avec des configurations non prises en charge, vous obtiendrez un message d’erreur pour la validation.

Récupérez tous les réseaux virtuels de l’abonnement à l’aide de la commande suivante.

    azure network vnet list

Vous obtenez un résultat semblable à ce qui suit :

![Capture d’écran de la ligne de commande avec le nom de réseau virtuel entier en surbrillance.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Dans l’exemple ci-dessus, **virtualNetworkName** est le nom complet **« Group classicubuntu16 classicubuntu16 »**.

Vérifiez d’abord que vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Préparez le réseau virtuel de votre choix pour la migration à l’aide de la commande suivante.

    azure network vnet prepare-migration <virtualNetworkName>

Vérifiez la configuration pour les machines virtuelles préparées à l’aide de l’interface de ligne de commande ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

    azure network vnet abort-migration <virtualNetworkName>

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Étape 5 : migration d’un compte de stockage
Une fois que vous avez terminé la migration des machines virtuelles, nous vous recommandons de migrer le compte de stockage.

Préparer le compte de stockage pour la migration à l’aide de la commande suivante

    azure storage account prepare-migration <storageAccountName>

Vérifiez la configuration pour le compte de stockage préparé à l’aide de l’interface de ligne de commande ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante.

    azure storage account abort-migration <storageAccountName>

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Outils de la communauté pour aider à la migration de ressources IaaS de Classic vers Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Passez en revue les questions fréquemment posées sur la migration des ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
