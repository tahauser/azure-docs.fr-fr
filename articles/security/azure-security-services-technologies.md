---
title: "Services et technologies de sécurité Azure | Microsoft Docs"
description: "Cet article fournit une liste des services et technologies de sécurité Azure."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 1ea707d3d149734dad2d5c76d853ddd667cbf73b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="azure-security-services-and-technologies"></a>Services et technologies de sécurité Azure
Dans nos discussions avec les clients Azure actuels et futurs, une question revient souvent : « Avez-vous une liste de tous les services et technologies de sécurité proposés par Azure ? ».

Lors de l’évaluation des options de fournisseur de services cloud, il est utile de disposer de ces informations.

Ce document fournit une première version de cette liste. Au fil du temps, cette liste sera modifiée et développée, tout comme Azure. La liste est classée par catégories, et la liste des catégories évoluera également au fil du temps. Veillez à consulter cette page régulièrement pour vous tenir au courant de l’évolution de nos technologies et services liés à la sécurité.

## <a name="azure-security---general"></a>Sécurité de Windows Azure – Généralités
* [Centre de sécurité Azure](https://azure.microsoft.com/documentation/services/security-center/)
* [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)
* [Azure Disk Encryption](azure-security-disk-encryption.md)
* [Log Analytics](../log-analytics/log-analytics-overview.md)
* [Dev/Test Labs Azure](https://azure.microsoft.com/documentation/services/devtest-lab/)

## <a name="azure-storage-security"></a>Sécurité Azure Storage
* [Storage Service Encryption Azure](../storage/common/storage-service-encryption.md)
* [Stockage hybride chiffré StorSimple](https://azure.microsoft.com/documentation/services/storsimple/)
* [Chiffrement côté client Azure](../storage/common/storage-client-side-encryption.md)
* [Signatures d’accès partagé Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Clés de compte de stockage Azure](../storage/common/storage-create-storage-account.md)
* [Partages de fichiers Azure avec chiffrement SMB 3.0](../storage/files/storage-dotnet-how-to-use-files.md)
* [Azure Storage Analytics](https://msdn.microsoft.com/library/hh343270.aspx)

## <a name="azure-database-security"></a>Sécurité des bases de données Azure
* [Pare-feu SQL Azure](../sql-database/sql-database-firewall-configure.md)
* [Chiffrement au niveau des cellules SQL Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)
* [Chiffrement de la connexion SQL Azure](../sql-database/sql-database-control-access.md)
* [Authentification SQL Azure](../sql-database/sql-database-control-access.md)
* [Chiffrement systématique SQL Azure](https://msdn.microsoft.com/library/mt163865.aspx)
* [Chiffrement au niveau des colonnes SQL Azure](https://msdn.microsoft.com/library/ms179331.aspx)
* [Chiffrement transparent des données SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx)
* [Audit de base de données SQL Azure](../sql-database/sql-database-auditing.md)

## <a name="azure-identity-and-access-management"></a>Gestion de l’identité et de l’accès Azure
* [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md)
* [Azure Active Directory](../active-directory/active-directory-whatis.md)
* [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-get-started.md)
* [Services de domaine Azure Active Directory](../active-directory-domain-services/active-directory-ds-overview.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)

## <a name="backup-and-disaster-recovery"></a>Sauvegarde et récupération d’urgence
* [Azure Backup](https://azure.microsoft.com/documentation/services/backup/)
* [Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/)

## <a name="azure-networking"></a>Mise en réseau Azure
* [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
* [Passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Application Gateway Azure](../application-gateway/application-gateway-introduction.md)
* [Équilibrage de charge Azure](../load-balancer/load-balancer-overview.md)
* [Azure ExpressRoute](../expressroute/expressroute-introduction.md)
* [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Proxy d’application Azure](../active-directory/active-directory-application-proxy-enable.md)
