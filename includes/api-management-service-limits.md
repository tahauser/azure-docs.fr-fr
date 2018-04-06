---
title: Fichier Include
description: Fichier Include
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
| Ressource | Limite |
| --- | --- |
| Unités d'échelle | 10 par région<sup>1</sup> |
| Cache | 5 Go par unité<sup>1</sup> |
| Les connexions simultanées principales<sup>2</sup> par autorité HTTP | 2 048 par unité<sup>3</sup> |
| Taille maximale de la réponse mise en cache | 10 Mo |
| Maximum de domaines de passerelle personnalisés | 20 par instance de service<sup>4</sup> |


<sup>1</sup>Les limites d'API Management sont différentes pour chaque niveau de tarification. Pour consulter les niveaux de tarification et leurs limites de mise à l'échelle, consultez [Tarification de la gestion des API](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> Les connexions sont regroupées et réutilisées, sauf si elles sont explicitement fermées par le serveur principal.
<sup>3</sup> Par unité des niveaux De base, Standard et Premium. Le niveau développeur est limité à 1 024.
<sup>4</sup> Disponible pour le niveau Premium uniquement.


