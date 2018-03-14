---
title: "Créer un cluster Service Fabric Linux dans Azure | Microsoft Docs"
description: "Dans ce didacticiel, vous découvrez comment déployer un cluster Service Fabric Linux dans un réseau virtuel Azure existant à l’aide de l’interface de ligne de commande Azure."
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
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: e6cbc8c3c7e1a9ebe9684efb0fa6ffa0717240ea
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Didacticiel : Déployer un cluster Service Fabric Linux dans un réseau virtuel Azure
Ce didacticiel est la première partie d’une série d’étapes. Vous allez apprendre à déployer un cluster Service Fabric Linux dans un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) et un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) à l’aide d’Azure CLI et d’un modèle. Lorsque vous avez terminé, vous disposez d’un cluster en cours d’exécution dans le cloud sur lequel vous pouvez déployer des applications. Pour créer un cluster Windows à l’aide de PowerShell, consultez la section relative à la [création d’un cluster Windows sécurisé sur Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide de l’interface Azure CLI
> * Créer un cluster Service Fabric sécurisé dans Azure à l’aide de l’interface Azure CLI
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter au cluster à l’aide de l’interface de ligne de commande (CLI) de Service Fabric
> * Supprimer un cluster

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster sécurisé sur Azure
> * [Mettre à l’échelle un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Mettre à niveau le runtime d’un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [déployer la Gestion des API avec Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez l’[interface de ligne de commande (CLI) de Service Fabric](service-fabric-cli.md).
- Installez [Azure CLI 2.0](/cli/azure/install-azure-cli).

Les procédures suivantes créent un cluster Service Fabric à cinq nœuds. Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Concepts clés
Un [cluster Service Fabric](service-fabric-deploy-anywhere.md) est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés. Les clusters peuvent être mis à l’échelle pour des milliers de machines. Une machine ou une machine virtuelle appartenant à un cluster est appelée « nœud ». Un nom (chaîne) est affecté à chaque nœud. Les nœuds présentent des caractéristiques, telles que des propriétés de placement.

Un type de nœud définit la taille, le nombre et les propriétés d’un groupe de machines virtuelles du cluster. Chaque type de nœud défini est configuré en tant que [groupe de machines virtuelles identiques](/azure/virtual-machine-scale-sets/). Il s’agit de ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles en tant que groupe. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Les types de nœuds permettent de définir les rôles d’un groupe de nœuds de cluster, par exemple « frontal » ou « back end ».  Votre cluster peut avoir plusieurs types de nœuds, mais le type de nœud principal doit avoir au moins cinq machines virtuelles pour les clusters de production (ou au moins trois machines virtuelles pour les clusters de test).  Les [services système de Service Fabric](service-fabric-technical-overview.md#system-services) sont placés sur les nœuds du type de nœud principal.

Le cluster est sécurisé avec un certificat de cluster. Un certificat de cluster est un certificat X.509 qui permet de sécuriser la communication de nœud à nœud et d’authentifier les points de terminaison de gestion de cluster auprès d’un client de gestion.  Ce certificat de cluster fournit également un certificat SSL pour l’API de gestion HTTPS et pour Service Fabric Explorer par le biais de HTTPS. Les certificats auto-signés sont destinés aux clusters de test.  Pour les clusters de production, utilisez un certificat délivré par une autorité de certification (AC) en tant que certificat de cluster.

Le certificat de cluster doit :

- Contenir une clé privée
- Être créé pour un échange de clés, pouvant faire l’objet d’un export vers un fichier .pfx (Personal Information Exchange)
- Avoir un sujet dont le nom correspond au domaine utilisé pour accéder au cluster Service Fabric. Cette correspondance est nécessaire pour que le certificat SSL soit fourni aux points de terminaison de gestion HTTPS du cluster et à Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine azure.com. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Azure Key Vault permet de gérer des certificats pour des clusters Service Fabric dans Azure.  Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure chargé de la création des clusters Service Fabric extrait les certificats de Key Vault et les installe sur les machines virtuelles du cluster.

Ce didacticiel explique comment déployer un cluster de cinq nœuds dans un type de nœud unique. Pour le déploiement d’un cluster de production, la [planification de la capacité](service-fabric-cluster-capacity.md) est néanmoins une étape importante. Voici quelques éléments à prendre en compte dans le cadre de ce processus.

- Nombre de nœuds et de types de nœuds dont votre cluster a besoin 
- Propriétés de chaque type de nœud (par exemple, taille, principal ou non, accessibilité sur Internet et nombre de machines virtuelles)
- Caractéristiques de fiabilité et de durabilité du cluster

## <a name="download-and-explore-the-template"></a>Télécharger et explorer le modèle
Téléchargez les fichiers de modèle Resource Manager suivants :
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

Le modèle [vnet-linuxcluster.json][template] déploie un certain nombre de ressources, notamment celles ci-dessous.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric
Un cluster Linux est déployé avec les caractéristiques suivantes :
- un type de nœud unique 
- cinq nœuds dans le type de nœud principal (configurable dans les paramètres du modèle)
- système d’exploitation : Ubuntu 16.04 LTS (configurable dans les paramètres du modèle)
- certificat sécurisé (configurable dans les paramètres du modèle)
- [service DNS](service-fabric-dnsservice.md) activé
- [niveau de durabilité](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronze (configurable dans les paramètres du modèle)
- [niveau de fiabilité](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (configurable dans les paramètres du modèle)
- point de terminaison de connexion client : 19000 (configurable dans les paramètres du modèle)
- point de terminaison de passerelle HTTP : 19080 (configurable dans les paramètres du modèle)

### <a name="azure-load-balancer"></a>Équilibrage de charge Azure
Un équilibreur de charge est déployé, et des sondes et règles sont configurées pour les ports suivants :
- point de terminaison de connexion client : 19000
- point de terminaison de passerelle HTTP : 19080 
- port de l’application : 80
- port de l’application : 443

### <a name="virtual-network-subnet-and-network-security-group"></a>Réseau virtuel, sous-réseau et groupe de sécurité réseau
Les noms du réseau virtuel, du sous-réseau et du groupe de sécurité réseau sont déclarés dans les paramètres du modèle.  Les espaces d’adressage du réseau virtuel et du sous-réseau sont également déclarés dans les paramètres du modèle :
- espace d’adressage du réseau virtuel : 10.0.0.0/16
- espace d’adressage de sous-réseau Service Fabric : 10.0.2.0/24

Les règles de trafic entrant suivantes sont activées dans le groupe de sécurité réseau. Vous pouvez modifier les valeurs de port en modifiant les variables de modèle.
- ClientConnectionEndpoint (TCP) : 19000
- HttpGatewayEndpoint (HTTP/TCP) : 19080
- SMB : 445
- Internodecommunication : 1025, 1026, 1027
- Plage de ports éphémères : 49152 à 65534 (256 ports min. nécessaires)
- Ports pour l’utilisation de l’application : 80 et 443
- Plage de ports de l’application : 49152 à 65534 (utilisés pour les communications entre les services ; ne sont pas ouverts sur l’équilibreur de charge)
- Bloquer tous les autres ports

Si d’autres ports de l’application sont nécessaires, vous devez ajuster les ressources Microsoft.Network/loadBalancers et Microsoft.Network/networkSecurityGroups pour autoriser le trafic entrant.

## <a name="set-template-parameters"></a>Définir les paramètres de modèle
Le fichier de paramètres [vnet-cluster.parameters.json][parameters] déclare de nombreuses valeurs utilisées pour déployer le cluster et les ressources associées. Voici certains des paramètres que vous devrez peut-être modifier pour votre déploiement :

|Paramètre|Exemple de valeur|Notes|
|---|---||
|adminUsername|vmadmin| Nom d’utilisateur administrateur pour les machines virtuelles de cluster. |
|adminPassword|Password#1234| Mot de passe d’administrateur pour les machines virtuelles de cluster.|
|clusterName|mysfcluster123| Nom du cluster. |
|location|southcentralus| Emplacement du cluster. |
|certificateThumbprint|| <p>La valeur doit être vide si vous créez un certificat auto-signé ou si vous fournissez un fichier de certificat.</p><p>Pour utiliser un certificat existant déjà chargé dans un coffre de clés, renseignez la valeur d’empreinte du certificat. Par exemple, « 6190390162C988701DB5676EB81083EA608DCCF3 ». </p>| 
|certificateUrlValue|| <p>La valeur doit être vide si vous créez un certificat auto-signé ou si vous fournissez un fichier de certificat.</p><p>Pour utiliser un certificat existant déjà chargé dans un coffre de clés, renseignez l’URL du certificat. Par exemple, « https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346 ».</p>|
|sourceVaultValue||<p>La valeur doit être vide si vous créez un certificat auto-signé ou si vous fournissez un fichier de certificat.</p><p>Pour utiliser un certificat existant déjà chargé dans un coffre de clés, renseignez la valeur de coffre source. Par exemple, « /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT ».</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Déployer le réseau virtuel et le cluster
Puis, configurez la topologie de réseau et déployez le cluster Service Fabric. Le modèle Resource Manager [vnet-linuxcluster.json][template] crée un réseau virtuel (VNET), ainsi qu’un groupe de sécurité réseau (NSG) et un sous-réseau pour Service Fabric. Le modèle déploie également un cluster avec la sécurité de certificat activée.  Pour les clusters de production, utilisez un certificat délivré par une autorité de certification (AC) en tant que certificat de cluster. Un certificat auto-signé peut être utilisé pour garantir la sécurité des clusters de test.

Le script suivant utilise la commande [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) et le modèle pour déployer un nouveau cluster sécurisé à l’aide d’un certificat existant. La commande crée aussi un coffre de clés dans Azure et charge votre certificat.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"  
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Se connecter à un cluster sécurisé
Connectez-vous au cluster à l’aide de votre clé par le biais de la commande `sfctl cluster select` de l’interface de ligne de commande de Service Fabric.  Notez que vous ne pouvez utiliser que l’option **--no-verify** pour un certificat auto-signé.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Vérifiez que vous êtes connecté et que le cluster est sain à l’aide de la commande `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Les autres articles de cette série de didacticiels utilisent le cluster que vous venez de créer. Si vous ne passez pas immédiatement à l’article suivant, vous souhaiterez peut-être supprimer le cluster pour éviter de subir des frais. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources.

Connectez-vous à Azure et sélectionnez l’ID d’abonnement pour lequel vous souhaitez supprimer le cluster.  Vous pouvez trouver votre ID d’abonnement en vous connectant au [portail Azure](http://portal.azure.com). Pour supprimer un groupe de ressources et toutes les ressources de cluster, utilisez la commande [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide de l’interface Azure CLI
> * Créer un cluster Service Fabric sécurisé dans Azure à l’aide de l’interface Azure CLI
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter au cluster à l’aide de l’interface de ligne de commande (CLI) de Service Fabric
> * Supprimer un cluster

Maintenant, passez au didacticiel suivant pour savoir comment mettre à l’échelle votre cluster.
> [!div class="nextstepaction"]
> [Mise à l’échelle d’un cluster](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
