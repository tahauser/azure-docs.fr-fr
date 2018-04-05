---
title: 'Plateformes d’analyse : comparaison entre Apache Storm et Stream Analytics | Microsoft Docs'
description: Comparez Apache Storm et Stream Analytics pour vous aider à choisir une plateforme d’analyse cloud. Découvrez les fonctionnalités et les différences.
keywords: plateforme d’analyse, plateformes d’analyse, plateforme d’analyse cloud, comparaison avec storm
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: 48599c362c5db50ebec3acd0aaa4359439edb00b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Choix d’une plateforme d’analyse de flux : comparaison d’Apache Storm et d’Azure Stream Analytics
Azure fournit plusieurs solutions pour analyser les données de flux : [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) et [Apache Storm sur Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Les deux plateformes d’analyse offrent les avantages d’une solution PaaS, mais leurs fonctionnalités sont sensiblement différentes, tout comme la façon de les configurer et de les gérer. 

Cet article contient un tableau comparatif des fonctionnalités pour vous aider à choisir entre Apache Storm et Azure Stream Analytics comme plateforme d’analyse cloud. 

## <a name="general-features"></a>Fonctionnalités générales

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Open Source ?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non. Azure Stream Analytics est une offre propriétaire de Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui. Apache Storm est une technologie sous licence Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Support Microsoft ?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
OUI </p>
            </td>
            <td width="246" valign="top">
                <p>
OUI </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Configuration matérielle requise</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Aucune. Azure Stream Analytics est un service Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Aucune. Apache Storm est un service Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unité de niveau supérieur</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les utilisateurs déploient et surveillent les travaux de diffusion en continu.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Les utilisateurs déploient et surveillent un cluster entier, qui peut héberger plusieurs travaux Storm ainsi que d’autres charges de travail (notamment le traitement).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Tarification</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Facturé par volume de données traitées et par nombre d’unités de diffusion en continu requises par heure d’exécution du travail. 
                </p>
                    <p>Pour plus d’informations, voir <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Tarification de Stream Analytics </a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
L’unité d’achat est basée sur le cluster et facturée en fonction de la durée d’exécution du cluster, indépendamment des travaux déployés.
                </p>
                <p>
Pour plus d’informations, voir <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Tarification de HDInsight</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Création

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctionnalités : langage DSL SQL ?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Oui. Stream Analytics fournit un langage de type SQL pour créer des transformations.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Non. Les utilisateurs écrivent du code en Java ou en C#, ou ils utilisent les API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctionnalités : opérateurs temporels ?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les agrégats fenêtrés et les jointures temporelles sont pris en charge par défaut.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Les opérateurs temporels doivent être implémentés par l’utilisateur.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Expérience de développement</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les utilisateurs peuvent créer, déboguer et surveiller des travaux via le portail Azure, à l’aide d’exemples de données dérivés d’un flux temps réel.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
À l’aide de .NET, les utilisateurs peuvent développer, déboguer et surveiller au moyen de Visual Studio. Les utilisateurs de Java ou d’autres langages peuvent utiliser l’environnement IDE de leur choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge du débogage</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Des journaux des opérations et d’état de travail de base sont disponibles et aident au débogage. Pour le moment, Stream Analytics ne permet pas aux utilisateurs de spécifier le type de contenu ou le volume de contenu inclus dans les journaux (par exemple, le mode détaillé).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Des journaux détaillés sont disponibles. Les utilisateurs peuvent accéder aux journaux dans Visual Studio ou en se connectant au cluster et en accédant directement aux journaux.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensibilité à l’aide de code personnalisé ?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Prend partiellement en charge les fonctions JavaScript. Pour plus d’informations, voir <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">Intégration d’UDF JavaScript</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui. Les utilisateurs peuvent écrire du code personnalisé en C#, Java ou dans tout autre langage pris en charge sur Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Sources de données (entrées) et sorties ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Sources de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Azure Event Hubs et stockage Blob Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Des connecteurs sont disponibles pour Azure Event Hubs, Azure Service Bus, Kafka, etc. Les utilisateurs peuvent créer des connecteurs supplémentaires à l’aide de code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Les utilisateurs peuvent implémenter tout format à l’aide de code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Sorties</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Un travail de diffusion en continu peut compter plusieurs sorties. Sorties prises en charge : Azure Event Hubs, stockage Blob Azure, stockage Table Azure, Azure SQL DB et Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm prend en charge de nombreuses sorties dans une topologie, et chacune peut disposer d’une logique personnalisée pour le traitement en aval. Storm inclut des connecteurs pour Power BI, Azure Event Hubs, le stockage Blob Azure, Azure Cosmos DB, SQL et HBase. Les utilisateurs peuvent créer des connecteurs supplémentaires à l’aide de code personnalisé.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats d’encodage des données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les données doivent être formatées à l’aide de UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Les utilisateurs peuvent implémenter tout format d’encodage des données à l’aide de code personnalisé.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Gestion et opérations ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modèle de déploiement de travail</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Portail Azure, PowerShell et interfaces API REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Portail Azure, PowerShell, Visual Studio et interfaces API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Analyse (statistiques, compteurs, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
L’analyse est implémentée via le portail Azure et les interfaces API REST. Les utilisateurs peuvent également configurer des alertes Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
L’analyse est implémentée via l’interface utilisateur de Storm et les interfaces API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensibilité</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
L’extensibilité est déterminée par le nombre d’unités de diffusion en continu pour chaque travail. Chaque unité de diffusion en continu traite jusqu’à 1 Mo par seconde, avec une capacité maximale de 50 unités. Pour plus d’informations, voir <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">Mettre à l’échelle pour augmenter le débit</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
L’extensibilité est déterminée par le nombre de nœuds contenus dans le cluster Storm HDInsight. La limite supérieure du nombre de nœuds est définie par le quota Azure de l’utilisateur.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de traitement des données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les utilisateurs peuvent augmenter le traitement des données ou optimiser les coûts en augmentant ou en diminuant le nombre d’unités de diffusion en continu, avec une limite supérieure de 1 Go par seconde.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Les utilisateurs peuvent mettre à l’échelle la taille du cluster en l’augmentant ou en la réduisant.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrêt/Reprise</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Arrêt et reprise à partir du dernier arrêt.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Arrêt et reprise à partir du dernier arrêt basé sur un filigrane.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Mise à jour de service et d’infrastructure</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Mise à jour corrective automatique sans temps d’arrêt.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Mise à jour corrective automatique sans temps d’arrêt.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuité d’activité via un service hautement disponible avec contrats SLA garantis</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>Contrat SLA de temps d’activité de 99,9 %</li>
                <li>Récupération automatique des défaillances</li>
                <li>Récupération intégrée des opérateurs temporels avec état</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
Contrat SLA de temps d’activité de 99,9 % du cluster Storm. 
                </p>
                <p>
Apache Storm est une plateforme de diffusion en continu à tolérance de panne. Toutefois, il incombe à l’utilisateur de s’assurer que les travaux de diffusion en continu s’exécutent sans interruption.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Fonctionnalités avancées ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrivée tardive et gestion des événements de manière désordonnée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Des stratégies configurables intégrées peuvent réorganiser et supprimer des événements ou régler leur heure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Les utilisateurs doivent implémenter une logique pour gérer ce scénario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Données de référence</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les données de référence sont disponibles à partir du stockage Blob Azure avec un maximum de 100 Mo de cache en mémoire. Les données de référence sont actualisées par le service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Aucune limitation de la taille des données. Des connecteurs sont disponibles pour HBase, Azure Cosmos DB, SQL Server et Azure. Les utilisateurs peuvent créer des connecteurs supplémentaires à l’aide de code personnalisé. Les données de référence doivent être actualisées à l’aide de code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Intégration à Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les modèles Azure Machine Learning publiés peuvent être configurés en tant que fonctions lors de la création d’un travail. Pour plus d’informations, voir <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">Mettre à l’échelle pour les fonctions Machine Learning</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponible via Storm Bolts.
                </p>
            </td>
        </tr>
    </tbody>
</table>
