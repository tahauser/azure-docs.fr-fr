---
title: "Découvrez comment utiliser le connecteur FTP dans des applications logiques | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. Connectez-vous à un serveur FTP pour gérer vos fichiers. Vous pouvez exécuter diverses actions, comme charger, mettre à jour, obtenir et supprimer des fichiers dans le serveur FTP."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 7719e0e90df3a384e870e2e839cb77a27d95ada8
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-ftp-connector"></a>Prise en main du connecteur FTP
Utilisez le connecteur FTP pour surveiller, gérer et créer des fichiers sur un serveur FTP. 

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>Se connecter à FTP
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

### <a name="create-a-connection-to-ftp"></a>Créer une connexion à FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Utiliser un déclencheur FTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> Le connecteur FTP requiert un serveur FTP accessible à partir d’Internet et configuré pour fonctionner en mode PASSIF. En outre, le connecteur FTP **n’est pas compatible avec le protocole FTPS (FTP sur SSL) implicite**. Le connecteur FTP prend uniquement en charge FTPS (FTP sur SSL) en mode explicite.  
> 
> 

Dans cet exemple, nous allons vous indiquer comment utiliser le déclencheur **FTP - Lors de l’ajout ou de la modification d’un fichier** pour initialiser un workflow d’application logique lorsqu’un fichier est ajouté à un serveur FTP ou modifié sur ce dernier. Dans un contexte d’entreprise, vous pourriez utiliser ce déclencheur pour surveiller l’apparition dans un dossier FTP de nouveaux fichiers représentant des commandes émanant de clients.  Vous pourriez ensuite utiliser une action de connecteur FTP telle que **Obtenir le contenu d’un fichier** pour récupérer le contenu de la commande à des fins de traitement ultérieur et de stockage dans votre base de données de commandes.

1. Entrez *ftp* dans la zone de recherche du Concepteur d’applications logiques, puis sélectionnez le déclencheur **FTP - Lors de l’ajout ou de la modification d’un fichier**.   
   ![Image de déclencheur FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   Le contrôle **Lors de l’ajout ou de la modification d’un fichier** s’affiche.  
   ![Image de déclencheur FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Sélectionnez le **...** situé à droite du contrôle. Cette opération ouvre le contrôle de sélecteur de dossier   
   ![Image de déclencheur FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Sélectionnez le symbole **>** (flèche droite) et recherchez le dossier dans lequel vous souhaitez surveiller l’apparition de fichiers nouveaux ou modifiés. Sélectionnez le dossier et remarquez qu’il apparaît à présent dans le contrôle **Dossier**.  
   ![Image de déclencheur FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

À ce stade, votre application logique a été configurée avec un déclencheur qui lance une série d’autres déclencheurs et actions dans le workflow lorsqu’un fichier est modifié ou créé dans le dossier FTP sélectionné. 

> [!NOTE]
> Pour qu’une application logique soit fonctionnelle, elle doit contenir au moins un déclencheur et une action. Suivez les étapes décrites dans la section suivante pour ajouter une action.  
> 
> 

## <a name="use-a-ftp-action"></a>Utiliser une action FTP
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action qui récupérera le contenu du fichier nouveau ou modifié trouvé par le déclencheur.    

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action permettant d’obtenir le contenu du fichier sur le serveur FTP.  
2. Sélectionnez le lien **Ajouter une action** .  
   ![Image d’action FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Entrez *FTP* pour rechercher toutes les actions associées à FTP.
4. Sélectionnez **FTP - Obtenir le contenu d’un fichier** comme action à exécuter lorsqu’un fichier nouveau ou modifié est trouvé dans le dossier FTP.      
   ![Image d’action FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   Le contrôle **Obtenir le contenu d’un fichier** s’affiche. **Remarque** : vous serez invité à autoriser votre application logique à accéder à votre compte de serveur FTP, si vous ne l’avez pas fait précédemment.  
   ![Image d’action FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Sélectionnez le contrôle **Fichier** (espace blanc situé sous **FICHIER***). Ce contrôle vous permet d’utiliser les diverses propriétés du fichier nouveau ou modifié trouvé sur le serveur FTP.  
6. Sélectionnez l’option **Contenu du fichier**.  
   ![Image d’action FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Le contrôle est mis à jour, ce qui indique que l’action **FTP - Obtenir le contenu d’un fichier** récupérera le *contenu du fichier* nouveau ou modifié sur le serveur FTP.      
   ![Image d’action FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Enregistrez votre travail, puis ajoutez un fichier au dossier FTP pour tester votre workflow.    

À ce stade, l’application logique a été configurée avec un déclencheur pour surveiller un dossier d’un serveur FTP et pour initialiser le workflow lorsqu’elle détecte un fichier nouveau ou modifié sur le serveur FTP. 

L’application logique a également été configurée avec une action destinée à récupérer le contenu du fichier nouveau ou modifié.

Vous pouvez à présent ajouter une autre action, telle que l’action [SQL Server - Insérer une ligne](connectors-create-api-sqlazure.md), pour insérer le contenu du fichier nouveau ou modifié dans une table de base de données SQL.  

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)

