---
title: Configurer un projet de service cloud Azure avec Visual Studio | Microsoft Docs
description: Découvrez comment configurer un projet de service cloud Azure dans Visual Studio selon vos spécifications pour ce projet.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: 1afab86e920c66459e08190f922d9439c2310aef
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurer un projet de service cloud Azure avec Visual Studio
Vous pouvez configurer un projet de service cloud Azure selon vos spécifications pour ce projet. Vous pouvez définir des propriétés pour le projet pour les catégories suivantes :

- **Publier un service cloud sur Azure** - Vous pouvez définir une propriété pour vous assurer qu’un service cloud existant déployé sur Azure ne puisse pas être effacé accidentellement.
- **Exécuter ou déboguer un service cloud sur l’ordinateur local** - Vous pouvez sélectionner une configuration de service à utiliser et indiquer si vous voulez démarrer l’émulateur de stockage Azure.
- **Valider un package de service cloud lors de sa création** - Vous pouvez décider de traiter tous les avertissements comme des erreurs, afin de vous assurer que le package de service cloud se déploie sans problèmes. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Étapes de configuration d’un projet de service cloud Azure
1. Ouvrir ou créer un projet de service cloud dans Visual Studio

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis, dans le menu contextuel, sélectionnez **Propriétés**.
   
1. Dans la page de propriétés du projet, sélectionnez l’onglet **Développement**.

    ![Menu des propriétés du projet](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Définissez **Demandez confirmation de la suppression d’un déploiement existant** sur **Vrai**. Ce paramètre permet de garantir que vous ne supprimez pas accidentellement un déploiement existant dans Azure

1. Sélectionnez une **configuration de service** pour indiquer la configuration de service que vous souhaitez utiliser lorsque vous exécutez ou déboguez localement votre service cloud. Pour plus d’informations sur la façon de modifier une configuration de service pour un rôle, consultez [Comment configurer les rôles pour un service cloud Azure avec Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Définissez **Démarrer l’émulateur de stockage Azure** sur **Vrai** pour démarrer l’émulateur de stockage Azure quand vous exécutez ou déboguez localement votre service cloud.

1. Définissez **Considérer les avertissements comme des erreurs** sur **Vrai** pour vous assurer que vous ne pouvez pas publier s’il y a des erreurs de validation du package.

1. Définissez **Utiliser les ports du projet web** sur **Vrai** pour vous assurer que votre rôle web utilise le même port à chaque démarrage local dans IIS Express.

1. Dans la barre d’outils de Visual Studio, sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
- [Configurer un projet Azure à l'aide de plusieurs configurations de service](vs-azure-tools-multiple-services-project-configurations.md)

