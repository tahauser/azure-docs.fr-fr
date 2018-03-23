---
title: Découvrez comment utiliser le connecteur SFTP dans vos applications logiques | Microsoft Docs
description: Créez des applications logiques avec Azure App Service. Connectez-vous à l’API SFTP pour envoyer et recevoir des fichiers. Vous pouvez effectuer diverses opérations, telles que créer, mettre à jour, obtenir ou supprimer des fichiers.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: estfan; ladocs
ms.openlocfilehash: ee4dd59bd7b4c09cccadfff29868029559955c28
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sftp-connector"></a>Prise en main du connecteur SFTP
Utilisez le connecteur SFTP pour accéder à un compte SFTP afin d’envoyer et de recevoir des fichiers. Vous pouvez effectuer diverses opérations, telles que créer, mettre à jour, obtenir ou supprimer des fichiers.  

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-sftp"></a>Se connecter à SFTP
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

### <a name="create-a-connection-to-sftp"></a>Créer une connexion à SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Utiliser un déclencheur SFTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Dans cet exemple,nous utilisons le déclencheur **SFTP - Lors de l’ajout ou de la modification d’un fichier**, un déclencheur est utilisé pour initialiser un flux de travail d’application logique lorsqu’un fichier est ajouté à un serveur SFTP ou modifié sur ce dernier. Vous ajoutez une condition qui vérifie le contenu du fichier nouveau ou modifié, puis prend une décision pour extraire le fichier si son contenu indique qu’il doit être extrait avant d’être utilisé. Enfin, ajoutez une action pour extraire le contenu d’un fichier et placer le contenu extrait dans un dossier du serveur SFTP. 

Dans un contexte d’entreprise, vous pourriez utiliser ce déclencheur pour surveiller l’apparition dans un dossier SFTP de nouveaux fichiers représentant les commandes des clients.  Vous pourriez ensuite utiliser une action de connecteur SFTP telle que **Obtenir le contenu d’un fichier** pour récupérer le contenu de la commande à des fins de traitement ultérieur et de stockage dans une base de données de commandes.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Ajouter une condition
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Utiliser une action SFTP
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Détails spécifiques aux connecteurs

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).