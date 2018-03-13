---
title: "Comprendre le niveau de compatibilité pour les travaux Azure Stream Analytics. | Microsoft Docs"
description: "Apprenez à définir un niveau de compatibilité pour un travail Azure Stream Analytics et découvrez les principales modifications apportées au dernier niveau de compatibilité"
keywords: "Niveau de compatibilité, diffusion en continu de données"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/03/2018
ms.author: sngun
ms.openlocfilehash: f354c39fc3b366795fe4ed8dbeeb961bb11d5420
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Niveau de compatibilité pour les travaux Azure Stream Analytics
 
Le niveau de compatibilité fait référence aux comportements spécifiques à chaque version d’un service Azure Stream Analytics. Azure Stream Analytics est un service géré qui est régulièrement enrichi de nouvelles fonctionnalités et bénéficie d’améliorations de performances. En règle générale, les mises à jour sont automatiquement accessibles aux utilisateurs finaux. Toutefois, certaines nouvelles fonctionnalités peuvent introduire des modifications majeures, comme un changement de comportement sur un travail existant, une modification des processus qui consomment des données sur ces travaux, etc. Un niveau de compatibilité permet de représenter un changement majeur introduit dans Stream Analytics. Les changements majeurs sont toujours introduits avec un nouveau niveau de compatibilité. 

Le niveau de compatibilité permet de garantir la bonne exécution des travaux existants. Lorsque vous créez un nouveau travail Stream Analytics, il est recommandé d’utiliser le dernier niveau de compatibilité disponible. 
 
## <a name="set-a-compatibility-level"></a>Définir un niveau de compatibilité 

Le niveau de compatibilité contrôle le comportement d’exécution d’un travail Stream Analytics. Vous pouvez définir le niveau de compatibilité d’un travail Stream Analytics à l’aide du portail ou en appelant [l’API REST create job](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics prend actuellement en charge deux niveaux de compatibilité : « 1.0 » et « 1.1 ». Par défaut, le niveau de compatibilité est défini sur « 1.0 », qui a été introduit lors de la publication en disponibilité générale d’Azure Stream Analytics. Pour mettre à jour la valeur par défaut, accédez à votre travail Stream Analytics existant > sélectionnez l’option **Niveau de compatibilité** dans la section **Configurer** et modifiez la valeur. 

Veillez à interrompre le travail avant de mettre à jour le niveau de compatibilité. Si votre travail est en cours d’exécution, vous ne peut pas mettre à jour le niveau de compatibilité. 

![Niveau de compatibilité dans le portail](media\stream-analytics-compatibility-level/image1.png)

 
Lorsque vous mettez à jour le niveau de compatibilité, le compilateur T-SQL valide le travail avec la syntaxe correspondant au niveau de compatibilité sélectionné. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Principales modifications apportées au dernier niveau de compatibilité (1.1)

Voici les principales modifications introduites dans le niveau de compatibilité 1.1 :

* **Format XML de Service Bus**  

  * **versions précédentes :** Azure Stream Analytics utilisait DataContractSerializer, c’est pourquoi le contenu des messages incluait des balises XML. Par exemple : 
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ “SensorId”:”1”, “Temperature”:64\}\u0001 

  * **version actuelle :** le contenu du message renferme directement le flux sans aucune balise supplémentaire. Par exemple : 
  
   { “SensorId”:”1”, “Temperature”:64} 
 
* **Conservation de la casse pour les noms de champ**  

  * **versions précédentes :** les noms de champs passaient en minuscules au moment du traitement par le moteur Azure Stream Analytics. 

  * **version actuelle :** la casse des noms de champ est préservée pour les noms de champs lorsqu’ils sont traités par le moteur Azure Stream Analytics. 

  > [!NOTE] 
  > La conservation de la casse n’est pas encore disponible pour les tâches Stream Analytics hébergés à l’aide de l’environnement Edge. Par conséquent, tous les noms de champs sont convertis en minuscules si votre tâche est hébergée sur Edge. 

* **FloatNaNDeserializationDisabled**  

  * **versions précédentes :** la commande CREATE TABLE ne filtrait pas les événements avec une valeur NaN (Not-a-Number. Par exemple, Infinity, -Infinity) dans une colonne de type FLOAT, car ils étaient en dehors des limites documentées pour ces numéros.

  * **version actuelle :** la commande CREATE TABLE vous permet de spécifier un schéma fort. Le moteur Stream Analytics vérifie que les données sont conformes à ce schéma. Avec ce modèle, la commande peut filtrer des événements avec des valeurs NaN. 

* **Désactivation de l’upcast automatique pour les chaînes date/heure au format JSON.**  

  * **versions précédentes :** l’analyseur JSON upcastait automatiquement en type DateTime les valeurs de chaîne contenant des informations de type date/heure/fuseau horaire et les convertissait au format UTC. Cela entraînait la perte des informations relatives au fuseau horaire.

  * **version actuelle :** il n’existe plus aucun upcast automatique des valeurs de chaîne avec des informations de date/heure/fuseau horaire en type DateTime. Les informations relatives au fuseau horaire sont donc conservées. 

## <a name="next-steps"></a>Étapes suivantes
* [Guide de dépannage pour Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
* [Panneau Resource Health de Stream Analytics](stream-analytics-resource-health.md)