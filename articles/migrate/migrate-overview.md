---
title: "À propos d’Azure Migrate | Microsoft Docs"
description: "Fournit une vue d’ensemble du service Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b313bb4-c8f4-43ad-883c-789824add3288
ms.service: migrate
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: raynew
ms.openlocfilehash: d3d5a3bcd3be55d1915ff7fdc6d82aebbb992fc7
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Le service Azure Migrate évalue les charges de travail locales pour la migration vers Azure. Le service évalue la pertinence de la migration et le dimensionnement en fonction des performances et fournit des estimations de coût pour l’exécution de vos ordinateurs locaux dans Azure. Si vous envisagez d’effectuer des migrations lift-and-shift ou si vous êtes dans les premières étapes de l’évaluation de la migration, ce service vous correspond. Après l’évaluation, vous pouvez utiliser des services tels qu’Azure Site Recovery et Azure Database Migration, pour migrer les ordinateurs vers Azure.

> [!NOTE]
> Azure Migrate est actuellement en préversion et prend en charge les charges de travail de production.

## <a name="why-use-azure-migrate"></a>Pourquoi utiliser Azure Migrate ?

Azure Migrate vous aide à :

- **Évaluer la préparation pour Azure** : évaluer si vos ordinateurs locaux sont appropriés pour l’exécution dans Azure. 
- **Obtenir des recommandations sur la taille** : la taille recommandée pour les machines virtuelles Azure après la migration, en fonction de l’historique des performances des machines virtuelles locales. 
- **Estimer les coûts mensuels** : les coûts estimés pour l’exécution d’ordinateurs locaux dans Azure.
- **Migrer en toute confiance** : lorsque vous regroupez des machines locales pour l’évaluation, vous pouvez augmenter la confiance de l’évaluation en visualisant les dépendances. Vous pouvez afficher précisément les dépendances pour un ordinateur spécifique, ou pour tous les ordinateurs d’un groupe.

## <a name="current-limitations"></a>Limitations actuelles

