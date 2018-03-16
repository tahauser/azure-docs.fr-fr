---
title: "Vue d’ensemble d’Azure Cloud Shell | Microsoft Docs"
description: "Vue d’ensemble d’Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 72338a4c168b4d3b7c918fbb16758724f73fefc2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-azure-cloud-shell"></a>Vue d’ensemble d’Azure Cloud Shell
Azure Cloud Shell est un shell interactif, accessible par navigateur pour la gestion des ressources Azure.
Il vous donne la possibilité de choisir l’expérience d’interpréteur de commandes la plus adaptée à votre façon de travailler.
Les utilisateurs Linux peuvent choisir une expérience Bash, et les utilisateurs Windows l’option PowerShell.

Essayez à partir de shell.azure.com en cliquant sur ce bouton.

[![](https://shell.azure.com/images/launchcloudshell.png "Lancer Azure Cloud Shell")](https://shell.azure.com)

Essayez à partir du Portail Azure en cliquant sur l’icône Cloud Shell.

![Lancement du portail](media/overview/portal-launch-icon.png)

## <a name="features"></a>Caractéristiques
### <a name="browser-based-shell-experience"></a>Expérience shell basée sur navigateur
Cloud Shell permet d’accéder à une expérience de ligne de commande basée sur navigateur avec les tâches de gestion Azure à l’esprit.
Exploitez Cloud Shell pour travailler librement à partir d’une machine locale d’une façon que seul le cloud peut fournir.

### <a name="choice-of-preferred-shell-experience"></a>Choix de votre expérience d’interpréteur de commandes préféré
Les utilisateurs Linux peuvent utiliser Bash dans Cloud Shell, et les utilisateurs Windows, PowerShell dans Cloud Shell (préversion) dans la liste déroulante Shell.

![Bash dans Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell dans Cloud Shell (préversion)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Station de travail Azure configurée et authentifiée
Service managé par Microsoft, Cloud Shell est livré avec des outils en ligne de commande usuels et une prise en charge de langages. De plus, il s’authentifie automatiquement de façon sécurisée pour garantir un accès immédiat aux ressources par l’intermédiaire d’Azure CLI 2.0 ou des cmdlets Azure PowerShell.

Affichez la liste complète des outils pour les expériences [Bash](features.md#tools) et [PowerShell (préversion).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Plusieurs points d’accès
Cloud Shell est un outil flexible qui s’utilise à partir de :
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentation « Essayez-le » d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Application mobile Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extension de compte Azure VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Connecter votre stockage Microsoft Azure Files
Les machines Cloud Shell, temporaires, ont besoin qu’un partage Azure Files soit monté en tant que `clouddrive` pour pouvoir conserver les fichiers.

Lors du premier lancement, Cloud Shell vous invite à créer un groupe de ressources, un compte de stockage et un partage Azure Files en votre nom. Il s’agit d’une étape unique, et ces ressources sont automatiquement jointes pour toutes les sessions. Un partage de fichiers unique peut être mappé, puis utilisé par Bash et PowerShell dans Cloud Shell (préversion).

#### <a name="create-new-storage"></a>Créer un stockage
![](media/overview/basic-storage.png)

Un compte de stockage localement redondant (LRS) et un partage Azure Files peuvent être créés en votre nom. Le partage Azure Files servira pour les environnements Bash et PowerShell si vous choisissez d’utiliser les deux. Les coûts de stockage standard s’appliquent.

Trois ressources sont créées en votre nom :
1. Groupe de ressources nommé : `cloud-shell-storage-<region>`
2. Compte de stockage nommé : `cs<uniqueGuid>`
3. Partage de fichiers nommé : `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash dans Cloud Shell crée également une image de disque de 5 Go par défaut pour rendre `$Home` persistant. Tous les fichiers figurant dans votre répertoire $Home, tels que les clés SSH, sont conservés dans l’image de disque utilisateur stockée dans votre partage de fichiers Azure monté. Appliquez les meilleures pratiques lors de l’enregistrement des fichiers dans votre répertoire $Home et le partage de fichiers Azure monté.

#### <a name="use-existing-resources"></a>Utiliser les ressources existantes
![](media/overview/advanced-storage.png)

Vous disposez d’une option avancée qui permet d’associer des ressources existantes à Cloud Shell.
À l’invite de configuration du stockage, cliquez sur « Afficher les paramètres avancés » pour afficher des options supplémentaires.

> [!Note]
> Les listes déroulantes sont filtrées pour la région Cloud Shell qui vous a été préattribuée et pour les comptes de stockage LRS/GRS/ZRS.

[Découvrez le stockage Cloud Shell, la mise à jour des partages de fichiers Azure et le chargement/téléchargement de fichiers.](persisting-shell-storage.md)

## <a name="concepts"></a>Concepts
* Cloud Shell s’exécute sur un hôte temporaire fourni par session et par utilisateur
* Cloud Shell expire après 20 minutes sans activité interactive
* Cloud Shell requiert qu’un partage de fichiers Azure soit monté
* Cloud Shell utilise le même partage de fichiers Azure pour Bash et PowerShell
* Cloud Shell est affecté à une machine par compte d’utilisateur
* Bash rend $Home persistant en conservant une image de 5 Go dans votre partage de fichiers
* Les autorisations sont définies en tant qu’utilisateur Linux standard dans Bash

En savoir plus sur les fonctionnalités de [Bash dans Cloud Shell](features.md) et [PowerShell dans Cloud Shell (préversion)](features-powershell.md).

## <a name="pricing"></a>Tarifs
La machine qui héberge Cloud Shell est gratuite, avec comme condition préalable le montage d’un partage Azure Files. Les coûts de stockage standard s’appliquent.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Bash dans Cloud Shell](quickstart.md) <br>
[Démarrage rapide de PowerShell dans Cloud Shell (préversion)](quickstart-powershell.md)