---
title: Monter un volume secret dans Azure Container Instances
description: "Découvrez comment monter un volume secret pour stocker des informations sensibles et y accéder à partir de vos instances de conteneur"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 6f8e1b6faac11b668a143f8013a198831a428c51
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Monter un volume secret dans Azure Container Instances

Découvrez comment monter un volume *secret* dans vos instances de conteneur pour le stockage et la récupération des informations sensibles par les conteneurs de vos groupes de conteneurs.

> [!NOTE]
> Le montage d’un volume *secret* est actuellement limité aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="secret-volume"></a>volume secret

Vous pouvez utiliser un volume *secret* pour fournir des informations sensibles aux conteneurs d’un groupe de conteneurs. Le volume *secret* stocke vos secrets spécifiés dans les fichiers du volume, auxquels les conteneurs de votre groupe de conteneurs peuvent accéder. Grâce aux secrets d’un volume *secret*, vous pouvez éviter de placer des données sensibles telles que les clés SSH ou les informations d’identification sur une base de données dans votre code d’application.

Tous les volumes *secrets* sont sauvegardés par [tmpfs][tmpfs], un système de fichiers reposant sur la RAM ; leur contenu n’est jamais écrit sur un stockage non volatile.

## <a name="mount-a-secret-volume"></a>Monter un volume secret

Pour monter un volume *secret* dans une instance de conteneur, vous devez opérer le déploiement à l’aide d’un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Tout d’abord, remplissez le tableau `volumes` dans la section `properties` du groupe de conteneurs du modèle. Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter le volume *secret*, remplissez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur.

Par exemple, le modèle Resource Manager suivant crée un groupe de conteneurs consistant en un seul conteneur. Le conteneur monte un volume *secret* constitué de deux secrets codés en Base64.

[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Pour voir un exemple de déploiement d’instance de conteneur avec un modèle Azure Resource Manager, consultez [Déployer des groupes de plusieurs conteneurs dans Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un partage de fichiers Azure dans Azure Container Instances](container-instances-volume-azure-files.md)
* [Monter un volume emptyDir dans Azure Container Instances](container-instances-volume-emptydir.md)
* [Monter un volume gitRepo dans Azure Container Instances](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs