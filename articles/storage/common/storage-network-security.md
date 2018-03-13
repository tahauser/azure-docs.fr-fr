---
title: "Configurer des pare-feu et des réseaux virtuels dans Stockage Azure | Microsoft Docs"
description: "Configurez une sécurité réseau en couche pour votre compte de stockage."
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: fc13b7cc164c948f25a6908bdf71124a5be02fb9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurer des pare-feu et des réseaux virtuels dans Stockage Azure
Le service Stockage Azure fournit un modèle de sécurité en couche qui vous permet de sécuriser vos comptes de stockage sur un ensemble spécifique de réseaux autorisés.  Quand des règles de réseau sont configurées, seules les applications des réseaux autorisés peuvent accéder à un compte de stockage.  En cas d’appel à partir d’un réseau autorisé, les applications continuent à demander une autorisation appropriée (une clé d’accès ou un jeton SAS valide) pour accéder au compte de stockage.

> [!IMPORTANT]
> L’activation de règles de pare-feu pour votre compte de stockage bloque l’accès aux requêtes entrantes pour les données, notamment celles provenant d’autres services Azure.  Cela inclut l’utilisation du portail, l’écriture de journaux, et ainsi de suite.  Pour les services participants, vous pouvez réactiver la fonctionnalité dans la section [Exceptions](#Exceptions) ci-dessous.  Pour accéder au portail, vous devez utiliser un ordinateur qui se trouve dans la limite de confiance (IP ou réseau virtuel) que vous avez définie.
>

## <a name="scenarios"></a>Scénarios
Les comptes de stockage peuvent être configurés pour refuser l’accès au trafic de tous les réseaux (y compris le trafic Internet) par défaut.  Vous pouvez accorder l’accès au trafic en provenance de réseaux virtuels Azure spécifiques pour pouvoir créer une limite réseau sécurisé pour vos applications.  Vous pouvez aussi accorder l’accès à des plages d’adresses IP Internet publiques, en activant des connexions à partir de clients Internet ou locaux spécifiques.

Les règles de réseau sont appliquées sur tous les protocoles réseau vers le stockage Azure, notamment REST et SMB.  L’accès à vos données à partir d’outils comme le portail Azure, l’Explorateur Stockage et AZCopy nécessite des règles de réseau explicites qui accordent l’accès quand elles sont appliquées.

Les règles de réseau peuvent être appliquées aux comptes de stockage existants ou à la création des comptes de stockage.

Une fois que les règles de réseau sont appliquées, elles concernent toutes les demandes.  Les jetons SAS qui accordent l’accès à un service d’adresse IP spécifique servent à **limiter** l’accès du détenteur du jeton, mais n’accordent pas d’accès au-delà des règles de réseau configurées. 

Le trafic des disques de machine virtuelle (notamment les opérations de montage et démontage et les E/S de disque) n’est **pas** affecté par les règles de réseau.  L’accès REST aux objets blob de pages est protégé par les règles de réseau.

> [!NOTE]
> La sauvegarde et la restauration de machines virtuelles à l’aide de disques non managés dans des comptes de stockage avec des règles de réseau ne sont actuellement pas prises en charge.  Pour plus d’informations, consultez [Limites lors de la sauvegarde et de la restauration d’une machine virtuelle](/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm).
>

Les comptes de stockage Classic ne prennent **pas** en charge Pare-feu et réseaux virtuels.

## <a name="change-the-default-network-access-rule"></a>Changer la règle d’accès réseau par défaut
Par défaut, les comptes de stockage acceptent les connexions des clients sur n’importe quel réseau.  Pour limiter l’accès aux réseaux sélectionnés, vous devez d’abord changer l’action par défaut.

> [!WARNING]
> Le changement des règles de réseau peut impacter la capacité de vos applications à se connecter au stockage Azure.  La définition de la règle de réseau par défaut sur **Refuser** bloque tout accès aux données, sauf si des règles de réseau spécifiques *accordant* l’accès sont également appliquées.  Accordez l’accès uniquement aux réseaux autorisés à l’aide des règles de réseau avant de changer la règle par défaut pour refuser l’accès.
>

#### <a name="azure-portal"></a>Portail Azure
1. Accédez au compte de stockage que vous voulez sécuriser.  

2. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.
3. Pour refuser l’accès par défaut, choisissez d’autoriser l’accès à partir de « Réseaux sélectionnés ».  Pour autoriser le trafic de tous les réseaux, choisissez d’autoriser l’accès à partir de « Tous les réseaux ».
4. Cliquez sur *Enregistrer* pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell
1. Installez la dernière version d’[Azure PowerShell](/powershell/azure/install-azurerm-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

2. Affichez l’état de la règle par défaut pour le compte de stockage.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Définissez la règle par défaut pour refuser l’accès réseau par défaut.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Définissez la règle par défaut pour autoriser l’accès réseau par défaut.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Installez Azure CLI 2.0](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).
2. Affichez l’état de la règle par défaut pour le compte de stockage.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Définissez la règle par défaut pour refuser l’accès réseau par défaut.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Définissez la règle par défaut pour autoriser l’accès réseau par défaut.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Accorder l’accès à partir d’un réseau virtuel
Les comptes de stockage peuvent être configurés pour autoriser l’accès uniquement à partir de réseaux virtuels Azure spécifiques. 

En activant un [point de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview) pour Stockage Azure dans le réseau virtuel, le trafic suit une route optimale vers le service Stockage Azure. Les identités du réseau virtuel et du sous-réseau sont également transmises avec chaque demande.  Les administrateurs peuvent configurer par la suite des règles de réseau pour le compte de stockage qui autorisent la réception des demandes à partir de sous-réseaux spécifiques dans le réseau virtuel.  Les clients qui obtiennent un accès par le biais de ces règles de réseau doivent continuer à respecter les exigences d’autorisation du compte de stockage pour accéder aux données.

Chaque compte de stockage peut prendre en charge jusqu'à 100 règles de réseau virtuel qui peuvent être combinées avec des [règles de réseau IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Régions de réseau virtuel disponibles
En général, les points de terminaison de service fonctionnent entre les réseaux virtuels et les instances de service d’une même région Azure.  Quand les points de terminaison de service sont utilisés avec Stockage Azure, cette étendue inclut aussi la [région jumelée](/azure/best-practices-availability-paired-regions).  Cela permet une continuité des activités pendant un basculement régional ainsi qu’un accès sans interruption aux instances de stockage géoredondantes en lecture seule (RA-GRS).  Les règles de réseau qui autorisent l’accès à un compte de stockage à partir d’un réseau virtuel accordent également l’accès à toutes les instances RA-GRS.

Quand vous planifiez une récupération d’urgence en cas de panne régionale, vous devez approvisionner les réseaux virtuels à l’avance dans la région jumelée. Les points de terminaison de service pour le stockage Azure doivent être activés et les règles de réseau accordant l’accès à partir de ces réseaux virtuels alternatifs doivent être appliquées à vos comptes de stockage géoredondants.

> [!NOTE]
> Les points de terminaison de service ne s’appliquent pas au trafic extérieur à la région du réseau virtuel et à la région jumelée désignée.  Les règles de réseau accordant l’accès à partir de réseaux virtuels aux comptes de stockage peuvent uniquement être appliquées aux réseaux virtuels dans la région principale d’un compte de stockage ou dans la région jumelée désignée.
>

### <a name="required-permissions"></a>Autorisations requises
Pour appliquer une règle de réseau virtuel à un compte de stockage, l’utilisateur doit avoir l’autorisation de *Joindre le service à un sous-réseau* pour les sous-réseaux à ajouter.  Cette autorisation se trouve dans le rôle intégré *Contributeur de compte de stockage* et peut être ajoutée aux définitions de rôle personnalisées.

Le compte de stockage et les réseaux virtuels qui ont un accès **peuvent** se trouver dans des abonnements différents, mais ces abonnements doivent faire partie du même locataire Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Gestion des règles de réseau virtuel
Les règles de réseau virtuel pour les comptes de stockage peuvent être gérées dans le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure
1. Accédez au compte de stockage que vous voulez sécuriser.  
2. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.
3. Vérifiez que vous avez choisi d’autoriser l’accès à partir des « Réseaux sélectionnés ».
4. Pour accorder l’accès à un réseau virtuel avec une nouvelle règle de réseau, sous « Réseaux virtuels », cliquez sur « Ajouter existant » pour sélectionner un réseau virtuel et des sous-réseaux existants, puis cliquez sur *Ajouter*.  Pour créer un réseau virtuel et lui accorder un accès, cliquez sur *Ajouter nouveau*, fournissez les informations nécessaires pour créer le réseau virtuel, puis cliquez sur *Créer*.

> [!NOTE]
> Si un point de terminaison de service pour le stockage Azure n’a pas déjà été configuré pour le réseau virtuel et les sous-réseaux sélectionnés, il peut être configuré dans le cadre de cette opération.
>

5. Pour supprimer une règle de réseau ou sous-réseau virtuel, cliquez sur « ... » pour ouvrir le menu contextuel du réseau ou sous-réseau virtuel, puis cliquez sur « Supprimer ».
6. Cliquez sur *Enregistrer* pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell
1. Installez la dernière version d’[Azure PowerShell](/powershell/azure/install-azurerm-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).
2. Listez les règles de réseau virtuel.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Activez le point de terminaison de service pour Stockage Azure sur un réseau virtuel et un sous-réseau existants.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Supprimez une règle de réseau pour un réseau virtuel et un sous-réseau.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur Refuser, sinon les règles de réseau n’ont aucun effet.
>

#### <a name="cliv2"></a>CLIv2
1. [Installez Azure CLI 2.0](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).
2. Listez les règles de réseau virtuel.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Activez le point de terminaison de service pour Stockage Azure sur un réseau virtuel et un sous-réseau existants.
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Supprimez une règle de réseau pour un réseau virtuel et un sous-réseau. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur Refuser, sinon les règles de réseau n’ont aucun effet.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Accorder l’accès à partir d’une plage d’adresses IP Internet
Les comptes de stockage peuvent être configurés pour autoriser l’accès à partir de plages d’adresses IP Internet publiques spécifiques.  Cette configuration permet à des services Internet et des réseaux locaux spécifiques d’avoir un accès quand le trafic Internet général est bloqué.

Les plages d’adresses Internet autorisées peuvent être fournies à l’aide de la [notation CIDR](https://tools.ietf.org/html/rfc4632) sous la forme *16.17.18.0/24* ou comme des adresses IP individuelles de type *16.17.18.19*.

> [!NOTE]
> Les petites plages d’adresses qui utilisent les tailles de préfixe « /31 » ou « /32 » ne sont pas prises en charge.  Ces plages doivent être configurées à l’aide des règles d’adresses IP individuelles.
>

Les règles de réseau IP sont autorisées uniquement pour les adresses IP **Internet publiques**.  Les plages d’adresses IP réservées aux réseaux privés (comme défini dans RFC 1918) ne sont pas autorisées dans les règles IP.  Les réseaux privés incluent les adresses qui commencent par *10.\**, *172.16.\** et *192.168.\**

Seules les adresses IPv4 sont prises en charge pour le moment.

Chaque compte de stockage peut prendre en charge jusqu'à 100 règles de réseau IP qui peuvent être combinées avec des [règles de réseau virtuel](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Configuration de l’accès à partir de réseaux locaux
Pour accorder l’accès à votre compte de stockage à partir de réseaux locaux avec une règle de réseau IP, vous devez identifier les adresses IP Internet utilisées par votre réseau.  Contactez votre administrateur réseau pour obtenir de l’aide.

Si votre réseau est connecté au réseau Azure à l’aide d’[ExpressRoute](/azure/expressroute/expressroute-introduction), chaque circuit est configuré avec deux adresses IP publiques sur Microsoft Edge qui sont utilisées pour se connecter aux services Microsoft comme Stockage Azure au moyen de l’[Appairage public Azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Pour permettre la communication de votre circuit avec Stockage Azure, vous devez créer des règles de réseau IP pour les adresses IP publiques de vos circuits.  Pour rechercher les adresses IP publiques de votre circuit ExpressRoute, [ouvrez un ticket de support avec ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) dans le portail Azure.


### <a name="managing-ip-network-rules"></a>Gestion des règles de réseau IP
Les règles de réseau IP pour les comptes de stockage peuvent être gérées dans le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure
1. Accédez au compte de stockage que vous voulez sécuriser.  
2. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.
3. Vérifiez que vous avez choisi d’autoriser l’accès à partir des « Réseaux sélectionnés ».
4. Pour accorder l’accès à une plage d’adresses IP Internet, entrez l’adresse IP ou la plage d’adresses IP (au format CIDR) sous Pare-feu, Plages d’adresses.
5. Pour supprimer une règle de réseau IP, cliquez sur « ... » pour ouvrir le menu contextuel de la règle, puis cliquez sur « Supprimer ».
6. Cliquez sur *Enregistrer* pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell
1. Installez la dernière version d’[Azure PowerShell](/powershell/azure/install-azurerm-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).
2. Listez les règles de réseau IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Ajoutez une règle de réseau pour une adresse IP individuelle.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Ajoutez une règle de réseau pour une plage d’adresses IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Supprimez une règle de réseau pour une adresse IP individuelle. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Supprimez une règle de réseau pour une plage d’adresses IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur Refuser, sinon les règles de réseau n’ont aucun effet.
>

#### <a name="cliv2"></a>CLIv2
1. [Installez Azure CLI 2.0](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).
2. Listez les règles de réseau IP.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Ajoutez une règle de réseau pour une adresse IP individuelle.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Ajoutez une règle de réseau pour une plage d’adresses IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Supprimez une règle de réseau pour une adresse IP individuelle.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Supprimez une règle de réseau pour une plage d’adresses IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur Refuser, sinon les règles de réseau n’ont aucun effet.
>

## <a name="exceptions"></a>Exceptions
Bien que les règles de réseau puissent activer une configuration de réseau sécurisé dans la plupart des scénarios, il existe certains cas où des exceptions doivent être accordées pour activer toutes les fonctionnalités.  Les comptes de stockage peuvent être configurés avec des exceptions pour les services Microsoft approuvés et pour accéder aux données Storage Analytics.

### <a name="trusted-microsoft-services"></a>Services Microsoft approuvés
Certains services Microsoft qui interagissent avec les comptes de stockage fonctionnent à partir de réseaux qui ne peuvent pas obtenir l’accès par le biais des règles de réseau. 

Pour que ce type de service fonctionne comme prévu, vous pouvez autoriser l’ensemble des services Microsoft approuvés à contourner les règles de réseau. Ces services utilisent alors une authentification forte pour accéder au compte de stockage.

Quand l’exception « Services Microsoft approuvés » est activée, les services suivants (s’ils sont inscrits dans votre abonnement) ont accès au compte de stockage :

|Service|Nom du fournisseur de ressources|Objectif|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|Création d’une image personnalisée et installation de l’artefact.  [Plus d’informations](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview)|
|Azure Event Grid|Microsoft.EventGrid|Activation de la publication d’événements Stockage Blob.  [Plus d’informations](https://docs.microsoft.com/azure/event-grid/overview)|
|Hubs d'événements Azure|Microsoft.EventHub|Archivage des données avec Event Hubs Capture.  [En savoir plus](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Mise en réseau Azure|Microsoft.Networking|Stockage et analyse des journaux du trafic réseau.  [Plus d’informations](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
||||

### <a name="storage-analytics-data-access"></a>Accès aux données Storage Analytics
Dans certains cas, un accès en lecture aux journaux et aux métriques de diagnostic est nécessaire en dehors de la limite du réseau.  Les exceptions aux règles de réseau peuvent être accordées pour autoriser l’accès en lecture aux fichiers journaux ou aux tables de métriques du compte de stockage, ou aux deux. [Découvrez plus d’informations sur l’utilisation de Storage Analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestion des exceptions
Les exceptions de règle de réseau peuvent être gérées dans le portail Azure, PowerShell ou Azure CLI v2.

#### <a name="azure-portal"></a>Portail Azure
1. Accédez au compte de stockage que vous voulez sécuriser.  
2. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.
3. Vérifiez que vous avez choisi d’autoriser l’accès à partir des « Réseaux sélectionnés ».
4. Sous Exceptions, sélectionnez les exceptions que vous voulez accorder.
5. Cliquez sur *Enregistrer* pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell
1. Installez la dernière version d’[Azure PowerShell](/powershell/azure/install-azurerm-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).
2. Affichez les exceptions pour les règles de réseau du compte de stockage.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Configurez les exceptions aux règles de réseau du compte de stockage.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Supprimez les exceptions aux règles de réseau du compte de stockage.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur Refuser, sinon la suppression des exceptions n’a aucun effet.
>

#### <a name="cliv2"></a>CLIv2
1. [Installez Azure CLI 2.0](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).
2. Affichez les exceptions pour les règles de réseau du compte de stockage.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Configurez les exceptions aux règles de réseau du compte de stockage.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Supprimez les exceptions aux règles de réseau du compte de stockage.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur Refuser, sinon la suppression des exceptions n’a aucun effet.
>

## <a name="next-steps"></a>Étapes suivantes
Découvrez plus d’informations sur les points de terminaison de service du réseau Azure dans [Points de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview).

Explorez en détail la sécurité de Stockage Azure dans [Guide de sécurité de Stockage Azure](storage-security-guide.md).
