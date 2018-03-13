---
title: "Surveiller les connexions réseau avec Azure Network Watcher - Portail Azure | Microsoft Docs"
description: "Apprenez à surveiller la connectivité réseau avec Azure Network Watcher à l’aide du portail Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Surveiller les connexions réseau avec Azure Network Watcher à l’aide du portail Azure

Apprenez à utiliser le moniteur de connexion pour surveiller la connectivité réseau entre une machine virtuelle Azure et une adresse IP. L’adresse IP peut être attribuée à une autre ressource Azure ou bien à une ressource Internet ou locale.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’effectuer les étapes décrites dans cet article :

* Une instance de Network Watcher dans la région pour laquelle vous voulez surveiller une connexion. Si vous n’en avez pas déjà une, vous pouvez en créer une en suivant les étapes indiquées dans [Créer une instance Azure Network Watcher](network-watcher-create.md).
* Une machine virtuelle à partir de laquelle effectuer la surveillance.
* Extension `AzureNetworkWatcherExtension` installée sur la machine virtuelle à partir de laquelle effectuer la surveillance d’une connexion. Pour installer l’extension sur une machine virtuelle Windows, consultez [Extension de machine virtuelle Agent Azure Network Watcher pour Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) et pour l’installer sur une machine virtuelle Linux, consultez [Extension de machine virtuelle Agent Azure Network Watcher pour Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au [Portail Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion

1. Dans la partie gauche du portail, sélectionnez **Autres services**.
2. Commencez à taper *network watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
3. Sous **SURVEILLANCE**, sélectionnez **Moniteur de connexion (préversion)**. Les fonctionnalités en préversion n’offrent pas le même niveau de fiabilité ou de disponibilité régionale que les fonctionnalités de la version générale.
4. Sélectionnez **Ajouter**.
5. Entrez ou sélectionnez les informations appropriées à la connexion à surveiller, puis sélectionnez **Ajouter**. Dans cet exemple, une connexion entre les machines virtuelles *myVmSource* et *myVmDestination* est surveillée sur le port 80.
    
    |  Paramètre                                 |  Valeur               |
    |  -------------------------------------   |  ------------------- |
    |  NOM                                    |  myConnectionMonitor |
    |  Machine virtuelle source                  |  myVmSource          |
    |  Port source                             |                      |
    |  Destination, sélectionner une machine virtuelle   |  myVmDestination     |
    |  Port de destination                        |  80                  |

6. La surveillance commence. Le moniteur de connexion procède à un sondage toutes les 60 secondes.
7. Le moniteur de connexion vous montre les moyennes en temps et en pourcentage des sondages aller-retour qui échouent. Vous pouvez afficher les données de surveillance dans une grille ou un graphe.

## <a name="next-steps"></a>étapes suivantes

- Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en [créant une capture de paquets déclenchée par alerte](network-watcher-alert-triggered-packet-capture.md).

- Déterminez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en utilisant [Vérification du flux IP](network-watcher-check-ip-flow-verify-portal.md).