---
title: "Connexion à une machine virtuelle SQL Server sur Azure (classique) | Microsoft Docs"
description: "Découvrez comment vous connecter à SQL Server exécuté sur une machine virtuelle dans Azure. Cette rubrique utilise le modèle de déploiement classique. Les scénarios diffèrent selon la configuration réseau et l’emplacement du client."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: e986440edc0b683d1b1cd49207a355dac060c53d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Connexion à une machine virtuelle SQL Server dans Azure (déploiement classique)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Classique](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Cette rubrique décrit comment se connecter à votre instance de SQL Server exécuté sur une machine virtuelle Azure. Elle aborde certains [scénarios de connectivité générale](#connection-scenarios) et fournit une [procédure détaillée pour configurer la connectivité à SQL Server dans une machine virtuelle Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Si vous utilisez des machines virtuelles Resource Manager, consultez l’article [Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scénarios de connexion
La méthode utilisée par un client pour se connecter à un serveur SQL Server exécuté sur une machine virtuelle diffère selon l’emplacement du client et la configuration de la machine ou du réseau. Ces scénarios sont les suivants :

* [se connecter à SQL Server dans le même service de cloud computing](#connect-to-sql-server-in-the-same-cloud-service)
* [Se connecter à SQL Server via Internet](#connect-to-sql-server-over-the-internet)
* [se connecter à SQL Server dans le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Avant de vous connecter avec une de ces méthodes, vous devez suivre les [étapes de cet article pour configurer la connectivité](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>se connecter à SQL Server dans le même service de cloud computing
Plusieurs machines virtuelles peuvent être créées dans le même service cloud. Pour comprendre ce scénario impliquant des machines virtuelles, consultez la page [Connexion d’ordinateurs virtuels à un réseau virtuel ou un service cloud](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). Dans ce scénario, un client sur une machine virtuelle tente de se connecter à une instance SQL Server en cours d’exécution sur une autre machine virtuelle dans le même service cloud.

Dans ce scénario, vous pouvez vous connecter à l’aide de la machine virtuelle **nom** (représentée par **nom de l’ordinateur** ou **nom d’hôte** dans le portail). Il s’agit du nom que vous avez fourni pour la machine virtuelle lors de sa création. Par exemple, si vous avez nommé votre machine virtuelle SQL **mysqlvm**, une machine virtuelle cliente dans le même service cloud peut utiliser la chaîne de connexion suivante pour se connecter :

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Se connecter à SQL Server via Internet
Si vous souhaitez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, vous devez créer un point de terminaison de machine virtuelle pour les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

Pour vous connecter via Internet, vous devez utiliser le nom DNS de la machine virtuelle et le numéro de port du point de terminaison de la machine virtuelle (configuré plus loin dans cet article). Pour trouver le nom DNS, accédez au portail Azure, puis sélectionnez **Machines virtuelles (classiques)**. Sélectionnez ensuite votre machine virtuelle. Le **nom DNS** est indiqué dans la section **Vue d’ensemble**.

Prenons par exemple une machine virtuelle classique nommée **mysqlvm** avec comme nom DNS **mysqlvm7777.cloudapp.net** et un point de terminaison de machine virtuelle **57500**. En supposant que la connectivité est correctement configurée, vous pouvez utiliser la chaîne de connexion suivante pour accéder à la machine virtuelle à partir de n’importe où sur Internet :

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cette chaîne de connexion permet aux clients de se connecter via Internet, cela ne signifie pas que tout le monde peut se connecter à votre serveur SQL Server. Les clients externes doivent entrer les nom d’utilisateur et mot de passe corrects. Pour renforcer la sécurité, n’utilisez pas le port commun 1433 pour le point de terminaison de la machine virtuelle publique. Dans la mesure du possible, vous pouvez également envisager d’ajouter une liste de contrôle d’accès réseau (ACL) sur votre point de terminaison pour limiter le trafic uniquement aux clients que vous autorisez. Pour obtenir des instructions sur l’utilisation de listes ACL avec des points de terminaison, voir [Gestion de l’ACL sur un point de terminaison](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)

> [!NOTE]
> Lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données sortantes depuis le centre de données sont sujettes à la [tarification sur les transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) standard.
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>se connecter à SQL Server dans le même réseau virtuel
[réseau virtuel](../../../virtual-network/virtual-networks-overview.md) offre des scénarios supplémentaires. Vous pouvez connecter les machines virtuelles au sein d’un même réseau virtuel, même si elles existent dans différents services cloud. De plus, un [VPN de site à site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)permet de créer une architecture hybride qui connecte les machines virtuelles aux machines et réseaux locaux.

Les réseaux virtuels vous permettent également d’associer vos machines virtuelles Azure à un domaine. Joindre un domaine la seule façon d’utiliser l’authentification Windows avec SQL Server. Les autres scénarios de connexion requièrent l’authentification SQL avec des noms d’utilisateur et mots de passe.

Si vous souhaitez configurer un environnement de domaine et l’authentification Windows, il est inutile d’effectuer la procédure de configuration du point de terminaison public ou de l’authentification SQL et des connexions. Dans ce scénario, vous pouvez vous connecter à votre instance SQL Server en spécifiant le nom de la machine virtuelle SQL Server dans la chaîne de connexion. L’exemple suivant part du principe que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Procédure de configuration de la connectivité SQL Server dans une machine virtuelle Azure
Les étapes suivantes montrent comment se connecter à l’instance SQL Server sur Internet à l’aide de SQL Server Management Studio (SSMS). Toutefois, les mêmes étapes s’appliquent pour rendre votre machine virtuelle SQL Server accessible pour vos applications exécutées en local et dans Azure.

Avant de pouvoir vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou d’Internet, vous devez effectuer les tâches suivantes :

* [Créer un point de terminaison TCP pour la machine virtuelle](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Ouvrir des ports TCP dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurer SQL Server pour l’écoute du protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
* [Créer des connexions d’authentification SQL Server](#create-sql-server-authentication-logins)
* [Déterminer le nom DNS de la machine virtuelle](#determine-the-dns-name-of-the-virtual-machine)
* [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

Le chemin de connexion est résumé dans le schéma suivant :

![Connexion à une machine virtuelle SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Étapes suivantes
Si vous envisagez également d’utiliser des groupes de disponibilité AlwaysOn pour la haute disponibilité et la récupération d’urgence, nous vous recommandons d’implémenter un écouteur. Les clients de base de données se connectent à l’écouteur plutôt que directement à l’une des instances SQL Server. L’écouteur achemine les clients vers le réplica principal du groupe de disponibilité. Pour plus d’informations, voir [Configuration d’un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](../classic/ps-sql-int-listener.md).

Il est important d’examiner toutes les recommandations de sécurité pour SQL Server exécuté sur une machine virtuelle Azure. Pour plus d’informations, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-security.md).

[Découvrez le parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur les machines virtuelles Azure. 

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

