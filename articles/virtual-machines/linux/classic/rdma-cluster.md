---
title: "Configuration d’un cluster Linux RDMA pour exécuter des applications MPI | Microsoft Docs"
description: "Créer un cluster Linux de machines virtuelles de taille H16r, H16mr, A8 ou A9 afin d’utiliser le réseau RDMA Azure pour exécuter des applications MPI"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: 428dc1e8ba03ba17e348a33a33b5cf5e6118a43c
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configuration d’un cluster Linux RDMA pour exécuter des applications MPI
Découvrez comment configurer un cluster RDMA Linux dans Azure avec des [tailles de machines virtuelles de calcul haute performance](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour exécuter des applications MPI (Message Passing Interface) parallèles. Cet article explique comment préparer une image Linux HPC pour exécuter Intel MPI sur un cluster. Après la préparation, vous déployez un cluster de machines virtuelles à l’aide de cette image et d’une des tailles de machine virtuelle Azure prenant en charge RDMA (actuellement H16r, H16mr, A8 ou A9). Utilisez le cluster pour exécuter des applications MPI communiquant efficacement avec un réseau haut débit basé sur la technologie d’accès direct à la mémoire à distance (RDMA) à faible latence.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager](../../../resource-manager-deployment-model.md) et classique. Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## <a name="cluster-deployment-options"></a>Options de déploiement de cluster
Voici des méthodes que vous pouvez utiliser pour créer un cluster Linux RDMA avec ou sans planificateur de tâches.

* **Scripts de l’interface de ligne de commande Azure** : comme indiqué plus loin dans cet article, utilisez la [CLI Azure](../../../cli-install-nodejs.md) pour créer un script de déploiement d’un cluster de machines virtuelles prenant en charge RDMA. La CLI en mode Service Management crée les nœuds de cluster en série dans le modèle de déploiement classique. Le déploiement d’un grand nombre de nœuds de calcul peut donc prendre plusieurs minutes. Pour activer la connexion réseau RDMA lorsque vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud.
* **Modèles Azure Resource Manager** : vous pouvez aussi utiliser le modèle de déploiement Resource Manager pour déployer un cluster de machines virtuelles prenant en charge RDMA, qui se connecte au réseau RDMA. Vous pouvez [créer votre propre modèle](../../../resource-group-authoring-templates.md) ou consulter la page [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) pour trouver des modèles fournis par Microsoft ou la communauté, pour déployer la solution de votre choix. Les modèles Resource Manager peuvent fournir un moyen rapide et fiable pour déployer un cluster Linux. Pour activer la connexion réseau RDMA lorsque vous utilisez le modèle de déploiement Resource Manager, déployez les machines virtuelles dans le même groupe à haute disponibilité.
* **HPC Pack** : créez un cluster Microsoft HPC Pack dans Azure, et ajoutez des nœuds de calcul prenant en charge RDMA, qui exécutent des distributions Linux prises en charge pour accéder au réseau RDMA. Pour plus d’informations, consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Exemple d’étapes de déploiement dans le modèle classique
Les étapes suivantes montrent comment utiliser la CLI Azure pour déployer une machine virtuelle HPC SUSE Linux Enterprise Server (SLES) 12 SP1 à partir de la Place de marché Azure, la personnaliser, et créer une image de machine virtuelle personnalisée. Vous pouvez ensuite utiliser l’image pour créer un script de déploiement d’un cluster de machines virtuelles prenant en charge RDMA.

> [!TIP]
> Utilisez une procédure similaire pour déployer un cluster de machines virtuelles prenant en charge RDMA sur des images HPC basées sur CentOS dans la Place de marché Azure. Certaines étapes diffèrent légèrement, comme indiqué. 
>
>

