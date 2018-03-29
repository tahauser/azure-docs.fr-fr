---
title: Fichier Include
description: Fichier Include
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
| Ressource | De base | standard | Premium |
|---|---|---|---|---|
| Stockage | 10 Go | 100 Go| 500 Go |
| ReadOps par minute<sup>1, 2</sup> | 1 000 | 3000 | 10000 |
| WriteOps par minute<sup>1, 3</sup> | 100 | 500 | 2000 |
| Bande passante de téléchargement en Mbits/s<sup>1</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Géoréplication | N/A | N/A | [Prise en charge *(préversion)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> Les valeurs *ReadOps*, *WriteOps* et de *bande passante* sont des estimations minimales. ACR s’efforce d’améliorer les performances en fonction de l’utilisation requise.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, plus la récupération du manifeste.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.