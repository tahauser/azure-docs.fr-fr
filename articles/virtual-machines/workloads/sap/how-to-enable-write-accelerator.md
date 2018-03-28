---
title: Accélérateur des écritures Azure pour les déploiements SAP | Microsoft Docs
description: Guide des opérations pour les systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d1ca15028590824cef95e3e9c2d957f9883a0e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Accélérateur des écritures Azure pour les déploiements SAP
L’Accélérateur des écritures Azure est une fonctionnalité qui est fournie uniquement pour les machines virtuelles de la série M. Il n’est pas disponible pour les autres séries de machines virtuelles dans Azure. Comme son nom l’indique, cette fonctionnalité vise à améliorer la latence d’E/S des écritures dans le stockage Azure Premium. 

>[!NOTE]
> L’Accélérateur des écritures Azure est disponible en préversion publique et son utilisation nécessite l’approbation de votre ID d’abonnement Azure

La fonctionnalité Accélérateur des écritures Azure est disponible en préversion publique dans les régions suivantes :

- Ouest des États-Unis 2
- Europe de l’Ouest

## <a name="planning-for-using-azure-write-accelerator"></a>Planification de l’utilisation de l’Accélérateur des écritures Azure
Utilisez l’Accélérateur des écritures Azure pour les volumes qui stockent le journal des transactions ou les journaux de restauration d’un SGBD (système de gestion de base de données). Il n’est pas recommandé de l’utiliser pour les volumes de données d’un SGBD. Cette restriction s’explique par le fait que l’Accélérateur des écritures Azure nécessite le montage de disques durs virtuels de stockage Azure Premium sans le cache de lecture supplémentaire qui est disponible pour le stockage Premium. Ce type de mise en cache est surtout intéressant avec les bases de données conventionnelles. Dans la mesure où l’Accélérateur des écritures Azure impacte uniquement les activités d’écriture et n’accélère pas les lectures, la conception prise en charge par SAP consiste à utiliser l’Accélérateur des écritures sur les disques où se trouvent les journaux des transactions ou de restauration des bases de données SAP prises en charge. 

