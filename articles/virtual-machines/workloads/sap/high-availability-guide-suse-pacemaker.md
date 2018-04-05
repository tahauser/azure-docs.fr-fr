---
title: Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure | Microsoft Docs
description: Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure
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
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: 75615de523f1fba808f44fb1a1015138fb190edc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Pour configurer un cluster Pacemaker dans Azure, deux options s’offrent à vous : vous pouvez soit utiliser un agent d’isolation, qui s’occupe de redémarrer les nœuds défaillants via les API Azure, soit utiliser un appareil SBD.

L’appareil SBD requiert une machine virtuelle supplémentaire qui joue le rôle de serveur cible iSCSI et fournit un appareil SBD. Ce serveur cible iSCSI peut toutefois être partagé avec d’autres clusters Pacemaker. L’utilisation d’un appareil SBD présente l’avantage de garantir un temps de basculement plus rapide et, si vous faites appel à des appareils SBD en local, de ne pas vous obliger à revoir la façon dont vous exploitez le cluster Pacemaker. Il est toujours possible d’utiliser l’agent d’isolation Azure en tant que mécanisme d’isolation de secours pour l’isolation SBD en cas d’indisponibilité du serveur cible iSCSI.

Si vous ne voulez pas investir dans une machine virtuelle supplémentaire, vous pouvez également utiliser l’agent d’isolation Azure. L’inconvénient, c’est qu’un basculement peut prendre entre 10 et 15 minutes si une ressource échoue ou si les nœuds de cluster ne peuvent plus communiquer entre eux.

![Vue d’ensemble de Pacemaker sur SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>Isolation SBD

Si vous souhaitez utiliser un appareil SBD pour l’isolation, procédez comme suit.

### <a name="set-up-an-iscsi-target-server"></a>Configurer un serveur cible iSCSI

Vous devez d’abord créer une machine virtuelle cible iSCSI si ce n’est pas déjà fait. Les serveurs cibles iSCSI peuvent être partagés avec plusieurs clusters Pacemaker.

1. Déployez une nouvelle machine virtuelle SLES 12 SP1 ou version ultérieure et connectez-vous à la machine via le protocole SSH. La machine n’a pas besoin de présenter une grande taille. Une taille de machine virtuelle telle que Standard_E2s_v3 ou Standard_D2s_v3 est suffisante.

1. Mettre à jour SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. Installer les packages cibles iSCSI

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Activer le service cible iSCSI

   <pre><code>   
   sudo systemctl enable target
   sudo systemctl enable targetcli
   sudo systemctl start target
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Créer un périphérique iSCSI sur le serveur cible iSCSI

Attachez un nouveau disque de données à la machine virtuelle cible iSCSI qui peut être utilisée pour ce cluster. Le disque de données peut présenter une taille de seulement 1 Go et doit être placé sur un compte de stockage Premium ou un disque managé Premium pour bénéficier du [contrat SLA de machine virtuelle unique](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Exécutez la commande suivante sur la **machine virtuelle cible iSCSI** afin de créer un disque iSCSI pour le nouveau cluster. Dans l’exemple ci-dessous, **cl1** est utilisé pour identifier le nouveau cluster, et **prod-cl1-0** et **prod-cl1-1** sont les noms d’hôte des nœuds de cluster. Remplacez-les par les noms d’hôte de vos nœuds de cluster.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
sudo systemctl restart target
</code></pre>

### <a name="set-up-sbd-device"></a>Configurer l’appareil SBD

Connectez-vous au périphérique iSCSI créé à l’étape précédente à partir du cluster.
Exécutez les commandes suivantes sur les nœuds du nouveau cluster que vous souhaitez créer.
Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]**  Se connecter aux périphériques iSCSI

   Tout d’abord, activez les services iSCSI et SBD.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Modifier le nom de l’initiateur sur le premier nœud

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modifiez le contenu du fichier pour qu’il corresponde aux listes de contrôle d’accès (ACL) utilisées lors de la création du périphérique iSCSI sur le serveur cible iSCSI.

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Modifier le nom de l’initiateur sur le deuxième nœud

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modifiez le contenu du fichier pour qu’il corresponde aux listes de contrôle d’accès (ACL) utilisées lors de la création du périphérique iSCSI sur le serveur cible iSCSI.

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** Redémarrer le service iSCSI

   Maintenant, redémarrez le service iSCSI pour appliquer les modifications.
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Connectez les périphériques iSCSI. Dans l’exemple ci-dessous, 10.0.0.17 est l’adresse IP du serveur cible iSCSI et 3260 le port par défaut. <b>iqn.2006-04.cl1.local:cl1</b> correspond au nom de cible qui s’affiche lors de l’exécution de la première commande.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Assurez-vous que le périphérique iSCSI est disponible et notez son nom (« /dev/sde » dans l’exemple ci-dessous).

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Maintenant, récupérez l’ID du périphérique iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   La commande fait apparaître trois ID de périphérique. Nous vous recommandons d’utiliser l’ID qui commence par scsi-3. Dans l’exemple ci-dessus, il s’agit de l’ID :
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** Créer l’appareil SBD

   Utilisez l’ID de périphérique du périphérique iSCSI pour créer un nouvel appareil SBD sur le premier nœud de cluster.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Adapter la configuration SBD

   Ouvrez le fichier de configuration SBD.

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Modifiez la propriété de l’appareil SBD, activez l’intégration de Pacemaker et modifiez le mode de démarrage de SBD.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Créez le fichier de configuration softdog.

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Maintenant, chargez le module.

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Installation du cluster

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Mettre à jour SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Activer l’accès SSH

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2**] Activer l’accès SSH

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
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
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Installer le cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Ajouter un nœud au cluster
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
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
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Redémarrez ensuite le service corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Modifier les paramètres par défaut du cluster Pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Créer l’appareil STONITH

