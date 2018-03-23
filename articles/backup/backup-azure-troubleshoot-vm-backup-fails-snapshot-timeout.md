---
title: 'Résolution des échecs de sauvegarde Azure : état de l’agent invité non disponible | Microsoft Docs'
description: Symptômes, causes et résolution des défaillances de la Sauvegarde Azure liées à l’agent, à l’extension et aux disques.
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
keywords: Sauvegarde Azure ; agent de machine virtuelle ; connectivité réseau ;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: a18718aba3ef7f70caa541c6eb56311082d02bed
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Résoudre les problèmes de défaillance de la Sauvegarde Azure : problèmes d’agent ou d’extension

Cet article indique les étapes à suivre pour résoudre les erreurs de la Sauvegarde Azure liées à la communication avec l’agent et l’extension de machine virtuelle.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>L’agent de machine virtuelle ne parvient pas à communiquer avec la Sauvegarde Azure

Message d’erreur : « L’agent de machine virtuelle ne parvient pas à communiquer avec la Sauvegarde Azure »

Dès que vous avez enregistré et planifié une machine virtuelle dans le service de sauvegarde, ce dernier lance la tâche en communiquant avec l’agent de la machine virtuelle pour prendre un instantané à la date et l’heure. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Lorsque un instantané n’est pas déclenché, la sauvegarde risque d’échouer. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :

