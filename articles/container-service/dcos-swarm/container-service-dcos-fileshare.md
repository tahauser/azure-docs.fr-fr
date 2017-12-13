---
title: Partage de fichiers pour un cluster DC/OS Azure
description: "Créer et monter un partage de fichiers sur un cluster DC/OS dans Azure Container Service"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Créer et monter un partage de fichiers sur un cluster DC/OS

Ce didacticiel explique en détail comment créer un partage de fichiers dans Azure et comment le monter sur chaque agent et chaque maître du cluster DC/OS. La configuration d’un partage de fichiers facilite le partage de fichiers sur votre cluster, par exemple la configuration, l’accès, les journaux et bien plus encore. Dans ce didacticiel, les tâches suivantes sont effectuées :

> [!div class="checklist"]
> * Créer un compte de stockage Azure
> * Créer un partage de fichiers
> * Montage du partage sur votre cluster DC/OS

Vous avez besoin d’un cluster DC/OS ACS pour suivre les étapes de ce didacticiel. Le cas échéant, cet [exemple de script](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) peut en créer un pour vous.

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Créer un partage de fichiers sur Microsoft Azure

Avant d’utiliser un partage de fichiers Azure avec un cluster ACS DC/OS, vous devez créer le compte de stockage et le partage de fichiers. Exécutez le script suivant pour créer le stockage et le partage de fichiers. Mettez à jour les paramètres avec ceux de votre environnement.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Monter le partage sur votre cluster

Ensuite, le partage de fichiers doit être monté sur chaque machine virtuelle à l’intérieur de votre cluster. Cette tâche est effectuée à l’aide de l’outil/protocole cifs. Vous pouvez effectuer manuellement cette opération de montage sur chaque nœud du cluster, ou exécuter un script sur chacun d’eux.

Dans cet exemple, deux scripts sont exécutés, l’un pour monter le partage de fichiers Azure, l’autre pour exécuter ce script sur chaque nœud du cluster DC/OS.

Tout d’abord, vous devez connaître le nom du compte de stockage Azure et la clé d’accès. Pour récupérer ces informations, utilisez les commandes suivantes. Notez-les, car elles vont être utiles à une prochaine étape.

Nom du compte de stockage :

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Clé d’accès au compte de stockage :

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Récupérez ensuite le nom de domaine complet du maître DC/OS et stockez-le dans une variable.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Copiez votre clé privée dans le nœud principal. Cette clé est nécessaire pour créer une connexion ssh avec tous les nœuds du cluster. Si une valeur non définie par défaut a été utilisée lors de la création du cluster, mettez à jour le nom d’utilisateur. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Connectez-vous par SSH au maître (ou premier maître) de votre cluster DC/OS. Si une valeur non définie par défaut a été utilisée lors de la création du cluster, mettez à jour le nom d’utilisateur.

```azurecli-interactive
ssh azureuser@$FQDN
```

Créez un fichier nommé **cifsMount.sh** et copiez le contenu suivant dedans. 

Ce script est utilisé pour monter le partage de fichiers Azure. Mettez à jour les variables `STORAGE_ACCT_NAME` et `ACCESS_KEY` avec les informations recueillies précédemment.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Créez un deuxième fichier nommé **getNodesRunScript.sh** et copiez le contenu suivant dans le fichier. 

Ce script détecte tous les nœuds de cluster, puis exécute le script **cifsMount.sh** pour monter le partage de fichiers sur chacun d’eux.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Exécutez le script pour monter le partage de fichiers Azure sur tous les nœuds du cluster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Le partage de fichiers est désormais accessible sur `/mnt/share/dcosshare` dans chaque nœud du cluster.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, un partage de fichiers Azure a été rendu disponible sur un cluster DC/OS en suivant les étapes suivantes :

> [!div class="checklist"]
> * Créer un compte de stockage Azure
> * Créer un partage de fichiers
> * Monter le partage sur votre cluster DC/OS

Passez au didacticiel suivant pour en savoir plus sur l’intégration d’un service Azure Container Registry avec DC/OS dans Azure.  

> [!div class="nextstepaction"]
> [Équilibrer la charge des applications](container-service-dcos-acr.md)
