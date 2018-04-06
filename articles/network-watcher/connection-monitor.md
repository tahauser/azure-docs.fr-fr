---
title: Surveiller les connexions réseau avec Azure Network Watcher - Portail Azure | Microsoft Docs
description: Apprenez à surveiller la connectivité réseau avec Azure Network Watcher à l’aide du portail Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Surveiller les connexions réseau avec Azure Network Watcher à l’aide du portail Azure

Apprenez à utiliser le moniteur de connexion pour surveiller la connectivité réseau entre une machine virtuelle Azure et une adresse IP. Le contrôle de la connexion fournit l’analyse à un niveau de la connexion. Une connexion est définie comme une combinaison d’adresse IP et de port de destination et sources. Le contrôle de la connexion permet des scénarios tels que l’analyse de la connectivité à partir d’une machine virtuelle dans un réseau virtuel vers une machine virtuelle exécutant le serveur SQL dans le réseau virtuel identique ou différent, sur le port 1433. Le contrôle de la connexion fournit la latence de la connexion en tant que métrique Azure Monitor, enregistrée toutes les 60 secondes. Il fournit également une topologie tronçon par tronçon et identifie les problèmes de configuration ayant un impact sur votre connexion.


## <a name="prerequisites"></a>Prérequis


Vous devez respecter les prérequis suivants avant d’effectuer les étapes décrites dans cet article :

* Une instance de Network Watcher dans la région pour laquelle vous voulez surveiller une connexion. Si vous n’en avez pas déjà une, vous pouvez en créer une en suivant les étapes indiquées dans [Créer une instance Azure Network Watcher](network-watcher-create.md).
* Une machine virtuelle à partir de laquelle effectuer la surveillance.
* Extension `AzureNetworkWatcherExtension` installée sur la machine virtuelle à partir de laquelle effectuer la surveillance d’une connexion. Pour installer l’extension sur une machine virtuelle Windows, consultez [Extension de machine virtuelle Agent Azure Network Watcher pour Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) et pour l’installer sur une machine virtuelle Linux, consultez [Extension de machine virtuelle Agent Azure Network Watcher pour Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au [Portail Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion

Les étapes suivantes permettent le contrôle de la connexion à une machine virtuelle de destination sur les ports 80 et 1433 :

1. Dans la partie gauche du portail, sélectionnez **Autres services**.
2. Commencez à taper *network watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
3. Sous **SURVEILLANCE**, sélectionnez **Moniteur de connexion (préversion)**. Les fonctionnalités en préversion n’offrent pas le même niveau de fiabilité ou de disponibilité régionale que les fonctionnalités de la version générale.
4. Sélectionnez **Ajouter**.
5. Entrez ou sélectionnez les informations appropriées à la connexion à contrôler, puis sélectionnez **Ajouter**. Dans l’exemple illustré dans l’image suivante, la connexion contrôlée est comprise entre les machines virtuelles *MultiTierApp0* et *Database0* :

    ![Ajouter un contrôle de la connexion](./media/connection-monitor/add-connection-monitor.png)

    La surveillance commence. Le moniteur de connexion procède à un sondage toutes les 60 secondes.

## <a name="view-connection-monitoring"></a>Afficher le contrôle de la connexion

1. Effectuez les étapes 1 à 3 dans [Créer un contrôle de la connexion](#create-a-connection-monitor) pour afficher le contrôle de la connexion.
2. L’image suivante affiche les détails de la connexion AppToDB(80). L’**État** est accessible. L’**Affichage graphique** montre le **temps d’aller-retour moyen** et le **% d’échecs des sondes**. Le graphique fournit des informations tronçon par tronçon et indique qu’aucun problème n’impacte l’accessibilité de la destination.

    ![Afficher le contrôle de la connexion](./media/connection-monitor/view-connection-monitor.png)

3. L’affichage du contrôle *AppToDB(1433)*, illustré dans l’image suivante, indique que pour les mêmes machines virtuelles source et de destination, l’état est inaccessible sur le port 1433. La **Vue de grille** dans ce scénario fournit les informations tronçon par tronçon et le problème ayant un impact sur l’accessibilité. Dans ce cas, une règle du Groupe de sécurité réseau bloque tout le trafic sur le port 1433 sur le second tronçon.

    ![Afficher le contrôle de la connexion](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en [créant une capture de paquets déclenchée par alerte](network-watcher-alert-triggered-packet-capture.md).
- Déterminez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en utilisant [Vérification du flux IP](network-watcher-check-ip-flow-verify-portal.md).