---
title: Utiliser Ansible avec Azure
description: "Présentation de l’utilisation d’Ansible pour automatiser l’approvisionnement du cloud, la gestion de la configuration et le déploiement des applications."
ms.service: ansible
keywords: "ansible, azure, devops, présentation, approvisionnement du cloud, gestion de la configuration, déploiement des applications, modules ansible, playbooks ansible"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="ansible-with-azure"></a>Ansible avec Azure

[Ansible](http://www.ansible.com) est un produit open source qui automatise l’approvisionnement du cloud, la gestion de la configuration et le déploiement des applications. Il vous permet d’approvisionner les machines virtuelles, les conteneurs et le réseau, ainsi que des infrastructures cloud complètes. En outre, Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement.

Cet article présente certains des avantages de l’utilisation d’Ansible avec Azure.

## <a name="ansible-playbooks"></a>Playbooks Ansible

Les [playbooks Ansible](http://docs.ansible.com/ansible/latest/playbooks.html) sont le langage d’orchestration, du déploiement et de la configuration d’Ansible. Ils peuvent décrire une stratégie que vous souhaitez que vos systèmes distants appliquent ou un ensemble d’étapes dans un processus informatique général. Lorsque vous créez un playbook, c’est à l’aide de YAML que vous le faites. YAML définit un modèle d’une configuration ou d’un processus.

## <a name="ansible-modules"></a>Modules Ansible

Ansible comprend une suite de [modules Ansible](http://docs.ansible.com/ansible/latest/modules_by_category.html) qui peuvent être exécutés directement sur les hôtes distants ou par le biais de [playbooks](http://docs.ansible.com/ansible/latest/playbooks.html). Les utilisateurs peuvent également créer leurs propres modules. Les modules peuvent être utilisés pour contrôler les ressources système, telles que les services, les packages ou les fichiers, ou exécuter des commandes système.

Pour interagir avec les services Azure, Ansible comprend une suite de [modules cloud Ansible](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) qui fournit les outils permettant de créer et d’organiser facilement votre infrastructure sur Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrer la charge de travail existante vers Azure

Une fois que vous avez utilisé Ansible pour définir votre infrastructure, vous pouvez appliquer le playbook de votre application autorisant Azure à mettre automatiquement à l’échelle votre environnement en fonction des besoins. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatiser une application cloud native dans Azure

Ansible vous permet d’automatiser des applications cloud natives dans Azure à l’aide des microservices Azure comme [Azure Functions](https://azure.microsoft.com//services/functions/) et [Kubernetes sur Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gérer des déploiements avec l’inventaire dynamique
Via sa fonctionnalité [d’inventaire dynamique](http://docs.ansible.com/ansible/intro_dynamic_inventory.html), Ansible vous permet d’extraire l’inventaire des ressources Azure. Vous pouvez ensuite étiqueter vos déploiements Azure existants et les gérer via Ansible.

## <a name="additional-azure-marketplace-options"></a>Autres options de Place de marché Microsoft Azure
L’image Place de marché Microsoft Azure [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) par Red Hat aide les organisations à mettre à l’échelle l’automatisation des tâches informatiques et à gérer les déploiements complexes dans les infrastructures physiques, virtuelles et cloud. Ansible Tower comprend des fonctionnalités qui fournissent d’autres niveaux de visibilité, de contrôle, de sécurité et d’efficacité nécessaires aux entreprises d’aujourd’hui. Ansible Tower chiffre les informations d’identification telles que les clés SSH et Azure, afin que vous puissiez déléguer des travaux à des employés moins expérimentés sans prendre le risque d’exposer vos informations d’identification.

## <a name="next-steps"></a>Étapes suivantes
- [Installer et configurer Ansible pour gérer des machines virtuelles dans Azure](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Créer une machine virtuelle Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)