### <a name="prerequisites"></a>Prérequis
* **Ordinateur client** : vous avez besoin d’un ordinateur client Mac, Linux ou Windows pour communiquer avec Azure. Ces étapes supposent que vous utilisez un client Linux.
* **Abonnement Azure** : si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes. Pour les clusters de grande taille, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat.
* **Disponibilité de taille de machine virtuelle** : les tailles d’instance prenant en charge RDMA sont les suivantes : H16r, H16mr, A8 et A9. Pour connaître la disponibilité dans les différentes régions Azure, voir [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/) .
* **Quota de cœurs** : il se peut que vous deviez augmenter le quota de cœurs pour déployer un cluster de machines virtuelles nécessitant beaucoup de ressources système. Par exemple, vous devez avoir au moins 128 cœurs si vous souhaitez déployer 8 machines virtuelles A9, comme indiqué dans cet article. Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer dans certaines familles de taille de machine virtuelle, dont la série H. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../../../azure-supportability/how-to-create-azure-support-request.md) gratuitement.
* **CLI Azure** : [installez](../../../cli-install-nodejs.md) la CLI Azure et [connectez-vous à votre abonnement Azure](/cli/azure/authenticate-azure-cli) sur l’ordinateur client.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Configurer une machine virtuelle SLES 12 SP1 HPC
Après la connexion à Azure avec la CLI Azure, exécutez la commande `azure config list` pour confirmer que la sortie affiche le mode Service Management. Si ce n’est pas le cas, définissez le mode en exécutant la commande suivante :

    azure config mode asm


Tapez la commande suivante pour répertorier tous les abonnements que vous êtes autorisé à utiliser :

    azure account list

L’abonnement actif actuel est identifié par `Current` avec la valeur `true`. S’il ne s’agit pas de l’abonnement que vous souhaitez utiliser pour créer le cluster, définissez l’ID d’abonnement approprié comme abonnement actif :

    azure account set <subscription-Id>

Pour afficher les images de SLES 12 SP1 HPC publiquement disponibles dans Azure, exécutez une commande comme celle qui suit, si votre environnement d’interpréteur de commandes prend en charge **grep** :

    azure vm image list | grep "suse.*hpc"

Configurez une machine virtuelle prenant en charge RDMA avec une image de SLES 12 SP1 HPC en exécutant une commande comme la suivante :

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Où :

* La taille (A9 dans cet exemple) est l’une des tailles de machine virtuelle prenant en charge RDMA.
* Le numéro de port SSH externe (22 dans cet exemple, qui est le SHH par défaut) est un numéro de port valide. Le numéro de port interne SSH est défini sur 22.
* Un service cloud est créé dans la région Azure spécifiée par l’emplacement. Spécifiez un emplacement dans lequel la taille de machine virtuelle que vous choisissez est disponible.
* Pour la prise en charge de la priorité SUSE (des frais supplémentaires s’appliquent), le nom de l’image de SLES 12 SP1 peut actuellement être conforme à une de ces deux options : 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Personnalisation de la machine virtuelle
Après l’approvisionnement de la machine virtuelle, utilisez SSH pour vous connecter à la machine virtuelle à l’aide de l’adresse IP externe de la machine virtuelle (ou du nom DNS) et du numéro de port externe configuré et personnalisez-la. Pour plus d’informations sur la connexion, consultez [Connexion à une machine virtuelle exécutant Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Exécutez les commandes comme l’utilisateur que vous avez configuré sur la machine virtuelle, à moins qu’un accès racine soit nécessaire pour accomplir une étape.

> [!IMPORTANT]
> Microsoft Azure ne fournit pas d'accès racine aux machines virtuelles Linux. Pour obtenir un accès administratif lorsque vous êtes connecté en tant qu’utilisateur à la machine virtuelle, exécutez les commandes avec `sudo`.
>
>

* **Mises à jour** : installez les mises à jour à l’aide de zypper. Vous pouvez également installer les utilitaires NFS.

  > [!IMPORTANT]
  > Dans une machine virtuelle SLES 12 SP1 HPC, nous vous recommandons de ne pas appliquer les mises à jour du noyau, qui peuvent provoquer des problèmes avec les pilotes RDMA Linux.
  >
  >
* **Intel MPI** : terminez l’installation d’Intel MPI sur la machine virtuelle SLES 12 SP1 HPC en exécutant la commande suivante :

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Verrouiller la mémoire** : pour que les codes MPI verrouillent la mémoire disponible pour RDMA, ajoutez ou modifiez les paramètres suivants dans le fichier /etc/security/limits.conf. Un accès racine est requis pour modifier ce fichier.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > À des fins de test, vous pouvez également définir memlock comme illimité. Par exemple : `<User or group name>    hard    memlock unlimited`. Pour plus d’informations, voir les [meilleures méthodes connues pour définir la taille de mémoire verrouillée](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **Clés SSH pour machines virtuelles SLES** : générez des clés SSH pour établir l’approbation de votre compte utilisateur parmi les nœuds de calcul du cluster SLES lors de l’exécution de travaux MPI. Si vous avez déployé une machine virtuelle HPC basée sur CentOS, ne suivez pas cette étape. Consultez les instructions de la suite de cet article pour définir une confiance SSH sans mot de passe entre les nœuds du cluster une fois que vous avez capturé l’image et déployé le cluster.

    Pour créer des clés SSH, exécutez la commande suivante. Lorsque vous y êtes invité, appuyez sur **Entrée** pour générer les clés dans l’emplacement par défaut sans définir de mot de passe.

        ssh-keygen

    Ajoutez la clé publique au fichier authorized_keys pour les clés publiques connues.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Dans l’annuaire ~/.ssh, modifiez ou créez le fichier de configuration. Fournissez la plage d’adresses IP du réseau privé que vous prévoyez d’utiliser dans Azure (10.32.0.0/16 dans cet exemple) :

        host 10.32.0.*
        StrictHostKeyChecking no

    Vous pouvez également répertorier l'adresse IP du réseau privé de chaque machine virtuelle dans votre cluster comme suit :

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > La configuration de `StrictHostKeyChecking no` peut créer un risque de sécurité potentiel si une adresse IP ou une plage d’adresses IP spécifiques ne sont pas spécifiées.
  >
  >
* **Applications** : installez les applications dont vous avez besoin ou effectuez d’autres personnalisations avant de capturer l’image.

### <a name="capture-the-image"></a>capture de l’image
Pour capturer l’image, exécutez d’abord la commande suivante sur la machine virtuelle Linux. Cette commande désaffecte la machine virtuelle, mais conserve les comptes utilisateurs et les clés SSH que vous avez configurés.

```
sudo waagent -deprovision
```

Depuis votre ordinateur client, exécutez les commandes CLI Azure suivantes pour capturer l’image. Pour plus d’informations, consultez la page [Capture d’une machine virtuelle Linux classique en tant qu’image](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Après avoir exécuté ces commandes, l'image de machine virtuelle est capturée pour que vous puissiez l'utiliser et la machine virtuelle est supprimée. Vous disposez maintenant d’une image personnalisée prête à être déployée sur un cluster.

### <a name="deploy-a-cluster-with-the-image"></a>Déploiement d’un cluster avec l'image
Modifiez le script Bash suivant avec les valeurs appropriées pour votre environnement et exécutez-le à partir de votre ordinateur client. Dans la mesure où Azure déploie les machines virtuelles en série dans le modèle de déploiement Service Management, quelques minutes sont nécessaires pour déployer les huit machines virtuelles A9 suggérées dans ce script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considérations pour un cluster HPC CentOS
Si vous souhaitez configurer un cluster basé sur l’une des images HPC basées sur CentOS dans Azure Marketplace au lieu de SLES 12 pour HPC, suivez les étapes générales de la section précédente. Notez les différences suivantes lorsque vous approvisionnez et configurez la machine virtuelle :

- Intel MPI est déjà installé sur une machine virtuelle approvisionnée à partir d’une image de HPC basée sur CentOS.
- Les paramètres de verrouillage de mémoire sont déjà ajoutés dans le fichier /etc/security/limits.conf de la machine virtuelle.
- Ne générez pas de clés SSH sur la machine virtuelle que vous approvisionnez pour la capture. Au lieu de cela, nous vous recommandons de configurer une authentification basée sur l’utilisateur après le déploiement du cluster. Pour plus d’informations, consultez la section suivante :  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurer une approbation SSH sans mot de passe sur le cluster
Sur un cluster HPC basé sur CentOS, deux méthodes permettent d’établir une approbation entre les nœuds de calcul : l’authentification basée sur l’hôte et l’authentification basée sur l’utilisateur. L’authentification basée sur l’hôte n’est pas expliquée dans cet article. Elle doit généralement être configurée à l’aide d’un script d’extension pendant le déploiement. L’authentification basée sur l’utilisateur est pratique pour établir une confiance après le déploiement, et nécessite la génération et le partage de clés SSH entre les nœuds de calcul dans le cluster. Cette méthode est communément appelée connexion SSH sans mot de passe. Elle est requise lors de l’exécution de travaux MPI.

Un exemple de script fourni par la communauté est disponible sur [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) pour permettre une authentification utilisateur simple sur un cluster HPC basé sur CentOS. Vous pouvez télécharger et utiliser ce script en procédant comme suit. Vous pouvez également modifier ce script ou utiliser toute autre méthode permettant d’établir une authentification SSH sans mot de passe entre les nœuds de calcul du cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

Pour exécuter le script, vous devez connaître le préfixe des adresses IP de votre sous-réseau. Vous pouvez obtenir ces informations en exécutant la commande suivante sur l’un des nœuds du cluster. Votre résultat doit ressembler à 10.1.3.5, et le préfixe est 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Ensuite, exécutez le script à l’aide de trois paramètres : le nom d’utilisateur courant utilisé sur les nœuds de calcul, le mot de passe pour cet utilisateur sur les nœuds de calcul et le préfixe de sous-réseau renvoyé par la commande précédente.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Ce script effectue les opérations suivantes :

* Crée sur le nœud hôte un répertoire nommé .ssh, qui est requis pour la connexion sans mot de passe.
* Crée un fichier de configuration dans le répertoire .ssh, qui indique à la méthode de connexion sans mot de passe d’autoriser la connexion à partir de n’importe quel nœud du cluster.
* Crée des fichiers contenant les noms et adresses IP de chacun des nœuds du cluster. Ces fichiers sont conservés après l’exécution du script à des fins de référence ultérieure.
* Crée une paire de clés privée et publique pour chaque nœud du cluster, (y compris le nœud hôte), et crée des entrées dans le fichier authorized_keys.

> [!WARNING]
> L’exécution de ce script peut créer un risque de sécurité potentiel. Assurez-vous que les informations de clé publique dans ~/.ssh ne sont pas distribuées.
>
>

## <a name="configure-intel-mpi"></a>Configurer Intel MPI
Pour exécuter des applications MPI sur RDMA Azure Linux, vous devez configurer certaines variables d'environnement spécifiques à Intel MPI. Voici un exemple de script bash pour configurer les variables nécessaires à l’exécution d’une application. Modifiez le chemin d’accès à mpivars.sh selon les besoins de votre installation d’Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Le format du fichier hôte est le suivant. Ajoutez une ligne pour chaque nœud de votre cluster. Spécifiez des adresses IP privées à partir du réseau virtuel défini précédemment, pas des noms DNS. Par exemple, pour deux hôtes avec les adresses IP 10.32.0.1 et 10.32.0.2, le fichier contient les éléments suivants :

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Exécuter MPI sur un cluster à deux nœuds de base
Si ce n'est pas déjà fait, commencez par configurer l'environnement pour Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Exécution d’une commande MPI
Exécutez une commande MPI sur l’un des nœuds de calcul pour montrer que MPI est installé correctement et peut communiquer entre au moins deux nœuds de calcul. La commande **mpirun** suivante exécute la commande **hostname** sur deux nœuds.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Votre sortie doit répertorier les noms de tous les nœuds que vous avez transmis comme entrée pour `-hosts`. Par exemple, une commande **mpirun** avec deux nœuds renvoie un résultat comme le suivant :

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Exécution d'un test d'évaluation MPI
La commande Intel MPI suivante vérifie la configuration du cluster et sa connexion au réseau RDMA à l’aide d’un banc d’essai pingpong.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Sur un cluster opérationnel à deux nœuds, vous devez voir une sortie comme ce qui suit. Sur le réseau RDMA Azure, vous devez vous attendre à une latence égale ou inférieure à 3 microsecondes pour les tailles de messages jusqu’à 512 octets.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>étapes suivantes
* Déploiement et exécution de vos applications MPI Linux sur votre cluster Linux.
* Consultez la [documentation de la bibliothèque Intel MPI](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) pour obtenir des conseils sur Intel MPI.
* Essayez un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) pour créer un cluster Intel Lustre en utilisant une image HPC basée sur CentOS. Pour plus d’informations, consultez [Déploiement d’Intel Cloud Edition pour Lustre sur Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
