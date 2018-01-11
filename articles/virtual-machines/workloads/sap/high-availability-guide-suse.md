---
title: "Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur SUSE Linux Enterprise Server pour les applications SAP | Microsoft Docs"
description: "Guide de haute disponibilité pour SAP NetWeaver sur SUSE Linux Enterprise Server pour les applications SAP"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 609b811705bb6f116db055b756910450f8990528
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP

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

Cet article décrit comment déployer et configurer les machines virtuelles, installer l’infrastructure de cluster et installer un système SAP NetWeaver 7.50 à haute disponibilité.
Dans les exemples de configuration, commandes d’installation, et ainsi de suite, nous utilisons le numéro d’instance ASCS 00, le numéro d’instance ERS 02 et l’ID de système SAP NWS. Les noms des ressources (telles que les machines virtuelles et les réseaux virtuels) dans l’exemple partent du principe que vous avez utilisé le [modèle convergé][template-converged] avec l’ID de système SAP NWS pour créer les ressources.

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

![Vue d’ensemble de la haute disponibilité SAP NetWeaver](./media/high-availability-guide-suse/img_001.png)

Le serveur NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS et la base de données SAP HANA utilisent un nom d’hôte virtuel et des adresses IP virtuelles. Sur Azure, un équilibreur de charge est nécessaire pour utiliser une adresse IP virtuelle. La liste suivante illustre la configuration de l’équilibreur de charge.

### <a name="nfs-server"></a>Serveur NFS
* Configuration du frontend
  * Adresse IP 10.0.0.4
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster NFS
* Port de la sonde
  * Port 61000
* Règles d’équilibrage de charge
  * TCP 2049 
  * UDP 2049

### <a name="ascs"></a>(A)SCS
* Configuration du frontend
  * Adresse IP 10.0.0.10
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster (A)SCS/ERS
* Port de la sonde
  * Port 620**&lt;nr&gt;**
* Règles d’équilibrage de charge
  * TCP 32**&lt;nr&gt;**
  * TCP 36**&lt;nr&gt;**
  * TCP 39**&lt;nr&gt;**
  * TCP 81**&lt;nr&gt;**
  * TCP 5**&lt;nr&gt;**13
  * TCP 5**&lt;nr&gt;**14
  * TCP 5**&lt;nr&gt;**16

### <a name="ers"></a>ERS
* Configuration du frontend
  * Adresse IP 10.0.0.11
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster (A)SCS/ERS
* Port de la sonde
  * Port 621**&lt;nr&gt;**
* Règles d’équilibrage de charge
  * TCP 33**&lt;nr&gt;**
  * TCP 5**&lt;nr&gt;**13
  * TCP 5**&lt;nr&gt;**14
  * TCP 5**&lt;nr&gt;**16

### <a name="sap-hana"></a>SAP HANA
* Configuration du frontend
  * Adresse IP 10.0.0.12
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster HANA
* Port de la sonde
  * Port 625**&lt;nr&gt;**
* Règles d’équilibrage de charge
  * TCP 3**&lt;nr&gt;**15
  * TCP 3**&lt;nr&gt;**17

## <a name="setting-up-a-highly-available-nfs-server"></a>Configuration d’un serveur NFS à haute disponibilité

### <a name="deploying-linux"></a>Déploiement de Linux

La Place de marché Azure contient une image de SUSE Linux Enterprise Server for SAP Applications 12 que vous pouvez utiliser pour déployer de nouvelles machines virtuelles.
Vous pouvez utiliser un des modèles de démarrage rapide disponibles sur github pour déployer toutes les ressources nécessaires. Le modèle déploie les machines virtuelles, l’équilibrage de charge, le groupe à haute disponibilité, etc. Suivez ces étapes pour déployer le modèle :

