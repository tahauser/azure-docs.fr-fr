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
ms.date: 12/12/2017
ms.author: sedusch
ms.openlocfilehash: 2bf9ed176f37c315aa4496894315f2318370ce7f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
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
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

En local, vous pouvez utiliser soit la réplication système HANA soit un stockage partagé pour établir une haute disponibilité pour SAP HANA.
Sur les machines virtuelles Azure, la réplication système SAP HANA dans Azure est la seule fonction de haute disponibilité prise en charge actuellement. La réplication SAP HANA se compose d’un nœud principal et d’au moins un nœud secondaire. Les modifications apportées aux données sur le nœud principal sont répliquées vers les nœuds secondaires de manière synchrone ou asynchrone.

Cet article décrit comment déployer et configurer les machines virtuelles, installer l’infrastructure de cluster, et installer et configurer la réplication système SAP HANA.
Les exemples de configuration et les commandes d’installation utilisent le numéro d’instance 03 et l’ID système HANA HDB.

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

## <a name="deploying-linux"></a>Déploiement de Linux

L’agent de ressource pour SAP HANA est inclus dans SUSE Linux Enterprise Server for SAP Applications.
La Place de marché Azure contient une image de SUSE Linux Enterprise Server for SAP Applications 12 avec BYOS (Bring Your Own Subscription), que vous pouvez utiliser pour déployer de nouvelles machines virtuelles.

### <a name="manual-deployment"></a>Déploiement manuel

1. Création d’un groupe de ressources
1. Création d'un réseau virtuel
1. Créer deux comptes de stockage
1. Créer un groupe à haute disponibilité  
   Définir un domaine de mise à jour maximal
1. Créer un équilibrage de charge (interne)  
   Sélectionnez le réseau virtuel créé à la deuxième étape
1. Créer la machine virtuelle 1   
   Utilisez au moins SLES4SAP 12 SP1, dans cet exemple, l’image BYOS SLES4SAP 12 SP1 https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS) est utilisé  
   Sélectionner le compte de stockage 1   
   Sélectionner le groupe à haute disponibilité  
1. Créer la machine virtuelle 2   
   Utilisez au moins SLES4SAP 12 SP1, dans cet exemple, l’image BYOS SLES4SAP 12 SP1 https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS) est utilisé  
   Sélectionner le compte de stockage 2    
   Sélectionner le groupe à haute disponibilité  
1. Ajouter des disques de données
1. Configurer l’équilibrage de charge
    1. Créer un pool d’adresse IP frontal
        1. Ouvrir l’équilibrage de charge, sélectionner le pool d’adresses IP frontal et cliquer sur Ajouter
        1. Entrer le nom du nouveau pool d’adresses IP frontal (par exemple hana-frontend)
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
        1. Conserver le protocole TCP et choisir le port 3**03**15
        1. Augmenter le délai d’inactivité à 30 minutes
        1. **Veiller à activer IP flottante**
        1. Cliquez sur OK
        1. Répéter les étapes ci-dessus pour le port 3**03**17

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
       Sélectionnez l’une des distributions Linux. Dans cet exemple, sélectionnez SLES 12 BYOS
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

## <a name="setting-up-linux-ha"></a>Configuration de la haute disponibilité Linux

Les éléments suivants sont précédés de [A] \(applicable à tous les nœuds), de [1] \(applicable uniquement au nœud 1) ou de [2] \(applicable uniquement au nœud 2).

1. [A] SLES for SAP BYOS uniquement : inscrire SLES pour pouvoir utiliser les référentiels
1. [A] SLES for SAP BYOS uniquement : ajouter un module de cloud public
1. [A] Mettre à jour SLES
    ```bash
    sudo zypper update

    ```

