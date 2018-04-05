---
title: Vue d’ensemble de l'API d’exportation Mobile Engagement
description: Apprenez les principes de base de l'exportation de vos données brutes générées par les appareils de vos utilisateurs de façon à en tirer parti dans vos propres outils
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: ''
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: c1dc8a34abf84be3d7f59d41c36d90c2a67e5d32
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="mobile-engagement-export-api-overview"></a>Vue d’ensemble de l'API d’exportation Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

## <a name="introduction"></a>Introduction
Dans ce document, vous allez apprendre les principes de base de l'exportation de vos données brutes générées par les appareils de vos utilisateurs de façon à en tirer parti dans vos propres outils.

## <a name="pre-requisites"></a>Conditions préalables
L’exportation des données brutes à partir de Mobile Engagement requiert :

* la possibilité pour la configuration de l'authentification des API d’utiliser les API (consultez [installation manuelle de l'authentification](mobile-engagement-api-authentication-manual.md)),
* l’utilisation des API REST ou du [Kit de développement logiciel (SDK) .NET](mobile-engagement-dotnet-sdk-service-api.md),
* un compte Azure Storage.

> [!NOTE]
> Nous recommandons également l'excellent [Explorateur Microsoft Azure Storage](http://storageexplorer.com/), au moins pendant la phase de développement, car il fournit une interface utilisateur facile à utiliser pour interagir avec Azure Storage.
> 
> 

## <a name="what-can-be-exported"></a>Que peut-on exporter ?
Mobile Engagement permet à ses utilisateurs de collecter de nombreux types de données et, par conséquent, possède plusieurs types d'exportation adaptés à ces différents types de données.
Il existe 2 types d'exportation principaux :

* Instantané : généralement utilisé pour exporter des données qui représentent un état et pour lesquelles Mobile Engagement n'a pas d'historique. Cela inclut par exemple les balises (app-info), les jetons ou les retours d’expérience de campagnes push. Par conséquent, ces types d'exportation ne sont pas liés à une date.
* Historique : ce type d'exportation est utilisé pour les données qui s'accumulent au fil du temps, par exemple des événements ou des activités.

Le tableau ci-dessous décrit de façon exhaustive toutes les exportations possibles :

| Type d’exportation | Type de données | Description |
| --- | --- | --- |
| Instantané |Émettre |Génère une exportation de retours d’expérience de campagnes push, ID d’appareil/d'utilisateur par ID d’appareil/d’utilisateur |
| Instantané |Tag |Génère une exportation des balises (app-info) associées à chaque appareil |
| Instantané |Appareil |Génère une exportation de la plupart des données sur les appareils, notamment les informations techniques (modèle, paramètres régionaux, fuseau horaire, etc.), les balises ou la date de la première consultation. |
| Instantané |par jeton |Génère une exportation de tous les jetons valides |
| Historique |Activité |Génère une exportation de toutes les activités de chaque appareil sur une période donnée |
| Historique |Événement |Génère une exportation de toutes les activités de chaque appareil sur une période donnée |
| Historique |Job |Génère une exportation de toutes les tâches de chaque appareil sur une période donnée |
| Historique |Error |Génère une exportation de toutes les erreurs de chaque appareil sur une période donnée |

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?
Les exportations sont des tâches de longue durée qui peuvent produire des fichiers de données volumineux. Pour cette raison, elles ne peuvent pas être appelées pour retourner immédiatement un fichier à télécharger.
Pour exporter des données à partir de Mobile Engagement, vous devrez créer une **Tâche d'exportation** avec l'API, où vous spécifiez généralement :

* le type d'exportation (instantané ou historique),
* le type de données,
* le **conteneur Azure Storage** (avec une SAP valide et un accès en écriture) où sera écrit le résultat de l'exportation.
* Par exemple, un paramètre d’URL de conteneur serait https://[StorageAccountName].blob.core.windows.net/[ContainerName]?[SASWritePermissionsToken]  

Voici un exemple réel. https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

Veuillez noter que le démarrage de votre tâche peut prendre quelques minutes et que son exécution peut durer quelques secondes pour les toutes petites applications ou plusieurs heures pour les applications avec un grand nombre d'utilisateurs ou d'activités.

Une fois la tâche créée, il est possible de vérifier son état pour voir si elle est toujours en cours d'exécution ou si elle est terminée.

Une fois la tâche RÉUSSIE, le fichier de données qui en résulte est disponible sur le conteneur de stockage fourni.

