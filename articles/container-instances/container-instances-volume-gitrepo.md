---
title: Monter un volume GitRepo dans Azure Container Instances
description: "Découvrez comment monter un volume GitRepo pour cloner un référentiel Git dans vos instances de conteneurs"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Monter un volume GitRepo dans Azure Container Instances

Découvrez comment monter un volume *GitRepo* pour cloner un référentiel Git dans vos instances de conteneurs.

> [!NOTE]
> Le montage d’un volume *GitRepo* est actuellement limité aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>Volumes GitRepo

Le volume *GitRepo* monte un répertoire et clone le référentiel Git spécifié dans celui-ci au démarrage du conteneur. L’utilisation d’un volume *GitRepo* dans vos instances de conteneur permet d’éviter d’ajouter du code à cette fin dans vos applications.

Lorsque vous montez un volume *GitRepo*, vous pouvez définir trois propriétés pour le configurer :

| Propriété | Obligatoire | Description |
| -------- | -------- | ----------- |
| `repository` | OUI | URL complète incluant `http://` ou `https://` du référentiel Git à cloner.|
| `directory` | Non  | Répertoire dans lequel le référentiel doit être cloné. Le chemin d’accès ne peut pas contenir ou commencer par « `..` ».  Si vous spécifiez « `.` », le référentiel est cloné dans le répertoire du volume. Autrement, le référentiel Git est cloné dans un sous-répertoire du nom spécifié à l’intérieur du répertoire de volume. |
| `revision` | Non  | Hachage de validation de la révision à cloner. À défaut de spécification, la révision `HEAD` est clonée. |

## <a name="mount-a-gitrepo-volume"></a>Monter un volume GitRepo

Pour monter un volume *GitRepo* dans une instance de conteneur, vous devez opérer le déploiement à l’aide d’un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Tout d’abord, remplissez le tableau `volumes` dans la section `properties` du groupe de conteneurs du modèle. Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter le volume *GitRepo*, remplissez le tableau `volumeMounts` dans la section  `properties` de la définition de conteneur.

Par exemple, le modèle Resource Manager suivant crée un groupe de conteneurs consistant en un seul conteneur. Le conteneur clone deux référentiels GitHub spécifiés par les blocs de volume *GitRepo*. Le deuxième volume inclut des propriétés supplémentaires spécifiant un répertoire vers lequel cloner, ainsi que le hachage de validation d’une révision spécifique à cloner.

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

La structure ainsi obtenue de répertoire des deux référentiels clonés définis dans le modèle précédent est la suivante :

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Pour voir un exemple de déploiement d’instance de conteneur avec un modèle Azure Resource Manager, consultez [Déployer des groupes de plusieurs conteneurs dans Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un partage de fichiers Azure dans Azure Container Instances](container-instances-volume-azure-files.md)
* [Monter un volume emptyDir dans Azure Container Instances](container-instances-volume-emptydir.md)
* [Monter un volume secret dans Azure Container Instances](container-instances-volume-secret.md)
