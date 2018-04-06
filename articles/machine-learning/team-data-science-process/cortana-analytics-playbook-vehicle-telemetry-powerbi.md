---
title: Tableau de bord Power BI pour l’état des véhicules et les habitudes de conduite - Azure | Microsoft Docs
description: Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 085ce90311d4d89b365f7fe51a95c00c1a734196
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instructions de configuration du tableau de bord Power BI pour le modèle de la solution Vehicle Telemetry Analytics
Ce menu contient des liens vers les chapitres de ce manuel : 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

La solution Analytics des données de télémétrie du véhicule présente comment les concessions, les fabricants d’automobiles et les compagnies d’assurance peuvent utiliser les fonctionnalités de Cortana Intelligence. Ils peuvent obtenir des analyses prédictives et en temps réel sur l’intégrité des véhicules et les habitudes de conduite afin d’améliorer l’expérience utilisateur, la recherche et le développement et les campagnes marketing. Ces instructions pas à pas montrent comment configurer le tableau de bord et les rapports Power BI après avoir déployé la solution dans votre abonnement. 

## <a name="prerequisites"></a>Prérequis

* Déployez la solution [Vehicle Telemetry Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90). 
* [Installez Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Obtenez un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous n’avez pas d’abonnement Azure, commencez par un abonnement Azure gratuit.
* Ouvrez un compte Power BI.

## <a name="cortana-intelligence-suite-components"></a>Composants de Cortana Intelligence Suite
Les services Cortana Intelligence suivants sont déployés dans votre abonnement parallèlement au modèle de solution Vehicle Telemetry Analytics :

* **Azure Event Hubs** ingère dans Azure des millions d’événements de télémétrie associés aux véhicules.
* **Azure Stream Analytics** fournit une visibilité en temps réel sur l’état des véhicules et conserve ces données dans un stockage à long terme afin d’enrichir l’analytique par lots.
* **Azure Machine Learning** détecte les anomalies en temps réel et utilise le traitement par lots pour fournir des informations prédictives.
* **Azure HDInsight** transforme les données à grande échelle.
* **Azure Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.

**Power BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données et les analyses prédictives. 

La solution utilise deux sources de données différentes :

* Jeux de données de diagnostic et de signaux des véhicules simulés
* Catalogue de véhicules

Un simulateur de télématique des véhicules est intégré à cette solution. Ce simulateur émet des informations de diagnostic et des signaux qui correspondent à l’état du véhicule et aux modes de conduite à un moment donné dans le temps. 

Le catalogue de véhicules est un jeu de données de référence qui mappe des numéros d’identification de véhicules (VIN) à des modèles.

## <a name="power-bi-dashboard-preparation"></a>Préparation du tableau de bord Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Configurer le tableau de bord Power BI en temps réel

#### <a name="start-the-real-time-dashboard-application"></a>Démarrer l’application de tableau de bord en temps réel
Une fois le déploiement terminé, suivez les instructions d’opération manuelle.

1. Téléchargez l’application de tableau de bord en temps réel RealtimeDashboardApp.zip et décompressez-la.

2.  Dans le dossier décompressé, ouvrez le fichier de configuration d’application RealtimeDashboardApp.exe.config. Remplacez les appSettings pour les connexions de service Event Hubs, Stockage Blob Azure et Azure Machine Learning par les valeurs fournies dans les instructions d’opération manuelle. Enregistrez vos modifications.

3. Exécutez l’application RealtimeDashboardApp.exe. Dans la fenêtre de connexion, entrez vos informations d’identification valides pour Power BI. 

   ![Fenêtre de connexion à Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Sélectionnez **Accepter**. L’application démarre.

   ![Autorisations du tableau de bord Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Connectez-vous au site web de Power BI et créez un tableau de bord en temps réel.

Vous êtes maintenant prêt à configurer le tableau de bord Power BI.  

### <a name="configure-power-bi-reports"></a>Configurer les rapports Power BI
Les rapports en temps réel et le tableau de bord prennent environ 30 à 45 minutes. 

1. Accédez à la page web de [Power BI](http://powerbi.com) connectez-vous.

    ![Page de connexion de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Un nouveau jeu de données est généré dans Power BI. Sélectionnez le jeu de données **ConnectedCarsRealtime**.

    ![Jeu de données ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Pour enregistrer le rapport vide, appuyez sur Ctrl+S.

    ![Rapport vide](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Nommez le rapport **Vehicle Telemetry Analytics Real-time - Reports**.

    ![Nom du rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Rapports en temps réel
Cette solution comporte trois rapports en temps réel :

* Véhicules opérationnels
* Véhicules nécessitant une maintenance
* Statistiques relatives à l’état des véhicules

Vous pouvez configurer les trois rapports en temps réel ou vous arrêter après n’importe quelle étape. Vous pouvez ensuite passer à la section suivante qui explique comment configurer les rapports de traitement par lots. Nous vous recommandons de créer les trois rapports afin de visualiser toutes les informations de la fonction temps réel de la solution.  

### <a name="vehicles-in-operation-report"></a>Rapport sur les véhicules opérationnels
1. Double-cliquez sur la **Page 1** et renommez-la **Véhicules opérationnels**.

    ![Véhicules opérationnels](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. Sous l’onglet **Champs**, sélectionnez **vin**. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Carte**.  

    La visualisation **Carte** est créée, comme illustré ci-dessous :

    ![Sélectionnez le numéro d’identification de véhicule (VIN)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

4. Sous l’onglet **Champs**, sélectionnez **city** et **vin**. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Carte**. Faites glisser **vin** vers la zone **Valeurs**. Faites glisser **city** vers la zone **Légende**. 

    ![Visualisation Carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. Sous l’onglet **Visualisations**, sélectionnez la section **Format**. Sélectionnez **Titre** et remplacez **Texte** par **Véhicules opérationnels par ville**.

    ![Véhicules opérationnels par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    La visualisation finale ressemble à l’exemple suivant :

    ![Visualisation finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

7. Sous l’onglet **Champs**, sélectionnez **city** et **vin**. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Histogramme groupé**. Faites glisser **city** vers la zone **Axe**. Faites glisser **vin** vers la zone **Valeur**.

8. Triez le graphique par **Nombre de vin**.

    ![Nombre de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Tapez **Véhicules opérationnels par ville** comme **Titre** du graphique. 

10. Sélectionnez la section **Format**, puis sélectionnez **Couleurs des données**. Affectez la valeur **Activé** à l’option **Afficher tout**.

    ![Couleurs des données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Changez la couleur d’une ville en sélectionnant le symbole de couleur.

    ![Changement de couleur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

13. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Histogramme groupé**. Sous l’onglet **Champs**, faites glisser **city** vers la zone **Axe**. Faites glisser **Model** vers la zone **Légende**. Faites glisser **vin** vers la zone **Valeur**.

    ![Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Le graphique ressemble à l’image suivante :

    ![Rendu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Réorganisez toutes les visualisations pour que la page ressemble à l’exemple suivant :

    ![Tableau de bord avec des visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Vous avez correctement configuré le rapport « Véhicules opérationnels ». Vous pouvez créer le rapport en temps réel suivant, ou bien vous arrêter ici et configurer le tableau de bord. 

### <a name="vehicles-requiring-maintenance-report"></a>Véhicules nécessitant un rapport de maintenance

1. Sélectionnez ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un nouveau rapport. Renommez-le **Véhicules nécessitant une maintenance**.

    ![Véhicules nécessitant une maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. Sous l’onglet **Champs**, sélectionnez **vin**. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Carte**.

    ![Visualisation de carte de VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Le jeu de données contient un champ nommé **MaintenanceLabel**. Ce champ peut avoir la valeur « 0 » ou « 1 ». La valeur est définie par le modèle d’apprentissage automatique provisionné dans le cadre de la solution. Elle est intégrée au chemin en temps réel. La valeur « 1 » indique qu’un véhicule nécessite une maintenance. 

3. Pour ajouter un **filtre au niveau de la page** et afficher les données pour les véhicules qui nécessitent une maintenance 

   a. Faites glisser le champ **MaintenanceLabel** vers **Filtres au niveau de la page**.
  
      ![Filtres au niveau de la page](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. En bas de **Filtres au niveau de la page MaintenanceLabel**, sélectionnez **Filtrage de base**.

      ![Filtrage de base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Affectez la valeur **1** au filtre.

      ![Valeur du filtre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

5. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Histogramme groupé**. 

    ![Carte de VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. Sous l’onglet **Champs**, faites glisser **Model** vers la zone **Axe**. Faites glisser **vin** vers la zone **Valeur**. Triez ensuite la visualisation par **Nombre de vin**. Tapez **Véhicules nécessitant une maintenance par modèle** comme **Titre** du graphique. 

7. Dans la section **Champs** ![Champs-image](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) de l’onglet **Visualisations**, faites glisser **vin** vers **Saturation de la couleur**.

    ![Saturation de la couleur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. Dans la section **Format**, changez **Couleurs des données** dans la visualisation : 

    a. Définissez **F2C812** comme couleur **Minimale**.

    b. Définissez **FF6300** comme couleur **Maximale**.

    ![Nouvelles couleurs de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Les nouvelles couleurs de visualisation ressemblent à l’exemple suivant :

    ![Nouvelles couleurs de visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

10. Sous l’onglet **Visualisations**, sélectionnez **Histogramme groupé**. Faites glisser **vin** vers la zone **Valeur**. Faites glisser **city** vers la zone **Axe**. Triez le graphique par **Nombre de vin**. Tapez **Véhicules nécessitant une maintenance par ville** comme **Titre** du graphique.

    ![Véhicules nécessitant une maintenance par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

12. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Carte à plusieurs lignes**. Faites glisser **Model** et **vin** vers la zone **Champs**.

    ![Carte à plusieurs lignes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Réorganisez toutes les visualisations pour que le rapport final ressemble à l’exemple suivant : 

    ![Rapport final réorganisé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Vous avez correctement configuré le rapport en temps réel « Véhicules nécessitant une maintenance ». Vous pouvez créer le rapport en temps réel suivant, ou bien vous arrêter ici et configurer le tableau de bord. 

### <a name="vehicle-health-statistics-report"></a>Rapport de statistiques relatives à l’état des véhicules

1. Sélectionnez ![Ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un nouveau rapport. Renommez-le **Statistiques relatives à l’état des véhicules**. 

2. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Jauge**. Faites glisser **speed** vers les zones **Valeur**, **Valeur minimale** et **Valeur maximale**.

   ![Statistiques relatives à l’état des véhicules](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. Dans la zone **Valeur**, modifiez l’agrégation par défaut de **speed** en lui affectant la valeur **Moyenne**.

4. Dans la zone **Valeur minimale**, modifiez l’agrégation par défaut de **speed** en lui affectant la valeur **Minimum**.

5. Dans la zone **Valeur maximale**, modifiez l’agrégation par défaut de **speed** en lui affectant la valeur **Maximum**.

   ![Valeurs de vitesse](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Affectez **Vitesse moyenne** comme **titre de la jauge**.

   ![Jauge](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.  

    Vous pouvez également ajouter une **Jauge** pour **Average engine oil**, **Average fuel** et **Average engine temperature**.  

8. Modifiez l’agrégation par défaut des champs de chaque jauge comme vous l’avez fait à l’étape précédente dans la jauge **Vitesse moyenne**.

    ![Jauges supplémentaires](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.

10. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Graphique en courbes et histogramme groupé**. Faites glisser **city** vers **Axe partagé**. Faites glisser **tirepressure**, **engineoil** et **speed** vers la zone **Valeurs de colonne**. Affectez **Moyenne** comme type d’agrégation. 

11. Faites glisser **engineTemperature** vers la zone **Valeurs de ligne**. Affectez **Moyenne** comme type d’agrégation. 

    ![Valeurs de ligne et de colonne](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Modifiez le **Titre** du graphique par **Vitesse moyenne, pression des pneus, huile moteur et température moteur**.  

    ![Titre de Graphique en courbes et histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.

14. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Treemap**. Faites glisser **Model** vers la zone **Groupe**. Faites glisser **MaintenanceProbability** vers la zone **Valeurs**.

15. Tapez **Modèles de véhicules nécessitant une maintenance** comme **Titre** du graphique.

    ![Titre de Treemap](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.

17. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Graphique à barres empilées 100 %**. Faites glisser **city** vers la zone **Axe**. Faites glisser **MaintenanceProbability** et **RecallProbability** vers la zone **Valeur**.

    ![Zones Axe et Valeur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. Dans la section **Format**, sélectionnez **Couleurs des données**. Affectez la valeur **F2C80F** à la couleur **MaintenanceProbability**.

19. Tapez **Probabilité de maintenance et de rappel de véhicules par ville** comme **Titre** du graphique.

    ![Titre du graphique à barres empilées 100 %](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.

21. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Graphique en aires**. Faites glisser **Model** vers la zone **Axe**. Faites glisser **engineOil**, **tirepressure**, **speed** et **MaintenanceProbability** vers la zone **Valeurs**. Affectez **Moyenne** comme type d’agrégation. 

    ![Type d’agrégation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Modifiez le **Titre** du graphique par **Huile moteur moyenne, pression des pneus, vitesse et probabilité de maintenance par modèle**.

    ![Titre de graphique en aires](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Sélectionnez la zone vide pour ajouter une nouvelle visualisation.

24. Sous l’onglet **Visualisations**, sélectionnez la visualisation **Nuage de points**. Faites glisser **Model** vers les zones **Détails** et **Légende**. Faites glisser **fuel** vers la zone **Axe X**. Affectez **Moyenne** comme type d’agrégation. Faites glisser **TempératureMoteur** vers la zone **Axe Y**. Affectez **Moyenne** comme type d’agrégation. Faites glisser **vin** vers la zone **Taille**.

    ![Zones Détails, Légende, Axe et Taille](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Comme **Titre** du graphique, tapez **Moyenne de carburant, moyenne de température moteur et nombre de VIN par modèle**.

    ![Titre du nuage de points](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Le rapport final ressemble à l’exemple suivant :

    ![Rapport final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Épingler les visualisations des rapports sur le tableau de bord en temps réel
1. Créez un tableau de bord vide en sélectionnant le signe plus à côté de **Tableaux de bord**. Tapez le nom **Vehicle Telemetry - Tableau de bord d’analytique**.

    ![Symbole Plus sur le tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Épinglez les visualisations des rapports précédents au tableau de bord. 

    ![Symbole d’épinglage au tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Une fois les trois rapports épinglés au tableau de bord, celui-ci doit ressembler à l’exemple suivant. Si vous n’avez pas créé tous les rapports, votre tableau de bord peut être différent. 

    ![Tableau de bord avec des rapports](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Vous avez correctement créé le tableau de bord en temps réel. À mesure que vous continuez à exécuter CarEventGenerator.exe et RealtimeDashboardApp.exe, vous voyez les mises à jour en direct sur le tableau de bord. Les étapes suivantes prennent environ 10 à 15 minutes.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Configurer le tableau de bord de traitement par lots de Power BI
> [!NOTE]
> Une fois le déploiement effectué, comptez environ deux heures pour permettre au pipeline de traitement par lots de terminer son exécution et de traiter l’équivalent d’une année de données générées. Attendez que le traitement se termine avant de passer aux étapes suivantes :
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Télécharger le fichier Power BI Designer

1. Un fichier Power BI Designer préconfiguré est inclus dans les instructions d’opération manuelle du déploiement. Recherchez « 2. Configurer le tableau de bord de traitement par lots de Power BI ».

2. Téléchargez le modèle Power BI pour le tableau de bord de traitement par lots nommé ici **ConnectedCarsPbiReport.pbix**.

3. Enregistrez-le localement.

### <a name="configure-power-bi-reports"></a>Configurer les rapports Power BI

1. Ouvrez le fichier de concepteur **ConnectedCarsPbiReport.pbix** à l’aide de Power BI Desktop. Si ce n’est déjà fait, installez Power BI Desktop à partir du site web [Installation de Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).

2. Sélectionnez **Modifier les requêtes**.

    ![Modifier les requêtes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Double-cliquez sur **Source**.

    ![Source](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Mettez à jour la chaîne de connexion serveur avec le serveur SQL Azure que vous avez provisionné dans le cadre du déploiement. Recherchez dans les instructions d’opération manuelle sous Base de données Azure SQL :

    * Serveur : somethingsrv.database.windows.net
    * Base de données : connectedcar
    * Nom d’utilisateur : nom d’utilisateur
    * Mot de passe : vous pouvez gérer votre mot de passe SQL Server à partir du portail Azure.

5. Laissez la **base de données** en tant que **connectedcar**.

    ![Base de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Sélectionnez **OK**.

7. L’onglet **Informations d'identification Windows** est sélectionné par défaut. Remplacez-la par **Informations d'identification de la base de données** en sélectionnant l’onglet **Base de données** à droite.

8. Entez le **Nom d’utilisateur** et le **Mot de passe** de votre base de données SQL Azure renseignés pendant la configuration du déploiement.

    ![Informations d’identification de la base de données](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Sélectionnez **Connecter**.

10. Répétez les étapes précédentes pour chacune des trois requêtes restantes présentes dans le volet droit. Ensuite, mettez à jour les détails de la connexion de source de données.

11. Sélectionnez **Fermer et charger**. Les jeux de données de fichier Power BI Desktop sont connectés à des tables de base de données SQL.

12. Sélectionnez **Fermer** pour fermer le fichier Power BI Desktop.

    ![fermez](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Sélectionnez **Enregistrer** pour enregistrer les modifications. 

Vous avez maintenant configuré tous les rapports qui correspondent au traitement par lots dans la solution. 

## <a name="upload-to-powerbicom"></a>Charger vers powerbi.com
1. Accédez au [portail web de Power BI](http://powerbi.com) et connectez-vous.

2. Sélectionnez **Obtenir des données**.

3. Chargez le fichier Power BI Desktop. Sélectionnez **Obtenir des données** > **Obtenir des fichiers** > **Fichier Local**.

4. Accédez à **ConnectedCarsPbiReport.pbix**.

5. Une fois le fichier chargé, revenez à votre espace de travail Power BI. Un jeu de données, un rapport et un tableau de bord vide sont alors créés.  

6. Épinglez les graphiques au nouveau tableau de bord **Vehicle Telemetry - Tableau de bord d’analyse** dans Power BI. Sélectionnez le tableau de bord vide créé précédemment, puis accédez à la section **Rapports**. Sélectionnez le rapport qui vient d’être chargé.  

    ![Nouveau tableau de bord Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Le rapport contient six pages :

    Page 1 : densité de véhicules  
    Page 2 : intégrité des véhicules en temps réel  
    Page 3 : véhicules utilisés de manière intensive   
    Page 4 : véhicules rappelés  
    Page 5 : véhicules économes en carburant  
    Page 6 : logo Contoso Motors  

    ![Rapport Power BI avec six pages](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. **Page 3**, épinglez le contenu suivant :  

    a. **Nombre de vin**  

   ![Page Nombre de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Véhicules utilisés de manière intensive par modèle – Graphique en cascade** 

   ![Page 3 Graphique 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. **Page 5**, épinglez le contenu suivant : 

    a. **Nombre de vin**

   ![Page 5 Graphique 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Véhicules économes en carburant par modèle : histogramme groupé**

   ![Page 5 Graphique 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. **Page 4**, épinglez le contenu suivant :  

    a. **Nombre de vin** 

   ![Page 4 Graphique 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Véhicules rappelés par ville, modèle : Treemap**

   ![Page 4 Graphique 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. **Page 6**, épinglez le contenu suivant :  

    * **Logo Contoso Motors**

    ![Logo Contoso Motors ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organiser le tableau de bord  

1. Accédez au tableau de bord.

2. Pointez sur chaque graphique. Renommez chaque graphique en fonction de l’attribution de noms fournie dans l’exemple de tableau de bord terminé suivant :

   ![Organisation du tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Déplacez les graphiques pour que le tableau de bord ressemble à l’exemple suivant :

    ![Tableau de bord réorganisé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Une fois que vous avez créé tous les rapports mentionnés dans ce document, le tableau de bord final ressemble à l’exemple suivant : 

   ![Tableau de bord final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Vous avez correctement créé les rapports et le tableau de bord pour obtenir des informations en temps réel, prédictives et par lots sur l’état des véhicules et les habitudes de conduite.  
