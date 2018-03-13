---
title: "Groupes de disponibilité SQL Server - Machines virtuelles Azure - Vue d’ensemble | Microsoft Docs"
description: "Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Présentation des groupes de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure #

Cet article présente les groupes de disponibilité de SQL Server sur les machines virtuelles Azure. 

Les groupes de disponibilité AlwaysOn sur les machines virtuelles Azure sont similaires aux groupes de disponibilité AlwaysOn locaux. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Ce schéma illustre les parties d’un groupe de disponibilité SQL Server complet dans les machines virtuelles Azure.

![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La différence principale d’un groupe de disponibilité dans les machines virtuelles Azure est que les machines virtuelles, nécessitent un [équilibrage de charge](../../../load-balancer/load-balancer-overview.md). Cet équilibrage de charge stocke les adresses IP de l’écouteur du groupe de disponibilité. Si vous avez plusieurs groupes de disponibilité, chacun requiert un écouteur. Un équilibrage de charge prend en charge plusieurs écouteurs.

Lorsque vous êtes prêt à générer un groupe de disponibilité SQL Server sur des machines virtuelles Azure, consultez ces didacticiels.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Créer automatiquement un groupe de disponibilité à l’aide d’un modèle

[Configurer automatiquement un groupe de disponibilité AlwaysOn dans une machine virtuelle Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Créer manuellement un groupe de disponibilité dans le portail Azure

Vous pouvez également créer les machines virtuelles vous-même sans le modèle. Tout d’abord, remplissez les conditions requises, puis créez le groupe de disponibilité. Consultez les rubriques suivantes : 

- [Remplir les conditions requises pour les groupes de disponibilité AlwaysOn SQL Server sur les machines virtuelles Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Créer un groupe de disponibilité AlwaysOn pour améliorer la disponibilité et la récupération d’urgence](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Étapes suivantes

[Configurer un groupe de disponibilité AlwaysOn SQL Server sur des machines virtuelles dans différentes régions](virtual-machines-windows-portal-sql-availability-group-dr.md).
