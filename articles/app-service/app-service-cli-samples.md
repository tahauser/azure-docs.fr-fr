---
title: "Exemples d’interface de ligne de commande Azure - App Service | Microsoft Docs"
description: "Exemples d’interface de ligne de commande - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cli-samples"></a>Exemples d’interface de ligne de commande Azure

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|-|-|
|**Créer une application**||
| [Créer une application web et déployer des fichiers par FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure et y déploie un fichier à l’aide de FTP. |
| [Créer une application web et déployer le code à partir de GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure et déploie le code à partir d’un référentiel GitHub public. |
| [Créer une application web avec un déploiement continu à partir de GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure avec la publication continue à partir d’un référentiel GitHub dont vous êtes le propriétaire. |
| [Créer une application web et déployer le code à partir d’un référentiel Git](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une application web Azure et configure la transmission de code de type push à partir d’un référentiel Git local. |
| [Créer une application web et déployer le code dans un environnement intermédiaire](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une application web Azure avec un emplacement de déploiement pour les modifications de code intermédiaires. |
| [Créer une application web ASP.NET Core dans un conteneur Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure sur Linux et charge une image Docker à partir de Docker Hub. |
|**Configurer l’application**||
| [Mapper un domaine personnalisé à une application web](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure et mappe un nom de domaine personnalisé. |
| [Lier un certificat SSL personnalisé à une application web](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure et lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. |
|**Mettre à l’échelle une application**||
| [Mettre à l’échelle une application web manuellement](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une application web Azure et la met à l’échelle entre 2 instances. |
| [Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Crée deux applications web Azure dans deux régions géographiques différentes et les rend disponibles par le biais d’un point de terminaison unique à l’aide d’Azure Traffic Manager. |
|**Connecter l’application aux ressources**||
| [Connecter une application web à une instance SQL Database](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure et une instance SQL Database, puis ajoute la chaîne de connexion de base de données aux paramètres d’application. |
| [Connecter une application web à un compte de stockage](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une application web Azure et un compte de stockage, puis ajoute la chaîne de connexion de stockage aux paramètres d’application. |
| [Connecter une application web à un cache redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une application web Azure et un cache redis, puis ajoute les détails de connexion redis aux paramètres d’application. |
| [Connecter une application web à Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une application web Azure et un Cosmos DB, puis ajoute les détails de connexion Cosmos DB aux paramètres d’application. |
|**Sauvegarder et restaurer une app**||
| [Sauvegarder une app web](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une app web Azure et une sauvegarde unique pour celle-ci. |
| [Créer une sauvegarde planifiée pour une app web](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une app web Azure et une sauvegarde planifiée pour celle-ci. |
| [Restaure une application web à partir d’une sauvegarde](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Restaure une application web Azure à partir d’une sauvegarde. |
|**Surveiller l’application**||
| [Analyser une application web avec les journaux de serveur web](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une application web Azure, active sa journalisation et télécharge les journaux sur votre ordinateur local. |
| | |