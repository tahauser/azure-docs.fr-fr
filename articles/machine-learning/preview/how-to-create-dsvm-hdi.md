---
title: "Comment créer une machine DSVM et un cluster HDI en tant que cibles de calcul pour Azure ML"
description: "Créez une machine DSVM et un cluster HDI Spark en tant que cibles de calcul pour l’expérimentation Azure ML."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 15cdee0fb3994874c88b16bebec35f5eae9f8de2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Créer une machine DSVM et un cluster HDI Spark en tant que cibles de calcul

Vous pouvez facilement faire monter en puissance votre expérience Machine Learning ou encore augmenter la taille des instances. Pour cela, il vous suffit d’ajouter des cibles de calcul, par exemple une machine DSVM (Data Science Virtual Machine) basée sur Ubuntu et un cluster Apache Spark pour Azure HDInsight. Cet article aborde les étapes nécessaires à la création de ces cibles de calcul dans Azure. Pour plus d’informations sur les cibles de calcul Azure ML, consultez [Présentation du service d’expérimentation Azure Machine Learning](experimentation-service-configuration.md).

>[!NOTE]
>Avant de continuer, vous devez disposer des autorisations nécessaires pour créer des ressources telles que des clusters de machines virtuelles et HDI dans Azure. De même, selon votre configuration, sachez que ces deux ressources peuvent utiliser de nombreux cœurs de calcul. Assurez-vous que votre abonnement a une capacité suffisante pour les cœurs de processeurs virtuels. Vous pouvez toujours contacter le support Azure pour augmenter le nombre maximal de cœurs autorisé dans votre abonnement.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Créer une machine DSVM Ubuntu dans le portail Azure

Vous pouvez créer une machine DSVM à partir du portail Azure. 

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Cliquez sur le lien **+Nouveau**, puis recherchez « data science virtual machine for Linux ».
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Choisissez **Data Science Virtual Machine for Linux (Ubuntu)** dans la liste, puis suivez les instructions à l’écran pour créer la machine DSVM.

>[!IMPORTANT]
>Assurez-vous de bien choisir **Mot de passe** comme _type d’authentification_.

