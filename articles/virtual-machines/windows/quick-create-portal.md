---
title: "Démarrage rapide avec Azure - Portail pour la création d’une machine virtuelle Windows | Microsoft Docs"
description: "Démarrage rapide avec Azure - Portail pour la création d’une machine virtuelle Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ef2fdfc8f248f9ec5dd9fce9753548dfe802982a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Créer une machine virtuelle Windows avec le portail Azure

Les machines virtuelles Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des machines virtuelles et toutes les ressources liées. Ce guide de démarrage rapide décrit les étapes permettant de créer une machine virtuelle et d’y installer un serveur web.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse https://portal.azure.com.

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 

3. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm). Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

4. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. 

    ![Capture d’écran montrant les tailles de machine virtuelle](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

5. Sous **Paramètres**, conservez les valeurs par défaut et cliquez sur **OK**. 

6. Sur la page Résumé, cliquez sur **OK** pour lancer le déploiement de machine virtuelle.

7. La machine virtuelle sera épinglée au tableau de bord du portail Azure. Une fois le déploiement terminé, le récapitulatif de la machine virtuelle s’ouvre automatiquement.


## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Créez une connexion Bureau à distance à la machine virtuelle.

1. Cliquez sur le bouton **Connexion** dans les propriétés de la machine virtuelle. Un fichier de protocole Remote Desktop Protocol (fichier .rdp) est créé et téléchargé.

    ![Portail 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. À l’invite, cliquez sur **Se connecter**. Sur un Mac, vous avez besoin d’un client RDP similaire à ce [Client Bureau à distance](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponible sur le Mac App Store.

3. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle, puis cliquez sur **OK**.

4. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion.


## <a name="install-iis-using-powershell"></a>Installation de IIS à l’aide de PowerShell

Sur la machine virtuelle, démarrez une session PowerShell et exécutez la commande suivante pour installer IIS.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Lorsque vous avez terminé, quittez la session RDP et revenez aux propriétés de la machine virtuelle dans le portail Azure.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Un groupe de sécurité réseau (NSG) sécurise les trafics entrant et sortant. Lorsqu’une machine virtuelle est créée à partir du portail Azure, une règle de trafic entrant est créée sur le port 3389 pour les connexions RDP. Comme cette machine virtuelle héberge un serveur web, vous devez créer une règle NSG pour le port 80.

1. Sur la machine virtuelle, cliquez sur le nom du **Groupe de ressources**.
2. Sélectionnez le **groupe de sécurité réseau**. Le groupe de sécurité réseau peut être identifié à l’aide de la colonne **Type**. 
3. Dans le menu de gauche, sous Paramètres, cliquez sur **Règles de sécurité entrantes**.
4. Cliquez sur **Ajouter**.
5. Dans **Nom**, tapez **http**. Assurez-vous que l’option **Plage de ports** est définie sur 80 et l’option **Action** sur **Autoriser**. 
6. Cliquez sur **OK**.


## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Avec IIS installé et le port 80 ouvert pour votre machine virtuelle, le serveur web est désormais accessible à partir d’internet. Ouvrez un navigateur web et saisissez l’adresse IP publique de la machine virtuelle. L’adresse IP publique peut être trouvée sous *Machines virtuelles* dans le portail Azure.

![Site IIS par défaut](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources de la machine virtuelle et cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple ainsi qu’une règle de groupe de sécurité réseau, et installé un serveur web. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)
