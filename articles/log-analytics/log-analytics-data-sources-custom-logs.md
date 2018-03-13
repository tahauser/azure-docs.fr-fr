---
title: "Collecter les journaux personnalisés dans Azure Log Analytics | Microsoft Docs"
description: "Log Analytics peut collecter des événements dans des fichiers texte sur des ordinateurs Windows et Linux.  Cet article décrit comment définir un nouveau journal personnalisé et les détails des enregistrements qu’il crée dans l’espace de travail Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: bwren
ms.openlocfilehash: 401fbb39194a24721274f55f0fc2a4cdc235a32b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="custom-logs-in-log-analytics"></a>Journaux personnalisés dans Log Analytics
La source de données Journaux personnalisés de Log Analytics vous permet de collecter des événements stockés dans des fichiers texte sur les ordinateurs Windows et Linux. De nombreuses applications consignent des informations dans des fichiers texte au lieu des services de journalisation standard tels que le Journal des événements Windows ou Syslog.  Une fois collectés, vous pouvez analyser chaque enregistrement du journal dans des champs individuels à l’aide de la fonctionnalité [Champs Personnalisés](log-analytics-custom-fields.md) de Log Analytics.

![Collecte de journaux personnalisés](media/log-analytics-data-sources-custom-logs/overview.png)

Les fichiers journaux à collecter doivent correspondre aux critères suivants.

- Le journal doit comporter une seule entrée par ligne ou utiliser un horodatage correspondant à l’un des formats suivants au début de chaque entrée.

    AAAA-MM-JJ HH:MM:SS <br>M/J/AAAA HH:MM:SS AM/PM <br>Mois JJ,AAAA HH:MM:SS

- Le fichier journal ne doit pas autoriser les mises à jour circulaires, où de nouvelles entrées sont consignées.
- Le fichier journal doit utiliser l’encodage ASCII ou UTF-8.  Les autres formats, par exemple UTF-16, ne sont pas pris en charge.

>[!NOTE]
>Si le fichier journal contient des entrées dupliquées, Log Analytics les collecte.  Toutefois, les résultats de la recherche seront incohérents dans les cas où les résultats du filtre affichent un nombre d’événements supérieur au nombre de résultats.  Il est important de valider le journal pour déterminer si l’application qui le crée cause ce comportement et résoudre, si possible, le problème avant la création de la définition de collection de journal personnalisée.  
>
  
## <a name="defining-a-custom-log"></a>Définition d’un journal personnalisé
Utilisez la procédure suivante pour définir un fichier journal personnalisé.  Rendez-vous à la fin de cet article pour une procédure détaillée d’ajout d’un journal personnalisé.

### <a name="step-1-open-the-custom-log-wizard"></a>Étape 1. Ouvrir l’Assistant Journal personnalisé
L’Assistant Journal personnalisé s’exécute dans le portail Azure et vous permet de définir un nouveau journal personnalisé de collecte.

1. Dans le portail Azure, sélectionnez **Log Analytics** > votre espace de travail > **Paramètres avancés**.
2. Cliquez sur **Données** > **Journaux personnalisés**.
3. Par défaut, toutes les modifications de configuration sont automatiquement transmises à l’ensemble des agents.  Pour les agents Linux, un fichier de configuration est envoyé au collecteur de données Fluentd.  Si vous souhaitez modifier ce fichier manuellement sur chaque agent Linux, décochez la case *Appliquer la configuration ci-dessous à mes machines Linux*.
4. Cliquez sur **Ajouter+** pour ouvrir l’Assistant Journal personnalisé.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Étape 2. Télécharger et analyser un exemple de journal
Commencez par télécharger un exemple du journal personnalisé.  L’assistant analyse et affiche les entrées de ce fichier afin que vous les validiez.  Log Analytics utilise le délimiteur que vous spécifiez pour identifier chaque enregistrement.

**Nouvelle ligne** .  Si la ligne commence par un horodatage dans un des formats disponibles, vous pouvez spécifier un délimiteur **Horodatage** prenant en charge les entrées qui s’étendent sur plusieurs lignes.

Si un délimiteur Horodatage est utilisé, la propriété TimeGenerated de chaque enregistrement stocké dans Log Analytics contiendra la date et l’heure spécifiées pour cette entrée dans le fichier journal.  Si un délimiteur Nouvelle ligne est utilisé, la propriété TimeGenerated est renseignée avec la date et l’heure auxquelles Log Analytics a collecté l’entrée.


