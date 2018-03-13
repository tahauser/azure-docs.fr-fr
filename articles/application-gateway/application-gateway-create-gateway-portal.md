---
title: "Créer une passerelle Application Gateway - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une passerelle Application Gateway à l’aide du portail Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: df9235bc7ff61943de52a0bcc4064bf9fab6636a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Créer une passerelle d’application à l’aide du portail Azure

Vous pouvez utiliser le portail Azure pour créer ou gérer des passerelles d’application. Ce guide de démarrage rapide montre comment créer des ressources réseau, des serveurs principaux et une passerelle d’application.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Un réseau virtuel est nécessaire pour la communication entre les ressources que vous créez. Deux sous-réseaux sont créés dans cet exemple : une pour la passerelle d’application et l’autre pour les serveurs principaux. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application.

1. Cliquez sur **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste de suggestions.
3. Entrez ces valeurs pour la passerelle d’application :

    - *myAppGateway* : pour le nom de la passerelle d’application.
    - *myResourceGroupAG* : pour le nouveau groupe de ressources.

    ![Créer une nouvelle passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
5. Cliquez sur **Choisir un réseau virtuel**, cliquez sur **Créer nouveau**, puis entrez ces valeurs pour le réseau virtuel :

    - *myVNet* : pour le nom du réseau virtuel.
    - *10.0.0.0/16* : pour l’espace d’adressage du réseau virtuel.
    - *myAGSubnet* : pour le nom du sous-réseau.
    - *10.0.0.0/24* : pour l’espace d’adressage du sous-réseau.

    ![Création d’un réseau virtuel](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Cliquez sur **OK** pour créer le réseau virtuel et le sous-réseau.
6. Cliquez sur **Choisir une adresse IP publique**, cliquez sur **Créer nouveau**, puis entrez le nom de l’adresse IP publique. Dans cet exemple, l’adresse IP publique est nommée *myAGPublicIPAddress*. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
8. Acceptez les valeurs par défaut pour la configuration de l’écouteur, laissez le pare-feu d’applications web désactivé, puis cliquez sur **OK**.
9. Passez en revue les paramètres sur la page de résumé, puis cliquez sur **OK** pour créer le réseau virtuel, l’adresse IP publique et la passerelle d’application. La création de la passerelle d’application peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myVNet** dans la liste des ressources.
2. Cliquez sur **Sous-réseaux**, puis cliquez sur **Sous-réseau**.

    ![Créer un sous-réseau](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Entrez *myBackendSubnet* pour le nom du sous-réseau, puis cliquez sur **OK**.

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cet exemple, vous créez deux machines virtuelles à utiliser en tant que serveurs principaux pour la passerelle d’application. Vous installez également IIS sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Cliquez sur **Nouveau**.
2. Cliquez sur **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste de suggestions.
3. Entrez ces valeurs pour la machine virtuelle :

    - *myVM* : pour le nom de la machine virtuelle.
    - *azureuser* : pour le nom d’utilisateur administrateur.
    - *Azure123456!* pour le mot de passe.
    - Sélectionnez **Utiliser l’existant**, puis *myResourceGroupAG*.

4. Cliquez sur **OK**.
5. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
6. Assurez-vous que **myVNet** est sélectionné pour le réseau virtuel et que le sous-réseau est **myBackendSubnet**. 
7. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage.
8. Cliquez sur **OK**, vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.

### <a name="install-iis"></a>Installer IIS

1. Ouvrez l’interpréteur de commandes interactif et assurez-vous qu’il est défini sur **PowerShell**.

    ![Installer l’extension personnalisée](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle : 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Créez une deuxième machine virtuelle et installez IIS à l’aide de la procédure que vous venez de terminer. Entrez *myVM2* pour son nom et VMName dans Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Ajouter des serveurs principaux

3. Cliquez sur **Toutes les ressources**, puis sur **myAppGateway**.
4. Cliquez sur **Pools principaux**. Un pool par défaut a été automatiquement créé avec la passerelle d’application. Cliquez sur **appGatewayBackendPool**.
5. Cliquez sur **Ajouter une cible** pour ajouter chaque machine virtuelle que vous avez créée au pool principal.

    ![Ajouter des serveurs principaux](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Cliquez sur **Enregistrer**.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Recherchez l’adresse IP publique de la passerelle d’application sur l’écran de présentation. Cliquez sur **Toutes les ressources**, puis sur **myAGPublicIPAddress**.

    ![Enregistrer l’adresse IP publique de la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

    ![Tester la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle Application Gateway et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources qui contient la passerelle d’application, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un groupe de ressources, des ressources réseau et des serveurs principaux. Vous avez ensuite utilisé ces ressources pour créer une passerelle d’application. Pour plus d’informations sur les passerelles d’application et leurs ressources associées, consultez les articles de procédures.
