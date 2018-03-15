---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
## <a name="terminology"></a>Terminologie

Une image de Place de Marché dans Azure a les attributs suivants :

* **Éditeur** : organisation qui a créé l’image. Exemples : Canonical, MicrosoftWindowsServer
* **Offre** : nom du groupe d’images associées, créé par un éditeur. Exemples : Ubuntu Server, Windows Server
* **Référence SKU** : instance d’une offre, par exemple une version majeure d’un logiciel. Exemples : 16.04-LTS, 2016-Datacenter
* **Version** : numéro de version d’une référence SKU d’image. 

Pour identifier une image de Place de Marché lorsque vous déployez une machine virtuelle par programmation, indiquez ces valeurs individuellement en tant que paramètres. Certains outils acceptent également l’*URN* de l’image. L’URN combine ces valeurs en les séparant par un caractère deux-points (:) : *Éditeur*:*Offre*:*Référence SKU*:*Version*. Dans l’URN, vous pouvez remplacer le numéro de version par la valeur « latest », qui permet de sélectionner la dernière version de l’image. 

Si l’éditeur d’images fournit des conditions de licences et d’achat supplémentaires, vous devez accepter les termes du contrat et activer le déploiement par programmation. Vous devez également indiquer les paramètres du *forfait* lors du déploiement d’une machine virtuelle par programmation. Consultez [Déployer une image avec les termes du contrat de la Place de Marché](#deploy-an-image-with-marketplace-terms).