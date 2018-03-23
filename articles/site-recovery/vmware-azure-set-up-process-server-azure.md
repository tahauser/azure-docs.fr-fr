---
title: Configurer un serveur de processus dans Azure pour la restauration automatique d’une machine virtuelle VMware et d’un serveur physique avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment configurer un serveur de processus dans Azure pour restaurer automatiquement des machines virtuelles Azure dans VMware.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: c6ef0ae663727c519f9b6a8a56027a3dd8a9503d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurer un serveur de processus dans Azure à des fins de restauration automatique

Après avoir restauré automatiquement des machines virtuelles VMware ou des serveurs physiques dans Azure avec [Site Recovery](site-recovery-overview.md), vous pouvez les rebasculer sur le site local dès qu’ils sont de nouveau opérationnels. Pour effectuer une restauration automatique, vous devez configurer un serveur de processus dans Azure de manière à pouvoir gérer la réplication entre Azure et le site local. Vous pouvez supprimer cette machine virtuelle à l’issue de la restauration automatique.

## <a name="before-you-start"></a>Avant de commencer

En savoir plus sur le processus de [reprotection](vmware-azure-reprotect.md) et de [restauration automatique](vmware-azure-failback.md).

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Déployer un serveur de processus dans Azure

1. Dans le coffre > **Infrastructure Site Recovery**> **Gérer** > **Serveurs de Configuration**, sélectionnez le serveur de configuration.
2. Sur la page des serveurs, cliquez sur **+ Serveur de processus**
3. Sur la page **Ajouter un serveur de processus**, choisissez de déployer le serveur de processus dans Azure.
4. Spécifiez les paramètres Azure, y compris l’abonnement utilisé pour la restauration automatique, un groupe de ressources, la région Azure utilisée pour la restauration automatique et le réseau virtuel dans lequel se trouvent les machines virtuelles Azure. Si vous avez utilisé plusieurs réseaux Azure, vous avez besoin d’un serveur de processus dans chacun d’eux.

  ![Élément de la galerie Ajouter un serveur de processus](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Dans **Nom du serveur**, **Nom d’utilisateur** et **Mot de passe**, spécifiez un nom pour le serveur de processus, ainsi que les informations d’identification pour lesquelles seront accordées des autorisations d’administrateur sur le serveur.
5. Spécifiez un compte de stockage à utiliser pour les disques de machine virtuelle du serveur, le sous-réseau dans lequel se trouve la machine virtuelle du serveur de processus, ainsi que l’adresse IP du serveur qui sera attribuée au démarrage de la machine virtuelle.
6. Cliquez sur le bouton **OK** pour commencer le déploiement de la machine virtuelle du serveur de processus.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Inscription du serveur de processus (en cours d’exécution dans Azure) auprès d’un serveur de Configuration (en cours d’exécution en local)

Une fois la machine virtuelle du serveur de processus entièrement opérationnelle, vous devez l’inscrire auprès du serveur de configuration local, comme suit :

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


