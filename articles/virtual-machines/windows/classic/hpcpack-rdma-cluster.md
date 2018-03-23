---
title: Configuration d’un cluster Windows RDMA pour exécuter des applications MPI | Microsoft Docs
description: Apprenez à créer un cluster Windows HPC Pack avec des machines virtuelles de taille H16r, H16mr, A8 ou A9 pour utiliser le réseau Azure RDMA afin d’exécuter des applications MPI.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 437c475735ec3823de51c5f9e996a5303fe9cfa7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configuration d’un cluster Windows RDMA avec HPC Pack pour exécuter des applications MPI
Configurez un cluster RDMA Windows dans Azure avec [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) et des [tailles de machines virtuelles de calcul haute performance compatibles RDMA](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) pour exécuter des applications MPI (Message Passing Interface) parallèles. Lorsque vous configurez des nœuds Windows Server compatibles RDMA dans un cluster HPC Pack, les applications MPI communiquent efficacement sur un réseau à latence faible et à débit élevé dans Azure, reposant sur la technologie d’accès direct à la mémoire à distance (RDMA).

Si vous souhaitez exécuter des charges de travail MPI sur des machines virtuelles Linux qui accèdent au réseau Azure RDMA, consultez [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>Options de déploiement de cluster HPC Pack
Microsoft HPC Pack est un outil fourni sans frais supplémentaires pour créer des clusters HPC locaux ou dans Azure afin d’exécuter des applications HPC Windows ou Linux. HPC Pack comprend un environnement d'exécution pour l'implémentation Microsoft de MPI pour Windows (MS-MPI). Dans le cas d’une utilisation d’instances compatibles RDMA exécutant un système d’exploitation Windows Server pris en charge, HPC Pack est un moyen efficace d’exécuter des applications MPI Windows qui accèdent au réseau Azure compatible RDMA. 

Cet article présente deux scénarios, ainsi que des liens vers des instructions détaillées, pour configurer un cluster RDMA Windows avec Microsoft HPC Pack 2012 R2. 

* Scénario 1 Déployer des instances de rôle de travail nécessitant beaucoup de ressources système (PaaS)
* Scénario 2 Déployer des nœuds de calcul sur des machines virtuelles nécessitant beaucoup de ressources système (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scénario 1 : Déployer des instances de rôle de travail nécessitant beaucoup de ressources système (PaaS)
À partir d’un cluster HPC Pack existant, ajoutez des ressources de calcul supplémentaires dans les instances de rôle de travail Azure (nœuds Azure) en cours d’exécution dans un service cloud (PaaS). Cette fonctionnalité, également appelée « Intégration à Azure » à partir de HPC Pack, prend en charge une plage de tailles d’instances de rôle de travail. Lors de l’ajout des nœuds Azure, spécifiez l’une des tailles compatibles RDMA.

Voici les considérations et les étapes pour l’intégration aux instances Azure compatibles avec RDMA à partir d’un cluster existant (généralement local). Utilisez des procédures similaires pour ajouter des instances de rôle de travail à un nœud principal HPC Pack déployé dans une machine virtuelle Azure.

> [!NOTE]
> Pour obtenir un didacticiel sur l’intégration à Azure avec HPC Pack, consultez [Configurer un cluster hybride avec HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenez compte des considérations dans les étapes suivantes qui s’appliquent spécifiquement aux nœuds Azure compatibles RDMA.
> 
> 

![Intégration à Azure][burst]

### <a name="steps"></a>Étapes
1. **Déployer et configurer un nœud principal HPC Pack 2012 R2**
   
    Téléchargez le package d’installation de HPC Pack à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Pour plus d’informations sur la configuration requise et des instructions de préparation pour le déploiement d’intégrations à Azure, consultez [Burst to Azure with Microsoft HPC Pack (Intégration à Azure avec Microsoft HPC Pack)](https://technet.microsoft.com/library/gg481749.aspx).
2. **Configurer un certificat de gestion dans l’abonnement Azure**
   
    Configurez un certificat pour sécuriser la connexion entre le nœud principal et Azure. Pour connaître les options et les procédures, consultez [Scénarios pour configurer le certificat de gestion Azure pour HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Pour les déploiements de test, HPC Pack installe un certificat Microsoft HPC Azure Management par défaut que vous pouvez télécharger rapidement dans votre abonnement Azure.
3. **Créer un nouveau service cloud et un compte de stockage**
   
    Utilisez le portail Azure pour créer un service cloud (classique) et un compte de stockage (classique) pour le déploiement. Créez ces ressources dans une région dans laquelle la taille d’instance de série H, A8 ou A9 souhaitée est disponible. Consultez [Produits Azure par région](https://azure.microsoft.com/regions/services/).

4. **Créer un modèle de nœud Azure**
   
    Utilisez l’Assistant Créer un modèle de nœud dans HPC Cluster Manager. Pour les étapes, consultez [Créer un modèle de nœud Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) dans « Étapes à suivre pour déployer des nœuds Azure avec Microsoft HPC Pack ».
   
    Pour les premiers tests, nous vous suggérons de configurer une stratégie de disponibilité manuelle dans le modèle.
5. **Ajouter un nœud au cluster**
   
    Utilisez l’Assistant Ajouter un nœud dans HPC Cluster Manager. Pour plus d’informations, consultez [Ajout des nœuds Azure au cluster Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Lorsque vous spécifiez la taille des nœuds, sélectionnez des tailles d’instance compatibles RDMA.
   
   > [!NOTE]
   > Dans chaque déploiement d’intégration à Azure avec les instances nécessitant beaucoup de ressources système, HPC Pack déploie automatiquement un minimum de deux instances compatibles RDMA (de taille A8) comme nœuds proxy, en plus des instances de rôle de travail Azure que vous spécifiez. Les nœuds proxy utilisent des cœurs qui sont affectés à l’abonnement et occasionnent des frais avec les instances de rôle de travail Azure.
   > 
   > 
6. **Démarrer (approvisionner) les nœuds et les mettre en ligne pour exécuter des travaux**
   
    Sélectionnez les nœuds et utilisez l’action **Démarrer** dans HPC Cluster Manager. Lorsque l’approvisionnement est terminé, sélectionnez les nœuds et utilisez l’action **Mettre en ligne** dans HPC Cluster Manager. Les nœuds sont prêts à exécuter des travaux.
7. **Envoyer des travaux au cluster**
   
   Utilisez les outils de soumission de travaux HPC Pack pour exécuter des travaux de cluster. Consultez [Microsoft HPC Pack : gestion des travaux](http://technet.microsoft.com/library/jj899585.aspx).
8. **Arrêter (annuler le déploiement) les nœuds**
   
   Lorsque vous avez terminé l’exécution des travaux, déconnectez les nœuds et utilisez l’action **Arrêter** dans HPC Cluster Manager.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scénario 2 : Déployer des nœuds de calcul sur des machines virtuelles nécessitant beaucoup de ressources système (IaaS)
Dans ce scénario, vous déployez le nœud principal HPC Pack et les nœuds de calcul de cluster sur des machines virtuelles dans un réseau virtuel Azure. HPC Pack fournit un certain nombre [d’options de déploiement sur les machines virtuelles Azure](../../linux/hpcpack-cluster-options.md), y compris les scripts de déploiement automatisé et les modèles de démarrage rapide Azure. Par exemple, les considérations et procédures suivantes vous guident dans l’utilisation du [script de déploiement de HPC Pack IaaS](hpcpack-cluster-powershell-script.md) pour automatiser le déploiement d’un cluster HPC Pack 2012 R2 dans Azure.

![Cluster sur les machines virtuelles Azure][iaas]

### <a name="steps"></a>Étapes
1. **Créer un nœud principal de cluster et des machines virtuelles à nœud de calcul en exécutant le script de déploiement de HPC Pack IaaS sur un ordinateur client**
   
    Téléchargez le package de script de déploiement de HPC Pack IaaS à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Pour préparer l’ordinateur client, créez le fichier de configuration de script, puis exécutez le script. Consultez [Créer un cluster de calcul haute performance (HPC) Windows avec le script de déploiement du HPC Pack IaaS](hpcpack-cluster-powershell-script.md). 
   
    Pour les points à envisager concernant le déploiement de nœuds de calcul compatibles RDMA, consultez [Tailles de machines virtuelles de calcul haute performance](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) et notez les points suivants :
   
   * **Réseau virtuel** : spécifiez un nouveau réseau virtuel dans une région dans laquelle la taille d’instance de série H, A8 ou A9 souhaitée est disponible. Consultez [Produits Azure par région](https://azure.microsoft.com/regions/services/).

   * **Système d’exploitation Windows Server** : pour prendre en charge la connectivité RDMA, spécifiez un système d’exploitation compatible, comme Windows Server 2012 R2, pour les machines virtuelles de nœud de calcul.
   * **Services cloud**  : étant donné que le script utilise le modèle de déploiement classique, les machines virtuelles du cluster sont déployées à l’aide des services de cloud computing Azure (paramètres `ServiceName` dans le fichier de configuration). Nous vous recommandons de déployer votre nœud principal dans un service cloud et vos nœuds de calcul dans un autre service cloud. 
   * **Taille du nœud principal** : pour ce scénario, considérez une taille d’au moins A4 (très grande) pour le nœud principal.
   * **Extension HpcVmDrivers** : le script de déploiement installe l’agent de machine virtuelle Azure et l’extension HpcVmDrivers automatiquement lors du déploiement de nœuds de calcul de taille A8 ou A9 avec un système d’exploitation Windows Server. HpcVmDrivers installe des pilotes sur des machines virtuelles à nœud de calcul afin qu’ils puissent se connecter au réseau RDMA. Sur les machines virtuelles de série H compatibles RDMA, vous devez installer manuellement l’extension HpcVmDrivers. Consultez [Tailles de machines virtuelles de calcul haute performance](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Configuration du réseau de clusters** : le script de déploiement définit automatiquement le cluster HPC Pack dans la topologie 5 (tous les nœuds sur le réseau d’entreprise). Cette topologie est requise pour tous les déploiements de cluster HPC Pack dans les machines virtuelles. Ne modifiez pas la topologie de réseau de clusters ultérieurement.
1. **Mettre en ligne les nœuds de calcul pour exécuter des travaux**
   
    Sélectionnez les nœuds et utilisez l’action **Mettre en ligne** dans HPC Cluster Manager. Les nœuds sont prêts à exécuter des travaux.
3. **Envoyer des travaux au cluster**
   
    Connectez-vous au nœud principal pour soumettre des travaux, ou configurez un ordinateur local pour ce faire. Pour plus d’informations, consultez [Envoyer des travaux à un cluster HPC dans Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Déconnecter les nœuds et les arrêter (désallouer)**
   
    Lorsque vous avez terminé l’exécution des travaux, déconnectez les nœuds dans HPC Cluster Manager. Puis, utilisez les outils de gestion Azure pour les arrêter.

## <a name="run-mpi-applications-on-the-cluster"></a>Exécuter des applications MPI sur le cluster
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemple : Exécuter mpipingpong sur un cluster HPC Pack
Pour vérifier un déploiement HPC Pack des instances compatibles RDMA, exécutez la commande HPC Pack **mpipingpong** sur le cluster. **mpipingpong** envoie des paquets de données entre des nœuds associés de façon répétée pour calculer la latence et les mesures et statistiques de débit sur le réseau d’application RDMA. Cet exemple montre un modèle classique d’exécution d’un travail MPI (dans ce cas, **mpipingpong**) à l’aide de la commande de cluster **mpiexec**.

Cet exemple suppose que vous ayez ajouté des nœuds Azure dans une configuration de type « intégration à Azure» ([scénario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Si vous avez déployé HPC Pack sur un cluster de machines virtuelles Azure, vous devez modifier la syntaxe de commande pour spécifier un autre groupe de nœuds et définir des variables d’environnement supplémentaires pour diriger le trafic réseau vers le réseau RDMA.

Pour exécuter mpipingpong sur le cluster :

1. Sur le nœud principal ou sur un ordinateur client correctement configuré, ouvrez une invite de commande.
2. Pour estimer la latence entre les paires de nœuds dans un déploiement d’intégration à Azure de quatre nœuds, tapez la commande suivante afin de soumettre un travail pour exécuter mpipingpong avec une petite taille de paquet et un grand nombre d’itérations :
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    La commande retourne l’ID du travail soumis.
   
    Si vous avez déployé le cluster HPC Pack déployé sur les machines virtuelles Azure, spécifiez un groupe de nœuds qui contient des machines virtuelles à nœud de calcul déployées dans un même service cloud et modifiez la commande **mpiexec** comme suit :
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Lorsque le travail est terminé, pour afficher le résultat (dans ce cas, la sortie de la tâche 1 du travail), tapez la commande suivante :
   
    ```Command
    task view <JobID>.1
    ```
   
    où &lt;*JobID*&gt; est l’ID du travail qui a été soumis.
   
    La sortie indiquera des résultats de latence semblables à ceci :
   
    ![Latence ping pong][pingpong1]
4. Pour estimer le débit entre les paires de nœuds d’extension Azure, tapez la commande suivante afin de soumettre un travail pour exécuter **mpipingpong** avec une grande taille de paquet et quelques itérations :
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    La commande retourne l’ID du travail soumis.
   
    Sur un cluster HPC Pack déployé sur les machines virtuelles Azure, modifiez la commande comme indiqué à l’étape 2.
5. Une fois le travail terminé, pour afficher le résultat (dans ce cas, la sortie de la tâche 1 du travail), tapez la commande suivante :
   
    ```Command
    task view <JobID>.1
    ```
   
   La sortie indiquera des résultats de débit semblables à ceci :
   
   ![Débit ping pong][pingpong2]

### <a name="mpi-application-considerations"></a>Considérations sur les applications MPI
Vous trouverez ci-dessous des considérations relatives à l’exécution d’applications MPI avec le HPC Pack dans Azure. Certaines s’appliquent uniquement aux déploiements de nœuds Azure (instances de rôle de travail ajoutées dans une configuration de type « intégration à Azure »).

* Les instances de rôle de travail dans un service cloud sont régulièrement réapprovisionnées sans préavis par Azure (par exemple, pour la maintenance du système ou en cas d’échec d’une instance). Si une instance est réapprovisionnée alors qu’elle exécute un travail MPI, elle perd ses données et retourne à l’état de son premier déploiement, ce qui peut entraîner l’échec du travail MPI. Plus vous utilisez de nœuds pour un même travail MPI et plus le travail sera long, plus la probabilité de réapprovisionnement de l’une des instances au cours d’un travail sera élevée. Prenez également ce point en considération si vous désignez un seul nœud dans le déploiement comme serveur de fichiers.
* Pour exécuter des travaux MPI dans Azure, vous n’avez pas besoin d’utiliser des instances compatibles RDMA. Vous pouvez utiliser n’importe quelle taille d’instance prise en charge par HPC Pack. Toutefois, les instances compatibles RDMA sont recommandées pour l’exécution de travaux MPI relativement volumineux qui sont sensibles à la latence et à la bande passante du réseau qui connecte les nœuds. Si vous utilisez d’autres tailles pour exécuter des travaux MPI pour lesquels la bande passante et la latence sont essentiels, nous vous recommandons d’exécuter de petits travaux où une tâche donnée s’exécute sur quelques nœuds seulement.
* Les applications déployées sur des instances Azure sont soumises aux termes du contrat de licence associés à l’application. Vérifiez auprès du fournisseur de toute application commerciale les questions de licence ou toute autre restriction relative à l’exécution dans le cloud. Tous les fournisseurs ne proposent pas le paiement à l'utilisation pour les licences.
* Les instances Azure nécessitent une configuration supplémentaire pour accéder aux nœuds locaux, partages et serveurs de licences. Par exemple, pour activer les nœuds Azure pour accéder à un serveur de licences local, vous pouvez configurer un réseau virtuel Azure de site à site.
* Pour exécuter des applications MPI sur des instances Azure, enregistrez chaque application MPI sur les instances avec le pare-feu Windows en exécutant la commande **hpcfwutil** . Cela permet aux communications MPI d’avoir lieu sur un port affecté dynamiquement par le pare-feu.
  
  > [!NOTE]
  > Pour des déploiements d’intégration à Azure, vous pouvez également configurer une commande d’exception de pare-feu pour qu’elle s’exécute automatiquement sur tous les nouveaux nœuds Azure ajoutés à votre cluster. Après avoir exécuté la commande **hpcfwutil** et vérifié que votre application fonctionne, ajoutez la commande à un script de démarrage pour vos nœuds Azure. Pour plus d'informations, consultez [Utiliser un script de démarrage pour les nœuds Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack utilise la variable d’environnement de cluster CCP_MPI_NETMASK pour spécifier une plage d’adresses acceptables pour la communication MPI. Depuis HPC Pack 2012 R2, la variable d’environnement de cluster CCP_MPI_NETMASK affecte uniquement la communication MPI entre les nœuds de calcul cluster appartenant à un domaine (localement ou sur des machines virtuelles Azure). La variable est ignorée par les nœuds ajoutés dans une configuration d’intégration à Azure.
* Les travaux MPI ne peuvent pas s’exécuter sur des instances Azure déployées dans différents services cloud (par exemple, dans des déploiements d’intégration à Azure avec différents modèles de nœud ou des nœuds de calcul dans des machines virtuelles Azure déployés dans plusieurs services cloud). Si vous avez plusieurs déploiements de nœuds Azure démarrés avec différents modèles de nœud, le travail MPI doit s’exécuter sur un seul ensemble de nœuds Azure.
* Lorsque vous ajoutez des nœuds Azure à votre cluster et que vous les mettez en ligne, le service de planification de travaux HPC essaie immédiatement de démarrer les travaux sur les nœuds. Si seulement une partie de votre charge de travail peut s’exécuter sur Azure, assurez-vous de mettre à jour ou de créer des modèles de projet pour définir les types de travaux qui peuvent s’exécuter sur Azure. Par exemple, pour vous assurer que les travaux soumis avec un modèle de travail s’exécutent uniquement sur des nœuds Azure, ajoutez la propriété Node Groups au modèle de travail et sélectionnez AzureNodes comme valeur requise. Pour créer des groupes personnalisés pour vos nœuds Azure, utilisez l'applet de commande Add-HpcGroup HPC PowerShell.

## <a name="next-steps"></a>Étapes suivantes
* Comme alternative à l'utilisation de HPC Pack, développez avec le service Azure Batch pour exécuter des applications MPI sur des pools gérés de nœuds de calcul dans Azure. Consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../../../batch/batch-mpi.md).
* Si vous souhaitez exécuter des applications MPI qui accèdent au réseau Azure RDMA, consultez [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
