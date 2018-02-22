---
title: "Créer une passerelle d’application hébergeant plusieurs sites - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une passerelle d’application qui héberge plusieurs sites à l’aide du portail Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: 403c6c254d8547b09e42f0b1561e5eff350a1f9b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Créer une passerelle d’application hébergeant plusieurs sites à l’aide du portail Azure

Vous pouvez utiliser le portail Azure pour configurer [l’hébergement de plusieurs sites web](application-gateway-multi-site-overview.md) quand vous créez une [passerelle d’application](application-gateway-introduction.md). Dans ce didacticiel, vous créez des pools backend à l’aide de groupes de machines virtuelles identiques. Vous configurez ensuite des écouteurs et des règles en fonction des domaines qui vous appartiennent pour vérifier que le trafic web arrive sur les serveurs appropriés dans les pools. Ce didacticiel, qui part du principe que vous avez plusieurs domaines, utilise *www.contoso.com* et *www.fabrikam.com* en guise d’exemples.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une passerelle Application Gateway
> * Créer des machines virtuelles pour les serveurs principaux
> * Créer des pools principaux avec les serveurs principaux
> * Créer des écouteurs et des règles de routage
> * Créer un enregistrement CNAME dans votre domaine

![Exemple de routage multisite](./media/application-gateway-create-multisite-portal/scenario.png)

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

    ![Créer une nouvelle passerelle d’application](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
5. Cliquez sur **Choisir un réseau virtuel**, cliquez sur **Créer nouveau**, puis entrez ces valeurs pour le réseau virtuel :

    - *myVNet* : pour le nom du réseau virtuel.
    - *10.0.0.0/16* : pour l’espace d’adressage du réseau virtuel.
    - *myAGSubnet* : pour le nom du sous-réseau.
    - *10.0.0.0/24* : pour l’espace d’adressage du sous-réseau.

    ![Création d’un réseau virtuel](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Cliquez sur **OK** pour créer le réseau virtuel et le sous-réseau.
7. Cliquez sur **Choisir une adresse IP publique**, cliquez sur **Créer nouveau**, puis entrez le nom de l’adresse IP publique. Dans cet exemple, l’adresse IP publique est nommée *myAGPublicIPAddress*. Acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
8. Acceptez les valeurs par défaut pour la configuration de l’écouteur, laissez le pare-feu d’applications web désactivé, puis cliquez sur **OK**.
9. Passez en revue les paramètres sur la page de résumé, puis cliquez sur **OK** pour créer les ressources réseau et la passerelle d’application. La création de la passerelle d’application peut prendre plusieurs minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante.

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myVNet** dans la liste des ressources.
2. Cliquez sur **Sous-réseaux**, puis cliquez sur **Sous-réseau**.

    ![Créer un sous-réseau](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Entrez *myBackendSubnet* pour le nom du sous-réseau, puis cliquez sur **OK**.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cet exemple, vous créez deux machines virtuelles à utiliser en tant que serveurs principaux pour la passerelle d’application. Vous installez également IIS sur les machines virtuelles pour vérifier que le trafic est correctement acheminé.

1. Cliquez sur **Nouveau**.
2. Cliquez sur **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste de suggestions.
3. Entrez ces valeurs pour la machine virtuelle :

    - *contosoVM* : pour le nom de la machine virtuelle.
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

    ![Installer l’extension personnalisée](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Exécutez la commande suivante pour installer IIS sur la machine virtuelle : 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Créez la deuxième machine virtuelle et installez IIS en suivant la procédure que vous venez de terminer. Entrez *fabrikamVM* comme nom et valeur de VMName dans Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Créer des pools backend avec les machines virtuelles

1. Cliquez sur **Toutes les ressources**, puis sur **myAppGateway**.
2. Cliquez sur **Pools principaux**, puis sur **Ajouter**.
3. Entrez le nom *contosoPool* et ajoutez *contosoVM* avec **Ajouter une cible**.

    ![Ajouter des serveurs principaux](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Cliquez sur **OK**.
5. Cliquez sur **Pools principaux**, puis sur **Ajouter**.
6. Créez le pool *fabrikamPool* avec la machine virtuelle *fabrikamVM* en suivant la procédure que vous venez de terminer.

## <a name="create-listeners-and-routing-rules"></a>Créer des écouteurs et des règles de routage

1. Cliquez sur  **Écouteurs** puis sur **Multisite**.
2. Entrez ces valeurs pour l’écouteur :
    
    - *contosoListener* : pour le nom de l’écouteur.
    - *www.contoso.com* : remplacez cet exemple de nom d’hôte par votre nom de domaine.

3. Cliquez sur **OK**.
4. Créez un deuxième écouteur en utilisant le nom *fabrikamListener* et utilisez le nom de votre deuxième domaine. Dans cet exemple, *www.fabrikam.com* est utilisé.

Les règles sont traitées dans l’ordre dans lequel elles sont répertoriées, et le trafic est dirigé selon la première règle correspondante, quelle que soit sa spécificité. Par exemple, si une règle utilise un écouteur de base et qu’une autre utilise un écouteur multisite sur le même port, la règle avec l’écouteur multisite doit être répertoriée avant la règle avec l’écouteur de base pour que la règle multisite fonctionne comme prévu. 

Dans cet exemple, vous créez deux règles et supprimez la règle par défaut qui a été créée au moment de la création de la passerelle d’application. 

1. Cliquez sur **Règles**, puis sur **De base**.
2. Entrez *contosoRule* comme nom.
3. Sélectionnez *contosoListener* comme écouteur.
4. Sélectionnez *contosoPool* comme pool backend.

    ![Créer une règle basée sur le chemin](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Cliquez sur **OK**.
6. Créez une deuxième règle en utilisant les noms *fabrikamRule*, *fabrikamListener* et *fabrikamPool*.
7. Supprimez la règle par défaut nommée *rule1*. Pour cela, cliquez dessus, puis cliquez sur **Supprimer**.

## <a name="create-a-cname-record-in-your-domain"></a>Créer un enregistrement CNAME dans votre domaine

Une fois la passerelle d’application créée avec son adresse IP publique, vous pouvez obtenir l’adresse DNS et l’utiliser pour créer un enregistrement CNAME dans votre domaine. L’utilisation d’enregistrements A n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer au moment du redémarrage de la passerelle d’application.

1. Cliquez sur **Toutes les ressources**, puis sur **myAGPublicIPAddress**.

    ![Enregistrer l’adresse DNS de la passerelle d’application](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Copiez l’adresse DNS et utilisez-la comme valeur pour un nouvel enregistrement CNAME dans votre domaine.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Entrez votre nom de domaine dans la barre d’adresse de votre navigateur. Par exemple : http://www.contoso.com.

    ![Tester le site contoso dans la passerelle d’application](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Remplacez l’adresse par celle de votre autre domaine. Voici ce qui doit apparaître :

    ![Tester le site fabrikam dans la passerelle d’application](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une passerelle Application Gateway
> * Créer des machines virtuelles pour les serveurs principaux
> * Créer des pools principaux avec les serveurs principaux
> * Créer des écouteurs et des règles de routage
> * Créer un enregistrement CNAME dans votre domaine

> [!div class="nextstepaction"]
> [En savoir plus sur ce que la passerelle d’application vous permet de faire](application-gateway-introduction.md)