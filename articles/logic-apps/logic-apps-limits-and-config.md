---
title: Limites et configuration - Azure Logic Apps | Microsoft Docs
description: Limites de service et valeurs de configuration pour Azure Logic Apps
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 54a35607e107a09188373cc5f71bb3068b4c6bab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="logic-apps-limits-and-configuration"></a>Limites et configuration de Logic Apps

Cette rubrique décrit les limites et les détails de configuration actuels d’Azure Logic Apps.

## <a name="limits"></a>limites

### <a name="http-request-limits"></a>Limites de requête HTTP

Les limites pour un appel de requête ou de connecteur HTTP unique sont les suivantes :

#### <a name="timeout"></a>Délai d'expiration

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Délai d’expiration de la demande | 120 secondes | Un [modèle asynchrone](../logic-apps/logic-apps-create-api-app.md) ou une [boucle Until](logic-apps-control-flow-loops.md) peuvent compenser en fonction des besoins. | 
|||| 

#### <a name="message-size"></a>Taille des messages

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Taille des messages | 100 Mo | Certains connecteurs et certaines API peuvent ne pas prendre en charge 100 Mo. | 
| Limite d’évaluation des expressions | 131 072 caractères | `@concat()`, `@base64()` et `string` ne peuvent pas contenir plus de caractères. | 
|||| 

#### <a name="retry-policy"></a>Stratégie de nouvelle tentative

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Nouvelles tentatives | 90 | Valeur par défaut : 4. Vous pouvez le configurer avec le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Délai maximal avant nouvelle tentative | 1 jour | Vous pouvez le configurer avec le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Délai minimal avant nouvelle tentative | 5 secondes | Vous pouvez le configurer avec le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Durée d’exécution et rétention

Les limites pour l’exécution d’une application logique sont les suivantes :

| NOM | Limite | 
| ---- | ----- | 
| Durée d’exécution | 90 jours | 
| Rétention de stockage | 90 jours à compter de l’heure de début de l’exécution | 
| Intervalle de périodicité minimal | 1 seconde </br>Pour les applications logiques avec un plan App Service : 15 secondes | 
| Intervalle de périodicité maximal | 500 jours | 
||| 

