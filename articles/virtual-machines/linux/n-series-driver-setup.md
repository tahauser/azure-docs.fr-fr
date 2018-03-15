---
title: "Configuration du pilote série N Azure pour Linux | Microsoft Docs"
description: "Procédure de configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Linux dans Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 201734661873c7ac7f7a5dd710009eb324cedc86
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installer les pilotes GPU NVIDIA sur les machines virtuelles série N exécutant Linux

Pour tirer parti des fonctionnalités GPU de machines virtuelles série N Azure exécutant Linux, installez des pilotes graphiques NVIDIA pris en charge. Cet article vous offre des étapes de configuration de pilote lorsque vous avez déployé une machine virtuelle de série N. Des informations de configuration du pilote sont également disponibles pour [les machines virtuelles Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus d’informations sur les spécifications, les capacités de stockage et les disques des machines virtuelles série N, consultez l’article [Tailles de machine virtuelle Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-ncv3-and-nd-series-vms"></a>Installer des pilotes CUDA pour des machines virtuelles de série NC, NCv2, NCv3 et ND

Voici les étapes pour installer les pilotes NVIDIA à partir du kit d’outils CUDA NVIDIA sur des machines virtuelles de série N. 

Les développeurs C et C++ peuvent éventuellement installer le kit d’outils complet pour créer des applications avec accélération GPU. Pour plus d’informations, consultez le [Guide d’installation de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

> [!NOTE]
> Les liens de téléchargement de pilotes CUDA fournis ici sont à jour au moment de la publication. Pour les pilotes CUDA les plus récents, visitez le site web de [NVIDIA](https://developer.nvidia.com/cuda-zone).
>

Pour installer le kit d’outils CUDA, établissez une connexion SSH à chaque machine virtuelle. Pour vérifier que le système dispose d’un GPU compatible CUDA, exécutez la commande suivante :

```bash
lspci | grep -i NVIDIA
```
Vous verrez une sortie similaire à l’exemple suivant (illustrant une carte K80 Tesla NVIDIA) :

![Sortie de commande Ispci](./media/n-series-driver-setup/lspci.png)

Ensuite, exécutez les commandes d’installation spécifiques de votre distribution.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Téléchargez et installez les pilotes CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  L’installation peut prendre plusieurs minutes.

2. Pour éventuellement installer le kit d’outils CUDA complet, saisissez :

  ```bash
  sudo apt-get install cuda
  ```

3. Redémarrez la machine virtuelle et vérifiez l’installation.

#### <a name="cuda-driver-updates"></a>Mises à jour de pilote CUDA

Nous vous recommandons de régulièrement mettre à jour les pilotes CUDA après le déploiement.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS ou Red Hat Enterprise Linux 7.3 ou 7.4

1. Mettez à jour le noyau.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest Linux Integration Services for Hyper-V.

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.4.tar.gz
 
  tar xvzf lis-rpms-4.2.4.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Reconnectez-vous à la machine virtuelle et continuez l’installation avec les commandes suivantes :

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  L’installation peut prendre plusieurs minutes. 

4. Pour éventuellement installer le kit d’outils CUDA complet, saisissez :

  ```bash
  sudo yum install cuda
  ```

5. Redémarrez la machine virtuelle et vérifiez l’installation.

### <a name="verify-driver-installation"></a>Vérification de l’installation du pilote

Pour interroger l’état de l’appareil GPU, connectez-vous par SSH à la machine virtuelle et exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote. 

Si le pilote est installé, vous obtenez un résultat qui ressemble à celui indiqué. Notez que **GPU-Util** affiche 0 %, sauf si vous exécutez actuellement une charge de travail GPU sur la machine virtuelle. La version de votre pilote et vos détails de GPU peuvent différer de ceux indiqués.

![État de l’appareil NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Connectivité réseau RDMA

La connectivité réseau RDMA peut être activée sur des machines virtuelles de série N compatibles RDMA, comme les machines NC24r déployées dans le même groupe à haute disponibilité. Le réseau RDMA prend en charge le trafic MPI (Message Passing Interface) pour les applications exécutées avec Intel MPI 5.x ou une version ultérieure. Des conditions supplémentaires suivent :

### <a name="distributions"></a>Distributions

Déployez des machines virtuelles de série N compatibles RDMA à partir d’une image dans la Place de marché Microsoft Azure qui prend en charge la connectivité RDMA sur les machines virtuelles de série N :
  
* **Ubuntu 16.04 LTS** - Configurez les pilotes RDMA sur la machine virtuelle et inscrivez-vous auprès d’Intel pour télécharger Intel MPI :

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

> [!NOTE]
> Les images HPC CentOS ne sont pas actuellement recommandées pour la connectivité RDMA sur les machines virtuelles de série N. RDMA n’est pas pris en charge sur le dernier noyau CentOS 7.4 qui prend en charge les GPU NVIDIA.
> 


## <a name="install-grid-drivers-for-nv-series-vms"></a>Installer des pilotes GRID pour des machines virtuelles de série NV

Pour installer des pilotes GRID NVIDIA sur des machines virtuelles de série NV, établissez une connexion SSH à chaque machine virtuelle et suivez les étapes de votre distribution Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Exécutez la commande `lspci`. Vérifiez que la ou les cartes NVIDIA M60 sont visibles en tant que périphériques PCI.

2. Installez les mises à jour.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Désactivez le pilote du noyau Nouveau, qui n’est pas compatible avec le pilote NVIDIA. (Utilisez uniquement le pilote NVIDIA sur les machines virtuelles NV.) Pour ce faire, créez un fichier `/etc/modprobe.d `nommé `nouveau.conf` avec le contenu suivant :

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Redémarrez la machine virtuelle et reconnectez-vous. Quittez le serveur X :

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Téléchargez et installez le pilote GRID :

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 

6. Lorsque vous êtes invité à indiquer si vous souhaitez exécuter l’utilitaire nvidia-xconfig pour mettre à jour votre fichier de configuration X, sélectionnez **Oui**.

7. Une fois l’installation terminée, copiez /etc/nvidia/gridd.conf.template sur un nouveau fichier gridd.conf dà l’emplacement etc/nvidia /

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Ajoutez la ligne suivante à `/etc/nvidia/gridd.conf` :
 
  ```
  IgnoreSP=TRUE
  ```
9. Redémarrez la machine virtuelle et vérifiez l’installation.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Mettez à jour le noyau et DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Désactivez le pilote du noyau Nouveau, qui n’est pas compatible avec le pilote NVIDIA. (Utilisez uniquement le pilote NVIDIA sur les machines virtuelles NV.) Pour ce faire, créez un fichier `/etc/modprobe.d `nommé `nouveau.conf` avec le contenu suivant :

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Redémarrez la machine virtuelle, reconnectez-vous et installez les derniers services d’intégration Linux pour Hyper-V :
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.4.tar.gz

  tar xvzf lis-rpms-4.2.4.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Reconnectez-vous à la machine virtuelle et exécutez la commande `lspci`. Vérifiez que la ou les cartes NVIDIA M60 sont visibles en tant que périphériques PCI.
 
5. Téléchargez et installez le pilote GRID :

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 
6. Lorsque vous êtes invité à indiquer si vous souhaitez exécuter l’utilitaire nvidia-xconfig pour mettre à jour votre fichier de configuration X, sélectionnez **Oui**.

7. Une fois l’installation terminée, copiez /etc/nvidia/gridd.conf.template sur un nouveau fichier gridd.conf dà l’emplacement etc/nvidia /
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Ajoutez la ligne suivante à `/etc/nvidia/gridd.conf` :
 
  ```
  IgnoreSP=TRUE
  ```
9. Redémarrez la machine virtuelle et vérifiez l’installation.

### <a name="verify-driver-installation"></a>Vérification de l’installation du pilote


Pour interroger l’état de l’appareil GPU, connectez-vous par SSH à la machine virtuelle et exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote. 

Si le pilote est installé, vous obtenez un résultat qui ressemble à celui indiqué. Notez que **GPU-Util** affiche 0 %, sauf si vous exécutez actuellement une charge de travail GPU sur la machine virtuelle. La version de votre pilote et vos détails de GPU peuvent différer de ceux indiqués.

![État de l’appareil NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Serveur X11
Si vous avez besoin d’un serveur X11 pour les connexions à distance vers une machine virtuelle NV, [x11vnc](http://www.karlrunge.com/x11vnc/) est recommandé, car il permet l’accélération matérielle des graphiques. Le BusID de l’appareil M60 doit être ajouté manuellement au fichier xconfig (`etc/X11/xorg.conf` sur Ubuntu 16.04 LTS, `/etc/X11/XF86config` sur CentOS 7.3 ou Red Hat Enterprise Server 7.3). Ajoutez une section `"Device"` similaire à la suivante :
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
En outre, mettez à jour votre section `"Screen"` pour utiliser cet appareil.
 
Vous trouverez le BusID en exécutant

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
Le BusID peut changer lorsqu’une machine virtuelle est réaffectée ou redémarrée. Par conséquent, il peut être judicieux d’utiliser un script pour mettre à jour le BusID dans la configuration X11 lors du redémarrage d’une machine virtuelle. Par exemple : 

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Ce fichier peut être appelé en tant que racine au démarrage en créant une entrée pour lui dans `/etc/rc.d/rc3.d`.

## <a name="troubleshooting"></a>Résolution de problèmes

* Vous pouvez définir le mode de persistance à l’aide de `nvidia-smi`. De cette façon, la sortie de la commande est plus rapide quand vous avez besoin d’effectuer une requête sur les cartes. Pour définir le mode de persistance, exécutez `nvidia-smi -pm 1`. Notez que si la machine virtuelle est redémarrée, le paramètre du mode n’est pas conservé. Vous pouvez toujours définir le paramètre du mode dans un script à exécuter au démarrage.

## <a name="next-steps"></a>étapes suivantes

* Pour capturer une image Linux VM avec vos pilotes NVIDIA installés, consultez [Généraliser et capturer une machine virtuelle Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
