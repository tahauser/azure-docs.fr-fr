---
title: "Utilisation d’Azure DNS pour les domaines privés | Microsoft Docs"
description: "Vue d’ensemble du service d’hébergement DNS privé sur Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>Utilisation d’Azure DNS pour les domaines privés
Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de service en une adresse IP. Azure DNS est un service d’hébergement pour les domaines DNS qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure.  En plus des domaines DNS accessibles sur Internet, Azure DNS prend désormais également en charge les domaines DNS privés en tant que fonctionnalité en préversion.  
 
Azure DNS fournit un service DNS fiable et sécurisé pour gérer et résoudre les noms de domaine dans un réseau virtuel sans avoir à ajouter de solution DNS personnalisée. Les zones DNS privées vous permettent d’utiliser vos propres noms de domaine personnalisés au lieu des noms fournis par Azure actuellement disponibles.  L’utilisation de noms de domaine personnalisés vous aide à adapter votre architecture de réseau virtuel en fonction des besoins de votre organisation. Elle offre une résolution de noms pour les machines virtuelles au sein d’un réseau virtuel et entre plusieurs réseaux virtuels. De plus, vous pouvez configurer des noms de zones avec une vue divisée qui permet à des zones DNS publique et privée de partager le même nom.

![Vue d’ensemble de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Avantages

* **Supprime le besoin d’utiliser des solutions DNS personnalisées.** Auparavant, un grand nombre de clients devaient créer des solutions DNS personnalisées pour gérer les zones DNS dans leur réseau virtuel.  La gestion des zones DNS peut désormais s’effectuer à l’aide de l’infrastructure native d’Azure, ce qui supprime la lourde tâche de créer et gérer des solutions DNS personnalisées.

* **Prise en charge de tous les types d’enregistrements DNS courants.**  Azure DNS prend en charge les enregistrements A, AAAA, CNAME, MX, NS, PTR, SOA, SRV et TXT.

* **Gestion automatique des enregistrements de noms d’hôte.** En plus d’héberger vos enregistrements DNS personnalisés, Azure gère automatiquement les enregistrements de noms d’hôte pour les machines virtuelles dans les réseaux virtuels spécifiés.  Cela vous permet d’optimiser les noms de domaine que vous utilisez sans avoir à créer de solutions DNS personnalisées ni modifier l’application.

* **Résolution de noms d’hôte entre réseaux virtuels.** Contrairement aux noms d’hôte fournis par Azure, les zones DNS privé peuvent être partagées entre des réseaux virtuels.  Cette fonctionnalité simplifie les scénarios de détection de services et réseaux croisés, tels que l’homologation de réseaux virtuels.

* **Outils familiers et expérience utilisateur.** Pour réduire la courbe d’apprentissage, cette nouvelle offre utilise les outils Azure DNS déjà bien établis (PowerShell, modèles Resource Manager, API REST).

* **Prise en charge DNS avec découpage d’horizon.** Azure DNS permet de créer des zones portant le même nom capables de résoudre des résultats différents au sein d’un réseau virtuel et à partir de l’Internet public.  Un scénario classique de DNS avec découpage d’horizon consiste à fournir une version dédiée d’un service pour une utilisation au sein du réseau virtuel.


## <a name="pricing"></a>Tarification

Les zones DNS privé sont gratuites dans la préversion managée. Durant sa disponibilité générale, cette fonctionnalité va utiliser un modèle tarifaire basé sur l’utilisation, similaire à l’offre Azure DNS actuelle. 


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une zone DNS privée](./private-dns-getstarted-powershell.md) dans Azure DNS.

Obteniez plus d’informations sur les zones et enregistrements DNS en consultant : [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.

