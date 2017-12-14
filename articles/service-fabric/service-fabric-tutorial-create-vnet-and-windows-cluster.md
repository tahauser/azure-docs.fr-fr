---
title: "Créer un cluster Windows Service Fabric dans Azure | Microsoft Docs"
description: "Découvrez comment déployer un cluster Windows Service Fabric dans un réseau virtuel Azure existant à l’aide de PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f043ad753560ecac83324a259341a69ffce01fa5
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Déployer un cluster Windows Service Fabric dans un réseau virtuel Azure
Ce didacticiel est la première partie d’une série d’étapes. Vous allez apprendre à déployer un cluster Service Fabric exécutant Windows dans un réseau et sous-réseau virtuel Azure existants à l’aide de PowerShell. Lorsque vous avez terminé, vous disposez d’un cluster en cours d’exécution dans le cloud sur lequel vous pouvez déployer des applications.  Pour créer un cluster Linux à l’aide de l’interface de ligne de commande Azure, consultez la page [Créer un cluster Linux sécurisé sur Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide de PowerShell
> * Créer un coffre de clés et charger un certificat
> * Créer un cluster Service Fabric sécurisé dans Azure PowerShell
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster sécurisé sur Azure
> * [Mettre à l’échelle un cluster](/service-fabric-tutorial-scale-cluster.md)
> * [Mettre à niveau le runtime d’un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [déployer la Gestion des API avec Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [Kit de développement logiciel (SDK) Service Fabric et le module PowerShell](service-fabric-get-started.md).
- Installez le [module Azure PowerShell, version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Les procédures suivantes créent un cluster Service Fabric à cinq nœuds. Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="introduction"></a>Introduction
Ce didacticiel explique comment déployer un cluster de cinq nœuds dans un type de nœud unique dans un réseau virtuel Azure.

Un [cluster Service Fabric](service-fabric-deploy-anywhere.md) est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés. Les clusters peuvent être mis à l’échelle pour des milliers de machines. Une machine ou une machine virtuelle appartenant à un cluster est appelée « nœud ». Un nom (chaîne) est affecté à chaque nœud. Les nœuds présentent des caractéristiques, telles que des propriétés de placement.

Un type de nœud définit la taille, le nombre et les propriétés d’un groupe de machines virtuelles du cluster. Chaque type de nœud défini est configuré en tant que [groupe de machines virtuelles identiques](/azure/virtual-machine-scale-sets/). Il s’agit de ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles en tant que groupe. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Les types de nœuds permettent de définir les rôles d’un groupe de nœuds de cluster, par exemple « frontal » ou « back end ».  Votre cluster peut avoir plusieurs types de nœuds, mais le type de nœud principal doit avoir au moins cinq machines virtuelles pour les clusters de production (ou au moins trois machines virtuelles pour les clusters de test).  Les [services système de Service Fabric](service-fabric-technical-overview.md#system-services) sont placés sur les nœuds du type de nœud principal.

## <a name="cluster-capacity-planning"></a>Planification de la capacité des clusters
Ce didacticiel explique comment déployer un cluster de cinq nœuds dans un type de nœud unique.  Pour le déploiement d’un cluster de production, la planification de la capacité est une étape importante. Voici quelques éléments à prendre en compte dans le cadre de ce processus.

- Nombre de types de nœuds dont votre cluster a besoin 
- Propriétés de chaque type de nœud (par exemple, taille, principal ou non, accessibilité sur Internet et nombre de machines virtuelles)
- Caractéristiques de fiabilité et de durabilité du cluster

Pour plus d’informations, consultez [Considérations en matière de planification de la capacité du cluster](service-fabric-cluster-capacity.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Se connecter à Azure et sélectionner un abonnement
Ce guide utilise Azure PowerShell. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant d’exécuter des commandes Azure.
 
Exécutez le script suivant pour vous connecter à votre compte Azure et sélectionner votre abonnement :

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources pour votre déploiement, puis donnez-lui un nom et un emplacement.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Déploiement de la topologie du réseau
Maintenant, configurez la topologie du réseau sur laquelle il est prévu de déployer la Gestion des API et le cluster Service Fabric. Le modèle Resource Manager [network.json][network-arm] est configuré pour créer un réseau virtuel (VNET) ainsi qu’un Groupe de sécurité réseau (NSG) et un sous-réseau pour Service Fabric et un NSG et un sous-réseau pour la Gestion des API. La Gestion des API est déployée ultérieurement dans ce tutoriel. Pour en savoir plus sur les réseaux virtuels, les sous-réseaux et les Groupes de sécurité réseau, cliquez [ici](../virtual-network/virtual-networks-overview.md).

Le fichier de paramètres [network.parameters.json][network-parameters-arm] contient les noms des sous-réseaux et des groupes de sécurité réseau sur lesquels Service Fabric et la Gestion des API sont déployés.  La Gestion des API est déployée dans le [didacticiel suivant](service-fabric-tutorial-deploy-api-management.md). Pour ce guide, il est inutile de changer les valeurs des paramètres. Les modèles Resource Manager pour Service Fabric utilisent ces valeurs.  Si vous les modifiez ici, vous devez en faire de même dans les autres modèles Resource Manager utilisés dans ce didacticiel et le [didacticiel Déployer la Gestion des API](service-fabric-tutorial-deploy-api-management.md). 

Téléchargez le modèle Resource Manager et le fichier de paramètres suivants :
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Utilisez la commande PowerShell suivante pour déployer le modèle Resource Manager et les fichiers de paramètres nécessaires à la configuration du réseau :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Déploiement du cluster Service Fabric
Une fois le déploiement des ressources réseau terminé, la prochaine étape consiste à déployer un cluster Service Fabric sur le réseau virtuel dans le sous-réseau et le groupe de sécurité réseau désignés pour le cluster Service Fabric. Le déploiement d’un cluster sur un réseau virtuel existant et le sous-réseau associé (déployés précédemment dans cet article) nécessite un modèle Resource Manager.  Dans cette série de didacticiels, le modèle est préconfiguré de façon à utiliser les noms du réseau virtuel, sous-réseau et groupe de sécurité réseau que vous avez configurés au cours d’une étape précédente.  

Téléchargez le modèle Resource Manager et le fichier de paramètres suivants :
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Utilisez ce modèle pour créer un cluster sécurisé.  Un certificat de cluster est un certificat X.509 qui permet de sécuriser la communication de nœud à nœud et d’authentifier les points de terminaison de gestion de cluster auprès d’un client de gestion.  Ce certificat de cluster fournit également un certificat SSL pour l’API de gestion HTTPS et pour Service Fabric Explorer par le biais de HTTPS. Azure Key Vault permet de gérer des certificats pour des clusters Service Fabric dans Azure.  Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure chargé de la création des clusters Service Fabric extrait les certificats de Key Vault et les installe sur les machines virtuelles du cluster. 

Pour le certificat de cluster, vous pouvez choisir un certificat provenant d’une autorité de certification, ou créer un certificat auto-signé à des fins de test. Le certificat de cluster doit :

- Contenir une clé privée
- Être créé pour un échange de clés, pouvant faire l’objet d’un export vers un fichier .pfx (Personal Information Exchange)
- Avoir un sujet dont le nom correspond au domaine utilisé pour accéder au cluster Service Fabric. Cette correspondance est nécessaire pour que le certificat SSL soit fourni aux points de terminaison de gestion HTTPS du cluster et à Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine azure.com. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Renseignez ces paramètres dans le fichier *cluster.parameters.json* de votre déploiement :

|Paramètre|Valeur|
|---|---|
|adminPassword|Password#1234|
|adminUsername|vmadmin|
|clusterName|mysfcluster|
|location|southcentralus|

Ne renseignez pas les paramètres *certificateThumbprint*, *certificateUrlValue* et *sourceVaultValue* si vous souhaitez créer un certificat auto-signé.  Si vous avez déjà chargé un certificat dans un coffre de clés et souhaitez l’utiliser, renseignez ces valeurs de paramètres.

Le script suivant utilise l’applet de commande et le modèle [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) pour déployer un nouveau cluster dans Azure. L’applet de commande crée aussi un coffre de clés dans Azure, ajoute un nouveau certificat auto-signé dans le coffre de clés, puis télécharge le fichier de certificat localement. Vous pouvez spécifier un certificat et/ou un coffre de clés existants à l’aide des autres paramètres de l’applet de commande [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster).

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
```

## <a name="connect-to-the-secure-cluster"></a>Se connecter à un cluster sécurisé
Connectez-vous au cluster à l’aide du module Service Fabric PowerShell installé avec le Kit de développement logiciel (SDK) Service Fabric.  Tout d’abord, installez le certificat dans le magasin personnel de l’utilisateur actuel sur votre ordinateur.  Exécutez la commande PowerShell suivante :

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Vous êtes maintenant prêt à vous connecter à votre cluster sécurisé.

Le module **Service Fabric** PowerShell fournit de nombreuses cmdlets pour la gestion des services, applications et clusters Service Fabric.  Pour vous connecter au cluster sécurisé, utilisez la cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Les détails du point de terminaison de connexion et de l’empreinte de certificat se trouvent dans la sortie de l’étape précédente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Vérifiez que vous êtes connecté et que le cluster est sain à l’aide de la cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Les autres articles de cette série de didacticiels utilisent le cluster que vous venez de créer. Si vous ne passez pas immédiatement à l’article suivant, vous pouvez supprimer le cluster et le coffre de clés pour éviter de subir des frais. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources.

Pour supprimer un groupe de ressources et toutes les ressources de cluster, utilisez la cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Supprimez également le groupe de ressources contenant le coffre de clés.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide de PowerShell
> * Créer un coffre de clés et charger un certificat
> * Création d’un cluster Service Fabric sécurisé dans Azure à l’aide de PowerShell
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Maintenant, passez au didacticiel suivant pour savoir comment mettre à l’échelle votre cluster.
> [!div class="nextstepaction"]
> [Mise à l’échelle d’un cluster](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
