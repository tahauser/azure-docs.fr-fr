---
title: "Conseils sur la récupération d’urgence pour Azure Data Lake Store | Microsoft Docs"
description: "Conseils sur la récupération d’urgence pour Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: d76f756e7a6fe77886f0d4e00ab8b23273b4e09d
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>Conseils sur la récupération d’urgence des données dans Azure Data Lake Store

Les données de votre compte Azure Data Lake Store seront résistantes aux défaillances matérielles temporaires au sein d’une région par le biais des réplicas automatisés. Cela garantit durabilité et haute disponibilité et permet de respecter le contrat de niveau de service d’Azure Data Lake Store. Cet article donne des conseils sur la protection des données contre les rares pannes ou suppressions accidentelles qui peuvent se produire à l’échelle d’une région.

## <a name="disaster-recovery-guidance"></a>Guide de récupération d’urgence
Il est essentiel que chaque client veille à élaborer son propre plan de récupération d’urgence. Reportez-vous à la documentation Azure ci-dessous pour créer votre plan de récupération d’urgence. Voici quelques ressources qui peuvent vous aider à créer votre propre plan.

* [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Guide technique de la résilience Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Meilleures pratiques
Nous vous recommandons de copier vos données critiques vers un autre compte Data Lake Store dans une autre région, à une fréquence adaptée aux besoins de votre plan de récupération d’urgence. Il existe différents moyens de copier des données, notamment [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) et [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory est un service utile pour créer et déployer des pipelines de déplacement des données à intervalles réguliers.

En cas de panne régionale, vous pouvez accéder à vos données dans la région où les données ont été copiées. Vous pouvez surveiller le [Tableau de bord d’intégrité du service Azure](https://azure.microsoft.com/status/) pour déterminer l’état du service Azure dans le monde entier.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Aide à la récupération suite à une corruption de données ou à une suppression accidentelle
Si Azure Data Lake Store assure la résilience des données via des réplicas automatisés, cela ne protège aucunement votre application (ou les développeurs/utilisateurs) de la corruption des données ou d’une suppression accidentelle.

### <a name="best-practices"></a>Meilleures pratiques
Pour éviter toute suppression accidentelle, nous vous recommandons de définir tout d’abord les stratégies d’accès adaptées à votre compte Data Lake Store.  Cela inclut la mise en œuvre de [verrous de ressources Azure](../azure-resource-manager/resource-group-lock-resources.md) pour verrouiller des ressources importantes, ainsi que l’application d’un contrôle d’accès au niveau du compte et des fichiers à l’aide des [fonctionnalités de sécurité de Data Lake Store](data-lake-store-security-overview.md) disponibles. Nous vous recommandons également de créer régulièrement des copies de vos données critiques avec [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) dans un autre compte Data Lake Store, dossier ou abonnement Azure.  Cela peut servir à la récupération après un incident de corruption ou de suppression de données. Azure Data Factory est un service utile pour créer et déployer des pipelines de déplacement des données à intervalles réguliers.

Les organisations peuvent également activer la [journalisation des diagnostics](data-lake-store-diagnostic-logs.md) pour leur compte Azure Data Lake Store afin de collecter des pistes d’audit d’accès aux données qui fournissent des informations sur les auteurs potentiels d’une suppression ou d’une mise à jour de fichier.

## <a name="next-steps"></a>Étapes suivantes
* [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)

