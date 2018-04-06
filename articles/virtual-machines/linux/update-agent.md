---
title: Mettre à jour l’agent Linux Azure à partir de GitHub | Microsoft Docs
description: Découvrez comment mettre à jour l’agent Linux Azure pour votre machine virtuelle Linux dans Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: danis
ms.openlocfilehash: ed5bea98bc9b4849436ff5fafc90f85c99d582aa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Guide pratique pour mettre à jour l’agent Linux Azure sur une machine virtuelle

Pour mettre à jour votre [agent Linux Azure](https://github.com/Azure/WALinuxAgent) sur une machine virtuelle Linux dans Azure vous devez déjà disposer des éléments suivants :

- Une machine virtuelle Linux en cours d'exécution dans Azure.
- Une connexion à cette machine virtuelle Linux à l'aide de SSH.

Vous devez toujours d’abord vérifier s’il existe un package dans le référentiel de distribution Linux. Il est possible que le package disponible ne soit pas la dernière version. Toutefois, l’activation de la mise à jour automatique permet de s’assurer que l’agent Linux obtiendra toujours la mise à jour la plus récente. Si vous avez des problèmes d’installation à partir des gestionnaires de package, contactez l’éditeur de distribution pour obtenir de l’aide.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Prise en charge minimale de l’agent de machine virtuelle dans Azure
Avant de continuer, vérifiez la [Prise en charge de version minimale pour les agents de machine virtuelle dans Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="updating-the-azure-linux-agent"></a>Mise à jour de l’agent Linux Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Mettre à jour le cache du package

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Redémarrer le service waagent

#### <a name="restart-agent-for-1404"></a>Redémarrer l’agent pour 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Redémarrer l’agent pour 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 « Wheezy »

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Mettre à jour le cache du package

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Activer la mise à jour automatique de l’agent
Cette version de Debian n’a pas de version supérieure ou égale à 2.0.16. Par conséquent, AutoUpdate n’est pas disponible pour elle. La sortie de la commande ci-dessus indique si le package est à jour.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 « Jessie » / Debian 9 « Stretch »

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Mettre à jour le cache du package

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée 

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Redémarrer le service waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Redhat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Vérifier les mises à jour disponibles

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée 

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Redémarrer le service waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Vérifier les mises à jour disponibles

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée 

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Redémarrer le service waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Vérifier les mises à jour disponibles

La sortie ci-dessus indique si le package est à jour.

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée 

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Redémarrer le service waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Vérifier votre version actuelle du package

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Vérifier les mises à jour disponibles

La sortie de la commande ci-dessus indique si le package est à jour.

#### <a name="install-the-latest-package-version"></a>Installer la dernière version du package

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée 

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Redémarrer le service waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 et 7

Pour Oracle Linux, assurez-vous que le référentiel `Addons` est activé. Modifiez le fichier `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux), modifiez la ligne `enabled=0` en `enabled=1` sous **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.

Puis, pour installer la dernière version de l'agent Linux Azure, tapez :

```bash
sudo yum install WALinuxAgent
```

Si vous ne trouvez pas le référentiel de compléments, vous pouvez ajouter ces lignes à la fin de votre fichier .repo en fonction de votre version d’Oracle Linux :

Pour les machines virtuelles Oracle Linux 6 :

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Pour les machines virtuelles Oracle Linux 7 :

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Tapez ensuite :

```bash
sudo yum update WALinuxAgent
```

Généralement, c’est tout ce qu’il vous est demandé de faire. Toutefois, si pour une raison quelconque vous devez l’installer directement à partir de https://github.com, procédez comme suit.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Mettre à jour l’agent Linux quand il n’existe aucun package d’agent pour la distribution

Installez wget (certaines versions, telles que Redhat, CentOS et Oracle Linux versions 6.4 et 6.5, ne l’installent pas par défaut) en tapant `sudo yum install wget` sur la ligne de commande.

### <a name="1-download-the-latest-version"></a>1. Téléchargez la dernière version
Ouvrez [la version de l’agent Linux Azure dans Github](https://github.com/Azure/WALinuxAgent/releases) dans une page web et cherchez le dernier numéro de version. (Vous pouvez rechercher votre version actuelle en tapant `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Pour la version 2.2.x ou ultérieure, tapez :
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip.zip
cd WALinuxAgent-2.2.x
```

La ligne suivante utilise la version 2.2.0 comme exemple :

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installez l'agent Linux Azure.

#### <a name="for-version-22x-use"></a>Pour la version 2.2.x, tapez :
Vous devrez peut-être installer d’abord le package `setuptools`. Consultez [ces informations](https://pypi.python.org/pypi/setuptools). Exécutez ensuite la commande suivante :

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Vérifier que la mise à jour automatique est activée

Tout d’abord, vérifiez si elle est activée :

```bash
cat /etc/waagent.conf
```

Recherchez « AutoUpdate.Enabled ». Si vous voyez cette sortie, cela signifie qu’elle est activée :

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Pour l’activer, exécutez :

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Redémarrer le service waagent
Pour la plupart des versions de linux :

```bash
sudo service waagent restart
```

Pour Ubuntu, procédez comme suit :

```bash
sudo service walinuxagent restart
```

Pour CoreOS, procédez comme suit :

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirmer la version de l'agent Linux Azure
    
```bash
waagent -version
```

Pour CoreOS, la commande ci-dessus peut ne pas fonctionner.

Vous verrez que la version de l'agent Linux Azure a été mise à jour vers la nouvelle version.

Pour plus d’informations sur l’agent Linux Azure, consultez le fichier [Lisezmoi de l’agent Linux Azure](https://github.com/Azure/WALinuxAgent).
