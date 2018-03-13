---
title: "Stream Analytics : inverser les informations d'identification de connexion pour les entrées et sorties | Microsoft Docs"
description: "Découvrez comment mettre à jour les informations d’identification pour les entrées et les sorties Stream Analytics."
keywords: "informations d’identification"
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/11/2018
ms.author: sngun
ms.openlocfilehash: c1aded8fefc7b56acd2e9ff36bb2c9641665db76
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rotation des informations d'identification pour les entrées et les sorties dans un travail Stream Analytics

Chaque fois que vous régénérez les informations d’identification pour l’entrée ou la sortie d’un travail Stream Analytics, vous devez mettre à jour ce travail avec les nouvelles informations d’identification. Vous devez arrêter le travail avant de mettre à jour les informations d’identification car vous ne pouvez pas remplacer les informations d’identification pendant l’exécution du travail. Pour réduire le délai entre l’arrêt et le redémarrage du travail, Stream Analytics est capable de reprendre un travail à partir de sa dernière sortie. Cette rubrique décrit le processus de rotation des informations d’identification de connexion et le redémarrage du travail avec les nouvelles informations d’identification.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Régénérer les nouvelles informations d’identification et mettre à jour votre travail avec les nouvelles informations d’identification 

Dans cette section, nous vous guiderons tout au long du processus de régénération des informations d’identification pour Stockage d’objets blob, Event Hubs, SQL Database et Stockage de tables. 

### <a name="blob-storagetable-storage"></a>Stockage d’objets blob/de tables
1. Connectez-vous au portail Azure > accédez au compte de stockage que vous avez utilisé comme entrée/sortie pour le travail Stream Analytics.    
2. Dans la section des paramètres, ouvrez **Clés d’accès**. Entre les clés par défaut (key1, key2), sélectionnez celle qui n’est pas utilisée par votre travail puis régénérez-la :  
   ![Régénérer les clés pour le compte de stockage](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Copiez la clé qui vient d’être générée.    
4. Dans le portail Azure, accédez à votre travail Stream Analytics > sélectionnez **Arrêter** et attendez que le travail s’arrête.    
5. Recherchez l’entrée/la sortie du stockage d’objets blob/de tables dont souhaitez mettre à jour les informations d’identification.    
6. Recherchez le champ **Clé du compte de stockage** et collez-y la clé qui vient d’être générée > cliquez sur **Enregistrer**.    
7. Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, et vous pouvez le visualiser dans l’onglet des notifications. Il existe deux notifications : une correspond à l’enregistrement de la mise à jour et l’autre au test de la connexion :  
   ![Notifications après modification de la clé](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Passez à la section [démarrer votre travail à partir de l’heure du dernier arrêt] (#start-your-job-from-the-last-stopped-time).

### <a name="event-hubs"></a>Hubs d'événements

1. Connectez-vous au portail Azure > accédez au hub d’événements que vous avez utilisé comme entrée/sortie pour le travail Stream Analytics.    
2. Dans la section des paramètres, ouvrez **Stratégies d'accès partagé** et sélectionnez la stratégie d’accès requise. Entre la **clé primaire** et la **clé secondaire**, choisissez celle qui n’est pas utilisée par votre travail et régénérez-la :  
   ![Régénérer les clés pour le hub d’événements](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Copiez la clé qui vient d’être générée.    
4. Dans le portail Azure, accédez à votre travail Stream Analytics > sélectionnez **Arrêter** et attendez que le travail s’arrête.    
5. Recherchez l’entrée/la sortie des hubs d’événements dont souhaitez mettre à jour les informations d’identification.    
6. Recherchez le champ **Clé de la stratégie du hub d’événements** et collez votre nouvelle clé > cliquez sur **Enregistrer**.    
7. Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.    
8. Passez à la section [démarrer votre travail à partir de l’heure du dernier arrêt](#start-your-job-from-the-last-stopped-time).

### <a name="sql-database"></a>Base de données SQL

Vous devez vous connecter à la base de données SQL pour mettre à jour les informations d’identification de connexion d’un utilisateur existant. Vous pouvez mettre à jour les informations d’identification en utilisant le portail Azure ou un outil côté client comme SQL Server Management Studio. Cette section décrit le processus de mise à jour des informations d’identification à l’aide du portail Azure.

1. Connectez-vous au portail Azure > accédez à la base de données SQL que vous avez utilisée comme sortie pour le travail Stream Analytics.    
2. À partir de l’**Explorateur de données**, connectez-vous à votre base de données > sélectionnez le type d’autorisation **Authentification SQL Server** > entrez votre **identifiant** et votre **mot de passe** > sélectionnez **Ok**.  
   ![Régénérer les informations d’identification pour la base de données SQL](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. Sous l’onglet de la requête, modifiez le mot de passe d’un de vos utilisateurs en exécutant la requête suivante (veillez à remplacer `<user_name>` par votre nom d’utilisateur et `<new_password>` par votre nouveau mot de passe) :  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Notez ce nouveau mot de passe.    
5. Dans le portail Azure, accédez à votre travail Stream Analytics > sélectionnez **Arrêter** et attendez que le travail s’arrête.    
6. Recherchez la sortie de la base de données SQL dont vous souhaitez remplacer les informations d’identification. Mettez à jour le mot de passe et enregistrez les modifications.    
7. Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.    
8. Passez à la section [démarrer votre travail à partir de l’heure du dernier arrêt](#start-your-job-from-the-last-stopped-time).

### <a name="power-bi"></a>Power BI
1. Connectez-vous au portail Azure > accédez à votre travail Stream Analytics > sélectionnez **Arrêter** et attendez que le travail s’arrête.    
2. Recherchez la sortie Power BI pour laquelle vous souhaitez renouveler les informations d’identification > cliquez sur **Renouveler l’autorisation** (un message de réussite devrait apparaître) > **enregistrez** les modifications.    
3. Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.    
4. Passez à la section [démarrer votre travail à partir de l’heure du dernier arrêt](#start-your-job-from-the-last-stopped-time).

## <a name="start-your-job-from-the-last-stopped-time"></a>Démarrer votre travail à partir de l’heure du dernier arrêt

1. Accédez au volet **Vue d’ensemble** du travail > sélectionnez **Démarrer** pour démarrer le travail.    
2. Sélectionnez **Lors du dernier arrêt** > cliquez sur **Démarrer**. Notez que l’option « Lors du dernier arrêt » s’affiche uniquement si vous avez précédemment exécuté le travail et généré une sortie. Le travail redémarre en fonction de l’heure de la dernière valeur de sortie.
   ![Démarrer le travail](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
