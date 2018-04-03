---
title: Gestion et surveillance Azure | Microsoft Docs
description: Azure propose plusieurs services et outils qui fonctionnent ensemble pour offrir une gestion complète, non seulement pour vos applications exécutées dans Azure, mais également dans d’autres clouds et en local.  Cet article fournit une description générale des différents domaines de la gestion et contient des liens vers du contenu sur les outils Azure permettant de gérer vos applications et ressources cloud.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-management---monitoring"></a>Gestion Azure | Surveillance

La surveillance dans Azure est un aspect de la gestion Azure.  Cet article décrit en bref les différents domaines de gestion requis pour déployer et tenir à jour vos applications et ressources sur Azure, et fournit des liens vers de la documentation vous permettant de démarrer.

## <a name="management-in-azure"></a>Gestion dans Azure

La gestion fait référence aux tâches et processus nécessaires pour maintenir vos applications métier et les ressources qui les prennent en charge.  Azure propose plusieurs services et outils qui fonctionnent ensemble pour offrir une gestion complète, non seulement pour vos applications exécutées dans Azure, mais également dans d’autres clouds et en local.  La compréhension des différents outils disponibles et de la façon dont ils peuvent être utilisés ensemble pour une variété de scénarios de gestion constitue la première étape de la conception d’un environnement de gestion complet.

Le schéma ci-dessous illustre les différents domaines de gestion requis pour maintenir toute application ou ressource.  Ces différents domaines peuvent être considérés en termes de cycle de vie, où chacun est requis successivement au cours de la vie d’une ressource.  Ce cycle commence par son déploiement initial, puis son exploitation dans la durée, et se termine par sa mise hors service.

![Fonctionnalités de gestion](media/management-overview/management-capabilities.png)


Aucun service Azure unique ne répond complètement aux exigences d’un domaine de gestion particulier ; en revanche, chacun de ces domaines est mis en œuvre au moyen de plusieurs services fonctionnant ensemble.  Certains services fournissent des fonctionnalités ciblées, comme Application Insights qui permet de surveiller les applications web.  D’autres fournissent des fonctions communes, comme Log Analytics qui stocke les données de gestion pour d’autres services, ce qui vous permet d’analyser des données de différents types collectées par différents services.  

Les sections suivantes décrivent en bref les différents domaines de gestion et contiennent des liens vers du contenu détaillé sur les principaux services Azure destinés à répondre à ces besoins.

## <a name="monitor"></a>Surveiller
La surveillance consiste à recueillir et à analyser des données afin de déterminer les performances, l’intégrité et la disponibilité de votre application métier et des ressources dont elle dépend. Une stratégie de surveillance efficace vous aidera à comprendre le fonctionnement détaillé des différents composants de votre application et à accroître la durée de fonctionnement en vous signalant de manière proactive les problèmes critiques, afin que vous puissiez les résoudre au plus vite.  Vous pouvez lire une vue d’ensemble de la surveillance dans Azure présentant les différents services utilisés dans le cadre d’une stratégie de surveillance sur la page [Surveillance des applications et des ressources Azure](monitoring-overview.md).


## <a name="configure"></a>Configuration
La configuration fait référence au déploiement initial et à la configuration des applications et des ressources, ainsi qu’à leur maintenance continue au moyen de correctifs et de mises à jour.  L’automatisation de ces tâches au moyen de script et de stratégie vous permet d’éviter la redondance, en réduisant le temps et les efforts et en augmentant votre précision et efficacité.  [Azure Automation](..\automation\automation-intro.md) fournit la majeure partie des services d’automatisation des tâches de configuration.  En plus des runbooks pour automatiser les processus, il fournit la configuration et la gestion des mises à jour, ce qui vous aide à gérer la configuration via la stratégie et à identifier et déployer les mises à jour.

## <a name="govern"></a>Gouvernance
La gouvernance propose des mécanismes et des processus pour garder le contrôle sur vos applications et ressources dans Azure.  Elle implique la planification de vos initiatives et la définition de priorités stratégiques.  La gouvernance dans Azure est principalement mise en œuvre à l’aide de deux services.  [Azure Policy](../azure-policy/azure-policy-introduction.md) vous permet de créer, assigner et gérer des définitions de stratégie qui appliquent différentes règles et actions sur vos ressources, qui restent donc conformes aux standards et aux contrats de niveau de service de l’entreprise. [Azure Cost Management par Cloudyn](../cost-management/overview.md) vous permet de suivre l’utilisation du cloud et les dépenses liées à vos ressources Azure et celles d’autres fournisseurs de services cloud, notamment AWS et Google.

## <a name="secure"></a>Sécuriser
La gestion de la sécurité de vos applications, ressources et données implique d’évaluer les menaces, de collecter et d’analyser les données de sécurité, et de garantir que vos applications et ressources sont conçues et configurées de manière sécurisée.  [Azure Security Center](../security-center/security-center-intro.md) assure la surveillance de la sécurité et l’analyse des menaces, et inclut des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides.  Consultez également la [Présentation d’Azure Security](../security/azure-security.md) pour plus d’informations sur la sécurité dans Azure et des conseils sur la configuration des ressources Azure en toute sécurité.


## <a name="protect"></a>Protéger
La protection consiste à s’assurer que vos applications et données sont toujours disponibles, même en cas de pannes hors de votre contrôle.  La protection dans Azure est fournie par deux services.  [Sauvegarde Azure](../backup/backup-introduction-to-azure-backup.md) permet la sauvegarde et la récupération de vos données, dans le cloud ou en local.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) garantit la haute disponibilité de votre application en fournissant la continuité d’activité et la récupération d’urgence en cas de sinistre.

## <a name="migrate"></a>Migrer 
La migration fait référence à la transition des charges de travail exécutées actuellement en local vers le cloud Azure.  [Azure Migrate](../migrate/migrate-overview.md) est un service qui vous aide à évaluer la pertinence d’une migration de machines virtuelles locales vers Azure, en fournissant notamment le dimensionnement en fonction des performances et l’estimation des coûts.  Azure Site Recovery peut vous aider à mettre en œuvre la migration de machines virtuelles [locales](../site-recovery/migrate-tutorial-on-premises-azure.md) ou [depuis Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure Database Migration](../dms/dms-overview.md) vous aide lors de la migration de plusieurs sources de bases de données vers les plateformes de données Azure.