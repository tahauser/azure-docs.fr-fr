---
title: "Installer MongoDB sur une machine virtuelle Linux à l’aide d’Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment installer et configurer MongoDB sur une machine virtuelle Linux dans Azure à l’aide du modèle de déploiement de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: ff9e23de41245ea21ba6e9c3efe13ca13b0b0ae1
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Guide pratique d’installation et de configuration de MongoDB sur une machine virtuelle Linux avec Azure CLI 1.0
[MongoDB](http://www.mongodb.org) est une base de données NoSQL open-source qui offre des performances élevées. Cet article vous montre comment installer et configurer MongoDB sur une machine virtuelle Linux dans Azure à l’aide du modèle de déploiement de Resource Manager. Présentations d’exemples détaillant comment :

* [Installer et configurer une instance MongoDB de base manuellement](#manually-install-and-configure-mongodb-on-a-vm)
* [Création d'une instance MongoDB de base à l'aide d'un modèle Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Créer un cluster partitionné MongoDB complexe avec jeux de réplicas à l’aide d’un modèle Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- Azure CLI 1.0 : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](create-cli-complete-nodejs.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installer et configurer MongoDB manuellement sur une machine virtuelle
MongoDB [propose des instructions d’installation](https://docs.mongodb.com/manual/administration/install-on-linux/) pour les distributions Linux, notamment Red Hat / CentOS, SUSE, Ubuntu et Debian. L’exemple suivant crée une machine virtuelle *CentOS* à l’aide d’une clé SSH stockée sur *~/.ssh/id_rsa.pub*. Répondez aux invites pour le nom de compte de stockage, le nom DNS et les informations d’identification d’administrateur :

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Connectez-vous à la machine virtuelle à l’aide de l’adresse IP publique affichée à la fin de l’étape de création de machine virtuelle précédente :

```bash
ssh azureuser@40.78.23.145
```

Pour ajouter les sources d’installation pour MongoDB, créez un fichier de référentiel **yum** comme suit :

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Ouvrez le fichier de référentiel MongoDB à modifier. Ajoutez les lignes suivantes :

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Installez MongoDB à l’aide de **yum** comme suit :

```bash
sudo yum install -y mongodb-org
```

Par défaut, SELinux est appliqué sur les images CentOS, ce qui vous empêche d’accéder à MongoDB. Installez les outils de gestion de stratégie et configurez SELinux afin d’autoriser MongoDB fonctionner sur le port TCP 27017 par défaut, comme suit. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Démarrez le service MongoDB comme suit :

```bash
sudo service mongod start
```

Vérifiez l’installation de MongoDB en vous connectant à l’aide du client `mongo` local :

```bash
mongo
```

Testez maintenant l’instance MongoDB en ajoutant des données, puis en recherchant :

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Si vous le souhaitez, configurez MongoDB pour démarrer automatiquement lors du redémarrage du système :

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Créeation d’une instance MongoDB de base sur CentOS à l’aide d’un modèle
Vous pouvez créer une instance MongoDB de base sur une machine virtuelle CentOS unique en utilisant le modèle de démarrage rapide Azure suivant à partir de GitHub. Ce modèle utilise l’extension de script personnalisé pour Linux pour ajouter un référentiel `yum` à votre nouvelle machine virtuelle CentOS, puis installer MongoDB.

* [Instance MongoDB de base sur CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans la région `eastus`. Saisissez vos propres valeurs, comme suit :

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> L’interface de ligne de commande Azure vous renvoie à une invite quelques secondes après la création du déploiement, mais l’installation et la configuration prennent quelques minutes. Vérifiez l’état du déploiement avec `azure group deployment show myResourceGroup`, en saisissant le nom de votre groupe de ressources. Attendez que **ProvisioningState** (État d’approvisionnement) indique *Succeeded* (Opération réussie) avant d’essayer d’utiliser SSH pour la machine virtuelle.

Une fois le déploiement terminé, connectez-vous par SSH à la machine virtuelle. Obtenez l’adresse IP de votre machine virtuelle à l’aide de la commande `azure vm show` comme dans l’exemple suivant :

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Vers la fin de la sortie, l’adresse IP publique s’affiche. Connectez-vous par SSH à votre machine virtuelle avec l’adresse IP de votre machine virtuelle :

```bash
ssh azureuser@138.91.149.74
```

Vérifiez l’installation de MongoDB en vous connectant à l’aide du client `mongo` local, comme suit :

```bash
mongo
```

Testez maintenant l’instance en ajoutant des données, puis en recherchant comme suit :

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Création d’un cluster partitionné MongoDB complexe sur CentOS à l’aide d’un modèle
Vous pouvez créer une instance MongoDB complexe sur un cluster partitionné en utilisant le modèle de démarrage rapide Azure suivant à partir de GitHub. Ce modèle suit les [meilleures pratiques pour les clusters partitionnés MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) pour fournir la redondance et la haute disponibilité. Le modèle crée deux partitions, avec trois nœuds dans chaque jeu de réplicas. Un jeu de réplicas de serveur de configuration avec trois nœuds est également créé, ainsi que deux serveurs de routeur **mongos** pour assurer la cohérence des applications au sein des partitions.

* [Cluster de partitionnement MongoDB sur CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Le déploiement de ce cluster partitionné MongoDB complexe requiert plus de 20 processeurs virtuels, ce qui est généralement le nombre de processeurs virtuels par défaut par région pour un abonnement. Ouvrez une demande de support Azure pour augmenter votre nombre de processeurs virtuels.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région *eastus*. Saisissez vos propres valeurs, comme suit :

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> L’interface de ligne de commande Azure vous renvoie à une invite quelques secondes après la création du déploiement, mais l’installation et la configuration peuvent prendre plus d’une heure. Vérifiez l’état du déploiement avec `azure group deployment show myResourceGroup`, en ajustant le nom de votre groupe de ressources. Attendez que **ProvisioningState** (État d’approvisionnement) indique *Succeeded* (Opération réussie) avant de vous connecter aux machines virtuelles.


## <a name="next-steps"></a>Étapes suivantes
Dans ces exemples, vous vous connectez à l’instance MongoDB localement à partir de la machine virtuelle. Si vous souhaitez vous connecter à l’instance MongoDB à partir d’une autre machine virtuelle ou d’un autre réseau, vérifiez que les bonnes [règles de groupe de sécurité réseau sont créées](nsg-quickstart.md).

Pour en savoir plus sur la création avec des modèles, voir [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Les modèles Azure Resource Manager utilisent l’extension de script personnalisé pour télécharger et exécuter des scripts sur vos machines virtuelles. Pour plus d’informations, consultez [Utilisation de l’extension de script personnalisé Azure avec des machines virtuelles Linux](extensions-customscript.md).

