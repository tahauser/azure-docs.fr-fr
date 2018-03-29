---
title: Configurer MySQL sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: Découvrez comment installer la pile MySQL sur une machine virtuelle Linux (famille de systèmes d’exploitation Ubuntu ou RedHat) dans Azure.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: iainfou
ms.openlocfilehash: d91f8cf8455a60d3e0afb2f209ba07933bcdee1c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-install-mysql-on-azure"></a>Installation de MySQL sur Azure
Dans cet article, vous allez apprendre à installer et à configurer MySQL sur une machine virtuelle Azure qui exécute Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Installation de MySQL sur votre machine virtuelle
> [!NOTE]
> Vous devez déjà disposer d’une machine virtuelle Microsoft Azure exécutant Linux pour effectuer ce didacticiel. Consultez le [didacticiel sur les machines virtuelles Linux Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour créer et configurer une machine virtuelle Linux avec `mysqlnode` comme nom de la machine virtuelle et `azureuser` en tant qu’utilisateur avant de continuer.
> 
> 

[Dans ce cas, utilisez le port 3306 comme port MySQL.]  

Connectez-vous à la machine virtuelle Linux que vous avez créée via putty. Si c’est la première fois que vous utilisez la machine virtuelle Linux Azure, découvrez comment utiliser putty pour vous connecter à une machine virtuelle Linux [ici](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nous allons utiliser le package du référentiel pour installer MySQL5.6 à titre d'exemple dans cet article. En réalité, MySQL5.6 est une version améliorée en termes de performances par rapport à MySQL5.5.  Plus d’informations [ici](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Installation de MySQL5.6 sur Ubuntu
Nous utiliserons ici une machine virtuelle Linux avec Ubuntu à partir d’Azure.

* Étape 1 : installation de MySQL Server 5.6. Basculer vers l’utilisateur `root` :
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Instraller mysql-server 5.6 :
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Lors de l’installation, une boîte de dialogue s’affichera pour vous demander de configurer le mot de passe racine MySQL ci-dessous et vous devrez configurer ce mot de passe ici.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Entrez une nouvelle fois le mot de passe pour le confirmer.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Étape 2 : connexion au serveur MySQL
  
    Une fois l’installation du serveur MySQL terminée, le service MySQL démarre automatiquement. Vous pouvez vous connecter au serveur MySQL avec l’utilisateur `root` .
    Utilisez la commande ci-dessous pour vous connecter et entrer votre mot de passe.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Étape 3 : gérer le service MySQL exécuté
  
    (a) Obtenir l’état du service MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) Démarrer le service MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) Arrêter le service MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) Redémarrer le service MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Installation de MySQL sur la famille de systèmes d’exploitation Redhat telle que CentOS, Oracle Linux
Nous utiliserons ici des machines virtuelles Linux avec CentOS ou Oracle Linux.

* Étape 1 : ajouter le référentiel MySQL Yum. Basculer vers l’utilisateur `root` :
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Téléchargez et installez le package de lancement MySQL :
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Étape 2 : modifier le fichier ci-dessous pour activer le référentiel MySQL afin de télécharger le package MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Mettre à jour chaque valeur de ce fichier ci-dessous :
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Étape 3 : installer MySQL à partir du référentiel MySQL   Installer MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Le package RPM MySQL et tous les packages associés seront installés.
* Étape 4 : gérer le service MySQL exécuté
  
    (a) Vérifier l’état du service du serveur MySQL :
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) Vérifier si le port par défaut du serveur MySQL est exécuté :
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) Démarrer le serveur MySQL :

           #[root@mysqlnode ~]#service mysqld start

    (d) Arrêter le serveur MySQL :

           #[root@mysqlnode ~]#service mysqld stop

    (e) Définir MySQL pour qu’il démarre lorsque le système démarre :

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Installation de MySQL sur SUSE Linux
Nous utiliserons ici une machine virtuelle Linux avec OpenSUSE.

* Étape 1 : téléchargez et installez MySQL Server
  
    Basculez vers l’utilisateur `root` via la commande ci-dessous :  
  
           #sudo su -
  
    Téléchargez et installez le package MySQL :
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Étape 2 : gérer le service MySQL exécuté
  
    (a) Vérifier l’état du serveur MySQL :
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) Vérifier le port par défaut du serveur MySQL : 
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) Démarrer le serveur MySQL :

           #[root@mysqlnode ~]# rcmysql start

    (d) Arrêter le serveur MySQL :

           #[root@mysqlnode ~]# rcmysql stop

    (e) Définir MySQL pour qu’il démarre lorsque le système démarre :

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>étape suivante
Vous trouverez plus d’informations sur l’utilisation de MySQL [ici](https://www.mysql.com/).

