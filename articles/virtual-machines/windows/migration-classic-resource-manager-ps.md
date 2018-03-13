---
title: Migrer vers Resource Manager avec PowerShell | Microsoft Docs
description: "Cet article décrit pas à pas la procédure de migration de ressources IaaS, comme les machines virtuelles (VM), les réseaux virtuels (VNET) et les comptes de stockage, prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager (ARM) à l’aide de commandes Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 38bc949917f6bb56486ad6267044ef2f19e33fcb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell
Ces étapes vous montrent comment utiliser les commandes Azure PowerShell pour migrer des ressources d’infrastructure en tant que service (IaaS) à partir du modèle de déploiement Classic vers le modèle de déploiement Azure Resource Manager.

Si vous le souhaitez, vous pouvez également migrer des ressources à l’aide de [l’Interface de ligne de commande Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Pour plus d’informations sur les scénarios de migration pris en charge, consultez [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Pour un guide détaillé et une procédure pas à pas pour la migration, consultez [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md)

<br>
Voici un diagramme de flux permettant d’identifier l’ordre dans lequel les étapes doivent être exécutées lors d’un processus de migration.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Étape 1 : planifier la migration
Voici quelques bonnes pratiques recommandées lorsque vous évaluez la migration de ressources IaaS d’un environnement Classic vers Resource Manager.

* Lisez les [Fonctionnalités et configurations prises en charge et non prises en charge](migration-classic-resource-manager-overview.md). Si vous avez des machines virtuelles qui utilisent des configurations ou fonctionnalités non prises en charge, nous vous conseillons d’attendre que leur prise en charge soit annoncée. Vous pouvez également supprimer cette fonctionnalité ou modifier cette configuration pour permettre la migration si cela répond à vos besoins.
* Si vous avez déjà des scripts automatisés qui déploient votre infrastructure et vos applications, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Vous pouvez également configurer des environnements de test à l’aide du portail Azure.

> [!IMPORTANT]
> Les passerelles d’application ne sont actuellement pas prises en charge pour la migration de déploiement classique vers Resource Manager. Pour migrer un réseau virtuel classique avec une passerelle d’application, supprimez la passerelle avant d’exécuter une opération de préparation pour déplacer le réseau. Après avoir effectué la migration, reconnectez la passerelle dans Azure Resource Manager.
>
>Les passerelles ExpressRoute se connectant à des circuits ExpressRoute dans un autre abonnement ne peuvent pas être migrées automatiquement. Dans ce cas, supprimez la passerelle ExpressRoute, migrez le réseau virtuel et recréez la passerelle. Pour plus d’informations, cnsultez [Migrer des circuits ExpressRoute et les réseaux virtuels associés du modèle de déploiement classique au modèle de déploiement Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Étape 2 : installer la dernière version d’Azure PowerShell
Il existe deux options principales d’installation d’Azure PowerShell : [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) et [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu. Cet article est basé sur Azure PowerShell version 2.1.0.

Pour connaître la procédure d’installation, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Étape 3 : vérifier que vous êtes administrateur de l’abonnement dans le portail Azure
Pour effectuer cette migration, vous devez être ajouté en tant que coadministrateur de l’abonnement dans le [portail Azure](https://portal.azure.com).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Abonnement**. Si vous ne le voyez pas, sélectionnez **Tous les services**.
3. Recherchez l’entrée d’abonnement appropriée, puis examinez le champ **MON RÔLE**. Pour un coadministrateur, la valeur doit être _Administrateur de compte_.

Si vous n’êtes pas en mesure d’ajouter un coadministrateur, contactez un administrateur de service ou un coadministrateur de l’abonnement afin qu’il vous ajoute.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Étape 4 : définition de votre abonnement inscription pour la migration
Commencez par démarrer une invite de commandes PowerShell. Pour la migration, vous devez configurer votre environnement à la fois pour l’environnement Classic et pour Resource Manager.

Connectez-vous à votre compte pour le modèle Resource Manager.

```powershell
    Login-AzureRmAccount
```

Obtenez tous les abonnements disponibles à l’aide de la commande suivante :

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Définissez votre abonnement Azure pour la session active. Cet exemple définit le nom d’abonnement par défaut sur **My Azure Subscription**. Remplacez l’exemple de nom d’abonnement par le vôtre.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> L’inscription constitue une étape unique, mais vous devez le faire une seule fois avant de tenter la migration. Sans vous inscrire, vous verrez le message d’erreur suivant :
>
> *Requête invalide : l’abonnement n’est pas inscrit pour la migration.*
>
>

Inscrivez-vous auprès du fournisseur de ressources de migration à l’aide de la commande suivante :

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Veuillez patienter cinq minutes le temps que l’inscription se termine. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante :

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Assurez-vous que RegistrationState est `Registered` avant de continuer.

Connectez-vous à votre compte pour le modèle Classic.

```powershell
    Add-AzureAccount
```

Obtenez tous les abonnements disponibles à l’aide de la commande suivante :

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Définissez votre abonnement Azure pour la session active. Cet exemple définit l’abonnement par défaut sur **My Azure Subscription**. Remplacez l’exemple de nom d’abonnement par le vôtre.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Étape 5 : vérification du nombre de processeurs virtuels de machines virtuelles Azure Resource Manager dans la région Azure de votre déploiement ou réseau virtuel actuel
Vous pouvez utiliser la commande PowerShell suivante pour vérifier la quantité de processeurs virtuels dont vous disposez actuellement dans Azure Resource Manager. Pour en savoir plus sur les quotas de processeurs virtuels, consultez [Limites et Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Cet exemple vérifie la disponibilité dans la région **ouest des États-Unis**. Remplacez l’exemple de nom de région par le vôtre.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Étape 6 : exécution de commandes pour effectuer la migration de vos ressources IaaS
> [!NOTE]
> Toutes les opérations décrites ici sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme réessaie alors d’exécuter l’action.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Étape 6.1 : Option 1 - migrer les machines virtuelles vers un service cloud (pas un réseau virtuel)
Obtenez la liste des services cloud à l’aide de la commande suivante, puis choisissez le service cloud que vous voulez migrer. La commande renvoie un message d’erreur si les machines virtuelles du service cloud sont dans un réseau virtuel ou si elles ont des rôles web/de travail.

```powershell
    Get-AzureService | ft Servicename
```

Obtenez le nom du déploiement du service cloud. Dans cet exemple, le nom du service est **Mon service**. Remplacez l’exemple de nom de service avec votre propre nom de service.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Préparez les machines virtuelles dans le service cloud pour la migration. Vous disposez de deux options.

* **Option 1. Migrer les machines virtuelles vers un réseau virtuel créé à partir d’une plateforme**

    La première étape consiste à valider si vous pouvez migrer le service cloud à l’aide des commandes suivantes :

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    La commande ci-dessus affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, passez à l’étape de **préparation** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Option 2. Migrer vers un réseau virtuel existant dans le modèle de déploiement Resource Manager**

    Cet exemple définit le nom de groupe de ressources sur **myResourceGroup**, le nom de réseau virtuel sur **myVirtualNetwork** et le nom du sous-réseau sur **mySubNet**. Remplacez les noms dans l’exemple par les noms de vos propres ressources.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Vérifiez d’abord que vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    La commande ci-dessus affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez passer à l’étape de la préparation :

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Une fois l’opération de préparation effectuée avec une des options précédentes, interrogez l’état de la migration des machines virtuelles. Assurez-vous qu’elles ont l’état `Prepared` .

Cet exemple définit le nom de la machine virtuelle sur **myVM**. Remplacez l’exemple de nom avec votre propre nom de machine virtuelle.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Vérifiez la configuration pour les ressources préparées à l’aide de PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Étape 6.1 : Option 2 - migrer les machines virtuelles vers un réseau virtuel

Pour migrer des machines virtuelles dans un service cloud, vous migrez le réseau virtuel. Les machines virtuelles migrent automatiquement avec le réseau virtuel. Sélectionnez le réseau virtuel dont vous souhaitez effectuer la migration.
> [!NOTE]
> [Migrez une machine virtuelle classique unique](migrate-single-classic-to-resource-manager.md) en créant une nouvelle machine virtuelle Resource Manager avec des disques gérés en utilisant les fichiers de disque dur virtuel (système d’exploitation et données) de la machine virtuelle.
<br>

> [!NOTE]
> Le nom du réseau virtuel peut différer de ce qui est affiché dans le nouveau portail. Le nouveau portail Azure affiche le nom sous la forme `[vnet-name]`, mais le nom réel du réseau virtuel est du type `Group [resource-group-name] [vnet-name]`. Avant de migrer, recherchez le nom du réseau virtuel à l’aide de la commande `Get-AzureVnetSite | Select -Property Name`, ou affichez-le dans l’ancien portail Azure. 

Cet exemple définit le nom de réseau virtuel sur **myVnet**. Remplacez l’exemple de nom de réseau virtuel par le vôtre.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Vous obtenez un message d’erreur pour la validation si le réseau virtuel contient des rôles web/de travail ou des machines virtuelles avec des configurations non prises en charge.
>
>

La première étape consiste à valider si vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

La commande ci-dessus affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez passer à l’étape de la préparation :

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Vérifiez la configuration pour les machines virtuelles préparées à l’aide d’Azure PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>Étape 6.2 : migrer un compte de stockage
Une fois que vous avez terminé la migration des machines virtuelles, nous vous recommandons de migrer les comptes de stockage.

Avant de migrer le compte de stockage, effectuez les vérifications de conditions préalables suivantes :

* **Migrer des machines virtuelles classiques dont les disques sont stockés dans le compte de stockage**

    La commande précédente renvoie les propriétés RoleName et DiskName de tous les disques de machine virtuelle classique dans le compte de stockage. RoleName est le nom de la machine virtuelle à laquelle un disque est joint. Si la commande précédente renvoie des disques, assurez-vous que les machines virtuelles sur lesquelles ces disques sont joints sont migrés avant de migrer le compte de stockage.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Supprimer les disques de machine virtuelle classique détachés stockés dans le compte de stockage**

    Recherchez les disques de machine virtuelle détachés dans le compte de stockage à l’aide de la commande suivante :

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Si la commande ci-dessus renvoie des disques, supprimez-les en utilisant la commande suivante :

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Supprimer les images de machine virtuelle stockées dans le compte de stockage**

    La commande précédente renvoie toutes les images de machine virtuelle avec un disque de système d’exploitation stocké dans le compte de stockage.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     La commande précédente renvoie toutes les images de machine virtuelle avec les disques de données stockés dans le compte de stockage.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Supprimez toutes les images de machine virtuelle renvoyées par les commandes précédentes à l’aide de la commande :
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Validez chaque compte de stockage pour la migration à l’aide de la commande suivante. Dans cet exemple, le nom de compte de stockage est **myStorageAccount**. Remplacez le nom d’exemple par celui de votre propre compte de stockage.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

L’étape suivante consiste à préparer le compte de stockage pour la migration.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Vérifiez la configuration pour le compte de stockage préparé à l’aide d’Azure PowerShell ou du portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Outils de la communauté pour aider à la migration de ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passez en revue les questions fréquemment posées sur la migration des ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