1. Cliquez sur **Parcourir** et accédez à un exemple de fichier.  Notez que ce bouton peut s’appeler **Choisir un fichier** dans certains navigateurs.
2. Cliquez sur **Suivant**.
3. L’Assistant Journal personnalisé télécharge le fichier et répertorie les enregistrements qu’il identifie.
4. Remplacez le délimiteur utilisé pour identifier un nouvel enregistrement par celui qui identifie au mieux les enregistrements de votre fichier journal.
5. Cliquez sur **Suivant**.

### <a name="step-3-add-log-collection-paths"></a>Étape 3. Ajouter des chemins de collecte de journaux
Vous devez définir un ou plusieurs chemins indiquant à l’agent où trouver le journal personnalisé.  Vous pouvez soit fournir le chemin d’accès et le nom du fichier journal, soit indiquer un chemin d’accès avec un caractère générique pour le nom.  Ce mécanisme prend en charge les applications qui créent un fichier par jour ou lorsqu’un fichier atteint une certaine taille.  Vous pouvez également fournir plusieurs chemins d’accès pour un fichier journal.

Par exemple, une application peut créer un fichier journal chaque jour avec la date dans le nom, comme dans log20100316.txt. Par exemple, ce modèle peut être *log\*.txt* et s’appliquer à un fichier journal conforme à la convention de dénomination de l’application.

Le tableau suivant fournit des exemples de modèles valides pour différents fichiers journaux.

| DESCRIPTION | path |
|:--- |:--- |
| Tous les fichiers sous *C:\Logs* avec l’extension .txt sur l’agent Windows |C:\Logs\\\*.txt |
| Tous les fichiers sous *C:\Logs* avec un nom commençant par log et portant l’extension .txt sur l’agent Windows |C:\Logs\log\*.txt |
| Tous les fichiers sous */var/log/audit* avec l’extension .txt sur l’agent Linux |/var/log/audit/*.txt |
| Tous les fichiers sous */var/log/audit* avec un nom commençant par log et portant l’extension .txt sur l’agent Linux |/var/log/audit/log\*.txt |

1. Sélectionnez Windows ou Linux pour spécifier le format du chemin d’accès que vous ajoutez.
2. Tapez le chemin d’accès et cliquez sur le bouton **+** .
3. Répétez la procédure pour les chemins d’accès supplémentaires.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Étape 4. Indiquer le nom et la description du journal
Le nom que vous spécifiez sera utilisé pour le type de journal, comme indiqué ci-dessus.  Il se termine toujours par _CL pour le distinguer d’un journal personnalisé.

1. Indiquez le nom du journal.  Le suffixe **\_CL** est ajouté automatiquement.
2. Si vous le souhaitez, renseignez le champ **Description**.
3. Cliquez sur **Suivant** pour enregistrer la définition du journal personnalisé.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Étape 5. Vérifier que les journaux personnalisés sont collectés
Il faut parfois une heure pour que les données initiales d’un nouveau journal personnalisé apparaissent dans Log Analytics.  La collecte des entrées commence par les journaux situés à l’emplacement spécifié, à partir du moment où vous avez défini le journal personnalisé.  Log Analytics ne conserve pas les entrées que vous avez téléchargées lors de la création du journal personnalisé, mais celles figurant dans les fichiers journaux qu’il trouve.

Lorsque Log Analytics commence la collecte du journal personnalisé, ses enregistrements sont accessibles en effectuant une recherche de journal.  Utilisez le nom que vous avez donné au journal personnalisé, comme le **Type** dans votre requête.

> [!NOTE]
> Si la propriété RawData est manquante dans la recherche, vous devrez peut-être fermer et rouvrir votre navigateur.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Étape 6. Analyser les entrées du journal personnalisé
L’entrée de journal est stockée dans une propriété unique appelée **RawData**.  Vous souhaiterez certainement séparer les différents éléments d’information de chaque entrée dans des propriétés séparées dans l’enregistrement.  Pour ce faire, utilisez la fonction [Champs personnalisés](log-analytics-custom-fields.md) de Log Analytics.

La procédure détaillée pour analyser l’entrée de journal personnalisé n’est pas décrite ici.  Pour plus d’informations sur cette procédure, consultez la documentation sur [Champs personnalisés](log-analytics-custom-fields.md) .

## <a name="removing-a-custom-log"></a>Suppression d’un journal personnalisé
Pour supprimer un journal personnalisé que vous avez défini précédemment, procédez comme suit dans le portail Azure.

1. Dans le menu **Données** des **Paramètres avancés** de votre espace de travail, sélectionnez **Journaux personnalisés** pour répertorier tous vos journaux personnalisés.
2. Cliquez sur **Supprimer** en regard du journal personnalisé à supprimer.


