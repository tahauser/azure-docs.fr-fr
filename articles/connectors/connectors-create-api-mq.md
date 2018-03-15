---
title: "Découvrir comment utiliser le connecteur MQ dans Azure Logic Apps | Microsoft Docs"
description: "Connectez-vous à un serveur MQ local ou dans Azure à partir de votre flux de travail d’application logique pour parcourir, recevoir et envoyer des messages dans WebSphere MQ"
services: logic-apps
author: valthom
manager: anneta
documentationcenter: 
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/01/2017
ms.author: valthom; ladocs
ms.openlocfilehash: 24ea1ef4d3c6229707458ef2f2005f90d8273304
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Se connecter à un serveur IBM MQ à partir d’applications logiques à l’aide du connecteur MQ 

Microsoft Connector pour MQ envoie et récupère les messages stockés dans un serveur MQ local ou dans Azure. Ce connecteur inclut un client Microsoft MQ qui communique avec un serveur IBM MQ distant sur un réseau TCP/IP. Ce document est un guide de démarrage pour l’utilisation du connecteur MQ. Nous vous recommandons de commencer par parcourir un message dans une file d’attente, puis de tenter les autres actions.    

Le connecteur MQ inclut les actions suivantes. Il n’y a aucun déclencheur.

-   Parcourir un seul message sans le supprimer du serveur IBM MQ
-   Parcourir un lot de messages sans supprimer ceux-ci du serveur IBM MQ
-   Recevoir un message unique et supprimer le message à partir du serveur IBM MQ
-   Recevoir un lot de messages et supprimer les messages du serveur IBM MQ
-   Envoyer un message unique au serveur IBM MQ 

## <a name="prerequisites"></a>Prérequis

* Si vous utilisez un serveur MQ local, [installez la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) sur un serveur au sein de votre réseau. Si le serveur MQ est disponible publiquement ou dans Azure, la passerelle de données n’est pas utilisée ou nécessaire.

    > [!NOTE]
    > Pour que le connecteur MQ fonctionne, .Net Framework 4.6 doit également être installé sur le serveur sur lequel la passerelle de données locale est installée.

* Créez la ressource Azure pour la passerelle de données locale : [configurer la connexion de passerelle de données](../logic-apps/logic-apps-gateway-connection.md).

* Versions d’IBM WebSphere MQ Officiellement prises en charge :
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Créer une application logique

1. Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**. 
2. Entrez le **Nom**, par exemple, MQTestApp, l’**Abonnement**, le **Groupe de ressources** et l’**Emplacement** (utilisez l’emplacement où la connexion de passerelle de données locale est configurée). Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.  
![Créer une application logique](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Ajouter un déclencheur

> [!NOTE]
> Le connecteur MQ ne possède aucun déclencheur. Par conséquent, utilisez un autre déclencheur pour démarrer votre application logique, tel que le déclencheur de **périodicité**. 

1. Le **Concepteur d’applications logique** s’ouvre. Sélectionnez **Périodicité** dans la liste des déclencheurs courants.
2. Sélectionnez **Modifier** dans le déclencheur de périodicité. 
3. Définissez la **Fréquence** sur **Jour** et l’**Intervalle** sur **7**. 

## <a name="browse-a-single-message"></a>Parcourir un seul message
1. Sélectionnez **+Nouvelle étape**, puis **Ajouter une action**.
2. Dans la zone de recherche, tapez `mq`, puis sélectionnez **MQ - Parcourir un message**.  
![Parcourir un message](media/connectors-create-api-mq/Browse_message.png)

3. À défaut de connexion MQ existante, créez la connexion :  

    1. Sélectionnez **Connect via on-premises data gateway** (Se connecter via la passerelle de données locale), puis entrez les propriétés de votre serveur MQ.  
    Pour **Serveur**, vous pouvez entrer le nom du serveur MQ, ou l’adresse IP suivie par un signe deux-points et le numéro de port. 
    2. Le menu déroulant **Passerelle** répertorie toutes les connexions de passerelle existantes qui ont été configurées. Sélectionnez votre passerelle.
    3. Lorsque vous avez terminé, sélectionnez **Créer**. Votre connexion ressemble à ce qui suit :   
    ![Propriétés de connexion](media/connectors-create-api-mq/Connection_Properties.png)

4. Dans les propriétés de l’action, vous pouvez :  

    * utiliser la propriété **Queue** pour accéder à un nom de file d’attente autre que celui défini dans la connexion ;
    * utiliser les propriétés **MessageId**, **CorrelationId**, **GroupId** et autres pour rechercher un message sur la base des différentes propriétés de message MQ ;
    * définir la propriété **IncludeInfo** sur **True** pour inclure des informations supplémentaires dans la sortie, ou la définir sur **False** pour ne pas inclure des informations de message supplémentaires dans la sortie ;
    * entrer une valeur de **délai d’attente** pour déterminer la durée d’attente de l’arrivée d’un message dans une file d’attente vide. Si aucune valeur n’est entrée, le premier message dans la file d’attente est récupéré et aucun temps n’est consacré à l’attente de l’affichage d’un message.  
    ![Parcourir les propriétés d’un message](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Enregistrez** vos modifications, puis **exécutez** votre application logique :  
![Enregistrer et exécuter](media/connectors-create-api-mq/Save_Run.png)

6. Après quelques secondes, les étapes de l’exécution sont affichées, et vous pouvez consulter la sortie. Sélectionnez la coche verte pour afficher les détails de chaque étape. Pour consulter des détails supplémentaires sur les données de sortie, sélectionnez **Afficher les sorties brutes**.  
![Parcourir une sortie de message](media/connectors-create-api-mq/Browse_message_output.png)  

    Sortie brute :  
    ![Parcourir une sortie brute de message](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Quand l’option **IncludeInfo** est définie sur true, la sortie suivante s’affiche :  
![Parcourir les informations include d’un message](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Parcourir plusieurs messages
L’action **Parcourir des messages** inclut une option **BatchSize** permettant d’indiquer le nombre de messages à retourner à partir de la file d’attente.  Si l’option **BatchSize** ne comporte aucune entrée, tous les messages sont retournés. La sortie retournée est un tableau de messages.

1. Lorsque vous ajoutez l’action **Parcourir les messages**, la première connexion configurée est sélectionnée par défaut. Sélectionnez **Modifier la connexion** pour créer une connexion, ou sélectionnez une autre connexion.

2. La sortie de Parcourir les messages affiche ce qui suit :  
![Parcourir la sortie des messages](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Recevoir un seul message
L’action **Recevoir un message** a les mêmes entrées et sorties que l’action **Parcourir un message**. Lorsque vous utilisez l’action **Recevoir un message**, le message est supprimé de la file d’attente.

## <a name="receive-multiple-messages"></a>Recevoir plusieurs messages
L’action **Recevoir des messages** a les mêmes entrées et sorties que l’action **Parcourir des messages**. Lorsque vous utilisez l’action **Recevoir des messages**, les messages sont supprimés de la file d’attente.

S’il n’existe aucun message dans la file d’attente lorsque vous effectuez une opération de recherche ou de réception, l’opération échoue en affichant la sortie suivante :  
![Erreur MQ Aucun message](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Envoyer un message
1. Lorsque vous ajoutez l’action **Envoyer un message**, la première connexion configurée est sélectionnée par défaut. Sélectionnez **Modifier la connexion** pour créer une connexion, ou sélectionnez une autre connexion. Les **Types de messages** valides sont **Datagramme**, **Réponse** ou **Demande**.  
![Propriétés d’envoi des messages](media/connectors-create-api-mq/Send_Msg_Props.png)

2. La sortie de l’opération Envoyer un message ressemble à ceci :  
![Sortie Envoyer un message](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/mq/).

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).
