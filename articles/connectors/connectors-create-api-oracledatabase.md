---
title: "Ajouter le connecteur de base de données Oracle à Azure Logic Apps | Microsoft Docs"
description: "Utiliser le connecteur de base de données Oracle dans une application logique"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: mandia; ladocs
ms.openlocfilehash: 4119e72abd568c9b53245a872691f06329b040de
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-oracle-database-connector"></a>Prise en main du connecteur de base de données Oracle

À l’aide du connecteur de base de données Oracle, vous créez des workflows d’organisation qui utilisent des données de votre base de données existante. Ce connecteur peut se connecter à une base de données Oracle locale ou à une machine virtuelle sur laquelle la base de données Oracle est installée. Avec ce connecteur, vous pouvez :

* Créez votre flux de travail en ajoutant un nouveau client à une base de données clients ou en mettant à jour une commande dans une base de données de commandes.
* Utilisez des actions pour obtenir une ligne de données, insérer une nouvelle ligne ou en supprimer. Par exemple, quand un enregistrement est créé dans Dynamics CRM Online (déclencheur), insérez une ligne dans une base de données Oracle (action). 

Cette rubrique décrit comment utiliser le connecteur de base de données Oracle dans une application logique.

## <a name="prerequisites"></a>Prérequis

* Versions d’Oracle prises en charge : 
    * Oracle 9 et versions ultérieures
    * Logiciel client Oracle 8.1.7 et versions ultérieures

* Installez la passerelle de données locale. [Connexion à des données locales à partir d’applications logiques](../logic-apps/logic-apps-gateway-connection.md) répertorie les étapes à suivre. La passerelle vous permet de vous connecter à une base de données Oracle locale ou à une machine virtuelle Azure sur laquelle la base de données Oracle est installée. 

    > [!NOTE]
    > La passerelle de données locale agit comme un pont et fournit un transfert de données sécurisé entre les données locales (qui ne sont pas sur le cloud) et vos applications logiques. La même passerelle peut être utilisée avec plusieurs services et plusieurs sources de données. Par conséquent, il vous suffira peut-être d’installer la passerelle une seule fois.

