---
title: "Vue d’ensemble de l’agent de machine virtuelle Linux Azure | Microsoft Docs"
description: "Apprenez à installer et à configurer l'agent Linux (waagent) pour gérer l'interaction de votre machine virtuelle avec le contrôleur de structure Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: szark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59266c6d6452eeff56b05e60389ac14f0b2c3f1f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Présentation et utilisation de l’agent Linux Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introduction
L’agent Microsoft Azure Linux (waagent) gère l’approvisionnement de Linux et FreeBSD, ainsi que l’interaction des machines virtuelles avec le contrôleur de structure Azure. Il offre les fonctionnalités suivantes pour les déploiements IaaS Linux et FreeBSD :

> [!NOTE]
> Pour plus d’informations, consultez le fichier [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md) de l’agent Linux Azure.
> 
> 

* **Approvisionnement d’image**
  
  * Création d'un compte d'utilisateur
  * Configuration des types d'authentification SSH
  * Déploiement des clés publiques et des paires de clés SSH
  * Définition du nom d'hôte
  * Publication du nom d'hôte sur la plateforme DNS
  * Génération de rapports sur l'empreinte digitale de la clé d'hôte SSH destinés à la plateforme
  * Gestion du disque de ressources
  * Formatage et montage du disque de ressources
  * Configuration de l'espace d'échange
* **Mise en réseau**
  
  * Gestion des itinéraires afin d'améliorer la compatibilité avec les serveurs DHCP de plateforme
  * Garantie de la stabilité du nom de l'interface réseau
* **Noyau**
  
  * Configure la topologie NUMA virtuelle (désactivée pour le noyau < à 2.6.37)
  * Consommation de l'entropie Hyper-V pour /dev/random
  * Configuration des délais d'expiration SCSI de l'appareil racine (qui peut être distant)
* **Diagnostics**
  
  * Redirection de la console vers le port série
* **Déploiements SCVMM**
  
  * Détection et amorçage de l'agent VMM pour Linux lors de l'exécution dans un environnement System Center Virtual Machine Manager 2012 R2
