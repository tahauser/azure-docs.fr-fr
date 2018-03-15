--titre : Gérer les certificats d’un cluster Azure Service Fabric | Microsoft Docs description : Explique comment ajouter de nouveaux certificats, substituer un certificat ou supprimer un certificat pour un cluster Service Fabric.
services: service-fabric documentationcenter: .net author: ChackDan manager: timlt editor: ''

ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74 ms.service: service-fabric ms.devlang: dotnet ms.topic: article ms.tgt_pltfrm: na ms.workload: na ms.date: 02/23/2018 ms.author: chackdan

---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Ajouter ou supprimer des certificats pour un cluster Service Fabric dans Azure
Nous vous recommandons de vous familiariser avec la façon dont Service Fabric utilise les certificats X.509 et de prendre connaissance des [scénarios de sécurité d’un cluster](service-fabric-cluster-security.md). Vous devez comprendre ce qu’est un certificat de cluster et quelle est son utilité avant de passer à la suite.

Lorsque vous configurez la sécurité par certificat lors de la création du cluster, Service Fabric vous permet de spécifier deux certificats de cluster, un principal et un secondaire, en plus des certificats clients. Pour plus d’informations sur la configuration de ces certificats au moment de la création, consultez [Création d’un cluster avec le portail](service-fabric-cluster-creation-via-portal.md) ou [Création d’un cluster Azure avec Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Si vous spécifiez un seul certificat de cluster au moment de la création, celui-ci est utilisé comme certificat principal. Après la création du cluster, vous pouvez ajouter un certificat en tant que certificat secondaire.

> [!NOTE]
> Pour un cluster sécurisé, vous avez toujours besoin d’au moins un certificat de cluster (principal ou secondaire) valide (non révoqué ni arrivé à expiration) déployé. Si cette condition n’est pas remplie, le cluster cesse de fonctionner. 90 jours avant l’expiration de tous les certificats valides, le système génère un suivi d’avertissement et un événement d’avertissement d’intégrité sur le nœud. Actuellement, Service Fabric n’envoie aucun courrier électronique ou notification d’aucune sorte à ce sujet. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Ajouter un certificat de cluster secondaire à l’aide du portail

Il n’est pas possible d’ajouter un certificat de cluster secondaire via le portail Azure. Pour ce faire, vous devez utiliser Azure PowerShell. La procédure est présentée plus loin dans ce document.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>Échanger les certificats de cluster à l’aide du portail

Une fois que vous avez déployé un certificat de cluster secondaire, si vous souhaitez échanger le certificat principal et le certificat secondaire, accédez au volet Sécurité et sélectionnez l’option Swap with primary (Échanger avec principal) dans le menu contextuel.

![Échange de certificat][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Supprimer un certificat de cluster à l’aide du portail

Pour un cluster sécurisé, vous avez toujours besoin d’au moins un certificat (principal ou secondaire) valide (non révoqué ni arrivé à expiration) déployé. Dans le cas contraire, le cluster cesse de fonctionner.

Pour supprimer un certificat secondaire afin de ne plus l’utiliser pour la sécurité du cluster, accédez au panneau Sécurité et sélectionnez l’option Supprimer dans le menu contextuel du certificat secondaire.

Si vous souhaitez supprimer le certificat principal, vous devez d’abord l’échanger avec le certificat secondaire, puis supprimer le certificat secondaire une fois qu’il a été mis à jour.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Ajouter un certificat secondaire à l’aide de Resource Manager PowerShell
> [MISE À JOUR] Nous disposons désormais d’un moyen amélioré et plus simple pour ajouter un certificat secondaire à l’aide de [Add-AzureRmServiceFabricClusterCertificate](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate?view=azurermps-5.3.0). Vous n’avez pas besoin de suivre les autres étapes de cette section si vous utilisez Add-AzureRmServiceFabricClusterCertificate.


Pour pouvoir exécuter ces étapes, vous devez connaître le fonctionnement de Resource Manager, avoir déployé au moins un cluster Service Fabric à l’aide d’un modèle Resource Manager et disposer du modèle que vous avez utilisé pour configurer le cluster. Il est également supposé que vous maîtrisez l’utilisation de JSON.

> [!NOTE]
> Si vous cherchez un exemple de modèle et de paramètres pour suivre la procédure ou comme point de départ, téléchargez-le dans ce [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Modifier votre modèle Resource Manager

Pour simplifier l’exécution de la procédure, l’exemple 5-VM-1-NodeTypes-Secure_Step2.JSON contient toutes les modifications que nous allons apporter. Cet exemple est disponible dans le [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Suivez bien toutes les étapes**

**Étape 1 :** Ouvrez le modèle Resource Manager que vous avez utilisé pour déployer votre cluster. (Si vous avez téléchargé l’exemple à partir du référentiel ci-dessus, utilisez 5-VM-1-NodeTypes-Secure_Step1.JSON pour déployer un cluster sécurisé, puis ouvrez ce modèle.)

**Étape 2 :** Ajoutez les **deux nouveaux paramètres** "secCertificateThumbprint" et "secCertificateUrlValue", de type "string", dans la section des paramètres de votre modèle. Vous pouvez copier l’extrait de code suivant et l’ajouter au modèle. Selon la source de votre modèle, il se peut que ces paramètres soient déjà définis. Si c’est le cas, passez à l’étape suivante. 
 
```JSON
   "secCertificateThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Thumbprint"
      }
    },
    "secCertificateUrlValue": {
      "type": "string",
      "metadata": {
        "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
      }
    },

```

**Étape 3 :** Apportez des modifications à la ressource **Microsoft.ServiceFabric/clusters** : Recherchez la définition de ressource « Microsoft.ServiceFabric/clusters » dans votre modèle. Sous les propriétés de cette définition, vous trouverez la balise JSON « certificate », qui doit ressembler à l’extrait de code JSON suivant :

   
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 

Ajoutez une nouvelle balise « thumbprintSecondary » et donnez-lui la valeur « [parameters(’secCertificateThumbprint’)] ».  

La définition de ressource doit maintenant ressembler à l’extrait de code ci-dessous (selon la source de votre modèle, il se peut qu’il y ait certaines différences). 

```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 

Si vous souhaitez **substituer le certificat**, spécifiez le nouveau certificat comme certificat principal et transformez le certificat principal actuel en certificat secondaire. Cela entraîne la substitution de votre certificat principal actuel par le nouveau certificat en une seule étape de déploiement.

```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('secCertificateThumbprint')]",
          "thumbprintSecondary": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 


**Étape 4 :** Apportez des modifications à **toutes** les définitions de ressources **Microsoft.Compute/virtualMachineScaleSets** : Recherchez la définition de ressource Microsoft.Compute/virtualMachineScaleSets. Accédez à la section "publisher": "Microsoft.Azure.ServiceFabric", sous "virtualMachineProfile".

Dans les paramètres de l’éditeur Service Fabric, vous devriez trouver des propriétés semblables à ceci :

![Json_Pub_Setting1][Json_Pub_Setting1]

Ajoutez les nouvelles entrées de certificat.

```JSON
               "certificateSecondary": {
                    "thumbprint": "[parameters('secCertificateThumbprint')]",
                    "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },

```

Les propriétés devraient maintenant ressembler à ceci :

![Json_Pub_Setting2][Json_Pub_Setting2]

Si vous souhaitez **substituer le certificat**, spécifiez le nouveau certificat comme certificat principal et transformez le certificat principal actuel en certificat secondaire. Cela entraîne la substitution de votre certificat actuel par le nouveau certificat en une seule étape de déploiement. 


```JSON
               "certificate": {
                   "thumbprint": "[parameters('secCertificateThumbprint')]",
                   "x509StoreName": "[parameters('certificateStoreValue')]"
                     },
               "certificateSecondary": {
                    "thumbprint": "[parameters('certificateThumbprint')]",
                    "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },

```
Les propriétés devraient maintenant ressembler à ceci :

![Json_Pub_Setting3][Json_Pub_Setting3]


**Étape 5 :** Apportez des modifications à **toutes** les définitions de ressources **Microsoft.Compute/virtualMachineScaleSets** : Recherchez la définition de ressource Microsoft.Compute/virtualMachineScaleSets. Accédez à la section "vaultCertificates":, sous "osProfile". Elle devrait ressembler à ceci :


![Json_Pub_Setting4][Json_Pub_Setting4]

Ajoutez le paramètre secCertificateUrlValue. Utilisez l’extrait de code suivant :

```Json
                  {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('secCertificateUrlValue')]"
                  }

```
Le code JSON obtenu devrait maintenant ressembler à ceci :
![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Veillez à répéter les étapes 4 et 5 pour toutes les définitions de ressources Nodetypes/Microsoft.Compute/virtualMachineScaleSets de votre modèle. Si vous en oubliez une, le certificat ne sera pas installé sur omettez un d’eux, le certificat ne sera pas installé sur ce groupe de machines virtuelles identiques (VMSS) et vous obtiendrez des résultats imprévisibles pour votre cluster, ce dernier risquant notamment de s’arrêter s’il ne trouve plus de certificat valide à utiliser pour la sécurité. Vérifiez donc bien que vous n’en avez oublié aucune avant de continuer.
> 
> 


### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Modifier votre fichier de modèle pour refléter les nouveaux paramètres ajoutés précédemment
Si vous utilisez l’exemple du [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) pour suivre la procédure, vous pouvez commencer à apporter des modifications à l’exemple 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON. 

Modifiez le fichier de paramètres de votre modèle Resource Manager en ajoutant les deux nouveaux paramètres pour secCertificateThumbprint et secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Déployer le modèle sur Azure

- Vous êtes maintenant prêt à déployer votre modèle sur Azure. Ouvrez une invite de commande Azure PS version 1 ou ultérieure.
- Connectez-vous à votre compte Azure, puis sélectionnez l’abonnement Azure concerné. C’est une étape importante pour ceux qui ont accès à plusieurs abonnements Azure.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Testez le modèle avant de le déployer. Utilisez le groupe de ressources sur lequel votre cluster est actuellement déployé.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Déployez le modèle sur votre groupe de ressources. Utilisez le groupe de ressources sur lequel votre cluster est actuellement déployé. Exécutez la commande New-AzureRmResourceGroupDeployment. Vous n’avez pas besoin de spécifier le mode, la valeur par défaut étant **incrémentielle**.

> [!NOTE]
> Si vous spécifiez le mode Complet, vous risquez de supprimer par inadvertance des ressources qui ne sont pas dans votre modèle. Par conséquent, ne l’utilisez pas dans ce scénario.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Voici un exemple renseigné de la même commande PowerShell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Une fois le déploiement terminé, connectez-vous à votre cluster avec le nouveau certificat et effectuez des requêtes. Si vous en avez la possibilité. Vous pouvez ensuite supprimer l’ancien certificat. 

Si vous utilisez un certificat auto-signé, n’oubliez pas de les importer dans votre magasin de certificats local TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Pour référence, voici la commande pour vous connecter à un cluster sécurisé 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Pour référence, voici la commande pour connaître l’intégrité du cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Déploiement de certificats d’application dans le cluster

Vous pouvez utiliser la même procédure qu’à l’étape 5 ci-dessus pour déployer les certificats sur les nœuds à partir d’un coffre de clés. Vous devez simplement définir et utiliser des paramètres différents.


## <a name="adding-or-removing-client-certificates"></a>Ajout ou suppression de certificats clients

Outre les certificats de cluster, vous pouvez ajouter des certificats clients pour effectuer des opérations de gestion sur un cluster Service Fabric.

Vous pouvez ajouter deux types de certificats clients : administrateur ou en lecture seule. Ces certificats peuvent ensuite être utilisés pour contrôler l’accès aux opérations d’administration et aux opérations de requête sur le cluster. Par défaut, les certificats de cluster sont ajoutés à la liste de certificats administrateur autorisés.

Vous pouvez spécifier autant de certificats clients que vous souhaitez. Chaque ajout ou suppression entraîne une mise à jour de la configuration du cluster Service Fabric.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Ajout d’un certificat client administrateur ou en lecture seule via le portail

1. Accédez au panneau Sécurité, puis sélectionnez le bouton + Authentification situé en haut de ce panneau.
2. Dans le panneau Ajouter une authentification, choisissez le type d’authentification : Read-only client (Client en lecture seule) ou Admin client (Client administrateur).
3. Choisissez maintenant la méthode d’autorisation. Ce paramètre indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou l’empreinte. En général, il n’est pas recommandé d’utiliser la méthode d’autorisation faisant appel au nom du sujet. 

![Ajout de certificat client][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Suppression d’un certificat client administrateur ou en lecture seule via le portail

Pour supprimer un certificat secondaire afin de ne plus l’utiliser pour la sécurité du cluster, accédez au panneau Sécurité et sélectionnez l’option Supprimer dans le menu contextuel du certificat en question.



## <a name="next-steps"></a>étapes suivantes
Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

* [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
* [Configurer l’accès en fonction du rôle pour les clients](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


