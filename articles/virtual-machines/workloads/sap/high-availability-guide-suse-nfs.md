---
title: Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server | Microsoft Docs
description: Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: sedusch
ms.openlocfilehash: b1a7b962d07b64aaa662aab937feed1782851a7b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Cet article décrit comment déployer les machines virtuelles, les configurer, installer l’infrastructure du cluster et installer un serveur NFS hautement disponible pouvant être utilisé pour stocker les données partagées d’un système SAP hautement disponible.
Ce guide décrit comment configurer un serveur NFS à haute disponibilité qui est utilisé par deux systèmes SAP, NW1 et NW2. Les noms des ressources (par exemple les machines virtuelles, les réseaux virtuels) de l’exemple partent du principe que vous avez utilisé le [modèle de serveur de fichiers SAP][template-file-server] avec le préfixe de ressource **prod**.

Commencez par lire les notes et publications SAP suivantes

* Note SAP [1928533], qui contient :
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure

* La note SAP [2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2205917] contient des paramètres de système d’exploitation recommandés pour SUSE Linux Enterprise Server pour les applications SAP
* La note SAP [1944799] contient des instructions SAP HANA pour SUSE Linux Enterprise Server pour les applications SAP
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1984787] contient des informations sur SUSE Linux Enterprise Server 12.
* La note SAP [1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* Le [WIKI de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contient toutes les notes SAP requises pour Linux.
* [Planification et implémentation de Machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de Machines virtuelles Azure pour SAP sur Linux (cet article)][deployment-guide]
* [Déploiement SGBD de Machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [SAP HANA SR Performance Optimized Scenario][suse-hana-ha-guide] (Scénario d’optimisation des performances de réplication système de SAP HANA)  
  Le guide contient toutes les informations requises pour configurer la réplication système SAP HANA en local. Utilisez ce guide comme référence.
* [Stockage NFS hautement disponible avec DRBD et Pacemaker][suse-drbd-guide] Ce guide contient toutes les informations nécessaires pour configurer un serveur NFS à haute disponibilité. Utilisez ce guide comme référence.


## <a name="overview"></a>Vue d'ensemble

Pour obtenir une haute disponibilité, SAP NetWeaver nécessite un serveur NFS. Le serveur NFS est configuré dans un cluster distinct et peut être utilisé par plusieurs systèmes SAP.

![Vue d’ensemble de la haute disponibilité SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Le serveur NFS utilise un nom d’hôte virtuel dédié et des adresses IP virtuelles pour chacun des systèmes SAP qui utilise ce serveur NFS. Sur Azure, un équilibreur de charge est nécessaire pour utiliser une adresse IP virtuelle. La liste suivante illustre la configuration de l’équilibreur de charge.        

* Configuration du frontend
  * Adresse IP 10.0.0.4 pour NW1
  * Adresse IP 10.0.0.5 pour NW2
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster NFS
* Port de la sonde
  * Port 61000 pour NW1
  * Port 61001 pour NW2
* Règles d’équilibrage de charge
  * TCP 2049 pour NW1
  * UDP 2049 pour NW1
  * TCP 2049 pour NW2
  * UDP 2049 pour NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurer un serveur NFS à haute disponibilité

Vous pouvez utiliser un modèle Azure de GitHub pour déployer l’ensemble des ressources Azure, notamment les machines virtuelles, les groupes à haute disponibilité et l’équilibreur de charge ou vous pouvez déployer les ressources manuellement.

### <a name="deploy-linux-via-azure-template"></a>Déployer Linux via le modèle Azure

La Place de marché Azure contient une image de SUSE Linux Enterprise Server for SAP Applications 12 que vous pouvez utiliser pour déployer de nouvelles machines virtuelles.
Vous pouvez utiliser un des modèles de démarrage rapide disponibles sur github pour déployer toutes les ressources nécessaires. Le modèle déploie les machines virtuelles, l’équilibrage de charge, le groupe à haute disponibilité, etc. Suivez ces étapes pour déployer le modèle :

1. Ouvrez le [modèle de serveur de fichiers SAP][template-file-server] dans le portail Azure.   
1. Entrez les paramètres suivants.
   1. Préfixe de ressource  
      Entrez le préfixe à utiliser. Cette valeur sera utilisée comme préfixe pour les ressources déployées.
   2. Nombre de systèmes SAP Saisissez le nombre de systèmes SAP qui utiliseront ce serveur de fichiers. Cette action déploie la quantité requise de configurations de serveurs frontaux, de règles d’équilibrage de charge, de ports de sondage, de disques, etc.
   3. Type de système d’exploitation  
      Sélectionnez l’une des distributions Linux. Dans cet exemple, sélectionnez SLES 12.
   4. Nom d’utilisateur et mot de passe d’administrateur  
      Un utilisateur pouvant être utilisé pour ouvrir une session sur la machine est créé.
   5. ID de sous-réseau  
      ID du sous-réseau auquel les machines virtuelles doivent être connectées. Laissez vide si vous souhaitez créer un réseau virtuel, ou sélectionnez le sous-réseau de votre VPN ou réseau virtuel Express Route pour connecter la machine virtuelle à votre réseau local. L’ID se présente généralement comme suit : /subscriptions/**&lt;ID_abonnement&gt;**/resourceGroups/**&lt;nom_groupe_ressources&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nom_réseau_virtuel&gt;**/subnets/**&lt;nom_sous_réseau&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Déployer manuellement Linux via le portail Azure

Vous devez tout d’abord créer les machines virtuelles pour ce cluster NFS. Par la suite, vous créez un équilibreur de charge et utilisez les machines virtuelles dans les pools principaux.

1. Création d’un groupe de ressources
1. Création d'un réseau virtuel
1. Créer un groupe à haute disponibilité  
   Définir un domaine de mise à jour maximal
1. Créer la machine virtuelle 1   
   Utiliser au minimum SLES4SAP 12 SP1. Dans cet exemple, nous allons utiliser l’image SLES4SAP 12 SP1 BYOS https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS) est utilisé  
   Sélectionner le groupe à haute disponibilité créé précédemment  
1. Créer la machine virtuelle 2   
   Utiliser au minimum SLES4SAP 12 SP1. Dans cet exemple, nous allons utiliser l’image SLES4SAP 12 SP1 BYOS https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS) est utilisé  
   Sélectionner le groupe à haute disponibilité créé précédemment  
1. Ajouter un disque de données pour chaque système SAP sur les deux machines virtuelles
1. Créer un équilibrage de charge (interne)  
   1. Créer les adresses IP de serveurs frontaux
      1. Adresse IP 10.0.0.4 pour NW1
         1. Ouvrir l’équilibrage de charge, sélectionner le pool d’adresses IP frontal et cliquer sur Ajouter
         1. Entrer le nom du nouveau pool d’adresses IP frontal (par exemple **nw1-frontend**)
         1. Définir l’affectation sur Statique et entrer l’adresse IP (par exemple **10.0.0.4**)
         1. Cliquez sur OK         
      1. Adresse IP 10.0.0.5 pour NW2
         * Répéter les étapes ci-dessus pour NW2
   1. Créer les pools principaux
      1. Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster NFS pour NW1
         1. Ouvrir l’équilibrage de charge, sélectionner les pools principaux et cliquer sur Ajouter
         1. Entrer le nom du nouveau pool principal (par exemple **nw1-backend**)
         1. Cliquer sur Ajouter une machine virtuelle
         1. Sélectionner le groupe à haute disponibilité créé précédemment
         1. Sélectionner les machines virtuelles du cluster NFS
         1. Cliquez sur OK
      1. Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster NFS pour NW2
         * Répéter les étapes ci-dessus pour créer un pool principal pour NW2
   1. Créer les sondes d’intégrité
      1. Port 61000 pour NW1
         1. Ouvrir l’équilibrage de charge, sélectionner les sondes d’intégrité et cliquer sur Ajouter
         1. Entrer le nom de la nouvelle sonde d’intégrité (par exemple **nw1-hp**)
         1. Sélectionner le protocole TCP et le port 610**00**, et conserver un intervalle de 5 et un seuil de défaillance de 2
         1. Cliquez sur OK
      1. Port 61001 pour NW2
         * Répéter les étapes ci-dessus pour créer une sonde d’intégrité pour NW2
   1. Règles d’équilibrage de charge
      1. TCP 2049 pour NW1
         1. Ouvrir l’équilibrage de charge, sélectionner les règles d’équilibrage de charge et cliquer sur Ajouter
         1. Entrer le nom de la nouvelle règle d’équilibrage de charge (par exemple **nw1-lb-2049**)
         1. Sélectionner l’adresse IP du serveur frontal, le pool principal et la sonde d’intégrité créés précédemment (par exemple **nw1-frontend**)
         1. Conserver le protocole **TCP** et choisir le port **2049**
         1. Augmenter le délai d’inactivité à 30 minutes
         1. **Veiller à activer IP flottante**
         1. Cliquez sur OK    
      1. UDP 2049 pour NW1
         * Répéter les étapes ci-dessus pour les ports 2049 et UDP pour NW1
      1. TCP 2049 pour NW2
         * Répéter les étapes ci-dessus pour les ports 2049 et TCP pour NW2
      1. UDP 2049 pour NW2
         * Répéter les étapes ci-dessus pour les ports 2049 et UDP pour NW2

### <a name="create-pacemaker-cluster"></a>Créer le cluster Pacemaker

Suivez les étapes décrites à la page [Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](high-availability-guide-suse-pacemaker.md) pour créer un cluster Pacemaker de base pour ce serveur NFS.

### <a name="configure-nfs-server"></a>Configurer le serveur NFS

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Configurer la résolution de nom d’hôte   

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Activer le serveur NFS

   Créer l’entrée d’exportation racine, démarrer le serveur NFS et activer le démarrage automatique

   <pre><code>
   sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/

   sudo systemctl enable nfsserver
   sudo service nfsserver restart
   </code></pre>

1. **[A]** Installer les composants drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Créer une partition pour les appareils drbd

   Répertorier tous les disques de données disponibles

   <pre><code>
   sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Exemple de sortie
   
   ```
   lun0  lun1
   ```

   Créer des partitions pour chaque disque de données

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Créer des configurations LVM

   Répertorier toutes les partitions disponibles

   <pre><code>
   ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Exemple de sortie
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Créer des volumes LVM pour chaque partition

   <pre><code>
   sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Configurer drbd

   <pre><code>
   sudo vi /etc/drbd.conf
   </code></pre>

   S’assurer que le fichier drbd.conf comporte les 2 lignes suivantes

   <pre><code>
   include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Modifier la configuration drbd globale

   <pre><code>
   sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Ajouter les entrées suivantes aux sections handler et net.

   <pre><code>
   global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             resync-rate 50M;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
        }
   }
   </code></pre>

1. **[A]** Créer les appareils NFS drbd

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Insérer la configuration pour le nouvel appareil drbd et quitter

   <pre><code>
   resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Insérer la configuration pour le nouvel appareil drbd et quitter

   <pre><code>
   resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Créer l’appareil drbd et le démarrer

   <pre><code>
   sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Ignorer la synchronisation initiale

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Définir le nœud principal

   <pre><code>
   sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Patienter jusqu’à ce que les nouveaux appareils drbd soient synchronisés

   <pre><code>
   sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Créer des systèmes de fichiers sur les appareils drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** Configurer la détection de Split-Brain drbd

   Lorsque vous utilisez drbd pour synchroniser les données entre 2 hôtes, un syndrome Split-Brain peut se produire. Il s’agit d’un scénario au cours duquel les 2 nœuds de cluster promeuvent l’appareil drbd en tant qu’instance principale et se désynchronisent. S’il s’agit d’un problème rare, vous avez tout intérêt à le résoudre dans les meilleurs délais. Par conséquent, il est important d’être prévenu de la survenue d’un Split-Brain.

   Consultez la [documentation officielle drbd](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) pour savoir comment configurer une notification de Split-Brain.

   Il est également possible de récupérer automatiquement à partir d’un scénario de Split-Brain. Pour plus d’informations, consultez la page [Automatic split brain recovery policies](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration) (Stratégies de récupération automatique à partir des scénarios de Split-Brain).
   
### <a name="configure-cluster-framework"></a>Configurer le framework du cluster

1. **[1]** Ajouter les appareils drbd NFS pour le système SAP NW1 à la configuration de cluster

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=3 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=4 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=5 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=6 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=7 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=8 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> exportfs_<b>NW1</b>_sidsys \
     exportfs_<b>NW1</b>_sapmntsid exportfs_<b>NW1</b>_trans exportfs_<b>NW1</b>_ASCS \
     exportfs_<b>NW1</b>_ASCSERS exportfs_<b>NW1</b>_SCS exportfs_<b>NW1</b>_SCSERS \
     nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Ajouter les appareils drbd NFS pour le système SAP NW2 à la configuration de cluster

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true   
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=9 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=10 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=11 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=12 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=13 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=14 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=15 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=16 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> exportfs_<b>NW2</b>_sidsys \
     exportfs_<b>NW2</b>_sapmntsid exportfs_<b>NW2</b>_trans exportfs_<b>NW2</b>_ASCS \
     exportfs_<b>NW2</b>_ASCSERS exportfs_<b>NW2</b>_SCS exportfs_<b>NW2</b>_SCSERS \
     nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]** Désactiver le mode de maintenance
   
   <pre><code>
   sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Étapes suivantes
* [Installer l’instance SAP ASCS et la base de données](high-availability-guide-suse.md)
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha].