* Installez le client Oracle sur l’ordinateur où vous avez installé la passerelle de données locale. Veillez à installer le fournisseur de données Oracle 64 bits pour .NET à partir d’Oracle :  

  [ODAC 12C version 4 (12.1.0.2.4) 64 bits pour Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Si le client Oracle n’est pas installé, une erreur se produit lorsque vous tentez de créer ou d’utiliser la connexion. Consultez les erreurs courantes dans cette rubrique.


## <a name="add-the-connector"></a>Ajouter le connecteur

> [!IMPORTANT]
> Ce connecteur ne possède aucun déclencheur. Il possède uniquement des actions. Par conséquent, lorsque vous créez votre application logique, ajoutez un autre déclencheur pour démarrer votre application logique, comme **Planification - Périodicité** ou **Requête/réponse - Réponse**. 

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide.

2. Au lancement de votre application logique, sélectionnez le déclencheur **Requête/réponse - Requête** : 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Sélectionnez **Enregistrer**. Au moment de l’enregistrement, une URL de requête est générée automatiquement. 

4. Sélectionnez **Nouvelle étape**, puis sélectionnez **Ajouter une action**. Entrez `oracle` pour afficher les actions disponibles : 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > C’est également le moyen le plus rapide de voir les déclencheurs et les actions disponibles pour n’importe quel connecteur. Entrez une partie du nom du connecteur, comme `oracle`. Le concepteur répertorie tous les déclencheurs et toutes les actions. 

5. Sélectionnez l’une des actions, comme **Base de données Oracle - Obtenir une ligne**. Sélectionnez l’option **Se connecter via la passerelle de données locale**. Entrez le nom du serveur Oracle, la méthode d’authentification, le nom d’utilisateur et le mot de passe, puis sélectionnez la passerelle :

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Une fois connecté, sélectionnez une table dans la liste et entrez l’ID de ligne pour votre table. Vous devez connaître l’identificateur de la table. Si vous ne le connaissez pas, contactez votre administrateur de base de données Oracle et récupérez la sortie à partir de `select * from yourTableName`. Vous disposerez ainsi des informations d’identification nécessaires pour continuer.

    Dans l’exemple suivant, les données de travail sont renvoyées à partir d’une base de données Ressources humaines : 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Dans l’étape suivante, vous pouvez utiliser l’un des autres connecteurs pour créer votre workflow. Si vous souhaitez tester l’obtention de données à partir d’Oracle, envoyez-vous un courrier électronique contenant les données Oracle à l’aide de l’un des connecteurs d’envoi de courriers électroniques, comme Office 365 ou Gmail. Utilisez les jetons dynamiques de la table Oracle pour créer les éléments `Subject` et `Body` de votre message électronique :

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Enregistrez** votre application logique, puis sélectionnez **Exécuter**. Fermez le concepteur et consultez l’état de l’historique des exécutions. En cas d’échec, sélectionnez la ligne du message ayant échoué. Le concepteur s’ouvre et affiche l’étape qui a échoué ainsi que les informations d’erreur. En cas de réussite, vous devez recevoir un courrier électronique contenant les informations que vous avez ajoutées.


### <a name="workflow-ideas"></a>Idées de workflow

* Vous souhaitez surveiller le hashtag #oracle et inclure les tweets dans une base de données afin de pouvoir les consulter et les utiliser dans d’autres applications. Dans une application logique, ajoutez le déclencheur `Twitter - When a new tweet is posted`, puis entrez le hashtag **#oracle**. Ajoutez ensuite l’action `Oracle Database - Insert row`, puis sélectionnez votre table :

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Les messages sont envoyés à une file d’attente Service Bus. Vous souhaitez récupérer ces messages et les inclure dans une base de données. Dans une application logique, ajoutez le déclencheur `Service Bus - when a message is received in a queue`, puis sélectionnez la file d’attente. Ajoutez ensuite l’action `Oracle Database - Insert row`, puis sélectionnez votre table :

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erreurs courantes

#### <a name="error-cannot-reach-the-gateway"></a>**Erreur** : La passerelle est injoignable

**Cause** : la passerelle de données locale n’est pas en mesure de se connecter au cloud. 

**Atténuation** : assurez-vous que votre passerelle s’exécute sur l’ordinateur local où vous l’avez installée, et qu’elle peut se connecter à Internet.  Nous vous recommandons ne pas installer la passerelle sur un ordinateur susceptible d’être mis hors tension ou en veille. Vous pouvez également redémarrer le service de passerelle de données locale (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-please-visit-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erreur**: Le fournisseur utilisé est déconseillé : System.Data.OracleClient requiert le logiciel client Oracle version 8.1.7 ou supérieure. Visitez [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) pour installer le fournisseur officiel.

**Cause** : le kit de développement du client Oracle n’est pas installé sur l’ordinateur qui exécute la passerelle de données locale.  

**Résolution** : téléchargez et installez le kit de développement logiciel du client Oracle sur le même ordinateur que la passerelle de données locale.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erreur** : La table « [Tablename] » ne définit aucune colonne clé

**Cause** : la table n’a pas de clé primaire.  

**Résolution** : le connecteur de la base de données Oracle requiert une table avec une colonne clé primaire.

#### <a name="currently-not-supported"></a>Actuellement non pris en charge

* Vues et procédures stockées 
* Toute table avec des clés composites
* Types d’objet imbriqués dans des tables
 
## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/oracle/). 

## <a name="get-some-help"></a>Obtenir de l’aide

Le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) est l’endroit idéal pour poser des questions, répondre aux questions posées et découvrir ce que font les autres utilisateurs de Logic Apps. 

Vous pouvez améliorer Logic Apps et les connecteurs en votant et en soumettant vos idées sur [http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>étapes suivantes
[Créez une application logique](apis-list.md) et explorez les connecteurs disponibles dans Logic Apps en consultant notre [liste d’API](../logic-apps/quickstart-create-first-logic-app-workflow.md).
