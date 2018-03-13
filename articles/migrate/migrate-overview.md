---
title: "À propos d’Azure Migrate | Microsoft Docs"
description: "Fournit une vue d’ensemble du service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 02/26/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 45eac1d1ecb173ba0a62ab13f47b7ee6e12f7af3
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Le service Azure Migrate évalue les charges de travail locales pour la migration vers Azure. Le service évalue la pertinence de la migration des machines locales vers Azure et le dimensionnement en fonction des performances et fournit des estimations de coût pour l’exécution de vos ordinateurs locaux dans Azure. Si vous envisagez d’effectuer des migrations lift-and-shift ou si vous êtes dans les premières étapes de l’évaluation de la migration, ce service vous correspond. Après l’évaluation, vous pouvez utiliser des services tels [qu’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) et [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview), pour migrer les ordinateurs vers Azure.

## <a name="why-use-azure-migrate"></a>Pourquoi utiliser Azure Migrate ?

Azure Migrate vous aide à :

- **Évaluer la préparation pour Azure** : évaluer si vos ordinateurs locaux sont appropriés pour l’exécution dans Azure. 
- **Obtenir des recommandations sur la taille** : obtenir des recommandations de taille pour des machines virtuelles Azure, en fonction de l’historique des performances des machines virtuelles locales. 
- **Estimer les coûts mensuels** : obtenir les coûts estimés pour l’exécution d’ordinateurs locaux dans Azure.  
- **Migrer en toute confiance** : visualiser les dépendances des machines locales pour créer des groupes d’ordinateurs que vous pourrez évaluer et migrer en même temps. 

## <a name="current-limitations"></a>Limitations actuelles

- Actuellement, vous pouvez seulement évaluer les machines virtuelles VMware locales pour la migration vers les machines virtuelles Azure. Les machines virtuelles VMware doivent être gérées par vCenter Server (version 5.5, 6.0 ou 6.5).

