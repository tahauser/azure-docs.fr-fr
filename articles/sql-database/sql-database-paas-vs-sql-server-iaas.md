---
title: SQL (PaaS) Database ou SQL Server dans le cloud sur des machines virtuelles (IaaS) | Microsoft Docs
description: 'Découvrez l’option SQL Server cloud adaptée à votre application : Azure SQL (PaaS) Database ou SQL Server sur les machines virtuelles Azure.'
services: sql-database, virtual-machines
keywords: Cloud SQL Server, SQL Server dans le cloud, base de données PaaS, cloud SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: a7fde828c7a88f440cf69e3a4b26bb6c75cdaafb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)
Azure propose deux options pour héberger des charges de travail SQL Server dans Microsoft Azure :

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): base de données SQL native du cloud, également appelée base de données PaaS (plateforme en tant que service) ou DBaaS (base de données en tant que service) optimisée pour le développement d’applications SaaS (logiciel en tant que service). Elle est compatible avec la plupart des fonctionnalités de SQL Server. Pour plus d’informations sur le PaaS, consultez la page [Qu’est-ce que le PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installé et hébergé dans le cloud sur des machines virtuelles Windows Server s’exécutant sur Azure, également appelé infrastructure en tant que service (IaaS).
  SQL Server sur les machines virtuelles Azure est optimisé pour la migration d’applications SQL Server existantes. Toutes les versions et éditions de SQL Server sont disponibles. Il offre une compatibilité de 100 % avec SQL Server, ce qui vous permet d’héberger autant de bases de données que nécessaire et d’exécuter des transactions entre bases de données. Il apporte un contrôle total sur SQL Server et Windows.

Découvrez comment chaque option s’inscrit dans la plate-forme de données de Microsoft et obtenez de l’aide sur l’option qui répond aux besoins de votre entreprise. Que vous privilégiiez les économies ou une administration minimale, cet article peut vous aider à déterminer l’approche adaptée en fonction des exigences clients essentielles.

## <a name="microsofts-data-platform"></a>Plateforme de données de Microsoft
L'une des premières choses à comprendre dans toute discussion comparant les bases de données Azure et SQL Server locales est que vous pouvez tout utiliser. La plateforme de données de Microsoft utilise la technologie SQL Server et la rend disponible sur les machines physiques locales, les environnements de cloud privé, les environnements de cloud privé hébergé par un tiers et le cloud public. SQL Server sur les machines virtuelles Azure vous permet de répondre à des besoins métier spécifiques et différents par le biais d’une combinaison de déploiements locaux et hébergés dans le cloud, tout en utilisant le même ensemble de produits serveur, d’outils de développement et de savoir-faire dans ces environnements.

   ![Options SQL Server Cloud : SQL server sur IaaS ou base de données SQL SaaS dans le cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Comme indiqué sur le diagramme, chaque offre peut être caractérisée par le niveau d'administration dont vous disposez sur l'infrastructure (sur l'axe des X) et par le degré d'efficacité opérationnelle obtenue par consolidation au niveau de la base de données et par automatisation (sur l'axe des Y).

Lorsque vous concevez une application, quatre options de base sont disponibles pour l'hébergement de sa partie SQL Server :

* SQL Server sur des machines physiques non virtualisées
* SQL Server sur des machines virtualisées locales (cloud privé)
* SQL Server sur Azure Virtual Machines (cloud public Microsoft)
* Base de données SQL Azure (cloud public Microsoft)

Dans les sections suivantes, vous allez découvrir SQL Server dans le cloud public Microsoft : Azure SQL Database et SQL Server sur les machines virtuelles Azure. Vous allez également explorer les motivations commerciales courantes permettant de déterminer l’option qui convient le mieux à votre application.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Étude détaillée d’Azure SQL Database et de SQL Server sur les machines virtuelles Azure
**Azure SQL Database** est une base de données en tant que service (DBaaS) relationnelle hébergée dans le cloud Azure, qui appartient aux catégories *Logiciel en tant que service (SaaS)* et *Plateforme en tant que service (PaaS)*. [Base de données SQL](sql-database-technical-overview.md) repose sur du matériel et des logiciels standardisés, qui sont détenus, hébergés et gérés par Microsoft. Avec SQL Database, vous pouvez développer directement sur le service à l'aide de fonctionnalités intégrées. Lorsque vous utilisez SQL Database, vous payez à l’utilisation, avec des possibilités de mise à l’échelle pour obtenir plus de puissance sans interruption du service.

**SQL Server sur les machines virtuelles Azure** est une *infrastructure en tant que service (IaaS)* et vous permet d’exécuter SQL Server dans une machine virtuelle résidant dans le cloud. Comme pour SQL Database, elle repose sur du matériel standardisé détenu, hébergé et géré par Microsoft. Quand vous utilisez SQL Server sur une machine virtuelle, vous pouvez payer à l’utilisation pour une licence SQL Server déjà incluse dans une image SQL Server ou utiliser simplement une licence existante. Vous pouvez aussi facilement monter/descendre en puissance et suspendre/reprendre la machine virtuelle en fonction de vos besoins.

En général, ces deux options SQL sont optimisées à différentes fins :

* **Azure SQL Database**est optimisé pour l’approvisionnement et la gestion de nombreuses bases de données à moindre coût. Elle réduit les coûts d’administration, car vous n’avez plus à gérer les machines virtuelles, le système d’exploitation et le logiciel de base de données. Vous n’avez pas à gérer les mises à niveau, la haute disponibilité, ni les [sauvegardes](sql-database-automated-backups.md). En règle générale, Azure SQL Database augmente considérablement le nombre de bases de données gérées par un informaticien ou un développeur.
* **SQL Server sur des machines virtuelles Azure** est optimisé pour la migration d’applications existantes vers Azure ou l’extension d’applications locales existantes vers le cloud dans les déploiements hybrides. En outre, vous pouvez utiliser SQL Server sur une machine virtuelle pour développer et tester des applications traditionnelles SQL Server. Avec SQL Server sur les machines virtuelles Azure, vous disposez de droits d’administration complets sur une instance SQL Server dédiée et sur une machine virtuelle dans le cloud. C'est le choix idéal lorsqu'une organisation possède déjà les ressources informatiques disponibles pour maintenir les machines virtuelles. Ces fonctionnalités vous permettent de créer un système hautement personnalisé afin de répondre aux exigences de performances de disponibilité de votre application.

Le tableau suivant résume les principales caractéristiques de SQL Database et de SQL Server sur les machines virtuelles Azure :

| **Idéal pour :** | **Azure SQL Database** | **SQL Server sur une machine virtuelle Azure** |
| --- | --- | --- |
|  |Nouvelles applications conçues pour le cloud qui ont des contraintes de temps de développement et de marketing. |Applications existantes qui requièrent une migration rapide vers le cloud avec un minimum de modifications. Scénarios de développement et de test rapides lorsque vous ne souhaitez pas acheter du matériel SQL Server local non destiné à la production. |
|  | Équipes qui nécessitent une haute disponibilité, une récupération d’urgence et une mise à niveau pour la base de données. |Équipes qui peuvent configurer et gérer une haute disponibilité, une récupération d’urgence et une mise à jour corrective pour SQL Server. Certaines fonctionnalités automatisées simplifient considérablement cette procédure. | |
|  | Équipes qui ne souhaitent pas gérer le système d’exploitation sous-jacent et les paramètres de configuration. |Vous avez besoin d’un environnement personnalisé avec des droits d’administration complets. | |
|  | Bases de données de moins de 4 To ou bases de données plus volumineuses pouvant être [partitionnées horizontalement ou verticalement](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) à l’aide d’un modèle de scale-out. |Instances SQL Server avec 64 To de stockage maximum. L’instance peut prendre en charge autant de bases de données que nécessaire. | |
|  | | |
| **Ressources :** |Vous ne souhaitez pas utiliser les ressources informatiques pour la configuration et la gestion de l’infrastructure sous-jacente, mais vous voulez vous concentrer sur la couche d’application. |Vous disposez de ressources informatiques pour la configuration et la gestion. Certaines fonctionnalités automatisées simplifient considérablement cette procédure. |
| **Coût total de possession :** |Élimine les coûts matériels et réduit les coûts d’administration. |Élimine les coûts matériels. |
| **Continuité des activités :** |Outre les fonctionnalités intégrées d’infrastructure de tolérance de panne, Azure SQL Database offre des fonctionnalités comme les [sauvegardes automatisées](sql-database-automated-backups.md), la [limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore), la [géo-restauration](sql-database-recovery-using-backups.md#geo-restore) et la [géo-réplication active](sql-database-geo-replication-overview.md) afin d’améliorer la continuité d’activité. Pour plus d’informations, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure](sql-database-business-continuity.md). |L’option SQL Server sur les machines virtuelles Azure vous permet de configurer une solution de haute disponibilité et de récupération d’urgence pour les besoins spécifiques de votre base de données. Vous pouvez donc disposer d'un système hautement optimisé pour votre application. Vous pouvez tester et exécuter des basculements par vous-même si nécessaire. Pour plus d’informations, voir [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Cloud hybride :** |Votre application locale peut accéder aux données d’Azure SQL Database. |Avec l’option SQL Server sur les machines virtuelles Azure, vous pouvez avoir des applications qui s’exécutent en partie dans le cloud et en partie localement. Par exemple, vous pouvez étendre votre réseau local et votre domaine Active Directory au cloud par le biais d’ [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). En outre, vous pouvez stocker des fichiers de données locaux dans Azure Storage en utilisant les [fichiers de données SQL Server dans Azure](http://msdn.microsoft.com/library/dn385720.aspx). Pour plus d’informations, consultez la rubrique [Introduction au cloud hybride SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Prend en charge la [réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) en tant qu’abonné pour la réplication des données. |Prend entièrement en charge la [réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), les [groupes de disponibilité AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services et la copie des journaux de transaction pour la réplication des données. En outre, les sauvegardes traditionnelles SQL Server sont entièrement prises en charge | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Critères pour choisir entre Azure SQL Database et SQL Server sur les machines virtuelles Azure
### <a name="cost"></a>Coût
Que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires, le manque de capitaux est souvent le principal moteur pour décider du mode d'hébergement des bases de données. Dans cette section, vous découvrirez d’abord les notions de base de facturation et de licence dans Azure pour ces deux options de base de données relationnelles : SQL Database et SQL Server sur les machines virtuelles Azure. Vous apprendrez également à calculer le coût total de l’application.

#### <a name="billing-and-licensing-basics"></a>Notions de base sur la facturation et les licences
**SQL Database** est vendu aux clients en tant que service, sans licence.  [SQL Server sur les machines virtuelles Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) est vendu avec une licence incluse que vous payez à la minute. Si vous disposez déjà d’une licence, vous pouvez également l’utiliser.  

Actuellement, **SQL Database** est disponible en plusieurs niveaux de service, tous facturés à un tarif horaire fixe en fonction du niveau de service et du niveau de performances choisis. Par ailleurs, vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)standard. Les niveaux de service De base, Standard et Premium sont conçus pour fournir des performances prévisibles avec plusieurs niveaux de performances pour répondre aux exigences de pic de votre application. Vous pouvez changer de niveau de service et de niveau de performances pour répondre aux besoins de débit variés de votre application. Si votre base de données présente un volume élevé de transactions et doit prendre en charge de nombreux utilisateurs simultanément, nous vous recommandons d’opter pour le niveau de service Premium. Pour obtenir des informations à jour sur les niveaux de service pris en charge, consultez [Niveaux de service d’Azure SQL Database](sql-database-service-tiers.md). Vous pouvez également créer des [pools élastiques](sql-database-elastic-pool.md) pour partager des ressources de performances entre des instances de base de données.

Avec **SQL Database**, Microsoft configure, corrige et met à niveau automatiquement le logiciel de base de données, ce qui réduit vos coûts d’administration. En outre, ses fonctionnalités de [sauvegarde intégrée](sql-database-automated-backups.md) vous permettent de réaliser d’importantes économies, notamment si vous avez un grand nombre de bases de données.

Avec **SQL Server sur les machines virtuelles Azure**, vous pouvez utiliser une image SQL Server fournie par la plateforme (qui inclut une licence) ou votre licence SQL Server. Toutes les versions SQL Server (2008 R2, 2012, 2014, 2016) et les éditions (Developer, Express, Web, Standard, Enterprise) prises en charge sont disponibles. Les versions BYOL (apportez votre propre licence) des images sont également disponibles. Lorsque vous utilisez les images fournies par Azure, les coûts opérationnels dépendent de la taille de la machine virtuelle et de l’édition SQL Server choisie. Quelle que soit la taille de la machine virtuelle ou l’édition de SQL Server, vous payez à la minute l’utilisation de la licence de SQL Server et Windows Server, ainsi que l’utilisation du stockage Azure pour les disques de machine virtuelle. L’option de facturation à la minute vous permet d’utiliser SQL Server aussi longtemps que vous en avez besoin, sans acheter de licences SQL Server supplémentaires. Si vous utilisez votre propre licence SQL Server sur Azure, seuls les coûts de stockage et de Windows Server vous sont facturés. Pour plus d’informations sur l’utilisation de votre propre licence, consultez [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calcul du coût total de l'application
Lorsque vous utilisez une plateforme cloud, le coût d’exécution de votre application inclut les coûts de développement et d’administration, ainsi que les coûts de service pour la plateforme de cloud public.

Voici le calcul de coûts détaillé pour votre application exécutée dans SQL Database et SQL Server sur les machines virtuelles Azure :

**Avec Azure SQL Database :**

*Coût total de l’application = coûts d’administration très réduits + coûts de développement logiciel + coûts du service SQL Database*

**Avec SQL Server sur les machines virtuelles Azure :**

*Coût total de l’application = coûts considérablement réduits de développement logiciel + coûts d’administration + coûts des licences SQL Server et Windows Server + coûts de stockage Azure*

Pour plus d'informations sur la tarification, consultez les ressources suivantes :

* [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
* [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) pour [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Calcul des coûts Azure](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> Dans SQL Serveur, quelques fonctionnalités ne sont pas applicables ou disponibles avec SQL Database. Pour plus d’informations, consultez [Fonctionnalités de SQL Database](sql-database-features.md) et [Informations sur les instructions Transact-SQL de SQL Database](sql-database-transact-sql-information.md). Si vous déplacez une solution SQL Server vers le nuage, consultez [Migration d’une base de données SQL Server vers une base de données SQL Azure](sql-database-cloud-migrate.md). Lorsque vous migrez une application SQL Server locale vers SQL Database, nous vous recommandons de mettre à jour l’application pour tirer parti des fonctionnalités des services cloud. Par exemple, vous pouvez héberger votre couche applicative dans [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) ou des [Services cloud Azure](https://azure.microsoft.com/services/cloud-services/) pour accroître votre rentabilité.
> 
> 

### <a name="administration"></a>Administration
Pour de nombreuses entreprises, la décision de migrer vers un service cloud vise essentiellement à simplifier la complexité d’administration, qui a un coût. Avec **SQL Database**, Microsoft administre le matériel sous-jacent. Microsoft réplique automatiquement toutes les données pour assurer la haute disponibilité, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et procède au basculement transparent en cas de défaillance du serveur. Vous pouvez continuer d’administrer votre base de données, mais vous n’avez plus à gérer le moteur de base de données, ni le système d’exploitation ou le matériel du serveur.  Parmi les éléments que vous pouvez continuer d’administrer, il y a les bases de données et les connexions, l’optimisation des index et des requêtes, ainsi que l’audit et la sécurité.

Avec **SQL Server sur les machines virtuelles Azure**, vous contrôlez entièrement le système d’exploitation et la configuration de l’instance SQL Server. Avec une machine virtuelle, c’est à vous de décider quand mettre à jour ou à niveau le système d’exploitation et le logiciel de base de données, et quand installer d’autres logiciels tels que l’antivirus. Certaines fonctionnalités automatisées simplifient considérablement la gestion des correctifs, la sauvegarde et la haute disponibilité. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Azure vous permet de modifier la taille d’une machine virtuelle en fonction des besoins. Pour plus d’informations, consultez [Tailles de machines virtuelles et de services cloud pour Azure](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrat de Niveau de Service (SLA)
Pour bon nombre de services informatiques, répondre aux obligations de temps d’exécution d’un contrat de niveau de service (SLA) est la priorité absolue. Dans cette section, nous allons détailler les implications du contrat SLA pour chaque option d’hébergement de base de données.

Pour **Azure SQL Database** , avec les niveaux de service De base, Standard et Premium, Microsoft fournit un contrat SLA dont la disponibilité est de 99,99 %. Pour obtenir les dernières informations, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/sql-database/). Pour obtenir les dernières informations sur les niveaux de service de SQL Database et les plans de continuité des activités pris en charge, consultez [Niveaux de service](sql-database-service-tiers.md).

Pour **SQL Server sur les machines virtuelles Azure**, Microsoft fournit un SLA avec une disponibilité de 99,95 %, qui ne couvre que la machine virtuelle. Ce contrat SLA ne couvre pas les processus (comme SQL Server) exécutés sur la machine virtuelle et nécessite l’hébergement d’au moins deux instances de machine virtuelle dans un groupe à haute disponibilité. Pour plus d’informations, consultez le [contrat SLA de machine virtuelle](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pour obtenir une haute disponibilité de la base de données dans les machines virtuelles, vous devez configurer une des options de haute disponibilité prises en charge dans SQL Server, comme les [groupes de disponibilité AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). L’utilisation d’une option de haute disponibilité prise en charge ne fournit pas de SLA supplémentaire, mais vous pouvez atteindre une disponibilité de base de données > 99,99 %.

### <a name="market"></a>Délai de mise sur le marché
**SQL Database** est la solution idéale pour les applications cloud lorsque la productivité des développeurs et la rapidité de mise sur marché sont essentielles. Avec les fonctionnalités de programmation comme le DBA, il est parfait pour les architectes et les développeurs du cloud, car il tempère la nécessité de gérer le système d'exploitation et la base de données sous-jacents. Par exemple, vous pouvez utiliser [l’API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) et des [applets de commande PowerShell](http://msdn.microsoft.com/library/mt740629.aspx) pour automatiser et gérer les opérations administratives de milliers de bases de données. Les fonctionnalités telles que les [pools élastiques](sql-database-elastic-pool.md) vous permettent de vous concentrer sur la couche Application et de mettre votre solution sur le marché plus rapidement.

**SQL Server sur des machines virtuelles Azure** est idéale si vos applications nouvelles ou existantes requièrent des bases de données volumineuses, des bases de données interdépendantes ou un accès à toutes les fonctionnalités dans SQL Server ou Windows. Elle convient également si vous souhaitez migrer des applications et des bases de données locales en l’état vers Azure. Étant donné que vous n'avez pas besoin de modifier la présentation, l'application et les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. Ainsi, vous pouvez vous concentrer sur la migration de toutes vos solutions vers Azure et sur l’optimisation des performances requises par la plateforme Azure. Pour plus d’informations, consultez [Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Résumé
Cet article a décrit les options SQL Database et SQL Server sur les machines virtuelles Azure, ainsi que les critères les plus courants susceptibles d’influencer votre décision. Voici un résumé des suggestions à prendre en compte :

Choisissez **Azure SQL Database** dans les cas suivants :

* Vous créez des applications cloud pour réaliser des économies et profiter des fonctionnalités d’optimisation des performances offertes par les services cloud. Cette approche apporte les avantages d’un service cloud entièrement géré, permet de réduire le délai de mise sur le marché et optimise les coûts à long terme.
* Vous souhaitez que Microsoft effectue des opérations de gestion courantes sur vos bases de données et vous avez besoin d'une plus grande disponibilité offerte par le biais des contrats SLA pour les bases de données.

Choisissez **SQL Server sur les machines virtuelles Azure** dans les cas suivants :

* Vous disposez d’applications locales existantes que vous souhaitez migrer ou étendre dans le cloud, ou vous souhaitez créer des applications d’entreprise de plus de 4 To. Cette approche offre une compatibilité SQL à 100 %, une capacité de base de données importante, un contrôle total sur SQL Server et Windows et un tunneling sécurisé en local. Cette approche réduit les coûts de développement et de modification des applications existantes.
* Vous disposez de ressources informatiques et vous pouvez bénéficier en fin de compte des correctifs, des sauvegardes et de la haute disponibilité de la base de données. Certaines fonctionnalités automatisées simplifient considérablement ces opérations. 

## <a name="next-steps"></a>Étapes suivantes
* Pour prendre en main SQL Database, consultez la page [Votre première base de données Azure SQL Database](sql-database-get-started-portal.md).
* Voir [Tarification de Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* Pour plus d’informations sur la prise en main de SQL Server sur Azure Virtual Machines, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) .

