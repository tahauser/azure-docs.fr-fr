---
title: "Gestion des adresses IP réservées Azure (Classic) - PowerShell | Microsoft Docs"
description: "Comprendre les adresses IP réservées (Classic) et la manière de les gérer à l’aide de PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 5e9c83cebec96c6bc8afd53b0c637d7af899746f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="reserved-ip-addresses-classic"></a>Adresses IP réservées (Classic)

> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interface de ligne de commande Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modèle](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (classique)](virtual-networks-reserved-public-ip.md)

Il existe deux catégories d’adresses IP dans Azure, les réservées et les dynamiques. Les adresses IP publiques gérées par Azure sont dynamiques par défaut. Cela signifie que l'adresse IP utilisée pour un service cloud donné (adresse IP virtuelle) ou pour accéder à une machine virtuelle ou à une instance de rôle directement (ILPIP) peut changer à tout moment, lorsque les ressources sont arrêtées ou désallouées.

Pour empêcher la modification des adresses IP, vous pouvez réserver une adresse IP. Les adresses IP réservées peuvent uniquement servir d’adresse IP virtuelle, garantissant que l'adresse IP utilisée pour le service cloud reste la même, et ce même si les ressources sont arrêtées ou désallouées. Par ailleurs, vous pouvez convertir une adresse IP dynamique existante utilisée comme adresse IP virtuelle en adresse IP réservée.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment réserver une adresse IP publique statique à l’aide du [modèle de déploiement Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Pour en savoir plus sur les adresses IP dans Azure, voir l’article [Adresses IP](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Quand ai-je besoin d’une adresse IP réservée ?
* **Vous souhaitez vous assurer que l'adresse IP est réservée dans votre abonnement**. Si vous souhaitez réserver une adresse IP qui reste associée à votre abonnement en toute circonstance, vous devez utiliser une adresse IP publique réservée.  
* **Vous souhaitez que votre adresse IP reste associée à votre service cloud, même lorsque les machines virtuelles sont arrêtées ou désallouées**. Si vous souhaitez que votre service soit accessible à l'aide d'une adresse IP qui ne change pas, même lorsque les machines virtuelles dans le service cloud sont arrêtées ou désallouées.
* **Vous souhaitez vous assurer que le trafic provenant d'Azure utilise une adresse IP prévisible**. Il se peut que la configuration de votre pare-feu local autorise uniquement le trafic provenant d'adresses IP spécifiques. En réservant une adresse IP, vous connaissez l'adresse IP source et n'avez pas besoin de mettre à jour les règles de pare-feu suite à une modification d'adresse IP.

## <a name="faq"></a>Forum Aux Questions
1. Puis-je utiliser une adresse IP réservée pour tous les services Azure ? <br>
    Non. Les adresses IP réservées peuvent être utilisées uniquement pour les machines virtuelles et les rôles d'instance de service cloud exposés par une adresse IP virtuelle.