- Actuellement, vous pouvez évaluer les machines virtuelles VMware locales pour la migration vers les machines virtuelles Azure.
> [!NOTE]
> La prise en charge pour Hyper-V fait partie de la feuille de route et sera activée dans les prochains mois. En attendant, nous vous recommandons d’utiliser le Planificateur de déploiement Azure Site Recovery pour planifier la migration des charges de travail Hyper-V. 
- Vous pouvez évaluer jusqu’à 1 000 ordinateurs virtuels en une seule évaluation et jusqu’à 1 500 machines en un seul projet Azure Migrate. Si vous avez besoin d’évaluer davantage, vous pouvez augmenter le nombre de projets ou d’évaluations. [En savoir plus](how-to-scale-assessment.md).
- Les machines virtuelles que vous souhaitez évaluer doivent être gérées par un vCenter Server, version 5.5, 6.0 ou 6.5.
- Vous ne pouvez créer un projet Azure Migrate que dans la région Centre-Ouest des États-Unis. Toutefois, cela n’affecte pas votre capacité à planifier la migration pour un autre emplacement Azure cible. L’emplacement du projet de migration est utilisé uniquement pour stocker les métadonnées détectées à partir de l’environnement local.
- Le portail Azure Migrate est actuellement disponible en anglais uniquement. 
- Actuellement, Azure Migrate ne prend en charge que la réplication de [Stockage localement redondant (LRS)](../storage/common/storage-introduction.md#replication).

## <a name="what-do-i-need-to-pay-for"></a>Pour quoi dois-je payer ?

Azure Migrate est disponible sans coût supplémentaire. Toutefois, pendant la période de la préversion publique, des frais supplémentaires s’appliquent pour l’utilisation des fonctionnalités de visualisation des dépendances. Pour prendre en charge la [visualisation de dépendance](concepts-dependency-visualization.md), Azure Migrate crée un espace de travail Log Analytics par défaut. Si vous utilisez la visualisation de dépendance ou que vous utilisez l’espace de travail en dehors d’Azure Migrate, vous êtes facturé pour l’utilisation de l’espace de travail. [En savoir plus](https://azure.microsoft.com/en-us/pricing/details/insight-analytics/) sur les frais. Lorsque le service sera généralement disponible, il n’y aura aucun frais lié à l’utilisation des fonctionnalités de visualisation de dépendance.


## <a name="whats-in-an-assessment"></a>Que comprend une évaluation ?

Les évaluations Azure Migrate sont basées sur les paramètres répertoriés dans le tableau.

**Paramètre** | **Détails**
--- | ---
**Emplacement cible** | L’emplacement Azure vers lequel vous souhaitez migrer. Par défaut, il s’agit de l’emplacement dans lequel vous créez le projet Azure Migrate. Vous pouvez modifier ce paramètre.   
**Redondance du stockage** | Le type de stockage que les machines virtuelles Azure utiliseront après la migration. LRS est la valeur par défaut.
**Plans de tarification** | L’évaluation prend en compte le fait que vous soyez inscrit Software Assurance et que vous puissiez utiliser [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Elle prend également en compte les offres Azure qui doivent être appliquées et vous permet d’indiquer des réductions spécifiques aux abonnements (%), que vous obtenez en plus de l’offre. 
**Niveau tarifaire** | Vous pouvez spécifier le [niveau tarifaire (de base/standard)](../virtual-machines/windows/sizes-general.md) des machines virtuelles Azure. Cela vous aide à migrer vers une famille de machine virtuelle Azure appropriée, selon que vous soyez dans un environnement de production. Par défaut le niveau [standard](../virtual-machines/windows/sizes-general.md) est utilisé.
**Historique des performances** | Par défaut, Azure Migrate évalue les performances des ordinateurs locaux à l’aide d’un mois d’historique, avec une valeur de centile de 95 %. Vous pouvez modifier ce paramètre.
**Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. Le paramètre de confort par défaut est 1.3x.


## <a name="how-does-azure-migrate-work"></a>Comment fonctionne Azure Migrate ?

1.  Lorsque vous créez un projet Azure Migrate.
2.  Azure Migrate utilise une machine virtuelle locale appelée l’appliance collecteur, pour détecter des informations sur vos machines locales. Pour créer l’appliance, vous devez télécharger le fichier d’installation au format Open Virtualization Appliance (.ova) et l’importer en tant que machine virtuelle sur votre serveur vCenter local.
3.  Connectez-vous à la machine virtuelle à l’aide des informations d’identification en lecture seule pour le serveur vCenter et exécutez le collecteur.
4.  Le collecteur collecte les métadonnées de machine virtuelle à l’aide des applets de commande VMware PowerCLI. La détection se fait sans agent et n’installe rien sur les ordinateurs hôtes VMware ou les machines virtuelles. Les métadonnées collectées incluent des informations sur les machines virtuelles (cœurs, mémoire, disques, tailles de disque et cartes réseau). Elle collecte également les données de performances des machines virtuelles, notamment concernant le processeur et la mémoire, les IOPS du disque, le débit de disque (Mbits/s) et la sortie du réseau (Mbits/s).
5.  Les métadonnées sont ajoutées au projet Azure Migrate. Vous pouvez les afficher dans le portail Azure.
6.  Dans le cadre de l’évaluation, regroupez les machines virtuelles dans des groupes. Par exemple, vous pouvez regrouper les machines virtuelles qui exécutent la même application. Vous pouvez regrouper les machines virtuelles à l’aide d’un balisage dans vCenter ou dans le portail de vCenter. Utilisez la visualisation pour vérifier les dépendances pour un ordinateur spécifique, ou pour tous les ordinateurs d’un groupe.
7.  Créez une évaluation pour un groupe.
8.  Une fois l’évaluation terminée, vous pouvez l’afficher dans le portail ou la télécharger au format Excel.



  ![Architecture de planificateur Azure](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Quelles sont les exigences de port ?

Le tableau récapitule les ports nécessaires pour les communications d’Azure Migrate.

|Composant          |Communication avec     |Port requis  |Motif   |
|-------------------|------------------------|---------------|---------|
|Collecteur          |Service Azure Migrate   |TCP 443        |Le collecteur se connecte au service via le port SSL 443|
|Collecteur          |Serveur vCenter          |Par défaut 9443   | Par défaut, le collecteur se connecte au serveur vCenter via le port 9443. Si les serveurs sont à l’écoute sur un port différent, il doit être configuré comme port sortant sur le collecteur de machine virtuelle. |
|Machine virtuelle locale     | Espace de travail OMS          |[TCP 443](../log-analytics/log-analytics-windows-agents.md#system-requirements-and-required-configuration) |L’agent MMA utilise le port TCP 443 pour se connecter à Log Analytics. Vous n’avez besoin de ce port que si vous utilisez la fonctionnalité de visualisation des dépendances et installez l’agent MMA. |


  
## <a name="what-happens-after-assessment"></a>Que se passe-t-il après l’évaluation ?

Une fois que vous avez évalué des machines locales pour la migration avec le service Azure Migrate, vous pouvez utiliser deux outils pour effectuer la migration :

- **Azure Site Recovery**: vous pouvez utiliser Azure Site Recovery pour migrer vers Azure, comme suit :
  - Préparez des ressources Azure, notamment un abonnement Azure, un réseau virtuel Azure et un compte de stockage.
  - Préparez vos serveurs VMware locaux pour la migration. Vérifiez les conditions de prise en charge de VMware pour Site Recovery, préparez les serveurs VMware pour la détection et préparez l’installation du service Mobilité de Site Recovery sur des machines virtuelles que vous souhaitez migrer. 
  - Configurez la migration. Configurez un coffre Recovery Services, configurez les paramètres de migration source et cible, définissez une stratégie de réplication et activez la réplication. Vous pouvez exécuter un exercice de récupération d’urgence pour vérifier que la migration d’une machine virtuelle dans Azure fonctionne correctement.
  - Exécutez un basculement pour migrer des machines locales vers Azure. 
  - [En savoir plus](../site-recovery/tutorial-migrate-on-premises-to-azure.md) dans le didacticiel de migration de Site Recovery.

- **Azure Database Migration**: si vos ordinateurs locaux sont en cours d’exécution sur une base de données telle que SQL Server, MySQL ou Oracle, vous pouvez utiliser Azure Database Migration Service pour les migrer vers Azure. [En savoir plus](https://azure.microsoft.com/campaigns/database-migration/).



## <a name="next-steps"></a>Étapes suivantes 
[Suivre un didacticiel](tutorial-assessment-vmware.md) pour créer une évaluation pour une machine virtuelle VMware locale.