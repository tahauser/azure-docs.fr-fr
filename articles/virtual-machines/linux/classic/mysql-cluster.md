---
title: "Regroupement de MySQL avec des jeux à charge équilibrée | Microsoft Docs"
description: "Configuration d’un cluster MySQL Linux à haute disponibilité, à charge équilibrée créé avec le modèle de déploiement classique dans Azure"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: 8b39da7b96002e14c7d9a567ddc4f1dbc9d45c60
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Utiliser des jeux d'équilibrage de la charge pour regrouper MySQL sur Linux
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager](../../../resource-manager-deployment-model.md) et classique. Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Un [modèle Resource Manager](https://azure.microsoft.com/documentation/templates/mysql-replication/) est disponible si vous voulez déployer un cluster MySQL.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Cet article explore et illustre les différentes approches disponibles pour déployer des services Linux hautement disponibles sur Microsoft Azure en explorant la haute disponibilité de MySQL Server. Une vidéo illustrant cette approche est disponible sur [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Nous allons présenter une solution haute disponibilité MySQL à maître unique, deux nœuds et sans partage basée sur DRBD, Corosync et Pacemaker. Seul un nœud à la fois exécute MySQL. La lecture et l'écriture à partir de la ressource DRBD sont également limitées à un seul nœud à la fois.

Les solutions d’adresses IP virtuelles, telles que LVS, ne sont pas nécessaires, car vous allez utiliser des jeux d’équilibrage de la charge dans Microsoft Azure pour fournir la fonctionnalité de tourniquet (round robin), ainsi que la détection de points de terminaison, la suppression et la récupération sans perte de données des adresses IP virtuelles. L’adresse IP virtuelle est une adresse IPv4 globalement routable attribuée par Microsoft Azure lorsque vous créez le service cloud.

Plusieurs architectures sont possibles pour MySQL, comme NBD Cluster, Percona et Galera, ainsi que plusieurs solutions intermédiaires, notamment au moins une disponible sous forme de machine virtuelle sur [VM Depot](http://vmdepot.msopentech.com). Tant que ces solutions peuvent se répliquer en monodiffusion, multidiffusion ou en diffusion et tant qu'elles ne reposent pas sur un stockage partagé ou plusieurs interfaces réseau, les scénarios doivent être faciles à déployer sur Microsoft Azure.

Ces architectures de clustering peuvent être étendues de la même façon à d’autres produits tels que PostgreSQL et OpenLDAP. Cette procédure d’équilibrage de charge sans partage peut notamment être testée correctement avec OpenLDAP à multiples maîtres. Vous pouvez regarder la vidéo sur le blog Channel 9.

## <a name="get-ready"></a>Se préparer
Vous devez disposer des ressources et des capacités suivantes :

  - Un compte Microsoft Azure avec un abonnement valide, en mesure de créer au moins deux machines virtuelles (dans cet exemple, nous avons utilisé XS)
  - Un réseau et un sous-réseau
  - Un groupe d’affinités
  - Un groupe à haute disponibilité
  - La possibilité de créer des disques durs virtuels dans la même région que celle où se situe le service cloud et de les joindre aux machines virtuelles Linux

### <a name="tested-environment"></a>Environnement testé
* Ubuntu 13.10
  * DRBD
  * MySQL Server
  * Corosync et Pacemaker

### <a name="affinity-group"></a>Groupe d'affinités
Créez un groupe d’affinités pour la solution en vous connectant au portail Azure, en sélectionnant **Paramètres** et en créant un groupe d’affinités. Des ressources allouées créées ultérieurement seront attribuées à ce groupe d'affinités.

### <a name="networks"></a>Réseaux
Un réseau est créé et un sous-réseau est créé à l'intérieur de ce dernier. Cet exemple utilise un réseau 10.10.10.0/24 avec un seul sous-réseau /24.

### <a name="virtual-machines"></a>Machines virtuelles
La première machine virtuelle Ubuntu 13.10 est créée à l’aide d’une image de la galerie Ubuntu prise en charge et elle est nommée `hadb01`. Un service cloud est créé au cours du processus. Il se nomme hadb. Ce nom illustre la nature partagée et l’équilibre de charge du service lorsque nous lui ajouterons des ressources supplémentaires. La création de `hadb01` est sans incident. Elle est réalisée à l’aide du portail. Un point de terminaison est automatiquement créé pour SSH et le nouveau réseau est sélectionné. Vous pouvez désormais créer un groupe à haute disponibilité pour les machines virtuelles.

Une fois la première machine virtuelle créée (techniquement, lors de la création du service cloud), créez la deuxième machine virtuelle, `hadb02`. Pour celle-ci, utilisez la machine virtuelle Ubuntu 13.10 de la galerie à l’aide du portail, mais utilisez un service cloud existant, `hadb.cloudapp.net`, au lieu d’en créer un. Le réseau et le groupe à haute disponibilité doivent être automatiquement sélectionnés. Un point de terminaison SSH est également créé.

Une fois les deux machines virtuelles créées, notez le port SSH pour `hadb01` (TCP 22) et `hadb02` (attribués automatiquement par Azure).

### <a name="attached-storage"></a>Stockage associé
Associez un nouveau disque aux deux machines virtuelles et créez de nouveaux disques de 5 Go au cours du processus. Les disques sont hébergés dans le conteneur de disques durs virtuels utilisé pour nos disques de système d’exploitation principaux. Une fois les disques créés et associés, il est inutile de redémarrer Linux, car le noyau voit le nouvel appareil. Cet appareil est généralement `/dev/sdc`. Vérifiez `dmesg` pour la sortie.

Sur chaque machine virtuelle, créez une partition à l’aide de `cfdisk` (partition Linux principale) et écrivez la nouvelle table de partition. Ne créez pas de système de fichiers sur cette partition.

## <a name="set-up-the-cluster"></a>Configurer le cluster
Sur les deux machines virtuelles Ubuntu, utilisez APT pour installer Corosync, Pacemaker et DRBD. Pour ce faire avec `apt-get`, exécutez le code suivant :

    sudo apt-get install corosync pacemaker drbd8-utils.

N’installez pas MySQL pour l’instant. Les scripts d’installation Debian et Ubuntu initialisent un répertoire de données MySQL sur `/var/lib/mysql`, mais le répertoire étant remplacé par un système de fichiers DRBD, vous devez installer MySQL ultérieurement.

Vérifiez (à l’aide de `/sbin/ifconfig`) que les deux machines virtuelles utilisent des adresses du sous-réseau 10.10.10.0/24 et qu’elles peuvent s’envoyer des commandes ping en utilisant leur nom. Vous pouvez également utiliser `ssh-keygen` et `ssh-copy-id` pour vérifier que les deux machines virtuelles peuvent communiquer via SSH sans nécessiter de mot de passe.

### <a name="set-up-drbd"></a>Configurer DRBD
Créez une ressource DRBD utilisant la partition `/dev/sdc1` sous-jacente pour produire une ressource `/dev/drbd1` capable d’être formatée à l’aide d’ext3 et utilisée dans les nœuds principaux et secondaires.

1. Ouvrez `/etc/drbd.d/r0.res` et copiez la définition de ressource suivante sur les deux machines virtuelles :

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Initialisez la ressource à l’aide de `drbdadm` sur les deux machines virtuelles :

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. Sur la machine virtuelle principale (`hadb01`), forcez la propriété (principale) de la ressource DRBD :

        sudo drbdadm primary --force r0

Si vous examinez le contenu de /proc/drbd (`sudo cat /proc/drbd`) sur les deux machines virtuelles, vous devez voir `Primary/Secondary` sur `hadb01` et `Secondary/Primary` sur `hadb02`, ce qui est cohérent avec la solution jusqu’à présent. Le disque de 5 Go est synchronisé sur le réseau 10.10.10.0/24 gratuitement pour les clients.

Une fois le disque synchronisé, vous pouvez créer le système de fichiers sur `hadb01`. À des fins de test, nous avons utilisé ext2, mais le code suivant créera un système de fichiers ext3 :

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Installer la ressource DRBD
Vous êtes maintenant prêts à installer des ressources DRBD sur `hadb01`. Le système Debian et ses dérivés utilisent `/var/lib/mysql` en tant que répertoire de données MySQL. Puisque vous n’avez pas installé MySQL, créez le répertoire et montez la ressource DRBD. Pour exécuter cette option, exécutez le code suivant sur `hadb01` :

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Configurez MySQL
Vous êtes maintenant prêt à installer MySQL sur `hadb01`:

    sudo apt-get install mysql-server

Pour `hadb02`, vous avez deux options. Vous pouvez installer mysql-server maintenant, ce qui créera /var/lib/mysql, y mettra un nouveau répertoire de données, puis supprimera le contenu. Pour exécuter cette option, exécutez le code suivant sur `hadb02` :

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La deuxième option est de basculer vers `hadb02` et d’installer mysql-server à cet emplacement. Les scripts d’installation remarqueront l’installation existante et n’y toucheront pas.

Exécutez le code suivant sur `hadb01` :

    sudo drbdadm secondary –force r0

Exécutez le code suivant sur `hadb02` :

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si vous ne prévoyez pas de faire basculer DRBD maintenant, la première option, bien que sans doute moins élégante, est la plus simple. Une fois la configuration terminée, vous pouvez commencer à travailler sur votre base de données MySQL. Exécutez le code suivant sur `hadb02` (ou sur le serveur actif selon DRBD) :

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Cette dernière instruction désactive de manière efficace l’authentification de l’utilisateur racine dans ce tableau. Elle doit être remplacée par vos instructions GRANT de production et n'est incluse qu'à titre d'exemple.

Si vous souhaitez effectuer des requêtes depuis l’extérieur des machines virtuelles (ce qui est l’objet de ce guide), vous devez également activer la mise en réseau pour MySQL. Sur les deux machines virtuelles, ouvrez `/etc/mysql/my.cnf` et accédez à `bind-address`. Remplacez l’adresse 127.0.0.1 par 0.0.0.0. Après avoir enregistré le fichier, émettez une commande `sudo service mysql restart` sur votre machine virtuelle principale actuelle.

### <a name="create-the-mysql-load-balanced-set"></a>Créer le jeu d’équilibrage de la charge MySQL
Revenez au portail, accédez à `hadb01`, puis choisissez **Points de terminaison**. Pour créer un point de terminaison, choisissez MySQL (TCP 3306) dans la liste déroulante et sélectionnez **Créer un jeu d’équilibrage de la charge**. Nommez le point de terminaison soumis à l’équilibrage de charge `lb-mysql`. Définissez **Temps** sur 5 secondes au minimum.

Après avoir créé le point de terminaison, accédez à `hadb02`, choisissez **Points de terminaison** et créez un point de terminaison. Choisissez `lb-mysql`, puis sélectionnez MySQL dans la liste déroulante. Vous pouvez aussi utiliser l'interface de ligne de commande Azure pour cette étape.

Vous avez désormais tout le nécessaire pour un fonctionnement manuel du cluster.

### <a name="test-the-load-balanced-set"></a>Tester le jeu d’équilibrage de la charge
Il est possible d’exécuter des tests à partir d’une machine externe à l’aide d’un client MySQL, ou à l’aide de certaines applications, telles que phpMyAdmin exécutée en tant que site web Azure. Dans ce cas, vous avez utilisé l’outil de ligne de commande de MySQL sur une autre boîte Linux :

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Basculement manuel
Vous pouvez simuler des basculements en arrêtant MySQL, en commutant la machine virtuelle principale de DRBD et en redémarrant MySQL.

Pour exécuter cette tâche, exécutez le code suivant sur hadb01 :

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Puis, sur hadb02 :

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Après un basculement manuel, vous pouvez répéter votre requête à distance. Elle devrait fonctionner parfaitement.

## <a name="set-up-corosync"></a>Configurer Corosync
Corosync est l'infrastructure de clusters sous-jacente nécessaire au fonctionnement de Pacemaker. Pour Heartbeat (ainsi que d’autres méthodologies comme Ultramonkey), Corosync est une division des fonctionnalités CRM, tandis que Pacemaker reste plus proche de Heartbeat du point de vue des fonctionnalités.

La principale contrainte de Corosync sur Azure est qu'il préfère les communications multidiffusion sur diffusion aux communications monodiffusion, alors que les réseaux Microsoft Azure ne prennent en charge que la monodiffusion.

Heureusement, Corosync dispose d’un mode de monodiffusion fonctionnel. La seule contrainte réelle est que, puisque tous les nœuds ne communiquent pas entre eux de manière automatique, vous devez les définir dans vos fichiers de configuration en incluant leurs adresses IP. Nous pouvons utiliser les fichiers d’exemple Corosync pour la monodiffusion et modifier l’adresse de liaison, les listes de nœuds et les répertoires de journalisation (Ubuntu utilise `/var/log/corosync` alors que les fichiers d’exemple utilisent `/var/log/cluster`) et activer les outils de quorum.

> [!NOTE]
> Utilisez la directive `transport: udpu` ci-dessous et les adresses IP des nœuds, définies manuellement.

Exécutez le code suivant sur `/etc/corosync/corosync.conf` pour les deux nœuds :

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copiez ce fichier de configuration sur les deux machines virtuelles et démarrez Corosync dans les deux nœuds :

    sudo service start corosync

Peu après le démarrage du service, le cluster doit être établi dans l’anneau actuel et un quorum doit être constitué. Il est possible de vérifier cette fonctionnalité en consultant les journaux ou en exécutant le code suivant :

    sudo corosync-quorumtool –l

Vous devez voir une sortie similaire à l’image qui suit :

![corosync-quorumtool -l sample output](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Configurer Pacemaker
Pacemaker utilise le cluster pour surveiller les ressources, définir le moment où les machines virtuelles principales s’arrêtent et les faire basculer vers les machines secondaires. Les ressources peuvent notamment être définies à partir d'un ensemble de scripts disponibles ou à partir de script LSB (de type init).

Nous voulons que Pacemaker « s’accapare » la ressource DRBD, du point de montage et du service MySQL. Si Pacemaker peut activer et désactiver DRBD, montez et démontez-le, puis démarrez et arrêtez MySQL dans le bon ordre en cas de problème avec la machine virtuelle principale. La configuration est terminée.

Lors de la première installation de Pacemaker, votre configuration doit être simple. Exemple :

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Vérifiez la configuration en exécutant `sudo crm configure show`.
2. Puis, créez un fichier (par exemple, `/tmp/cluster.conf`) avec les ressources suivantes :

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Chargez le fichier dans la configuration. Vous n’avez besoin d’exécuter cette opération que pour un seul nœud.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Vérifiez que Pacemaker se lance au démarrage dans les deux nœuds :

        sudo update-rc.d pacemaker defaults

5. À l’aide de `sudo crm_mon –L`, vérifiez que l’un de vos nœuds devient le maître du cluster et qu’il exécute toutes les ressources. Vous pouvez utiliser mount et ps pour vérifier que les ressources sont en cours d'exécution.

La capture d’écran suivante montre `crm_mon` avec un nœud arrêté (quittez en sélectionnant Ctrl+C) :

![crm_mon node stopped](./media/mysql-cluster/image002.png)

Cette capture d'écran montre les deux nœuds, avec un maître et un esclave :

![crm_mon operational master/slave](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Test
Vous êtes prêts pour une simulation de basculement automatique. Il existe deux façons de procéder : de manière douce et de manière dure.

La méthode douce consiste à utiliser la fonction d’arrêt du cluster : ``crm_standby -U `uname -n` -v on``. Si vous utilisez cette méthode sur le maître, l’esclave prend sa place. N’oubliez pas de désactiver à nouveau cette fonctionnalité. Dans le cas contraire, crm_mon affichera un nœud en veille.

La méthode dure consiste à arrêter la machine virtuelle principale (hadb01) via le portail ou à changer le niveau d’exécution de la machine virtuelle (halt, shutdown). Ceci aide Corosync et Pacemaker en signalant que le maître va s’arrêter. Il est possible de tester ceci (utile pour la maintenance), mais vous pouvez aussi forcer le scénario en gelant la machine virtuelle.

## <a name="stonith"></a>STONITH
Il doit être possible d’effectuer un arrêt de la machine virtuelle grâce aux outils en ligne de commande Azure pour Linux à la place d’un script STONITH contrôlant un périphérique physique. Vous pouvez utiliser `/usr/lib/stonith/plugins/external/ssh` comme base et activer STONITH dans la configuration du cluster. L’interface de ligne de commande Azure doit être installée globalement et les paramètres et le profil de publication doivent être chargés pour l’utilisateur du cluster.

Un exemple de code pour la ressource est disponible sur [GitHub](https://github.com/bureado/aztonith). Modifiez la configuration du cluster en ajoutant le code suivant à `sudo crm configure` :

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> Le script n’effectue pas de vérification en amont/en aval. La ressource SSH d’origine avait 15 vérifications ping, mais le temps de récupération d’une machine virtuelle Azure peut être variable.

## <a name="limitations"></a>Limitations
Les limites suivantes s'appliquent :

* Le script de ressource DRBD linbit qui gère DRBD comme ressource dans Pacemaker utilise `drbdadm down` lors de l’arrêt d’un nœud, même si celui-ci est seulement passé en veille. Cette situation n’est pas idéale, car l’esclave ne synchronisera pas la ressource DRBD lorsque le maître recevra des écritures. Si le maître n’échoue pas par commande, l’esclave peut prendre un état de système de fichiers antérieur. Cela peut être résolu de deux façons :
  * En appliquant un `drbdadm up r0` dans tous les nœuds du cluster grâce à une surveillance locale (non groupée)
  * En modifiant le script DRBD linbit, en veillant à ce que `down` ne soit pas appelé, dans `/usr/lib/ocf/resource.d/linbit/drbd`
* L’équilibrage de charge a besoin d’au moins 5 secondes pour répondre. Les applications doivent donc être conscientes des clusters et plus tolérantes en matière de délai. D’autres architectures peuvent également aider, par exemple les files d’attente intégrées et les intergiciels de requête.
* Un réglage de MySQL est nécessaire pour veiller à ce que l’écriture soit effectuée à un rythme raisonnable et que les caches soient vidés le plus souvent possible afin de limiter la perte de mémoire.
* Les performances d’écriture dépendent de l’interconnexion des machines virtuelles dans le commutateur virtuel, car il s’agit du mécanisme utilisé par DRBD pour répliquer l’appareil.
