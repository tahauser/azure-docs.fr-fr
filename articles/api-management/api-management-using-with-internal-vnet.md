---
title: "Guide pratique de la Gestion des API Azure avec des réseaux virtuels internes | Microsoft Docs"
description: "Découvrez comment installer et configurer la Gestion des API Azure sur un réseau virtuel interne"
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: cf062cfcbbb2454adf20a06c31c81a60f6f5719f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utiliser le service Gestion des API Azure avec un réseau virtuel interne
Avec les réseaux virtuels Azure, la Gestion des API Azure peut gérer des API inaccessibles sur Internet. Plusieurs technologies VPN sont disponibles pour établir la connexion. La Gestion des API peut être déployée selon deux modes principaux à l’intérieur d’un réseau virtuel :
* Externe
* Interne


En cas de déploiement de Gestion des API dans un mode réseau virtuel interne, tous les points de terminaison de service (passerelle, portail des développeurs, portail Azure, gestion directe et Git) ne sont visibles que dans un réseau virtuel auquel vous contrôlez l’accès. Aucun point de terminaison de service n’est inscrit sur le serveur DNS Public.

Avec la Gestion des API en mode interne, vous pouvez effectuer les scénarios suivants :
* Rendre les API hébergées dans votre centre de données privé accessibles de l’extérieur en toute sécurité à des tiers à l’aide de connexions site à site ou de connexions Azure VPN ExpressRoute.
* Activer les scénarios de cloud hybride en exposant vos API cloud et sur site par le biais d’une passerelle commune.
* Gérer vos API hébergées dans plusieurs emplacements géographiques à l’aide d’un seul point de terminaison de passerelle. 


## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ **Un abonnement Azure actif**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Une instance du service Gestion des API Azure**. Pour plus d'informations, consultez [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Créer une Gestion des API dans un réseau virtuel interne
Le service Gestion des API dans un réseau virtuel interne est hébergé derrière un équilibreur de charge interne.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Activer une connexion de réseau virtuel à l’aide du portail Azure

1. Accédez à votre instance Gestion des API Azure dans le [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Réseau virtuel**.
3. Configurez l’instance Gestion des API pour la déployer à l’intérieur du réseau virtuel.

    ![Menu pour configurer une Gestion des API Azure dans un réseau virtuel interne][api-management-using-internal-vnet-menu]

4. Sélectionnez **Enregistrer**.

Une fois le déploiement réussi, l’adresse IP virtuelle interne de votre service apparaît sur le tableau de bord.

![Tableau de bord Gestion des API avec réseau virtuel interne configuré][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Activer une connexion de réseau virtuel à l’aide des applets de commande PowerShell
Vous pouvez également activer une connectivité de réseau virtuel à l’aide d’applets de commande PowerShell.

* Créer un service Gestion des API au sein d’un réseau virtuel : utilisez l’applet de commande [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) pour créer un service Gestion des API Azure au sein d’un réseau virtuel et le configurer de sorte qu’il utilise le type réseau virtuel interne.

* Déployer un service Gestion des API existant au sein d’un réseau virtuel : utilisez l’applet de commande [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) pour déplacer un service Gestion des API existant au sein d’un réseau virtuel et le configurer de sorte qu’il utilise le type réseau virtuel interne.

## <a name="apim-dns-configuration"></a>Configuration DNS
Lorsque la Gestion des API se trouve en mode réseau virtuel externe, le DNS est géré par Azure. En mode réseau virtuel interne, vous devez gérer votre propre routage.

> [!NOTE]
> Le service Gestion des API n’écoute pas les demandes provenant des adresses IP. Il répond uniquement aux demandes pour le nom d’hôte configuré sur ses points de terminaison de service. Ces points de terminaison incluent la passerelle, le portail des développeurs, le portail Azure, le point de terminaison de la gestion directe et Git.

### <a name="access-on-default-host-names"></a>Accès sur les noms d’hôtes par défaut
Lorsque vous créez un service Gestion des API, nommé « contoso » par exemple, les points de terminaison de service suivants sont configurés par défaut :

   * Passerelle ou proxy : contoso.azure-api.net

   * Portail Azure et portail des développeurs : contoso.portal.azure-api.net

   * Point de terminaison de gestion directe : contoso.management.azure-api.net

   * Git : contoso.scm.azure-api.net

Pour accéder à ces points de terminaison de service Gestion des API, vous pouvez créer une machine virtuelle dans un sous-réseau connecté au réseau virtuel dans lequel la Gestion des API est déployée. En supposant que l’adresse IP virtuelle interne de votre service est 10.0.0.5, vous pouvez effectuer le mappage des fichiers hôtes, %SystemDrive%\drivers\etc\hosts, de la façon suivante :

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

Vous pouvez alors accéder à tous les points de terminaison de service à partir de la machine virtuelle que vous avez créée. Si vous utilisez un serveur DNS personnalisé dans un réseau virtuel, vous pouvez également créer des enregistrements DNS A et accéder à ces points de terminaison à partir de l’endroit de votre choix dans votre réseau virtuel. 

### <a name="access-on-custom-domain-names"></a>Accès sur des noms de domaines personnalisés

   1. Si vous ne souhaitez pas accéder au service Gestion des API avec les noms d’hôtes par défaut, vous pouvez configurer des noms de domaines personnalisés pour tous vos points de terminaison de service, comme indiqué dans l’image suivante : 

   ![Configuration d’un domaine personnalisé pour la Gestion des API][api-management-custom-domain-name]

   2. Vous pouvez ensuite créer des enregistrements dans votre serveur DNS de façon à accéder aux points de terminaison qui ne sont accessibles qu’à partir de votre réseau virtuel.

## <a name="routing"> </a> Routage
+ Une adresse IP virtuelle privée à charge équilibrée de la plage de sous-réseau est réservée et utilisée pour accéder aux points de terminaison du service Gestion des API service à partir du réseau virtuel.
+ Une adresse IP publique à charge équilibrée (adresse IP virtuelle) est également réservée pour fournir l’accès au point de terminaison de service de gestion uniquement sur le port 3443.
+ Une adresse IP d’une plage d’adresses IP de sous-réseau (adresse IP dynamique) est utilisée pour accéder aux ressources sur le réseau virtuel, tandis qu’une adresse IP publique (adresse IP virtuelle) est utilisée pour accéder aux ressources à l’extérieur du réseau virtuel.
+ Les adresses IP privée et publique à charge équilibrée se trouvent dans le panneau Vue d’ensemble/Bases sur le portail Azure.

## <a name="related-content"></a>Contenu connexe
Pour en savoir plus, consultez les articles suivants :
* [Problèmes courants de configuration réseau lors de la configuration de la Gestion des API Azure dans un réseau virtuel][Common network configuration problems]
* [FAQ des réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Création d’un enregistrement A dans DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

