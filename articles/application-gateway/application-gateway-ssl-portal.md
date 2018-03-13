---
title: "Créer une passerelle d’application avec un arrêt SSL - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une passerelle d’application et ajouter un certificat pour un arrêt SSL à l’aide du portail Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: daab3ada5ef0cc20883130e4c12b1dc3570e63b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Créer une passerelle d’application avec un arrêt SSL à l’aide du portail Azure

Vous pouvez utiliser le portail Azure pour créer une [passerelle d’application](application-gateway-introduction.md) avec un certificat pour un arrêt SSL qui utilise des machines virtuelles pour des serveurs principaux.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Créer une passerelle d’application avec le certificat
> * Créer les machines virtuelles utilisées comme serveurs principaux

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Dans cette section, vous utilisez [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) pour créer un certificat auto-signé que vous chargez sur le portail Azure lorsque vous créez l’écouteur pour la passerelle d’application.

Sur votre ordinateur local, ouvrez une fenêtre Windows PowerShell en tant qu’administrateur. Exécutez la commande suivante pour créer le certificat :

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Une réponse comme l’exemple suivant devrait s’afficher :

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Un réseau virtuel est nécessaire pour la communication entre les ressources que vous créez. Deux sous-réseaux sont créés dans cet exemple : une pour la passerelle d’application et l’autre pour les serveurs principaux. Vous pouvez créer un réseau virtuel en même temps que la passerelle d’application.

1. Cliquez sur **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis sélectionnez **Application Gateway** dans la liste de suggestions.
3. Entrez *myAppGateway* pour le nom de la passerelle d’application et *myResourceGroupAG* pour le nouveau groupe de ressources.
4. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
5. Cliquez sur **Choisir un réseau virtuel**, cliquez sur **Créer nouveau**, puis entrez ces valeurs pour le réseau virtuel :

    - *myVNet* : pour le nom du réseau virtuel.
    - *10.0.0.0/16* : pour l’espace d’adressage du réseau virtuel.
    - *myAGSubnet* : pour le nom du sous-réseau.
    - *10.0.0.0/24* : pour l’espace d’adressage du sous-réseau.

    ![Création d’un réseau virtuel](./media/application-gateway-ssl-portal/application-gateway-vnet.png)

6. Cliquez sur **OK** pour créer le réseau virtuel et le sous-réseau.
7. Cliquez sur **Choisir une adresse IP publique**, cliquez sur **Créer nouveau**, puis entrez le nom de l’adresse IP publique. Dans cet exemple, l’adresse IP publique est nommée *myAGPublicIPAddress*. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
8. Cliquez sur **HTTPS** pour le protocole de l’écouteur et assurez-vous que le port est défini sur **443**.
9. Cliquez sur l’icône de dossier et recherchez le certificat *appgwcert.pfx* que vous avez créé précédemment pour le charger.
10. Entrez *mycert1* pour le nom du certificat et *Azure123456!* pour le mot de passe, puis cliquez sur **OK**.

    ![Créer une nouvelle passerelle d’application](./media/application-gateway-ssl-portal/application-gateway-create.png)

11. Passez en revue les paramètres sur la page de résumé, puis cliquez sur **OK** pour créer les ressources réseau et la passerelle d’application. La création de la passerelle d’application peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myVNet** dans la liste des ressources.
2. Cliquez sur **Sous-réseaux**, puis cliquez sur **Sous-réseau**.

    ![Créer un sous-réseau](./media/application-gateway-ssl-portal/application-gateway-subnet.png)

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

    ![Installer l’extension personnalisée](./media/application-gateway-ssl-portal/application-gateway-extension.png)

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
4. Cliquez sur **Pools principaux**. Un pool par défaut a été automatiquement créé avec la passerelle d’application. Cliquez sur **appGateayBackendPool**.
5. Cliquez sur **Ajouter une cible** pour ajouter chaque machine virtuelle que vous avez créée au pool principal.

    ![Ajouter des serveurs principaux](./media/application-gateway-ssl-portal/application-gateway-backend.png)

6. Cliquez sur **Enregistrer**.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Cliquez sur **Toutes les ressources**, puis sur **myAGPublicIPAddress**.

    ![Enregistrer l’adresse IP publique de la passerelle d’application](./media/application-gateway-ssl-portal/application-gateway-ag-address.png)

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. Pour accepter l’avertissement de sécurité si vous avez utilisé un certificat auto-signé, sélectionnez Détails, puis Atteindre la page web :

    ![Avertissement de sécurité](./media/application-gateway-ssl-portal/application-gateway-secure.png)

    Votre site IIS sécurisé apparaît maintenant comme dans l’exemple suivant :

    ![Tester l’URL de base dans la passerelle d’application](./media/application-gateway-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Créer une passerelle d’application avec le certificat
> * Créer les machines virtuelles utilisées comme serveurs principaux

Pour plus d’informations sur les passerelles d’application et leurs ressources associées, consultez les articles de procédures.