---
title: Instance de cluster de basculement (FCI) SQL Server - Machines virtuelles Azure | Microsoft Docs
description: Cet article explique comment créer une instance de cluster de basculement SQL Server sur des machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 13/22/2018
ms.author: mikeray
ms.openlocfilehash: faa849fc53aa15a47e850a20531c4fa30544f750
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurer une instance de cluster de basculement SQL Server sur des machines virtuelles Azure

Cet article explique comment créer une instance de cluster de basculement (FCI) SQL Server sur des machines virtuelles Azure dans le modèle Resource Manager. Cette solution utilise [l’édition Espaces de stockage direct \(S2D\) de Windows Server 2016 Datacenter](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) en tant que réseau SAN virtuel basé sur logiciel qui synchronise le stockage (disques de données) entre les nœuds (machines virtuelles Azure) dans un cluster Windows. La technologie Espaces de stockage direct (S2D) est une nouveauté dans Windows Server 2016.

Le schéma suivant illustre la solution complète sur les machines virtuelles Azure :

![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Le schéma précédent illustre :

- Deux machines virtuelles Azure dans un cluster de basculement Windows. Lorsqu’une machine virtuelle se trouve dans un cluster de basculement, elle est également désignée comme *nœud* ou *nœuds* de cluster.
- Chaque machine virtuelle possède au moins deux disques de données.
- La technologie S2D synchronise les données sur le disque de données et présente le stockage synchronisé en tant que pool de stockage.
- Le pool de stockage présente un volume partagé de cluster au cluster de basculement.
- Le rôle du cluster de l’instance de cluster de basculement SQL Server utilise le volume partagé de cluster pour les lecteurs de données.
- Un équilibrage de charge Azure pour contenir l’adresse IP de l’instance de cluster de basculement SQL Server.
- Un groupe à haute disponibilité Azure contient toutes les ressources.

   >[!NOTE]
   >Toutes les ressources Azure dans le schéma se trouvent dans le même groupe de ressources.

Pour plus d’informations sur la technologie S2D, consultez [l’édition Espaces de stockage direct \(S2D\) de Windows Server 2016 Datacenter](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

La technologie S2D prend en charge deux types d’architectures : convergée et hyper-convergée. L’architecture dans ce document est hyper-convergée. Une infrastructure hyper-convergée place le stockage sur les mêmes serveurs que ceux qui hébergent l’application en cluster. Dans cette architecture, le stockage se trouve sur chaque nœud de l’instance de cluster de basculement SQL Server.

## <a name="licensing-and-pricing"></a>Licences et tarification

Sur les machines virtuelles Azure, vous pouvez acquérir une licence SQL Server à l’aide des images de machines virtuelles avec paiement à l’utilisation (PAYG) ou BYOL (apportez votre propre licence). Le type d’image que vous choisissez affecte la façon dont vous êtes facturé.

Avec la licence PAYG, une instance de cluster de basculement (FCI) de SQL Server sur des machines virtuelles Azure entraîne des frais pour tous les nœuds de FCI, y compris les nœuds passifs. Pour plus d’informations, consultez [Tarification des machines virtuelles SQL Server Entreprise](http://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Les clients avec un Contrat Entreprise et la Software Assurance ont le droit d’utiliser un nœud FCI passif gratuit pour chaque nœud actif. Pour tirer parti de cet avantage dans Azure, utilisez des images de machines virtuelles BYOL, puis utilisez la même licence sur les nœuds actifs et passifs de l’instance FCI. Pour plus d’informations, consultez [Accord Entreprise](http://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Pour comparer les licences PAYG et BYOL pour SQL Server sur des machines virtuelles Azure, consultez [Bien démarrer avec des machines virtuelles SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Pour plus d’informations sur les licences SQL Server, consultez [Tarification](http://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemple de modèle Azure

Vous pouvez créer la solution complète dans Azure à partir d’un modèle. Un exemple de modèle est disponible dans les [Modèles de démarrage rapide Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) sur GitHub. Cet exemple n’est pas conçu ni testé pour une charge de travail spécifique. Vous pouvez exécuter le modèle pour créer une instance de cluster de basculement SQL Server avec le stockage S2D connecté à votre domaine. Vous pouvez évaluer le modèle et le modifier selon vos besoins.

## <a name="before-you-begin"></a>Avant de commencer

Il existe quelques éléments que vous devez connaître et deux choses que vous devez mettre en place avant de continuer.

### <a name="what-to-know"></a>Éléments à connaître
Vous devez avoir une compréhension opérationnelle des technologies suivantes :

- [Technologies de cluster Windows](http://technet.microsoft.com/library/hh831579.aspx)
-  [Instances de cluster de basculement SQL Server](http://msdn.microsoft.com/library/ms189134.aspx).

Vous devez également avoir une compréhension générale des technologies suivantes :

- [Solution hyper-convergée utilisant les Espaces de stockage direct dans Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Groupes de ressources Azure](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>Éléments à mettre en place

Avant de suivre les instructions de cet article, vérifiez que vous disposez déjà des éléments suivants :

- Un abonnement Microsoft Azure.
- Un domaine Windows sur des machines virtuelles Azure.
- Un compte autorisé à créer des objets sur la machine virtuelle Azure.
- Un réseau virtuel et un sous-réseau Azure avec suffisamment d’espace d’adressage IP pour les composants suivants :
   - Les deux machines virtuelles.
   - L’adresse IP du cluster de basculement.
   - Une adresse IP pour chaque instance de cluster de basculement.
- Un DNS configuré sur le réseau Azure, pointant vers les contrôleurs de domaine.

Une fois ces conditions préalables en place, vous pouvez passer à la création de votre cluster de basculement. La première étape consiste à créer les machines virtuelles.

## <a name="step-1-create-virtual-machines"></a>Étape 1 : Créer les machines virtuelles

1. Connectez-vous au [portail Azure](http://portal.azure.com) avec votre abonnement.

1. [Créez un groupe à haute disponibilité Azure](../tutorial-availability-sets.md).

   Le groupe à haute disponibilité regroupe les machines virtuelles entre les domaines d’erreur et les domaines de mise à jour. Le groupe à haute disponibilité garantit que votre application n’est pas affectée par les points de défaillance uniques, comme le commutateur réseau ou l’unité d’alimentation d’un rack de serveurs.

   Si vous n’avez pas créé le groupe de ressources pour vos machines virtuelles, faites-le lorsque vous créez un groupe à haute disponibilité Azure. Si vous utilisez le portail Azure pour créer le groupe à haute disponibilité, procédez comme suit :

   - Dans le portail Azure, cliquez sur **+** pour ouvrir Azure Marketplace. Recherchez **Groupe à haute disponibilité**.
   - Cliquez sur **Groupe à haute disponibilité**.
   - Cliquez sur **Créer**.
   - Dans le panneau **Créer un groupe à haute disponibilité**, définissez les valeurs suivantes :
      - **Nom** : nom du groupe à haute disponibilité.
      - **Abonnement** : votre abonnement Azure.
      - **Groupe de ressources** : si vous souhaitez utiliser un groupe existant, cliquez sur **Use existing** (Utiliser le groupe existant) et sélectionnez le groupe dans la liste déroulante. Sinon, choisissez **Créer** et entrez un nom pour le groupe.
      - **Emplacement** : définissez l’emplacement où vous souhaitez créer vos machines virtuelles.
      - **Domaines d’erreur** : utilisez la valeur par défaut (3).
      - **Domaines de mise à jour** : utilisez la valeur par défaut (5).
   - Cliquez sur **Créer** pour créer le groupe à haute disponibilité.

1. Créez les machines virtuelles dans le groupe à haute disponibilité sélectionné.

   Configurez deux machines virtuelles SQL Server dans le groupe à haute disponibilité Azure. Pour obtenir des instructions, consultez [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Placez les deux machines virtuelles :

   - Dans le même groupe de ressources Azure que celui où se trouve le groupe à haute disponibilité.
   - Sur le même réseau que votre contrôleur de domaine.
   - Sur un sous-réseau avec un espace d’adressage IP suffisant pour les deux machines virtuelles et toutes les instances de cluster de basculement que vous pourriez utiliser sur ce cluster.
   - Dans le groupe à haute disponibilité Azure.   

      >[!IMPORTANT]
      >Vous ne pouvez pas définir ni modifier un groupe à haute disponibilité après la création d’une machine virtuelle.

   Choisissez une image dans Azure Marketplace. Vous pouvez utiliser une image Marketplace qui inclut Windows Server et SQL Server, ou uniquement Windows Server. Pour plus d’informations, consultez [Présentation de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   Les images SQL Server officielles dans la galerie Azure incluent une instance SQL Server installée, ainsi que le logiciel d’installation SQL Server et la clé requise.

   Choisissez l’image appropriée en fonction de la façon dont vous souhaitez payer pour la licence SQL Server :

   - **Licences avec paiement à l’utilisation** : le coût par seconde de ces images inclut l’attribution de licences SQL Server :
      - **SQL Server 2016 Enterprise sur Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard sur Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer sur Windows Server Datacenter 2016**

   - **BYOL (apportez votre propre licence)**

      - **{BYOL} SQL Server 2016 Enterprise sur Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard sur Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Après avoir créé la machine virtuelle, supprimez l’instance SQL Server autonome préinstallée. Vous utiliserez le support SQL Server préinstallé pour créer l’instance de cluster de basculement SQL Server après avoir configuré le cluster de basculement et les espaces de stockage direct.

   Vous pouvez également utiliser des images Azure Marketplace avec le système d’exploitation seulement. Choisissez une image **Windows Server 2016 Datacenter** et installez l’instance de cluster de basculement SQL Server après avoir configuré le cluster de basculement et les espaces de stockage direct. Cette image ne contient aucun support d’installation SQL Server. Placez le support d’installation dans un emplacement où vous pouvez exécuter l’installation de SQL Server pour chaque serveur.

1. Une fois que vos machines virtuelles ont été créées par Azure, connectez-vous à chaque machine virtuelle en utilisant le protocole RDP.

   Lors de votre première connexion à une machine virtuelle avec le protocole RDP, l’ordinateur vous demande si vous souhaitez autoriser que cet ordinateur soit détecté sur le réseau. Cliquez sur **Oui**.

1. Si vous utilisez l’une des images de machine virtuelle basée sur SQL Server, supprimez l’instance SQL Server.

   - Dans **Programmes et fonctionnalités**, cliquez avec le bouton droit sur **Microsoft SQL Server 2016 (64 bits)** et sur **Désinstaller/modifier**.
   - Cliquez sur **Supprimer**.
   - Sélectionnez l’instance par défaut.
   - Supprimer toutes les fonctionnalités sous **Services Moteur de base de données**. Ne supprimez pas les **Fonctionnalités partagées**. Consultez l’illustration suivante :

      ![Supprimer des fonctionnalités](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Cliquez sur **Suivant**, puis sur **Supprimer**.

1. <a name="ports"></a>Ouvrez les ports du pare-feu.

   Sur chaque machine virtuelle, ouvrez les ports suivants du pare-feu Windows.

   | Objectif | Port TCP | Notes
   | ------ | ------ | ------
   | SQL Server | 1433 | Port normal pour les instances par défaut de SQL Server. Si vous avez utilisé une image de la galerie, ce port s’ouvre automatiquement.
   | Sonde d’intégrité | 59999 | Tout port TCP ouvert. Dans une étape ultérieure, configurez la [sonde d’intégrité](#probe) de l’équilibrage de charge et le cluster pour qu’ils utilisent ce port.  

1. Ajoutez du stockage à la machine virtuelle. Pour plus d’informations, consultez [Ajouter du stockage](../premium-storage.md).

   Les deux machines virtuelles ont besoin d’au moins deux disques de données.

   Attachez des disques bruts, et non des disques au format NTFS.
      >[!NOTE]
      >Si vous attachez des disques au format NTFS, vous pouvez uniquement activer la technologie S2D sans vérification d’éligibilité de disque.  

   Attachez au moins deux instances de stockage Premium (disques SSD) à chaque machine virtuelle. Nous vous recommandons d’utiliser au minimum des disques P30 (1 To).

   Définissez la mise en cache de l’hôte sur **Lecture seule**.

   La capacité de stockage que vous utilisez dans des environnements de production dépend de votre charge de travail. Les valeurs décrites dans cet article sont à des fins de démonstration et de test.

1. [Ajoutez les machines virtuelles à votre domaine préexistant](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Une fois les machines virtuelles créées et configurées, vous pouvez configurer le cluster de basculement.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Étape 2 : Configurer le cluster de basculement Windows avec la technologie S2D

L’étape suivante consiste à configurer le cluster de basculement avec la technologie S2D. Dans cette étape, vous allez exécuter les sous-étapes suivantes :

1. Ajouter la fonctionnalité Windows de Clustering de basculement
1. Valider le cluster
1. Créer le cluster de basculement
1. Créer le témoin cloud
1. Ajouter du stockage

### <a name="add-windows-failover-clustering-feature"></a>Ajouter la fonctionnalité Windows de Clustering de basculement

1. Pour commencer, connectez-vous à la première machine virtuelle avec RDP à l’aide d’un compte de domaine qui est membre du groupe Administrateurs locaux et qui dispose des autorisations pour créer des objets dans Active Directory. Utilisez ce compte pour le reste de la configuration.

1. [Ajoutez la fonctionnalité de Clustering de basculement à chaque machine virtuelle](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Pour installer la fonctionnalité de Clustering de basculement à partir de l’interface utilisateur, exécutez les étapes suivantes sur les deux machines virtuelles.
   - Dans le **Gestionnaire de serveur**, cliquez sur **Gérer**, puis sur **Ajouter des rôles et fonctionnalités**.
   - Dans **l’Assistant Ajout de rôles et de fonctionnalités**, cliquez sur **Suivant** jusqu’à ce que vous atteigniez la page **Sélectionner les fonctionnalités**.
   - Dans **Sélectionner les fonctionnalités**, cliquez sur **Clustering de basculement**. Incluez toutes les fonctionnalités et les outils de gestion requis. Cliquez sur **Ajouter des fonctionnalités**.
   - Cliquez sur **Suivant**, puis sur **Terminer** pour installer les fonctionnalités.

   Pour installer la fonctionnalité Clustering de basculement avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

À titre de référence, les étapes suivantes respectent les instructions de l’Étape 3 sous [Solution hyper-convergée utilisant les Espaces de stockage direct dans Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Valider le cluster

Ce guide fait référence aux instructions sous [Valider le cluster](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Validez le cluster dans l’interface utilisateur ou avec PowerShell.

Pour valider le cluster avec l’interface utilisateur, effectuez les étapes suivantes à partir d’une des machines virtuelles.

1. Dans le **Gestionnaire de serveur**, cliquez sur **Outils**, puis cliquez sur **Gestionnaire du cluster de basculement**.
1. Dans le **Gestionnaire du cluster de basculement**, cliquez sur **Action**, puis cliquez sur **Valider la configuration...**.
1. Cliquez sur **Suivant**.
1. Sous **Sélectionner des serveurs ou un cluster**, entrez le nom des deux machines virtuelles.
1. Sous **Options de test**, choisissez **Exécuter uniquement les tests que je sélectionne**. Cliquez sur **Suivant**.
1. Sous **Sélection du test**, incluez tous les tests sauf **Stockage**. Consultez l’illustration suivante :

   ![Valider les tests](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Cliquez sur **Suivant**.
1. Sous **Confirmation**, cliquez sur **Suivant**.

**L’Assistant Validation d’une configuration** exécute les tests de validation.

Pour valider le cluster avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Après avoir validé le cluster, créez le cluster de basculement.

### <a name="create-the-failover-cluster"></a>Créer le cluster de basculement

Ce guide fait référence à la section [Créer le cluster de basculement](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Pour créer le cluster de basculement, vous avez besoin des éléments suivants :
- Les noms des machines virtuelles qui deviennent les nœuds du cluster.
- Un nom pour le cluster de basculement.
- Une adresse IP pour le cluster de basculement. Vous pouvez spécifier une adresse IP qui n’est pas utilisée sur le même réseau virtuel et sous-réseau Azure que les nœuds du cluster.

Le script PowerShell suivant crée un cluster de basculement. Mettez à jour le script avec les noms des nœuds (les noms des machines virtuelles) et une adresse IP disponible à partir du réseau virtuel Azure :

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Créer un témoin cloud

Un témoin cloud est un nouveau type de témoin de quorum de cluster stocké dans un Azure Storage Blob. Cela supprime la nécessité de disposer d’une machine virtuelle distincte qui héberge un partage de fichiers du témoin.

1. [Créez un témoin cloud pour le cluster de basculement](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Créez un conteneur d’objets blob.

1. Enregistrez les clés d’accès et l’URL du conteneur.

1. Configurez le témoin de quorum du cluster de basculement. Consultez, [Configurer le témoin de quorum dans l’interface utilisateur]. (http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) dans l’interface utilisateur.

### <a name="add-storage"></a>Ajouter du stockage

Les disques pour la technologie S2D doivent être vides et sans partitions ou autres données. Pour nettoyer les disques, suivez [les étapes décrites dans ce guide](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Activez les Espaces de stockage direct \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Le script PowerShell suivant active les espaces de stockage direct.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   Dans le **Gestionnaire du cluster de basculement**, vous pouvez maintenant voir le pool de stockage.

1. [Créez un volume](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   L’une des fonctionnalités de la technologie S2D est qu’elle crée automatiquement un pool de stockage lorsque vous l’activez. Vous êtes maintenant prêt à créer un volume. L’applet de commande PowerShell `New-Volume` automatise le processus de création de volume, notamment la mise en forme, l’ajout au cluster et la création d’un volume partagé de cluster (CSV). L’exemple suivant crée un volume partagé de cluster de 800 gigaoctets.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Une fois cette commande terminée, un volume de 800 Go est monté en tant que ressource du cluster. Le volume se trouve sous `C:\ClusterStorage\Volume1\`.

   Le schéma suivant illustre un volume partagé de cluster avec la technologie S2D :

   ![VolumePartagéCluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Étape 3 : Tester le basculement du cluster de basculement

Dans le Gestionnaire du cluster de basculement, vérifiez que vous pouvez déplacer la ressource de stockage vers l’autre nœud du cluster. Si vous pouvez vous connecter au cluster de basculement avec le **Gestionnaire du cluster de basculement** et déplacer le stockage d’un nœud à l’autre, vous êtes prêt à configurer l’instance de cluster de basculement.

## <a name="step-4-create-sql-server-fci"></a>Étape 4 : Créer l’instance de cluster de basculement SQL Server

Après avoir configuré le cluster de basculement et tous les composants du cluster, notamment le stockage, vous pouvez créer l’instance de cluster de basculement SQL Server.

1. Connectez-vous à la première machine virtuelle avec RDP.

1. Dans le **Gestionnaire du cluster de basculement**, vérifiez que toutes les ressources principales du cluster se trouvent sur la première machine virtuelle. Si nécessaire, déplacez toutes les ressources vers cette machine virtuelle.

1. Recherchez le support d’installation. Si la machine virtuelle utilise l’une des images Azure Marketplace, le support se situe sous `C:\SQLServer_<version number>_Full`. Cliquez sur **Setup**.

1. Dans le **Centre d’installation SQL Server**, cliquez sur **Installation**.

1. Cliquez sur **Installation d’un nouveau cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer l’instance de cluster de basculement SQL Server.

   Les répertoires de données de l’instance de cluster de basculement doivent se trouver sur le stockage en cluster. Avec la technologie S2D, il ne s’agit pas d’un disque partagé, mais d’un point de montage vers un volume sur chaque serveur. La technologie S2D synchronise le volume entre les deux nœuds. Le volume est présenté au cluster en tant que volume partagé de cluster. Utilisez le point de montage du volume partagé de cluster pour les répertoires de données.

   ![RépertoiresDonnées](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Une fois l’Assistant terminé, le programme d’installation installe une instance de cluster de basculement SQL Server sur le premier nœud.

1. Une fois que le programme d’installation a correctement installé l’instance de cluster de basculement sur le premier nœud, connectez-vous au second nœud avec RDP.

1. Ouvrez le **Centre d’installation SQL Server**. Cliquez sur **Installation**.

1. Cliquez sur **Ajouter un nœud à un cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer SQL Server et ajouter ce serveur à l’instance de cluster de basculement.

   >[!NOTE]
   >Si vous avez utilisé une image de la galerie Azure Marketplace avec SQL Server, les outils SQL Server ont été inclus avec l’image. Si vous n’avez pas utilisé cette image, installez les outils SQL Server séparément. Consultez [Télécharger SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Étape 5 : Créer un équilibrage de charge Azure

Sur les machines virtuelles Azure, les clusters utilisent un équilibrage de charge pour conserver une adresse IP qui doit se trouver sur un nœud de cluster à la fois. Dans cette solution, l’équilibrage de charge contient l’adresse IP de l’instance de cluster de basculement SQL Server.

[Créez et configurez un équilibrage de charge Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Créer l’équilibrage de charge dans le portail Azure

Pour créer l’équilibrage de charge :

1. Dans le portail Azure, accédez au groupe de ressources contenant les machines virtuelles.

1. Cliquez sur **+ Ajouter**. Recherchez **Équilibrage de charge** dans Azure Marketplace. Cliquez sur **Équilibrage de charge**.

1. Cliquez sur **Créer**.

1. Configurez l’équilibrage de charge avec :

   - **Nom** : nom qui identifie l’équilibrage de charge.
   - **Type** : l’équilibrage de charge peut être public ou privé. Un équilibrage de charge privé est accessible à partir du même réseau virtuel. La plupart des applications Azure peut utiliser un équilibrage de charge privé. Si votre application doit accéder à SQL Server directement via Internet, utilisez un équilibrage de charge public.
   - **Réseau virtuel** : le même réseau que les machines virtuelles.
   - **Sous-réseau** : le même sous-réseau que les machines virtuelles.
   - **Adresse IP privée** : la même adresse IP que celle attribuée à la ressource réseau de cluster FCI SQL Server.
   - **Abonnement** : votre abonnement Azure.
   - **Groupe de ressources** : utilisez le même groupe de ressources que celui de vos machines virtuelles.
   - **Emplacement** : utilisez le même emplacement Azure que celui de vos machines virtuelles.
   Consultez l’illustration suivante :

   ![CréerÉquilibrageCharge](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurer le pool principal de l’équilibrage de charge

1. Revenez au groupe de ressources Azure contenant les machines virtuelles et recherchez le nouvel équilibrage de charge. Vous devrez peut-être actualiser l’affichage du groupe de ressources. Cliquez sur l’équilibreur de charge.

1. Dans le panneau de l’équilibrage de charge, cliquez sur **Pools principaux**.

1. Cliquez sur **+ Ajouter** pour ajouter un pool principal.

1. Entez un nom pour le pool principal.

1. Cliquez sur **Ajouter une machine virtuelle**.

1. Dans le panneau **Choisir des machines virtuelles**, cliquez sur **Choisir un groupe à haute disponibilité**.

1. Choisissez le groupe à haute disponibilité dans lequel vous avez placé les machines virtuelles SQL Server.

1. Dans le panneau **Choisir des machines virtuelles**, cliquez sur **Choisir les machines virtuelles**.

   Votre portail Azure doit ressembler à l’image suivante :

   ![CréerPoolPrincipalÉquilibrageCharge](./media/virtual-machines-windows-portal-sql-create-failover-cluster/33-load-balancer-back-end.png)

1. Cliquez sur **Sélectionner** dans le panneau **Choisir des machines virtuelles**.

1. Cliquez sur **OK** deux fois.

### <a name="configure-a-load-balancer-health-probe"></a>Configurer une sonde d’intégrité d’équilibrage de charge

1. Dans le panneau de l’équilibrage de charge, cliquez sur **Sondes d’intégrité**.

1. Cliquez sur **+ Ajouter**.

1. Dans le panneau **Ajouter une sonde d’intégrité**, <a name="probe"></a>définissez les paramètres de la sonde d’intégrité :

   - **Nom** : nom de la sonde d’intégrité.
   - **Protocole** : TCP.
   - **Port** : défini sur un port TCP disponible. Ce port nécessite un port de pare-feu ouvert. Utilisez le [même port](#ports) que celui défini pour la sonde d’intégrité au niveau du pare-feu.
   - **Intervalle** : 5 secondes.
   - **Seuil de défaillance** : 2 défaillances consécutives.

1. Cliquez sur OK.

### <a name="set-load-balancing-rules"></a>Définir les règles d’équilibrage de charge

1. Dans le panneau de l’équilibrage de charge, cliquez sur **Règles d’équilibrage de charge**.

1. Cliquez sur **+ Ajouter**.

1. Configurez les paramètres d’équilibrage de charge :

   - **Nom** : nom des règles d’équilibrage de charge.
   - **Adresse IP frontale** : utilisez l’adresse IP de la ressource réseau de cluster FCI SQL Server.
   - **Port** : défini pour le port TCP FCI SQL Server. Le port d’instance par défaut est 1433.
   - **Port principal** : cette valeur utilise le même port que la valeur **Port** lorsque vous activez **Adresse IP flottante (retour serveur direct)**.
   - **Pool principal** : utilisez le nom du pool principal que vous avez configuré précédemment.
   - **Sonde d’intégrité** : utilisez la sonde d’intégrité que vous avez configurée précédemment.
   - **Persistance de session** : aucune.
   - **Délai d’inactivité (minutes)** : 4.
   - **Adresse IP flottante (retour serveur direct)** : activée

1. Cliquez sur **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Étape 6 : Configurer le cluster pour la sonde

Définissez le paramètre de port de sonde de cluster dans PowerShell.

Pour définir le paramètre de port de sonde de cluster, mettez à jour les variables dans le script suivant avec des valeurs à partir de votre environnement. Supprimez les crochets pointus `<>` du script. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Dans le script précédent, définissez les valeurs pour votre environnement. La liste ci-dessous décrit les valeurs :

   - `<Cluster Network Name>` : nom du cluster de basculement Windows Server pour le réseau. Dans le **Gestionnaire du cluster de basculement** > **Réseaux**, cliquez avec le bouton droit sur le réseau et cliquez sur **Propriétés**. La valeur correcte est sous **Nom** dans l’onglet **Général**. 

   - `<SQL Server FCI IP Address Resource Name>`: nom de ressource d’adresse IP de l’instance de cluster de basculement SQL Server. Dans le **Gestionnaire du cluster de basculement** > **Rôles**, sous le rôle de l’instance de cluster de basculement SQL Server, sous **Nom du serveur**, cliquez avec le bouton droit sur la ressource d’adresse IP, et cliquez sur **Propriétés**. La valeur correcte est sous **Nom** dans l’onglet **Général**. 

   - `<ILBIP>` : adresse IP de l’ILB. Cette adresse est configurée dans le portail Azure en tant qu’adresse frontale d’équilibrage de charge interne. Il s’agit également de l’adresse IP de l’instance de cluster de basculement SQL Server. Vous pouvez la trouver dans le **Gestionnaire du cluster de basculement** sur la page de propriétés où se trouve également localisé le `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: est le port de sonde que vous avez configuré dans la sonde d’intégrité d’équilibrage de charge. N’importe quel port TCP inutilisé est valide. 

>[!IMPORTANT]
>Le masque de sous-réseau pour le paramètre de cluster doit être l’adresse de diffusion TCP IP : `255.255.255.255`.

Après avoir défini la sonde du cluster, vous pouvez voir tous les paramètres de cluster dans PowerShell. Exécutez le script qui suit :

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Étape 7 : Test du basculement FCI

Testez le basculement de l’instance de cluster de basculement pour valider le fonctionnement du cluster. Effectuez également les étapes suivantes :

1. Connectez-vous à l’un des nœuds de cluster FCI SQL Server avec RDP.

1. Ouvrez le **Gestionnaire du cluster de basculement**. Cliquez sur **Rôles**. Notez le nœud qui possède le rôle de l’instance de cluster de basculement SQL Server.

1. Cliquez avec le bouton droit sur le rôle de l’instance de cluster de basculement SQL Server.

1. Cliquez sur **Déplacer** et sur **Meilleur nœud possible**.

Le **Gestionnaire du cluster de basculement** présente le rôle et ses ressources hors connexion. Ensuite, les ressources sont déplacées et mises en ligne sur l’autre nœud.

### <a name="test-connectivity"></a>Tester la connectivité

Pour tester la connectivité, connectez-vous à une autre machine virtuelle sur le même réseau virtuel. Ouvrez **SQL Server Management Studio** et connectez-vous au nom FCI SQL Server.

>[!NOTE]
>Si nécessaire, vous pouvez [télécharger SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limites
Sur les machines virtuelles Azure, Microsoft Distributed Transaction Coordinator (DTC) n’est pas pris en charge sur les instances de cluster de basculement car le port RPC n’est pas pris en charge par l’équilibrage de charge.

## <a name="see-also"></a>Voir aussi

[Configurer S2D avec le Bureau à distance (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solution hyper-convergée avec Espaces de stockage direct](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Vue d’ensemble de l’espace de stockage direct](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Prise en charge de SQL Server pour S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
