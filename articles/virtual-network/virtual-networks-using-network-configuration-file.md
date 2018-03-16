---
title: "Configurer un réseau virtuel Azure (Classic) - Fichier config réseau | Microsoft Docs"
description: "Découvrez comment créer et modifier des réseaux virtuels (Classic) en exportant, modifiant et important un fichier config réseau."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 1bb75b8bd1d525f3104fb517cb4a09b2e33caaca
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurer un réseau virtuel (Classic) à l’aide d’un fichier config réseau
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle de déploiement Resource Manager.

Vous pouvez créer et configurer un réseau virtuel (Classic) avec un fichier config réseau à l’aide de l’interface de ligne de commande Azure (CLI) 1.0 ou d’Azure PowerShell. Vous ne pouvez pas utiliser de fichier de configuration de réseau pour créer ou modifier un réseau virtuel via le modèle de déploiement Azure Resource Manager. Vous ne pouvez pas utiliser le portail Azure pour créer ou modifier un réseau virtuel (Classic) à l’aide d’un fichier config réseau, mais vous pouvez utiliser le portail Azure pour créer un réseau virtuel (Classic) sans l’aide d’un fichier config réseau.

La création et la configuration d’un réseau virtuel (Classic) avec un fichier config réseau requièrent l’exportation, la modification et l’importation du fichier.

## <a name="export"></a>Exporter un fichier config réseau

Vous pouvez utiliser PowerShell ou l’interface CLI Azure pour exporter un fichier config réseau. PowerShell exporte un fichier XML tandis que l’interface Azure CLI exporte un fichier JSON.

### <a name="powershell"></a>PowerShell
 
1. [Installez Azure PowerShell et connectez-vous à Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Modifiez le répertoire (et assurez-vous qu’il existe) ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour exporter le fichier config réseau :

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Installez l’interface de ligne de commande Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Effectuez les étapes restantes à partir d’une invite de commandes Azure CLI 1.0.
2. Connectez-vous à Azure en entrant la commande `azure login`.
3. Assurez-vous que vous vous trouvez bien en mode ASM par le biais de la commande `azure config mode asm`.
4. Modifiez le répertoire (et assurez-vous qu’il existe) ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour exporter le fichier config réseau :
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Créer ou modifier un fichier config réseau

Un fichier config réseau est un fichier XML (lorsque vous utilisez PowerShell) ou un fichier JSON (lorsque vous utilisez l’interface de ligne de commande Azure CLI). Vous pouvez modifier le fichier dans n’importe quel éditeur de texte, ou XML/JSON. L’article [Network configuration file schema settings](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Paramètres de schéma de fichier config réseau) inclut des informations détaillées pour tous les paramètres. Pour obtenir des explications supplémentaires sur les paramètres, consultez [Afficher des réseaux virtuels et des paramètres](manage-virtual-network.md#view-virtual-networks-and-settings). Les modifications apportées au fichier :

- Doivent respecter le schéma, sans quoi l’importation du fichier config réseau échouera.
- Remplacent les paramètres réseau existants de votre abonnement : soyez donc très vigilant lors de vos modifications. Par exemple, servez-vous des exemples de fichier config réseau qui suivent. Supposons que le fichier d’origine contienne deux instances **VirtualNetworkSite** et que vous l’avez modifié comme indiqué dans les exemples. Lorsque vous importez le fichier, Azure supprime le réseau virtuel de l’instance **VirtualNetworkSite** que vous avez supprimée dans le fichier. Ce scénario simplifié suppose qu’aucune ressource ne se trouve dans le réseau virtuel, car dans le cas contraire, le réseau virtuel ne pourrait pas être supprimé, et l’importation échouerait.

> [!IMPORTANT]
> Azure considère tout sous-réseau qui comprend des éléments déployés comme étant **« en cours d'utilisation »**. Une fois le sous-réseau utilisé, il ne peut pas être modifié. Avant toute modification des informations de sous-réseau dans un fichier config réseau, déplacez tout ce que vous avez déployé sur le sous-réseau vers un autre sous-réseau qui n’est pas en cours de modification. Consultez [Déplacer une machine virtuelle ou une instance de rôle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md) pour en savoir plus.

### <a name="example-xml-for-use-with-powershell"></a>Exemple de code XML pour une utilisation avec PowerShell

L’exemple de fichier config réseau suivant crée un réseau virtuel nommé *myVirtualNetwork* avec un espace d’adressage *10.0.0.0/16* dans la région Azure *États-Unis de l’Est*. Le réseau virtuel contient un sous-réseau nommé *mySubnet* avec le préfixe d’adresse *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Si le fichier config réseau que vous avez exporté ne comporte aucun contenu, vous pouvez copier le code XML de l’exemple précédent et le coller dans un nouveau fichier.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Exemple JSON pour une utilisation avec Azure CLI 1.0

L’exemple de fichier config réseau suivant crée un réseau virtuel nommé *myVirtualNetwork* avec un espace d’adressage *10.0.0.0/16* dans la région Azure *États-Unis de l’Est*. Le réseau virtuel contient un sous-réseau nommé *mySubnet* avec le préfixe d’adresse *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Si le fichier config réseau que vous avez exporté ne comporte aucun contenu, vous pouvez copier le code JSON de l’exemple précédent et le coller dans un nouveau fichier.

## <a name="import"></a>Importer un fichier config réseau

Vous pouvez utiliser PowerShell ou l’interface CLI Azure pour importer un fichier config réseau. PowerShell importe un fichier XML tandis qu’Azure CLI importe un fichier JSON. Si l’importation échoue, vérifiez que le fichier est compatible avec le [schéma de configuration réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installez Azure PowerShell et connectez-vous à Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Modifiez le répertoire ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour importer le fichier config réseau :
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Installez l’interface de ligne de commande Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Effectuez les étapes restantes à partir d’une invite de commandes Azure CLI 1.0.
2. Connectez-vous à Azure en entrant la commande `azure login`.
3. Assurez-vous que vous vous trouvez bien en mode ASM par le biais de la commande `azure config mode asm`.
4. Modifiez le répertoire ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour importer le fichier config réseau :

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
