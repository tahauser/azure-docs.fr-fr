---
title: Utiliser Terraform pour créer une machine virtuelle Linux complète dans Azure | Microsoft Docs
description: Découvrez comment utiliser Terraform pour créer et gérer un environnement de machine virtuelle Linux complète dans Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: timlt
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: aaa8235d5fe47e72963ef4942876ea9827a38ef6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Créer une infrastructure de machine virtuelle Linux complète dans Azure avec Terraform

Terraform vous permet de définir et de créer des déploiements d’infrastructures complètes dans Azure. Vous générez des modèles Terraform dans un format lisible pour créer et configurer des ressources Azure de manière cohérente et reproductible. Cet article vous explique comment créer un environnement Linux complet et des ressources de support avec Terraform. Vous pouvez également apprendre à [installer et configurer Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Créer une connexion Azure et un groupe de ressources

Passons en revue chaque section d’un modèle Terraform. Vous pouvez également voir la version complète du [modèle Terraform](#complete-terraform-script) que vous pouvez copier et coller.

La section `provider` indique à Terraform d’utiliser un fournisseur Azure. Afin d’obtenir des valeurs pour *subscription_id*, *client_id*, *client_secret* et *tenant_id*, consultez [Installer et configurer Terraform](terraform-install-configure.md). 

> [!TIP]
> Si vous créez des variables d’environnement pour les valeurs ou que vous utilisez l’[expérience Bash d’Azure Cloud Shell](/azure/cloud-shell/overview) , vous n’avez pas besoin d’inclure les déclarations de variables dans cette section.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

La section suivante crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `eastus` :

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

Dans d’autres sections, vous référencez le groupe de ressources avec *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Création d’un réseau virtuel
La section suivante crée un réseau virtuel nommé *myVnet* dans l’espace d’adressage *10.0.0.0/16* :

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
: La section suivante crée un sous-réseau nommé *mySubnet* dans le réseau virtuel *myVnet*

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Création d’une adresse IP publique
Pour pouvoir accéder à des ressources via Internet, créez et attribuez une adresse IP publique à votre machine virtuelle. La section suivante crée une adresse IP publique nommée *myPublicIP* :

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Créer un groupe de sécurité réseau
Les groupes de sécurité réseau contrôlent le flux du trafic réseau en direction et en provenance de votre machine virtuelle. La section suivante crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* et définit une règle pour autoriser le trafic SSH sur le port TCP 22 :

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    ;
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Créer une carte réseau virtuelle
Une carte réseau virtuelle connecte votre machine virtuelle à un réseau virtuel donné, à une adresse IP publique et à un groupe de sécurité réseau. La section suivante dans un modèle Terraform crée une carte réseau virtuelle nommée *myNIC* connectée aux ressources du réseau virtuel que vous avez créées :

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Créer un compte de stockage pour les diagnostics
Pour stocker les diagnostics de démarrage pour une machine virtuelle, vous avez besoin d’un compte de stockage. Ces diagnostics de démarrage peuvent vous aider à résoudre les problèmes et surveiller l’état de votre machine virtuelle. Le compte de stockage que vous créez sert uniquement à stocker les données de diagnostics de démarrage. Comme chaque compte de stockage doit avoir un nom unique, la section suivante génère du texte aléatoire :

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Vous pouvez maintenant créer un compte de stockage. La section suivante crée un compte de stockage dont le nom est basé sur le texte aléatoire généré à l’étape précédente :

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Créer une machine virtuelle

La dernière étape consiste à créer une machine virtuelle et à utiliser toutes les ressources créées. La section suivante crée une machine virtuelle nommée *myVM* et attache la carte réseau virtuelle nommée *myNIC*. La dernière image *Ubuntu 16.04-LTS* est utilisée et un utilisateur nommé *azureuser* est créé avec l’authentification par mot de passe désactivée.

 Les données de clé SSH sont fournies dans la section *ssh_keys*. Indiquez une clé SSH publique valide dans le champ *key_data*.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Script Terraform complet

Pour rassembler toutes ces sections et voir Terraform en action, créez un fichier appelé *terraform_azure.tf* et collez le contenu suivant :

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Générer et déployer l’infrastructure
Une fois votre modèle Terraform créé, la première étape consiste à initialiser Terraform. Cette étape garantit que Terraform réunit tous les prérequis pour générer votre modèle dans Azure.

```bash
terraform init
```

L’étape suivante consiste à faire examiner et valider le modèle par Terraform. Cette étape compare les ressources demandées aux informations d’état enregistrées par Terraform et génère ensuite l’exécution planifiée. Les ressources ne sont *pas* créées dans Azure.

```bash
terraform plan
```

Après l’exécution de la commande précédente, vous devriez voir un écran similaire au suivant :

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Si tout semble correct et que vous êtes prêt à créer l’infrastructure dans Azure, appliquez le modèle dans Terraform :

```bash
terraform apply
```

Une fois l’opération dans Terraform terminée, votre infrastructure de machine virtuelle est prête. Obtenez l’adresse IP publique de votre machine virtuelle à l’aide de la commande [az vm show](/cli/azure/vm#az_vm_show) :

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Vous pouvez ensuite établir une connexion SSH à votre machine virtuelle :

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Étapes suivantes
Vous avez créé une infrastructure de base dans Azure à l’aide de Terraform. Pour des scénarios plus complexes, y compris des exemples utilisant des équilibreurs de charge et des groupes de machines virtuelles identiques, consultez les nombreux [exemples Terraform pour Azure](https://github.com/hashicorp/terraform/tree/master/examples). Pour obtenir une liste actualisée et complète des fournisseurs Azure pris en charge, consultez la [documentation Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
