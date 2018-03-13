---
title: "Utiliser Ansible pour gérer vos inventaires dynamiques Azure"
description: "Découvrez comment utiliser Ansible pour gérer vos inventaires dynamiques Azure"
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, inventaire dynamique
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 8753d039582abdf22f105bf7f139a35c224e7c59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Utiliser Ansible pour gérer vos inventaires dynamiques Azure
Ansible peut être utilisé pour extraire des informations d’inventaire de diverses sources (y compris les sources cloud comme Azure) dans un *inventaire dynamique*. Dans cet article, vous utilisez [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) pour configurer un inventaire dynamique Azure Ansible dans lequel vous créez deux machines virtuelles, étiquetez l’une d’entre elles et installez Nginx sur la machine virtuelle étiquetée.

## <a name="prerequisites"></a>configuration requise

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Informations d’identification Azure** - [Créer des informations d’identification Azure et configurer Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Créer les machines virtuelles de test

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

1. Créez un groupe de ressources Azure pour héberger les machines virtuelles utilisées dans ce didacticiel.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Créez deux machines virtuelles de Linux sur Azure en utilisant l’une des méthodes suivantes :

    - **Playbook Ansible** : l’article [Créer une machine virtuelle Linux de base dans Azure avec Ansible](/azure/virtual-machines/linux/ansible-create-vm) montre comment créer une machine virtuelle à partir d’un playbook Ansible. Si vous utilisez un playbook pour définir une machine virtuelle ou les deux, assurez-vous que la connexion SSH est utilisée plutôt qu’un mot de passe.

    - **Azure CLI** : émettez toutes les commandes suivantes dans Cloud Shell pour créer les deux machines virtuelles :

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Étiqueter une machine virtuelle
Vous pouvez [utiliser des étiquettes pour organiser vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) par catégories définies par l’utilisateur. 

Entrez la commande suivante [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) pour étiqueter la machine virtuelle `ansible-inventory-test-vm1` avec la clé `nginx` :

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/&lt;YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Générer un inventaire dynamique
Une fois que vos machines virtuelles sont définies (et étiquetées), il est temps de générer l’inventaire dynamique. Ansible fournit un script Python nommé [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) qui génère un inventaire dynamique de vos ressources Azure en envoyant des requêtes d’API à Azure Resource Manager. Les étapes suivantes vous expliquent comment utiliser le script `azure_rm.py` pour vous connecter à vos deux machines virtuelles de test Azure :

1. Utilisez la commande `wget` GNU pour récupérer le script `azure_rm.py` :

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Utilisez la commande `chmod` pour modifier les autorisations d’accès au script `azure_rm.py`. La commande suivante utilise le paramètre `+x` pour permettre l’exécution du fichier spécifié (`azure_rm.py`) :

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Utilisez la [commande ansible](https://docs.ansible.com/ansible/2.4/ansible.html) pour vous connecter à votre groupe de ressources : 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Une fois connecté, vous constatez des résultats similaires à ceux de la sortie suivante :

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Activer l’étiquette de la machine virtuelle
Une fois que vous avez défini l’étiquette de votre choix, vous devez « l’activer ». Pour activer une étiquette, vous pouvez l’exporter dans une variable d’environnement appelée `AZURE_TAGS` via la commande **export** :

```azurecli-interactive
export AZURE_TAGS=nginx
```

Une fois que l’étiquette a été exportée, vous pouvez de nouveau essayer d’exécuter la commande `ansible` :

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Vous voyez maintenant une seule machine virtuelle (celle dont l’étiquette correspond à la valeur exportée dans la variable d’environnement **AZURE_TAGS**) :

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurer Nginx sur la machine virtuelle étiquetée
L’objectif des étiquettes est de travailler rapidement et facilement avec des sous-groupes de vos machines virtuelles. Par exemple, supposons que vous souhaitez installer Nginx uniquement sur les machines virtuelles auxquelles vous avez assigné une étiquette `nginx`. Les étapes suivantes illustrent la facilité de réalisation de cette tâche :

1. Créez un fichier (pour héberger votre playbook) nommé `nginx.yml` comme suit :

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Insérez le code suivant dans le fichier `nginx.yml` nouvellement créé :

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Exécutez le playbook `nginx.yml` :

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. Une fois que vous avez exécuté le playbook, vous constatez des résultats similaires à ceux de la sortie suivante :

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Tester l’installation de Nginx
Cette section présente une technique de test de l’installation de Nginx sur votre machine virtuelle.

1. Utilisez la commande [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) pour récupérer l’adresse IP de la machine virtuelle `ansible-inventory-test-vm1`. La valeur renvoyée (l’adresse IP de la machine virtuelle) est ensuite utilisée comme paramètre de la commande SSH pour vous connecter à la machine virtuelle.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. La commande [nginx-v](https://nginx.org/en/docs/switches.html) est généralement utilisée pour imprimer la version de Nginx. Toutefois, elle peut également être utilisée pour déterminer si Nginx est installé. Entrez-la lorsque vous êtes connecté à la machine virtuelle `ansible-inventory-test-vm1`.

    ```azurecli-interactive
    nginx -v
    ```

1. Une fois que vous exécutez la commande `nginx -v`, vous voyez la version de Nginx (deuxième ligne) qui indique que Nginx est installé.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Appuyez sur la combinaison de touches **&lt;Ctrl >D** pour déconnecter la session SSH.

1. L’exécution des étapes précédentes pour la machine virtuelle `ansible-inventory-test-vm2` génère un message d’information indiquant l’emplacement où vous pouvez trouver Nginx (ce qui signifie que vous ne l’avez pas encore installé) :

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Créer une machine virtuelle Linux de base dans Azure avec Ansible](/azure/virtual-machines/linux/ansible-create-vm)