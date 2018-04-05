---
title: Créer un cluster Azure Service Fabric à partir d’un modèle | Microsoft Docs
description: Cet article explique comment configurer un cluster Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager, Azure Key Vault et Azure Active Directory (Azure AD) pour l’authentification des clients.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: b245c9e46c994d40a6d0f75eb8494828d0d4d165
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Créer un cluster Service Fabric à l’aide d’Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portail Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Ce guide vous montre pas à pas comment configurer un cluster Azure Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager. Nous reconnaissons que cet article est un peu long. Cependant, à moins que vous soyez déjà parfaitement familiarisés avec le contenu, veillez à suivre attentivement chaque étape.

Ce guide couvre les procédures suivantes :

* Concepts clés que vous devez connaître avant de déployer un cluster Service Fabric.
* Création d’un cluster dans Azure à l’aide des modules Resource Manager de Service Fabric.
* Configuration d’Azure Active Directory (Azure AD) pour authentifier les utilisateurs exécutant les opérations de gestion sur le cluster.
* Création et déploiement d’un modèle Azure Resource Manager personnalisé pour votre cluster.

## <a name="key-concepts-to-be-aware-of"></a>Concepts clés à connaître
Dans Azure, Service Fabric demande à ce que vous utilisiez un certificat x509 pour sécuriser votre cluster et ses points de terminaison. Les certificats sont utilisés dans Service Fabric à des fins d’authentification et de chiffrement pour sécuriser les divers aspects d’un cluster et de ses applications. Pour l’accès client/l’exécution des opérations de gestion sur le cluster, notamment le déploiement, la mise à niveau et la suppression d’applications, de services et des données qu’ils contiennent, vous pouvez utiliser des certificats ou des informations d’identification Azure Active Directory. L’utilisation d’Azure Active Directory est vivement recommandée, car il s’agit de la seule façon d’empêcher le partage de certificats sur vos clients.  Pour plus d’informations sur l’utilisation de certificats dans Service Fabric, consultez la page [Scénarios de sécurité d’un cluster Service Fabric][service-fabric-cluster-security].

Service Fabric utilise des certificats X.509 pour sécuriser un cluster et fournir des fonctionnalités de sécurité d’applications. [Key Vault][key-vault-get-started] sert à gérer des certificats pour des clusters Service Fabric dans Azure. 


### <a name="cluster-and-server-certificate-required"></a>Certificat de cluster et de serveur (obligatoire)
Ces certificats (un principal et éventuellement un secondaire) sont requis pour sécuriser un cluster et empêcher l’accès non autorisé à celui-ci. Il assure la sécurité du cluster de deux manières :

* **Authentification du cluster :** authentifie la communication nœud à nœud pour la fédération du cluster. Seuls les nœuds qui peuvent prouver leur identité avec ce certificat peuvent être ajoutés au cluster.
* **Authentification du serveur :** authentifie les points de terminaison de gestion du cluster sur un client de gestion, afin que le client de gestion sache qu’il communique avec le véritable cluster et pas avec un « intercepteur ». Ce certificat fournit également un certificat SSL pour l’API de gestion HTTPS et Service Fabric Explorer par le biais de HTTPS.

Pour cela, le certificat doit répondre aux exigences suivantes :

* Le certificat doit contenir une clé privée. Ces certificats ont généralement les extensions .pfx ou .pem  
* Le certificat doit être créé pour l’échange de clés, qui peut faire l’objet d’un export vers un fichier .pfx (Personal Information Exchange).
* Le **nom d’objet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric**. Cela est nécessaire pour la fourniture d’un certificat SSL pour le point de terminaison de gestion HTTPS du cluster et pour Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine *.cloudapp.azure.com. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Configuration d’Azure Active Directory pour l’authentification des clients (facultatif mais recommandé)

