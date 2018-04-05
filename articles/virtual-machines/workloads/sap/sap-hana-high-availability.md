---
title: Configuration de la réplication système SAP HANA sur les machines virtuelles Azure | Microsoft Docs
description: Créer une haute disponibilité de SAP HANA sur des machines virtuelles Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: b84b523f919e6b253462139b6888e5eb16248084
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Haute disponibilité de SAP HANA sur des machines virtuelles Azure

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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

En local, vous pouvez utiliser soit la réplication système HANA soit un stockage partagé pour établir une haute disponibilité pour SAP HANA.
Sur les machines virtuelles Azure, la réplication système SAP HANA dans Azure est la seule fonction de haute disponibilité prise en charge actuellement. La réplication SAP HANA se compose d’un nœud principal et d’au moins un nœud secondaire. Les modifications apportées aux données sur le nœud principal sont répliquées vers les nœuds secondaires de manière synchrone ou asynchrone.

Cet article décrit comment déployer et configurer les machines virtuelles, installer l’infrastructure de cluster, et installer et configurer la réplication système SAP HANA.
Les exemples de configuration et les commandes d’installation utilisent le numéro d’instance 03 et l’ID système HANA HN1.

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
* [Scénario d’optimisation des performances de réplication système de SAP HANA][suse-hana-ha-guide] Le guide contient toutes les informations nécessaires pour configurer la réplication système SAP HANA locale. Utilisez ce guide comme référence.

## <a name="overview"></a>Vue d'ensemble

Pour atteindre la haute disponibilité, SAP HANA est installé sur deux machines virtuelles. Les données sont répliquées à l’aide de la réplication de système HANA.

