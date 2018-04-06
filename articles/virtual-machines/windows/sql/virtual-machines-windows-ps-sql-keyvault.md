---
title: Intégrer Key Vault à SQL Server sur des machines virtuelles Windows dans Azure Resource Manager | Microsoft Docs
description: Apprenez à automatiser la configuration du chiffrement de SQL Server pour une utilisation avec Azure Key Vault. Cette rubrique explique comment utiliser l’intégration de coffre de clés Azure avec des machines virtuelles SQL Server créées avec Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: jroth
ms.openlocfilehash: 7df0bc4b74694baa6b1c8a30d0c126b248e51168
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Configurer Azure Key Vault Integration pour SQL Server sur des machines virtuelles Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](virtual-machines-windows-ps-sql-keyvault.md)
> * [Classique](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Vue d'ensemble
Il existe plusieurs fonctionnalités de chiffrement SQL Server, telles que le [chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), le [chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) et le [chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces types de chiffrement nécessitent que vous gériez et stockiez les clés de chiffrement que vous utilisez pour le chiffrement. Le service Azure Key Vault (coffre de clés Azure, AKV) est conçu pour optimiser la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le [connecteur SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server d'utiliser ces clés depuis Azure Key Vault.

Si vous exécutez SQL Server sur des ordinateurs sur site, vous devez [suivre la procédure d'accès à Azure Key Vault à partir de votre ordinateur SQL Server sur site](https://msdn.microsoft.com/library/dn198405.aspx). Mais, pour SQL Server dans des machines virtuelles Azure, vous pouvez gagner du temps à l'aide de la fonctionnalité *Azure Key Vault Integration* .

Lorsque cette fonctionnalité est activée, elle installe automatiquement le connecteur SQL Server, configure le fournisseur EKM pour accéder à Azure Key Vault et crée les informations d'identification vous permettant d'accéder à votre coffre. Si vous avez examiné les étapes décrites dans la documentation locale mentionnée précédemment, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. La seule étape que vous devez encore exécuter manuellement consiste à créer le coffre de clé et des clés. À partir de là, la configuration complète de votre machine virtuelle SQL est automatisée. Une fois que cette fonctionnalité a terminé cette configuration, vous pouvez exécuter des instructions T-SQL pour crypter vos bases de données et vos sauvegardes comme vous y êtes habitué.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Activation et configuration de l’intégration AKV
Vous pouvez activer l’intégration AKV lors de l’approvisionnement ou vous pouvez la configurer pour les machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Si vous approvisionnez une nouvelle machine virtuelle SQL Server avec Resource Manager, le portail Azure permet d’activer l’intégration d’Azure Key Vault. La fonctionnalité Azure Key Vault est disponible uniquement pour les éditions Enterprise, Developer et Evaluation de SQL Server.

![Intégration du coffre de clés Azure SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Consultez la procédure pas à pas détaillée de l’approvisionnement dans l’article [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Puis sélectionnez la section **Configuration de SQL Server** du panneau **Paramètres**.

![Intégration AKV SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Dans le panneau **Configuration de SQL Server**, cliquez sur le bouton **Modifier** dans la section d’intégration du coffre de clés automatisé.

![Configurer l’intégration AKV SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas du panneau **Configuration de SQL Server** pour enregistrer vos modifications.

> [!NOTE]
> Le nom des informations d’identification que nous avons créé ici sera mappé à une connexion SQL ultérieurement. La connexion SQL pourra ainsi accéder au coffre de clés. 
>
>

> [!NOTE]
> Vous pouvez également configurer l’intégration AKV à l’aide d’un modèle. Pour plus d’informations, consultez l’article [Azure quickstart template for Azure Key Vault integration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)(Modèle de démarrage rapide d’Azure pour l’intégration d’Azure Key Vault).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

