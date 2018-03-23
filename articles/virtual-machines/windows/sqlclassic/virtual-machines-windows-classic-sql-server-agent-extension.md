---
title: Automatiser les tâches de gestion sur des machines virtuelles SQL (Classic) | Microsoft Docs
description: Cette rubrique indique comment gérer l’extension Agent SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent Sauvegarde automatisée, Mise à jour corrective automatisée et Azure Key Vault Integration. Cette rubrique utilise le modèle de déploiement classique.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ff9a8b91b0359c57fae5b1a01b5d895ab9a1685
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatiser les tâches de gestion sur des machines virtuelles Azure avec l’extension SQL Server Agent (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Classique](../classic/sql-server-agent-extension.md)
> 
>
 
L’extension Agent IaaS SQL Server (SQLIaaSAgent) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cette rubrique présente les services pris en charge par l’extension, ainsi que des instructions d’installation, d’état et de suppression.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour afficher la version Resource Manager de cet article, consultez [Extension Agent SQL Server pour machines virtuelles SQL Server (Resource Manager)](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Services pris en charge
L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
| --- | --- |
| **Sauvegarde automatisée SQL** |Automatise la planification des sauvegardes de toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (Classic)](../classic/sql-automated-backup.md). |
| **Mise à jour corrective automatisée SQL** |Configure une fenêtre de maintenance pendant laquelle les mises à jour de Windows importantes de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Classic)](../classic/sql-automated-patching.md). |
| **Intégration du coffre de clés Azure** |Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour SQL Server sur des machines virtuelles Azure (Classic)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Prérequis

Configuration requise pour utiliser l’extension Agent IaaS SQL Server sur votre machine virtuelle :

### <a name="operating-system"></a>Système d’exploitation :
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versions de SQL Server :
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell :
[Télécharger et configurer les commandes de la version la plus récente d’Azure PowerShell](/powershell/azure/overview).

Démarrez Windows PowerShell, puis connectez-le à votre abonnement Azure avec la commande **Add-AzureAccount** .

    Add-AzureAccount

Si vous avez plusieurs abonnements, utilisez **Select-AzureSubscription** pour sélectionner l’abonnement qui contient votre machine virtuelle classique cible.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

À ce stade, vous pouvez obtenir la liste des machines virtuelles classiques et de leurs noms de service associés à l’aide de la commande **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installation
Pour les machines virtuelles classiques, vous devez utiliser PowerShell pour installer l’extension de l’agent SQL Server IaaS et configurer les services associés. Utilisez l’applet de commande PowerShell **Set-AzureVMSqlServerExtension** pour installer l’extension. Par exemple, la commande suivante installe l’extension sur une machine virtuelle (classique) Windows Server et la nomme SQLIaaSExtension.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Si vous mettez à jour l’extension de l’agent IaaS SQL vers la dernière version, vous devez ensuite redémarrer votre machine virtuelle.

> [!NOTE]
> Les machines virtuelles classiques ne permettent pas d’installer et de configurer l’extension de l’agent IaaS SQL via le portail.
> 
> 

## <a name="status"></a>Statut
Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez une machine virtuelle répertoriée dans le panneau des machines virtuelles, puis cliquez sur **Extensions**. L’extension **SQLIaaSAgent** doit s’afficher.

![Extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande Azure PowerShell **Get-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Suppression
Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection dans le panneau **Extensions** des propriétés de votre machine virtuelle. Cliquez ensuite sur **Désinstaller**.

![Désinstaller l’extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande PowerShell **Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes
Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus d’informations, consultez les rubriques référencées dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

