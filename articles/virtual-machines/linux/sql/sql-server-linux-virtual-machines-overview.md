---
title: Présentation de SQL Server sur les machines virtuelles Linux Azure | Microsoft Docs
description: Découvrez comment exécuter les éditions complètes de SQL Server sur les machines virtuelles Linux Azure. Obtenez des liens directs vers toutes les images de machine virtuelle SQL Server Linux et le contenu associé.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 03/22/2018
ms.author: jroth
ms.openlocfilehash: e752ad844a6efe572564e7081ebac87193e9c2a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Présentation de SQL Server sur les machines virtuelles Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Cette rubrique décrit les options d’exécution de SQL Server sur des machines virtuelles Linux Azure et fournit des [liens vers des images de portail](#create).

> [!NOTE]
> Si vous connaissez déjà SQL Server et que vous souhaitez simplement savoir comment déployer une machine virtuelle Linux SQL Server, consultez [Approvisionner une machine virtuelle SQL Server Linux dans le portail Azure](provision-sql-server-linux-virtual-machine.md). Ou si vous souhaitez créer une machine virtuelle Windows avec SQL Server, consultez [Approvisionner une machine virtuelle SQL Server Windows dans le portail Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Si vous êtes administrateur de base de données ou développeur, les machines virtuelles Azure vous offrent un moyen de déplacer vers le cloud vos applications et charges de travail SQL Server locales.

## <a name="scenarios"></a>Scénarios

Vous pouvez choisir d’héberger vos données dans Azure pour de nombreuses raisons. Si vous développez ou migrez votre application dans Azure, il améliore les performances pour également localiser les données du serveur principal dans Azure. Vous avez automatiquement accès à plusieurs centres de données pour assurer les récupérations d’urgence et votre présence mondiale. Les données sont également parfaitement sécurisés et fiables.

L’exécution de SQL Server sur des machines virtuelles Azure vous permet de stocker des données relationnelles dans Azure. Vous avez également la possibilité d’utiliser le service Azure SQL Database. Pour plus d’informations sur le choix entre SQL Server sur des machines virtuelles par rapport à Azure SQL Database, consultez [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) (Choisir une option SQL Server Cloud : Azure SQL (PaaS) Database ou SQL Server sur machines virtuelles Azure (IaaS).

## <a id="create"></a> Créer une nouvelle machine virtuelle SQL

Vous trouverez des instructions pas à pas pour créer une nouvelle machine virtuelle SQL dans le didacticiel [Approvisionner une machine virtuelle SQL Server Linux dans le portail Azure](provision-sql-server-linux-virtual-machine.md).

Le tableau suivant récapitule les images les plus récentes liées à SQL Server dans la galerie de machines virtuelles. Cliquez sur un lien pour commencer à créer une machine virtuelle SQL avec la version, l’édition et le système d’exploitation de votre choix.

> [!TIP]
> Pour en savoir plus sur la tarification des machines virtuelles SQL pour ces images, consultez la [page de tarification relative aux machines virtuelles SQL Server Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Système d’exploitation | Édition |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Pour afficher les images de machines virtuelles Windows SQL Server disponibles, consultez [Présentation de SQL Server sur les machines virtuelles Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Packages installés

Lorsque vous configurez SQL Server sur Linux, vous installez le package du moteur de base de données et plusieurs packages facultatifs selon vos besoins. Les images de machines virtuelles Linux pour SQL Server installent automatiquement la plupart des packages pour vous. Le tableau suivant montre les packages installés pour chaque distribution.

| Distribution | [Moteur de base de données](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [outils](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Recherche en texte intégral](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Module complémentaire HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon de configurer et d’utiliser SQL Server sur Linux, consultez [Vue d’ensemble de SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
