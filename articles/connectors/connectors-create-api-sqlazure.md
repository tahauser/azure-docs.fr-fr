---
title: "Ajouter le connecteur de base de données SQL Azure à vos applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur de base de données SQL Azure avec les paramètres de l’API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: def2b65f009c377233c45356f8fa661b86d73f51
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Prise en main du connecteur de base de données SQL Azure
Créez des flux de travail pour votre organisation destinés à gérer les données dans vos tables à l’aide du connecteur de base de données SQL Azure. 

Avec Base de données SQL, vous pouvez effectuer les opérations suivantes :

* Créez votre flux de travail en ajoutant un nouveau client à une base de données clients ou en mettant à jour une commande dans une base de données de commandes.
* Utilisez des actions pour obtenir une ligne de données, insérer une nouvelle ligne ou en supprimer. Par exemple, quand un enregistrement est créé dans Dynamics CRM Online (déclencheur), insérez une ligne dans une base de données SQL Azure (action). 

Cette rubrique explique comment utiliser le connecteur SQL Database dans une application logique, et répertorie les actions.

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../logic-apps/logic-apps-overview.md) et [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-sql-database"></a>Connexion à la base de données SQL Azure
Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à SQL Database, vous devez commencer par créer une *connexion* SQL Database. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ensuite, dans la base de données SQL, entrez vos informations d’identification de base de données SQL pour créer la connexion. 

#### <a name="create-the-connection"></a>Créer la connexion
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur
Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, notamment un déclencheur de périodicité, un déclencheur Webhook HTTP, des déclencheurs disponibles avec d’autres connecteurs, etc. [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) vous fournit un exemple.

## <a name="use-an-action"></a>Utilisation d’une action
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Choisissez **Ajouter une action**.
3. Dans la zone de texte, saisissez « sql » pour obtenir la liste de toutes les actions disponibles.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. Dans notre exemple, choisissez **SQL Server - Obtenir une ligne**. S’il existe déjà une connexion, sélectionnez le **nom de la table** dans la liste déroulante, puis entrez l’**ID de ligne** à retourner.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-sqlazure.md#create-the-connection) dans cette rubrique décrit ces propriétés. 
   
   > [!NOTE]
   > Dans cet exemple, nous obtiendrons une ligne d’une table. Pour consulter les données de cette ligne, ajoutez une autre action qui crée un fichier en utilisant les champs de la table. Par exemple, ajoutez une action OneDrive qui utilise les champs FirstName et LastName pour créer un nouveau fichier dans le compte de stockage cloud. 
   > 
   > 
5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/sql/). 

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).

