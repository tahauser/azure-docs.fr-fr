---
title: "Présentation de SQL Server sur les machines virtuelles Azure | Microsoft Docs"
description: "Découvrez comment exécuter les éditions complètes de SQL Server sur les machines virtuelles Azure."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/14/2017
ms.author: jroth
ms.openlocfilehash: 268ae1dae1a247ee63adef22403f89680daa4961
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Qu’est-ce que SQL Server sur les machines virtuelles Azure ? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) vous permet d’utiliser des versions complètes de SQL Server dans le cloud sans devoir gérer du matériel local. Les machines virtuelles SQL Server simplifient également les coûts de licence lorsque vous payez à l’utilisation.

Les machines virtuelles Azure sont exécutées dans différentes [régions géographiques](https://azure.microsoft.com/regions/) du monde entier. Elles offrent également un éventail de [tailles de machine](../sizes.md). La galerie d’images de machine virtuelle vous permet de créer une machine virtuelle SQL Server avec la version, l’édition et le système d’exploitation appropriés. Les machines virtuelles constituent ainsi une bonne solution pour différentes charges de travail SQL Server.

## <a name="automated-updates"></a>Mises à jour automatisées

Les machines virtuelles SQL Server Azure peuvent utiliser la fonctionnalité [Mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md) afin de planifier une fenêtre de maintenance pour l’installation automatique des mises à jour importantes de Windows et de SQL Server.

## <a name="automated-backups"></a>Sauvegardes automatisées

Les machines virtuelles SQL Server Azure peuvent tirer parti de la fonctionnalité [Sauvegarde automatisée](virtual-machines-windows-sql-automated-backup-v2.md), qui crée à intervalles réguliers des sauvegardes de votre base de données dans le stockage blob. Vous pouvez utiliser cette technique manuellement. Pour plus d’informations, voir [Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Haute disponibilité

Si vous avez besoin d’une haute disponibilité, pensez à configurer les groupes de disponibilité SQL Server. Cela implique la présence de plusieurs machines virtuelles SQL Server Azure dans un réseau virtuel. Vous pouvez configurer votre solution de haute disponibilité manuellement, ou vous pouvez utiliser des modèles dans le portail Azure pour une configuration automatique. Pour une vue d’ensemble des options de haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Performances

Les machines virtuelles Azure présentent différentes tailles pour répondre aux diverses demandes de charge de travail. Les machines virtuelles SQL fournissent également une configuration de stockage automatisée, qui est optimisée pour vos besoins en termes de performances. Pour plus d’informations sur la configuration du stockage des machines virtuelles SQL, consultez [Configuration du stockage pour les machines virtuelles SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Pour ajuster les performances, consultez [Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Bien démarrer avec les machines virtuelles SQL

Pour commencer, choisissez une image de machine virtuelle SQL Server avec la version, l’édition et le système d’exploitation requis. Les sections suivantes fournissent des liens directs vers le portail Azure pour les images de la galerie de machines virtuelles SQL Server.

> [!TIP]
> Pour en savoir plus sur la tarification des machines virtuelles SQL pour ces images, consultez l’article [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Tarification des machines virtuelles SQL Server Azure).

> [!TIP]
> Pour comprendre la stratégie de cycle de vie et de mise à jour pour les images de la galerie de machines virtuelles SQL Server, consultez l’article [SQL Server VMs FAQ (Forum aux questions relatif aux machines virtuelles SQL Server)](virtual-machines-windows-sql-server-iaas-faq.md#images).

### <a id="payasyougo"></a> Paiement à l’utilisation
Le tableau suivant fournit une matrice des images SQL Server de paiement à l’utilisation.

| Version | Système d’exploitation | Édition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Pour afficher les images de machines virtuelles Linux SQL Server disponibles, consultez la [présentation de SQL Server sur les machines virtuelles Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

### <a id="BYOL"></a> BYOL (apportez votre propre licence)
Vous pouvez également apporter votre propre licence (modèle BYOL). Dans ce scénario, vous payez uniquement pour la machine virtuelle sans frais supplémentaires pour la gestion de licences SQL Server.  Apporter votre propre licence peut être avantageux sur le long terme pour des charges de travail de production continue. Pour la configuration requise pour utiliser cette option, consultez [Tarification des machines virtuelles SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md).

| Version | Système d’exploitation | Édition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Dans le portail, le nom de ces images comporte le préfixe **{BYOL}**.

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle
Lorsque vous avez créé votre machine virtuelle SQL Server, connectez-vous à celle-ci à partir d’applications ou d’outils, tels que SQL Server Management Studio (SSMS). Pour obtenir des instructions, consultez [Se connecter à une machine virtuelle SQL Server sur Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migration de vos données
Si vous disposez d’une base de données existante, vous voudrez la déplacer vers la machine virtuelle SQL récemment approvisionnée. Pour obtenir la liste des options de migration ainsi que de l’aide, voir [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

## <a name="customer-experience-improvement-program-ceip"></a>Programme d’amélioration du produit (CEIP)
Le Programme d’amélioration du produit est activé par défaut. Il transmet régulièrement des rapports à Microsoft afin de contribuer à améliorer SQL Server. Aucune tâche de gestion n’est requise dans le cadre de ce programme, sauf si vous souhaitez le désactiver après sa mise en service. Vous pouvez personnaliser ou désactiver le CEIP en vous connectant à la machine virtuelle avec le Bureau à distance. Exécutez ensuite l’utilitaire **Rapports d’erreurs et d’utilisation SQL Server** . Suivez les instructions pour désactiver la création de rapports. Pour plus d’informations sur la collecte de données, consultez la [Déclaration de confidentialité de SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx).

## <a name="related-products-and-services"></a>Produits et services associés
### <a name="windows-virtual-machines"></a>Machines virtuelles Windows
* [Présentation des machines virtuelles](../overview.md)

### <a name="storage"></a>Stockage
* [Introduction à Stockage Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Mise en réseau
* [Présentation du réseau virtuel](../../../virtual-network/virtual-networks-overview.md)
* [Adresses IP dans Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Créer un nom de domaine complet dans le Portail Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentation de SQL Server](https://docs.microsoft.com/sql/index)
* [Comparaison Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Étapes suivantes

Prise en main de SQL Server sur des machines virtuelles Azure :

* [Créer une machine virtuelle SQL Server dans le portail Azure](quickstart-sql-vm-create-portal.md)

Obtenir des réponses aux questions fréquemment posées sur les machines virtuelles SQL :

* [Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-faq.md)
