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
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Déployer une image avec les termes du contrat de la Place de Marché

Certaines images de machine virtuelle dans la Place de Marché Azure ont des conditions de licence et d’achat supplémentaires que vous devez accepter pour pouvoir effectuer un déploiement par programmation.  

Pour déployer une machine virtuelle à partir d’une de ces images, vous devez accepter les termes du contrat de l’image et activer le déploiement par programmation. Vous ne devez effectuer cette opération qu’une seule fois pour votre abonnement. Ensuite, chaque fois que vous déployez une machine virtuelle par programmation à partir de l’image, vous devez également spécifier les paramètres du *forfait*.

Les sections suivantes décrivent diverses opérations :

* Déterminer si une image de la Place de Marché possède des conditions de licence supplémentaires 
* Accepter les termes du contrat par programmation
* Fournir des paramètres de forfait lorsque vous déployez une machine virtuelle par programmation

