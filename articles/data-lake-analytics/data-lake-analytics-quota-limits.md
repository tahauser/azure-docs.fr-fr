---
title: Limites de quota pour Azure Data Lake Analytics
description: Découvrez comment ajuster et augmenter les limites de quota dans un compte Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Service Analytique Azure Data Lake
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 22774511720173915207da80a6ca33d5dbc83e19
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de quota pour Azure Data Lake Analytics

Découvrez comment ajuster et augmenter les limites de quota dans un compte Azure Data Lake Analytics (ADLA). Il vous sera plus simple d’appréhender le comportement de vos tâches U-SQL si vous connaissez ces limites. Toutes ces limites sont souples. Pour les augmenter, contactez le support Azure.

## <a name="azure-subscriptions-limits"></a>Limites des abonnements Azure

**Nombre maximal de comptes ADLA par abonnement :** 5.

Il s’agit du nombre maximal de comptes ADLA que vous pouvez créer par abonnement et par région. Lorsque vous essayez de créer le sixième compte ADLA, le message d’erreur suivant s’affiche : « Vous avez atteint le nombre maximal de comptes Data Lake Analytics autorisés (5) dans (zone) sous l’abonnement (nom). » Le cas échéant, vous pouvez choisir une autre région, si besoin, ou supprimer d’éventuels comptes ADLA inutilisés dans la même région, ou encore contacter le support Azure en [ouvrant un ticket de support](#increase-maximum-quota-limits) pour demander une augmentation de quota.

## <a name="adla-account-limits"></a>Limites des comptes ADLA

**Nombre maximal d’unités Analytics par compte :**  250

Il s’agit du nombre maximal d’unités Analytics pouvant s’exécuter simultanément dans votre compte. Si le nombre total d’unités Analytics exécutées dans l’ensemble des tâches dépasse cette limite, les tâches les plus récentes sont automatiquement placées dans la file d’attente. Par exemple : 

* Si une seule tâche est exécutée avec 250 unités Analytics, lorsque vous envoyez une deuxième tâche, celle-ci est placée dans la file d’attente jusqu’à ce que la première tâche soit terminée.
* Si vous avez déjà cinq tâches en cours d’exécution et que chacune utilise 50 unités Analytics, lorsque vous envoyez une sixième tâche nécessitant 20 unités Analytics, celle-ci est placée dans la file d’attente jusqu’à ce que les 20 unités Analytics soient disponibles.

**Nombre maximal de tâches U-SQL simultanées par compte :** 20

Il s’agit du nombre maximal de tâches pouvant s’exécuter simultanément dans votre compte. Au-dessus de cette valeur, les tâches les plus récentes sont automatiquement placées dans la file d’attente.

## <a name="adjust-adla-quota-limits-per-account"></a>Ajuster les limites de quota ADLA par compte

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Choisissez un compte ADLA existant.
3. Cliquez sur **Propriétés**.
4. Ajustez les valeurs **Parallélisme** et **Tâches simultanées** selon vos besoins.

    ![Page du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Augmenter les limites de quota maximales

1. Ouvrez une demande de support dans le portail Azure.

    ![Page du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Page du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Sélectionnez **Quota** comme type de problème.
3. Sélectionnez votre **Abonnement** (vérifiez qu’il ne s’agit pas d’une version d’essai).
4. Sélectionnez le type de quota **Data Lake Analytics**.

    ![Page du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Dans la page du problème, expliquez le motif de votre demande d’augmentation de limite. Indiquez pourquoi vous avez besoin de cette capacité supplémentaire dans la zone **Détails**.

    ![Page du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Vérifiez vos informations de contact et créez la demande de support.

Microsoft examine votre demande et essaie de répondre aux besoins de votre activité dans les plus brefs délais.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
