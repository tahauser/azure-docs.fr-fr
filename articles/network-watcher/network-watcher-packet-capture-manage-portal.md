---
title: "Gérer les captures de paquets avec Azure Network Watcher - Portail Azure | Microsoft Docs"
description: "Cette page explique comment gérer la fonctionnalité de capture de paquets de Network Watcher à l’aide du portail Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 40414afbfcd456353b4290585ccd9d594fbf55dd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gérer les captures de paquets avec Azure Network Watcher à l’aide du portail

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST Azure](network-watcher-packet-capture-manage-rest.md)

La fonctionnalité de capture des paquets Network Watcher vous permet de créer des sessions de capture afin d’effectuer le suivi du trafic en direction et en provenance d’une machine virtuelle. Les filtres sont fournis pour la session de capture afin de vous garantir que vous capturez uniquement le trafic souhaité. La capture des paquets permet de diagnostiquer les anomalies réseau de manière proactive et réactive. Elle permet aussi de collecter des statistiques réseau, d’obtenir des informations sur les intrusions, de déboguer des communications client-serveur, etc. En déclenchant à distance des captures de paquets, cette fonctionnalité simplifie l’exécution manuelle de la capture de paquets sur l’ordinateur souhaité, ce qui permet d’économiser un temps précieux.

Cet article passe en revue les différentes tâches de gestion actuellement disponibles pour la capture de paquets.

