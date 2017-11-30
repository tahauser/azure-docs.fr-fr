---
title: "Créer une machine virtuelle avec une adresse IP publique statique - Azure CLI | Microsoft Docs"
description: "Apprenez à créer une machine virtuelle avec une adresse IP publique statique à l’aide de l’interface de ligne de commande Azure (CLI)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c50f685745a645b5fbe383a5fe4726faa0e36345
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Créer une machine virtuelle avec une adresse IP publique statique à l’aide d’Azure CLI

> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interface de ligne de commande Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modèle](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (classique)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Créer la machine virtuelle

Les valeurs des variables comprises entre symboles "" dans les étapes suivantes créent des ressources avec ces paramètres à partir du scénario. Modifiez les valeurs, le cas échéant, en fonction de votre environnement.

1. Installez [Azure CLI 2.0](/cli/azure/install-az-cli2) si vous ne l’avez pas encore fait.
2. Créez une paire de clés SSH publique et privée pour les machines virtuelles Linux en effectuant les étapes décrites dans l’article [Créer une paire de clés publique et privée SSH pour les machines virtuelles Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Dans un interpréteur de commandes, connectez-vous par le biais de la commande `az login`.
4. Créez la machine virtuelle en exécutant le script suivant sur un ordinateur Linux ou Mac. L’adresse IP publique Azure, le réseau virtuel, l’interface réseau et les ressources de machine virtuelle doivent se trouver au même emplacement. Bien que les ressources n’aient pas toutes à se trouver dans le même groupe de ressources, c’est le cas dans le script suivant.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

En plus de créer une machine virtuelle, le script génère :
- Un disque unique géré par compte Premium par défaut, mais vous pouvez choisir un autre type de disque. Consultez l’article [Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour plus d’informations.
- Des ressources de réseau virtuel, de sous-réseau, de carte réseau et d’adresse IP publique. Vous pouvez également utiliser des ressources *existantes* en matière de réseau virtuel, sous-réseau, carte réseau ou adresse IP publique. Pour savoir comment utiliser des ressources réseau existantes au lieu de créer des ressources supplémentaires, utilisez `az vm create -h`.

## <a name = "validate"></a>Valider la création de la machine virtuelle et l’adresse IP publique

1. Entrez la commande `az resource list --resouce-group IaaSStory --output table` pour afficher la liste des ressources créées par le script. La sortie retournée doit comporter 5 ressources : une interface réseau, un disque, une adresse IP publique, un réseau virtuel et une machine virtuelle.
2. Entrez la commande `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Dans la sortie retournée, la valeur **IpAddress** et la valeur **PublicIpAllocationMethod** sont *Statique*.
3. Avant d’exécuter la commande suivante, supprimez les <>, remplacez *Username* par le nom que vous avez utilisé pour la variable **Username** du script, et remplacez *ipAddress* par la valeur **ipAddress** de l’étape précédente. Entrez la commande suivante pour vous connecter à la machine virtuelle : `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Supprimer la machine virtuelle et les ressources associées

Nous vous recommandons de supprimer les ressources créées durant cet exercice si vous ne les utilisez pas en production. La machine virtuelle, l’adresse IP publique et le disque occasionnent des frais tant que ces ressources sont configurées. Pour supprimer les ressources créées durant cet exercice, procédez comme suit :

1. Exécutez la commande `az resource list --resource-group IaaSStory` pour afficher les ressources du groupe de ressources.
2. Vérifiez qu’il n’existe aucune autre ressource dans le groupe de ressources que celles créées par le script dans cet article. 
3. Pour supprimer toutes les ressources créées durant cet exercice, exécutez la commande `az group delete -n IaaSStory`. La commande supprime le groupe de ressources et les ressources qu’il contient.

## <a name="next-steps"></a>Étapes suivantes

Tout le trafic réseau peut circuler vers et en provenance de la machine virtuelle créée dans le cadre de cet article. Vous pouvez définir des règles entrantes et sortantes au sein d’un groupe de sécurité réseau afin de limiter le trafic susceptible de circuler vers et en provenance de l’ interface réseau, du sous-réseau ou des deux. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Vue d’ensemble d’un groupe de sécurité réseau](virtual-networks-nsg.md).