* **Extension de machine virtuelle**
  
  * Injection de composants créés par Microsoft et ses partenaires dans la machine virtuelle Linux pour activer les logiciels et l’automatisation de la configuration
  * Implémentation de référence de l’extension de machine virtuelle à l’adresse [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Communication
Le flux d'informations de la plateforme à l'agent se produit via deux canaux :

* Un DVD attaché au moment du démarrage pour les déploiements IaaS. Ce DVD comprend un fichier de configuration compatible OVF qui inclut toutes les informations d'approvisionnement différentes des paires de clés SSH réelles.
* Un point de terminaison TCP qui expose une API REST utilisée pour obtenir la configuration du déploiement et de la topologie.

## <a name="requirements"></a>Configuration requise
Les systèmes suivants ont été testés et fonctionnent avec l’agent Linux Azure :

> [!NOTE]
> Notez que cette liste peut être différente de la liste officielle des systèmes pris en charge sur la plateforme Microsoft Azure, disponible ici : [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Autres systèmes pris en charge :

* FreeBSD 10+ (agent Azure Linux v2.0.10+)

L’agent Linux repose sur certains packages système pour fonctionner correctement :

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Utilitaires de système de fichiers : sfdisk, fdisk, mkfs, séparés
* Outils de mot de passe : chpasswd, sudo
* Outils de traitement de texte : sed, grep
* Outils réseau : ip-route
* Prise en charge du noyau pour le montage de systèmes de fichiers UDF.

## <a name="installation"></a>Installation
L’installation à l’aide d’un package RPM ou DEB à partir de votre référentiel de packages de distribution est la méthode privilégiée pour installer et mettre à niveau l’agent Microsoft Linux Azure. Tous les [fournisseurs de distribution approuvés](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) intègrent l’agent Azure Linux dans leurs images et référentiels.

Consultez la documentation dans le [référentiel de l’agent Linux Azure sur GitHub](https://github.com/Azure/WALinuxAgent) pour connaître les options d’installation avancées, telles que les préfixes et l’installation à partir d’une source ou dans des emplacements personnalisés.

## <a name="command-line-options"></a>Options de la ligne de commande
### <a name="flags"></a>Indicateurs
* verbose : accroît le niveau de détail de la commande spécifiée.
* force : ignore la confirmation interactive de certaines commandes.

### <a name="commands"></a>Commandes
* help : répertorie les commandes et les indicateurs pris en charge.
* deprovision : essaie de nettoyer le système et de le préparer pour le réapprovisionnement. Cette opération supprime les éléments suivants :
  
  * toutes les clés de l'hôte SSH (si Provisioning.RegenerateSshHostKeyPair a la valeur « y » dans le fichier de configuration) ;
  * la configuration de Nameserver dans /etc/resolv.conf ;
  * le mot de passe racine de /etc/shadow (si Provisioning.DeleteRootPassword a la valeur « y » dans le fichier de configuration) ;
  * les baux du client DHCP mis en cache.
  * Réinitialise le nom d’hôte sur localhost.localdomain.

> [!WARNING]
> L’annulation de l’approvisionnement ne garantit pas que l’image est exempte de toute information sensible et qu’elle convient à la redistribution.
> 
> 

* deprovision+user : effectue tout ce qui est décrit sous -deprovision (ci-dessus) et supprime également le dernier compte d’utilisateur approvisionné (obtenu à partir de /var/lib/waagent) et les données associées. Ce paramètre est utilisé pour annuler l'approvisionnement d'une image qui a été précédemment approvisionnée sur Azure en vue d'être capturée et réutilisée.
* version : affiche la version de waagent.
* serialconsole : configure GRUB pour marquer ttyS0 (premier port série) en tant que console de démarrage. Les journaux de démarrage du noyau sont ainsi envoyés au port série et sont prêts à être débogués.
* daemon : exécute waagent en tant que démon afin de gérer l’interaction avec la plateforme. Cet argument est spécifié à waagent dans le script waagent init.
* start : exécute waagent en arrière-plan

## <a name="configuration"></a>Configuration
Un fichier de configuration (/etc/waagent.conf) contrôle les actions de waagent. Un exemple de fichier de configuration est affiché ci-dessous :

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y

Les diverses options de configuration sont décrites de manière détaillée ci-dessous. Elles sont de trois types : Boolean, String ou Integer. Les options de configuration Boolean peuvent être spécifiées à l'aide de « y » (oui) ou « n » (non). Le mot clé « None » (Aucun) peut être utilisé dans le cas de certaines entrées de type chaîne comme décrit ci-dessous.

**Provisioning.Enabled :**  
Type : booléen  
Par défaut : y

L'utilisateur peut activer ou désactiver la fonctionnalité d'approvisionnement dans l'agent. Les valeurs valides sont « y » ou « n ». Si l'approvisionnement est désactivé, les clés d'utilisateur et d'hôte SSH dans l'image sont conservées et toute configuration spécifiée dans l'API d'approvisionnement Azure est ignorée.

> [!NOTE]
> La valeur par défaut du paramètre `Provisioning.Enabled` est « n » dans les images cloud Ubuntu qui utilisent cloud-init pour l’approvisionnement.
> 
> 

**Provisioning.DeleteRootPassword :**  
Type : booléen  
Par défaut : n

Si elle est définie, le mot de passe racine dans le fichier /etc/shadow est effacé au cours du processus d'approvisionnement.

**Provisioning.RegenerateSshHostKeyPair :**  
Type : booléen  
Par défaut : y

Si elle est définie, toutes les paires de clés d'hôte SSH (ecdsa, dsa et rsa) sont supprimées de /etc/ssh/ au cours du processus d'approvisionnement. Une nouvelle paire de clés unique est générée.

L'entrée Provisioning.SshHostKeyPairType peut configurer le type de chiffrement pour la nouvelle paire de clés. Veuillez noter que certaines distributions créent à nouveau les paires de clés SSH pour tout type de chiffrement manquant au redémarrage du démon SSH.

**Provisioning.SshHostKeyPairType :**  
Type : string  
Par défaut : rsa

Un type d'algorithme de chiffrement qui est pris en charge par le démon SSH sur la machine virtuelle peut être défini. Les valeurs généralement prises en charge sont « rsa », « dsa » et « ecdsa ». Notez que « putty.exe » sur Windows ne prend pas en charge « ecdsa ». Si vous envisagez d'utiliser putty.exe sur Windows pour établir une connexion sur un déploiement Linux, veuillez utiliser « rsa » ou « dsa ».

**Provisioning.MonitorHostName :**  
Type : booléen  
Par défaut : y

Si elle est définie, waagent surveille la machine virtuelle Linux en vue de détecter des modifications de nom d'hôte (comme renvoyé par la commande « hostname ») et met automatiquement à jour la configuration de mise en réseau dans l'image afin de refléter la modification. Afin de transmettre la modification de nom aux serveurs DNS, la mise en réseau est redémarrée sur la machine virtuelle. La connexion Internet est alors brièvement interrompue.

**Provisioning.DecodeCustomData**  
Type : booléen  
Par défaut : n

Si ce paramètre est défini, waagent décodera CustomData en Base64.

**Provisioning.ExecuteCustomData**  
Type : booléen  
Par défaut : n

Si ce paramètre est défini, waagent exécute CustomData après l’approvisionnement.

**Provisioning.AllowResetSysUser** Type : Boolean Par défaut : n

Cette option permet de réinitialiser le mot de passe de l’utilisateur sys. Par défaut, elle est désactivée.

**Provisioning.PasswordCryptId**  
Type : string  
Par défaut : 6

Algorithme utilisé par crypt lors de la génération du hachage de mot de passe.  
 1 - MD5  
 2 a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
Type : string  
Valeur par défaut : 10

Longueur de la chaîne salt aléatoire utilisée lors de la génération du hachage de mot de passe.

**ResourceDisk.Format :**  
Type : booléen  
Par défaut : y

Si elle est définie, le disque de ressources fourni par la plateforme est formaté et monté par waagent si le type de système de fichiers demandé par l'utilisateur dans « ResourceDisk.Filesystem » est différent de « ntfs ». Une partition unique de type Linux (83) est mise à la disposition sur le disque. Notez que cette partition n'est pas formatée si elle ne peut pas être correctement montée.

**ResourceDisk.Filesystem :**  
Type : string  
Par défaut : ext4

Cette commande spécifie le type de système de fichiers pour le disque de ressources. Les valeurs prises en charge diffèrent selon la distribution Linux. Si la chaîne est X, mkfs.X doit être présent sur l'image Linux. Les images SLES 11 doivent généralement utiliser « ext3 ». Les images FreeBSD doivent utiliser « ufs2 » ici.

**ResourceDisk.MountPoint :**  
Type : string  
Par défaut : /mnt/resource 

Cette commande spécifie le chemin où le disque de ressources est monté. Notez que le disque de ressources est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle.

**ResourceDisk.MountOptions**  
Type : string  
Par défaut : aucun

Spécifie les options de montage de disque à transmettre à la commande mount -o. Les valeurs de cette liste sont séparées par des virgules, par exemple 'nodev,nosuid'. Pour plus d’informations, consultez mount(8).

**ResourceDisk.EnableSwap :**  
Type : booléen  
Par défaut : n

Si elle est définie, un fichier d'échange (/swapfile) est créé sur le disque de ressources et est ajouté à l'espace d'échange système.

**ResourceDisk.SwapSizeMB :**  
Type : entier  
Par défaut : 0

Taille du fichier d'échange en mégaoctets.

**Logs.Verbose :**  
Type : booléen  
Par défaut : n

Si elle est définie, le niveau de détail du journal est optimisé. Waagent enregistre dans /var/log/waagent.log et exploite la fonctionnalité logrotate du système pour faire tourner les journaux.

**OS.EnableRDMA**  
Type : booléen  
Par défaut : n

Si ce paramètre est défini, l’agent tente de s’installer, puis charge un pilote de noyau RDMA qui correspond à la version du microprogramme sur le matériel sous-jacent.

**OS.RootDeviceScsiTimeout :**  
Type : entier  
Par défaut : 300

Le délai d'expiration SCSI est configuré en secondes sur le disque du système d'exploitation et les lecteurs de données. Si elle n'est pas définie, les valeurs par défaut du système sont utilisées.

**OS.OpensslPath :**  
Type : string  
Par défaut : aucun

Cette commande sert à spécifier un autre chemin pour les données binaires openssl à utiliser pour les opérations de chiffrement.

**HttpProxy.Host, HttpProxy.Port**  
Type : string  
Par défaut : aucun

Si ce paramètre est défini, l’agent utilisera ce serveur proxy pour accéder à internet. 

**AutoUpdate.Enabled** Type : Boolean Par défaut : y

Activer ou désactiver la mise à jour automatique du traitement de l’état de l’objectif. Par défaut : activé.



## <a name="ubuntu-cloud-images"></a>Images cloud Ubuntu
Notez que les images cloud Ubuntu utilisent [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) pour exécuter les tâches de configuration qui pourraient être gérées par l’agent Linux Azure.  Notez les différences suivantes :

* **Provisioning.Enabled** est défini par défaut sur n sur les images cloud Ubuntu utilisant Cloud-init pour exécuter les tâches d’approvisionnement.
* Les paramètres de configuration suivants n’ont aucun effet sur les images cloud Ubuntu utilisant Cloud-init pour gérer le disque de ressources et l’espace d’échange :
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**
* Consultez les ressources suivantes pour configurer le point de montage du disque de ressources et l’espace d’échange sur les images cloud Ubuntu durant l’approvisionnement :
  
  * [Wiki Ubuntu : Configurer les partitions d’échange](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injection de données personnalisées dans une machine virtuelle Azure](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

