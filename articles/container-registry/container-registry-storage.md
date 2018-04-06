---
title: Stockage des images dans Azure Container Registry
description: Plus d’informations sur comment vos images conteneur Docker sont stockées dans Azure Container Registry, y compris la sécurité, de redondance et la capacité.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: df46712889a3eba54f1a2288ba93c82b21b92deb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="container-image-storage-in-azure-container-registry"></a>Stockage des images conteneur dans Azure Container Registry

Chaque registre de conteneurs Azure [de base, standard et Premium](container-registry-skus.md) bénéficie de fonctionnalités de stockage Azure avancées, tels que le chiffrement au repos pour la sécurité des données image et la géo-redondance pour la protection des données image. Les sections suivantes décrivent les fonctionnalités et les limites du stockage des images dans Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Chiffrement des données au repos

Toutes les images conteneur dans votre registre sont chiffrées au repos. Azure chiffre automatiquement une image avant de la stocker et la déchiffre à la volée lorsque vous ou vos applications et services extraient l’image.

## <a name="geo-redundant-storage"></a>Stockage géo-redondant

Azure utilise un schéma de stockage géo-redondant pour vous prémunir contre la perte de vos images conteneur. Azure Container Registry réplique automatiquement vos images conteneur dans plusieurs centres de données géographiquement distants, empêchant leur perte en cas de défaillance du stockage régional.

## <a name="geo-replication"></a>Géoréplication

Pour les scénarios nécessitant une assurance de plus haute disponibilité, envisagez d’utiliser la fonctionnalité [géo-réplication](container-registry-geo-replication.md) des registres Premium. La géo-réplication permet de se prémunir contre la perte d’accès à votre registre en cas d’un échec régional *total* et pas seulement un échec de stockage. La géo-réplication offre également d’autres avantages, comme le stockage des images proches du réseau pour les envois et extraits les plus rapides dans des scénarios de développement ou de déploiement distribués.

## <a name="image-limits"></a>Limites de l'image

La table suivante décrit l’image conteneur et les limites de stockage en place pour les registres de conteneurs Azure.

| Ressource | Limite |
| -------- | :---- |
| Référentiels | Aucune limite |
| Images | Aucune limite |
| Calques | Aucune limite |
| Balises | Aucune limite|
| Stockage | 5 To |

Un très grand nombre de balises et de référentiels peuvent affecter les performances de votre registre. Supprimez régulièrement les référentiels non utilisés, les balises et les images à l’aide de [Azure CLI](/cli/azure/acr), l’ACR [l’API REST](/rest/api/containerregistry/) ou le [portail Azure][portal] en tant que partie de votre routine de maintenance du registre. Les ressources de registre supprimées telles que des référentiels, des images et des balises ne *peuvent pas* être récupérées après suppression.

## <a name="storage-cost"></a>Coût de stockage

Pour plus d’informations sur la tarification, consultez [tarification d’Azure Container Registry ][pricing].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différentes références SKU Azure Container Registry (de base, standard, Premium), consultez [Références SKU Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
