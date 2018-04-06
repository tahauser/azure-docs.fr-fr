---
title: Bureau à distance sur une machine virtuelle Linux | Microsoft Docs
description: Découvrez comment installer et configurer le Bureau à distance pour vous connecter à une machine virtuelle Linux Microsoft Azure pour le modèle de déploiement classique.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 0e1bfe468e1572ca98be956d39d82df562dce0e6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Utilisation du Bureau à distance pour se connecter à une machine virtuelle Linux Microsoft Azure.
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour afficher la version de Resource Manager mise à jour de cet article, voir [ici](../use-remote-desktop.md).

## <a name="overview"></a>Vue d'ensemble
Le protocole RDP (Remote Desktop Protocol) est un protocole propriétaire utilisé pour Windows. Comment pouvons-nous utiliser le protocole RDP pour se connecter à distance à une machine virtuelle Linux ?

Ce guide vous donne la réponse ! Il vous aidera à installer et à configurer xrdp sur votre machine virtuelle Linux Microsoft Azure. Vous pourrez vous y connecter avec le Bureau à distance à partir d’une machine Windows. Nous allons utiliser la machine virtuelle Linux exécutant Ubuntu ou OpenSUSE comme exemple dans ce guide.

L’outil xrdp est un serveur RDP open source, qui vous permet de connecter votre serveur Linux avec le Bureau à distance à partir d’une machine Windows. Le protocole RDP fonctionne mieux que VNC (Virtual Network Computing). VNC affiche des graphiques de qualité JPEG et peut être lent, alors que RDP est rapide et parfaitement net.

> [!NOTE]
> Vous devez déjà disposer d’une machine virtuelle Microsoft Azure exécutant Linux. Pour créer et configurer une machine virtuelle Linux, voir le [Didacticiel sur les machines virtuelles Linux Azure](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Créer un point de terminaison pour le Bureau à distance
Nous allons utiliser dans ce document le point de terminaison par défaut 3389 pour Bureau à distance. Définissez le point de terminaison 3389 comme `Remote Desktop` pour votre machine virtuelle Linux, comme ci-dessous :

![image](./media/remote-desktop/endpoint-for-linux-server.png)

Si vous ne savez pas comment configurer un point de terminaison pour votre machine virtuelle, lisez [ces conseils](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Installer Gnome Desktop
Connectez-vous à votre machine virtuelle Linux via `putty` et installez `Gnome Desktop`.

Pour Ubuntu, procédez comme suit :

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Pour OpenSUSE, utilisez :

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Installer xdrp
Pour Ubuntu, procédez comme suit :

```bash
sudo apt-get install xrdp
```

Pour OpenSUSE, utilisez :

> [!NOTE]
> Mettez à jour la version d’OpenSUSE avec la version que vous utilisez dans la commande suivante. Voici un exemple de commande pour `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Démarrez xrdp et paramétrez le service xdrp au démarrage
Pour OpenSUSE, utilisez :

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Pour Ubuntu, xrdp est démarré et activé au démarrage automatiquement après l’installation.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Utilisation de xfce si vous utilisez une version d’Ubuntu ultérieure à 12.04LTS
Comme la version actuelle de xrdp ne prend pas en charge Gnome Desktop pour les versions d’Ubuntu ultérieures à 12.04LTS, nous allons utiliser `xfce` Desktop à la place.

Pour installer `xfce`, utilisez cette commande :

```bash
sudo apt-get install xubuntu-desktop
```

Activez ensuite `xfce` à l’aide de cette commande :

```bash
echo xfce4-session >~/.xsession
```

Modifiez le fichier de configuration `/etc/xrdp/startwm.sh` :

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Ajoutez la ligne `xfce4-session` avant la ligne `/etc/X11/Xsession`.

Pour redémarrer le service xrdp, utilisez cette commande :

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Connectez votre machine virtuelle Linux à partir d’un ordinateur Windows
Sur un ordinateur Windows, démarrez le client Bureau à distance et entrez le nom DNS de votre machine virtuelle Linux. Vous pouvez également accéder au tableau de bord de votre machine virtuelle dans le portail Azure et cliquer sur `Connect` pour connecter votre machine virtuelle Linux. Dans ce cas, vous verrez la fenêtre de connexion ci-dessous :

![image](./media/remote-desktop/no2.png)

Connectez-vous avec le nom d’utilisateur et le mot de passe de votre machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de xrdp, voir [http://www.xrdp.org/](http://www.xrdp.org/).
