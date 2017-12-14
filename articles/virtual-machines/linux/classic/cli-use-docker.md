---
title: "Utilisation de l’extension Docker VM pour Linux sur Azure"
description: "Décrit Docker et les extensions Microsoft Azure Virtual Machines, et illustre la création par programme de machines virtuelles sur Microsoft Azure qui sont des hôtes Docker à partir de la ligne de commande en utilisant l’interface de ligne de commande Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: b276911ecbbf161cb6068c1af7a035850035b98d
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Microsoft Azure
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur l’utilisation de l’extension Docker VM avec le modèle Resource Manager, suivez [ce lien](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cette rubrique décrit la création d’une machine virtuelle avec l’extension Docker VM, en mode Azure Service Management (ASM) à partir de l’interface de ligne de commande Azure sur toute plateforme. [Docker](https://www.docker.com/) fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Vous pouvez utiliser l’extension Docker VM et [l’agent Linux Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure. Pour une discussion sur les conteneurs et leurs avantages, consultez le [Tableau blanc Docker de haut niveau](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Utilisation de l'extension Docker VM avec Azure
Pour utiliser l’extension Docker VM avec Azure, vous devez installer une version de [l’interface de ligne de commande Azure](https://github.com/Azure/azure-sdk-tools-xplat) supérieure à 0.8.6 (lors de la rédaction de ce document, la version la plus récente était 0.10.0). Vous pouvez installer cette interface sur Mac, Linux et Windows.

Le processus d'utilisation de Docker sur Azure est relativement simple :

* Installez l’interface de ligne de commande et ses dépendances sur l’ordinateur à partir duquel vous souhaitez contrôler Microsoft Azure (sur Windows, il s’agira d’une distribution Linux qui s’exécute comme une machine virtuelle).
* Utiliser les commandes Docker de l’interface de ligne de commande Azure pour créer un hôte VM Docker dans Microsoft Azure
* Utilisez les commandes Docker locales pour gérer vos conteneurs dans votre machine virtuelle Docker sous Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
Pour installer et configurer l’interface de ligne de commande Azure, consultez [Installation de l’interface de ligne de commande Azure](../../../cli-install-nodejs.md). Pour confirmer l’installation, tapez `azure` à l’invite de commandes. Après quelques secondes, l’illustration ASCII azure-cli doit normalement apparaître. Elle répertorie les commandes de base mises à votre disposition. Si l’installation s’est déroulée correctement, vous pouvez taper `azure help vm` et voir que l’une des commandes répertoriées est « docker ».

> [!NOTE]
> Docker présente des outils pour Windows, [Docker Machine](https://docs.docker.com/installation/windows/), qui vous permet également d’automatiser la création d’un client Docker, à mettre à profit pour utiliser des machines virtuelles Azure en tant qu’hôtes Docker.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Connecter l’interface de ligne de commande Microsoft Azure à votre compte Microsoft Azure
Avant de pouvoir utiliser l’interface de ligne de commande Microsoft Azure, vous devez associer vos informations d’identification Microsoft Azure à l’interface sur votre plateforme. La section [Connexion à votre abonnement Azure](/cli/azure/authenticate-azure-cli) explique comment télécharger ou importer votre fichier **.publishsettings** ou associer votre interface de ligne de commande Azure à un ID d’organisation.

> [!NOTE]
> Le comportement diffère quelque peu selon que vous utilisez l’une ou l’autre méthode d’authentification. Veuillez donc lire attentivement le document ci-dessus pour bien comprendre les différentes fonctionnalités.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installation de Docker et utilisation de l’extension Docker VM pour Azure
Suivez les [instructions d'installation de Docker](https://docs.docker.com/installation/#installation) pour installer Docker sur votre ordinateur.

Pour utiliser Docker avec une machine virtuelle Azure, [l’Agent de machine virtuelle Linux Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) doit être installé dans l’image Linux utilisée pour la machine virtuelle. Actuellement, seuls deux types d'image le permettent :

* une image Ubuntu de la galerie d'images Azure ou
* une image Linux personnalisée que vous avez créée avec l'Agent de machine virtuelle Linux Azure installé et configuré. Consultez [Agent de machine virtuelle Linux Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour en savoir plus sur la création d’une machine virtuelle Linux personnalisée avec l’Agent de machine virtuelle Azure.

### <a name="using-the-azure-image-gallery"></a>Utilisation de la galerie d’images Azure
Dans une session Bash ou Terminal, utilisez la commande d’interface de ligne de commande Microsoft Azure suivante pour localiser l’image Ubuntu la plus récente dans la galerie de machines virtuelles ; tapez :

`azure vm image list | grep Ubuntu-14_04`

Puis sélectionnez un des noms d’images (par ex. `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`) ; utilisez la commande suivante pour créer une machine virtuelle en utilisant cette image.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

où :

* *&lt;vm-cloudservice name&gt;* est le nom de la machine virtuelle qui deviendra l’ordinateur hôte du conteneur Docker dans Azure
* *&lt;username&gt;* est le nom de l'utilisateur racine par défaut de la machine virtuelle
* *&lt;password&gt;* est le mot de passe du compte *username* remplissant les conditions de complexité Azure

> [!NOTE]
> Un mot de passe doit comporter au moins 8 caractères, dont une lettre minuscule et une lettre majuscule, un chiffre et un caractère spécial parmi les suivants : `!@#$%^&+=`. Le point à la fin de la phrase précédente n'est PAS un caractère spécial.
> 
> 

Si la commande a été exécutée correctement, vous devriez normalement obtenir un résultat comparable à ce qui est illustré ci-dessous, suivant les arguments et options spécifiques que vous avez utilisés :

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> La création d’une machine virtuelle peut prendre quelques minutes. Cependant, une fois l’approvisionnement terminé (valeur de l’état `ReadyRole`), le démon Docker (service Docker) démarre et vous pouvez vous connecter à l’hôte du conteneur Docker.
> 
> 

Pour tester la machine virtuelle Docker que vous avez créée dans Azure, tapez

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

où *&lt;vm-name-you-used&gt;* est le nom de la machine virtuelle utilisée dans votre appel à `azure vm docker create`. Ce que vous devez voir est similaire à ce qui suit ; cela indique que votre machine virtuelle hôte Docker est en service et attend vos commandes. 

Vous pouvez maintenant essayer de vous connecter à l’aide de votre client docker pour obtenir des informations (dans certaines configurations du client Docker, par exemple sur Mac, vous devrez peut-être utiliser `sudo`) :

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Pour vous assurer que tout fonctionne, vous pouvez examiner la machine virtuelle pour l’extension Docker :

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Authentification de la machine virtuelle hôte Docker
Outre la création de la machine virtuelle Docker, la commande `azure vm docker create` crée automatiquement les certificats nécessaires pour autoriser votre ordinateur client Docker à se connecter à l’hôte de conteneur Azure à l’aide du protocole HTTPS. Les certificats sont stockés sur les ordinateurs clients et hôtes, suivant le cas. Lors des tentatives suivantes, les certificats existants sont réutilisés et partagés avec le nouvel hôte.

Par défaut, les certificats sont placés dans `~/.docker`, et Docker est configuré pour s’exécuter sur le port **2376**. Si vous souhaitez utiliser un autre port ou répertoire, vous pouvez utiliser l’une des options de ligne de commande `azure vm docker create` suivantes afin de configurer votre machine virtuelle hôte de conteneur Docker, de telle sorte qu’elle utilise un port ou des certificats différents pour la connexion des clients :

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Le démon Docker sur l’hôte est configuré pour écouter et authentifier les connexions client sur le port spécifié à l’aide des certificats générés par la commande `azure vm docker create` . L’ordinateur client doit posséder ces certificats pour avoir accès à l’hôte Docker.

> [!NOTE]
> Un hôte en réseau qui s’exécute sans ces certificats est à la merci de toute personne capable de se connecter à l’ordinateur. Avant de modifier la configuration par défaut, vous devez bien comprendre les risques auxquels sont exposés vos ordinateurs et applications.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Vous êtes prêt à consulter le [Guide d'utilisation Docker] et à utiliser votre machine virtuelle Docker. Pour créer une machine virtuelle Docker dans le nouveau portail, consultez la page [Utilisation de l'extension Docker VM avec le portail].
* L’extension de machine virtuelle Azure Docker prend également en charge Docker Compose, qui utilise un fichier YAML déclaratif pour utiliser une application modélisée par un développeur dans tout environnement et générer un déploiement cohérent. Voir [Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples sur une machine virtuelle Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[Utilisation de l'extension Docker VM avec le portail]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guide d'utilisation Docker]:https://docs.docker.com/userguide/

[Prise en main de Docker et Compose pour définir et exécuter une application à conteneurs multiples sur une machine virtuelle Azure]:../docker-compose-quickstart.md
