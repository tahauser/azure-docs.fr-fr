---
title: Utilisation de Docker Compose sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: "Procédure d’utilisation de Docker et Compose sur des machines virtuelles Linux avec l’interface CLI Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 387f3095e8eebce3fa6c2f47ffc87995e65bfe2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples dans Azure
Avec [Compose](http://github.com/docker/compose), un fichier texte simple vous permet de définir une application composée de plusieurs conteneurs Docker. Vous faites ensuite tourner votre application dans une seule commande qui fait tout pour déployer votre environnement défini. À titre d’exemple, cet article vous explique comment configurer rapidement un blog WordPress avec une base de données SQL MariaDB de type backend sur une machine virtuelle Ubuntu. Vous pouvez également utiliser Compose pour configurer des applications plus complexes.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Configurer une machine virtuelle Linux en tant qu’hôte Docker
La Place de marché Azure propose différentes procédures Azure et des images ou des modèles Resource Manager disponibles pour la création d’une machine virtuelle Linux et sa configuration en tant qu’hôte Docker. Par exemple, la page [Utilisation de l’extension de machine virtuelle Docker pour déployer votre environnement](dockerextension.md) contient une procédure rapide relative à la création d’une machine virtuelle Ubuntu avec l’extension de machine virtuelle Docker à l’aide d’un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Si vous utilisez l’extension de machine virtuelle Docker, votre machine virtuelle est automatiquement configurée en tant que hôte Docker et Compose est déjà installé.


### <a name="create-docker-host-with-azure-cli-20"></a>Créer un hôte Docker avec Azure CLI 2.0
Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Tout d’abord, créez un groupe de ressources pour votre environnement Docker avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Ensuite, déployez une machine virtuelle avec la commande [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) qui inclut l’extension de machine virtuelle Azure Docker de [ce modèle Azure Resource Manager sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). À l’invite, renseignez vos propres valeurs uniques pour *newStorageAccountName*, *adminUsername*, *adminPassword* et *dnsNameForPublicIP* :

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Le déploiement prend quelques minutes.


## <a name="verify-that-compose-is-installed"></a>Vérifier que Compose est installé
Pour afficher les détails de votre machine virtuelle, y compris le nom DNS, utilisez [az vm show](/cli/azure/vm#az_vm_show) :

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH pour votre nouvel hôte Docker. Indiquez votre propre nom d’utilisateur et le nom DNS à partir de la procédure précédente :

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Pour vérifier que Compose est installé sur la machine virtuelle, exécutez la commande suivante :

```bash
docker-compose --version
```

Vous devriez voir une sortie similaire à *docker-composer 1.6.2, build 4d72027*.

> [!TIP]
> Si vous avez utilisé une autre méthode pour créer un hôte Docker et devez installer Compose vous-même, consultez la [Documentation Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Créer un fichier de configuration docker-compose.yml
À présent, créez un fichier `docker-compose.yml` , qui est simplement un fichier texte de configuration, pour définir les conteneurs Docker s’exécutant sur la machine virtuelle. Le fichier indique l’image à exécuter sur chaque conteneur (il peut également s’agir d’un build d’un fichier Dockerfile), les variables d’environnement et les dépendances nécessaires, les ports et les liens entre les conteneurs. Pour plus d’informations sur la syntaxe du fichier yml, consultez [Référence du fichier Compose](https://docs.docker.com/compose/compose-file/).

Créez un fichier *docker-compose.yml*. Utilisez votre éditeur de texte favori pour ajouter d’autres données au fichier. L’exemple suivant crée le fichier avec une invite de commandes pour `sensible-editor` pour sélectionner un éditeur que vous souhaitez utiliser :

```bash
sensible-editor docker-compose.yml
```

Collez l’exemple suivant dans votre fichier Docker Compose. Cette configuration utilise des images du [registre DockerHub](https://registry.hub.docker.com/_/wordpress/) pour installer WordPress (le système open source de gestion de blogs et de contenu) et une base de données principale associée SQL MariaDB. Entrez votre propre *MYSQL_ROOT_PASSWORD* comme suit :

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Démarrer les conteneurs avec Compose
Dans le même répertoire que votre fichier *docker-compose.yml*, exécutez la commande suivante (selon votre environnement, vous devrez peut-être exécuter `docker-compose` à l’aide de `sudo`) :

```bash
docker-compose up -d
```

Cette commande démarre les conteneurs Docker spécifiés dans *docker-compose.yml*. L’exécution de cette étape peut prendre une minute ou deux. Vous voyez une sortie similaire à l’exemple suivant :

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Veillez à utiliser l’option **-d** au démarrage, de manière à ce que les conteneurs s’exécutent en continu en arrière-plan.


Pour vérifier que les contrôleurs sont en cours d’exécution, tapez `docker-compose ps`. Le résultat suivant doit s’afficher :

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Vous pouvez maintenant vous connecter directement à WordPress sur la machine virtuelle sur le port 80. Ouvrez un navigateur Web et entrez le nom DNS de votre machine virtuelle (par exemple `http://mypublicdns.eastus.cloudapp.azure.com`). L’écran de démarrage de WordPress, vous permettant de terminer l’installation et de prendre en main l’application, doit maintenant s’afficher.

![Écran d’accueil WordPress][wordpress_start]

## <a name="next-steps"></a>Étapes suivantes
* Pour accéder à d’autres options de configuration de Docker et Compose dans votre machine virtuelle Docker, consultez le [Guide de l’utilisateur de l’extension de machine virtuelle Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) . Par exemple, une option consiste à placer un fichier yml Compose (converti au format JSON) directement dans la configuration de l’extension de machine virtuelle Docker.
* Pour plus d’exemples de développement et de déploiement d’applications à plusieurs conteneurs, consultez la [Référence de la ligne de commande Compose](http://docs.docker.com/compose/reference/) et le [Guide d’utilisation](http://docs.docker.com/compose/).
* Utilisez un modèle Microsoft Azure Manager, le vôtre ou un élément de la [communauté](https://azure.microsoft.com/documentation/templates/), pour déployer une machine virtuelle Microsoft Azure avec Docker et une application configurée avec Compose. Par exemple, le modèle [Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) (en anglais) utilise Docker et Compose pour déployer rapidement WordPress avec un serveur principal MySQL sur une machine virtuelle Ubuntu.
* Essayez d’intégrer Docker Compose à un cluster Docker Swarm. Pour examiner des scénarios, consultez [Utilisation de Compose avec Swarm](https://docs.docker.com/compose/swarm/) .

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