> [!NOTE]
> La prise en charge pour Hyper-V fait partie de la feuille de route et sera activée prochainement. En attendant, nous vous recommandons d’utiliser le [Planificateur de déploiement Azure Site Recovery](http://aka.ms/asr-dp-hyperv-doc) pour planifier la migration des charges de travail Hyper-V. 

- Vous pouvez détecter jusqu’à 1 000 machines virtuelles par détection et jusqu’à 1 500 machines virtuelles par projet. En outre, vous pouvez évaluer jusqu’à 400 machines virtuelles par évaluation. Si vous avez besoin de détecter ou d’évaluer davantage de machines virtuelles, vous pouvez augmenter le nombre de détections ou d’évaluations. [Plus d’informations](how-to-scale-assessment.md)
- Vous ne pouvez créer un projet Azure Migrate que dans la région Centre-Ouest ou Est des États-Unis. Toutefois, cela n’affecte pas votre capacité à planifier la migration pour un autre emplacement Azure cible. L’emplacement du projet de migration est utilisé uniquement pour stocker les métadonnées détectées à partir de l’environnement local.
- Azure Migrate prend uniquement en charge les disques managés pour l’évaluation de la migration.

## <a name="what-do-i-need-to-pay-for"></a>Pour quoi dois-je payer ?

En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>Que comprend une évaluation ?

Une évaluation vous permet de déterminer dans quelle mesure vos machines virtuelles locales sont adaptées à Azure et d’obtenir des recommandations de redimensionnement ainsi que des estimations du coût associé à l’exécution de machines virtuelles dans Azure. Les évaluations peuvent être personnalisées selon vos besoins en modifiant leurs propriétés. Les propriétés prises en compte lors de la création d’une évaluation sont les suivantes. 

**Propriété** | **Détails**
--- | ---
**Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/><br/>À l’heure actuelle, Azure Migrate prend en charge les 30 régions suivantes : Sud-Est de l’Australie, Est de l’Australie, Sud du Brésil, Canada Est, Canada Centre, Centre de l’Inde, Inde du Sud, Inde de l’Ouest, États-Unis de l’Ouest, États-Unis Centre-Ouest, Ouest des États-Unis, Ouest des États-Unis 2, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis du Centre, Sud du centre des États-Unis, Nord du centre des États-Unis, Nord de la Chine, Est de la Chine, Centre de l’Allemagne, Nord-Est de l’Allemagne, Japon de l’Ouest, Japon de l’Est, Corée du Sud, Corée Centre, Europe de l’Ouest, Europe du Nord, Sud-Est asiatique, Royaume-Uni Ouest, Royaume-Uni Sud. Ouest des États-Unis 2 est l’emplacement cible par défaut. 
**Redondance du stockage** | Type de [redondance du stockage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) que les machines virtuelles Azure utilisent après la migration. Par défaut, le stockage localement redondant (LRS) est sélectionné. Notez qu’Azure Migrate prend uniquement en charge les évaluations basées sur des disques managés et les disques managés prennent uniquement en charge le stockage LRS. Par conséquent, la propriété ne comporte pour le moment que l’option LRS. 
**Critère de dimensionnement** | Critère utilisé par Azure Migrate pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer le dimensionnement en fonction de *l’historique des performances* des machines virtuelles locales ou dimensionner les machines virtuelles *locales* pour Azure sans tenir compte de l’historique des performances. La valeur par défaut est le dimensionnement basé sur les performances.
**Plans de tarification** | Pour les calculs de coût, l’évaluation prend en compte le fait que vous soyez inscrit Software Assurance et que vous puissiez utiliser [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Elle prend également en compte les [offres Azure](https://azure.microsoft.com/support/legal/offer-details/) auxquelles vous êtes abonné et vous permet d’indiquer des réductions spécifiques aux abonnements (%), que vous obtenez en plus de l’offre. 
**Niveau tarifaire** | Vous pouvez spécifier le [niveau tarifaire (de base/standard)](../virtual-machines/windows/sizes-general.md) des machines virtuelles Azure cible. Par exemple, si vous envisagez de migrer un environnement de production, vous pouvez prendre en compte le niveau Standard, qui fournit des machines virtuelles avec une faible latence, mais est sans doute plus coûteux. En revanche, dans un environnement de développement et de test, vous pouvez prendre en compte le niveau Basique, qui fournit des machines virtuelles avec une latence plus élevée, moins coûteuses. Par défaut le niveau [Standard](../virtual-machines/windows/sizes-general.md) est utilisé.
**Historique des performances** | Applicable uniquement si le critère de dimensionnement est basé sur les performances. Par défaut, Azure Migrate évalue les performances des machines locales à l’aide de l’historique des performances du dernier jour, avec une valeur de centile de 95 %. Vous pouvez modifier ces valeurs dans les propriétés de l’évaluation. 
**Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. Le paramètre de confort par défaut est 1.3x.


## <a name="how-does-azure-migrate-work"></a>Comment fonctionne Azure Migrate ?

1.  Lorsque vous créez un projet Azure Migrate.
2.  Azure Migrate utilise une machine virtuelle locale appelée l’appliance collecteur, pour détecter des informations sur vos machines locales. Pour créer l’appliance, vous devez télécharger un fichier d’installation au format Open Virtualization Appliance (.ova) et l’importer en tant que machine virtuelle sur votre vCenter Server local.
3.  Vous devez vous connecter à la machine virtuelle à l’aide de la connexion à la console dans vCenter Server, spécifier un nouveau mot de passe pour la machine virtuelle en cours de connexion, puis exécuter l’application de collecteur dans la machine virtuelle afin d’initier la découverte.
4.  Le collecteur collecte les métadonnées de machine virtuelle à l’aide des applets de commande VMware PowerCLI. La détection se fait sans agent et n’installe rien sur les ordinateurs hôtes VMware ou les machines virtuelles. Les métadonnées collectées incluent des informations sur les machines virtuelles (cœurs, mémoire, disques, tailles de disque et cartes réseau). Elle collecte également les données de performances des machines virtuelles, notamment concernant le processeur et la mémoire, les IOPS du disque, le débit de disque (Mbits/s) et la sortie du réseau (Mbits/s).
5.  Les métadonnées sont ajoutées au projet Azure Migrate. Vous pouvez les afficher dans le portail Azure.
6.  Dans le cadre de l’évaluation, vous pouvez regrouper les machines virtuelles découvertes. Par exemple, vous pouvez regrouper les machines virtuelles qui exécutent la même application. Pour un regroupement plus précis, vous pouvez utiliser la visualisation des dépendances pour afficher les dépendances d’une machine spécifique ou de toutes les machines d’un groupe et affiner le groupe.
7.  Une fois votre groupe formé, vous pouvez créer une évaluation pour ce groupe. 
8.  Une fois l’évaluation terminée, vous pouvez l’afficher dans le portail ou la télécharger au format Excel.



  ![Architecture de planificateur Azure](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Quelles sont les exigences de port ?

Le tableau récapitule les ports nécessaires pour les communications d’Azure Migrate.

|Composant          |Communication avec     |Port requis  |Motif   |
|-------------------|------------------------|---------------|---------|
|Collecteur          |Service Azure Migrate   |TCP 443        |Le collecteur se connecte au service via le port SSL 443|
|Collecteur          |Serveur vCenter          |Par défaut 9443   | Par défaut, le collecteur se connecte au serveur vCenter via le port 9443. Si le serveur écoute sur un port différent, il doit être configuré comme port sortant sur le collecteur de machine virtuelle. |
|Machine virtuelle locale     | Espace de travail Operations Management Suite (OMS)          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |L’agent MMA utilise le port TCP 443 pour se connecter à Log Analytics. Vous n’avez besoin de ce port que si vous utilisez la fonctionnalité de visualisation des dépendances et installez l’agent MMA (Microsoft Monitoring Agent). |


  
## <a name="what-happens-after-assessment"></a>Que se passe-t-il après l’évaluation ?

Une fois que vous avez évalué des machines locales pour la migration avec le service Azure Migrate, vous pouvez utiliser deux outils pour effectuer la migration :

- **Azure Site Recovery**: vous pouvez utiliser Azure Site Recovery pour migrer vers Azure, comme suit :
  - Préparez des ressources Azure, notamment un abonnement Azure, un réseau virtuel Azure et un compte de stockage.
  - Préparez vos serveurs VMware locaux pour la migration. Vérifiez les conditions de prise en charge de VMware pour Site Recovery, préparez les serveurs VMware pour la détection et préparez l’installation du service Mobilité de Site Recovery sur des machines virtuelles que vous souhaitez migrer. 
  - Configurez la migration. Configurez un coffre Recovery Services, configurez les paramètres de migration source et cible, définissez une stratégie de réplication et activez la réplication. Vous pouvez exécuter un exercice de récupération d’urgence pour vérifier que la migration d’une machine virtuelle dans Azure fonctionne correctement.
  - Exécutez un basculement pour migrer des machines locales vers Azure. 
  - [En savoir plus](../site-recovery/tutorial-migrate-on-premises-to-azure.md) dans le didacticiel de migration de Site Recovery.

- **Azure Database Migration**: si vos ordinateurs locaux sont en cours d’exécution sur une base de données telle que SQL Server, MySQL ou Oracle, vous pouvez utiliser Azure Database Migration Service pour les migrer vers Azure. [Plus d’informations](https://azure.microsoft.com/campaigns/database-migration/)



## <a name="next-steps"></a>Étapes suivantes 
[Suivre un didacticiel](tutorial-assessment-vmware.md) pour créer une évaluation pour une machine virtuelle VMware locale.