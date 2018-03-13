---
title: "Ajouter le connecteur Stockage Blob Azure à vos applications logiques | Microsoft Docs"
description: Prise en main et configuration du connecteur Stockage Blob Azure dans une application logique
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: e12669abd41f09d161fab786af29955da54a1633
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Utilisation du connecteur Stockage Blob Azure dans une application logique
Le connecteur Stockage Blob Azure permet de télécharger, de mettre à jour, d’obtenir et de supprimer des objets blob dans votre compte de stockage, le tout au sein d’une application logique.  

Avec Azure Blob Storage, vous pouvez effectuer les opérations suivantes :

* Créez votre workflow en téléchargeant les nouveaux projets ou en extrayant les fichiers récemment mis à jour.
* Utilisez des actions pour obtenir les métadonnées d’un fichier, supprimer un fichier, copier des fichiers, etc. Par exemple, lorsqu’un outil est mis à jour dans un site web Azure (déclencheur), vous pouvez mettre à jour un fichier dans le stockage blob (action). 

Cette rubrique décrit comment utiliser le connecteur de stockage blob dans une application logique.

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../logic-apps/logic-apps-overview.md) et [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-blob-storage"></a>Connexion au stockage d’objets blob Azure
Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à un compte de stockage, commencez par créer une *connexion* de stockage des objets blob. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans le cas d’Azure Storage, entrez les informations d’identification de votre compte de stockage pour créer la connexion. 

#### <a name="create-the-connection"></a>Créer la connexion
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur
Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, notamment un déclencheur de périodicité, un déclencheur Webhook HTTP, des déclencheurs disponibles avec d’autres connecteurs, etc. [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) vous fournit un exemple.

## <a name="use-an-action"></a>Utilisation d’une action
Une action est une opération effectuée par le flux de travail défini dans une application logique.

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Choisissez **Ajouter une action**.
3. Dans la zone de texte, saisissez « blob » pour obtenir la liste de toutes les actions disponibles.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. Dans notre exemple, choisissez **AzureBlob - Obtenir les métadonnées d’un fichier à l’aide du chemin**. Si une connexion existe déjà, sélectionnez le bouton **...** (Afficher le sélecteur) pour sélectionner un fichier.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-azureblobstorage.md#create-the-connection) dans cette rubrique décrit ces propriétés. 
   
   > [!NOTE]
   > Dans cet exemple, nous obtiendrons les métadonnées d’un fichier. Pour consulter les métadonnées, ajoutez une autre action qui crée un nouveau fichier à l’aide d’un autre connecteur. Par exemple, ajoutez une action OneDrive qui crée un nouveau fichier « test » basé sur les métadonnées. 


5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

> [!TIP]
> L’[Explorateur de stockage](http://storageexplorer.com/) est un excellent outil pour gérer plusieurs comptes de stockage.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).

