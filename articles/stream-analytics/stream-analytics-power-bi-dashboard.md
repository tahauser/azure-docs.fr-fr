---
title: Tableau de bord Power BI sur Azure Stream Analytics | Microsoft Docs
description: Utilisez un tableau de bord Power BI de diffusion en temps réel pour collecter des informations d’analyse décisionnelle et analyser les données volumineuses à partir d’un travail Stream Analytics.
keywords: tableau de bord d’analyse, tableau de bord en temps réel
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: e1f1d960c312362e0e0cd6d2f83599c28c8c3f05
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics et Power BI : tableau de bord d’analyse en temps réel pour les données de streaming
Azure Stream Analytics vous permet de tirer parti de [Microsoft Power BI](https://powerbi.com/), l’un des principaux outils d’analyse décisionnelle. Dans cet article, vous allez découvrir comment créer des outils d’analyse décisionnelle en utilisant Power BI comme sortie pour vos travaux Azure Stream Analytics. Vous allez également découvrir comment créer et utiliser un tableau de bord en temps réel.

Cet article est la suite du didacticiel [Détection des fraudes en temps réel](stream-analytics-real-time-fraud-detection.md) de Stream Analytics. Il s’appuie sur le flux de travail créé dans ce didacticiel et ajoute une sortie Power BI afin que vous puissiez visualiser les appels téléphoniques frauduleux qui sont détectés par un travail Stream Analytics. 

Vous pouvez visionner [une vidéo](https://www.youtube.com/watch?v=SGUpT-a99MA) illustrant ce scénario.


## <a name="prerequisites"></a>Prérequis


Avant de commencer, veillez à disposer des éléments qui suivent :

* Un compte Azure.
* Un compte pour Power BI. Vous pouvez utiliser un compte professionnel ou un compte scolaire.
* Une version terminée du didacticiel [Détection des fraudes en temps réel](stream-analytics-real-time-fraud-detection.md). Le didacticiel comprend une application qui génère des métadonnées d’appels téléphoniques fictifs. Dans le didacticiel, vous créez un concentrateur Event Hub et lui envoyez des données d’appels téléphoniques de diffusion en continu. Vous écrivez une requête qui détecte les appels frauduleux (appels simultanés provenant d’un même numéro à différents emplacements). 


## <a name="add-power-bi-output"></a>Ajouter une sortie Power BI
Dans le didacticiel de détection des fraudes en temps réel, la sortie est envoyée vers le stockage Blob Azure. Dans cette section, vous ajoutez une sortie qui envoie des informations à Power BI.

1. Dans le portail Azure, ouvrez le travail Stream Analytics que vous avez créé précédemment. Si vous avez utilisé le nom suggéré, le travail est nommé `sa_frauddetection_job_demo`.

2. Sélectionnez la zone **Sorties** au milieu du tableau de bord du travail, puis sélectionnez **+ Ajouter**.

3. Pour **Alias de sortie**, entrez `CallStream-PowerBI`. Vous pouvez utiliser un autre nom. Le cas échéant, prenez-en note, car vous devrez l’utiliser ultérieurement. 

4. Dans **Récepteur**, sélectionnez **Power BI**.

   ![Créer une sortie pour Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Cliquez sur **Autoriser**.

    Une fenêtre s’ouvre dans laquelle vous pouvez indiquer vos informations d’identification Azure pour un compte professionnel ou scolaire. 

    ![Entrer les informations d’identification pour accéder à Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Entrez vos informations d’identification. N’oubliez pas que lorsque vous entrez vos informations d’identification, vous autorisez également le travail Stream Analytics à accéder à votre zone Power BI.

7. Lorsque vous retournez dans le panneau **Nouvelle sortie**, entrez les informations suivantes :

    * **Espace de travail de groupe** : sélectionnez un espace de travail dans votre locataire Power BI où vous voulez créer le jeu de données.
    * **Nom du jeu de données** : entrez `sa-dataset`. Vous pouvez utiliser un autre nom. Le cas échéant, prenez-en note pour l’utiliser ultérieurement.
    * **Nom de la table** : entrez `fraudulent-calls`. Actuellement, une sortie Power BI des travaux Stream Analytics ne peut avoir qu’une table dans un jeu de données.

    ![Espace de travail PBI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Si Power BI dispose d’un jeu de données et d’une table portant les mêmes noms que ceux que vous spécifiez dans le travail Stream Analytics, les données existantes sont écrasées.
    > Nous vous recommandons de ne pas créer explicitement ce jeu de données et cette table dans votre compte Power BI. Ceux-ci sont automatiquement créés au démarrage du travail Stream Analytics et lorsque celui-ci se met à injecter des sorties dans Power BI. Si la requête de travail ne retourne aucun résultat, le jeu de données et la table ne sont pas créés.
    >

8. Cliquez sur **Créer**.

Le jeu de données est créé avec les paramètres suivants :

* **defaultRetentionPolicy : BasicFIFO** : données FIFO avec un maximum de 200 000 lignes.
* **defaultMode : pushStreaming** : le jeu de données prend en charge les vignettes de diffusion en continu et les éléments visuels basés sur des rapports standard (ou push).

Pour le moment, vous ne pouvez pas créer de jeux de données avec d’autres indicateurs.

Pour plus d’informations sur les jeux de données Power BI, consultez la référence [API REST Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-the-query"></a>Écrire la requête

1. Fermez le panneau **Sorties** et retournez dans le panneau du travail.

2. Cliquez dans la zone **Requête**. 

3. Entrez la requête suivante. Cette requête est similaire à la requête de jointure réflexive que vous avez créée dans le didacticiel de détection des fraudes, à la différence près que cette requête envoie les résultats vers la nouvelle sortie que vous avez créée (`CallStream-PowerBI`). 

    >[!NOTE]
    >Si vous n’avez pas nommé l’entrée `CallStream` dans le didacticiel de détection des fraudes, remplacez votre nom par `CallStream` dans les clauses **FROM** et **JOIN** de la requête.

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. Cliquez sur **Enregistrer**.


## <a name="test-the-query"></a>Tester la requête
Cette étape est facultative, mais recommandée. 

1. Si l’application TelcoStreaming n’est pas en cours d’exécution, démarrez-la en procédant comme suit :

    * Ouvrez une fenêtre de commandes.
    * Accédez au dossier dans lequel se trouvent les fichiers telcogenerator.exe et telcodatagen.exe.config modifiés.
    * Exécutez la commande suivante :

            telcodatagen.exe 1000 .2 2

2. Dans le panneau **Requête**, cliquez sur les points situés à côté de l’entrée `CallStream`, puis sélectionnez **Exemple de données de l’entrée**.

3. Spécifiez que vous souhaitez une valeur de trois minutes de données, puis cliquez sur **OK**. Patientez jusqu’à ce que vous soyez informé de l’échantillonnage des données.

4. Cliquez sur **Test** et vérifiez que vous obtenez des résultats.


## <a name="run-the-job"></a>Exécution de la tâche

1. Vérifiez que l’application TelcoStreaming est en cours d’exécution.

2. Fermez le panneau **Requête**.

3. Dans le panneau du travail, cliquez sur **Démarrer**.

    ![Démarrage de la tâche Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Le travail Stream Analytics commence par rechercher les appels frauduleux dans le flux entrant. Le travail crée également le jeu de données et la table dans Power BI et commence à leur envoyer des données sur les appels frauduleux.


## <a name="create-the-dashboard-in-power-bi"></a>Créer le tableau de bord dans Power BI

1. Accédez à [Powerbi.com](https://powerbi.com), puis connectez-vous à l’aide de votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, vous voyez que votre jeu de données est déjà créé :

    ![Jeu de données de diffusion en continu dans Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Dans votre espace de travail, cliquez sur **+&nbsp;Créer**.

    ![Bouton Créer dans l’espace de travail Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Créez un tableau de bord et nommez-le `Fraudulent Calls`.

    ![Créer un tableau de bord et lui donner un nom dans l’espace de travail Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. En haut de la fenêtre, cliquez sur **Ajouter une vignette**, sélectionnez **DONNÉES DE STREAMING PERSONNALISÉES**, puis cliquez sur **Suivant**.

    ![Jeu de données de streaming personnalisé](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Sous **VOS JEUX DE DONNÉES**, sélectionnez votre jeu de données, puis cliquez sur **Suivant**.

    ![Votre jeu de données de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Sous **Type de visualisation**, sélectionnez **Carte**, puis, dans la liste **Champs**, sélectionnez **fraudulentcalls**.

    ![Détails de la visualisation de la nouvelle vignette](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Cliquez sur **Suivant**.

8. Renseignez les détails de la vignette, par exemple un titre et un sous-titre.

    ![Titre et sous-titre de la nouvelle vignette](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Cliquez sur **Appliquer**.

    Nous avons à présent un compteur de fraudes.

    ![Compteur de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Suivez à nouveau les étapes pour ajouter une vignette (à partir de l’étape 4). Cette fois, procédez comme suit :

    * Lorsque vous accédez à **Type de visualisation**, sélectionnez **Graphique en courbes**. 
    * Ajoutez un axe, puis sélectionnez **windowend**. 
    * Ajoutez une valeur, puis sélectionnez **fraudulentcalls**.
    * Pour **Fenêtre de temps à afficher**, sélectionnez les 10 dernières minutes.

    ![Créer une vignette pour le graphique en courbes](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Cliquez sur **Suivant**, ajoutez un titre et un sous-titre, puis cliquez sur **Appliquer**.

    Le tableau de bord Power BI présente désormais deux vues de données sur les appels frauduleux détectés dans les données de diffusion en continu.

    ![Tableau de bord Power BI complété affichant deux vignettes pour les appels frauduleux](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>En savoir plus sur Power BI

Ce didacticiel montre comment créer quelques types de visualisations d’un jeu de données. Power BI peut vous aider à créer d’autres outils d’analyse décisionnelle clients pour votre organisation. Pour plus d’idées, consultez les ressources suivantes :

* Pour un autre exemple de tableau de bord Power BI, visionnez la vidéo de [prise en main de Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .
* Pour plus d’informations sur la configuration d’une sortie de travail Stream Analytics vers Power BI ainsi que sur l’utilisation des groupes Power BI, passez en revue la section [Power BI](stream-analytics-define-outputs.md#power-bi) de l’article sur les [sorties Stream Analytics](stream-analytics-define-outputs.md). 
* Pour plus d’informations sur l’utilisation de Power BI en général, consultez [Tableaux de bord dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>En savoir plus sur les limites et les meilleures pratiques
Actuellement, Power BI peut être appelé environ une fois par seconde. Les éléments visuels de streaming prennent en charge des paquets de 15 Ko. Au-delà de cette taille, ils échouent (mais les transmissions de type push continuent de fonctionner). En raison de cette limite, Power BI s’applique naturellement dans les cas où Azure Stream Analytics débouche sur une réduction significative de la charge des données. Nous vous recommandons d’utiliser une fenêtre bascule ou une fenêtre récurrente pour vous assurer que la transmission de type push des données est effectuée au plus une fois par seconde et que votre requête respecte les exigences de débit.

Vous pouvez utiliser l’équation suivante pour calculer la valeur à donner à votre fenêtre en secondes :

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Par exemple : 

* Vous disposez de 1 000 appareils qui envoient des données à des intervalles d’une seconde.
* Vous utilisez la référence Power BI Pro qui prend en charge 1 000 000 lignes par heure.
* Vous souhaitez publier la quantité moyenne de données par appareil sur Power BI.

L’équation est donc la suivante :

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Étant donné cette configuration, vous pouvez modifier la requête d’origine et la remplacer par ce qui suit :

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Renouveler une autorisation
Si le mot de passe a été modifié depuis la création ou la dernière authentification de votre travail, vous devez réauthentifier votre compte Power BI. Si Azure Multi-Factor Authentication est configuré sur votre client Azure Active Directory (Azure AD), vous devez également renouveler l’autorisation Power BI toutes les deux semaines. Si vous ne la renouvelez pas, vous pouvez observer des symptômes comme un manque de sortie du travail ou une `Authenticate user error` dans les journaux des opérations.

De même, si un travail démarre après l’expiration du jeton, une erreur se produit et le travail échoue. Pour résoudre ce problème, arrêtez le travail en cours d’exécution et accédez à votre sortie Power BI. Pour éviter toute perte de données, sélectionnez le lien **Renouveler l’autorisation**, puis redémarrez votre travail depuis l’**heure du dernier arrêt**.

Une fois que l’autorisation a été actualisée avec Power BI, une alerte verte apparaît dans la zone d’autorisation pour indiquer que le problème a été résolu.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
