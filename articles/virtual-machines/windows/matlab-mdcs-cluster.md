---
title: Clusters MATLAB sur des machines virtuelles | Microsoft Docs
description: "Utilisez des machines virtuelles Microsoft Azure pour créer des clusters MATLAB Distributed Computing Server afin d’exécuter vos charges de travail MATLAB parallèles nécessitant beaucoup de ressources système."
services: virtual-machines-windows
documentationcenter: 
author: mscurrell
manager: timlt
editor: 
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 177f8a61487130e718e3e6cfb779b17a3ed8ed69
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Créer des clusters MATLAB Distributed Computing Server sur des machines virtuelles Azure
Utilisez des machines virtuelles Microsoft Azure pour créer un ou plusieurs clusters MATLAB Distributed Computing Server afin d’exécuter vos charges de travail MATLAB parallèles nécessitant beaucoup de ressources système. Installez votre logiciel MATLAB Distributed Computing Server sur une machine virtuelle à utiliser en tant qu’image de base, et utilisez un modèle de démarrage rapide Azure ou un script Azure PowerShell (disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) pour déployer et gérer le cluster. Après le déploiement, connectez-vous au cluster pour exécuter vos charges de travail.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Présentation de MATLAB et de MATLAB Distributed Computing Server
La plateforme [MATLAB](http://www.mathworks.com/products/matlab/) a été optimisée pour la résolution des problèmes techniques et scientifiques. Les utilisateurs MATLAB ayant recours à des simulations à grande échelle et à des tâches de traitement de données peuvent utiliser les produits de calcul parallèle MathWorks pour accélérer leurs charges de travail nécessitant beaucoup de ressources système en tirant avantage des clusters de calcul et des services de Grid Computing. [Parallel Computing Toolbox](http://www.mathworks.com/products/parallel-computing/) permet aux utilisateurs MATLAB de mettre des applications en parallèle et de tirer parti de processeurs multicœur, de GPU et de clusters de calcul. [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) permet aux utilisateurs MATLAB d’utiliser de nombreux ordinateurs dans un cluster de calcul.

Grâce aux machines virtuelles Azure, vous pouvez créer des clusters MATLAB Distributed Computing Server disposant tous des mêmes mécanismes pour soumettre un travail parallèle en tant que clusters locaux, comme des travaux interactifs, des traitements par lots, des tâches indépendantes et des tâches de communication. L’utilisation conjointe d’Azure et de la plateforme MATLAB offre de nombreux avantages par rapport à l’approvisionnement et à l’utilisation des matériels locaux traditionnels : différentes tailles de machine virtuelle, création de clusters à la demande vous permettant de payer uniquement les ressources de calcul que vous utilisez, et possibilité de tester les modèles à grande échelle.  

## <a name="prerequisites"></a>Conditions préalables
* **Ordinateur client** : vous avez besoin d’un ordinateur client Windows pour communiquer avec Azure et le cluster MATLAB Distributed Computing Server après le déploiement.
* **Azure PowerShell** : voir [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) pour installer Azure PowerShell sur votre ordinateur client.
* **Abonnement Azure** : si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes. Pour les clusters de grande taille, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat.
* **Quota de processeurs virtuels** : vous devrez peut-être augmenter le quota de processeurs virtuels pour déployer un cluster de grande taille ou plusieurs clusters MATLAB Distributed Computing Server. Pour augmenter un quota, [ouvrez une demande de service clientèle en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.
* **Licences MATLAB, Parallel Computing Toolbox et MATLAB Distributed Computing Server** : les scripts reposent sur l’hypothèse que le gestionnaire [MathWorks Hosted License Manager](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) est utilisé pour toutes les licences.  
* **Logiciel MATLAB Distributed Computing Server** : ce logiciel est installé sur une machine virtuelle qui fera office d’image de machine virtuelle de base pour les machines virtuelles de cluster.

## <a name="high-level-steps"></a>Étapes de haut niveau
Pour utiliser des machines virtuelles Azure pour vos clusters MATLAB Distributed Computing Server, il convient de suivre les étapes de haut niveau ci-après. Les instructions détaillées figurent dans la documentation qui accompagne le modèle de démarrage rapide et les scripts sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Créer une image de machine virtuelle de base**  

   * Téléchargez et installez le logiciel MATLAB Distributed Computing Server sur cette machine virtuelle.

     > [!NOTE]
     > Ce processus peut prendre quelques heures, mais vous n’avez besoin de l’exécuter qu’une seule fois par version de MATLAB utilisée.   
     >
     >
2. **Créer un ou plusieurs clusters**  

   * Utilisez le script PowerShell fourni ou le modèle de démarrage rapide pour créer un cluster à partir de l’image de machine virtuelle de base.   
   * Gérez les clusters à l’aide du script PowerShell fourni, qui vous permet de répertorier, suspendre, reprendre et supprimer les clusters.

## <a name="cluster-configurations"></a>Configurations de cluster
Pour l’instant, le modèle et le script de création de cluster vous permettent de créer une seule topologie MATLAB Distributed Computing Server. Si vous le souhaitez, créez un ou plusieurs clusters supplémentaires, comportant chacun un nombre distinct de machines virtuelles de travail, utilisant différentes tailles de machine virtuelle, et ainsi de suite.

### <a name="matlab-client-and-cluster-in-azure"></a>Client MATLAB et cluster dans Azure
Le nœud du client MATLAB, le nœud du planificateur de travaux MATLAB et les nœuds de travail MATLAB Distributed Computing Server sont tous configurés en tant que machines virtuelles Azure dans un réseau virtuel, comme illustré par la figure suivante.


* Pour utiliser le cluster, connectez-vous au nœud client à l’aide de la fonctionnalité Bureau à distance. Le nœud client exécute le client MATLAB.
* Le nœud client présente un partage de fichiers accessible par tous les travaux.
* Le gestionnaire MathWorks Hosted License Manager est utilisé pour la vérification des licences de tous les logiciels MATLAB.
* Par défaut, un seul travail MATLAB Distributed Computing Server par processeur virtuel est créé sur les machines virtuelles de travail, mais vous pouvez spécifier le nombre de travaux de votre choix.

## <a name="use-an-azure-based-cluster"></a>Utiliser un cluster Azure
Comme pour d’autres types de clusters MATLAB Distributed Computing Server, vous devez utiliser le gestionnaire Cluster Profile Manager dans le client MATLAB (sur la machine virtuelle cliente) pour créer un profil de cluster de planificateur de travaux MATLAB.

![Cluster Profile Manager](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des instructions détaillées concernant le déploiement et la gestion des clusters MATLAB Distributed Computing Server dans Azure, voir le référentiel [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contenant les modèles et les scripts.
* Accédez au [site de MathWorks](http://www.mathworks.com/) pour découvrir la documentation détaillée de MATLAB et de MATLAB Distributed Computing Server.