1. [1] Activer l’accès SSH
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] Activer l’accès SSH
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] Activer l’accès SSH
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] installer l’extension de haute disponibilité
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] Configurer la disposition du disque
    1. LVM  
    En général, nous recommandons d’utiliser LVM pour les volumes qui stockent des données et des fichiers journaux. L’exemple suivant part du principe que les machines virtuelles disposent de quatre disques de données joints qui doivent être utilisés pour créer deux volumes.
        * Créez des volumes physiques pour tous les disques que vous souhaitez utiliser.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Créez un groupe de volumes pour les fichiers de données, un groupe de volumes pour les fichiers journaux et l’autre pour le répertoire partagé de SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Créez les volumes logiques
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Créez les répertoires de montage et copiez l’UUID de tous les volumes logiques
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the ID of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Créez des entrées fstab pour les trois volumes logiques
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Insérez cette ligne dans /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Montez les nouveaux volumes
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Disques simples  
       Pour les petits systèmes ou les systèmes de démonstration, vous pouvez placer vos données et fichiers journaux HANA sur un disque. Les commandes suivantes permettent de créer une partition au format xfs sur /dev/sdc.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>notez l’ID de /dev/sdc1
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] Configurer la résolution de nom d’hôte pour tous les hôtes  
    Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes
    ```bash
    sudo vi /etc/hosts
    ```
    Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] Installer le cluster
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Ajouter un nœud au cluster
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] Modifier le mot de passe hacluster pour utiliser le même mot de passe
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] Configurer corosync pour utiliser les autres transports et ajouter la liste de nœuds. Sinon, le cluster ne va pas fonctionner. 
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

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
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Redémarrez ensuite le service corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] Installer les packages haute disponibilité HANA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Installation de SAP HANA

Consultez le chapitre 4 de la publication [SAP HANA SR Performance Optimized Scenario guide][suse-hana-ha-guide] (Scénario d’optimisation des performances de réplication système de SAP HANA) pour installer la réplication système SAP HANA.

1. [A] Exécuter hdblcm à partir du DVD HANA
    * Choose installation -> 1
    * Select additional components for installation -> 1
    * Enter Installation Path [/hana/shared]: -> ENTRÉE
    * Enter Local Host Name [..]: -> ENTRÉE
    * Do you want to add additional hosts to the system? (y/n) [n] : -> ENTRÉE
    * Enter SAP HANA System ID : <SID of HANA e.g. HDB>
    * Enter Instance Number [00] :   
  Numéro d’instance HANA. Utilisez 03 si vous avez utilisé le modèle Azure ou si vous avez suivi le déploiement manuel
    * Select Database Mode / Enter Index [1] : -> ENTRÉE
    * Select System Usage / Enter Index [4] :  
  Select the system Usage
    * Enter Location of Data Volumes [/hana/data/HDB] : -> ENTRÉE
    * Enter Location of Log Volumes [/hana/log/HDB] : -> ENTRÉE
    * Restrict maximum memory allocation? [n] : -> ENTRÉE
    * Enter Certificate Host Name For Host '...' [...]: -> ENTER
    * Enter SAP Host Agent User (sapadm) Password:
    * Confirm SAP Host Agent User (sapadm) Password:
    * Enter System Administrator (hdbadm) Password:
    * Confirm System Administrator (hdbadm) Password:
    * Enter System Administrator Home Directory [/usr/sap/HDB/home] : -> ENTRÉE
    * Enter System Administrator Login Shell [/bin/sh] : -> ENTRÉE
    * Enter System Administrator User ID [1001] : -> ENTRÉE
    * Enter ID of User Group (sapsys) [79] : -> ENTRÉE
    * Enter Database User (SYSTEM) Password:
    * Confirm Database User (SYSTEM) Password:
    * Restart system after machine reboot? [n] : -> ENTRÉE
    * Do you want to continue? (y/n) :   
  Validez le résumé et entrez y pour continuer
1. [A] Mettre à niveau l’agent hôte SAP  
  Téléchargez la dernière archive de l’agent hôte SAP à partir du [SAP Softwarecenter][sap-swcenter] et exécutez la commande suivante pour mettre à niveau l’agent. Remplacez le chemin d’accès à l’archive pour pointer vers le fichier que vous avez téléchargé.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] Créer la réplication HANA (en tant que root)  
    Exécutez la commande ci-dessous. Veillez à remplacer les chaînes en gras (ID du système HANA HDB et numéro d’instance 03) par les valeurs de votre installation SAP HANA.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Créer l’entrée keystore (en tout que root)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] Sauvegarder la base de données (en tant que root)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] Basculer sur l’utilisateur sapsid (par exemple, hdbadm) et créer le site principal.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] Basculer sur l’utilisateur sapsid (par exemple, hdbadm) et créer le site secondaire.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Configurer le framework du cluster