L’appareil STONITH utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service, effectuez les étapes suivantes.

1. Accédez à <https://portal.azure.com>
1. Ouvrez le panneau Azure Active Directory  
   Accédez aux propriétés et notez l’ID de répertoire. Il s’agit de **l’ID du locataire**.
1. Cliquez sur Inscriptions d’applications
1. Cliquez sur Ajouter.
1. Entrez un nom, sélectionnez le type d’application Application/API web, saisissez une URL de connexion (par exemple, http://localhost), puis cliquez sur Créer.
1. L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez la nouvelle application et cliquez sur Clés dans l’onglet Paramètres
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais » et cliquez sur Enregistrer
1. Notez la valeur. Cette valeur est utilisée comme **mot de passe** pour le principal de service
1. Notez l’ID de l’application. Cet identifiant est utilisé comme nom d’utilisateur (**ID de connexion** dans la procédure ci-dessous) du principal de service

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Créer un rôle personnalisé pour l’agent d’isolation

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Vous devez accorder au principal de service les autorisations de démarrer et arrêter (libérer) toutes les machines virtuelles du cluster. Si vous n’avez pas encore créé le rôle personnalisé, vous pouvez le créer à l’aide de [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell#create-a-custom-role) ou de l’[interface de ligne de commande Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli#create-a-custom-role).

Utilisez le contenu suivant pour le fichier d’entrée. Vous devez adapter le contenu à vos abonnements, c’est-à-dire remplacer c276fc76-9cd4-44c9-99a7-4fd71546436e et e91d47c4-76f3-4271-a796-21b4ecfe3624 par les ID de vos abonnements. Si vous n’avez qu’un seul abonnement, supprimez la deuxième entrée dans AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Affecter le rôle personnalisé au principal de service

Affectez au principal de service le rôle personnalisé Linux Fence Agent Role (Rôle d’agent d’isolation Linux) créé dans la section précédente. N’utilisez plus le rôle Propriétaire !

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Toutes les ressources
1. Sélectionnez la machine virtuelle du premier nœud de cluster.
1. Cliquez sur Contrôle d’accès (IAM)
1. Cliquez sur Ajouter.
1. Sélectionnez le rôle Linux Fence Agent Role (Rôle d’agent d’isolation Linux).
1. Entrez le nom de l’application que vous avez créée ci-dessus
1. Cliquez sur OK

Répétez les étapes ci-dessus pour le deuxième nœud de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Créer les appareils STONITH

Une fois que vous avez modifié les autorisations pour les machines virtuelles, vous pouvez configurer les appareils STONITH dans le cluster.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Créer la topologie d’isolation pour l’isolation SBD

Si vous souhaitez utiliser un appareil SBD, nous vous recommandons d’utiliser un agent d’isolation Azure en tant que mécanisme de secours en cas d’indisponibilité du serveur cible iSCSI.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** Activer l’utilisation d’un appareil STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Étapes suivantes
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha].