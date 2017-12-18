---
title: "Architecture d’Operations Management Suite (OMS) | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) est une solution de gestion informatique de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.  Cet article identifie les différents services inclus dans OMS et fournit des liens vers leur documentation détaillée."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: fb4e9150c1069d48399fb217f865b294ccd317dc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="oms-architecture"></a>Architecture OMS
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) est un ensemble de services informatiques destinés à gérer vos environnements locaux et de cloud.  Cet article décrit les différents composants OMS locaux et de cloud, ainsi que leur architecture de cloud computing de haut niveau.  Pour plus d’informations, vous pouvez consulter la documentation de chaque service.

## <a name="log-analytics"></a>Log Analytics
Toutes les données collectées par [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) sont stockées dans le référentiel OMS, qui est hébergé dans Azure.  Les Sources connectées génèrent des données collectées dans le référentiel OMS.  Actuellement, trois types de sources connectées sont pris en charge.

* Un agent installé sur un ordinateur [Windows](../log-analytics/log-analytics-windows-agent.md) ou [Linux](../log-analytics/log-analytics-linux-agents.md) directement connecté à OMS.
* Un groupe d’administration System Center Operations Manager (SCOM) [connecté à Log Analytics](../log-analytics/log-analytics-om-agents.md) .  Les agents SCOM continuent à communiquer avec les serveurs d’administration qui transmettent des événements et des données de performances à Log Analytics.
* Un [compte de stockage Azure](../log-analytics/log-analytics-azure-storage.md) qui collecte les données [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) à partir d’un rôle de travail, d’un rôle Web ou d’une machine virtuelle dans Azure.

Les sources de données définissent les données que Log Analytics collecte auprès des sources connectées, y compris les journaux d’événements et les compteurs de performances.  Les solutions ajoutent des fonctionnalités à OMS et peuvent être facilement ajoutées à votre espace de travail à partir de la [Galerie des solutions OMS](../log-analytics/log-analytics-add-solutions.md).  Certaines solutions peuvent nécessiter une connexion directe à Log Analytics depuis les agents SCOM, tandis que d’autres nécessiteront l’installation d’un agent supplémentaire.

Log Analytics présente un portail web que vous pouvez utiliser pour gérer les ressources OMS, ajouter et configurer des solutions OMS, et afficher et analyser des données dans le référentiel OMS.

![Architecture Log Analytics haute performance](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
[Runbooks Azure Automation](http://azure.microsoft.com/documentation/services/automation) sont exécutés dans le cloud Azure et peuvent accéder aux ressources situées dans Azure, dans d’autres services cloud ou accessibles depuis l’Internet public.  Vous pouvez également désigner des ordinateurs de votre centre de données local avec un [Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md), afin que les Runbooks puissent accéder aux ressources locales.

[configurations DSC](../automation/automation-dsc-overview.md) stockées dans Azure Automation peuvent être directement appliquées aux machines virtuelles Azure.  Les autres machines physiques et virtuelles peuvent demander des configurations du serveur d’extraction Azure Automation DSC.

Azure Automation dispose d’une solution OMS qui affiche des statistiques et des liens permettant de lancer le portail Azure pour toutes les opérations.

![Architecture Azure Automation haute performance](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Sauvegarde Azure
Les données protégées dans [Azure Backup](http://azure.microsoft.com/documentation/services/backup) sont stockées dans un archivage de sauvegarde situé dans une zone géographique spécifique.  Les données sont répliquées au sein de cette région et, selon le type d’archivage, peuvent aussi être répliquées vers une autre région afin d’assurer une deuxième redondance.

Dans Azure Backup, il existe trois scénarios fondamentaux.

* Une machine Windows avec un agent Azure Backup.  Ce scénario vous permet de sauvegarder des fichiers et des dossiers à partir de n’importe quel client ou serveur Windows directement dans votre archivage de sauvegarde Azure.  
* System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server. Ce scénario vous permet d’exploiter DPM ou Microsoft Azure Backup Server pour sauvegarder des fichiers, des dossiers et des charges de travail d’applications telles que SQL ou SharePoint dans un stockage local, puis de les répliquer dans votre archivage de sauvegarde Azure.
* Extensions de machine virtuelle Azure  Ce scénario vous permet de sauvegarder des machines virtuelles Azure dans votre archivage de sauvegarde Azure.

Azure Backup dispose d’une solution OMS qui affiche des statistiques et des liens permettant de lancer le portail Azure pour toutes les opérations.

![Architecture Azure Backup haute performance](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) orchestre la réplication, le basculement et la restauration des machines virtuelles et des serveurs physiques. Les données de réplication sont échangées entre des hôtes Hyper-V, des hyperviseurs VMware et des serveurs physiques dans les centres de données principaux et secondaires, ou entre le centre de données et Azure Storage.  Site Recovery stocke les métadonnées dans des archivages situés dans une zone géographique Azure spécifique. Aucune donnée répliquée n’est stockée par le service de Site Recovery.

Dans Azure Site Recovery, il existe trois scénarios de réplication fondamentaux.

**La réplication de machines virtuelles Hyper-V**

* Si les machines virtuelles Hyper-V sont gérées dans des clouds VMM, vous pouvez les répliquer vers un centre de données secondaire ou vers Azure Storage.  La réplication vers Azure se fait via une connexion Internet sécurisée.  La réplication vers un centre de données secondaire se fait via le réseau LAN.
* Si les machines virtuelles Hyper-V ne sont pas gérées par VMM, vous ne pouvez les répliquer que vers Azure Storage.  La réplication vers Azure se fait via une connexion Internet sécurisée.

**La réplication de machines virtuelles VMWare**

* Vous pouvez répliquer des machines virtuelles VMware vers un centre de données secondaire exécutant VMware ou vers Azure Storage.  La réplication vers Azure peut se faire via Azure ExpressRoute ou un réseau privé virtuel de site à site, ou via une connexion Internet sécurisée. La réplication vers un centre de données secondaire se fait sur le canal de données InMage Scout.

**La réplication de serveurs physiques Windows ou Linux** 

* Vous pouvez répliquer des serveurs physiques vers un centre de données secondaire ou vers Azure Storage. La réplication vers Azure peut se faire via Azure ExpressRoute ou un réseau privé virtuel de site à site, ou via une connexion Internet sécurisée. La réplication vers un centre de données secondaire se fait sur le canal de données InMage Scout.  Azure Site Recovery dispose d’une solution OMS qui affiche des statistiques, mais vous devez utiliser le portail Azure pour toutes les opérations.

![Architecture Azure Site Recovery haute performance](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* En savoir plus sur [Azure Automation](https://azure.microsoft.com/documentation/services/automation).
* En savoir plus sur [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* En savoir plus sur [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