Modifier les paramètres par défaut

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>nous allons maintenant charger le fichier dans le cluster
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Créer le périphérique STONITH

L’appareil STONITH utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service, effectuez les étapes suivantes.

1. Accédez à <https://portal.azure.com>
1. Ouvrez le panneau Azure Active Directory  
   Accédez aux propriétés et notez l’ID de répertoire. Cet ID est **l’ID du locataire**.
1. Cliquez sur Inscriptions d’applications
1. Cliquez sur Ajouter.
1. Entrez un nom, sélectionnez le type d’application « Application web/API », entrez une URL de connexion (par exemple, http://localhost) et cliquez sur Créer
1. L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez la nouvelle application et cliquez sur Clés dans l’onglet Paramètres
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais » et cliquez sur Enregistrer
1. Notez la valeur. Cette valeur est utilisée comme **mot de passe** pour le principal de service
1. Notez l’ID de l’application. Cet identifiant est utilisé comme nom d’utilisateur (**ID de connexion** dans la procédure suivante) du principal de service

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Accordez au principal de service les autorisations de démarrer et arrêter (libérer) toutes les machines virtuelles du cluster.

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Toutes les ressources
1. Sélectionnez la machine virtuelle
1. Cliquez sur Contrôle d’accès (IAM)
1. Cliquez sur Ajouter.
1. Sélectionnez le rôle de propriétaire
1. Entrez le nom de l’application que vous avez créée lors des étapes précédentes
1. Cliquez sur OK

Une fois que vous avez modifié les autorisations pour les machines virtuelles, vous pouvez configurer les périphériques STONITH dans le cluster.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>nous allons maintenant charger le fichier dans le cluster
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Créer des ressources SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system ID
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>nous allons maintenant charger le fichier dans le cluster
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>nous allons maintenant charger le fichier dans le cluster
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Tester la configuration du cluster
Ce chapitre explique comment vous pouvez tester votre configuration. Chaque test suppose que vous êtes connecté en tant que root et que le nœud principal SAP HANA est en cours d’exécution sur la machine virtuelle saphanavm1.

#### <a name="fencing-test"></a>Test de délimitation

Vous pouvez tester l’installation de l’agent de délimitation en désactivant l’interface réseau sur le nœud saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

La machine virtuelle doit maintenant être redémarrée ou arrêtée en fonction de la configuration de votre cluster.
Si vous désactivez l’action stonith, la machine virtuelle est arrêtée et les ressources sont migrées vers la machine virtuelle en cours d’exécution.

Une fois que vous redémarrez la machine virtuelle, la ressource SAP HANA ne peut pas démarrer en tant que ressource secondaire si vous définissez AUTOMATED_REGISTER="false". Dans ce cas, configurez l’instance HANA comme étant secondaire en exécutant cette commande :

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Tester un basculement manuel

Vous pouvez tester un basculement manuel en arrêtant le service pacemaker sur le nœud saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

Après le basculement, vous pouvez démarrer de nouveau le service. La ressource SAP HANA sur saphanavm1 ne peut pas démarrer en tant que ressource secondaire si vous définissez AUTOMATED_REGISTER="false". Dans ce cas, configurez l’instance HANA comme étant secondaire en exécutant cette commande :

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Tester une migration

Vous pouvez migrer le nœud principal SAP HANA en exécutant la commande suivante
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Si vous définissez AUTOMATED_REGISTER="false", cette séquence de commandes permet de migrer vers saphanavm2 le nœud principal SAP HANA et le groupe qui contient l’adresse IP virtuelle.
La ressource SAP HANA sur saphanavm1 ne peut pas démarrer en tant que ressource secondaire. Dans ce cas, configurez l’instance HANA comme étant secondaire en exécutant cette commande :

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

La migration crée des contraintes d’emplacement qui doivent être de nouveau supprimées.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Vous devez également nettoyer l’état de la ressource du nœud secondaire

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Étapes suivantes
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md). 