**Cause 1 : [La machine virtuelle n’a pas accès à Internet](#the-vm-has-no-internet-access)**  
**Cause 2 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Cause 3 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Cause 4 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Cause 5 : [Impossible de mettre à jour ou de charger l’extension de sauvegarde](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>L’opération de capture instantanée échoue parce que la machine virtuelle n’est pas connectée au réseau

Message d’erreur : « L’opération de capture instantanée a échoué, car la machine virtuelle ne présente aucune connectivité réseau »

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :    
**Cause 1 : [La machine virtuelle n’a pas accès à Internet](#the-vm-has-no-internet-access)**  
**Cause 2 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Cause 3 : [Impossible de mettre à jour ou de charger l’extension de sauvegarde](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>L’opération d’extension VMSnapshot échoue

Message d’erreur : « Échec de l’opération d’extension VMSnapshot »

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :  
**Cause 1 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Cause 2 : [Impossible de mettre à jour ou de charger l’extension de sauvegarde](#the-backup-extension-fails-to-update-or-load)**  
**Cause 3 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Cause 4 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>La sauvegarde échoue, car l’agent de machine virtuelle ne répond pas

Message d’erreur : « Impossible d’effectuer l’opération, car l’agent de machine virtuelle ne répond pas »

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :  
**Cause 1 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Cause 2 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Cause 3 : [La machine virtuelle n’a pas accès à Internet](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>La sauvegarde échoue, avec une erreur interne

Message d’erreur : « La sauvegarde a échoué avec une erreur interne. Veuillez réessayer l’opération dans quelques minutes »

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :  
**Cause 1 : [La machine virtuelle n’a pas accès à Internet](#the-vm-has-no-internet-access)**  
**Cause 2 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Cause 3 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Cause 4 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Cause 5 : [Impossible de mettre à jour ou de charger l’extension de sauvegarde](#the-backup-extension-fails-to-update-or-load)**  
**Cause 6 : [Le service de sauvegarde n’est pas autorisé à supprimer les anciens points de restauration en raison du verrouillage d’un groupe de ressources](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="disk-configuration-is-not-supported"></a>Configuration de disque non prise en charge

Message d’erreur : « La configuration de disque spécifiée n’est pas prise en charge »

> [!NOTE]
> Nous avons une préversion privée qui prend en charge les sauvegardes de machines virtuelles dotées de disques de plus de 1 To. Pour plus d’informations, consultez la section [Préversion privée pour la prise en charge de la sauvegarde des machines virtuelles dotées de disques volumineux](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a).
>
>

Actuellement, la Sauvegarde Azure ne prend pas en charge les disques de taille [supérieure à 1 023 Go](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). Si vous avez des disques de plus de 1 To :  
1. [Attachez de nouveaux disques](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) de taille inférieure à 1 To.  
2. Copiez les données des disques de plus de 1 To sur les disques ainsi créés, de taille inférieure à 1 To.  
3. Vérifiez que toutes les données ont été copiées. Ensuite, supprimez les disques de plus de 1 To.  
4. Lancez la sauvegarde.

## <a name="causes-and-solutions"></a>Causes et solutions

### <a name="the-vm-has-no-internet-access"></a>La machine virtuelle n’a pas accès à Internet
Selon la spécification du déploiement, la machine virtuelle n’a pas accès à Internet. Il se peut également que des restrictions empêchent l’accès à l’infrastructure Azure.

Pour fonctionner correctement, l’extension Sauvegarde a besoin d’une connectivité aux adresses IP publiques Azure. Elle envoie des commandes à un point de terminaison Stockage Azure (URL HTTP) pour gérer les instantanés de la machine virtuelle. Si elle n’a pas accès à l’Internet public, la sauvegarde échoue.

####  <a name="solution"></a>Solution
Pour résoudre le problème, essayez l’une des méthodes suivantes :

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Autoriser l’accès au Stockage Azure correspondant à la région

Vous pouvez utiliser des [balises de service](../virtual-network/security-overview.md#service-tags) pour autoriser les connexions au stockage de la région concernée. Vérifiez que la règle qui autorise l’accès au compte de stockage a la priorité par rapport à la règle bloquant l’accès à Internet. 

![Groupe de sécurité réseau avec des balises de stockage pour une région](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Les balises de service de stockage sont en préversion. Elles ne sont disponibles que dans certaines régions. Vous en trouverez la liste dans la section [Balises de service pour le stockage](../virtual-network/security-overview.md#service-tags).

##### <a name="create-a-path-for-http-traffic"></a>Créer un chemin d’accès pour le trafic HTTP

1. Si des restrictions réseau ont été mises en place (un groupe de sécurité réseau, par exemple), déployez un serveur proxy HTTP pour acheminer le trafic.
2. Pour autoriser l’accès à Internet à partir du serveur proxy HTTP, ajoutez des règles au groupe de sécurité réseau (le cas échéant).

Pour savoir comment configurer un proxy HTTP pour les sauvegardes de machines virtuelles, consultez [Préparer votre environnement pour la sauvegarde des machines virtuelles Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Si vous utilisez Azure Managed Disks, vous devrez peut-être ouvrir un port supplémentaire (le port 8443) sur les pare-feu.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)

#### <a name="solution"></a>Solution
Il se peut que l’agent de machine virtuelle soit endommagé ou que le service ait été arrêté. Réinstallez l’agent de machine virtuelle pour obtenir la dernière version. Cela permet également de redémarrer la communication avec le service.

1. Regardez si le service d’agent invité Windows s’exécute dans les services de machine virtuelle (services.msc). Essayez de le redémarrer et de lancer la sauvegarde.    
2. Si le service d’agent invité Windows n’apparaît pas dans les services, accédez à **Programmes et fonctionnalités** dans le Panneau de configuration pour déterminer s’il est installé.
4. Si le service d’agent invité Windows figure sous **Programmes et fonctionnalités**, désinstallez-le.
5. Téléchargez et installez la [dernière version du MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Des droits d’administrateur sont nécessaires pour effectuer l’installation.
6. Vérifiez que le service d’agent invité Windows apparaît dans les services.
7. Exécutez une sauvegarde à la demande : 
    * Dans le portail, sélectionnez **Sauvegarder maintenant**.

Vérifiez également que [Microsoft .NET 4.5 est installé](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) dans la machine virtuelle. Si ce n’est pas le cas, l’agent de machine virtuelle ne pourra pas communiquer avec le service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)

#### <a name="solution"></a>Solution
La plupart des échecs des machines virtuelles Linux liés aux agents ou aux extensions sont causés par des problèmes qui affectent un agent obsolète de machine virtuelle. Pour résoudre ce problème, suivez ces recommandations générales :

1. Suivez les instructions fournies pour la [mise à jour d’un agent de machine virtuelle Linux](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Nous *recommandons vivement* la mise à jour de l’agent uniquement par le biais de référentiel de distribution. Nous ne recommandons pas de télécharger le code de l’agent à partir de GitHub directement et d’effectuer la mise à jour. Si la dernière version de l’agent n’est pas disponible pour la distribution, contactez le support de distribution pour savoir comment l’installer. Pour rechercher l’agent le plus récent, accédez à la page relative à l’[agent Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) du référentiel GitHub.

2. Vérifiez que l’agent Azure s’exécute sur la machine virtuelle à l’aide de la commande suivante : `ps -e`.

 Si le processus ne s’exécute pas, redémarrez-le à l’aide des commandes suivantes :

 * Pour Ubuntu : `service walinuxagent start`
 * Pour les autres distributions : `service waagent start`

3. [Configurez l’agent de redémarrage automatique](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Exécutez une nouvelle sauvegarde de test. Si la défaillance persiste, collectez les journaux suivants à partir de la machine virtuelle :

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Si nous exigeons une journalisation détaillée pour waagent, procédez comme suit :

1. Dans le fichier /etc/waagent.conf, recherchez la ligne suivante : **Activer l’enregistrement des informations détaillées (o|n)**
2. Passez la valeur **Logs.Verbose** de *N* à *O*.
3. Enregistrez la modification, puis redémarrez waagent en suivant les étapes détaillées plus haut dans cette section.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Impossible de récupérer l’état de l’instantané ou de capturer un instantané
La sauvegarde de machine virtuelle émet une commande de capture instantanée à destination du stockage sous-jacent. Elle peut échouer pour deux raisons : soit elle n’a pas accès au compte de stockage, soit l’exécution de la tâche de capture instantanée est différée.

#### <a name="solution"></a>Solution
Voici les causes possibles de l’échec de la tâche de capture instantanée :

| Cause : | Solution |
| --- | --- |
| L’état de la machine virtuelle est rapporté de manière incorrecte, car la machine virtuelle est arrêtée dans le protocole RDP (Remote Desktop Protocol). | Si vous avez arrêté la machine virtuelle dans le protocole RDP, retournez sur le portail pour vérifier que son état est correct. Si ce n’est pas le cas, arrêtez la machine virtuelle dans le portail à l’aide de l’option **Arrêter** dans le tableau de bord de la machine virtuelle. |
| La machine virtuelle ne parvient pas à récupérer l’adresse d’hôte/de structure à partir du protocole DHCP. | Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne. Si la machine virtuelle ne parvient pas à récupérer l’adresse d’hôte/de structure à partir de la réponse 245 DHCP, elle ne peut ni télécharger, ni exécuter des extensions. Si vous avez besoin d’une adresse IP privée statique, configurez-la sur la plateforme. L’option DHCP à l’intérieur de la machine virtuelle doit être laissée désactivée. Pour plus d’informations, consultez la section [Définir une adresse IP privée interne statique](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>L’extension de sauvegarde ne peut être ni mise à jour ni chargée
Si les extensions ne sont pas chargées, la sauvegarde échoue, car il n’est pas possible de capturer un instantané.

#### <a name="solution"></a>Solution

Désinstallez l’extension pour forcer le rechargement de l’extension VMSnapshot. La prochaine tentative de sauvegarde rechargera l’extension.

Pour désinstaller l’extension :

1. Sur le [Portail Azure](https://portal.azure.com/), accédez à la machine virtuelle qui rencontre des échecs de sauvegarde.
2. Sélectionnez **Paramètres**.
3. Sélectionnez **Extensions**.
4. Sélectionnez **Extension VMSnapshot**.
5. Sélectionnez **Désinstaller**.

Cette procédure réinstalle l’extension lors de la sauvegarde suivante.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Le service de sauvegarde n’est pas autorisé à supprimer les anciens points de restauration en raison du verrouillage d’un groupe de ressources
Ce problème est propre aux machines virtuelles gérées, pour lesquelles l’utilisateur verrouille le groupe de ressources. Dans ce cas, le service de sauvegarde ne peut pas supprimer les anciens points de restauration. En raison de la limite de 18 points de restauration, les nouvelles sauvegardes échouent.

#### <a name="solution"></a>Solution

Pour résoudre le problème, suivez les étapes ci-dessous afin de supprimer la collection de points de restauration : <br>
 
1. Supprimez le verrou du groupe de ressources dans lequel se trouve la machine virtuelle. 
2. Installez ARMClient à l’aide de Chocolatey : <br>
   https://github.com/projectkudu/ARMClient
3. Connectez-vous à ARMClient : <br>
    `.\armclient.exe login`
4. Récupérez la collection de points de restauration correspondant à la machine virtuelle : <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Exemple : `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Supprimez la collection de points de restauration : <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. La sauvegarde planifiée suivante crée automatiquement une collection de points de restauration et de nouveaux points de restauration.

 
Le problème se reproduira si vous verrouillez à nouveau le groupe de ressources. 

