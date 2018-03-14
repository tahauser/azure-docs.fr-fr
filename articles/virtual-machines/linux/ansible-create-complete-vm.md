---
title: "Utiliser Ansible pour créer une machine virtuelle Linux complète dans Azure | Microsoft Docs"
description: "Découvrez comment utiliser Ansible pour créer et gérer un environnement de machine virtuelle Linux complète dans Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: d5e3f3db4726bfb16cbb389cf99b9bf7c511da97
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Création d’un environnement de machine virtuelle Linux complète dans Azure avec Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer vos machines virtuelles dans Azure comme vous le feriez pour toute autre ressource. Cet article vous explique comment créer un environnement Linux complet et des ressources de support avec Ansible. Vous pouvez aussi découvrir comment [créer une machine virtuelle de base avec Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Prérequis
Pour gérer des ressources Azure avec Ansible, vous avez besoin des éléments suivants :

- Ansible et les modules du SDK Azure Python installés sur votre système hôte.
    - Installation d’Ansible sur [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) et [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Les informations d’identification Azure, et Ansible configuré pour les utiliser.
    - [Créer des informations d’identification Azure et configurer Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. 
    - Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). Vous pouvez également utiliser [Cloud Shell](/azure/cloud-shell/quickstart) à partir de votre navigateur.


## <a name="create-virtual-network"></a>Création d’un réseau virtuel
La section suivante d’un playbook Ansible crée un réseau virtuel nommé *myVnet* dans l’espace d’adressage *10.0.0.0/16* :

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Pour ajouter un sous-réseau, la section suivante crée un sous-réseau nommé *mySubnet* dans le réseau virtuel *myVnet* :

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Création d’une adresse IP publique
Pour pouvoir accéder à des ressources via Internet, créez et attribuez une adresse IP publique à votre machine virtuelle. La section suivante d’un playbook Ansible crée une adresse IP publique nommée *myPublicIP* :

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Créer un groupe de sécurité réseau
Les groupes de sécurité réseau contrôlent le flux du trafic réseau en direction et en provenance de votre machine virtuelle. La section suivante d’un playbook Ansible crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* et définit une règle pour autoriser le trafic SSH sur le port TCP 22 :

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Créer une carte réseau virtuelle
Une carte réseau virtuelle connecte votre machine virtuelle à un réseau virtuel donné, à une adresse IP publique et à un groupe de sécurité réseau. La section suivante d’un playbook Ansible crée une carte réseau virtuelle nommée *myNIC* connectée aux ressources du réseau virtuel que vous avez créé :

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Créer une machine virtuelle
La dernière étape consiste à créer une machine virtuelle et à utiliser toutes les ressources créées. La section suivante d’un playbook Ansible crée une machine virtuelle nommée *myVM* et attache la carte réseau virtuelle nommée *myNIC*. Entrez vos données de clé publique complètes dans la paire *key_data* comme suit :

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Terminer le playbook Ansible
Pour rassembler toutes ces sections, créez un playbook Ansible nommé *azure_create_complete_vm.yml* et collez le contenu suivant. Entrez vos données de clé publique complètes dans la paire *key_data* :

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible a besoin d’un groupe de ressources pour y déployer toutes vos ressources. Créez un groupe de ressources avec la commande [az group create](/cli/azure/vm#az_vm_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Pour créer l’environnement de machine virtuelle complète avec Ansible, exécutez le playbook comme suit :

```bash
ansible-playbook azure_create_complete_vm.yml
```

La sortie est similaire à l’exemple suivant, qui montre que la machine virtuelle a été créée :

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>étapes suivantes
Cet exemple crée un environnement de machine virtuelle complète, avec les ressources de réseau virtuel nécessaires. Pour obtenir un exemple plus direct de création d’une machine virtuelle dans des ressources réseau existantes avec des options par défaut, consultez [Créer une machine virtuelle](ansible-create-vm.md).
