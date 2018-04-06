---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

* Pour connaître la disponibilité des machines virtuelles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/en-us/regions/services/).

* Les machines virtuelles de série N ne peuvent être déployées que dans le modèle de déploiement Resource Manager.

* Les machines virtuelles de série N diffèrent en fonction du type de stockage Azure qu’elles prennent en charge pour les disques. Les machines virtuelles NC et NV ne prennent en charge que les disques de machines virtuelles sauvegardés par le Stockage sur disque Standard (HDD). Les machines virtuelles NCv2, ND, et NCv3 ne prennent en charge que des disques de machines virtuelles s’appuyant sur un Stockage sur disque Premium (SSD).

* Si vous voulez déployer plus de quelques machines virtuelles de série N, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

* Il se peut que vous deviez augmenter le quota de cœurs (par région) dans votre abonnement Azure et le quota séparé des cœurs NC, NCv2, NCv3, ND ou NV. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitement. Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.




