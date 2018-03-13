---
title: "Démarrage rapide Azure - Exécution d’un travail Batch - Portail"
description: "Apprenez rapidement à exécuter un travail Batch avec le portail Azure."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 01/19/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a00c8ea07c31d2ab4ba2638f2a7e4adcf5ca4a10
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Démarrage rapide : exécution de votre premier travail Batch dans le portail Azure

Ce démarrage rapide montre comment utiliser le portail Azure pour créer un compte Batch, un *pool* de nœuds de calcul (machines virtuelles) et un *travail* qui exécute des *tâches* de base sur le pool. À l’issue de ce démarrage rapide, vous maîtriserez les concepts clés du service Batch et serez prêt à essayer Azure Batch avec des charges de travail plus réalistes à plus grande échelle.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure depuis l’adresse https://portal.azure.com.

## <a name="create-a-batch-account"></a>Création d’un compte Batch

Suivez ces étapes pour créer un compte Batch d’exemple à des fins de test. Vous avez besoin d’un compte Batch pour créer des pools et des travaux. Comme indiqué ici, vous pouvez lier un compte de stockage Azure avec le compte Batch. Bien que ce ne soit pas obligatoire pour ce démarrage rapide, le compte de stockage est utile pour déployer des applications et stocker des données d’entrée et de sortie pour la plupart des charges de travail réelles.


1. Cliquez sur **Nouveau** > **Calcul** > **Service Batch**. 

  ![Batch dans Marketplace][marketplace_portal]

2. Entrez les valeurs **Nom du compte** et **Groupe de ressources**. Le nom du compte doit être unique à l’**emplacement** Azure sélectionné, utilisez uniquement des caractères en minuscules ou des nombres dans une limite de 3 à 24 caractères. 

3. Dans **Compte de stockage** : sélectionnez un compte de stockage à usage général existant ou créez un nouveau compte de stockage.

4. Conserver les valeurs par défaut pour les paramètres restants, puis cliquez sur **Créer** pour créer le compte.

  ![Création d’un compte Batch][account_portal]  

Lorsque le message **Déploiement réussi** s’affiche, accédez au compte Batch dans le portail.

## <a name="create-a-pool-of-compute-nodes"></a>Création d’un pool de nœuds de calcul

Maintenant que vous avez un compte Batch, créez un pool d’exemple de nœuds de calcul Windows à des fins de test. Le pool pour cet exemple rapide se compose de 2 nœuds exécutant une image Windows Server 2012 R2 à partir de la Place de marché Microsoft Azure.


1. Dans le compte Batch, cliquez sur **Pools** > **Ajouter**.

2. Entrez un **ID de Pool** appelé *mypool*. 

3. Dans **Système d’exploitation**, sélectionnez les paramètres suivants (vous pouvez explorer d’autres options).
  
  |Paramètre  |Valeur  |
  |---------|---------|
  |**Type d’image**|Place de marché (Linux/Windows)|
  |**Publisher**     |MicrosoftWindowsServer|
  |**Offer**     |WindowsServer|
  |**Sku**     |2012-R2-Datacenter-smalldisk|

  ![Sélectionner un système d’exploitation pour le pool][pool_os] 

4. Faites défiler pour accéder aux paramètres **Taille du nœud** et **Mise à l’échelle**. La taille de nœud suggérée offre un bon compromis entre performances et coûts pour cet exemple rapide.
  
  |Paramètre  |Valeur  |
  |---------|---------|
  |**Niveau de tarification du nœud**     |Standard_A1|
  |**Nœuds dédiés cibles**     |2|

  ![Sélectionner une taille de pool][pool_size] 

5. Conservez les valeurs par défaut pour les paramètres restants, puis cliquez sur **OK** pour créer le pool.

Azure Batch crée le pool immédiatement, mais prend quelques minutes pour allouer et démarrer les nœuds de calcul. Pendant ce temps, l’**état de l’allocation** du pool est **Redimensionnement**. Vous pouvez commencer et créer un travail ainsi que des tâches alors que le pool est en redimensionnement. 

![Pool en état de redimensionnement][pool_resizing]

