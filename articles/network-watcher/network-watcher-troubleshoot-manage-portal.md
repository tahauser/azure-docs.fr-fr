---
title: "Résoudre les problèmes liés à la passerelle de réseau virtuel et aux connexions Azure - Portail | Microsoft Docs"
description: "Cette page explique comment utiliser Azure Network Watcher pour résoudre des problèmes liés au portail"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: fb8f60eea0eaa24649f584ae4183aa8e552a433e
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>Résoudre des problèmes liés à la passerelle de réseau virtuel et aux connexions à l’aide de Network Watcher depuis le portail Azure

> [!div class="op_single_selector"]
> - [Portail](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Le service Network Watcher offre de nombreuses fonctionnalités en lien avec la bonne compréhension de vos ressources réseau dans Azure. Il permet notamment de résoudre les problèmes liés aux ressources. Vous pouvez appeler la solution de résolution des problèmes de ressources par le biais du portail, de PowerShell, de l’interface de ligne de commande ou de l’API REST. Lorsque cette fonctionnalité est appelée, Network Watcher inspecte l’intégrité d’une passerelle de réseau virtuel ou d’une connexion et renvoie ses résultats.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher.

Vous trouverez la liste des types de passerelles pris en charge sur la page [Types de passerelles pris en charge](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Vue d’ensemble

La résolution des problèmes liés aux ressources offre la possibilité de résoudre les problèmes qui surviennent avec les passerelles de réseau virtuel et les connexions. Lorsqu’une demande de résolution des problèmes liés aux ressources est faite, les journaux sont interrogés et inspectés. Lorsque l’inspection est terminée, les résultats sont renvoyés. Les demandes de résolution des problèmes liés aux ressources sont longues : vous devrez peut-être patienter plusieurs minutes avant d’obtenir un résultat. Les journaux de dépannage sont stockés dans un conteneur sur un compte de stockage spécifié.

## <a name="troubleshoot-a-gateway-or-connection"></a>Résoudre les problèmes de passerelle ou de connexion

1. Accédez au [Portail Azure](https://portal.azure.com) et cliquez sur **Mise en réseau** > **Network Watcher** > **Diagnostics VPN**.
2. Sélectionnez un **Abonnement**, un **Groupe de ressources** et un **Emplacement**.
3. La solution de résolution des problèmes de ressources retourne des données sur l’intégrité de la ressource. Elle enregistre également les journaux concernés sur un compte de stockage à passer en revue. Cliquez sur **Compte de stockage** pour sélectionner un compte de stockage.
4. Sur le panneau **Comptes de stockage**, sélectionnez un compte de stockage existant ou cliquez sur **+ Compte de stockage** pour en créer un.
5. Sur le panneau **Conteneurs**, choisissez un conteneur existant ou cliquez sur **+ Conteneur** pour en créer un. Lorsque vous avez terminé, cliquez sur **Sélectionner**.
6. Sélectionnez les ressources de passerelle et de connexion à dépanner et cliquez sur **Démarrer la résolution des problèmes**.

Si plusieurs ressources sont sélectionnées, la résolution des problèmes s’exécute simultanément sur les ressources de passerelle. Elle ne peut pas s’exécuter en même temps sur une connexion et sur la passerelle associée. Il est recommandé de commencer par dépanner les passerelles, car la résolution des problèmes de connexion est un processus plus long. Pendant l’exécution des diagnostics VPN sur une ressource, la colonne **ÉTAT DE RÉSOLUTION DES PROBLÈMES** affiche l’état **En cours d’exécution**

À la fin, la colonne d’état passe à **Sain** ou à **Défectueux**.

![résolution des problèmes terminée][2]

## <a name="understanding-the-results"></a>Compréhension des résultats

Dans la section **Détails** de la fenêtre, l’onglet **État** affiche l’état de la dernière exécution de la résolution des problèmes sur la ressource sélectionnée. Les résultats du dernier diagnostic s’affichent xx minutes après la dernière exécution.

|Propriété  |Description  |
|---------|---------|
|Ressource     | Lien vers la ressource.        |
|Chemin de stockage     |  Chemin d’accès du compte de stockage et du conteneur qui contiennent les journaux (si produits pendant l’exécution). Ce paramètre n’est pas conservé une fois que vous avez quitté le portail.        |
|Résumé     | Résumé de l’intégrité de la ressource.        |
|Détails     | Informations détaillées sur l’intégrité de la ressource.        |
|Dernière exécution     | Heure d’exécution de la dernière résolution des problèmes.        |


L’onglet **Action** offre une aide générale sur la résolution du problème. Si une action peut être entreprise pour résoudre le problème, un lien est fourni avec des indications supplémentaires. En l’absence d’indications supplémentaires, la réponse fournit l’URL permettant d’ouvrir un dossier de support.  Pour plus d’informations sur les propriétés de la réponse et sur ce qu’elle contient, consultez [Network Watcher Troubleshoot overview (Vue d’ensemble de la résolution des problèmes Network Watcher)](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>étapes suivantes

Si les paramètres ont été modifiés et arrêtent la connectivité VPN, consultez la page [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) afin d’effectuer le suivi du groupe de sécurité réseau et des règles de sécurité pouvant être concernés.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
