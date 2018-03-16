---
title: "Gérer le service Protection DDos Standard Azure à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser les données de télémétrie du service Protection DDos Standard Azure dans Azure Monitor pour atténuer une attaque."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6a5ab1ba44197c0103e1e7d353a116dc01dfc163
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gérer le service Protection DDos Standard Azure à l’aide du portail Azure

Découvrez comment activer et désactiver la protection contre les attaques par déni de service distribué (DDoS), et utiliser la télémétrie pour atténuer ce type d’attaque, grâce au service Protection DDos Standard Azure. Ce service protège les ressources Azure, telles que les machines virtuelles, les équilibreurs de charge et les passerelles d’application auxquels une [adresse IP publique](virtual-network-public-ip-address.md) a été affectée. Pour en savoir plus sur le service Protection DDos Standard et ses fonctionnalités, voir [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md). 

>[!IMPORTANT]
>Le service Protection DDos Standard Azure (Protection DDos) est disponible en préversion. Un nombre limité de ressources Azure prend en charge le service Protection DDos et ce, dans un nombre limité de régions. Pour obtenir la liste des régions disponibles, voir [Vue d’ensemble du service Protection DDos Standard](ddos-protection-overview.md). Vous devez [vous inscrire au service](http://aka.ms/ddosprotection) pendant la préversion limitée afin que le service Protection DDos soit activé pour votre abonnement. L’équipe Azure DDoS vous contacte après l’inscription, pour vous guider tout au long du processus d’activation. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Activer le service Protection DDos Standard : nouveau réseau virtuel

1. Connectez-vous au Portail Azure à l’adresse http://portal.azure.com. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
2. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
3. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
4. Créer un réseau virtuel avec les paramètres choisis. Pour en savoir plus sur la création de réseaux virtuels, consultez la rubrique [Créer un réseau virtuel](manage-virtual-network.md#create-a-virtual-network). Sous **Protection DDoS**, cliquez sur **Activé**, puis cliquez sur **Créer**. Si vous ne voyez pas **Protection DDoS**, la cause probable en est que votre abonnement n’est pas inscrit pour la fonctionnalité. Vous devez effectuer [l’inscription](http://aka.ms/ddosprotection) et recevoir une notification que votre abonnement a été activé pour la fonctionnalité avant que **Protection DDoS** apparaisse.

    ![Création d’un réseau virtuel](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > Lorsque vous sélectionnez une région, choisissez-en une qui soit prise en charge dans la liste de la section [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md). Si vous ne sélectionnez pas une région prise en charge, la création du réseau virtuel échoue.

    Un avertissement indique que l’activation du service Protection DDos entraîne des frais. Aucun frais lié au service Protection DDos n’est facturé pendant la préversion. Des frais s’appliquent au moment de la disponibilité générale. Les clients sont avertis 30 jours à l’avance de l’application de frais et de la disponibilité générale.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Activer le service Protection DDos Standard - Réseau virtuel existant 

1. Cliquez sur **Réseaux virtuels** dans le menu du portail Azure, puis sélectionnez votre réseau virtuel.
2. Cliquez sur **Protection DDoS**, cliquez sur **Activé** dans l’écran *Protection DDoS*, puis cliquez sur **Enregistrer**. Si vous ne voyez pas **Protection DDoS**, la cause probable en est que votre abonnement n’est pas inscrit pour la fonctionnalité. Vous devez effectuer [l’inscription](http://aka.ms/ddosprotection) et recevoir une notification que votre abonnement a été activé pour la fonctionnalité avant que **Protection DDoS** apparaisse. 

    > [!WARNING]
    > Le réseau virtuel doit exister dans une région prise en charge. Pour obtenir la liste des régions prises en charge, voir [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md).

    Un avertissement indique que l’activation du service Protection DDos entraîne des frais. Aucun frais lié au service Protection DDos n’est facturé pendant la préversion. Des frais s’appliquent au moment de la disponibilité générale. Les clients sont avertis 30 jours à l’avance de l’application de frais et de la disponibilité générale.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Désactiver le service Protection DDos sur un réseau virtuel

1. Cliquez sur **Réseaux virtuels** dans le menu du portail Azure, puis sélectionnez votre réseau virtuel.
2. Cliquez sur **Protection DDoS**, cliquez sur **Désactivé** dans l’écran *Protection DDoS*, puis cliquez sur **Enregistrer**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurer des alertes pour les métriques du service Protection DDos

Vous pouvez sélectionner les métriques du service Protection DDos disponibles de votre choix pour être averti quand il existe une atténuation active pendant une attaque à l’aide de la configuration des alertes Azure Monitor. Quand les conditions sont remplies, vous recevez un e-mail d’alerte à l’adresse spécifiée.

1. Cliquez sur **Surveiller**, puis cliquez sur **Métriques**.
2. Dans l’écran *Métriques*, sélectionnez le groupe de ressources, le type de ressource **Adresse IP publique** et votre adresse IP publique Azure.
3. Pour configurer une alerte par e-mail pour une métrique, cliquez sur **Ajouter une alerte Métrique**. Vous pouvez créer une alerte par e-mail sur n’importe quelle métrique, mais la métrique la plus évidente est **Sous attaque DDoS ou non**. Il s’agit d’une valeur booléenne 1 ou 0. **1** signifie que vous faites l’objet d’une attaque. **0** signifie que vous ne faites pas l’objet d’une attaque.
4. Pour recevoir un e-mail quand vous faites l’objet d’une attaque, définissez la métrique **Sous attaque DDoS ou non** et **Condition de supériorité à zéro (0) au cours des 5 dernières minutes**. Vous pouvez configurer des alertes similaires pour les autres métriques.

    ![Configurer les métriques](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Dans les quelques minutes qui suivent la détection d’une attaque, vous êtes informé grâce aux métriques Azure Monitor.

    ![Alerte en cas d’attaque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Vous pouvez également découvrir plus en détail la [configuration de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) et les [applications logiques](../logic-apps/logic-apps-overview.md) pour créer des alertes.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Configurer la journalisation des métriques du service Protection DDos Standard

1. Cliquez sur **Surveiller**, puis sur **Paramètres de diagnostic**.
2. Dans l’écran *Métriques*, sélectionnez le groupe de ressources, le type de ressource **Adresse IP publique** et votre adresse IP publique Azure.
3. Cliquez sur **Activer les diagnostics pour collecter les données suivantes**.

Vous disposez de trois options pour la journalisation :

- **Archiver dans un compte de stockage** : écrit les journaux dans un compte de stockage.
- **Diffuser vers Event Hub** : permet à un récepteur de journal de sélectionner les journaux à l’aide d’un hub d’événements. Cette option permet l’intégration à Splunk ou à d’autres systèmes SIEM.
- **Envoyer à Log Analytics** : écrit les journaux dans le service Azure OMS Log Analytics.

## <a name="use-ddos-protection-telemetry"></a>Utiliser les données de télémétrie du service Protection DDos

Les données de télémétrie pour une attaque sont fournies par le biais d’Azure Monitor en temps réel. Les données de télémétrie ne sont disponibles que pendant l’atténuation dont fait l’objet une adresse IP publique. Vous ne les voyez pas avant ou après l’atténuation d’une attaque.

1. Cliquez sur **Surveiller**, puis cliquez sur **Métriques**. 
2. Dans l’écran *Métriques*, sélectionnez le groupe de ressources, le type de ressource **Adresse IP publique** et votre adresse IP publique Azure. Une série de **métriques disponibles** s’affiche sur le côté gauche de l’écran. Quand elles sont sélectionnées, ces métriques sont représentées dans le **graphique des métriques Azure Monitor** sur l’écran de vue d’ensemble. 

Les noms des métriques présentent différents types de paquets et les octets/paquets, et les noms de balise sur chaque métrique sont essentiellement construits comme suit :

- **Nom de balise des paquets transférés (par exemple, Paquets entrants ignorés DDoS)** : nombre de paquets supprimés/nettoyés par le système de protection DDoS.
- **Nom de balise des paquets transférés (par exemple, Paquets entrants transférés DDoS) :** nombre de paquets transférés par le système DDoS vers l’adresse IP virtuelle de destination (trafic qui n’a pas été filtré).
- **Nom de balise du nombre de paquets (par exemple, Paquets entrants DDoS) :** nombre total de paquets ayant atteint le système de nettoyage, représentant la somme des paquets supprimés et transférés.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)