L’Accélérateur des écritures Azure fonctionne uniquement en association avec [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Vous devez donc planifier cette fonctionnalité en conséquence. 

>[!NOTE]
> Comme l’Accélérateur des écritures Azure est une fonctionnalité encore en préversion publique, il ne peut pas être déployé dans des scénarios de production.

L’Accélérateur des écritures Azure prend en charge un nombre limité de disques durs virtuels de stockage Azure Premium par machine virtuelle. Les limites actuelles sont :

- 16 disques durs virtuels pour une machine virtuelle M128xx
- 8 disques durs virtuels pour une machine virtuelle M64xx

> [!IMPORTANT]
> Si vous souhaitez activer ou désactiver l’Accélérateur des écritures Azure pour un volume existant constitué de plusieurs disques de stockage Azure Premium et agrégé par bandes à l’aide des gestionnaires de disques ou de volumes Windows, des espaces de stockage Windows, de la fonctionnalité SOFS Windows, de LVM ou MDADM pour Linux, vous devez l’activer ou le désactiver sur tous les disques du volume en effectuant plusieurs étapes distinctes. **Avant d’activer ou de désactiver l’Accélérateur des écritures Azure dans cette configuration, arrêtez la machine virtuelle Azure**. 


> [!IMPORTANT]
> Si vous souhaitez activer l’Accélérateur des écritures Azure sur un disque Azure existant qui ne fait PAS partie d’un volume constitué de plusieurs disques à l’aide des gestionnaires de disques ou de volumes Windows, des espaces de stockage Windows, de la fonctionnalité SOFS Windows, de LVM ou MDADM pour Linux, vous devez arrêter la charge de travail qui accède au disque Azure. Les applications de base de données qui utilisent le disque Azure DOIVENT également être arrêtées.

> [!IMPORTANT]
> L’activation de l’Accélérateur des écritures Azure sur le disque système de la machine virtuelle provoque le redémarrage de la machine virtuelle. 

L’activation de l’Accélérateur des écritures sur les disques système n’est généralement pas nécessaire pour les configurations de machines virtuelles SAP

### <a name="restrictions-when-using-azure-write-accelerator"></a>Restrictions relatives à l’utilisation de l’Accélérateur des écritures Azure
Quand vous utilisez l’Accélérateur des écritures Azure sur un disque/disque dur virtuel Azure, les restrictions suivantes s’appliquent :

- La mise en cache du disque Premium doit être définie sur Aucune. Les autres modes de mise en cache ne sont pas pris en charge.
- Les instantanés sur un disque avec l’Accélérateur des écritures activé ne sont pas pris en charge. Cette restriction empêche le service Sauvegarde Azure d’effectuer un instantané cohérent des applications sur tous les disques de la machine virtuelle.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Activation de l’Accélérateur des écritures sur un disque spécifique
Les sections ci-après décrivent comment activer l’Accélérateur des écritures Azure sur des disques durs virtuels de stockage Azure Premium.

Pour le moment, l’Accélérateur des écritures peut uniquement être activé par le biais de l’API Rest Azure et de PowerShell. Au cours des prochaines semaines, d’autres méthodes d’activation prises en charge seront proposées dans le portail Azure.

### <a name="prerequisites"></a>Prérequis

Assurez-vous de remplir les prérequis suivants pour pouvoir utiliser l’Accélérateur des écritures Azure :

- Votre ID d’abonnement qui a été utilisé pour déployer la machine virtuelle et le stockage associé doit être approuvé. Contactez votre responsable CSA, GBB ou de compte Microsoft pour faire approuver votre ID d’abonnement. 
- Les disques sur lesquels vous voulez activer l’Accélérateur des écritures Azure doivent être des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Activation par le biais de PowerShell
Dans les versions 5.5.0 et ultérieures du module Azure PowerShell, des modifications ont été apportées aux applets de commande concernées pour permettre l’activation ou la désactivation de l’Accélérateur des écritures Azure sur des disques de stockage Azure Premium spécifiques.
Pour permettre l’activation ou le déploiement de disques pris en charge par l’Accélérateur des écritures, les commandes PowerShell suivantes ont été modifiées et étendues avec un paramètre pour l’accélérateur.

Le nouveau paramètre de commutateur WriteAccelerator a été ajouté aux applets de commande suivantes : 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Si le paramètre n’est pas spécifié, la propriété est définie sur false, et les disques déployés ne sont pas pris en charge par l’Accélérateur des écritures Azure.

Le nouveau paramètre de commutateur OsDiskWriteAccelerator a été ajouté aux applets de commande suivantes : 

- Set-AzureRmVmssStorageProfile

Si le paramètre n’est pas spécifié, la propriété est définie sur false, et les disques déployés n’utilisent pas l’Accélérateur des écritures Azure.

Le nouveau paramètre booléen OsDiskWriteAccelerator (paramètre facultatif et qui n’accepte pas les valeurs Null) a été ajouté aux applets de commande suivantes : 

- Update-AzureRmVM
- Update-AzureRmVmss

Spécifiez $true ou $false pour activer ou désactiver la prise en charge de l’Accélérateur des écritures Azure avec les disques.

Exemples de commandes :

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Les scripts montrés dans les sections suivantes correspondent à deux scénarios courants.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Ajout d’un nouveau disque pris en charge par l’Accélérateur des écritures Azure
Vous pouvez utiliser ce script pour ajouter un nouveau disque à votre machine virtuelle. Le disque créé avec ce script pourra utiliser l’Accélérateur des écritures Azure.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
Changez le nom de la machine virtuelle, le nom du disque, le nom du groupe de ressources, la taille du disque et le LunID du disque en fonction de votre déploiement spécifique.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Activation de l’Accélérateur des écritures Azure sur un disque Azure existant
Si vous devez activer l’Accélérateur des écritures sur un disque existant, faites-le à l’aide de ce script :

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Changez les noms de la machine virtuelle, du disque et du groupe de ressources pour votre propre déploiement. Le script ci-dessus ajoute l’Accélérateur des écritures à un disque existant si le paramètre $newstatus est défini sur $true. Si ce paramètre est défini sur $false, l’Accélérateur des écritures est désactivé sur le disque.

> [!Note]
> L’exécution du script ci-dessus détache le disque spécifié, active l’Accélérateur des écritures sur le disque, puis réattache le disque




### <a name="enabling-through-rest-apis"></a>Activation par le biais des API Rest
Pour effectuer le déploiement à l’aide de l’API Rest Azure, vous devez installer Azure armclient

#### <a name="install-armclient"></a>Installer armclient

Pour pouvoir utiliser armclient, installez-le via Chocolatey, à l’aide de cmd.exe ou PowerShell. Exécutez ces commandes avec des droits élevés (« Exécuter en tant qu’administrateur »).

Avec cmd.exe, exécutez la commande suivante :

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Avec PowerShell, exécutez la commande suivante :

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Vous pouvez maintenant installer armclient en exécutant la commande suivante dans cmd.exe ou PowerShell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Obtenir la configuration actuelle de votre machine virtuelle
Pour changer les attributs de configuration du disque, vous devez d’abord obtenir la configuration actuelle et l’enregistrer dans un fichier JSON. Pour cela, exécutez la commande suivante :

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Remplacez le contenu entre les balises <<   >> par vos propres données, dont le nom souhaité pour le fichier JSON.

Le résultat doit être semblable à ceci :

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

L’étape suivante consiste à mettre à jour le fichier JSON et à activer l’Accélérateur des écritures sur le disque appelé « log1 ». Pour cela, ajoutez cet attribut après l’entrée du cache du disque dans le fichier JSON. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Ensuite, mettez à jour le déploiement existant avec cette commande :

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

Le résultat doit ressembler à l’exemple ci-dessous. Vous pouvez voir que l’Accélérateur des écritures est activé pour un seul disque.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

À présent, le disque est normalement pris en charge par l’Accélérateur des écritures.

 