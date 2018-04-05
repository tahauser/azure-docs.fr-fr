---
title: Utiliser les outils Visual Studio Stream Analytics pour configurer le processus de déploiement et d’intégration continus | Microsoft Docs
description: Tutoriel de développement d’outils Stream Analytics pour Visual Studio qui permettent de concevoir, déboguer et créer des travaux Stream Analytics Edge.
keywords: Visual Studio, NuGet, DevOps, travaux Edge, Stream Analytics
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/13/2018
ms.author: sujie
ms.openlocfilehash: c6e1d0693035ef343e20cee4b09f0669e089afee
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="develop-stream-analytics-edge-jobs-by-using-visual-studio-tools"></a>Développer des travaux Stream Analytics Edge à l’aide d’outils Visual Studio

Dans ce tutoriel, vous allez apprendre à utiliser les outils Stream Analytics pour Visual Studio afin de concevoir, déboguer et créer des travaux Stream Analytics Edge. Après avoir créé et testé le travail, vous pourrez accéder au portail Azure pour le déployer sur vos appareils. 

## <a name="prerequisites"></a>Prérequis


Pour effectuer ce didacticiel, vous avez besoin des prérequis suivants :

* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) ou [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge. L’édition Express n’est pas prise en charge.  

* Suivez les [instructions d’installation](stream-analytics-tools-for-visual-studio-edge-jobs.md) pour installer les outils Stream Analytics pour Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Créer un projet Stream Analytics Edge 

Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**. Accédez à la liste **Modèles** à gauche > développez **Azure Stream Analytics** > **Stream Analytics Edge** > **Application Azure Stream Analytics Edge**. Entrez le nom, l’emplacement et le nom de la solution pour votre projet, puis sélectionnez **OK**.

![Nouveau projet Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Une fois que vous avez créé le projet, accédez à **l’Explorateur de solutions** pour afficher l’arborescence des dossiers.

![Vue Explorateur de solutions](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Choisir le bon abonnement

1. Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de serveurs**.  

2. Cliquez avec le bouton droit sur **Azure** > sélectionnez **Se connecter à un abonnement Microsoft Azure** > puis connectez-vous avec votre compte Azure.

## <a name="define-inputs"></a>Définir les entrées

1. Dans **l’Explorateur de solutions**, développez le nœud **Entrées** pour afficher l’entrée **EdgeInput.json**. Double-cliquez sur cette entrée pour afficher ses paramètres.  

2. Vérifiez que le paramètre Type de source est défini sur **Flux de données** > que le paramètre Source est défini sur **Edge Hub** > que le paramètre Format de sérialisation de l’événement est défini sur **Json** > et que le paramètre Encodage est défini sur **UTF8**. Pour les besoins de cet exemple, laissez l’alias indiqué dans **Alias d’entrée**. Cet alias peut toutefois être renommé. Si vous renommez l’alias d’entrée, utilisez le nom que vous avez spécifié au moment de la définition de la requête. Sélectionnez **Enregistrer** pour enregistrer les paramètres.  
   ![Configuration de l’entrée](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Définir les sorties

1. Dans **l’Explorateur de solutions**, développez le nœud **Sorties** pour afficher la sortie **EdgeOutput.json**. Double-cliquez sur cette sortie pour afficher ses paramètres.  

2. Vérifiez que le paramètre Récepteur est défini sur **Edge Hub** > que le paramètre Format de sérialisation de l’événement est défini sur **Json** > que le paramètre Encodage est défini sur **UTF8** > et que le paramètre Format est défini sur **Tableau**. Pour les besoins de cet exemple, laissez l’alias indiqué dans **Alias de sortie**. Cet alias peut toutefois être renommé. Si vous renommez l’alias de sortie, utilisez le nom que vous avez spécifié au moment de la définition de la requête. Sélectionnez **Enregistrer** pour enregistrer les paramètres. 
   ![Configuration de la sortie](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Définir la requête de transformation

Les travaux Stream Analytics déployés dans les environnements Edge prennent en charge la plupart des [références du langage de requête Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Toutefois, ils ne prennent pas en charge les opérations suivantes : 


|**Catégorie**  | **Commande**  |
|---------|---------|
|Opérateurs géospatiaux |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Autres opérateurs | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Paramètre d’expression dans l’opérateur COUNT</li><li>Microseconde dans les fonctions DATE et TIME</li><li>UDA JavaScript (fonctionnalité actuellement en préversion pour les travaux déployés dans le cloud)</li></ul>   |

Quand vous créez un travail Edge dans le portail, le compilateur affiche un avertissement si vous utilisez un opérateur non pris en charge.

Dans Visual Studio, définissez la requête de transformation suivante dans l’éditeur de requête (**fichier script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Tester le travail localement

Pour tester la requête localement, vous devez charger les exemples de données. Pour obtenir les exemples de données, téléchargez les données d’inscription à partir du [référentiel GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json). Enregistrez-les sur votre ordinateur local. 

1. Pour charger les exemples de données > cliquez avec le bouton droit sur le fichier **EdgeInput.json** > choisissez **Ajouter une entrée locale**  

2. Dans la fenêtre indépendante qui s’affiche > sélectionnez **Parcourir** pour accéder aux exemples de données à partir de votre chemin local > sélectionnez **Enregistrer**.
   ![Configuration de l’entrée locale](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Un fichier nommé **local_EdgeInput.json** est automatiquement ajouté à votre dossier d’entrées.  
4. Vous pouvez l’exécuter localement ou l’envoyer sur Azure. Pour tester la requête > sélectionnez **Exécuter localement**.  
   ![Options d’exécution](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. La fenêtre d’invite de commandes affiche l’état du travail. Quand le travail s’exécute avec succès, un dossier du type « 2018-02-23-11-31-42 » est créé dans le chemin du dossier de projet « Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42 ». Accédez à l’emplacement du dossier pour afficher les résultats dans le dossier local :

   Vous pouvez également vous connecter au portail Azure pour vérifier que le travail a bien été créé. 

   ![Dossier de résultats](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Envoyer le travail sur Azure

1. Avant d’envoyer le travail sur Azure, vous devez vous connecter à votre abonnement Azure. Ouvrez **l’Explorateur de serveurs** > cliquez avec le bouton droit sur **Azure** > **Se connecter à un abonnement Microsoft Azure** > connectez-vous à votre abonnement Azure.  

2. Pour envoyer le travail sur Azure, accédez à l’éditeur de requête > sélectionnez **Envoyer sur Azure**.  

3. Une fenêtre indépendante s’ouvre, dans laquelle vous pouvez choisir de mettre à jour un travail Edge existant ou d’en créer un. Quand vous mettez à jour un travail existant, le travail mis à jour remplace la configuration entière du travail existant. Dans ce scénario, vous allez créer un travail. Sélectionnez **Créer un travail Azure Stream Analytics** > entrez un nom pour votre travail (par exemple, **MyASAEdgeJob**) > choisissez les paramètres **Abonnement**, **Groupe de ressources** et **Emplacement** appropriés > sélectionnez **Envoyer**.

   ![Envoyer sur Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Votre travail Stream Analytics Edge est créé. Vous pouvez maintenant utiliser le tutoriel [Exécuter des travaux sur IoT Edge](stream-analytics-edge.md) pour apprendre à déployer ce travail sur vos appareils. 

## <a name="manage-the-job"></a>Gérer le travail 

Vous pouvez afficher l’état et le diagramme du travail à partir de l’Explorateur de serveurs. Dans **l’Explorateur de serveurs** > **Stream Analytics** > développez l’abonnement et le groupe de ressources où vous avez déployé le travail Edge > vous pouvez voir que le travail MyASAEdgejob affiche l’état **Créé**. Développez le nœud du travail et double-cliquez dessus pour afficher la vue du travail.

![Options de l’Explorateur de serveurs](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Dans la fenêtre de vue du travail, vous pouvez effectuer plusieurs opérations, comme actualiser le travail, supprimer le travail, ouvrir le travail à partir du portail Azure, etc.

![Diagramme du travail et autres options](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Étapes suivantes

* [Plus d’informations sur Azure Iot Edge](../iot-edge/how-iot-edge-works.md)
* [Didacticiel pour ASA sur IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Envoyer des commentaires à l’équipe à l’aide de ce questionnaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
