---
title: Certifications Microsoft Azure pour SAP | Microsoft Docs
description: Liste mise à jour des configurations et certifications en cours pour SAP sur la plateforme Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 96a3b6f8f8a03019f0446aa7ba42f53298e21009
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifications et configurations SAP en cours sur Microsoft Azure

SAP et Microsoft ont une longue expérience de collaboration dans le cadre d’un fort partenariat qui présente des avantages mutuels pour leurs clients respectifs. Microsoft met constamment à jour sa plateforme et envoie régulièrement de nouvelles informations sur les certifications pour SAP afin de garantir que Microsoft Azure est la meilleure plateforme pour exécuter vos charges de travail SAP. Les tableaux suivants décrivent nos configurations prises en charge et la liste toujours plus grande de certifications. 

## <a name="sap-hana-certifications"></a>Certifications SAP HANA
Références :

- [Note SAP 2316233 - SAP HANA sur Microsoft Azure (grandes instances)](https://launchpad.support.sap.com/#/notes/2316233) traitant les grandes instances HANA relatives au support SAP HANA.
- [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure) pour le support SAP HANA des machines virtuelles Azure natives.

| Produit SAP | Systèmes d’exploitation pris en charge | Offres Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluant le logiciel client HANA composé des pilotes SQLODBC, ODBO (Windows uniquement), ODBC et JDBC, HANA Studio et HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Famille de machine virtuelle de la série D |
| Business One sur HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise | Mise à disposition contrôlée pour GS5, SAP HANA sur Azure (grandes instances) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 pour déploiements à nœud unique dans le cadre de scénarios hors production, SAP HANA sur Azure (grandes instances) |
| HANA Enterprise pour BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 pour déploiements à nœud unique, SAP HANA sur Azure (grandes instances) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 pour déploiements à nœud unique, SAP HANA sur Azure (grandes instances) |

## <a name="sap-netweaver-certifications"></a>Certifications SAP NetWeaver
Microsoft Azure est certifié pour les produits SAP suivants, avec un support complet de la part de Microsoft et de SAP.
Références :

- [1928533 - Applications SAP sur Azure : produits et types de machine virtuelle Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533) pour toutes les applications SAP NetWeaver, notamment SAP TREX, SAP LiveCache et SAP Content Server. Et toutes les bases de données, à l’exception de SAP HANA.


| Produit SAP | SE invité | SGBDR | Types de machine virtuelle |
| --- | --- | --- | --- |
| SAP Business Suite Software |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 pour GS5, D2s_v3 à D64s_v3, E2s_v3 à E64s_v3, M64s à M128ms |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 pour GS5, D2s_v3 à D64s_v3, E2s_v3 à E64s_v3, M64s à M128ms |
| SAP BusinessObjects BI |Windows |N/A |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 pour GS5, D2s_v3 à D64s_v3, E2s_v3 à E64s_v3, M64s à M128ms |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 pour GS5, D2s_v3 à D64s_v3, E2s_v3 à E64s_v3, M64s à M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Autre charge de travail SAP prise en charge sur Azure

| Produit SAP | SE invité | SGBDR | Types de machine virtuelle |
| --- | --- | --- | --- |
| SAP Business One sur SQL Server | Windows  | SQL Server | Tous les types de machine virtuelle certifiés NetWeaver |
| SAP BPC 10.01 MS SP08 | Windows | | Tous les types de machine virtuelle certifiés NetWeaver<br /> Note SAP n° 2451795 |
| Plateforme SAP Business Objects BI | Windows | | Note SAP n° 2145537 |
| SAP Data Services 4.2 | | | Note SAP n° 2288344 |
| Plateforme commerciale SAP Hybris 5.x et 6.x | Windows | SQL Server, Oracle | Tous les types de machine virtuelle certifiés NetWeaver<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
