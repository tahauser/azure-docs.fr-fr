---
title: "Azure Stream Analytics sur IoT Edge (version préliminaire)"
description: "Créez des tâches de périphérie dans Azure Stream Analytics et déployez-les sur des appareils exécutant Azure IoT Edge."
keywords: "flux de données,IoT, Edge"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/16/2017
ms.author: jeanb
ms.openlocfilehash: f1ff8d6f64a04ab03c8170fd2b6a7c881227da2e
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics sur IoT Edge (version préliminaire)

> [!IMPORTANT]
> Cette fonctionnalité n’existe qu’en version préliminaire. Nous ne recommandons pas une utilisation en production.
 
Azure Stream Analytics (ASA) sur IoT Edge encourage les développeurs à déployer une intelligence analytique quasiment en temps réel plus proche des appareils IoT pour leur permettre de déverrouiller la valeur complète des données générées par l’appareil. Conçu pour une latence faible, une résilience, une utilisation efficace de la bande passante et la conformité, les entreprises peuvent désormais déployer la logique de contrôle proche des opérations industrielles et compléter l’analytique du Big Data effectuée dans le cloud.  
Azure Stream Analytics sur IoT Edge s’exécute dans le framework [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Une fois que le travail est créé dans ASA, déployez et gérez des travaux ASA à l’aide de IoT Hub.
Cette fonctionnalité est en préversion, si vous avez des questions ou des commentaires, vous pouvez utiliser [ce questionnaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) pour contacter l’équipe produit. 

## <a name="scenarios"></a>Scénarios
![Diagramme général](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Contrôle et commande de faible latence**: par exemple, les systèmes de sécurité de fabrication doivent répondre aux données opérationnelles avec une latence très faible. Avec ASA sur IoT Edge, vous pouvez analyser les données de capteur quasiment en temps réel et émettre des commandes lorsque vous détectez des anomalies pour arrêter une machine ou déclencher des alertes.
*   **Connectivité au cloud limitée** : les systèmes stratégiques, tels que les équipements miniers à distance, les navires connectés ou les installations de forage offshore, ont besoin d’analyser les données et d’y réagir, même lorsque la connectivité au cloud est intermittente. Avec ASA, votre logique de diffusion en continu s’exécute indépendamment de la connectivité réseau et vous pouvez choisir ce que vous envoyez sur le cloud pour un traitement ultérieur ou pour y être stocké.
* **Bande passante limitée** : le volume de données produites par les moteurs à réaction ou par les voitures connectées peut être tellement important que les données doivent être filtrées ou traitées au préalable avant d’être envoyées vers le cloud. À l’aide de ASA, vous pouvez filtrer ou agréger les données qui doivent être envoyés vers le cloud.
* **Conformité** : pour obtenir une conformité réglementaire, certaines données peuvent être rendues anonymes localement ou agrégées avant d’être envoyés vers le cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Tâches de périphérie dans Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Qu’est-ce qu’une tâche de périphérie ?

Les tâches ASA Edge s’exécutent sous forme de modules dans le [runtime Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Elles sont composées de deux parties :
1.  Une partie cloud qui est responsable de la définition de tâche : les utilisateurs définissent des entrées, des sorties, des requêtes et d’autres paramètres (événements en désordre, etc.) dans le cloud.
2.  Le module ASA sur IoT Edge qui s’exécute localement. Il contient le moteur de traitement des événements complexes ASA et reçoit la définition de tâche à partir du cloud. 

ASA utilise IoT Hub pour déployer des tâches de périphérie sur les périphériques. Vous obtiendrez plus d’informations sur le [déploiement IoT Edge ici](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Tâche de périphérie](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Instructions d’installation
La procédure générale est décrite dans le tableau suivant. Vous obtiendrez plus de détails dans les sections suivantes.
|      |Étape   | Emplacement     | Notes   |
| ---   | ---   | ---       |  ---      |
| 1   | **Créer une tâche ASA Edge**   | Portail Azure      |  Créez une nouvelle tâche, sélectionnez **Edge** en tant qu’**environnement d’hébergement**. <br> Ces tâches sont créées/gérées à partir du cloud et s’exécutent sur vos propres appareils IoT Edge.     |
| 2   | **Créer un conteneur de stockage**   | Portail Azure       | Les conteneurs de stockage sont utilisés pour enregistrer votre définition de tâche, là où ils sont accessibles par vos appareils IoT. <br>  Vous pouvez réutiliser un conteneur de stockage existant.     |
| 3   | **Configurer votre environnement IoT Edge sur vos appareils**   | Appareil(s)      | Instructions pour [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Déployer ASA sur vos appareils IoT Edge**   | Portail Azure      |  La définition de tâche ASA est exportée vers le conteneur de stockage créé précédemment.       |
Vous pouvez suivre [ce didacticiel pas à pas](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) pour déployer votre première tâche ASA sur IoT Edge. La vidéo suivante permet de comprendre le processus d’exécution d’une tâche Stream Analytics sur un appareil IoT Edge :  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]



#### <a name="create-an-asa-edge-job"></a>Créer une tâche ASA Edge
1. Depuis le portail Azure, créez une nouvelle tâche Stream Analytics. [Lien direct pour créer une nouvelle tâche ASA](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

> [!Note]
> Vous pouvez créer des tâches Edge dans toutes les régions prises en charge par ASA, **sauf dans la région « États-Unis de l’Ouest 2 »**.
> Cette limitation sera supprimée sous peu.

2. Dans l’écran de création, sélectionnez **Edge** en tant qu’**environnement d’hébergement** (voir l’image suivante) ![Création de la tâche](media/stream-analytics-edge/ASAEdge_create.png)
3. Définition de la tâche
    1. **Définir le(s) flux d’entrée**. Définissez un ou plusieurs flux d’entrée pour votre tâche.
    2. Définissez les données de référence (facultatif).
    3. **Définir le(s) flux de sortie**. Définissez un ou plusieurs flux de sortie pour votre tâche. 
    4. **Définir la requête**. Définissez la requête ASA dans le cloud à l’aide de l’éditeur inclus. Le compilateur vérifie automatiquement la syntaxe prise en charge par ASA Edge. Vous pouvez également tester votre requête en téléchargeant des exemples de données. 
4. Définir des paramètres facultatifs
    1. **Ordre des événements**. Vous pouvez configurer une stratégie d’arrivée en désordre dans le portail. La documentation est disponible [ici](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Paramètres régionaux**. Définissez le format d’internationalisation.


#### <a name="create-a-storage-container"></a>Créer un conteneur de stockage
Un conteneur de stockage est nécessaire pour exporter la requête ASA compilée et la configuration de tâche. Il est utilisé pour configurer l’image ASA Docker avec votre requête spécifique. 
1. Suivez ces [instructions](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) pour créer un compte de stockage à partir du portail Azure. Vous pouvez conserver toutes les options par défaut pour utiliser ce compte avec ASA.
2. Dans le compte de stockage nouvellement créé, créez un conteneur de stockage d’objets blob :
    1. Cliquez sur « BLOB », puis « + conteneur ». 
    2. Entrez un nom et conservez le conteneur en tant que « Privé ».


> [!Note]
> Lors de la création d’un déploiement, ASA exporte la définition de tâche dans un conteneur de stockage. Cette définition de tâche reste la même pendant la durée d’un déploiement. Par conséquent, si vous souhaitez mettre à jour une tâche en cours d’exécution en périphérie, vous devez modifier la tâche dans ASA, puis créer un nouveau déploiement dans IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurer votre environnement IoT Edge sur vos appareils
Les tâches de périphérie peuvent être déployées sur les appareils exécutant Azure IoT Edge.
Pour ce faire, vous devez procéder comme suit :
- Créez un Iot Hub ;
- Installez le runtime Docker et IoT Edge sur vos appareils de périphérie ;
- Définissez vos appareils comme « Appareils IoT Edge » dans le IoT Hub.

Ces étapes sont décrites dans la documentation IoT Edge pour [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Déployer ASA sur vos appareils IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Ajouter ASA à votre déploiement
- Dans le portail Azure, ouvrez IoT Hub, accédez à l’explorateur IoT Edge et ouvrez le panneau des appareils.
- Sélectionnez **Définir modules** puis **Importer le module IoT Edge d’Azure Service**.
- Sélectionnez l’abonnement et la tâche ASA Edge que vous avez créée. Sélectionnez ensuite votre compte de stockage. Cliquez sur Enregistrer.
![Ajouter un module ASA dans votre déploiement](media/stream-analytics-edge/set_module.png)


> [!Note]
> Au cours de cette étape, ASA demande l’accès au conteneur de stockage sélectionné, puis crée un dossier nommé « EdgeJobs ». Pour chaque déploiement, un nouveau sous-dossier est créé dans le dossier « EdgeJobs ».
> Pour déployer votre projet sur les appareils en périphérie, ASA crée une signature d’accès partagé (SAP) pour le fichier de définition de tâche. La clé SAP est transmise de façon sécurisée aux appareils IoT Edge à l’aide de jumeaux d’appareil. La durée avant expiration de cette clé est de trois ans à partir du jour de sa création.


Pour plus d’informations sur les déploiements IoT Edge, consultez [cette page](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurer des itinéraires
IoT Edge offre un moyen de router les messages entre les modules et entre les modules et IoT Hub de façon déclarative. La syntaxe complète est décrite [ici](https://docs.microsoft.com/azure/iot-edge/module-composition).
Les noms des entrées et sorties créés dans la tâche ASA peuvent être utilisés en tant que points de terminaison pour le routage.  

###### <a name="example"></a>Exemple
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Cet exemple montre les itinéraires pour le scénario décrit dans l’image suivante. Il contient une tâche de périphérie appelée **ASA**, une entrée nommée **temperature** et une sortie nommée **alert**.
![Exemple de routage](media/stream-analytics-edge/RoutingExample.png)

Cet exemple définit les itinéraires suivants :
- Tous les messages du module **tempSensor** sont envoyés au module nommé **ASA** à l’entrée nommée **temperature**.
- Toutes les sorties du module **ASA** sont envoyées au IoT Hub lié à cet appareil ($upstream).
- Toutes les sorties du module **ASA** sont envoyées au point de terminaison **control** de **tempSensor**.


## <a name="technical-information"></a>Informations techniques
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Limitations actuelles pour les tâches de périphérie par rapport aux tâches cloud
L’objectif est d’obtenir la parité entre les tâches de périphérie et les tâches cloud. La plupart des fonctionnalités de notre langage de requête SQL sont déjà prises en charge.
Cependant, les fonctionnalités suivantes ne sont pas encore prises en charge pour les tâches de périphérie :
* Fonctions définies par l’utilisateur (UDF) et agrégats définis par l’utilisateur (UDA).
* Fonctions Azure ML.
* Utilisation de plus de 14 agrégats dans une seule étape.
* Format AVRO pour l’entrée/sortie. À ce stade, seuls les formats CSV et JSON sont pris en charge.
* Compression d’entrée JSON.
* Les opérateurs SQL suivants :
    * AnomalyDetection
    * Opérateurs géospatiaux :
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Runtime et conditions matérielles requises
Pour exécuter ASA sur IoT Edge, vous avez besoin d’appareils pouvant exécuter [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA et Azure IoT Edge utilisent des conteneurs **Docker** pour fournir une solution portable s’exécutant sur plusieurs systèmes d’exploitation hôtes (Windows, Linux).

ASA sur IoT Edge est mis à disposition en tant qu’image Windows et Linux, s’exécutant sur les architectures x86-64 ou ARM. 


### <a name="input-and-output"></a>Entrée et sortie
#### <a name="input-and-output-streams"></a>Flux d’entrée et de sortie
Les tâches ASA Edge peuvent obtenir des entrées et sorties à partir d’autres modules qui s’exécutent sur des appareils IoT Edge. Pour vous connecter à partir de modules spécifiques et à ces derniers, vous pouvez définir la configuration de routage au moment du déploiement. Pour plus d’informations, consultez [la documentation de composition du module IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Les formats CSV et JSON sont pris en charge pour les entrées et sorties.

Pour chaque flux d’entrée et de sortie que vous créez dans votre tâche ASA, un point de terminaison correspondant est créé dans votre module déployé. Ces points de terminaison sont utilisables dans les itinéraires de votre déploiement.



##### <a name="reference-data"></a>Données de référence
Les données de référence (également appelées « tables de choix ») sont un jeu de données finies, statiques ou variant lentement au fil du temps par nature. Elles sont utilisées pour effectuer des recherches ou pour effectuer des mises en corrélation avec votre flux de données. Pour utiliser des données de référence dans votre tâche Azure Stream Analytics, vous utiliserez généralement une [jointure de données de référence](https://msdn.microsoft.com/library/azure/dn949258.aspx) dans votre requête. Pour plus d’informations, consultez la [documentation ASA sur les données de référence](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Pour pouvoir utiliser les données de référence pour ASA sur Iot Edge, vous devez procédez comme suit : 
1. Créez une nouvelle entrée pour votre tâche.
2. Choisissez **données de référence** pour le **type de source**.
3. Définissez le chemin d’accès du fichier. Le chemin d’accès du fichier doit être un chemin d’accès **absolu** pour la ![création de données de référence](media/stream-analytics-edge/ReferenceData.png) du périphérique.
4. Activez les **lecteurs partagés** dans votre configuration Docker et assurez-vous que le lecteur est activé avant de commencer votre déploiement.

Pour plus d’informations, consultez la [documentation Docker pour Windows](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> Seules les données de référence locales sont prises en charge pour le moment.




## <a name="license-and-third-party-notices"></a>Licence et mentions tierces
* [Azure Stream Analytics sur IoT Edge, licence de version d’évaluation](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Mentions tierces pour Azure Stream Analytics sur IoT Edge, version préliminaire](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>étapes suivantes

* [Plus d’informations sur Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Didacticiel pour ASA sur IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Envoyer des commentaires à l’équipe à l’aide de ce questionnaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