![utiliser le mot de passe](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Créer une machine DSVM Ubuntu à l’aide de l’interface de ligne de commande Azure

Vous pouvez également utiliser un modèle de gestion des ressources Azure pour déployer une machine DSVM.

>[!NOTE]
>Nous partons du principe que toutes les commandes suivantes sont émises à partir du dossier racine d’un projet Azure ML.

Commencez par créer un fichier `mydsvm.json` dans votre éditeur de texte, dans le dossier `docs`. (Si aucun dossier `docs` ne figure dans le dossier racine du projet, créez-en un.) Nous utilisons ce fichier pour configurer certains paramètres de base pour le modèle de gestion des ressources Azure. 

Copiez et collez l’extrait de code JSON suivant dans le fichier `mydsvm.json`, puis renseignez les valeurs demandées :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Dans le champ _vmSize_, vous pouvez utiliser une taille de machine virtuelle prise en charge au choix, telle qu’indiquée dans le [modèle de gestion des ressources Azure DSVM Ubuntu](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Nous vous recommandons d’utiliser comme cible de calcul pour Azure ML l’une des tailles ci-dessous. 


>[!TIP]
> Pour [un apprentissage approfondi des charges de travail](how-to-use-gpu.md), vous pouvez déployer sur des machines virtuelles alimentées par GPU.

- [Machines virtuelles à usage général](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [Machines virtuelles alimentées par GPU](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

Apprenez-en davantage sur les [tailles des machines virtuelles Linux dans Azure](../../virtual-machines/linux/sizes.md) et sur leur [tarification](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Ouvrez la fenêtre de l’interface de ligne de commande depuis l’application Azure ML Workbench en cliquant sur **Fichier** --> **Ouvrir l’invite de commandes** ou l’élément de menu **Ouvrir PowerShell**. 

>[!NOTE]
>Vous pouvez également le faire dans n’importe quel environnement de ligne de commande dès lors que la commande az-cli est installée.

Dans la fenêtre de ligne de commande, entrez les commandes suivantes :

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Joindre une cible de calcul DSVM
Une fois la machine DSVM créée, vous pouvez l’attacher à votre projet Azure ML.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Normalement, vous êtes maintenant prêt à exécuter des expériences sur cette machine DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Libérer une machine DSVM et la redémarrer ultérieurement
Lorsque vous avez finalisé les tâches de calcul à partir d’Azure ML, vous pouvez libérer la machine DSVM. Cette action arrête la machine virtuelle, libère les ressources de calcul, mais conserve les disques virtuels. Les coûts de calcul ne vous sont pas facturés lorsque la machine virtuelle est libérée.

Pour libérer une machine virtuelle :

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Pour ramener à la vie la machine virtuelle, utilisez la commande `az ml start` :

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Développer le disque de système d’exploitation DSVM
La machine virtuelle Linux dans Azure est habituellement dotée d’un disque de système d’exploitation de 30 Go. Lorsqu’elle est utilisée comme cible de calcul pour Azure ML, cette capacité peut rapidement être consommée par le moteur Docker, qui extrait des images Docker et crée des couches conda par-dessus. Il est judicieux pour développer le disque de système d’exploitation et d’accroître sa taille (par exemple, 200 Go) pour éviter une erreur du type « disque plein » au cours de l’exécution. Pour savoir comment y parvenir facilement depuis azure-cli, voir [Comment développer des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](../../virtual-machines/linux/expand-disks.md). 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Créer un cluster Apache Spark pour Azure HDInsight dans le portail Azure

Pour exécuter des travaux Spark de montée en puissance, vous devez créer un cluster Apache Spark pour Azure HDInsight dans le portail Azure.

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Cliquez sur le lien **+Nouveau** et recherchez « HDInsight ».

    ![trouver hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Choisissez **HDInsight** dans la liste, puis cliquez sur le bouton **Créer**.
4. Dans l’écran de configuration **Notions de base**, paramètres **Type de Cluster**, assurez-vous de choisir **Spark** en tant que _Type de cluster_, **Linux** en tant que _Système d’exploitation_ et **Spark 2.1.0 (HDI 3.6)** en tant que _Version.

    ![configurer hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Notez dans l’écran ci-dessus que le cluster possède un champ _Nom d’utilisateur de connexion du cluster_ et un champ _Nom d’utilisateur SSH (Secure Shell)_. Il s’agit de deux identités d’utilisateur différentes, bien que pour des raisons pratiques, vous pouvez spécifier le même mot de passe pour ces deux connexions. Le champ _Nom d’utilisateur de connexion du cluster_ est utilisé pour se connecter à l’interface utilisateur web de gestion du cluster HDI. Le champ _Nom d’utilisateur SSH (Secure Shell)_ est utilisé pour se connecter au nœud principal du cluster, ce qui est demandé afin qu’Azure ML puisse distribuer les travaux Spark.

5. Choisissez les tailles de cluster et de nœud dont vous avez besoin, puis terminez les étapes de l’Assistant de création. Jusqu’à 30 minutes sont parfois nécessaires au cluster pour finaliser la configuration. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Joindre un cluster HDI Spark en tant que cible de calcul

Une fois le cluster HDI Spark créé, vous pouvez l’attacher à votre projet Azure ML.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Vous devez maintenant être prêt à exécuter des expériences sur ce cluster Spark.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :
- [Présentation du service d’expérimentation Azure Machine Learning](experimentation-service-configuration.md)
- [Fichiers de configuration du service d’expérimentation Azure Machine Learning Workbench](experimentation-service-configuration-reference.md)
- [Cluster Apache Spark pour Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (Machine virtuelle Science des données)