1. Ouvrez le [modèle de serveur de fichiers SAP][template-file-server] dans le portail Azure.   
1. Entrez les paramètres suivants.
   1. Préfixe de ressource  
      Entrez le préfixe à utiliser. Cette valeur sera utilisée comme préfixe pour les ressources déployées.
   2. Type de système d’exploitation  
      Sélectionnez l’une des distributions Linux. Dans cet exemple, sélectionnez SLES 12.
   3. Nom d’utilisateur et mot de passe d’administrateur  
      Un utilisateur pouvant être utilisé pour ouvrir une session sur la machine est créé.
   4. ID de sous-réseau  
      ID du sous-réseau auquel les machines virtuelles doivent être connectées. Laissez vide si vous souhaitez créer un réseau virtuel, ou sélectionnez le sous-réseau de votre VPN ou réseau virtuel Express Route pour connecter la machine virtuelle à votre réseau local. L’ID se présente généralement comme suit : /subscriptions/**&lt;ID_abonnement&gt;**/resourceGroups/**&lt;nom_groupe_ressources&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nom_réseau_virtuel&gt;**/subnets/**&lt;nom_sous_réseau&gt;**

### <a name="installation"></a>Installation

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Mettre à jour SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code>
   sudo ssh-keygen -tdsa
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

2. **[2**] Activer l’accès SSH

   <pre><code>
   sudo ssh-keygen -tdsa

   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Installer l’extension de haute disponibilité
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]** Configurer la résolution de nom d’hôte   

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   </code></pre>

1. **[1]** Installer le cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> ENTER
   # Multicast port [5405] -> ENTER
   # Do you wish to use SBD? [y/N] -> N
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Ajouter un nœud au cluster
   
   <pre><code> 
   sudo ha-cluster-join

   # WARNING: NTP is not configured to start at system boot.
   # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.10
   # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** Changer le mot de passe hacluster pour utiliser le même mot de passe

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** Configurer corosync pour utiliser les autres transports et ajouter la liste de nœuds Le cluster ne fonctionne pas dans le cas contraire.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Ajoutez le contenu en gras ci-dessous au fichier.
   
   <pre><code> 
   [...]
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-nfs-0</b>
      ring0_addr:10.0.0.5
     }
     node {
      # IP address of <b>prod-nfs-1</b>
      ring0_addr:10.0.0.6
     } 
   }</b>
   logging {
     [...]
   </code></pre>

   Redémarrez ensuite le service corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[A]** Installer les composants drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Créer une partition pour l’appareil drbd

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdc'
   </code></pre>

1. **[A]** Créer des configurations LVM

   <pre><code>
   sudo pvcreate /dev/sdc1   
   sudo vgcreate vg_NFS /dev/sdc1
   sudo lvcreate -l 100%FREE -n <b>NWS</b> vg_NFS
   </code></pre>

1. **[A]** Créer l’appareil drbd NFS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_nfs.res
   </code></pre>

   Insérer la configuration pour le nouvel appareil drbd et quitter

   <pre><code>
   resource <b>NWS</b>_nfs {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>prod-nfs-0</b> {
         address   <b>10.0.0.5</b>:7790;
         device    /dev/drbd0;
         disk      /dev/vg_NFS/NWS;
         meta-disk internal;
      }
      on <b>prod-nfs-1</b> {
         address   <b>10.0.0.6</b>:7790;
         device    /dev/drbd0;
         disk      /dev/vg_NFS/NWS;
         meta-disk internal;
      }
   }
   </code></pre>

   Créer l’appareil drbd et le démarrer

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_nfs
   sudo drbdadm up <b>NWS</b>_nfs
   </code></pre>

1. **[1]** Ignorer la synchronisation initiale

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_nfs
   </code></pre>

1. **[1]** Définir le nœud principal

   <pre><code>
   sudo drbdadm primary --force <b>NWS</b>_nfs
   </code></pre>

1. **[1]** Patienter jusqu’à ce que les nouveaux appareils drbd soient synchronisés

   <pre><code>
   sudo cat /proc/drbd

   # version: 8.4.6 (api:1/proto:86-101)
   # GIT-hash: 833d830e0152d1e457fa7856e71e11248ccf3f70 build by abuild@sheep14, 2016-05-09 23:14:56
   # 0: cs:Connected ro:Primary/Secondary ds:UpToDate/UpToDate C r-----
   #    ns:0 nr:0 dw:0 dr:912 al:8 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   </code></pre>

1. **[1]** Créer des systèmes de fichiers sur les appareils drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   </code></pre>


### <a name="configure-cluster-framework"></a>Configurer le framework du cluster

1. **[1]** Changer les paramètres par défaut

   <pre><code>
   sudo crm configure

   crm(live)configure# rsc_defaults resource-stickiness="1"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]** Ajouter l’appareil drbd NFS à la configuration de cluster

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_nfs drbd_<b>NWS</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]** Créer le serveur NFS

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive nfsserver \
     systemd:nfs-server \
     op monitor interval="30s"

   crm(live)configure# clone cl-nfsserver nfsserver interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]** Créer les ressources de système de fichiers NFS

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive fs_<b>NWS</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NWS</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# group g-<b>NWS</b>_nfs fs_<b>NWS</b>_sapmnt

   crm(live)configure# order o-<b>NWS</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NWS</b>_nfs:promote g-<b>NWS</b>_nfs:start
   
   crm(live)configure# colocation col-<b>NWS</b>_nfs_on_drbd inf: \
     g-<b>NWS</b>_nfs ms-drbd_<b>NWS</b>_nfs:Master

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]** Créer les exportations NFS

   <pre><code>
   sudo mkdir /srv/nfs/<b>NWS</b>/sidsys
   sudo mkdir /srv/nfs/<b>NWS</b>/sapmntsid
   sudo mkdir /srv/nfs/<b>NWS</b>/trans

   sudo crm configure

   crm(live)configure# primitive exportfs_<b>NWS</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NWS</b>" \
     options="rw,no_root_squash" \
     clientspec="*" fsid=0 \
     wait_for_leasetime_on_stop=true \
     op monitor interval="30s"

   crm(live)configure# modgroup g-<b>NWS</b>_nfs add exportfs_<b>NWS</b>

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’équilibreur de charge interne

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive vip_<b>NWS</b>_nfs IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_nfs anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 610<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0

   crm(live)configure# modgroup g-<b>NWS</b>_nfs add nc_<b>NWS</b>_nfs
   crm(live)configure# modgroup g-<b>NWS</b>_nfs add vip_<b>NWS</b>_nfs

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

### <a name="create-stonith-device"></a>Créer l’appareil STONITH

L’appareil STONITH utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service, effectuez les étapes suivantes.

1. Accédez à <https://portal.azure.com>
1. Ouvrez le panneau Azure Active Directory  
   Accédez aux propriétés et notez l’ID de répertoire. Il s’agit de **l’ID du locataire**.
1. Cliquez sur Inscriptions d’applications
1. Cliquez sur Ajouter.
1. Entrez un nom, sélectionnez le type d’application « Application web/API », entrez une URL de connexion (par exemple, http://localhost) et cliquez sur Créer
1. L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez la nouvelle application et cliquez sur Clés dans l’onglet Paramètres
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais » et cliquez sur Enregistrer
1. Notez la valeur. Cette valeur est utilisée comme **mot de passe** pour le principal de service
1. Notez l’ID de l’application. Cet identifiant est utilisé comme nom d’utilisateur (**ID de connexion** dans la procédure ci-dessous) du principal de service

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Vous devez accorder au principal de service les autorisations de démarrer et arrêter (libérer) toutes les machines virtuelles du cluster.

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Toutes les ressources
1. Sélectionnez la machine virtuelle
1. Cliquez sur Contrôle d’accès (IAM)
1. Cliquez sur Ajouter.
1. Sélectionnez le rôle de propriétaire
1. Entrez le nom de l’application que vous avez créée ci-dessus
1. Cliquez sur OK

#### <a name="1-create-the-stonith-devices"></a>**[1]**  Créer les appareils STONITH

Une fois que vous avez modifié les autorisations pour les machines virtuelles, vous pouvez configurer les appareils STONITH dans le cluster.

<pre><code>
sudo crm configure

# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password

crm(live)configure# primitive rsc_st_azure_1 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

crm(live)configure# primitive rsc_st_azure_2 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

crm(live)configure# colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started

crm(live)configure# commit
crm(live)configure# exit
</code></pre>

#### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Activer l’utilisation d’un appareil STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>

## <a name="setting-up-ascs"></a>Configuration de (A)SCS

### <a name="deploying-linux"></a>Déploiement de Linux

La Place de marché Azure contient une image de SUSE Linux Enterprise Server for SAP Applications 12 que vous pouvez utiliser pour déployer de nouvelles machines virtuelles. L’image Place de marché contient l’agent de ressource pour SAP NetWeaver.

Vous pouvez utiliser un des modèles de démarrage rapide disponibles sur github pour déployer toutes les ressources nécessaires. Le modèle déploie les machines virtuelles, l’équilibrage de charge, le groupe à haute disponibilité, etc. Suivez ces étapes pour déployer le modèle :

1. Ouvrez le [modèle ASCS/SCS Multi SID][template-multisid-xscs] ou le [modèle convergé][template-converged] dans le portail Azure. Le modèle ASCS/SCS crée uniquement les règles d’équilibrage de charge pour les instances de SAP NetWeaver ASCS/SCS et ERS (Linux uniquement), tandis que le modèle convergé crée également les règles d’équilibrage de charge pour une base de données (par exemple Microsoft SQL Server ou SAP HANA). Si vous prévoyez d’installer un système SAP NetWeaver et que vous souhaitez également installer la base de données sur les mêmes machines, utilisez le [modèle convergé][template-converged].
1. Entrez les paramètres suivants
   1. Préfixe de ressource (modèle ASCS/SCS Multi SID uniquement)  
      Entrez le préfixe à utiliser. Cette valeur sera utilisée comme préfixe pour les ressources déployées.
   3. ID du système SAP (modèle convergé uniquement)  
      Entrez l’ID du système SAP que vous souhaitez installer. Cet ID est utilisé comme préfixe pour les ressources déployées.
   4. Type de pile  
      Sélectionnez le type de pile de SAP NetWeaver
   5. Type de système d’exploitation  
      Sélectionnez l’une des distributions Linux. Dans cet exemple, sélectionnez SLES 12 BYOS
   6. Type de base de données  
      Sélectionnez HANA
   7. Taille du système SAP  
      Nombre de SAP fournis par le nouveau système. Si vous ne savez pas combien de SAP sont exigés par le système, demandez à votre partenaire technologique SAP ou un intégrateur système
   8. Disponibilité du système  
      Sélectionnez la haute disponibilité (HA).
   9. Nom d’utilisateur et mot de passe d’administrateur  
      Un utilisateur pouvant être utilisé pour ouvrir une session sur la machine est créé.
   10. ID de sous-réseau  
   ID du sous-réseau auquel les machines virtuelles doivent être connectées.  Laissez vide si vous souhaitez créer un réseau virtuel, ou sélectionnez le même sous-réseau que celui que vous avez utilisé ou créé dans le cadre du déploiement de serveur NFS. L’ID se présente généralement comme suit : /subscriptions/**&lt;ID_abonnement&gt;**/resourceGroups/**&lt;nom_groupe_ressources&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nom_réseau_virtuel&gt;**/subnets/**&lt;nom_sous_réseau&gt;**

### <a name="installation"></a>Installation

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Mettre à jour SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code>
   sudo ssh-keygen -tdsa
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

2. **[2**] Activer l’accès SSH

   <pre><code>
   sudo ssh-keygen -tdsa

   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Installer l’extension de haute disponibilité
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]** Mettre à jour les agents de ressources SAP  
   
   Un correctif pour les agents de ressources est nécessaire pour utiliser la nouvelle configuration décrite dans cet article. Vous pouvez vérifier si le correctif est déjà installé avec la commande suivante.

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   La sortie doit ressembler à ce qui suit :

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Si la commande grep ne trouve pas le paramètre IS_ERS, vous devez installer le correctif logiciel répertorié dans la [page de téléchargement SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents).

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Configurer la résolution de nom d’hôte   

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.10 nws-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.11 nws-ers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.12 nws-db</b>
   </code></pre>

1. **[1]** Installer le cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> ENTER
   # Multicast port [5405] -> ENTER
   # Do you wish to use SBD? [y/N] -> N
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Ajouter un nœud au cluster
   
   <pre><code> 
   sudo ha-cluster-join

   # WARNING: NTP is not configured to start at system boot.
   # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.10
   # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** Changer le mot de passe hacluster pour utiliser le même mot de passe

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** Configurer corosync pour utiliser les autres transports et ajouter la liste de nœuds Le cluster ne fonctionne pas dans le cas contraire.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Ajoutez le contenu en gras ci-dessous au fichier.
   
   <pre><code> 
   [...]
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>nws-cl-0</b>
      ring0_addr:     10.0.0.14
     }
     node {
      # IP address of <b>nws-cl-1</b>
      ring0_addr:     10.0.0.13
     } 
   }</b>
   logging {
     [...]
   </code></pre>

   Redémarrez ensuite le service corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[A]** Installer les composants drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Créer une partition pour l’appareil drbd

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdc'
   </code></pre>

1. **[A]** Créer des configurations LVM

   <pre><code>
   sudo pvcreate /dev/sdc1   
   sudo vgcreate vg_<b>NWS</b> /dev/sdc1
   sudo lvcreate -l 50%FREE -n <b>NWS</b>_ASCS vg_<b>NWS</b>
   sudo lvcreate -l 50%FREE -n <b>NWS</b>_ERS vg_<b>NWS</b>
   </code></pre>

1. **[A]** Créer l’appareil drbd SCS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_ascs.res
   </code></pre>

   Insérer la configuration pour le nouvel appareil drbd et quitter

   <pre><code>
   resource <b>NWS</b>_ascs {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>nws-cl-0</b> {
         address   <b>10.0.0.14</b>:7791;
         device    /dev/drbd0;
         disk      /dev/vg_NWS/NWS_ASCS;
         meta-disk internal;
      }
      on <b>nws-cl-1</b> {
         address   <b>10.0.0.13</b>:7791;
         device    /dev/drbd0;
         disk      /dev/vg_NWS/NWS_ASCS;
         meta-disk internal;
      }
   }
   </code></pre>

   Créer l’appareil drbd et le démarrer

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_ascs
   sudo drbdadm up <b>NWS</b>_ascs
   </code></pre>

1. **[A]** Créer l’appareil drbd ERS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_ers.res
   </code></pre>

   Insérer la configuration pour le nouvel appareil drbd et quitter

   <pre><code>
   resource <b>NWS</b>_ers {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>nws-cl-0</b> {
         address   <b>10.0.0.14</b>:7792;
         device    /dev/drbd1;
         disk      /dev/vg_NWS/NWS_ERS;
         meta-disk internal;
      }
      on <b>nws-cl-1</b> {
         address   <b>10.0.0.13</b>:7792;
         device    /dev/drbd1;
         disk      /dev/vg_NWS/NWS_ERS;
         meta-disk internal;
      }
   }
   </code></pre>

   Créer l’appareil drbd et le démarrer

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_ers
   sudo drbdadm up <b>NWS</b>_ers
   </code></pre>

1. **[1]** Ignorer la synchronisation initiale

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_ascs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_ers
   </code></pre>

1. **[1]** Définir le nœud principal

   <pre><code>
   sudo drbdadm primary --force <b>NWS</b>_ascs
   sudo drbdadm primary --force <b>NWS</b>_ers
   </code></pre>

1. **[1]** Patienter jusqu’à ce que les nouveaux appareils drbd soient synchronisés

   <pre><code>
   sudo cat /proc/drbd

   # version: 8.4.6 (api:1/proto:86-101)
   # GIT-hash: 833d830e0152d1e457fa7856e71e11248ccf3f70 build by abuild@sheep14, 2016-05-09 23:14:56
   # 0: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:93991268 nr:0 dw:93991268 dr:93944920 al:383 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   # 1: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:6047920 nr:0 dw:6047920 dr:6039112 al:34 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   # 2: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:5142732 nr:0 dw:5142732 dr:5133924 al:30 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   </code></pre>

1. **[1]** Créer des systèmes de fichiers sur les appareils drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkfs.xfs /dev/drbd1
   </code></pre>


### <a name="configure-cluster-framework"></a>Configurer le framework du cluster

**[1]** Changer les paramètres par défaut

   <pre><code>
   sudo crm configure

   crm(live)configure# rsc_defaults resource-stickiness="1"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Préparer l’installation de SAP NetWeaver

1. **[A]** Créer les répertoires partagés

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NWS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NWS</b>/SYS

   sudo chattr +i /sapmnt/<b>NWS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NWS</b>/SYS
   </code></pre>

1. **[A]** Configurer autofs
 
   <pre><code>
   sudo vi /etc/auto.master

   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Créer un fichier avec

   <pre><code>
   sudo vi /etc/auto.direct

   # Add the following lines to the file, save and exit
   /sapmnt/<b>NWS</b> -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/trans
   /usr/sap/<b>NWS</b>/SYS -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sidsys
   </code></pre>

   Redémarrer autofs pour monter les nouveaux partages

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Configurer le fichier SWAP
 
   <pre><code>
   sudo vi /etc/waagent.conf

   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>

   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Redémarrer l’Agent pour activer la modification

   <pre><code>
   sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>Installation de SAP NetWeaver ASC/ERS

1. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’équilibreur de charge interne

   <pre><code>
   sudo crm node standby <b>nws-cl-1</b>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_ASCS \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_ascs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_ASCS drbd_<b>NWS</b>_ASCS \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true"

   crm(live)configure# primitive fs_<b>NWS</b>_ASCS \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/usr/sap/<b>NWS</b>/ASCS<b>00</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# primitive vip_<b>NWS</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.10</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   crm(live)configure# group g-<b>NWS</b>_ASCS nc_<b>NWS</b>_ASCS vip_<b>NWS</b>_ASCS fs_<b>NWS</b>_ASCS \
      meta resource-stickiness=3000

   crm(live)configure# order o-<b>NWS</b>_drbd_before_ASCS inf: \
     ms-drbd_<b>NWS</b>_ASCS:promote g-<b>NWS</b>_ASCS:start
   
   crm(live)configure# colocation col-<b>NWS</b>_ASCS_on_drbd inf: \
     ms-drbd_<b>NWS</b>_ASCS:Master g-<b>NWS</b>_ASCS
   
   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

   <pre><code>
   sudo crm_mon -r

   # Node nws-cl-1: standby
   # <b>Online: [ nws-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      Stopped: [ nws-cl-1 ]
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   </code></pre>

1. **[1]** Installer SAP NetWeaver ASCS  

   Installez SAP NetWeaver ASCS comme racine sur le premier nœud à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend de l’équilibreur de charge pour l’ASCS, par exemple <b>nws-ascs</b>, <b>10.0.0.10</b>, et du numéro d’instance que vous avez utilisé pour la sonde de l’équilibreur de charge, par exemple <b>00</b>.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. **[1]** Créer une ressource IP virtuelle et la sonde d’intégrité pour l’équilibreur de charge interne

   <pre><code>
   sudo crm node standby <b>nws-cl-0</b>
   sudo crm node online <b>nws-cl-1</b>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_ERS \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_ers" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_ERS drbd_<b>NWS</b>_ERS \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true"

   crm(live)configure# primitive fs_<b>NWS</b>_ERS \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/usr/sap/<b>NWS</b>/ERS<b>02</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# primitive vip_<b>NWS</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.11</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0

   crm(live)configure# group g-<b>NWS</b>_ERS nc_<b>NWS</b>_ERS vip_<b>NWS</b>_ERS fs_<b>NWS</b>_ERS

   crm(live)configure# order o-<b>NWS</b>_drbd_before_ERS inf: \
     ms-drbd_<b>NWS</b>_ERS:promote g-<b>NWS</b>_ERS:start
   
   crm(live)configure# colocation col-<b>NWS</b>_ERS_on_drbd inf: \
     ms-drbd_<b>NWS</b>_ERS:Master g-<b>NWS</b>_ERS
   
   crm(live)configure# commit
   # WARNING: Resources nc_NWS_ASCS,nc_NWS_ERS,nc_NWS_nfs violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y

   crm(live)configure# exit
   
   </code></pre>
 
   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

   <pre><code>
   sudo crm_mon -r

   # Node <b>nws-cl-0: standby</b>
   # <b>Online: [ nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      Stopped: [ nws-cl-0 ]
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      Stopped: [ nws-cl-0 ]
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   </code></pre>

1. **[2]** Installer SAP NetWeaver ERS  

   Installez SAP NetWeaver ERS comme racine sur le deuxième nœud à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend de l’équilibreur de charge pour l’ERS, par exemple <b>nws-ers</b>, <b>10.0.0.11</b>, et du numéro d’instance que vous avez utilisé pour la sonde de l’équilibreur de charge, par exemple <b>02</b>.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Utilisez SWPM SP 20 PL 05 ou ultérieur. Les versions antérieures ne définissent pas les autorisations correctement et l’installation échouera.
   > 

1. **[1]** Adapter les profils d’instance ASCS/SCS et ERS
 
   * Profil ASCS/SCS

   <pre><code> 
   sudo vi /sapmnt/<b>NWS</b>/profile/<b>NWS</b>_<b>ASCS00</b>_<b>nws-ascs</b>

   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)

   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector

   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil ERS

   <pre><code> 
   sudo vi /sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b>

   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]** Configurer Keep Alive

   La communication entre le serveur d’applications SAP NetWeaver et l’ASCS/SCS est routée par l’intermédiaire d’un équilibreur de charge logiciel. L’équilibreur de charge déconnecte les connexions inactives après un délai configurable. Pour éviter ce problème, vous devez définir un paramètre dans le profil SAP NetWeaver ASCS/SCS et changer les paramètres système de Linux. Pour plus d’informations, consultez la [Note SAP 1410736][1410736].
   
   Le paramètre de profil ASCS/SCS enque/encni/set_so_keepalive a déjà été ajouté lors de la dernière étape.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Configurer les utilisateurs SAP après l’installation
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nws</b>adm   
   </code></pre>

1. **[1]** Ajouter les services ASCS et ERS SAP au fichier sapservice

   Ajoutez l’entrée de service ASCS au deuxième nœud et copiez l’entrée de service ERS dans le premier nœud.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nws-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nws-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Créer les ressources de cluster SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"

   sudo crm configure

   crm(live)configure# primitive rsc_sap_<b>NWS</b>_ASCS<b>00</b> SAPInstance \
    operations $id=rsc_sap_<b>NWS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NWS</b>_ASCS<b>00</b>_<b>nws-ascs</b> START_PROFILE="/sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ASCS<b>00</b>_<b>nws-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10

   crm(live)configure# primitive rsc_sap_<b>NWS</b>_ERS<b>02</b> SAPInstance \
    operations $id=rsc_sap_<b>NWS</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b> START_PROFILE="/sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000

   crm(live)configure# modgroup g-<b>NWS</b>_ASCS add rsc_sap_<b>NWS</b>_ASCS<b>00</b>
   crm(live)configure# modgroup g-<b>NWS</b>_ERS add rsc_sap_<b>NWS</b>_ERS<b>02</b>

   crm(live)configure# colocation col_sap_<b>NWS</b>_no_both -5000: g-<b>NWS</b>_ERS g-<b>NWS</b>_ASCS
   crm(live)configure# location loc_sap_<b>NWS</b>_failover_to_ers rsc_sap_<b>NWS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NWS</b> eq 1
   crm(live)configure# order ord_sap_<b>NWS</b>_first_start_ascs Optional: rsc_sap_<b>NWS</b>_ASCS<b>00</b>:start rsc_sap_<b>NWS</b>_ERS<b>02</b>:stop symmetrical=false

   crm(live)configure# commit
   crm(live)configure# exit

   sudo crm configure property maintenance-mode="false"
   sudo crm node online <b>nws-cl-0</b>
   </code></pre>

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

   <pre><code>
   sudo crm_mon -r

   # Online: <b>[ nws-cl-0 nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   #      rsc_sap_NWS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-0</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      <b>Slaves: [ nws-cl-0 ]</b>
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #      rsc_sap_NWS_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-1</b>
   </code></pre>

### <a name="create-stonith-device"></a>Créer l’appareil STONITH

L’appareil STONITH utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service, effectuez les étapes suivantes.

1. Accédez à <https://portal.azure.com>
1. Ouvrez le panneau Azure Active Directory  
   Accédez aux propriétés et notez l’ID de répertoire. Il s’agit de **l’ID du locataire**.
1. Cliquez sur Inscriptions d’applications
1. Cliquez sur Ajouter.
1. Entrez un nom, sélectionnez le type d’application « Application web/API », entrez une URL de connexion (par exemple, http://localhost) et cliquez sur Créer
1. L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez la nouvelle application et cliquez sur Clés dans l’onglet Paramètres
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais » et cliquez sur Enregistrer
1. Notez la valeur. Cette valeur est utilisée comme **mot de passe** pour le principal de service
1. Notez l’ID de l’application. Cet identifiant est utilisé comme nom d’utilisateur (**ID de connexion** dans la procédure ci-dessous) du principal de service

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Vous devez accorder au principal de service les autorisations de démarrer et arrêter (libérer) toutes les machines virtuelles du cluster.

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Toutes les ressources
1. Sélectionnez la machine virtuelle
1. Cliquez sur Contrôle d’accès (IAM)
1. Cliquez sur Ajouter.
1. Sélectionnez le rôle de propriétaire
1. Entrez le nom de l’application que vous avez créée ci-dessus
1. Cliquez sur OK

#### <a name="1-create-the-stonith-devices"></a>**[1]**  Créer les appareils STONITH

Une fois que vous avez modifié les autorisations pour les machines virtuelles, vous pouvez configurer les appareils STONITH dans le cluster.

<pre><code>
sudo crm configure

# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password

crm(live)configure# primitive rsc_st_azure_1 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

crm(live)configure# primitive rsc_st_azure_2 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

crm(live)configure# colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started

crm(live)configure# commit
crm(live)configure# exit
</code></pre>

#### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Activer l’utilisation d’un appareil STONITH

Activer l’utilisation d’un appareil STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>

## <a name="install-database"></a>Installer la base de données

Dans cet exemple, une réplication du système SAP HANA est installée et configurée. SAP HANA s’exécute dans le même cluster que SAP NetWeaver ASCS/SCS et ERS. Vous pouvez également installer SAP HANA dans un cluster dédié. Pour plus d’informations, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha].

### <a name="prepare-for-sap-hana-installation"></a>Préparer l’installation de SAP HANA

En général, nous recommandons d’utiliser LVM pour les volumes qui stockent des données et des fichiers journaux. À des fins de test, vous pouvez également choisir de stocker les données et les fichiers journaux directement sur un disque simple.

1. **[A]** LVM  
   L’exemple ci-dessous part du principe que les machines virtuelles disposent de quatre disques de données qui doivent être utilisés pour créer deux volumes.
   
   Créez des volumes physiques pour tous les disques que vous souhaitez utiliser.
   
   <pre><code>
   sudo pvcreate /dev/sdd
   sudo pvcreate /dev/sde
   sudo pvcreate /dev/sdf
   sudo pvcreate /dev/sdg
   </code></pre>
   
   Créez un groupe de volumes pour les fichiers de données, un groupe de volumes pour les fichiers journaux et l’autre pour le répertoire partagé de SAP HANA
   
   <pre><code>
   sudo vgcreate vg_hana_data /dev/sdd /dev/sde
   sudo vgcreate vg_hana_log /dev/sdf
   sudo vgcreate vg_hana_shared /dev/sdg
   </code></pre>
   
   Créez les volumes logiques
   
   <pre><code>
   sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
   sudo mkfs.xfs /dev/vg_hana_data/hana_data
   sudo mkfs.xfs /dev/vg_hana_log/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
   </code></pre>
   
   Créez les répertoires de montage et copiez l’UUID de tous les volumes logiques
   
   <pre><code>
   sudo mkdir -p /hana/data
   sudo mkdir -p /hana/log
   sudo mkdir -p /hana/shared
   sudo chattr +i /hana/data
   sudo chattr +i /hana/log
   sudo chattr +i /hana/shared
   # write down the ID of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
   sudo blkid
   </code></pre>
   
   Créez des entrées autofs pour les trois volumes logiques
   
   <pre><code>
   sudo vi /etc/auto.direct
   </code></pre>
   
   Insérez cette ligne dans sudo vi /etc/auto.direct
   
   <pre><code>
   /hana/data -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b>
   /hana/log -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b>
   /hana/shared -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b>
   </code></pre>
   
   Montez les nouveaux volumes
   
   <pre><code>
   sudo service autofs restart 
   </code></pre>

1. **[A]** Disques simples  

   Pour les petits systèmes ou les systèmes de démonstration, vous pouvez placer vos données et fichiers journaux HANA sur un disque. Les commandes suivantes permettent de créer une partition au format xfs sur /dev/sdc.
   ```bash
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdd'
   sudo mkfs.xfs /dev/sdd1
   
   # write down the id of /dev/sdd1
   sudo /sbin/blkid
   sudo vi /etc/auto.direct
   ```
   
   Insérez cette ligne dans /etc/auto.direct
   <pre><code>
   /hana -fstype=xfs :UUID=<b>&lt;UUID&gt;</b>
   </code></pre>
   
   Créez le répertoire cible et montez le disque.
   
   <pre><code>
   sudo mkdir /hana
   sudo chattr +i /hana
   sudo service autofs restart
   </code></pre>

### <a name="installing-sap-hana"></a>Installation de SAP HANA

Les étapes suivantes se basent sur le chapitre 4 de la publication [SAP HANA SR Performance Optimized Scenario][suse-hana-ha-guide] (Scénario d’optimisation des performances de réplication système de SAP HANA) pour installer la réplication système SAP HANA. Veuillez lire ce document avant de poursuivre l’installation.

1. **[A]** Exécuter hdblcm à partir du DVD HANA
   
   <pre><code>
   sudo hdblcm --sid=<b>HDB</b> --number=<b>03</b> --action=install --batch --password=<b>&lt;password&gt;</b> --system_user_password=<b>&lt;password for system user&gt;</b>

   sudo /hana/shared/<b>HDB</b>/hdblcm/hdblcm --action=configure_internal_network --listen_interface=internal --internal_network=<b>10.0.0/24</b> --password=<b>&lt;password for system user&gt;</b> --batch
   </code></pre>

1. **[A]** Mettre à niveau l’agent hôte SAP

   Téléchargez la dernière archive de l’agent hôte SAP à partir du [SAP Softwarecenter][sap-swcenter] et exécutez la commande suivante pour mettre à niveau l’agent. Remplacez le chemin d’accès à l’archive pour pointer vers le fichier que vous avez téléchargé.
   <pre><code>
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <b>&lt;path to SAP Host Agent SAR&gt;</b> 
   </code></pre>

1. **[1]** Créer la réplication HANA (en tant que racine)  

   Exécutez la commande ci-dessous. Veillez à remplacer les chaînes en gras (ID du système HANA HDB et numéro d’instance 03) par les valeurs de votre installation SAP HANA.
   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
   </code></pre>

1. **[A]** Créer l’entrée keystore (en tant que racine)

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>&lt;passwd&gt;</b>
   </code></pre>

1. **[1]** Sauvegarder la base de données (en tant que racine)

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
   </code></pre>

1. **[1]** Basculer vers l’utilisateur sapsid HANA et créer le site principal.

   <pre><code>
   su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Basculer vers l’utilisateur sapsid HANA et créer le site secondaire.

   <pre><code>
   su - <b>hdb</b>adm
   sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>nws-cl-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

1. **[1]** Créer les ressources de cluster SAP HANA

   Tout d’abord, créez la topologie.
   
   <pre><code>
   sudo crm configure

   # replace the bold string with your instance number and HANA system ID
   
   crm(live)configure# primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b>   ocf:suse:SAPHanaTopology \
     operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"
    
   crm(live)configure# clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>
   
   Ensuite, créez les ressources HANA.
   
   <pre><code>
   sudo crm configure

   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
    
   crm(live)configure# primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
    
   crm(live)configure# ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
    
   crm(live)configure# primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
     meta target-role="Started" is-managed="true" \ 
     operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
     op monitor interval="10s" timeout="20s" \ 
     params ip="<b>10.0.0.12</b>" 

   crm(live)configure# primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
     op monitor timeout=20s interval=10 depth=0 

   crm(live)configure# group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
    
   crm(live)configure# colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
     msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  

   crm(live)configure# order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
     msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
    
   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

   <pre><code>
   sudo crm_mon -r

   # <b>Online: [ nws-cl-0 nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      <b>Slaves: [ nws-cl-0 ]</b>
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #      rsc_sap_NWS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-1</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   #      rsc_sap_NWS_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-0</b>
   #  Clone Set: cln_SAPHanaTopology_HDB_HDB03 [rsc_SAPHanaTopology_HDB_HDB03]
   #      <b>Started: [ nws-cl-0 nws-cl-1 ]</b>
   #  Master/Slave Set: msl_SAPHana_HDB_HDB03 [rsc_SAPHana_HDB_HDB03]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g_ip_HDB_HDB03
   #      rsc_ip_HDB_HDB03   (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      rsc_nc_HDB_HDB03   (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   # rsc_st_azure_1  (stonith:fence_azure_arm):      <b>Started nws-cl-0</b>
   # rsc_st_azure_2  (stonith:fence_azure_arm):      <b>Started nws-cl-1</b>
   </code></pre>

1. **[1]** Installer l’instance de base de données SAP NetWeaver

   Installez l’instance de base de données SAP NetWeaver en tant que racine à l’aide d’un nom d’hôte virtuel mappé à l’adresse IP de la configuration frontend d’équilibreur de charge pour la base de données, par exemple <b>nws-db</b> et <b>10.0.0.12</b>.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installation de serveur d’applications SAP NetWeaver

Suivez ces étapes pour installer un serveur d’applications SAP. Les étapes ci-dessous partent du principe que vous installez le serveur d’applications sur un serveur différent des serveurs ASCS/SCS et HANA. Dans le cas contraire, certaines des étapes ci-dessous (par exemple la configuration de la résolution de nom d’hôte) ne sont pas nécessaires.

1. Configurer la résolution de nom d’hôte    
   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes
   ```bash
   sudo vi /etc/hosts
   ```
   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.10 nws-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.11 nws-ers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.12 nws-db</b>
   # IP address of the application server
   <b>10.0.0.8 nws-di-0</b>
   </code></pre>

1. Créer le répertoire sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NWS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NWS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Configurer autofs
 
   <pre><code>
   sudo vi /etc/auto.master

   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Créer un fichier avec

   <pre><code>
   sudo vi /etc/auto.direct

   # Add the following lines to the file, save and exit
   /sapmnt/<b>NWS</b> -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/trans
   </code></pre>

   Redémarrer autofs pour monter les nouveaux partages

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Configurer le fichier SWAP
 
   <pre><code>
   sudo vi /etc/waagent.conf

   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>

   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Redémarrer l’Agent pour activer la modification

   <pre><code>
   sudo service waagent restart
   </code></pre>

1. Installer le serveur d’applications SAP NetWeaver

   Installer un serveur d’applications SAP NetWeaver principal ou supplémentaire.

   Vous pouvez utiliser le paramètre sapinst SAPINST_REMOTE_ACCESS_USER pour autoriser un utilisateur non racine à se connecter à sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Mettre à jour la banque d’informations sécurisée SAP HANA

   Mettez à jour la banque d’informations sécurisée SAP HANA pour qu’elle pointe vers le nom virtuel de la configuration de la réplication système SAP HANA.
   <pre><code>
   su - <b>nws</b>adm
   hdbuserstore SET DEFAULT <b>nws-db</b>:3<b>03</b>15 <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Étapes suivantes
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha].
