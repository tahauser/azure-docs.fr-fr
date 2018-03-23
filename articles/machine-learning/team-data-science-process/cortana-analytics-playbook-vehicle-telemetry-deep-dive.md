---
title: Découvrir de manière approfondie comment prédire l’état des véhicules et les habitudes de conduite | Microsoft Docs
description: Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 370ab807ef85240238c51d1693796c26981edb15
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Guide de la solution Vehicle Telemetry Analytics : découverte approfondie de la solution
Ce menu contient des liens vers les sections de ce manuel : 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Ce document explore chacune des étapes mentionnées dans l’architecture de solution. Il contient également des instructions et des pointeurs de personnalisation. 

## <a name="data-sources"></a>Sources de données
La solution utilise deux sources de données différentes :

* Jeu de données de diagnostic et de signaux des véhicules simulés
* Catalogue de véhicules

Un simulateur de télématique des véhicules est intégré à cette solution, comme indiqué dans la capture d’écran ci-dessous. Ce simulateur émet des informations de diagnostic et des signaux qui correspondent à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. Pour télécharger la solution Vehicle Telematics Simulator Visual Studio afin de la personnaliser en fonction de vos besoins, accédez à la page web [Simulateur de télématique des véhicules](http://go.microsoft.com/fwlink/?LinkId=717075). Le catalogue de véhicules contient un jeu de données de référence qui mappe les numéros d’identification des véhicules (VIN, Vehicle Identification Numbers) aux modèles.

![Simulateur de télématique des véhicules](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Ce jeu de données au format JSON contient le schéma suivant.

| Colonne | Description | Valeurs |
| --- | --- | --- |
| VIN |Numéro d’identification de véhicule généré de manière aléatoire |Obtenu à partir d’une liste principale de 10 000 VIN générés de manière aléatoire |
| Outside temperature |Température extérieure mesurée dans la zone de conduite du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 100 |
| Engine temperature |Température moteur du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 500 |
| Vitesse |Régime de conduite du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 100 |
| Fuel |Niveau de carburant du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 100 (indique le niveau de carburant en pourcentage) |
| EngineOil |Niveau d’huile moteur du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 100 (indique le niveau d’huile moteur en pourcentage) |
| Pression des pneus |Pression des pneus du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 50 (indique le niveau de pression des pneus en pourcentage) |
| Odometer |Valeur lue sur le compteur kilométrique du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 200 000 |
| Accelerator_pedal_position |Position de la pédale d’accélérateur du véhicule |Nombre généré de manière aléatoire et compris entre 0 et 100 (indique le niveau d’accélération en pourcentage) |
| Parking_brake_status |Indique si le véhicule est stationné ou non |True ou False |
| Headlamp_status |Indique si les phares sont allumés ou non |True ou False |
| Brake_pedal_status |Indique si la pédale de frein est enfoncée ou non |True ou False |
| Transmission_gear_position |Position de la boîte de vitesses du véhicule |États : première, deuxième, troisième, quatrième, cinquième, sixième, septième, huitième |
| Ignition_status |Indique si le véhicule roule ou s’il est arrêté |True ou False |
| Windshield_wiper_status |Indique si les essuie-glaces sont activés ou non |True ou False |
| ABS |Indique si l’ABS est activé ou non |True ou False |
| Timestamp |Date et heure de création du point de données |Date |
| City |Emplacement du véhicule |Quatre villes dans cette solution : Bellevue, Redmond, Sammamish, Seattle |

Le jeu de données de référence des modèles de véhicules mappe les VIN aux modèles. 

| VIN | Modèle |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Berline |
| 8J0U8XCPRGW4Z3NQE |Hybride |
| WORG68Z2PLTNZDBI7 |Berline familiale |
| JTHMYHQTEPP4WBMRN |Berline |
| W9FTHG27LZN1YWO0Y |Hybride |
| MHTP9N792PHK08WJM |Berline familiale |
| EI4QXI2AXVQQING4I |Berline |
| 5KKR2VB4WHQH97PF8 |Hybride |
| W9NSZ423XZHAONYXB |Berline familiale |
| 26WJSGHX4MA5ROHNL |Cabriolet |
| GHLUB6ONKMOSI7E77 |Break |
| 9C2RHVRVLMEJDBXLP |Voiture compacte |
| BRNHVMZOUJ6EOCP32 |Petit SUV |
| VCYVW0WUZNBTM594J |Voiture de sport |
| HNVCE6YFZSA5M82NY |SUV de taille moyenne |
| 4R30FOR7NUOBL05GJ |Break |
| WYNIIY42VKV6OQS1J |Grand SUV |
| 8Y5QKG27QET1RBK7I |Grand SUV |
| DF6OX2WSRA6511BVG |Coupé |
| Z2EOZWZBXAEW3E60T |Berline |
| M4TV6IEALD5QDS3IR |Hybride |
| VHRA1Y2TGTA84F00H |Berline familiale |
| R0JAUHT1L1R3BIKI0 |Berline |
| 9230C202Z60XX84AU |Hybride |
| T8DNDN5UDCWL7M72H |Berline familiale |
| 4WPYRUZII5YV7YA42 |Berline |
| D1ZVY26UV2BFGHZNO |Hybride |
| XUF99EW9OIQOMV7Q7 |Berline familiale |
| 8OMCL3LGI7XNCC21U |Cabriolet |
| ……. | |

## <a name="ingestion"></a>Ingestion
Les composants Azure Event Hubs, Azure Stream Analytics et Azure Data Factory sont combinés pour ingérer les signaux des véhicules, les événements de diagnostic, ainsi que l’analytique de traitement par lots et en temps réel. Tous ces composants sont créés et configurés dans le cadre du déploiement de la solution. 

### <a name="real-time-analysis"></a>Analyse en temps réel
Les événements générés par le simulateur de télématique des véhicules sont publiés dans le hub d’événements à l’aide du SDK Event Hub.  

![Tableau de bord Event Hub](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

La tâche Stream Analytics ingère ces événements à partir du hub d’événements et traite les données en temps réel pour analyser l’état du véhicule.

![Données de traitement de la tâche Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


La tâche Stream Analytics :

* Ingère les données du hub d’événements.
* Effectue une jointure avec les données de référence pour mapper le numéro d’identification du véhicule au modèle correspondant. 
* Conserve les données dans le Stockage Blob Azure pour une analytique en mode batch approfondie. 

La requête Stream Analytics suivante est utilisée pour conserver les données dans le Stockage Blob : 

![Requête de tâche Stream Analytics pour l’ingestion de données](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Analyse en mode batch
Un volume supplémentaire de signaux de véhicules simulés et un jeu de données de diagnostic sont également générés pour permettre une analytique en mode batch approfondie. Ce volume supplémentaire est nécessaire pour garantir un volume de données représentatif dans le cadre d’un traitement par lots. Nous utilisons pour cela PrepareSampleDataPipeline dans le flux de travail Data Factory pour générer l’équivalent d’une année de signaux et de diagnostics de véhicules simulés. Pour télécharger la solution Visual Studio Activité .NET personnalisée Data Factory afin de la personnaliser en fonction de vos besoins, accédez à la page web [Activité personnalisée Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077). 

Ce flux de travail montre des exemples de données préparés pour le traitement par lots.

![Exemples de données préparés pour le flux de travail de traitement par lots](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Le pipeline est composé d’une activité .NET Data Factory personnalisée.

![Activité PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Une fois que le pipeline est correctement exécuté et que le jeu de données RawCarEventsTable est marqué comme « Prêt », l’équivalent d’une année de données de signaux et de diagnostics de véhicules simulés est généré. Le dossier et le fichier ci-dessous sont créés dans votre compte de stockage sous le conteneur connectedcar :

![Sortie de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Partitionner le jeu de données
Au cours de l’étape de préparation des données, les signaux et les données de diagnostic de véhicules bruts et semi-structurés sont partitionnés au format ANNÉE/MOIS. Ce partitionnement favorise une interrogation plus efficace et un stockage scalable à long terme en permettant le basculement. Par exemple, quand un compte de stockage d’objets blob est rempli, il bascule vers le compte suivant. 

>[!NOTE] 
>Cette étape de la solution s’applique uniquement au traitement par lots.

Gestion des données d’entrée et de sortie :

* Les **données de sortie** (intitulées PartitionedCarEventsTable) sont conservées pendant une longue période sous une forme primaire/« la plus brute » dans le Data Lake (lac de données) du client. 
* Les **données d’entrée** de ce pipeline sont en général ignorées, car les données de sortie sont entièrement fidèles à l’entrée. Elles sont mieux stockées (partitionnées) pour une utilisation ultérieure.

Flux de travail Partition Car Events

![Workflow Partition Car Events](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Les données brutes sont partitionnées à l’aide d’une activité Hive Azure HDInsight dans PartitionCarEventsPipeline, comme indiqué dans la capture d’écran suivante. Les exemples de données générés pour une année à l’étape de préparation des données sont partitionnés par ANNÉE/MOIS. Les partitions sont utilisées pour générer les signaux et les données de diagnostic de véhicules pour chaque mois (12 partitions au total) d’une année. 

![Activité PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Script Hive PartitionConnectedCarEvents**

Le script Hive partitioncarevents.hql est utilisé pour le partitionnement. Il se trouve dans le dossier \demo\src\connectedcar\scripts du fichier zip téléchargé. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

Une fois le pipeline exécuté, les partitions suivantes sont générées dans votre compte de stockage sous le conteneur connectedcar :

![Sortie partitionnée](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Les données sont maintenant optimisées, plus faciles à gérer et prêtes à être traitées pour obtenir des informations de traitement par lots complètes. 

## <a name="data-analysis"></a>Analyse des données
Cette section explique comment associer les composants Stream Analytics, Azure Machine Learning, Data Factory et HDInsight pour analyser avec précision l’état des véhicules et les habitudes de conduite.

### <a name="machine-learning"></a>Apprentissage automatique
L’objectif est ici de prédire quels véhicules devront faire l’objet d’une intervention de maintenance ou d’un rappel en fonction de certaines statistiques de contrôle d’intégrité et des suppositions suivantes :

* Si l’une des trois conditions suivantes est remplie, les véhicules ont besoin d’une intervention de maintenance :
  
  * La pression des pneus est faible.
  * Le niveau d’huile moteur est faible.
  * La température du moteur est élevée.

* Si l’une des conditions suivantes est remplie, les véhicules peuvent présenter un problème de sécurité et nécessiter un rappel :
  
  * La température du moteur est élevée mais la température extérieure est faible.
  * La température du moteur est faible mais la température extérieure est élevée.

En fonction des exigences précédentes, deux modèles distincts détectent les anomalies. Un modèle concerne la détection de maintenance de véhicule, tandis que l’autre concerne la détection de rappel de véhicule. Dans les deux modèles, l’algorithme Principal Component Analysis (PCA) intégré est utilisé pour la détection des anomalies. 

#### <a name="maintenance-detection-model"></a>**Modèle de détection de maintenance**

Si l’un des trois indicateurs (pression des pneus, huile moteur ou température du moteur) remplit sa condition respective, le modèle de détection de maintenance signale une anomalie. Ainsi, seules ces trois variables doivent être prises en compte dans la génération du modèle. Dans l’expérience d’apprentissage automatique, nous utilisons le module **Sélectionner des colonnes dans le jeu de données** pour extraire ces trois variables. Nous utilisons ensuite le module de détection d’anomalies basé sur l’algorithme PCA pour générer le modèle de détection d’anomalies. 

PCA est une technique d’apprentissage automatique reconnue qui peut être appliquée à la sélection des fonctionnalités, à la classification et à la détection d’anomalies. PCA convertit un ensemble de cas qui contiennent des variables potentiellement corrélées en un ensemble de valeurs appelées composants principaux. La modélisation PCA vise essentiellement à projeter des données dans un espace de plus faible dimension afin de faciliter l’identification des fonctionnalités et des anomalies.

Pour chaque nouvelle entrée dans le modèle de détection, le détecteur d’anomalies calcule d’abord sa projection sur les vecteurs propres. Ensuite, il calcule l’erreur de reconstruction normalisée. Cette erreur normalisée est le score d’anomalie : plus l’erreur est élevée, plus l’anomalie de l’instance est élevée. 

Dans le problème de la détection de maintenance, chaque enregistrement est considéré comme un point dans un espace tridimensionnel défini par les coordonnées de pression des pneus, d’huile moteur et de température du moteur. Pour capturer ces anomalies, nous utilisons PCA afin de projeter les données d’origine de l’espace 3D dans un espace 2D. Nous affectons donc la valeur 2 au paramètre « nombre de composants à utiliser » dans PCA. Ce paramètre joue un rôle important dans l’application de la détection d’anomalies basée sur l’algorithme PCA. Après l’utilisation de PCA pour projeter les données, ces anomalies sont identifiées plus facilement.

#### <a name="recall-anomaly-detection-model"></a>**Modèle de détection des anomalies de rappel**

Dans le modèle de détection des anomalies de rappel, nous utilisons le module **Sélectionner des colonnes dans le jeu de données** et le module de détection d’anomalies reposant sur PCA de manière similaire. Plus précisément, nous commençons par extraire trois variables (température du moteur, température extérieure et vitesse) à l’aide du module **Sélectionner des colonnes dans le jeu de données**. Nous ajoutons également la variable vitesse, car la température du moteur est généralement corrélée à la vitesse. Nous utilisons ensuite le module de détection d’anomalies PCA pour projeter les données de l’espace 3D sur un espace 2D. Les critères de rappel sont satisfaits. Le véhicule nécessite un rappel quand la température du moteur et la température extérieure sont corrélées de façon très négative. Une fois l’analyse PCA exécutée, nous utilisons l’algorithme de détection d’anomalies PCA pour capturer les anomalies. 

Lors de l’apprentissage de l’un des modèles, les données normales sont utilisées comme données d’entrée pour l’apprentissage du modèle de détection d’anomalies basé sur PCA. (Les données normales ne nécessitent pas de maintenance ou de rappel.) Dans l’expérience d’évaluation, nous utilisons le modèle de détection d’anomalies formé pour déterminer si le véhicule nécessite une maintenance ou un rappel. 

### <a name="real-time-analysis"></a>Analyse en temps réel
Nous utilisons la requête SQL Stream Analytics suivante pour obtenir la moyenne de tous les paramètres importants des véhicules. Ces paramètres incluent la vitesse du véhicule, le niveau de carburant, la température du moteur, le kilométrage, la pression des pneus, le niveau d’huile moteur, et autres. Les moyennes servent à détecter des anomalies, à émettre des alertes et à déterminer les conditions d’intégrité globale des véhicules utilisés dans une région spécifique. Les moyennes sont ensuite corrélées à des données démographiques. 

![Requête Stream Analytics pour le traitement en temps réel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Toutes les moyennes sont calculées sur une fenêtre bascule de trois secondes. Nous utilisons une fenêtre bascule, car des intervalles de temps contigus et sans chevauchement sont nécessaires. 

Pour en savoir plus sur les fonctionnalités de fenêtrage dans Stream Analytics, consultez [Fenêtrage (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Prédiction en temps réel**

Une application est incluse dans le cadre de la solution pour configurer le modèle d’apprentissage automatique en temps réel. L’application RealTimeDashboardApp est créée et configurée dans le cadre du déploiement de la solution. L’application :

* Écoute une instance de hub d’événements dans laquelle Stream Analytics publie les événements en continu.

    ![Requête Stream Analytics pour la publication des données](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Reçoit des événements. Pour chaque événement reçu par cette application : 
   
   * Les données sont traitées à l’aide d’un point de terminaison Request-Response Scoring (RRS) d’apprentissage automatique. Le point de terminaison RRS est automatiquement publié dans le cadre du déploiement.
   * La sortie RRS est publiée dans un ensemble de données Power BI à l’aide des API Push.

Ce modèle s’applique également aux scénarios dans lesquels vous souhaitez intégrer une application métier avec le flux d’analytique en temps réel. Ces scénarios incluent les alertes, les notifications et la messagerie.

Pour télécharger la solution RealtimeDashboardApp Visual Studio pour les personnalisations, consultez la page web de [téléchargement de RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078). 

#### <a name="execute-the-real-time-dashboard-application"></a>**Exécuter l’application de tableau de bord en temps réel**
1. Effectuez l’extraction de RealtimeDashboardApp et enregistrez-la localement.

    ![Dossier RealTimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Exécutez l’application RealtimeDashboardApp.exe.

3. Entrez vos informations d’identification valides pour Power BI et sélectionnez **Se connecter**.  

    ![Fenêtre de connexion à l’application de tableau de bord en temps réel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Sélectionnez **Accepter**.

    ![Dernière fenêtre de connexion à l’application de tableau de bord en temps réel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Si vous voulez vider le jeu de données Power BI, exécutez l’application RealtimeDashboardApp avec le paramètre « flushdata ». 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Analyse en mode batch
Le but est ici d’illustrer comment Contoso Motors utilise les fonctionnalités de calcul d’Azure pour exploiter des données volumineuses. Ces données donnent un éclairage approfondi quant aux modes de conduite, au comportement d’utilisation et à l’intégrité des véhicules. Ces informations permettent :

* D’améliorer l’expérience client à moindre coût en offrant des perspectives sur les habitudes de conduite et sur les comportements de conduite économes en carburant.
* De s’informer de manière proactive sur les clients et leurs modes de conduite afin d’orienter les décisions commerciales et de fournir des produits et services de meilleure qualité.

Dans cette solution, nous ciblons les métriques suivantes :

* **Comportement de conduite agressive** : identifie la tendance des modèles, des emplacements, des conditions de conduite et de la période de l’année pour fournir des informations sur les modes de conduite agressive. Contoso Motors peut utiliser ces informations pour ses campagnes marketing, pour introduire de nouvelles fonctionnalités personnalisées et pour proposer des assurances adaptées à l’utilisation.
* **Comportement de conduite économe en carburant** : identifie la tendance des modèles, des emplacements, des conditions de conduite et de la période de l’année pour fournir des informations sur les modes de conduite agressive. Contoso Motors peut utiliser ces informations pour ses campagnes marketing, et pour proposer aux conducteurs de nouvelles fonctionnalités et des rapports proactifs afin d’encourager les habitudes de conduite économique et respectueuses de l’environnement.
* **Modèles de rappel** : identifie les modèles nécessitant des rappels en améliorant l’expérience d’apprentissage automatique pour la détection des anomalies.

Examinons en détail chacune de ces métriques.

#### <a name="aggressive-driving-behavior-patterns"></a>**Comportements de conduite agressive**

Les signaux et les données de diagnostic de véhicules partitionnés sont traités dans AggresiveDrivingPatternPipeline, comme indiqué dans le flux de travail ci-dessous. Hive est utilisé pour déterminer les modèles, l’emplacement, le véhicule, les conditions de conduite et d’autres paramètres dénotant un mode de conduite agressive.

![Flux de travail de mode de conduite agressive](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Requête Hive de mode de conduite agressive***

Nous utilisons le script Hive aggresivedriving.hql pour analyser des modes de condition de conduite agressive. Il se trouve dans le dossier \demo\src\connectedcar\scripts du fichier zip téléchargé. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


Le script utilise à la fois la position du levier de vitesses, l’état de la pédale de frein et la vitesse du véhicule pour détecter un comportement de conduite imprudent/agressif en fonction des schémas de freinage à vitesse élevée. 

Une fois le pipeline exécuté, les partitions suivantes sont générées dans votre compte de stockage sous le conteneur connectedcar :

![Sortie AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Modes de comportement de conduite économe en carburant**

Les signaux et les données de diagnostic de véhicules partitionnés sont traités dans FuelEfficientDrivingPatternPipeline, comme indiqué dans le flux de travail ci-dessous. Nous utilisons Hive pour déterminer les modèles, l’emplacement, le véhicule, les conditions de conduite et d’autres propriétés dénotant des modes de conduite économe en carburant.

![Modes de conduite économe en carburant](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Requête Hive du mode de conduite économe en carburant***

Nous utilisons le script Hive fuelefficientdriving.hql pour analyser des modes de condition de conduite économe en carburant. Il se trouve dans le dossier \demo\src\connectedcar\scripts du fichier zip téléchargé. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


Le script utilise à la fois la position du levier de vitesses, l’état de la pédale de frein, la vitesse du véhicule et la position de la pédale d’accélérateur pour détecter un comportement de conduite économe en carburant en fonction des schémas d’accélération, de freinage et de vitesse élevée. 

Une fois le pipeline exécuté, les partitions suivantes sont générées dans votre compte de stockage sous le conteneur connectedcar :

![Sortie FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Prédictions de modèle de rappel**

L’expérience d’apprentissage automatique est configurée et publiée en tant que service web dans le cadre du déploiement de la solution. Le point de terminaison de calcul de score du lot est utilisé dans ce flux de travail. Il est inscrit en tant que service lié de fabrique de données et opérationnalisé à l’aide de l’activité de calcul de score du lot de fabrique de données.

![Point de terminaison d’apprentissage automatique](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Le service lié inscrit est utilisé dans DetectAnomalyPipeline pour noter les données à l’aide du modèle de détection d’anomalies. 

![Activité de calcul de score du lot d’apprentissage automatique dans la fabrique de données](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Quelques étapes sont exécutées dans ce pipeline pour préparer les données afin de les rendre opérationnelles avec le service web de calcul de score du lot. 

![DetectAnomalyPipeline pour la prédiction de rappel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Requête Hive de détection des anomalies***

Une fois la notation terminée, une activité HDInsight traite et regroupe les données que le modèle a classées comme des anomalies. Le modèle utilise un score de probabilité de 0,60 ou plus.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Une fois le pipeline exécuté, les partitions suivantes sont générées dans votre compte de stockage sous le conteneur connectedcar :

![Sortie DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publish

### <a name="real-time-analysis"></a>Analyse en temps réel
L’une des requêtes de la tâche Stream Analytics publie les événements dans une instance de hub d’événements de sortie. 

![Tâche Stream Analytics publiée dans une instance de hub d’événements de sortie](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

La requête Stream Analytics suivante est utilisée pour la publication dans une instance de hub d’événements de sortie :

![Requête Stream Analytics pour publier dans une instance de hub d’événements de sortie](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Ce flux d’événements est consommé par l’application RealTimeDashboardApp intégrée dans la solution. Cette application utilise le service web de requête-réponse d’apprentissage automatique pour calculer les scores en temps réel. Elle publie les données résultantes dans un jeu de données Power BI pour la consommation. 

### <a name="batch-analysis"></a>Analyse en mode batch
Les résultats du traitement par lots et en temps réel sont publiés dans les tables Azure SQL Database pour être consommés. Le serveur SQL, la base de données et les tables sont créés automatiquement dans le cadre du script d’installation. 

Les résultats du traitement par lots sont copiés dans le flux de travail du Mini-Data Warehouse.

![Résultats du traitement par lots copiés dans le flux de travail du Mini-Data Warehouse](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

La tâche Stream Analytics est publiée dans le Mini-Data Warehouse.

![Tâche Stream Analytics publiée dans le Mini-Data Warehouse](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Le paramètre de Mini-Data Warehouse se trouve dans la tâche Stream Analytics.

![Configuration de l’entrepôt de données dans la tâche Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Utiliser
Power BI offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives. 

Le tableau de bord final doit ressembler à cet exemple :

![Tableau de bord Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Résumé
Ce document explore de façon détaillée la solution Vehicle Telemetry Analytics. Le modèle d’architecture lambda est utilisé pour une analyse en temps réel et par lots reposant sur des prédictions et des actions. Ce modèle s’applique à un large éventail de scénarios qui requièrent des analyses à chaud (en temps réel) et à froid (par lots). 

### <a name="references"></a>Références

* [Solution Vehicle Telematics Simulator Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [SDK Azure Event Hubs pour l’ingestion de flux](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Fonctionnalités de déplacement de données d’Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Activité .NET Azure Data Factory](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Solution Visual Studio d’activité .NET Azure Data Factory utilisée pour la préparation des exemples de données](http://go.microsoft.com/fwlink/?LinkId=717077) 
