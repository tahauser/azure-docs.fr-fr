---
title: Présentation des groupes de machines virtuelles identiques Azure | Microsoft Docs
description: En savoir plus sur les groupes de machines virtuelles identiques Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64c4b309d214b91c759ff93ad659b9c69320bea7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2018
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>À quoi correspondent les groupes de machines virtuelles identiques dans Azure ?
Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques. Toutes les machines virtuelles étant configurées de la même façon, les groupes identiques sont conçus pour prendre en charge une véritable mise à l’échelle automatique (aucun pré-approvisionnement de machine virtuelle n’est nécessaire). Ainsi, il est plus facile de créer des services à grande échelle pour le Big Compute, les données volumineuses et les charges de travail en conteneurs.

Pour les applications nécessitant une mise à l’échelle des ressources de calcul internes et externes, les opérations de mise à l’échelle sont équilibrées de façon implicite sur plusieurs domaines d’erreur et de mise à jour. Pour accéder à une présentation plus poussée des groupes identiques, reportez-vous à [l’Annonce du blog Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Regardez ces vidéos pour en savoir plus sur les groupes identiques :

* [Mark Russinovich parle des groupes identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Jeux de mise à l’échelle de machine virtuelle, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Création et gestion des groupes identiques
Vous pouvez créer un groupe identique sur le [Portail Azure](https://portal.azure.com) en sélectionnant **Créer une ressource** et en tapant **identique** dans la barre de recherche. **Groupe de machines virtuelles identiques** apparaîtra dans les résultats. À partir de là, vous pourrez renseigner les champs obligatoires pour personnaliser et déployer votre groupe identique. Vous pouvez également configurer les règles de mise à l’échelle automatique de base en fonction de l’utilisation du processeur. Pour gérer votre groupe identique, vous pouvez utiliser le portail Azure, les [cmdlets Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) ou Azure CLI 2.0.

Les groupes identiques peuvent être déployés dans des [zones de disponibilité](virtual-machine-scale-sets-use-availability-zones.md).

Vous pouvez définir et déployer des groupes identiques à l’aide de modèles JSON et d’[API REST](https://msdn.microsoft.com/library/mt589023.aspx), tout comme des machines virtuelles Azure Resource Manager individuelles. Par conséquent, vous pouvez utiliser toute méthode de déploiement standard d’Azure Resource Manager. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Vous trouverez des modèles d’exemple de groupes de machines virtuelles identiques parmi les [référentiels de démarrage rapide GitHub](https://github.com/Azure/azure-quickstart-templates). (Recherchez les modèles dont le titre contient **vmss**.)

Pour les exemples de modèles de démarrage rapide, un bouton « Déployer sur Azure » du fichier readme de chaque modèle est lié à la fonctionnalité de déploiement du portail. Pour déployer le groupe identique, cliquez sur le bouton et renseignez ensuite tous les paramètres requis dans le portail. 


## <a name="autoscale"></a>Autoscale
Pour conserver la cohérence des performances d’applications, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machines virtuelles dans votre groupe identique. Cette capacité de mise à l’échelle automatique réduit le traitement de gestion pour surveiller et arranger votre groupe identique, tandis que la demande client change au fil du temps. Vous définissez les règles en fonction des indicateurs de performance, de la réponse de l’application ou d’un emploi du temps fixé, et votre groupe identique s’occupe des ajustements.

Pour des règles de mise à l’échelle automatique de base, vous pouvez utiliser les indicateurs de performance basés sur les hôtes tels que l’utilisation UC, ou le disque E/S. Ces indicateurs basés sur les hôtes sont disponibles automatiquement, sans agents ni extensions supplémentaires à installer ou configurer. Les règles de mise à l’échelle qui utilisent des indicateurs basés sur les hôtes peuvent être créées avec l’un des outils suivants :

- [Portail Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Pour utiliser davantage d’indicateurs de performance granulaires, vous pouvez installer et configurer l’extension de diagnostic Azure sur les instances de machines virtuelles de votre groupe identique. L’extension de diagnostic Azure vous permet de collecter des indicateurs de performances supplémentaires, telles que la consommation de mémoire, à l’intérieur de chaque instance de machine virtuelle. Ces indicateurs de performance sont transmis en continu vers un compte de stockage Azure, et vous créez des règles de mise à l’échelle automatique pour consommer ces données. Pour en savoir plus, consultez les articles sur l’activation de l’extension de diagnostic Azure sur une [machine virtuelle Linux](../virtual-machines/linux/diagnostic-extension.md) ou [Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).

Pour surveiller les performances d’application, vous pouvez installer et configurer un petit package d’instrumentation dans votre application pour App Insights. Les indicateurs de performance détaillés pour temps de réponse de l’application ou le nombre de sessions peuvent ensuite être retransmis vers votre application. Vous pouvez alors créer des règles de mise à l’échelle automatique avec des seuils définis pour les performances au niveau de l’application. Pour plus d’informations sur App Insights, consultez [Présentation d’Application Insights](../application-insights/app-insights-overview.md).


## <a name="manually-scaling-a-scale-set-out-and-in"></a>Mise à l’échelle manuelle d’un groupe identique (augmentation et diminution)
Vous pouvez modifier manuellement la capacité d’un groupe identique dans le portail Azure en cliquant sur la section **Mise à l’échelle** sous **Paramètres**. 

Pour modifier la capacité de groupe identique sur la ligne de commande, utilisez la commande **scale** dans [l’interface de ligne de commande Azure](https://github.com/Azure/azure-cli). Par exemple, vous pouvez l’utiliser pour définir un groupe identique sur une capacité de 10 machines virtuelles :

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Pour définir le nombre de machines virtuelles dans un groupe identique à l’aide de PowerShell, utilisez la commande **Update-AzureRmVmss** :

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Pour augmenter ou diminuer le nombre de machines virtuelles dans un groupe identique à l’aide d’un modèle Azure Resource Manager, il suffit de changer la propriété **capacité** et de redéployer le modèle. Cette simplicité facilite l’intégration de groupes identiques à la mise à l’échelle automatique Azure ou l’écriture de votre couche de mise à l’échelle personnalisée si vous devez définir des événements d’échelle personnalisée que la mise à l’échelle automatique Azure ne prend pas en charge. 

Si vous redéployez un modèle Azure Resource Manager pour modifier la capacité, vous pouvez définir un modèle beaucoup plus petit qui inclut uniquement le paquet de propriété **SKU** et la capacité mise à jour. [Voici un exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).


## <a name="monitoring-your-scale-set"></a>Surveillance de votre groupe identique
Le [portail Azure](https://portal.azure.com) répertorie les groupes identiques et affiche leurs propriétés. Le portail prend également en charge les opérations de gestion. Vous pouvez effectuer des opérations de gestion à la fois sur des groupes identiques, et sur des machines virtuelles individuelles faisant partie d’un groupe identique. Le portail fournit également un graphique d’utilisation des ressources personnalisable. 

Si vous souhaitez afficher ou modifier la définition JSON sous-jacente d’une ressource Azure, vous pouvez également utiliser l’[Explorateur de ressources Azure](https://resources.azure.com). Les groupes identiques sont pourvus par le fournisseur de ressources Azure Microsoft.Compute. À partir de ce site, vous pouvez les voir en développant les liens suivants :

**Abonnements** > **votre abonnement** > **resourceGroups** > **fournisseurs** > **Microsoft.Compute** > **virtualMachineScaleSets** > **votre groupe identique** &gt; etc.

## <a name="scale-set-scenarios"></a>Scénarios de groupe identique
Cette section répertorie quelques scénarios de groupe identique classiques. Certains services Azure de niveau supérieur (comme Batch, Service Fabric, Container Service) utilisent également ces scénarios.

* **Utiliser RDP ou SSH pour se connecter à des instances de groupe identique** : un groupe identique est créé dans un réseau virtuel et les machines virtuelles individuelles du groupe ne reçoivent pas d’adresses IP publiques par défaut. Cette stratégie évite la surcharge de gestion et de coût liée à l’attribution d’adresses IP publiques distinctes sur tous les nœuds dans la grille de calcul. Si vous avez besoin de connexions externes directes vers les machines virtuelles d’un groupe identique, vous pouvez configurer un groupe de sorte qu’il affecte automatiquement des adresses IP publiques aux nouvelles machines virtuelles. Vous pouvez également vous connecter à ces machines virtuelles à partir d’autres ressources dans votre réseau virtuel, par exemple les équilibreurs de charge et les machines virtuelles autonomes, auxquels vous pouvez allouer des adresses IP publiques. 
* **Se connecter à des machines virtuelles à l’aide de règles NAT** – Vous pouvez créer une adresse IP publique, l’affecter à un équilibreur de charge, puis définir un pool de règles NAT entrantes. Ces actions mappent les ports de l’adresse IP sur un port de machine virtuelle, dans le groupe identique. Par exemple : 
  
  | Source | Port source | Destination | Port de destination |
  | --- | --- | --- | --- |
  |  Adresse IP publique |Port 50000 |vmss\_0 |Port 22 |
  |  Adresse IP publique |Port 50001 |vmss\_1 |Port 22 |
  |  Adresse IP publique |Port 50002 |vmss\_2 |Port 22 |
  
   Dans [cet exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat), les règles NAT sont définies de manière à permettre une connexion SSH à chaque machine virtuelle d’un groupe identique, avec une seule adresse IP publique.
  
   [Cet exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) fait de même avec le protocole RDP et Windows.
* **Connectez-vous à la machine virtuelle à l’aide d’une « jumpbox »** – Si vous créez un groupe identique et une machine virtuelle autonome dans le même réseau virtuel, la machine virtuelle autonome et les machines virtuelles de groupe identique peuvent se connecter entre elles en utilisant leurs adresses IP internes comme défini par le réseau virtuel/sous-réseau. Si vous créez une adresse IP publique et l’affectez à la machine virtuelle autonome, vous pouvez lancer un RDP ou un SSH pour vous connecter à la machine virtuelle autonome. Vous pouvez ensuite vous connecter depuis cette machine à vos instances de VMSS. À ce stade, vous pouvez remarquer qu’un simple groupe identique est intrinsèquement plus sûr qu’une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.
  
   Par exemple, [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) déploie une échelle simple avec une machine virtuelle autonome. 
* **Équilibrage de charge pour les instances de groupe identique** – Si vous souhaitez remettre un travail à un cluster de calcul de machines virtuelles selon une approche de type « tourniquet », vous pouvez configurer un équilibreur de charge Azure avec des règles d’équilibrage de charge de couche 4. Vous pouvez définir des sondes pour vérifier que votre application s’exécute en envoyant des requêtes ping aux ports, en spécifiant un protocole, un intervalle et un chemin d’accès à la demande. [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) prend également en charge les groupes identiques de machines virtuelles, ainsi que des scénarios d’équilibrage de charge de couche 7 et plus.
  
   [Cet exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) montre comment créer un groupe identique exécutant des serveurs web Apache et utilise un équilibrage de charge pour équilibrer la charge de chaque machine virtuelle. (Examinez le type de ressource Microsoft.Network/loadBalancers et networkProfile et extensionProfile dans virtualMachineScaleSet.)

   [Cet exemple Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) et [cet exemple Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) utilisent Application Gateway.  

* **Déploiement d’un groupe identique en tant que cluster de calcul dans un gestionnaire de cluster PaaS** – Les groupes identiques sont parfois décrits en tant que rôle de travail de la prochaine génération. Bien qu’il s’agisse d’une description valide, il est possible de confondre les fonctionnalités du groupe identique avec les fonctionnalités d’Azure Cloud Services. Dans un sens, les groupes identiques correspondent à un rôle de travail ou à une ressource Worker. Ils sont une ressource de calcul généralisée indépendante de la plateforme/du service d’exécution, personnalisable et pouvant être intégrée à l’IaaS Azure Resource Manager.
  
   Un rôle de travail des Services cloud est limité en termes de prise en charge de la plate-forme/du service d’exécution (images de plateforme Windows uniquement). Cependant, il inclut également des services tels que l’échange d’adresses IP virtuelles, la configuration des paramètres de mise à niveau, et les paramètres spécifiques au déploiement du service d’exécution/de l’application. Ces services ne sont pas *encore* disponibles dans les groupes identiques. Ils peuvent être transmis par d’autres services PaaS de niveau supérieur, comme Azure Service Fabric. Vous pouvez envisager les groupes identiques comme une infrastructure prenant en charge PaaS. Les solutions PaaS, telles que [Service Fabric](https://azure.microsoft.com/services/service-fabric/), s’appuient sur cette infrastructure.
  
   Dans [cet exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) de cette approche, [Azure Container Service](https://azure.microsoft.com/services/container-service/) déploie un cluster basé sur les groupes identiques avec un orchestrateur de conteneur.

## <a name="scale-set-performance-and-scale-guidance"></a>Performance de groupe identique et guide de mise à l’échelle
* Un groupe identique prend en charge jusqu'à 1 000 machines virtuelles. Si vous créez et chargez vos propres images de machine virtuelle personnalisées, la limite est de 300. Pour connaître les aspects à prendre en compte en matière d’utilisation de grands groupes identiques, voir [Utilisation de grands groupes de machines virtuelles identiques](virtual-machine-scale-sets-placement-groups.md).
* Vous n’êtes pas obligé de créer au préalable des comptes Azure Storage pour utiliser les groupes identiques. Les groupes identiques prennent en charge Azure Managed Disks, qui élimine les problèmes de performances liés au nombre de disques par compte de stockage. Pour plus d’informations, voir [Groupes de machines virtuelles identiques et disques gérés Azure](virtual-machine-scale-sets-managed-disks.md).
* Envisagez d’utiliser Azure Premium Storage plutôt que le stockage Azure afin d’optimiser les temps d’approvisionnement des machines virtuelles et les performances d’E/S.
* Le quota de processeurs virtuels dans la région dans laquelle vous effectuez le déploiement limite le nombre de machines virtuelles que vous pouvez créer. Vous devrez peut-être contacter le support technique pour augmenter votre limite de quota de calcul, même si votre limite actuelle de processeurs virtuels utilisables avec Azure Cloud Services est élevée. Pour interroger votre quota, exécutez cette commande de l’interface de ligne de commande Azure : `az vm list-usage`. Ou bien, exécutez cette commande PowerShell : `Get-AzureRmVMUsage`.

## <a name="frequently-asked-questions-for-scale-sets"></a>Forum Aux Questions (FAQ) pour les groupes identiques
**Q.** Combien de machines virtuelles peut-il y avoir dans un groupe identique ?

**A.** Un groupe identique peut contenir de 0 à 1 000 machines virtuelles basées sur des images de plateforme, ou de 0 à 300 machines virtuelles basées sur des images personnalisées. 

**Q.** Les disques de données sont-ils pris en charge dans les groupes identiques ?

**A.** Oui. Un groupe identique peut définir une configuration de disques de données attachés qui s’appliquera à toutes les machines virtuelles du groupe. Pour plus d’informations, consultez [Groupes identiques Azure et disques de données attachés](virtual-machine-scale-sets-attached-disks.md). Il existe d’autres options pour stocker les données :

* Fichiers Azure (lecteurs SMB partagés)
* Système d’exploitation de lecteur
* Lecteur temp (local, non sauvegardé par le stockage Azure)
* Service de données Azure (par exemple, tables Azure, objets blob Azure)
* Service de données externe (par exemple, base de données distante)

**Q.** Quelles sont les régions Azure qui prennent en charge les groupes identiques ?

**A.** Toutes les régions prennent en charge les groupes identiques.

**Q.** Comment créer un groupe identique à l’aide d’une image personnalisée ?

**A.** Créez un disque géré en fonction de votre image personnalisée de disque dur virtuel et référencez-le dans votre modèle de groupe identique. [Voici un exemple](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**Q.** Si je réduis ma capacité de groupe identique de 20 à 15, quelles sont les machines virtuelles qui seront supprimées ?

**A.** Les ordinateurs virtuels sont supprimés du jeu de mise à l’échelle uniformément entre les domaines de mise à jour et les domaines d’erreur pour optimiser la disponibilité. Les machines virtuelles avec les ID les plus élevés sont supprimées en premier.

**Q.** Que se passe-t-il si j’augmente ensuite la capacité de 15 à 18 ?

**A.** Si vous augmentez la capacité à 18, 3 machines virtuelles sont créées. À chaque fois, l’ID d’instance de la machine virtuelle est incrémenté avec la valeur la plus élevée précédente (par exemple, 20, 21, 22). Les machines virtuelles sont réparties sur les domaines d’erreur et les domaines de mise à jour.

**Q.** Lorsque j’utilise plusieurs extensions dans un groupe identique, puis-je appliquer une séquence d’exécution ?

**A.** Pas directement, mais pour l’extension customScript, votre script peut attendre que l’exécution d’une autre extension soit terminée. Vous trouverez des conseils supplémentaires sur le séquencement d’extensions dans ce billet de blog : [Extension Sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Séquencement d’extensions dans les groupes identiques de machines virtuelles Azure).

**Q.** Les groupes identiques fonctionnent-ils avec des ensembles haute disponibilité Azure ?

**A.** Oui. Un groupe identique est un ensemble de disponibilité implicite comprenant cinq domaines d’erreur et cinq domaines de mise à jour. Les groupes identiques de plus de 100 machines virtuelles couvrent plusieurs *groupes de placement* qui équivalent à plusieurs groupes à haute disponibilité. Pour plus d’informations sur les groupes de placement, voir [Working with large virtual machine scale sets](virtual-machine-scale-sets-placement-groups.md) (Utilisation de grands groupes de machines virtuelles identiques). Un groupe de machines virtuelles à haute disponibilité peut figurer dans le même réseau virtuel qu’un groupe identique de machines virtuelles. Une configuration courante consiste à placer les machines virtuelles du nœud de contrôle qui nécessitent souvent une configuration unique dans un groupe à haute disponibilité, et les nœuds de données dans le groupe identique.

Vous trouverez plus de réponses aux questions relatives aux groupes identiques, dans les [FAQ sur les groupes de machines virtuelles identiques Azure](virtual-machine-scale-sets-faq.md).
