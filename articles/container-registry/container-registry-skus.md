---
title: "Références (SKU) Azure Container Registry"
description: "Comparez les différents niveaux de service disponibles dans Azure Container Registry."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 15179fa3e3567f92a5eae69ba9a684addc3138dd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-container-registry-skus"></a>Références (SKU) Azure Container Registry

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, appelés références (SKU). Ces références SKU offrent une tarification prévisible et plusieurs options pour l’alignement en fonction de la capacité et des modèles d’utilisation de votre registre Docker privé dans Azure.

| SKU | Adresses IP gérées | Description |
| --- | :-------: | ----------- |
| **De base** | OUI | Point d’entrée au coût optimisé pour les développeurs apprenant Azure Container Registry. Les registres De base ont les mêmes fonctionnalités de programmation que les registres Standard et Premium (intégration de l’authentification Azure Active Directory, suppression d’image et webhooks). Toutefois, il existe des contraintes de taille et d’utilisation. |
| **Standard** | OUI | Les registres standard offrent les mêmes fonctionnalités que la version De base, avec des limites de stockage et un débit d’image accrus. Les registres Standard devraient satisfaire les besoins de la plupart des scénarios de production. |
| **Premium** | OUI | Les registres Premium fournissent des limites supérieures en matière de contraintes de stockage et d’opérations simultanées, ce qui permet de bénéficier de scénarios à haut volume. En plus de la capacité de débit d’image supérieure, la version Premium ajoute des fonctionnalités telles que la [géoréplication][container-registry-geo-replication] pour la gestion d’un registre parmi plusieurs régions tout en conservant un registre proche du réseau pour chaque déploiement. |
| Classique | Non  | La référence de registre Classique a permis la publication initiale du service Azure Container Registry dans Azure. Les registres classiques sont secondés par un compte de stockage crée par Azure dans votre abonnement, qui limite la capacité d’ACR à fournir des fonctionnalités de niveau supérieur telles que l’augmentation du débit et la géoréplication. En raison de ses capacités limitées, nous prévoyons de déprécier la référence Classique. |

Le choix d'une référence SKU de niveau supérieur améliore les performances et l’évolutivité. Toutefois, toutes les références SKU managées fournissent les mêmes fonctionnalités de programmation. Avec plusieurs niveaux de service, vous pouvez commencer avec un niveau basique, puis passer aux niveaux Standard et Premium au fur et à mesure que votre l’utilisation du registre augmente.

> [!NOTE]
> En raison de la dépréciation planifiée de la référence de registre Classique, nous vous recommandons d’utiliser la version De base, Standard ou Premium pour tous les nouveaux registres. Pour plus d’informations sur la conversion de votre registre Classique existant, voir [Mise à niveau d’un registre Classique][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Managé et non managé

Les références SKU de base, Standard et Premium sont collectivement appelées « registres *managés* », et les registres Classique sont qualifiés de *non managés*. La principale différence entre les deux est la façon dont sont stockées vos images de conteneur.

### <a name="managed-basic-standard-premium"></a>Managé (de base, Standard, Premium)

Les registres managés sont soutenus par un compte de stockage Azure géré par Azure. Autrement dit, le compte de stockage qui stocke vos images n’apparaît pas dans votre abonnement Azure. L’utilisation de références SKU de registre managé offre plusieurs avantages, présentés en détail dans [Mise à niveau d’un registre Classique][container-registry-upgrade]. Cet article se concentre sur les références SKU de registre managé et leurs fonctionnalités.

### <a name="unmanaged-classic"></a>Non managé (classique)

Les registres Classiques sont « non managés », dans le sens où le compte de stockage qui soutient un registre Classique se trouve dans *votre* abonnement Azure. Par conséquent, vous êtes responsable de la gestion du compte de stockage rassemblant vos images de conteneur. Avec les registres non managés, vous ne pouvez pas changer de références SKU selon vos besoins (sauf pour effectuer une [mise à niveau][container-registry-upgrade] vers un registre managé), et plusieurs fonctionnalités des registres managés ne sont pas disponibles (par exemple, la suppression d’image de conteneur, la [géoréplication][container-registry-geo-replication] et les [webhooks][container-registry-webhook]).

Pour plus d’informations sur la mise à niveau d’un registre Classique vers une des références SKU managées, voir [Mise à niveau d’un registre Classique][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Matrice de fonctionnalités des références SKU

Le tableau suivant décrit en détail les fonctionnalités et les limites des niveaux de service De base, Standard et Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Changement de référence

Vous pouvez changer la référence SKU d’un registre via l’interface de ligne de commande Azure ou dans le portail Azure. Vous pouvez changer librement de références SKU managées tant que la nouvelle référence SKU possède la capacité de stockage maximale requise. Il est impossible de revenir à une référence SKU Classique. Il s’agit d’une conversion unidirectionnelle.

### <a name="azure-cli"></a>Azure CLI

Pour changer de références SKU dans l’interface de ligne de commande Azure, utilisez la commande [az acr update][az-acr-update]. Par exemple, pour basculer vers Premium :

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portail Azure

Dans la **vue d’ensemble** du registre de conteneur dans le portail Azure, sélectionnez **Mettre à jour**, puis choisissez une nouvelle **référence SKU** dans la liste déroulante.

![Mettre à jour la référence de registre de conteneurs dans le portail Azure][update-registry-sku]

Si vous avez un registre Classique, vous ne pouvez pas sélectionner une référence SKU managée dans le portail Azure. Au lieu de cela, vous devez d’abord effectuer une [mise à niveau][container-registry-upgrade] vers un registre managé (voir [Mise à jour à partir d’une version Classique](#changing-from-classic)).

## <a name="changing-from-classic"></a>Mise à jour à partir d’une version Classique

Il existe des considérations supplémentaires à prendre en compte lors de la migration d’un registre Classique non managé vers l’une des références SKU managées de base, Standard ou Premium. Si votre registre Classique contient un grand nombre d’images et fait plusieurs gigaoctets, le processus de migration peut prendre un certain temps. En outre, les opérations `docker push` sont désactivées jusqu'à la fin de la migration.

Pour plus d’informations sur la mise à niveau d’un registre Classique vers une des références SKU managées, voir [Mettre à niveau un registre de conteneurs Classique][container-registry-upgrade].

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur la tarification de chacune des références SKU Azure Container Registry, voir [Tarification Container Registry][container-registry-pricing].

## <a name="next-steps"></a>étapes suivantes

**Feuille de route Azure Container Registry**

Consultez la [feuille de route ACR][acr-roadmap] sur GitHub pour obtenir des informations sur les fonctionnalités à venir dans le service.

**Azure Container Registry UserVoice**

Proposez et votez pour des suggestions de fonctionnalités dans [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-webhook]: container-registry-webhook.md
