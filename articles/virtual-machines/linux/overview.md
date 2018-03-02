---
title: "Vue d’ensemble des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Décrit les services de calcul, de stockage et de mise en réseau Azure à l’aide de machines virtuelles Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: cdb2bda0c3f7e73b115c2609c3f229c633093bdc
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-and-linux"></a>Azure et Linux
Microsoft Azure propose une gamme croissante de services cloud publics intégrés, comprenant des analyses, des machines virtuelles, des bases de données, des services mobiles, la mise en réseau, le stockage et le web.&mdash;En d’autres termes, il s’agit de la méthode idéale pour héberger vos solutions.  Microsoft Azure fournit une plateforme de calcul scalable qui vous permet de payer uniquement ce que vous utilisez, quand vous le souhaitez, sans avoir à investir dans du matériel en local.  Azure permet de faire face à toutes les exigences en matière de montée en puissance de vos solutions ou d’augmentation de la taille des instances.

Si vous êtes familiarisé avec les différentes fonctionnalités d’Amazon AWS, vous pouvez examiner le [document de mappage de définition](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)Azure vs AWS.

## <a name="regions"></a>Régions
Les ressources Microsoft Azure sont réparties sur plusieurs régions géographiques dans le monde.  Une « région » représente plusieurs centres de données au sein d’une seule zone géographique. Azure possède actuellement (novembre 2017) 36 régions généralement disponibles dans le monde avec 6 autres régions prévues. Une liste mise à jour des régions existantes et récemment annoncées est disponible à la page suivante :

