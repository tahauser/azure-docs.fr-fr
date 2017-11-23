---
title: "Résolution des échecs de sauvegarde Azure : état de l’agent invité non disponible | Microsoft Docs"
description: "Symptômes, causes et résolution des échecs de sauvegarde Azure liés à l’agent, à l’extension et aux disques"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Sauvegarde Azure ; agent de machine virtuelle ; connectivité réseau ;"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli;markgal;
ms.openlocfilehash: a07fb9388f1e83bd167cf7c65cd3cd1e4f51ecd1
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Résoudre les problèmes de sauvegarde Microsoft Azure : problèmes liés à un agent et/ou une extension

Cet article indique les étapes à appliquer pour résoudre les échecs de sauvegarde liés à des problèmes de communication avec l’agent et l’extension de machine virtuelle.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>L’agent de machine virtuelle ne peut pas communiquer avec la sauvegarde Microsoft Azure
Une fois que vous avez enregistré et planifié une machine virtuelle pour le service de sauvegarde Azure, ce dernier lance le travail en communiquant avec l’agent de la machine virtuelle pour prendre un instantané jusqu’à une date et heure. Une des conditions suivantes peut empêcher le déclenchement de l’instantané, qui à son tour risque de faire échouer la sauvegarde. Suivez les étapes de résolution des problèmes ci-dessous dans l’ordre indiqué et recommencez l’opération.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Cause 1 : [La machine virtuelle ne possède aucun accès à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Cause 2 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (pour les machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Cause 3 : [L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Cause 4 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Cause 5 : [L’extension de sauvegarde ne peut être mise à jour ou chargée](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>L’opération de capture instantanée a échoué, car la machine virtuelle ne présente aucune connectivité réseau
Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Une des conditions suivantes peut empêcher le déclenchement de l’instantané, qui à son tour risque de faire échouer la sauvegarde. Suivez les étapes de résolution des problèmes ci-dessous dans l’ordre indiqué et recommencez l’opération.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Cause 1 : [La machine virtuelle ne possède aucun accès à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Cause 2 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Cause 3 : [L’extension de sauvegarde ne peut être mise à jour ou chargée](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>Échec de l’opération d’extension VMSnapshot

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Une des conditions suivantes peut empêcher le déclenchement de l’instantané, qui à son tour risque de faire échouer la sauvegarde. Suivez les étapes de résolution des problèmes ci-dessous dans l’ordre indiqué et recommencez l’opération.
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Cause 1 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Cause 2 : [L’extension de sauvegarde ne peut être mise à jour ou chargée](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Cause 3 : [La machine virtuelle ne possède aucun accès à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Cause 4 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (pour les machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Cause 5 : [L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>Impossible d’effectuer l’opération, car l’agent de machine virtuelle ne répond pas

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Une des conditions suivantes peut empêcher le déclenchement de l’instantané, qui à son tour risque de faire échouer la sauvegarde. Suivez les étapes de résolution des problèmes ci-dessous dans l’ordre indiqué et recommencez l’opération.
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Cause 1 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (pour les machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Cause 2 : [L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Cause 3 : [La machine virtuelle ne possède aucun accès à Internet](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>Échec de l’opération de sauvegarde avec une erreur interne : veuillez retenter l’opération dans quelques minutes

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Une des conditions suivantes peut empêcher le déclenchement de l’instantané, qui à son tour risque de faire échouer la sauvegarde. Suivez les étapes de résolution des problèmes ci-dessous dans l’ordre indiqué et recommencez l’opération.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Cause 1 : [La machine virtuelle ne possède aucun accès à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Cause 2 : [L’agent est installé sur la machine virtuelle, mais ne répond pas (pour les machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Cause 3 : [L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Cause 4 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Cause 5 : [L’extension de sauvegarde ne peut être mise à jour ou chargée](#the-backup-extension-fails-to-update-or-load)

## <a name="the-specified-disk-configuration-is-not-supported"></a>La configuration de disque spécifiée n’est pas prise en charge

> [!NOTE]
> Nous avons une préversion privée pour prendre en charge les sauvegardes des machines virtuelles dotées de disques non managés de >1 To. Pour plus d’informations, consultez [Préversion privée pour la prise en charge de la sauvegarde des machines virtuelles dotées de disques volumineux](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

Actuellement, Sauvegarde Azure ne prend pas en charge les tailles de disque [supérieures à 1 023 Go](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Si vous disposez de disques supérieurs à 1 To, [attachez de nouveaux disques](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) inférieurs à 1 To. <br>
- Copiez ensuite les données du disque de plus de 1 To dans le ou les disques plus petits. <br>
- Vérifiez que toutes les données ont bien été copiées et retirez les disques supérieures à 1 To.
- Lancez la sauvegarde.

## <a name="causes-and-solutions"></a>Causes et solutions

### <a name="the-vm-has-no-internet-access"></a>La machine virtuelle ne possède aucun accès à Internet
Selon la configuration requise pour le déploiement, la machine virtuelle n’a pas accès à Internet, ou a des restrictions en place qui empêchent l’accès à l’infrastructure Azure.

Pour fonctionner correctement, l’extension de sauvegarde nécessite une connectivité vers les adresses IP publiques Azure. L’extension envoie des commandes vers un point de terminaison Azure Storage (HTTP URL) pour gérer les instantanés de la machine virtuelle. Si l’extension n’a pas accès à Internet public, la sauvegarde échoue.

####  <a name="solution"></a>Solution
Pour résoudre le problème, essayez l’une des méthodes répertoriées ci-dessous :
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Autorisez l’accès aux plages IP du centre de données Azure.

1. Obtenez la [liste des adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) pour y autoriser l’accès.
2. Débloquez les adresses IP en exécutant l’applet de commande **New-NetRoute** de la machine virtuelle Azure dans une fenêtre PowerShell avec élévation de privilèges. Exécutez l’applet de commande en tant qu’administrateur.
3. Ajoutez des règles au groupe de sécurité réseau (le cas échéant) pour autoriser l’accès aux adresses IP.

##### <a name="create-a-path-for-http-traffic-to-flow"></a>Créez un chemin d'accès pour le trafic HTTP

1. Si des restrictions réseau ont été mises en place (un groupe de sécurité réseau, par exemple), déployez un serveur proxy HTTP pour acheminer le trafic.
2. Ajoutez des règles au groupe de sécurité réseau (le cas échéant) pour autoriser l’accès à Internet à partir du serveur proxy HTTP.

Pour savoir comment configurer un proxy HTTP pour les sauvegardes de machines virtuelles, consultez [Préparer votre environnement pour la sauvegarde des machines virtuelles Azure](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

Si vous utilisez Managed Disks, vous devrez peut-être disposer d’un port supplémentaire (8443) ouvert sur les pare-feu.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L’agent est installé dans la machine virtuelle, mais ne répond pas (pour les machines virtuelles Windows)

#### <a name="solution"></a>Solution
Il se peut que l’agent de machine virtuelle soit endommagé ou que le service ait été arrêté. La réinstallation de l’agent de machine virtuelle peut vous aider à obtenir la version la plus récente et à redémarrer la communication.

1. Vérifiez que le service d’agent invité Windows apparaît dans les services de la machine virtuelle (services.msc). Essayez de redémarrer le service d’agent invité Windows et de démarrer la sauvegarde.<br>
2. S’il ne figure pas dans les services, vérifiez si le service d’agent invité Windows est installé sous Programmes et fonctionnalités.
4. S’il ne figure pas sous Programmes et fonctionnalités, désinstallez l’agent invité Windows.
5. Téléchargez et installez la [dernière version du MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges d’administrateur pour terminer l’installation.
6. Les services d’agent invité Windows devraient alors apparaître dans les services.
7. Essayez d’exécuter une sauvegarde à la demande/ad hoc en cliquant sur « Sauvegarder maintenant » dans le portail.

Vérifiez également que **[.NET 4.5 est installé dans le système](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)** de la machine virtuelle. Si ce n’est pas le cas, l’agent de machine virtuelle ne pourra pas communiquer avec le service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)

#### <a name="solution"></a>Solution
La plupart des échecs des machines virtuelles Linux liés aux agents ou aux extensions sont causés par des problèmes qui affectent un agent obsolète de machine virtuelle. Pour résoudre ce problème, suivez ces recommandations générales :

1. Suivez les instructions fournies pour la [mise à jour d’un agent de machine virtuelle Linux](../virtual-machines/linux/update-agent.md).

 >[!NOTE]
 >Nous *recommandons vivement* la mise à jour de l’agent uniquement par le biais de référentiel de distribution. Nous ne recommandons pas de télécharger le code de l’agent à partir de GitHub directement et d’effectuer la mise à jour. Si le dernier agent n’est pas disponible pour la distribution, contactez le support de distribution pour obtenir des instructions sur l’installation de celui-ci. Pour rechercher l’agent le plus récent, accédez à la page relative à l’[agent Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) du référentiel GitHub.

2. Assurez-vous que l’agent Azure s’exécute sur la machine virtuelle en exécutant la commande suivante : `ps -e`

 Si le processus ne s’exécute pas, redémarrez-le à l’aide des commandes suivantes :

 * Pour Ubuntu : `service walinuxagent start`
 * Pour les autres distributions : `service waagent start`

3. [Configurez l’agent de redémarrage automatique](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Exécutez une nouvelle sauvegarde de test. Si le problème persiste, veuillez collecter les journaux suivants à partir de la machine virtuelle du client :

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Si nous exigeons une journalisation détaillée pour waagent, procédez comme suit :

1. Dans le fichier /etc/waagent.conf, recherchez la ligne suivante : **Activer l’enregistrement des informations détaillées (o|n)**
2. Modifiez la valeur **Logs.Verbose** en remplaçant *n* par *O*.
3. Enregistrez cette modification, puis redémarrez waagent en suivant les étapes précédentes de cette section.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Impossible de récupérer l’état de l’instantané ou de capturer un instantané
La sauvegarde de machine virtuelle émet une commande de capture instantanée à destination du stockage sous-jacent. La sauvegarde peut échouer, car elle n’a pas accès au compte de stockage ou parce que l’exécution de la tâche d’instantané est différée.

#### <a name="solution"></a>Solution
Voici les causes possibles de l’échec d’une tâche de capture instantanée :

| Cause : | Solution |
| --- | --- |
| La sauvegarde SQL Server a été configurée que la machine virtuelle. | Par défaut, la sauvegarde de machine virtuelle exécute une sauvegarde complète VSS sur les machines virtuelles Windows. Sur les machines virtuelles exécutant des serveurs basés sur SQL Server et sur lesquelles la sauvegarde SQL Server est configurée, des retards d’exécution des captures instantanées peuvent survenir.<br><br>Définissez la clé de Registre suivante si vous rencontrez des échecs de sauvegarde en raison de problèmes de capture instantanée.<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| L’état de la machine virtuelle est rapporté de manière incorrecte en raison de l’arrêt de la machine virtuelle dans RDP. | Si vous avez arrêté la machine virtuelle dans Remote Desktop Protocol (RDP), retournez sur le portail pour vérifier que l’état de la machine virtuelle est correct. Si ce n’est pas le cas, arrêtez la machine virtuelle dans le portail à l’aide de l’option **Arrêter** dans le tableau de bord de la machine virtuelle. |
| Plusieurs machines virtuelles du même service cloud sont configurées pour exécuter la sauvegarde en même temps. | Il est recommandé d’étaler les planifications de sauvegarde entre les différentes machines virtuelles d’un même service cloud. |
| Forte sollicitation du processeur ou de la mémoire sur la machine virtuelle. | Si la machine virtuelle sollicite fortement le processeur (plus de 90 pour cent) ou la mémoire, la tâche de capture instantanée est mise en file d’attente et retardée, puis finit par expirer. En pareille situation, essayez de procéder à une sauvegarde à la demande. |
| La machine virtuelle ne peut pas obtenir l’adresse d’hôte/de structure du protocole DHCP. | Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne.  Si la machine virtuelle ne peut pas bénéficier de l’adresse d’hôte/de structure de la réponse DHCP 245, elle ne peut ni télécharger, ni exécuter d’extension. Si vous avez besoin d’une adresse IP privée statique, vous devez la configurer via la plateforme. L’option DHCP à l’intérieur de la machine virtuelle doit être laissée désactivée. Pour en savoir plus, consultez la [définition d’une adresse IP privée interne statique](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>L’extension de sauvegarde ne peut être ni mise à jour ni chargée
Si les extensions ne peuvent pas être chargées, la sauvegarde échoue en raison de l’impossibilité de capturer un instantané.

#### <a name="solution"></a>Solution

**Pour les invités Windows :** vérifiez que le service iaasvmprovider est activé et qu’il a un type de démarrage *automatique*. Si ce n’est pas le cas, activez le service pour déterminer si la sauvegarde suivante réussit.

**Pour les invités Linux :** vérifiez que la dernière version de VMSnapshot pour Linux (extension utilisée par la sauvegarde) est 1.0.91.0.<br>


Si l’extension de sauvegarde rencontre toujours un échec lors de la mise à jour ou du chargement, vous pouvez forcer le rechargement de l’extension VMSnapshot en désinstallant l’extension. La prochaine tentative de sauvegarde rechargera l’extension.

Pour désinstaller l’extension, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com/).
2. Localisez la machine virtuelle qui rencontre des problèmes de sauvegarde.
3. Cliquez sur **Paramètres**.
4. Cliquez sur **Extensions**.
5. Cliquez sur **Extension Vmsnapshot**.
6. Cliquer sur **Désinstaller**.

Cette procédure réinstalle l’extension lors de la sauvegarde suivante.

