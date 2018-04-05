---
title: Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de diagnostic | Microsoft Docs
description: Découvrez comment analyser les journaux de diagnostic à partir de travaux Stream Analytics dans Microsoft Azure.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 164d522d7beaea222dbc408765877fa67a34c203
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de diagnostic

Il arrive parfois qu’un travail Azure Stream Analytics s’arrête de manière inattendue. Il est important d’être en mesure de résoudre ce type de problème. Le problème peut être lié à un résultat de requête inattendu, à une mauvaise connectivité aux périphériques ou à un arrêt inattendu du service. Les journaux de diagnostic dans Stream Analytics peuvent vous aider à identifier la cause des problèmes lorsqu’ils surviennent et à réduire le délai de récupération.

## <a name="log-types"></a>Types de journaux

Stream Analytics fournit deux types de journaux : 
* Les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (activés en permanence). Les journaux d’activité fournissent des détails sur les opérations effectuées sur les travaux.
* Les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurables). Les journaux de diagnostic offrent des informations plus détaillées sur tous les événements liés à un travail. Les journaux de diagnostic enregistrent les événements entre le moment où le travail est créé et celui où le travail est supprimé. Ils consignent les événements qui se produisent lorsque le travail est mis à jour et lors de son exécution.

> [!NOTE]
> Vous pouvez utiliser des services tels que le stockage Azure, les concentrateurs d’événements Azure et Azure Log Analytics pour analyser les données non conformes. Les frais qui vous seront facturés varient selon le modèle de tarification de chaque service.
>

## <a name="turn-on-diagnostics-logs"></a>Activer les journaux de diagnostic

Les journaux de diagnostic sont **désactivés** par défaut. Pour activer les journaux de diagnostic, procédez comme suit :

1.  Connectez-vous au portail Azure et accédez au panneau du travail de streaming. Sous **Surveillance**, sélectionnez **Journaux de diagnostic**.

    ![Navigation dans le panneau jusqu’aux journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Sélectionnez **Activer les diagnostics**.

    ![Activer les journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  Sur la page **Paramètres de diagnostic**, définissez le paramètre **État** sur **Activé**.

    ![Modifier l’état pour les journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Configurez la cible d’archivage (compte de stockage, concentrateur d’événements, Log Analytics) de votre choix. Sélectionnez ensuite les catégories de journaux à collecter (Exécution, Création). 

5.  Enregistrez la nouvelle configuration de diagnostic.

Celle-ci prend effet dans un délai de 10 minutes environ. Après cela, les journaux commencent à apparaître dans la cible d’archivage configurée (vous les retrouvez sur la page **Journaux de diagnostics**) :

![Navigation dans le panneau jusqu’aux journaux de diagnostic - cibles d’archivage](./media/stream-analytics-job-diagnostic-logs/image4.png)

Pour en savoir plus sur la configuration de diagnostic, consultez l’article [Collecte et utilisation des données de diagnostic à partir de vos ressources Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Catégories de journaux de diagnostic

Deux catégories de journaux de diagnostic sont actuellement disponibles :

* **Création** : capture les événements de journal liés aux opérations de création de travaux (création du travail, ajout et suppression d’entrées et de sorties, ajout et mise à jour de la requête, démarrage et arrêt du travail).
* **Exécution** : capture les événements qui se produisent pendant l’exécution du travail :
    * Erreurs de connectivité
    * Erreurs de traitement des données, notamment :
        * Événements non conformes à la définition de la requête (types et valeurs de champs ne correspondant pas, champs manquants, etc.)
        * Erreurs d’évaluation d’expression
    * Autres erreurs et événements

## <a name="diagnostics-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux sont stockés au format JSON. Chaque entrée comprend les champs de chaîne courants suivants :

NOM | Description
------- | -------
time | L’horodatage (heure UTC) du journal.
ResourceId | L’ID de la ressource sur laquelle l’opération a eu lieu, en majuscules. Comprend l’ID d’abonnement, le groupe de ressources et le nom du travail. Par exemple, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | La catégorie de journal, **Exécution** ou **Création**.
operationName | Le nom de l’opération qui est journalisée. Par exemple, **Événements d’envoi : Échec d’écriture de la sortie SQL sur mysqloutput**.
status | État de l’opération. Par exemple, **Échec** ou **Réussite**.
level | Le niveau du journal. Par exemple, **Erreur**, **Avertissement** ou **Informations**.
properties | Détail spécifique de l’entrée du journal, sérialisé comme chaîne JSON. Pour plus d’informations, consultez les sections suivantes.

### <a name="execution-log-properties-schema"></a>Schéma de propriétés des journaux d’exécution

Les journaux d’exécution contiennent des informations sur les événements qui se sont produits pendant l’exécution du travail Stream Analytics. Le schéma de propriétés varie selon le type d’événement. Les types de journaux d’exécution suivants sont actuellement disponibles :

### <a name="data-errors"></a>Erreurs de données

Toute erreur qui se produit lorsque le travail traite des données est consignée dans cette catégorie de journaux. La plupart du temps, ces journaux sont créés au cours des opérations de lecture, de sérialisation et d’écriture des données. Ces journaux n’incluent pas les erreurs de connectivité. Les erreurs de connectivité sont traitées comme des événements génériques.

NOM | Description
------- | -------
Source | Nom de l’entrée ou de la sortie du travail où l’erreur s’est produite.
Message | Message associé à l’erreur.
type | Le type d’erreur. Par exemple, **DataConversionError**, **CsvParserError** ou **ServiceBusPropertyColumnMissingError**.
Données | Contient des données utiles pour localiser avec précision la source de l’erreur. Troncation possible en fonction de la taille.

En fonction de la valeur **operationName**, les erreurs de données ont le schéma suivant :
* **Événements de sérialisation** : les événements de sérialisation se produisent pendant les opérations de lecture d’événements, lorsque les données en entrée ne répondent pas aux conditions du schéma de requête pour l’une des raisons suivantes :
    * *Incompatibilité de type pendant la (dé)sérialisation de l’événement* : identifie le champ à l’origine de l’erreur.
    * *Impossible de lire un événement, sérialisation non valide* : fournit des informations sur l’emplacement où l’erreur s’est produite dans les données d’entrée (nom d’objet blob pour une entrée d’objet blob, décalage et exemple de données).
* **Événements d’envoi** : les événements d’envoi se produisent pendant les opérations d’écriture. Ils identifient l’événement de streaming à l’origine de l’erreur.

### <a name="generic-events"></a>Événements génériques

Les événements génériques couvrent tout le reste.

NOM | Description
-------- | --------
Error | (facultatif) Informations sur l’erreur, en général des informations sur l’exception si celles-ci sont disponibles.
Message| Message de journal.
type | Type de message, correspond à la catégorisation interne des erreurs. Par exemple, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de corrélation : | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) qui identifie de façon unique l’exécution du travail. Toutes les entrées du journal d’exécution générées depuis le démarrage du travail jusqu’à son arrêt ont le même **ID de corrélation**.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
