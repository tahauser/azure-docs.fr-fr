---
title: "Analyse des modèles d’utilisation CDN Azure | Microsoft Docs"
description: "Le client peut activer l’analyse des journaux pour Azure CDN."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: ce9ec021250a3548e23ad87273a9225cdf700c70
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analyse des modèles d’utilisation CDN Azure

Après avoir activé le CDN pour votre application, vous pouvez surveiller l’utilisation du CDN, vérifier l’intégrité de votre application et corriger les éventuels problèmes. Azure CDN fournit ces fonctionnalités sous les formes suivantes : 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analytique principale via les journaux de diagnostic Azure

L’analytique principale est disponible pour tous les points de terminaison CDN appartenant à des profils CDN Verizon (Standard et Premium) et Akamai (Standard). Les journaux de diagnostics Azure permettent d’exporter l’analytique principale vers un stockage Azure, des hubs d’événements ou Operations Management Suite (OMS) Log Analytics. OMS Log Analytics offre une solution avec des graphiques configurables par l’utilisateur et personnalisables. Pour plus d’informations, consultez [Journaux de diagnostic Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Rapports principaux de Verizon

En tant qu’utilisateur d’Azure CDN avec un profil Standard ou Premium Verizon, vous pouvez afficher les rapports principaux de Verizon dans le portail supplémentaire Verizon. Les rapports principaux de Verizon sont accessibles par le biais de l’option **Gérer** du portail Azure. Ils offrent toute une variété de graphiques et de vues. Pour plus d’informations, consultez [Rapports principaux de Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Rapports personnalisés de Verizon

En tant qu’utilisateur d’Azure CDN avec un profil Standard ou Premium Verizon, vous pouvez afficher les rapports personnalisés de Verizon dans le portail supplémentaire Verizon. Les rapports personnalisés de Verizon sont accessibles par le biais de l’option **Gérer** du portail Azure. La page des rapports personnalisés de Verizon indique le nombre d’accès ou les données transférées pour chaque CName de périphérie appartenant à un profil Azure CDN. Les données peuvent être groupées par code de réponse HTTP ou état du cache sur n’importe quelle période. Pour plus d’informations, consultez [Rapports personnalisés de Verizon](cdn-verizon-custom-reports.md).

## <a name="verizon-premium-reports"></a>Rapports Premium de Verizon

Avec **Azure CDN Premium de Verizon**, vous pouvez également accéder aux rapports suivants :
   * [Rapports HTTP avancés](cdn-advanced-http-reports.md)
   * [Statistiques en temps réel](cdn-real-time-stats.md)
   * [Performances de nœuds Edge](cdn-edge-performance.md)