![Présentation de la haute disponibilité de SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Le programme d’installation de SAP HANA SR utilise un nom d’hôte virtuel et des adresses IP virtuelles. Sur Azure, un équilibreur de charge est nécessaire pour utiliser une adresse IP virtuelle. La liste suivante illustre la configuration de l’équilibreur de charge.

* Configuration du frontend
  * Adresse IP 10.0.0.13 pour hn1-db
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie de la réplication de système HANA
* Port de la sonde
  * Port 62503
* Règles d’équilibrage de charge
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Déploiement de Linux

L’agent de ressource pour SAP HANA est inclus dans SUSE Linux Enterprise Server for SAP Applications.
La Place de marché Azure contient une image de SUSE Linux Enterprise Server for SAP Applications 12 que vous pouvez utiliser pour déployer de nouvelles machines virtuelles.

### <a name="deploy-with-template"></a>Déployer avec un modèle
Vous pouvez utiliser un des modèles de démarrage rapide disponibles sur github pour déployer toutes les ressources nécessaires. Le modèle déploie les machines virtuelles, l’équilibrage de charge, le groupe à haute disponibilité, etc. Suivez ces étapes pour déployer le modèle :

1. Ouvrez le [modèle de base de données][template-multisid-db] ou [modèle convergé][template-converged] sur le portail Azure. 
   Le modèle de base de données crée uniquement les règles d’équilibrage de charge pour une base de données, tandis que le modèle convergé crée également les règles d’équilibrage de charge pour une instance ASCS/SCS et une instance ERS (Linux uniquement). Si vous prévoyez d’installer un système SAP NetWeaver et souhaitez également installer l’instance ASC/SCS sur les mêmes machines, utilisez le [modèle convergé][template-converged].
1. Entrez les paramètres suivants
    1. ID du système SAP  
       Entrez l’ID du système SAP que vous souhaitez installer. Cet ID est utilisé comme préfixe pour les ressources déployées.
    1. Type de pile (applicable uniquement si vous utilisez le modèle convergé)   
       Sélectionnez le type de pile de SAP NetWeaver
    1. Type de système d’exploitation  
       Sélectionnez l’une des distributions Linux. Dans cet exemple, sélectionnez SLES 12.
    1. Type de base de données  
       Sélectionnez HANA
    1. Taille du système SAP  
       La quantité de SAP que le nouveau système va fournir. Si vous ne savez pas combien de SAP sont exigés par le système, demandez à votre partenaire technologique SAP ou un intégrateur système
    1. Disponibilité du système  
       Sélectionnez la haute disponibilité (HA).
    1. Nom d’utilisateur et mot de passe d’administrateur  
       Un utilisateur pouvant être utilisé pour ouvrir une session sur la machine est créé.
    1. Sous-réseau nouveau ou existant  
       Détermine s’il faut créer un réseau virtuel et un sous-réseau, ou utiliser un sous-réseau existant. Si vous disposez déjà d’un réseau virtuel connecté à votre réseau local, sélectionnez existant.
    1. ID de sous-réseau  
    ID du sous-réseau auquel les machines virtuelles doivent être connectées. Sélectionnez le sous-réseau de votre VPN ou réseau virtuel Express Route pour connecter la machine virtuelle à votre réseau local. L’identifiant se présente généralement comme suit : /abonnements/`<subscription ID`>/groupesderessources/`<resource group name`>/fournisseurs/Réseau.Microsoft/réseauxVirtuels/`<virtual network name`>/sous-réseaux/`<subnet name`>

### <a name="manual-deployment"></a>Déploiement manuel

1. Création d’un groupe de ressources
1. Création d'un réseau virtuel
1. Créer un groupe à haute disponibilité  
   Définir un domaine de mise à jour maximal
1. Créer un équilibrage de charge (interne)  
   Sélectionner le réseau virtuel créé à la deuxième étape
1. Créer la machine virtuelle 1  
   Utiliser au minimum la version SLES4SAP 12 SP1. Dans cet exemple, nous allons utiliser l’image SLES4SAP 12 SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES for SAP 12 SP2 (Premium)  
   Sélectionner le groupe à haute disponibilité créé précédemment  
1. Créer la machine virtuelle 2  
   Utiliser au minimum la version SLES4SAP 12 SP1. Dans cet exemple, nous allons utiliser l’image BYOS SLES4SAP 12 SP1 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES for SAP 12 SP2 (Premium)  
   Sélectionner le groupe à haute disponibilité créé précédemment  
1. Ajouter des disques de données
1. Configurer l’équilibrage de charge
    1. Créer un pool d’adresse IP frontal
        1. Ouvrir l’équilibrage de charge, sélectionner le pool d’adresses IP frontal et cliquer sur Ajouter
        1. Entrer le nom du nouveau pool d’adresses IP frontal (par exemple hana-frontend)
        1. Définir l’affectation sur Statique et entrer l’adresse IP (par exemple **10.0.0.13**)
        1. Cliquez sur OK
        1. Une fois le pool d’adresses IP frontal créé, noter son adresse IP
    1. Créer un pool principal
        1. Ouvrir l’équilibrage de charge, sélectionner les pools principaux et cliquer sur Ajouter
        1. Entrer le nom du nouveau pool principal (par exemple hana-backend)
        1. Cliquer sur Ajouter une machine virtuelle
        1. Sélectionner le groupe à haute disponibilité créé précédemment
        1. Sélectionner les machines virtuelles du cluster SAP HANA
        1. Cliquez sur OK
    1. Créer une sonde d’intégrité
        1. Ouvrir l’équilibrage de charge, sélectionner les sondes d’intégrité et cliquer sur Ajouter
        1. Entrer le nom de la nouvelle sonde d’intégrité (par exemple hana-hp)
        1. Sélectionner le protocole TCP et le port 625**03**, et conserver un intervalle de 5 et un seuil de défaillance de 2
        1. Cliquez sur OK
    1. Créer des règles d’équilibrage de charge
        1. Ouvrir l’équilibrage de charge, sélectionner les règles d’équilibrage de charge et cliquer sur Ajouter
        1. Entrer le nom de la nouvelle règle d’équilibrage de charge (par exemple hana-lb-3**03**15)
        1. Sélectionner l’adresse IP du serveur frontal, le pool principal et la sonde d’intégrité créés précédemment (par exemple hana-frontend)
        1. Conserver le protocole TCP et choisir le port 3**03**13
        1. Augmenter le délai d’inactivité à 30 minutes
        1. **Veiller à activer IP flottante**
        1. Cliquez sur OK
        1. Répéter les étapes ci-dessus pour les ports 3**03**15 et 3**03**17

## <a name="create-pacemaker-cluster"></a>Créer le cluster Pacemaker

Suivez les étapes décrites à la page [Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](high-availability-guide-suse-pacemaker.md) pour créer un cluster Pacemaker de base pour ce serveur HANA. Vous pouvez également utiliser le même cluster Pacemaker pour SAP HANA et SAP NetWeaver (A)SCS.

## <a name="installing-sap-hana"></a>Installation de SAP HANA

Les éléments suivants sont précédés de **[A]** \(applicable à tous les nœuds), de **[1]** \(applicable uniquement au nœud 1) ou de **[2]** \(applicable uniquement au nœud 2 du cluster Pacemaker).

1. **[A]** Configurer la disposition du disque
    1. LVM  
       
       En général, nous recommandons d’utiliser LVM pour les volumes qui stockent des données et des fichiers journaux. L’exemple suivant part du principe que les machines virtuelles disposent de quatre disques de données joints qui doivent être utilisés pour créer deux volumes.

       Répertorier tous les disques disponibles
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Exemple de sortie
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Créez des volumes physiques pour tous les disques que vous souhaitez utiliser.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Créez un groupe de volumes pour les fichiers de données, un groupe de volumes pour les fichiers journaux et l’autre pour le répertoire partagé de SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       Créez les volumes logiques

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Créez les répertoires de montage et copiez l’UUID de tous les volumes logiques
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Créez des entrées fstab pour les trois volumes logiques
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Insérez cette ligne dans /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Montez les nouveaux volumes
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Disques simples  
       Pour les systèmes de démonstration, vous pouvez placer vos données et fichiers journaux HANA sur un disque. Les commandes suivantes permettent de créer une partition au format xfs sur /dev/disk/azure/scsi1/lun0.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Insérez cette ligne dans /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Créez le répertoire cible et montez le disque.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** Configurer la résolution de nom d’hôte pour tous les hôtes  
    Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes
    ```bash
    sudo vi /etc/hosts
    ```
    Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Installer les packages haute disponibilité HANA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Consultez le chapitre 4 de la publication [SAP HANA SR Performance Optimized Scenario guide][suse-hana-ha-guide] (Scénario d’optimisation des performances de réplication système de SAP HANA) pour installer la réplication système SAP HANA.

1. **[A]** Exécuter hdblcm à partir du DVD HANA
    * Choose installation -> 1
    * Select additional components for installation -> 1
    * Enter Installation Path [/hana/shared]: -> ENTRÉE
    * Enter Local Host Name [..]: -> ENTRÉE
    * Do you want to add additional hosts to the system? (y/n) [n] : -> ENTRÉE
    * Enter SAP HANA System ID : <SID of HANA e.g. HN1>
    * Enter Instance Number [00] :   
  Numéro d’instance HANA. Utilisez 03 si vous avez utilisé le modèle Azure ou si vous avez suivi le déploiement manuel
    * Select Database Mode / Enter Index [1] : -> ENTRÉE
    * Select System Usage / Enter Index [4] :  
  Select the system Usage
    * Enter Location of Data Volumes [/hana/data/HN1] : -> ENTRÉE
    * Enter Location of Log Volumes [/hana/log/HN1] : -> ENTRÉE
    * Restrict maximum memory allocation? [n] : -> ENTRÉE
    * Enter Certificate Host Name For Host '...' [...]: -> ENTER
    * Enter SAP Host Agent User (sapadm) Password:
    * Confirm SAP Host Agent User (sapadm) Password:
    * Enter System Administrator (hdbadm) Password:
    * Confirm System Administrator (hdbadm) Password:
    * Enter System Administrator Home Directory [/usr/sap/HN1/home] : -> ENTRÉE
    * Enter System Administrator Login Shell [/bin/sh] : -> ENTRÉE
    * Enter System Administrator User ID [1001] : -> ENTRÉE
    * Enter ID of User Group (sapsys) [79] : -> ENTRÉE
    * Enter Database User (SYSTEM) Password:
    * Confirm Database User (SYSTEM) Password:
    * Restart system after machine reboot? [n] : -> ENTRÉE
    * Do you want to continue? (y/n) :   
  Validez le résumé et entrez y pour continuer

1. **[A]** Mettre à niveau l’agent hôte SAP  
  Téléchargez la dernière archive de l’agent hôte SAP à partir du [SAP Softwarecenter][sap-swcenter] et exécutez la commande suivante pour mettre à niveau l’agent. Remplacez le chemin d’accès à l’archive pour pointer vers le fichier que vous avez téléchargé.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Configurer la réplication de système SAP HANA 2.0

Les éléments suivants sont précédés de **[A]** \(applicable à tous les nœuds), de **[1]** \(applicable uniquement au nœud 1) ou de **[2]** \(applicable uniquement au nœud 2 du cluster Pacemaker).

1. **[1]** Créer une base de données de locataire

   Si vous utilisez SAP HANA 2.0 ou MDC, créez une base de données de locataire pour votre système SAP NetWeaver. Remplacez NW1 par le SID de votre système SAP.

   Connectez-vous en tant que `<hanasid`>adm et exécutez la commande suivante

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configurer la réplication de système sur le premier nœud
   
   Connectez-vous en tant que `<hanasid`>adm et sauvegardez les bases de données

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copier les fichiers d’infrastructure à clé publique du système dans la base de données secondaire

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Créez le site principal.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configurer la réplication de système sur le second nœud
    
    Inscrivez le second nœud pour démarrer la réplication de système. Connectez-vous en tant que `<hanasid`>adm et exécutez la commande suivante

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurer la réplication de système SAP HANA 1.0

1. **[1]** Créer les utilisateurs requis

    Connectez-vous en tant qu’utilisateur racine et exécutez la commande suivante. Veillez à remplacer les chaînes en gras (ID du système HANA HN1 et numéro d’instance 03) par les valeurs de votre installation SAP HANA.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** Créer l’entrée keystore
   
    Connectez-vous en tant qu’utilisateur racine et exécutez la commande suivante pour créer une nouvelle entrée de magasin de clés.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]** Sauvegarder la base de données

   Connectez-vous en tant qu’utilisateur racine et sauvegardez les bases de données

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Si vous utilisez une installation mutualisée, sauvegardez également la base de données locataire

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configurer la réplication de système sur le premier nœud
    
    Connectez-vous en tant que `<hanasid`> adm et créez le site principal.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]** Configurer la réplication de système sur le nœud secondaire.

    Connectez-vous en tant que `<hanasid`> adm et inscrivez le site secondaire.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Créer les ressources de cluster SAP HANA

   Tout d’abord, créez la topologie HANA. Exécutez les commandes suivantes sur l’un des nœuds du cluster Pacemaker.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Ensuite, créez les ressources HANA.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées. Le nœud sur lequel les ressources s’exécutent n’a aucune importance.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Tester la configuration du cluster