Azure AD permet aux organisation (appelées locataires) de gérer l’accès utilisateur aux applications. Ces dernières se composent d’applications avec une interface utilisateur de connexion web et d’applications avec une expérience client natif. Dans cet article, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Obtention d’un client Azure Active Directory][active-directory-howto-tenant].

Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils [Service Fabric Explorer][service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous allez créer deux applications Azure AD pour contrôler l’accès au cluster : une application web et une application native.

Vous trouverez d’autres informations sur la configuration plus loin dans ce document.

### <a name="application-certificates-optional"></a>Certificats d’application (facultatif)
Un nombre quelconque de certificats supplémentaires peut être installé sur un cluster pour sécuriser une application. Avant de créer votre cluster, examinez les scénarios de sécurité d’application qui nécessitent l’installation d’un certificat sur les nœuds, notamment :

* Le chiffrement et déchiffrement de valeurs de configuration d’application.
* Le chiffrement des données sur les nœuds lors de la réplication.

La méthode de création de clusters sécurisés est la même pour les clusters Linux et Windows. 

### <a name="client-authentication-certificates-optional"></a>Certificats d’authentification client (facultatif)
Autant de certificats supplémentaires que vous souhaitez peuvent être spécifiés pour les opérations de client utilisateur ou administrateur. Par défaut, le certificat de cluster a des privilèges de client administrateur. Ces certificats de client supplémentaires ne doivent pas être installés dans le cluster, ils doivent juste être spécifiés comme étant autorisés dans la configuration. Toutefois, ils doivent être installés sur les machines client pour les connecter au cluster et effectuer des opérations de gestion.


## <a name="prerequisites"></a>Prérequis
 
La méthode de création de clusters sécurisés est la même pour les clusters Linux et Windows. Ce guide couvre l’utilisation d’Azure PowerShell ou d’Azure CLI pour créer des clusters. Les conditions requises sont les suivantes : 

-  [Azure PowerShell 4.1 et versions ultérieures][azure-powershell] ou [Azure CLI 2.0 et versions ultérieures][azure-CLI].
-  Vous trouverez des détails sur les modules Service Fabric ici - [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) et [az sf](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest).


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Utiliser le module RM Service Fabric pour déployer un cluster

Dans ce document, nous allons utiliser le module CLI et PowerShell RM Service Fabric pour déployer un cluster ; la commande de module CLI ou PowerShell permet de répondre à plusieurs scénarios. Parcourons chacun d’entre eux. Choisissez le scénario qui vous semble le plus approprié. 

- Créer un cluster à l’aide d’un certificat auto-signé généré par le système
    - Utiliser un modèle de cluster par défaut
    - Utiliser un modèle que vous possédez déjà
- Créer un cluster à l’aide d’un certificat que vous possédez déjà
    - Utiliser un modèle de cluster par défaut
    - Utiliser un modèle que vous possédez déjà

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Créer un cluster à l’aide d’un certificat auto-signé généré par le système

Utilisez la commande suivante pour créer le cluster, si vous souhaitez que le système génère un certificat auto-signé et l’utilise pour sécuriser votre cluster. Cette commande configure un certificat de cluster principal qui est utilisé pour la sécurité du cluster et pour configurer l’accès administrateur et effectuer des opérations de gestion à l’aide de ce certificat.

### <a name="login-in-to-azure"></a>Connectez-vous à Azure.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Utiliser le modèle de type de nœud Node 1 5 par défaut qui est fourni dans le module pour configurer le cluster

Utiliser la commande suivante pour créer un cluster rapidement, en spécifiant des paramètres minimum

Le modèle utilisé est disponible dans les [exemples de modèles Azure Service Fabric : modèle Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) et [modèle Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Les commandes ci-dessous permettent de créer des clusters Windows et Linux ; vous avez simplement à spécifier le système d’exploitation souhaité. Les commandes PowerShell/ CLI génèrent également le certificat dans CertificateOutputFolder spécifié. Vérifiez toutefois que le dossier Certificat est déjà créé. La commande accepte d’autres paramètres comme Référence de la machine virtuelle.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Utiliser le modèle personnalisé que vous possédez déjà 

Si vous avez besoin de créer un modèle personnalisé adapté à vos besoins, il est vivement recommandé de commencer par l’un des modèles disponibles dans les [exemples de modèles Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Suivez les instructions et explications ci-dessous pour [personnaliser votre modèle de cluster][customize-your-cluster-template].

Si vous disposez déjà d’un modèle personnalisé, vérifiez plusieurs fois que les trois paramètres liés au certificat dans le modèle et le fichier de paramètres portent les noms indiqués ci-après et que les valeurs sont null comme suit.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Voici la commande CLI équivalente pour faire de même. Modifiez les valeurs dans les instructions declare et remplacez-les par les valeurs appropriées. CLI prend en charge tous les autres paramètres que la commande PowerShell ci-dessus prend en charge.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Créez un cluster à l’aide du certificat acheté auprès d’une autorité de certification ou d’un certificat que vous avez déjà.

Utilisez la commande suivante pour créer un cluster, si vous disposez d’un certificat que vous souhaitez utiliser pour sécuriser votre cluster.

S’il s’agit d’un certificat signé d’une autorité de certification que vous allez également utiliser à d’autres fins, il est recommandé de fournir un groupe de ressources distinct pour votre coffre de clés. Nous vous recommandons de placer ce coffre de clés dans son propre groupe de ressources. Vous pouvez ainsi supprimer les groupes de ressources de calcul et de stockage, y compris le groupe de ressources contenant votre cluster Service Fabric, sans risquer de perdre vos clés et secrets. **Le groupe de ressources qui contient votre coffre de clés _doit se trouver dans la même région_ que le cluster qui l’utilise.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Utiliser le modèle de type de nœud Node 1 5 par défaut qui est fourni dans le module
Le modèle utilisé est disponible dans les [exemples Azure : modèle Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) et [modèle Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>Utiliser le modèle personnalisé que vous possédez 
Si vous avez besoin de créer un modèle personnalisé adapté à vos besoins, il est vivement recommandé de commencer par l’un des modèles disponibles dans les [exemples de modèles Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Suivez les instructions et explications ci-dessous pour [personnaliser votre modèle de cluster][customize-your-cluster-template].

Si vous disposez déjà d’un modèle personnalisé, vérifiez plusieurs fois que les trois paramètres liés au certificat dans le modèle et le fichier de paramètres portent les noms indiqués ci-après, et que les valeurs sont null comme mentionné ci-dessous.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword

```

Voici la commande CLI équivalente pour faire de même. Modifiez les valeurs dans les instructions declare et remplacez-les par les valeurs appropriées.

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Utiliser un pointeur vers le secret que vous avez déjà chargé dans le coffre de clés

Pour utiliser un Key Vault existant, vous _devez l’activer pour le déploiement_ afin d’autoriser le fournisseur de ressources de calcul à obtenir des certificats à partir de ce Key Vault et à les installer sur les nœuds de cluster :

```PowerShell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```
Voici la commande CLI équivalente pour faire de même. Modifiez les valeurs dans les instructions declare et remplacez-les par les valeurs appropriées.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configuration d’Azure Active Directory pour l’authentification des clients

Azure AD permet aux organisation (appelées locataires) de gérer l’accès utilisateur aux applications. Ces dernières se composent d’applications avec une interface utilisateur de connexion web et d’applications avec une expérience client natif. Dans cet article, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Obtention d’un client Azure Active Directory][active-directory-howto-tenant].

Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils [Service Fabric Explorer][service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous allez créer deux applications Azure AD pour contrôler l’accès au cluster : une application web et une application native.

Pour simplifier certaines des étapes impliquées dans la configuration d’Azure AD avec un cluster Service Fabric, nous avons créé un ensemble de scripts Windows PowerShell.

> [!NOTE]
> Vous devez exécuter les étapes suivantes avant de créer le cluster. Étant donné que les scripts attendent des noms de cluster et des points de terminaison, ces valeurs doivent être des valeurs planifiées et non celles que vous avez déjà créées.

1. [Téléchargez les scripts][sf-aad-ps-script-download] sur votre ordinateur.
2. Cliquez avec le bouton sur le fichier zip, sélectionnez **Propriétés**, activez la case à cocher **Débloquer**, puis cliquez sur **Appliquer**.
3. Extrayez le fichier zip.
4. Exécutez `SetupApplications.ps1` et indiquez les valeurs de TenantId, ClusterName et WebApplicationReplyUrl en tant que paramètres. Par exemple : 

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

Vous pouvez trouver votre TenantId en exécutant la commande PowerShell `Get-AzureSubscription`. L’exécution de cette commande permet d’afficher le TenantId de chaque abonnement.

Le paramètre ClusterName est utilisé pour préfixer les applications Azure AD créées par le script. Il ne doit pas forcément correspondre précisément au nom du cluster. Il sert uniquement à simplifier le mappage des artefacts Azure AD au cluster Service Fabric avec lequel ils sont utilisés.

Le paramètre WebApplicationReplyUrl est le point de terminaison par défaut renvoyé par Azure AD aux utilisateurs une fois qu’ils se sont connectés. Définissez ce point de terminaison en tant que point de terminaison Service Fabric Explorer pour votre cluster, qui est par défaut :

https://&lt;cluster_domain&gt;:19080/Explorer

Vous êtes invité à vous connecter à un compte qui dispose de privilèges d’administration pour le locataire Azure AD. Une fois que vous vous êtes connecté, le script crée les applications web et native pour représenter votre cluster Service Fabric. Si vous examinez les applications du client dans le [Portail Azure][azure-portal], vous devez voir deux nouvelles entrées :

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Comme le script imprime le JSON exigé par le modèle Azure Resource Manager lorsque vous créez le cluster (dans la section suivante), il est judicieux de garder la fenêtre PowerShell ouverte.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Création d’un modèle Service Fabric Cluster Resource Manager
Cette section est destinée aux utilisateurs qui souhaitent personnaliser un modèle Resource Manager de cluster Service Fabric. Une fois que vous avez un modèle, vous pouvez toujours revenir en arrière et utiliser les modules CLI ou PowerShell pour le déployer. 

Des exemples de modèles Resource Manager sont disponibles dans les [exemples Azure sur GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Ces modèles peuvent être utilisés comme point de départ pour votre modèle de cluster.

### <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager
Ce guide utilise l’exemple de modèle [cluster sécurisé à 5 nœuds][service-fabric-secure-cluster-5-node-1-nodetype] et ses paramètres du modèle. Téléchargez `azuredeploy.json` et `azuredeploy.parameters.json` sur votre ordinateur et ouvrez les deux fichiers dans votre éditeur de texte préféré.

### <a name="add-certificates"></a>Ajout de certificats
Pour ajouter les certificats à un modèle Resource Manager de cluster, vous devez référencer le Key Vault qui contient les clés de certificat. Ajoutez ces paramètres de coffre de clés et les valeurs dans un fichier de paramètres de modèle Resource Manager (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Ajouter tous les certificats à l’osProfile du groupe de machines virtuelles identiques
Chaque certificat qui est installé dans le cluster doit être configuré dans la section osProfile de la ressource des groupes identiques (Microsoft.Compute/virtualMachineScaleSets). Cela permet d’indiquer au fournisseur de ressources d’installer le certificat sur les machines virtuelles. Cette installation inclut le certificat de cluster et tous les certificats de sécurité d’application que vous projetez d’utiliser pour vos applications :

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurer le certificat de cluster Service Fabric
Le certificat d’authentification de cluster doit être configuré à la fois dans la ressource de cluster Service Fabric (Microsoft.ServiceFabric/clusters) et dans l’extension de Service Fabric pour les groupes de machines virtuelles identiques dans la ressource associée à ces derniers. Cette disposition permet au fournisseur de ressources de Service Fabric de configurer le certificat pour l’authentification du cluster et l’authentification du serveur pour les points de terminaison de gestion.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Ajoutez les informations de certificat à la ressource de groupe de machines virtuelles identiques :
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Ajoutez les informations de certificat à la ressource de cluster Service Fabric :
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Ajouter la configuration Azure AD afin d’utiliser Azure AD pour l’accès client

Vous ajoutez les configurations Azure AD à un modèle Resource Manager de cluster en référençant le coffre de clés qui contient les clés de certificat. Ajoutez ces paramètres et valeurs Azure AD dans un fichier de paramètres de modèle Resource Manager (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>Renseignez le fichier de paramètres avec les valeurs.
Enfin, utilisez les valeurs de sortie des commandes PowerShell Azure AD et du Key Vault pour renseigner le fichier de paramètres :

Si vous envisagez d’utiliser les modules PowerShell RM Azure Service Fabric, vous n’avez pas besoin de renseigner les informations de certificat de cluster ; si vous souhaitez que le système génère le certificat auto-signé pour la sécurité du cluster, vous avez simplement besoin de laisser les valeurs définies sur null. 

> [!NOTE]
> Pour que les modules RM récupèrent et renseignent des valeurs de paramètres vides, les noms des paramètres doivent correspondre à ceux indiqués ci-dessous.
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Si vous utilisez des certificats d’application ou un cluster existant que vous avez chargé dans le coffre de clés, vous devez récupérer ces informations et les renseigner 

Les modules RM n’ont pas la capacité de générer la configuration Azure AD pour vous. Par conséquent, si vous envisagez d’utiliser Azure AD pour l’accès client, vous devez renseigner la configuration.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>Tester votre modèle  
Pour tester votre modèle Resource Manager avec un fichier de paramètres, utilisez la commande PowerShell suivante :

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Si vous rencontrez des problèmes et obtenez des messages difficiles à déchiffrer, utilisez « -Debug » comme option.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Le diagramme suivant montre comment votre Key Vault et la configuration Azure AD s’appliquent à votre modèle Resource Manager.

![Mappage de dépendances de Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Créer le cluster à l’aide du modèle de ressources Azure 

Vous pouvez désormais déployer votre cluster à l’aide de la procédure décrite plus haut dans le document, ou si vous avez les valeurs dans le fichier de paramètres, renseigné, vous êtes maintenant prêt à créer le cluster à l’aide du [déploiement de modèle de ressource Azure][resource-group-template-deploy] directement.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Si vous rencontrez des problèmes et obtenez des messages difficiles à déchiffrer, utilisez « -Debug » comme option.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Affecter des utilisateurs aux rôles
Une fois que vous avez créé les applications pour représenter votre cluster, affectez les utilisateurs aux rôles pris en charge par Service Fabric : lecture seule et administrateur. Vous pouvez affecter les rôles à l’aide du [portail Azure][azure-portal].

1. Dans le portail Azure, sélectionnez votre locataire dans l’angle supérieur droit.

    ![Bouton Sélectionner le locataire][select-tenant-button]
2. Sélectionnez **Azure Active Directory** sous l’onglet gauche, puis « Applications d’entreprise ».
3. Sélectionnez « Toutes les applications », puis recherchez et sélectionnez l’application web qui porte un nom semblable à celui-ci : `myTestCluster_Cluster`.
4. Cliquez sur l’onglet **Utilisateurs et groupes**.

    ![Onglet Utilisateurs et groupes][users-and-groups-tab]
5. Cliquez sur le bouton **Ajouter un utilisateur** dans la nouvelle page, sélectionnez un utilisateur et le rôle à affecter, puis cliquez sur le bouton **Sélectionner** situé en bas de la page.

    ![Page Affecter des utilisateurs aux rôles][assign-users-to-roles-page]
6. Cliquez sur le bouton **Affecter** en bas de la page.

    ![Confirmation de l’ajout de l’affectation][assign-users-to-roles-confirm]

> [!NOTE]
> Pour plus d’informations sur les rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Aide pour la résolution des problèmes de configuration d’Azure Active Directory
La configuration et l’utilisation d’Azure AD peuvent être difficiles, voici donc quelques conseils sur ce que vous pouvez faire pour résoudre le problème.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vous demande de sélectionner un certificat
#### <a name="problem"></a>Problème
Une fois que vous vous êtes connecté à Azure AD dans Service Fabric Explorer, le navigateur revient à la page d’accueil, mais un message vous invite à sélectionner un certificat.

![Boîte de dialogue Certificat SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motif
Aucun rôle n’a été affecté à l’utilisateur dans l’application Azure AD en cluster. Par conséquent, l’authentification Azure AD échoue sur le cluster Service Fabric. Service Fabric Explorer revient à l’authentification par certificat.

#### <a name="solution"></a>Solution
Suivez les instructions de configuration d’Azure AD et affectez des rôles utilisateur. Nous vous recommandons également d’activer l’option Affectation de l’utilisateur requise pour accéder à l’application, comme le fait `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La connexion avec PowerShell échoue avec un message d’erreur : « Les informations d’identification spécifiées ne sont pas valides »
#### <a name="problem"></a>Problème
Lorsque vous utilisez PowerShell pour vous connecter au cluster à l’aide du mode de sécurité « AzureActiveDirectory », une fois que vous vous êtes connecté à Azure AD, la connexion échoue avec un message d’erreur : « Les informations d’identification spécifiées ne sont pas valides. »

#### <a name="solution"></a>Solution
La solution est la même que pour le problème précédent.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Lorsque vous vous connectez, Service Fabric Explorer renvoie un message d’échec : « AADSTS50011 »
#### <a name="problem"></a>Problème
Lorsque vous essayez de vous connecter à Azure AD dans Service Fabric Explorer, la page renvoie un message d’échec : « AADSTS50011 : l’adresse de réponse &lt;url&gt; ne correspond pas aux adresses de réponse configurées pour l’application : &lt;guid&gt;. »

![L’adresse de réponse SFX ne correspond pas][sfx-reply-address-not-match]

#### <a name="reason"></a>Motif
L’application en cluster (web) représentant Service Fabric Explorer tente de s’authentifier auprès d’Azure AD. Dans le cadre de cette demande, elle fournit l’URL de redirection. Cette dernière ne figure cependant pas dans la liste **URL DE RÉPONSE** de l’application Azure AD.

#### <a name="solution"></a>Solution
Sélectionnez « Inscriptions des applications » dans la page AAD, sélectionnez votre application en cluster, puis appuyez sur le bouton **URL de réponse**. Dans la page « URL de réponse », ajoutez l’URL de Service Fabric Explorer à la liste ou remplacez l’un des éléments dans la liste. Lorsque vous avez terminé, enregistrez vos modifications.

![URL de réponse d’application web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Connecter le cluster à l’aide de l’authentification Azure AD via PowerShell
Pour connecter le cluster Service Fabric, utilisez l’exemple de commande PowerShell suivant :

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Pour en savoir plus sur l’applet de commande Connect-ServiceFabricCluster, consultez [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Puis-je utiliser le même locataire Azure AD pour plusieurs clusters ?
Oui. Cependant, n’oubliez pas d’ajouter l’URL de Service Fabric Explorer à votre application en cluster (web). Si vous ne le faites pas, Service Fabric Explorer ne fonctionnera pas.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Pourquoi dois-je disposer d’un certificat de serveur lorsqu’Azure AD est activé ?
FabricClient et FabricGateway effectuent une authentification mutuelle. Pendant l’authentification Azure AD, l’intégration Azure AD fournit une identité de client au serveur et le certificat de serveur est utilisé pour vérifier l’identité du serveur. Pour plus d’informations sur les certificats Service Fabric, consultez [Certificats X.509 et Service Fabric][x509-certificates-and-service-fabric]

## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé avec l’authentification de gestion fournie par Azure Active Directory. Ensuite, [connectez-vous à votre cluster](service-fabric-connect-to-secure-cluster.md) et découvrez comment [gérer les secrets d’application](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

