---
title: "Exécuter Ansible avec Bash dans Azure Cloud Shell"
description: "Découvrir comment effectuer diverses tâches Ansible avec Bash dans Azure Cloud Shell"
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 92ca2950199d638c5f76c0c7aadbae4fda7e9d1e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Exécuter Ansible avec Bash dans Azure Cloud Shell

Dans ce didacticiel, vous allez apprendre à effectuer diverses tâches Ansible à partir de Bash dans Cloud Shell. Ces tâches comprennent la connexion à une machine virtuelle et la création de playbooks pour générer et supprimer un groupe de ressources Azure.

## <a name="prerequisites"></a>Prérequis


- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Informations d’identification Azure** - [Créer des informations d’identification Azure et configurer Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Configurer Azure Cloud Shell** : si vous ne connaissez pas encore Azure Cloud Shell, l’article [Démarrage rapide de Bash dans Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart) illustre comment démarrer et configurer Cloud Shell. Lancez un site web dédié pour Cloud Shell ici :

[![Lancer Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Lancer Cloud Shell")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Utiliser Ansible pour se connecter à une machine virtuelle
Ansible a créé un script Python nommé [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) qui génère un inventaire dynamique de vos ressources Azure en envoyant des requêtes d’API à Azure Resource Manager. Les étapes suivantes vous expliquent comment utiliser le script `azure_rm.py` pour vous connecter à une machine virtuelle Azure :

1. Ouvrez Bash dans Cloud Shell. Le type d’interpréteur de commandes est indiqué à gauche de la fenêtre Cloud Shell.

1. Si vous n’avez pas de machine virtuelle à utiliser, entrez les commandes suivantes dans Azure Cloud Shell pour créer une machine virtuelle à utiliser pour effectuer les tests :

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Utilisez la commande `wget` GNU pour récupérer le script `azure_rm.py` :

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Utilisez la commande `chmod` pour modifier les autorisations d’accès au script `azure_rm.py`. La commande suivante utilise le paramètre `+x` pour permettre l’exécution du fichier spécifié (`azure_rm.py`) :

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Utilisez la [commande ansible](https://docs.ansible.com/ansible/2.4/ansible.html) pour vous connecter à votre machine virtuelle : 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Une fois connecté, vous devez voir des résultats similaires à ceux de la sortie :

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Si vous avez créé un groupe de ressources et une machine virtuelle dans cette section

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Exécuter un playbook dans Cloud Shell
La commande [ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) exécute des playbooks Ansible, exécutant les tâches sur les hôtes ciblés. Cette section vous explique comment utiliser Cloud Shell pour créer et exécuter deux playbooks : un pour créer un groupe de ressources et un second pour supprimer le groupe de ressources. 

1. Créez un fichier nommé `rg.yml` comme suit :

  ```azurecli-interactive
  vi rg.yml
  ```

1. Copiez le contenu suivant dans la fenêtre Cloud Shell (qui héberge maintenant une instance de l’éditeur VI) :

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. Enregistrez le fichier et quittez l’éditeur VI en entrant `:wq` et en appuyant sur &lt;Entrée>.

1. Utilisez la commande `ansible-playbook` pour exécuter le playbook `rg.yml` :

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Vous obtenez normalement des résultats similaires à la sortie suivante :

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Vérifiez le déploiement :

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Maintenant que vous avez créé le groupe de ressources, créez un second playbook pour supprimer le groupe de ressources :

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Copiez le contenu suivant dans la fenêtre Cloud Shell (qui héberge maintenant une instance de l’éditeur VI) :

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. Enregistrez le fichier et quittez l’éditeur VI en entrant `:wq` et en appuyant sur &lt;Entrée>.

1. Utilisez la commande `ansible-playbook` pour exécuter le playbook `rg2.yml` :

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Vous obtenez normalement des résultats similaires à la sortie suivante :

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Créer une machine virtuelle Linux de base dans Azure avec Ansible](/azure/virtual-machines/linux/ansible-create-vm)
