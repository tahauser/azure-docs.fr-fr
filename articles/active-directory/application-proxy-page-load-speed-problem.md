---
title: "Le chargement d’une application Proxy d’application prend trop de temps | Microsoft Docs"
description: "Résolution des problèmes de performances associés au chargement de page avec le Proxy d’application Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: d02c29fa6239cb36cb5fb95da4d325cca57952aa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Le chargement d’une application Proxy d’application prend trop de temps

Cet article vous aide à comprendre pourquoi le chargement d’une application Proxy d’application peut prendre beaucoup de temps. Plusieurs solutions sont abordées pour résoudre ce problème.

## <a name="overview"></a>Vue d'ensemble
Si vos applications fonctionnent mais que vous constatez un temps de latence important, vous pouvez tenter d’apporter quelques ajustements mineurs à votre topologie de réseau pour améliorer la vitesse. Pour une évaluation des différentes topologies, consultez le [document des considérations sur la topologie du réseau](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations).

Si ces considérations ne vous aident pas à résoudre le problème, nous n’avons malheureusement pas d’autres recommandations relatives au réglage des performances. À mesure que le service Proxy d’application s’étend à des centres de données qui peuvent être plus proches de vous, il est possible que vous constatiez directement une amélioration de la latence. Pour afficher la liste complète des centres de données Azure, consultez la [page de test de la latence](http://www.azurespeed.com/Azure/Latency). 

Pour trouver les centres de données avec le service Proxy d’application, utilisez l’outil [Connector Ports Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Commentaires sur les emplacements des centres de données du Proxy d’application 
Même si certains centres de données Azure n’incluent pas encore le Proxy d’application, ils peuvent néanmoins améliorer considérablement vos temps de latence. Envoyez vos commentaires sur l’emplacement du centre de données à <aadapfeedback@microsoft.com> pour que nous puissions en tenir compte dans nos futurs développements.

Nous travaillons sur des fonctionnalités supplémentaires visant à améliorer la latence pour les locataires qui constatent actuellement des temps de latence longs. Nous ne manquerons pas de partager avec vous la documentation dès qu’elle sera disponible.

## <a name="next-steps"></a>Étapes suivantes
[Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md)