2. Combien d’adresses IP réservées puis-je avoir ? <br>
    Pour plus d’informations, consultez l’article sur les [limites Azure](../azure-subscription-service-limits.md#networking-limits).
3. L’obtention d’adresses IP réservées est-elle payante ? <br>
    Parfois. Pour plus d’informations sur la tarification, consultez la [Tarification – Adresses IP réservées](http://go.microsoft.com/fwlink/?LinkID=398482).
4. Comment réserver une adresse IP ? <br>
    Vous pouvez utiliser PowerShell, l’[API REST de gestion Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) ou le [portail Azure](https://portal.azure.com) pour réserver une adresse IP dans une région Azure. Une adresse IP réservée est associée à votre abonnement.
5. Puis-je utiliser une adresse IP réservée avec des réseaux virtuels basés sur un groupe d'affinités ? <br>
    Non. Les adresses IP réservées sont uniquement prises en charge dans les réseaux virtuels régionaux. Les adresses IP réservées ne sont pas prises en charge dans les réseaux virtuels associés à des groupes d’affinités. Pour plus d'informations sur l'association d'un réseau virtuel à une région ou un groupe d'affinités, consultez l’article [À propos des réseaux virtuels et groupes d’affinités régionaux](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Gérer les adresses IP virtuelles réservées

Vérifiez que vous avez installé et configuré PowerShell en procédant de la manière décrite dans l’article [Installer et configurer Azure PowerShell](/powershell/azure/overview). 

Avant de pouvoir utiliser une adresse IP réservée, vous devez l'ajouter à votre abonnement. Pour créer une adresse IP réservée à partir du pool d’adresses IP publiques disponibles dans la région *États-Unis du Centre*, exécutez la commande suivante :

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Toutefois, veuillez noter que vous ne pouvez pas spécifier quelle adresse IP vous souhaitez réserver. Pour voir quelles adresses IP sont réservées dans votre abonnement, exécutez la commande PowerShell suivante et notez les valeurs de *ReservedIPName* et *Address* :

```powershell
Get-AzureReservedIP
```

Sortie attendue :

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Lorsque vous créez une adresse IP réservée avec PowerShell, vous ne pouvez pas spécifier un groupe de ressources pour y créer l’adresse IP réservée. Azure place automatiquement cette adresse dans un groupe de ressources nommé *Default-Networking*. Si vous créez l’adresse IP réservée à l’aide du [portail Azure](http://portal.azure.com), vous pouvez spécifier le groupe de ressources de votre choix. Cependant, si vous créez l’adresse IP réservée dans un groupe de ressources autres que *Default-Networking*, chaque fois que vous référencez l’adresse IP réservée avec des commandes telles que `Get-AzureReservedIP` et `Remove-AzureReservedIP`, vous devez référencer le nom *Group resource-group-name reserved-ip-name*.  Par exemple, si vous créez une adresse IP réservée nommée *myReservedIP* dans un groupe de ressources nommé *myResourceGroup*, vous devez référencer le nom de l’adresse IP réservée comme *Group myResourceGroup myReservedIP*.   

Une fois une adresse IP réservée, elle reste associée à votre abonnement jusqu'à ce que vous la supprimiez. Pour supprimer une adresse IP réservée, exécutez la commande PowerShell suivante :

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Réserver l’adresse IP d’un service cloud existant
Vous pouvez réserver l’adresse IP d’un service cloud existant en ajoutant le paramètre `-ServiceName`. Pour réserver l’adresse IP d’un service cloud *TestService* dans les *États-Unis du Centre*, exécutez la commande PowerShell suivante :

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associer une adresse IP réservée à un service cloud
Le script suivant crée une adresse IP réservée, puis l’associe à un nouveau service cloud nommé *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Lorsque vous créez une adresse IP réservée à utiliser avec un service cloud, vous devez toujours faire référence à la machine virtuelle en utilisant *VIP:&lt;numéro de port>* pour les communications entrantes. Avoir une adresse IP réservée ne signifie pas que vous pouvez vous connecter directement à la machine virtuelle. L'adresse IP réservée est affectée au service cloud sur lequel la machine virtuelle a été déployée. Si vous souhaitez vous connecter à une machine virtuelle directement avec l’adresse IP, vous devez configurer une adresse IP publique de niveau de l'instance. Une adresse IP publique de niveau d'instance est un type d'adresse IP publique (appelée ILPIP) qui est affectée directement à votre machine virtuelle. Elle ne peut pas être réservée. Pour plus d’informations, consultez l’article [Adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md).
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Supprimer une adresse IP réservée d’un déploiement en cours d’exécution
Pour supprimer une adresse IP réservée ajoutée à un nouveau service cloud, exécutez la commande PowerShell suivante :

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Si vous supprimez une adresse IP réservée d’un déploiement en cours d'exécution, la réservation ne sera pas supprimée de votre abonnement. Cela libérera simplement l'adresse IP à utiliser par une autre ressource dans votre abonnement.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associer une adresse IP réservée à un déploiement en cours d’exécution
Les commandes suivantes créent un service cloud nommé *TestService2* avec une nouvelle machine virtuelle appelée *TestVM2*. L’adresse IP réservée nommée *MyReservedIP* est ensuite associée au service cloud.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associer une adresse réservée à un service cloud à l’aide d’un fichier de configuration de service
Vous pouvez aussi associer une IP réservée à un service cloud à l’aide d’un fichier de configuration de service (CSCFG). L’exemple de code xml suivant indique comment configurer un service cloud pour l’utilisation d’une adresse IP réservée nommée *MyReservedIP* :

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) fonctionne dans le modèle de déploiement Classic.
* En savoir plus sur [les adresses IP privées réservées](virtual-networks-reserved-private-ip.md).
* En savoir plus sur [les adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md).