Ce chapitre explique comment vous pouvez tester votre configuration. Chaque test suppose que vous êtes connecté en tant que root et que le nœud principal SAP HANA est en cours d’exécution sur la machine virtuelle hn1-db-0.

#### <a name="fencing-test"></a>Test de délimitation

Vous pouvez tester l’installation de l’agent de délimitation en désactivant l’interface réseau sur le nœud hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

La machine virtuelle doit maintenant être redémarrée ou arrêtée en fonction de la configuration de votre cluster.
Si vous désactivez l’action stonith, la machine virtuelle est arrêtée et les ressources sont migrées vers la machine virtuelle en cours d’exécution.

Une fois que vous redémarrez la machine virtuelle, la ressource SAP HANA ne peut pas démarrer en tant que ressource secondaire si vous définissez AUTOMATED_REGISTER="false". Dans ce cas, configurez l’instance HANA comme étant secondaire en exécutant cette commande :

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Tester un basculement manuel

Vous pouvez tester un basculement manuel en arrêtant le service pacemaker sur le nœud hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Après le basculement, vous pouvez démarrer de nouveau le service. La ressource SAP HANA sur hn1-db-0 ne peut pas démarrer en tant que ressource secondaire si vous définissez AUTOMATED_REGISTER="false". Dans ce cas, configurez l’instance HANA comme étant secondaire en exécutant cette commande :

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Tester une migration

Vous pouvez migrer le nœud principal SAP HANA en exécutant la commande suivante
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Si vous définissez AUTOMATED_REGISTER="false", cette séquence de commandes permet de migrer vers hn1-db-1 le nœud principal SAP HANA et le groupe qui contient l’adresse IP virtuelle.
La ressource SAP HANA sur hn1-db-0 ne peut pas démarrer en tant que ressource secondaire. Dans ce cas, configurez l’instance HANA comme étant secondaire en exécutant cette commande :

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

La migration crée des contraintes d’emplacement qui doivent être de nouveau supprimées.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Vous devez également nettoyer l’état de la ressource du nœud secondaire

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Étapes suivantes
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md). 
