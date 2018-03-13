---
title: Ouvrir une session sur une machine virtuelle Azure Classic | Microsoft Docs
description: "Utilisez le Portail Azure pour vous connecter à une machine virtuelle Windows créée avec le modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: c35bfeaa81f89dc5a636d67ed9a2a930d689c54a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Ouvrir une session sur une machine virtuelle Windows à l’aide du portail Azure
Sur le portail Azure, vous utilisez le bouton **Connecter** pour démarrer une session Bureau à distance et ouvrir une session sur une machine virtuelle Windows.

Vous souhaitez vous connecter à une machine virtuelle Linux ? Consultez [Connexion à une machine virtuelle sous Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur la connexion à une machine virtuelle avec le modèle Resource Manager, suivez [ce lien](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.
1. Connectez-vous au portail Azure.
2. Cliquez sur la machine virtuelle à laquelle vous souhaitez accéder. Le nom est répertorié dans le volet **Toutes les ressources**.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. Cliquez sur **Connexion** dans la barre de commandes en haut du tableau de bord de la machine virtuelle.

    ![Icône Connexion de la machine virtuelle](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Connexion à la machine virtuelle
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Étapes suivantes
* Si le bouton **Connexion** est inactif ou que vous rencontrez d’autres problèmes avec la connexion Bureau à distance, essayez de réinitialiser la configuration. Dans le tableau de bord de la machine virtuelle, cliquez sur **Réinitialiser la configuration à distance**.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Si votre mot de passe pose problème, essayez de le réinitialiser. Cliquez sur **Réinitialiser le mot de passe** le long du bord gauche de du tableau de bord de la machine virtuelle, sous **Support + dépannage**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Si ces conseils ne donnent aucun résultat ou ne vous sont pas utiles, consultez [Résolution des problèmes de connexion du Bureau à distance à une machine virtuelle Azure sous Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.