* [Régions Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilité
Azure a annoncé un contrat de niveau de service de pointe pour machine virtuelle à instance unique de 99,9 % à condition de déployer la machine virtuelle avec le stockage premium pour tous les disques.  Afin que votre déploiement puisse bénéficier du contrat de niveau de service standard de 99,95 % pour les machines virtuelles, vous devez déployer au moins deux machines virtuelles exécutant votre charge de travail à l’intérieur d’un groupe à haute disponibilité. Un groupe à haute disponibilité assure que vos machines virtuelles sont réparties sur plusieurs domaines d’erreur dans les centres de données Azure et déployées sur des hôtes ayant des fenêtres de maintenance distinctes. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explique la disponibilité garantie d’Azure dans son ensemble.

## <a name="managed-disks"></a>Managed Disks

Managed Disks se charge de la création et de la gestion du compte de stockage Azure en arrière-plan, éliminant les préoccupations liées aux limites d’extensibilité du compte de stockage. Vous spécifiez la taille du disque et le niveau de performances (Standard ou Premium) et Azure crée et gère le disque. Lorsque vous ajoutez des disques ou faites monter ou descendre en puissance la machine virtuelle, vous n’avez pas à vous soucier du stockage utilisé. Si vous créez de nouvelles machines virtuelles, [utilisez Azure CLI 2.0](quick-create-cli.md) ou le portail Azure pour créer des machines virtuelles avec des disques de système d’exploitation et de données gérés. Si vous avez des machines virtuelles qui utilisent des disques non gérés, vous pouvez [convertir vos machines virtuelles pour qu’elles soient sauvegardées avec Managed Disks](convert-unmanaged-to-managed-disks.md).

Vous pouvez également gérer vos images personnalisées dans un compte de stockage par région Azure et les utiliser pour créer des centaines de machines virtuelles dans le même abonnement. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble des disques gérés](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Machines virtuelles et instances Azure
Microsoft Azure prend en charge un certain nombre de distributions Linux populaires fournies et gérées par plusieurs partenaires.  Vous pouvez trouver des distributions comme Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD et plus encore dans la Place de marché Microsoft Azure. Microsoft travaille activement avec différentes communautés Linux pour enrichir davantage la liste des [distributions Linux approuvées par Azure](endorsed-distros.md).

Si votre distribution Linux préférée n’est pas présente dans la galerie, vous pouvez « apporter votre propre machine virtuelle Linux » en [créant et chargeant un disque dur virtuel dans Azure](create-upload-generic.md).

Les machines virtuelles Azure vous permettent de déployer un large éventail de solutions informatiques et ce, en toute flexibilité. Vous pouvez déployer pratiquement toute charge de travail et tout langage sur presque n’importe quel système d’exploitation : Windows, Linux ou un système personnalisé créé à partir de l’un des nombreux partenaires. Vous ne trouvez toujours pas ce que vous cherchez ?  Ne vous inquiétez pas, vous pouvez également ajouter vos propres images en local.

## <a name="vm-sizes"></a>Tailles de machine virtuelle
La [taille](sizes.md) de la machine virtuelle que vous utilisez est déterminée par la charge de travail que vous souhaitez exécuter. La taille que vous choisissez détermine ensuite des facteurs comme la puissance de traitement, la mémoire et la capacité de stockage. Azure propose différentes tailles vous permettant de prendre en charge de nombreux types d'utilisation.

Azure facture un [prix horaire](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en fonction de la taille et du système d’exploitation de la machine virtuelle. Pour les heures partielles, Azure facture uniquement les minutes utilisées. Le stockage est facturé séparément.

## <a name="automation"></a>Automatisation
Pour obtenir une culture DevOps appropriée, toute l’infrastructure doit être du code.  Lorsque l’ensemble de l’infrastructure se trouve dans le code, elle est facile à recréer (serveurs Phoenix).  Azure fonctionne avec tous les principaux outils d’automatisation, comme Ansible, Chef, SaltStack et Puppet.  Azure propose également ses propres outils pour l’automatisation :

* [Modèles Azure](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure déploie la prise en charge de [cloud-init](http://cloud-init.io/) sur la plupart des distributions Linux qui le prennent en charge.  Actuellement, les machines virtuelles Ubuntu de Canonical sont déployées avec cloud-init activé par défaut.  Fedora, CentOS et Red Hats RHEL prennent en charge cloud-init, mais les images Azure maintenues par RedHat ne disposent actuellement pas de cloud-init installé.  Pour utiliser cloud-init sur un système d’exploitation de la famille RedHat, vous devez créer une image personnalisée avec cloud-init installé.

* [À l’aide de cloud-init sur les machines virtuelles Linux Azure](using-cloud-init.md)

## <a name="quotas"></a>Quotas
Chaque abonnement Azure comporte des limites de quota qui peuvent avoir un impact négatif sur le déploiement d’un grand nombre de machines virtuelles pour votre projet. La limite est de 20 machines virtuelles par région et par abonnement.  Les limites de quota peuvent être augmentées rapidement et facilement en soumettant un ticket de support demandant leur hausse.  Pour plus d’informations sur les limites de quota :

* [Limites du service d’abonnement Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partenaires
Microsoft travaille en étroite collaboration avec des partenaires afin de garantir que les images disponibles sont mises à jour et optimisées pour un runtime Azure.  Pour plus d’informations sur les partenaires Azure, consultez les liens suivants :

* Linux sur Azure : [Distributions approuvées](endorsed-distros.md)
* SUSE : [Place de marché Azure - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Place de marché Azure - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Place de marché Azure - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Place de marché Azure - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Place de marché Azure - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Place de marché Azure - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Place de marché Azure - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library pour Azure](https://azure.bitnami.com/)
* Mesosphere - [Place de marché Azure - Mesosphere DC/OS sur Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Place de marché Azure - Azure Container Service avec Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Place de marché Azure - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Bien démarrer avec Linux sur Azure
Pour commencer à utiliser Azure, vous avez besoin d’un compte Azure, de l’interface de ligne de commande Azure installée et d’une paire de clés SSH (publique et privée).

### <a name="sign-up-for-an-account"></a>Inscrivez-vous pour obtenir un compte
La première étape pour utiliser Azure Cloud consiste à créer un compte Azure.  Accédez sur la page [Création d’un compte Azure](https://azure.microsoft.com/pricing/free-trial/) pour commencer.

### <a name="install-the-cli"></a>Installer l’interface de ligne de commande
Avec votre nouveau compte Azure, vous pouvez commencer immédiatement à utiliser le portail Azure, qui est un panneau d’administration web.  Pour gérer Azure Cloud via la ligne de commande, installez l’ `azure-cli`.  Installez [Azure CLI 2.0](/cli/azure/install-azure-cli) sur votre station de travail Mac ou Linux.

### <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH
Vous avez maintenant un compte Azure, le portail web Azure et l’interface de ligne de commande Azure.  L’étape suivante consiste à créer une paire de clés SSH utilisée pour exécuter SSH dans Linux sans utiliser de mot de passe.  [Créez des clés SSH sur Linux et Mac](mac-create-ssh-keys.md) pour activer les connexions sans mot de passe et améliorer la sécurité.

### <a name="create-a-vm-using-the-cli"></a>Créer une machine virtuelle à l’aide de l’interface de ligne de commande
La création d’une machine virtuelle Linux à l’aide de l’interface de ligne de commande est un moyen rapide de déployer une machine virtuelle sans quitter le terminal que vous utilisez.  Tout ce que vous pouvez spécifier sur le portail web est disponible via un commutateur ou un indicateur de ligne de commande.  

* [Créer une machine virtuelle Linux à l’aide de l’interface de ligne de commande](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Créer une machine virtuelle dans le portail
La création d’une machine virtuelle sur le portail web Azure consiste à facilement définir les différentes options à l’aide de la souris pour accéder à un déploiement.  Au lieu d’utiliser des indicateurs de ligne de commande ou des commutateurs, vous pouvez afficher une belle disposition d’options et de paramètres.  Tous les éléments disponibles au moyen de l’interface de ligne de commande sont également disponibles sur le portail.

* [Créer une machine virtuelle Linux à l’aide du portail](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Connexion avec SSH sans mot de passe
La machine virtuelle s’exécute maintenant sur Azure et vous êtes prêt à vous connecter.  L’utilisation de mots de passe pour vous connecter via le protocole SSH est peu sûre et prend du temps.  L’utilisation de clés SSH est la méthode la plus sûre et la plus rapide pour vous connecter.  Lorsque vous créez vos machines virtuelles Linux via le portail ou l’interface de ligne de commande, vous avez deux possibilités pour l’authentification.  Si vous choisissez un mot de passe pour SSH, Azure configure la machine virtuelle pour autoriser les connexions via les mots de passe.  Si vous choisissez d’utiliser une clé publique SSH, Azure configure la machine virtuelle pour autoriser uniquement les connexions via les clés SSH et désactive les connexions de mot de passe. Pour sécuriser votre machine virtuelle Linux en autorisant uniquement les connexions par clé SSH, utilisez l’option de clé SSH publique lors de la création de machines virtuelles sur le portail ou dans l’interface de ligne de commande.

## <a name="related-azure-components"></a>Composants Azure connexes
## <a name="storage"></a>Stockage
* [Introduction à Stockage Microsoft Azure](../../storage/common/storage-introduction.md)
* [Ajouter un disque à une machine virtuelle Linux avec l’interface de ligne de commande Azure](add-disk.md)
* [Attacher un disque de données à une machine virtuelle Linux dans le Portail Azure](attach-disk-portal.md)

## <a name="networking"></a>Mise en réseau
* [Présentation du réseau virtuel.](../../virtual-network/virtual-networks-overview.md)
* [Adresses IP dans Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Ouverture de ports sur une machine virtuelle Linux dans Azure](nsg-quickstart.md)
* [Créer un nom de domaine complet dans le Portail Azure](portal-create-fqdn.md)

## <a name="containers"></a>Conteneurs
* [Machines virtuelles et conteneurs dans Azure](containers.md)
* [Présentation d’Azure Container Service](../../container-service/container-service-intro.md)
* [Déploiement d’un cluster Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant une vue d’ensemble de Linux sur Azure.  L’étape suivante consiste à aller plus loin et créer quelques machines virtuelles.

* [Explorez la liste croissante d’exemples de scripts pour les tâches courantes via AzureCLI](cli-samples.md)