- [**Démarrer une capture de paquets**](#start-a-packet-capture)
- [**Arrêter une capture de paquets**](#stop-a-packet-capture)
- [**Supprimer une capture de paquets**](#delete-a-packet-capture)
- [**Télécharger une capture de paquets**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez des ressources suivantes :

- Une instance de Network Watcher dans la région où vous souhaitez créer une capture de paquets.
- Une machine virtuelle avec l’extension de capture de paquets activée.

> [!IMPORTANT]
> La capture de paquets requiert une extension de machine virtuelle `AzureNetworkWatcherExtension`. Pour installer l’extension sur une machine virtuelle Windows, consultez la page [Azure Network Watcher Agent virtual machine extension for Windows](../virtual-machines/windows/extensions-nwa.md) (Extension de machine virtuelle d’agent Azure Network Watcher pour Windows). Pour une machine virtuelle Linux, consultez la page [Azure Network Watcher Agent virtual machine extension for Linux](../virtual-machines/linux/extensions-nwa.md) (Extension de machine virtuelle d’agent Azure Network Watcher pour Linux).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Extension de l’agent de capture de paquets via le portail

Pour installer l’agent de machine virtuelle de capture de paquets via le portail, accédez à votre machine virtuelle, cliquez sur **Extensions** > **Ajouter** et recherchez **Agent Network Watcher pour Windows**

![vue de l’agent][agent]

## <a name="packet-capture-overview"></a>Vue d’ensemble des captures de paquets

Accédez au [Portail Azure](https://portal.azure.com) et cliquez sur **Mise en réseau** > **Network Watcher** > **Capture de paquets**.

La page de vue d’ensemble affiche la liste de toutes les captures de paquets existantes, quel que soit leur état.

> [!NOTE]
> La capture de paquets requiert la connectivité suivante.
> * Connectivité sortante au compte de stockage sur le port 443.
> * Connectivité entrante et sortante à 169.254.169.254
> * Connectivité entrante et sortante à 168.63.129.16

![Écran Vue d’ensemble des captures de paquets][1]

## <a name="start-a-packet-capture"></a>Démarrer une capture de paquets

Cliquez sur **Ajouter** pour créer une capture de paquets.

Les propriétés suivantes peuvent être définies dans une capture de paquets :

**Paramètres principaux**

- **Abonnement** : cette valeur correspond à l’abonnement utilisé. Une instance de Network Watcher est requise dans chaque abonnement.
- **Groupe de ressources** : groupe de ressources de la machine virtuelle ciblée.
- **Machine virtuelle cible** : machine virtuelle qui exécute la capture des paquets.
- **Nom de la capture de paquets** : cette valeur correspond au nom de la capture de paquets.

**Configuration de la capture**

- **Chemin de fichier local** : chemin local sur la machine virtuelle où la capture de paquets est enregistrée (valide uniquement quand **[fichier]** est sélectionné). Vous devez spécifier un chemin valide. Si vous utilisez une machine virtuelle Linux, le chemin doit commencer par / var / captures.
- **Compte de stockage** : détermine si la capture de paquets est enregistrée dans un compte de stockage.
- **Fichier** : détermine si la capture de paquets est enregistrée localement sur la machine virtuelle.
- **Comptes de stockage** : compte de stockage sélectionné dans lequel la capture de paquets est enregistrée. Emplacement par défaut : https://{nom du compte de stockage}.blob.core.windows.net/network-watcher-logs/subscriptions/{ID d’abonnement}/resourcegroups/{nom du groupe de ressources}/providers/microsoft.compute/virtualmachines/{nom de la machine virtuelle}/{AA}/{MM}/{JJ}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap. (Activé uniquement si **Stockage** est sélectionné.)
- **Chemin de fichier local** : chemin d’accès local sur une machine virtuelle pour enregistrer la capture des paquets. (Activé uniquement si **Fichier** est sélectionné.) Un chemin valide doit être fourni. Pour une machine virtuelle Linux, le chemin doit commencer par */var/captures*.
- **Nombre maximal d’octets par paquet** : nombre d’octets capturés dans chaque paquet. Si ce champ est vide, tous les octets sont capturés.
- **Nombre maximal d’octets par session** : nombre total d’octets capturés. Une fois cette valeur atteinte, la capture de paquets s’arrête.
- **Délai imparti (secondes)** : définit une limite de temps pour arrêter la capture de paquets. La valeur par défaut est de 18 000 secondes.

> [!NOTE]
> Les comptes de stockage Premium ne sont actuellement pas pris en charge pour le stockage des captures de paquets.

**Filtrage (facultatif)**

- **Protocole** : protocole permettant de filtrer la capture de paquets. Valeurs possibles : TCP, UDP et Quelconque.
- **Adresse IP locale** : cette valeur filtre la capture de paquets sur ceux dont l’adresse IP locale correspond à cette valeur de filtre.
- **Port local** : cette valeur filtre la capture de paquets sur ceux dont le port local correspond à cette valeur de filtre.
- **Adresse IP distante** : cette valeur filtre la capture de paquets sur ceux dont l’adresse IP distante correspond à cette valeur de filtre.
- **Port distant** : cette valeur filtre la capture de paquets sur ceux dont le port distant correspond à cette valeur de filtre.

> [!NOTE]
> Les valeurs d’adresse IP et de port peuvent être une valeur unique, une plage de valeurs ou un ensemble (c’est-à-dire, 80-1024 pour le port). Vous pouvez définir autant de filtres que vous le souhaitez.

Une fois les valeurs remplies, cliquez sur **OK** pour créer la capture de paquets.

![créer une capture de paquets][2]

Une fois le délai imparti défini pour la capture de paquets expiré, la capture de paquets s’arrête et peut être examinée. Vous pouvez également arrêter les sessions de capture de paquets manuellement.

## <a name="delete-a-packet-capture"></a>Supprimer une capture de paquets

Dans la vue de capture de paquets, cliquez sur le **menu contextuel** (...) ou cliquez avec le bouton droit, puis cliquez sur **Supprimer** pour arrêter la capture de paquets.

![supprimer une capture de paquets][3]

> [!NOTE]
> La suppression d’une capture de paquets ne supprime pas le fichier dans le compte de stockage.

Vous êtes invité à confirmer la suppression de la capture de paquets, cliquez sur **Oui**.

![confirmation][4]

## <a name="stop-a-packet-capture"></a>Arrêter une capture de paquets

Dans la vue de capture de paquets, cliquez sur le **menu contextuel** (...) ou cliquez avec le bouton droit, puis cliquez sur **Arrêter** pour arrêter la capture de paquets.

## <a name="download-a-packet-capture"></a>Télécharger une capture de paquets

Une fois votre session de capture de paquets terminée, le fichier de capture est téléchargé vers le stockage Blob ou dans un fichier local sur la machine virtuelle. L’emplacement de stockage de la capture de paquets est défini lors de la création de la session. L’explorateur de stockage Microsoft Azure est un outil très pratique pour accéder à ces fichiers de capture enregistrés dans un compte de stockage. Vous pouvez le télécharger ici : http://storageexplorer.com/

Si un compte de stockage est spécifié, les fichiers de capture de paquets sont enregistrés dans un compte de stockage à l’emplacement suivant :
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en consultant [Create an alert triggered packet capture (Créer une capture de paquets déclenchée par alerte)](network-watcher-alert-triggered-packet-capture.md)

Recherchez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en consultant [Check IP flow verify (Vérifier les flux IP)](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