Pour pouvoir dépasser les limites de durée d’exécution ou de rétention du stockage dans votre flux de traitement normal, [contactez l’équipe Logics Apps](mailto://logicappsemail@microsoft.com) afin de bénéficier d’une assistance adaptée à vos besoins.

### <a name="looping-and-debatching-limits"></a>Limites de bouclage et de décomposition

Les limites pour l’exécution d’une application logique sont les suivantes :

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Éléments ForEach | 100 000 | Vous pouvez utiliser [l’action de requête](../connectors/connectors-native-query.md) pour filtrer des tableaux plus grands au besoin. | 
| Itérations Until | 5 000 | | 
| Éléments SplitOn | 100 000 | | 
| Parallélisme ForEach | 50 | Valeur par défaut : 20. <p>Pour définir un niveau spécifique de parallélisme dans une boucle ForEach, définissez la propriété `runtimeConfiguration` dans l’action `foreach`. <p>Pour exécuter séquentiellement une boucle ForEach, donnez la valeur « Séquentiel » à la propriété `operationOptions` dans l’action `foreach`. | 
|||| 

### <a name="throughput-limits"></a>Limites de débit

Les limites pour une instance d’application logique sont les suivantes :

| NOM | Limite | Notes | 
| ----- | ----- | ----- | 
| Exécutions d’actions par tranche de 5 minutes | 100 000 | Pour augmenter la limite à 300 000, vous pouvez exécuter une application de logique en mode `High Througput`. Pour configurer le mode de débit élevé, sous `runtimeConfiguration` de la ressource de flux de travail, définissez la propriété `operationOptions` sur `OptimizedForHighThroughput`. <p>**Remarque** : le mode Débit élevé est en préversion. Vous pouvez également distribuer la charge de travail entre plusieurs applications si nécessaire. | 
| Appels sortants simultanés des actions | ~2,500 | Diminuer le nombre de demandes simultanées ou réduire la durée en fonction des besoins. | 
| Point de terminaison du runtime : appels entrants simultanés |~1,000 | Diminuer le nombre de demandes simultanées ou réduire la durée en fonction des besoins. | 
| Point de terminaison du runtime : appels de lecture toutes les cinq minutes  | 60 000 | Possibilité de distribuer la charge de travail entre plusieurs applications au besoin. | 
| Point de terminaison du runtime : appels d’invocation toutes les cinq minutes| 45,000 |Possibilité de distribuer la charge de travail entre plusieurs applications au besoin. | 
|||| 

Pour dépasser ces limites dans le cadre d’un traitement normal, ou exécuter un test de charge susceptible de les dépasser, [contactez l’équipe Logic Apps](mailto://logicappsemail@microsoft.com) afin de bénéficier d’une assistance adaptée à vos besoins.

### <a name="logic-app-definition-limits"></a>Limites de la définition d’application logique

Les limites pour la définition d’une application logique sont les suivantes :

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Actions par flux de travail | 500 | Pour étendre cette limite, vous pouvez au besoin ajouter des workflows imbriqués. |
| Niveaux d’imbrication d’actions autorisés | 8 | Pour étendre cette limite, vous pouvez au besoin ajouter des workflows imbriqués. | 
| Flux de travail par région et par abonnement | 1 000 | | 
| Déclencheurs par flux de travail | 10 | | 
| Limite de cas de basculement d’étendue | 25 | | 
| Nombre de variables par flux de travail | 250 | | 
| Caractères max par expression | 8 192 | | 
| Taille max de `trackedProperties` en caractères | 16 000 | 
| `action`/`trigger` | 80 | | 
| `description` | 256 | | 
| `parameters` limit | 50 | | 
| `outputs` limit | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Limites des connecteurs personnalisés

Ces limites s’appliquent à des connecteurs personnalisés qu’il est possible de créer à partir d’API Web.

| NOM | Limite | 
| ---- | ----- | 
| Nombre de connecteurs personnalisés qu’il est possible de créer | 1 000 par abonnement Azure | 
| Nombre de demandes par minute pour chaque connexion créée par un connecteur personnalisé | 500 demandes pour chaque connexion créée par le connecteur |
||| 

### <a name="integration-account-limits"></a>Limites du compte d’intégration

Les limites pour les artefacts que vous pouvez ajouter à un compte d’intégration sont les suivantes.

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Schéma | 8 Mo | Vous pouvez utiliser un [URI d’objet blob](../logic-apps/logic-apps-enterprise-integration-schemas.md) pour charger des fichiers supérieurs à 2 Mo. | 
| Mappage (fichier XSLT) | 2 Mo | | 
| Point de terminaison du runtime : appels de lecture toutes les cinq minutes | 60 000 | Possibilité de distribuer la charge de travail entre plusieurs comptes au besoin. | 
| Point de terminaison du runtime : appels d’invocation toutes les cinq minutes | 45,000 | Possibilité de distribuer la charge de travail entre plusieurs comptes au besoin. | 
| Point de terminaison du runtime : appels de suivi toutes les cinq minutes | 45,000 | Possibilité de distribuer la charge de travail entre plusieurs comptes au besoin. | 
| Point de terminaison du runtime : appels simultanés de blocage | ~1,000 | Diminuer le nombre de demandes simultanées ou réduire la durée en fonction des besoins. | 
|||| 

Ces limites s’appliquent au nombre d’artefacts qu’il est possible d’ajouter à un compte d’intégration.

#### <a name="free-pricing-tier"></a>Niveau de tarification gratuit

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Accords | 10 | | 
| Autres types d’artefacts | 25 | Les types d’artefact sont les suivants : partenaires, schémas, certificats et cartes. Chaque type peut disposer du nombre maximum d’artefacts. | 
|||| 

#### <a name="standard-pricing-tier"></a>Niveau de tarification Standard

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| N’importe quel type d’artefact | 500 | Les types d’artefacts sont les suivants : accords, partenaires, schémas, certificats et cartes. Chaque type peut disposer du nombre maximum d’artefacts. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Taille des messages des protocoles B2B (AS2, X12, EDIFACT)

Les limites qui s’appliquent aux protocoles B2B sont les suivantes :

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| AS2 | 50 Mo | S’applique au décodage et à l’encodage. | 
| X 12 | 50 Mo | S’applique au décodage et à l’encodage. | 
| EDIFACT | 50 Mo | S’applique au décodage et à l’encodage. | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuration : adresses IP

### <a name="logic-apps-service"></a>Service Logic Apps

Les appels émis directement par une application logique, c’est-à-dire par [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) ou autres requêtes HTTP, proviennent d’adresses IP de cette liste.

|Région Logic Apps|Adresse IP sortante|
|-----------------|-----------|
|Est de l’Australie|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sud-est de l’Australie|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sud du Brésil|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Centre du Canada|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Est du Canada|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Inde centrale|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Centre des États-Unis|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Est de l'Asie|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Est des États-Unis|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Est des États-Unis 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Est du Japon|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Ouest du Japon|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Centre-Nord des États-Unis|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europe du Nord|40.113.12.95, 52.178.165.215, 52.178.166.21|
|États-Unis - partie centrale méridionale|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Asie du Sud-Est|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Inde du Sud|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Centre-Ouest des États-Unis|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Europe de l'Ouest|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Inde occidentale|104.211.164.80, 104.211.162.205, 104.211.164.136|
|États-Unis de l’Ouest|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Ouest des États-Unis 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Sud du Royaume-Uni|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Ouest du Royaume-Uni|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Connecteurs

Les appels émis par des [connecteurs](../connectors/apis-list.md) proviennent des adresses IP de cette liste.

|Région Logic Apps|Adresse IP sortante|
|-----------------|-----------|
|Est de l’Australie|40.126.251.213|
|Sud-est de l’Australie|40.127.80.34|
|Sud du Brésil|191.232.38.129|
|Centre du Canada|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Est du Canada|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|Inde centrale|104.211.98.164|
|Centre des États-Unis|40.122.49.51|
|Est de l'Asie|23.99.116.181|
|Est des États-Unis|191.237.41.52|
|Est des États-Unis 2|104.208.233.100|
|Est du Japon|40.115.186.96|
|Ouest du Japon|40.74.130.77|
|Centre-Nord des États-Unis|65.52.218.230|
|Europe du Nord|104.45.93.9|
|États-Unis - partie centrale méridionale|104.214.70.191|
|Asie du Sud-Est|13.76.231.68|
|Inde du Sud|104.211.227.225|
|Europe de l'Ouest|40.115.50.13|
|Inde occidentale|104.211.161.203|
|États-Unis de l’Ouest|104.40.51.248|
|Sud du Royaume-Uni|51.140.80.51|
|Ouest du Royaume-Uni|51.141.47.105|
| | | 

## <a name="next-steps"></a>étapes suivantes  

* [Créez votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Exemples et scénarios courants](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Vidéo : Automatiser les processus d’entreprise avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Vidéo : Intégrer des systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)