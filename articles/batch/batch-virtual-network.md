---
title: Configurer le pool Azure Batch dans un réseau virtuel | Microsoft Docs
description: Vous pouvez créer un pool Batch dans un réseau virtuel afin que les nœuds de calcul puissent communiquer en toute sécurité avec d’autres machines virtuelles du réseau, comme un serveur de fichiers.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/05/2018
ms.author: danlep
ms.openlocfilehash: 5a06ad5086a42bb00147e085227f3c71c357544e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Créer un pool Azure Batch dans un réseau virtuel


Quand vous créez un pool Azure Batch, vous pouvez configurer le pool dans un sous-réseau d’un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) (VNet) que vous spécifiez. Cet article explique comment configurer un pool Batch dans un réseau virtuel. 



## <a name="why-use-a-vnet"></a>Pourquoi utiliser un réseau virtuel ?


Un pool Azure Batch comporte des paramètres qui servent à autoriser les nœuds de calcul à communiquer entre eux, par exemple, pour exécuter des tâches multi-instances. Ces paramètres n’exigent pas de réseau virtuel distinct. En revanche, par défaut, les nœuds ne peuvent pas communiquer avec des machines virtuelles qui ne font pas partie du pool Batch, comme un serveur de licences ou un serveur de fichiers. Pour autoriser les nœuds de calcul du pool à communiquer en toute sécurité avec d’autres machines virtuelles, ou avec un réseau local, vous pouvez configurer le pool dans un sous-réseau d’un réseau virtuel Azure. 



## <a name="prerequisites"></a>Prérequis


* **Authentification**. Pour utiliser un réseau virtuel Azure, l’API du client Batch doit utiliser l’authentification Azure Active Directory (AD). La prise en charge d’Azure Batch pour Azure AD est documentée dans [Authentifier les solutions de service Batch avec Active Directory](batch-aad-auth.md). 

* **Un réseau virtuel Azure**. Pour préparer un réseau virtuel avec un ou plusieurs sous-réseaux à l’avance, vous pouvez utiliser le portail Azure, Azure PowerShell, l’interface de ligne de commande Azure (CLI) ou d’autres méthodes. Pour créer un réseau virtuel basé sur Azure Resource Manager, consultez [Créer un réseau virtuel](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Pour créer un réseau virtuel classique, consultez [Créer un réseau virtuel (Classic) comprenant plusieurs sous-réseaux](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>Configuration requise du réseau virtuel
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Créer un pool avec un réseau virtuel dans le portail

Lorsque vous avez créé votre réseau virtuel et lui avez attribué un sous-réseau, vous pouvez créer un pool Batch avec ce réseau virtuel. Suivez ces étapes pour créer un pool à partir du portail Azure : 



1. Accédez à votre compte  Batch dans le portail Azure. Ce compte doit relever du même abonnement et de la même région que le groupe de ressources contenant le réseau virtuel que vous envisagez d’utiliser. 
2. Dans la fenêtre **Paramètres** située à gauche, sélectionnez l’élément de menu **Pools**.
3. Dans la fenêtre **Pools**, sélectionnez la commande **Ajouter**.
4. Dans la fenêtre **Ajouter un pool**, sélectionnez l’option que vous souhaitez utiliser à partir de la liste déroulante **Type d’image**. 
5. Sélectionnez le **Serveur de publication/Offre/Référence (SKU)** correspondant à votre image personnalisée.
6. Spécifiez les autres paramètres obligatoires, notamment **Taille de nœud**, **Nœuds dédiés cibles** et **Nœuds basse priorité** et éventuellement les paramètres facultatifs.
7. Dans **Réseau virtuel**, sélectionnez le réseau virtuel et le sous-réseau que vous souhaitez utiliser.
  
  ![Ajouter un pool avec un réseau virtuel](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Routages définis par l’utilisateur pour le tunneling forcé

Votre organisation vous oblige peut-être à rediriger (forcer) le trafic Internet provenant du sous-réseau vers votre emplacement local à des fins d’inspection et de journalisation. Vous avez peut-être activé le tunneling forcé pour les sous-réseaux de votre réseau virtuel. 

Pour vérifier que les nœuds de calcul de votre pool Azure Batch fonctionnent dans un réseau virtuel pour lequel le tunneling forcé est activé, vous devez ajouter les [routages définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) suivants pour ce sous-réseau :

* Le service Batch doit communiquer avec les nœuds de calcul du pool pour la planification des tâches. Pour activer cette communication, ajoutez un routage défini par l’utilisateur pour chaque adresse IP utilisée par le service Batch dans la région où se trouve votre compte Batch. Pour obtenir la liste des adresses IP du service Batch, contactez le support Azure.

* Vérifiez que le trafic sortant vers le stockage Azure (plus précisément, les URL sous la forme `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` et `<account>.blob.core.windows.net`) n’est pas bloqué par votre appliance de réseau local.

Lorsque vous ajoutez un itinéraire défini par l’utilisateur, définissez l’itinéraire pour chaque préfixe d’adresse IP Batch connexe et définissez **Type de tronçon suivant** sur **Internet**. Voir l’exemple suivant :

![Itinéraire défini par l’utilisateur](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
- Pour plus d’informations sur la création d’un itinéraire défini par l’utilisateur, consultez [Créer un itinéraire défini par l’utilisateur - portail Azure](../virtual-network/tutorial-create-route-table-portal.md).
