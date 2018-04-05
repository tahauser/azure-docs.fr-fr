---
title: Configuration et utilisation de l’émulateur de stockage avec Visual Studio | Microsoft Docs
description: Configuration et utilisation de l’émulateur de stockage avec Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: c8e7996f-6027-4762-806e-614b93131867
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: ecf9df0d94f34df69750a8baa7f5502764cf9b06
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configuration et utilisation de l’émulateur de stockage avec Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Vue d'ensemble
L’environnement de développement du Kit de développement logiciel (SDK) Azure comprend l’émulateur de stockage, un utilitaire qui simule les services de stockage d’objets blob, de files d’attente et de tables d’Azure Storage sur votre ordinateur de développement local. Si vous créez un service cloud qui utilise les services de stockage Azure ou écrivez une application externe qui appelle les services de stockage, vous pouvez tester localement votre code sur l’émulateur de stockage. Azure Tools pour Microsoft Visual Studio intègre la gestion de l’émulateur de stockage dans Visual Studio. Azure Tools initialise la base de données de l’émulateur de stockage à la première utilisation, il démarre le service de l’émulateur de stockage quand vous exécutez ou déboguez votre code dans Visual Studio, et il fournit un accès en lecture seule aux données de l’émulateur de stockage via l’Explorateur de stockage Azure.

Pour plus d’informations sur l’émulateur de stockage, y compris la configuration système requise et les instructions de configuration personnalisée, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](storage/common/storage-use-emulator.md).

> [!NOTE]
> Il existe quelques différences de fonctionnalité entre la simulation de l’émulateur de stockage et les services de stockage Azure. Pour plus d’informations sur ces différences, consultez [Différences entre l’émulateur de stockage et Azure Storage](storage/common/storage-use-emulator.md) dans la documentation du Kit de développement logiciel (SDK) Azure.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configuration d’une chaîne de connexion pour l’émulateur de stockage
Pour accéder à l’émulateur de stockage depuis du code au sein d’un rôle, vous devez configurer une chaîne de connexion qui pointe vers l’émulateur de stockage et qui pourra être modifiée ultérieurement pour pointer vers un compte de stockage Azure. Une chaîne de connexion est un paramètre de configuration que votre rôle peut lire pendant l’exécution pour se connecter à un compte de stockage. Pour plus d’informations sur la création de chaînes de connexion, consultez [Configuration d’une application Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Vous pouvez retourner une référence au compte de l’émulateur de stockage à partir de votre code à l’aide de la propriété **DevelopmentStorageAccount**. Cette approche fonctionne correctement si vous voulez accéder à l’émulateur de stockage à partir de votre code. Toutefois, si vous projetez de publier votre application dans Azure, vous devrez créer une chaîne de connexion pour accéder à votre compte de stockage et modifier votre code pour utiliser cette chaîne de connexion avant de publier l’application. Si vous passez fréquemment du compte de l’émulateur de stockage à un compte de stockage Azure, l’utilisation d’une chaîne de connexion vous permettra de simplifier ce processus.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Initialisation et exécution de l’émulateur de stockage
Il est possible de lancer automatiquement l’émulateur de stockage quand vous exécutez ou déboguez votre service dans Visual Studio. Dans l’Explorateur de solutions, ouvrez le menu contextuel de votre projet **Azure**, puis choisissez **Propriétés**. Sous l’onglet **Développement**, dans la liste **Démarrer l’émulateur de stockage Microsoft Azure**, choisissez **Vrai** (si cette valeur n’est pas déjà définie).

La première fois que vous exécutez ou déboguez votre service à partir de Visual Studio, l’émulateur de stockage lance un processus d’initialisation. Ce processus réserve des ports locaux pour l’émulateur de stockage et crée la base de données de l’émulateur de stockage. Une fois terminé, ce processus n’aura pas besoin d’être réexécuté, à moins que la base de données de l’émulateur de stockage ne soit supprimée.

> [!NOTE]
> À compter de la version de juin 2012 d’Azure Tools, l’émulateur de stockage est exécuté par défaut dans SQL Express LocalDB. Dans les versions antérieures d’Azure Tools, l’émulateur de stockage est exécuté sur une instance par défaut de SQL Express 2005 ou 2008, que vous devez installer avant de pouvoir installer le Kit de développement logiciel (SDK) Azure. Vous pouvez également exécuter l’émulateur de stockage sur une instance nommée de SQL Express ou sur une instance nommée ou par défaut de Microsoft SQL Server. Si vous avez besoin de configurer l’émulateur de stockage pour qu’il soit exécuté sur une instance autre que celle par défaut, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](storage/common/storage-use-emulator.md).
> 
> 

L’émulateur de stockage fournit une interface utilisateur qui permet de voir l’état des services de stockage local, ainsi que de les démarrer, arrêter et réinitialiser. Une fois que le service d’émulateur de stockage a été démarré, vous pouvez afficher l’interface utilisateur et démarrer ou arrêter le service en double-cliquant sur l’icône de zone de notification de l’émulateur Microsoft Azure dans la barre des tâches Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Affichage des données de l’émulateur de stockage dans l’Explorateur de serveurs
Le nœud Azure Storage dans l’Explorateur de serveurs permet d’afficher des données et de modifier les paramètres des données d’objets blob et de tables de vos comptes de stockage, y compris celui de l’émulateur de stockage. Consultez [Gérer les ressources Stockage Blob Azure avec l’explorateur de stockage (préversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) pour plus d’informations.