Après quelques minutes, l’état du pool est **Stable** et les nœuds démarrent. Cliquez sur **Nœuds** pour vérifier l’état des nœuds. Lorsque l’état d’un nœud est **Inactif**, il est prêt à exécuter des tâches. 

## <a name="create-a-job"></a>Création d’un travail

Maintenant que vous disposez d’un pool, créez un travail à exécuter sur celui-ci. Un travail Batch est un groupe logique d’une ou de plusieurs tâches. Un travail inclut les paramètres communs aux tâches, tels que la priorité et le pool pour exécuter des tâches. Dans un premier temps, le travail n’a aucune tâche. 

1. Dans la vue de compte Batch, cliquez sur **Travaux** > **Ajouter**. 

2. Entrez un **ID de travail** appelé *myjob*. Dans **Pool**, sélectionnez *mypool*. Conservez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.

  ![Création d’un travail][job_create]

Une fois que le travail est créé, la page **Tâches** s’ouvre.

## <a name="create-tasks"></a>Création de tâches

À présent créez des tâches d’exemple à exécuter dans le travail. En général, vous créez plusieurs tâches qu’Azure Batch met en file d’attente et distribue pour les exécuter sur les nœuds de calcul. Dans cet exemple, vous créez deux tâches identiques. Chaque tâche exécute une ligne de commande pour afficher les variables d’environnement Azure Batch sur un nœud de calcul, puis attend 90 secondes. 

Lorsque vous utilisez Azure Batch, la ligne de commande se trouve là où vous spécifiez votre application ou votre script. Azure Batch fournit plusieurs façons de déployer des applications et des scripts sur des nœuds de calcul. 

Pour créer la première tâche :

1. Cliquez sur **Add**.

2. Entrez un **ID de tâche** appelé *mytask*. 

3. Dans **Ligne de commande**, entrez `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Conservez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.

  ![Créer une tâche][task_create]

Après avoir créé une tâche, Azure Batch la met en file d’attente pour l’exécuter sur le pool. Lorsqu’un nœud est disponible pour l’exécuter, la tâche s’exécute.

Pour créer une seconde tâche, revenez à l’étape 1. Entrez un autre **ID de tâche**, mais spécifiez une ligne de commande identique. Si la première tâche est en cours d’exécution, Azure Batch démarre la deuxième tâche sur l’autre nœud dans le pool.

## <a name="view-task-output"></a>Afficher les sorties des tâches

Les exemples de tâche précédents se terminent en quelques minutes. Pour afficher la sortie d’une tâche terminée, cliquez sur **Fichiers sur le nœud**, puis sélectionnez le fichier `stdout.txt`. Ce fichier montre la sortie standard de la tâche. Le contenu ressemble à ce qui suit :

![Afficher les sorties des tâches][task_output]

Le contenu affiche les variables d’environnement Azure Batch qui sont définies sur le nœud. Lorsque vous créez vos propres travaux Batch et tâches, vous pouvez référencer ces variables d’environnement dans des lignes de commande de tâche, ainsi que dans les applications et les scripts exécutés par les lignes de commande.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez poursuivre les exemples et didacticiels Azure Batch, utilisez le compte Batch et le compte de stockage lié créés dans ce démarrage rapide. Il n’existe aucun frais pour le compte Batch proprement dit.

Vous êtes facturé pour le pool pendant que les nœuds sont en cours d’exécution, même si aucun travail n’est planifié. Lorsque vous n’avez plus besoin du pool, supprimez-le. Dans la vue de compte, cliquez sur **Pools** et le nom du pool. Ensuite, cliquez sur **Supprimer**.  Lorsque vous supprimez le pool, toutes les sorties de tâche sur les nœuds sont supprimées. 

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le compte Batch et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources pour le compte Batch, puis cliquez sur **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte Batch, un pool Batch et un travail Batch. Le travail a exécuté des tâches d’exemple et vous avez affiché des sorties créées sur un des nœuds. Maintenant que vous maîtriserez les concepts clés du service Batch, vous êtres prêt à essayer Azure Batch avec des charges de travail plus réalistes à plus grande échelle. Pour plus d’informations sur Microsoft Azure Batch, consultez les didacticiels Azure Batch. 

> [!div class="nextstepaction"]
> [Didacticiels Azure Batch](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png