## <a name="data-collection"></a>Collecte des données
Log Analytics collecte les nouvelles entrées de chaque journal personnalisé toutes les 5 minutes environ.  L’agent enregistre sa place dans chaque fichier journal dont il la collecte.  Si l’agent est hors connexion pendant un moment, Log Analytics collecte les entrées à partir de là où il s’était arrêté, même si ces entrées ont été créées lorsque l’agent était hors connexion.

L’entrée de journal est intégralement inscrite dans une propriété unique appelée **RawData**.  Vous pouvez l’analyser dans plusieurs propriétés que vous pouvez analyser et interroger, en définissant [Champs personnalisés](log-analytics-custom-fields.md) après avoir créé le journal personnalisé.

## <a name="custom-log-record-properties"></a>Propriétés d’enregistrement de journal personnalisé
Les enregistrements de journal personnalisé sont caractérisés par le nom du journal que vous fournissez et les propriétés dans le tableau suivant.

| Propriété | DESCRIPTION |
|:--- |:--- |
| TimeGenerated |Date et heure auxquelles l’enregistrement a été collecté par Log Analytics.  Si le journal utilise un délimiteur basé sur l’heure, il s’agit de l’heure collectée à partir de l’entrée. |
| SourceSystem |Type d’agent auprès duquel l’enregistrement a été collecté. <br> Ops Manager : Agent Windows. Connexion directe ou System Center Operations Manager <br> Linux – Tous les agents Linux |
| RawData |Texte complet de l’entrée collectée. |
| ManagementGroupName |Nom du groupe d’administration pour les agents System Center Operations Manage  Pour les autres agents, il s’agit d’AOI-\<workspace ID\> |

## <a name="log-searches-with-custom-log-records"></a>Recherches de journal avec des enregistrements de journal personnalisé
Les enregistrements de journaux personnalisés sont stockés dans l’espace de travail Log Analytics, comme les enregistrements d’autres sources de données.  Leur type correspond au nom que vous fournissez lorsque vous définissez le journal. Vous pouvez donc utiliser la propriété Type dans votre recherche pour récupérer les enregistrements collectés dans un journal spécifique.

Le tableau suivant fournit plusieurs exemples de recherches qui extraient des enregistrements de journaux personnalisés.

| Requête | DESCRIPTION |
|:--- |:--- |
| MyApp_CL |Tous les événements du fichier journal nommé MyApp_CL. |
| MyApp_CL &#124; where Severity_CF=="error" |Tous les événements d’un journal personnalisé nommé MyApp_CL avec la valeur *error* dans le champ personnalisé nommé *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exemple de procédure d’ajout d’un journal personnalisé
La section suivante décrit la procédure complète de création d’un champ personnalisé.  L’exemple de journal collecté comporte une seule entrée sur chaque ligne commençant par une date et une heure, suivie de plusieurs champs (code, état et message) séparés par des virgules.  Plusieurs exemples d’entrée sont présentés ci-dessous.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Télécharger et analyser un exemple de journal
Nous fournissons un des fichiers journaux et nous voyons les événements qu’il va collecter.  Dans ce cas, le délimiteur Nouvelle ligne suffit.  Cependant, si une entrée du journal s’étend sur plusieurs lignes, il faut utiliser un délimiteur Horodatage.

![Télécharger et analyser un exemple de journal](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Ajouter des chemins de collecte de journaux
Les fichiers journaux se situeront dans *C:\MyApp\Logs*.  Un fichier sera créé chaque jour avec un nom comprenant la date au format *appAAAAMMJJ.log*.  Le format adapté à ce fichier journal est *C:\MyApp\Logs\\\\*.log*.

![Chemin de collecte de journaux](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Indiquer le nom et la description du journal
Nous utilisons le nom *MyApp_CL* et complétons le champ **Description**.

![Nom du journal](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Vérifier que les journaux personnalisés sont collectés
Nous utilisons une requête *Type=MyApp_CL* pour retourner tous les enregistrements du journal collecté.

![Requête de journal sans aucun champ personnalisé](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analyser les entrées du journal personnalisé
Nous utilisons Champs personnalisés pour définir les champs *EventTime*, *Code*, *Status* et *Message*, et pouvons voir la différence dans les enregistrements retournés par la requête.

![Requête de journal avec des champs personnalisés](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Étapes suivantes
* Utilisez [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les entrées du journal personnalisé dans des champs individuels.
* Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions.
