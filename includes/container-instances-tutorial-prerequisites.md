---
title: Fichier Include
description: Fichier Include
services: container-instances
author: mmacy
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 95a8cf4eca7969631d069a31c9d08ba52c8b02b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Pour suivre ce didacticiel, vous devez satisfaire aux exigences suivantes :

**Azure CLI** : Azure CLI version 2.0.29 ou ultérieure doit être installée sur votre ordinateur local. Exécutez `az --version` pour trouver la version. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0][azure-cli-install].

**Docker** : ce didacticiel présuppose une compréhension de base des concepts Docker essentiels, comme les conteneurs, les images de conteneur et les commandes `docker` de base. Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker][docker-get-started].

**Moteur Docker** : pour suivre ce didacticiel, le moteur Docker doit être installé en local. Docker fournit des packages qui configurent l’environnement de Docker sur [macOS][docker-mac], [Windows][docker-windows] et [Linux][docker-linux].

> [!IMPORTANT]
> Étant donné qu’Azure Cloud Shell n’inclut pas le démon Docker, vous *devez* installer Azure CLI et le moteur Docker sur votre *ordinateur local* pour suivre ce didacticiel. Vous ne pouvez pas utiliser Azure Cloud Shell pour ce didacticiel.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli