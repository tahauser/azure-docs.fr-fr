---
title: Disponibilité de SAP HANA sur les machines virtuelles Azure - Vue d’ensemble | Microsoft Docs
description: Opérations de SAP HANA sur les machines virtuelles Azure natives
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Guide de haute disponibilité de SAP HANA pour les machines virtuelles Azure
Azure fournit de nombreuses fonctionnalités qui permettent de déployer des bases de données stratégiques comme SAP HANA dans des machines virtuelles Azure. Ce document fournit des conseils sur la façon de bénéficier d’une certaine disponibilité pour les instances SAP HANA qui sont hébergées dans des machines virtuelles Azure. Dans le document, plusieurs scénarios qui peuvent être implémentés sur l’infrastructure Azure pour augmenter la disponibilité de SAP HANA sur Azure sont décrits. 

## <a name="prerequisites"></a>Prérequis

Ce guide suppose que vous maîtrisiez certains concepts de base d’IaaS (Infrastructure as a Service) sur Azure, notamment : 

- Comment déployer des machines virtuelles ou des réseaux virtuels en utilisant le portail Azure ou PowerShell.
- L’interface de ligne de commande multiplateforme (CLI) Azure, y compris la possibilité d’utiliser des modèles JSON (JavaScript Object Notation).

Ce guide suppose également que vous êtes familiarisé avec l’installation, l’administration et le fonctionnement des instances SAP HANA, en particulier la configuration et les opérations autour de réplication du système HANA ou des tâches telles que la sauvegarde et la restauration des bases de données SAP HANA.

Voici d’autres articles qui offrent une bonne vue d’ensemble des sujets relatifs à SAP HANA dans Azure :

- [Installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Installer la réplication de système SAP HANA sur des machines virtuelles Azure](sap-hana-high-availability.md)
- [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

L’article et le contenu que vous devez connaître sur SAP HANA peuvent être répertoriés comme suit :

- [Haute disponibilité pour SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ : Haute disponibilité pour SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Comment effectuer une réplication de système pour SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [Nouveautés de SAP HANA 2.0 SPS 01 : Haute disponibilité](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Recommandations relatives au réseau pour la réplication de système SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Réplication de système SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Redémarrage automatique du service SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configuration de la réplication de système SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


En plus de vous familiariser avec le déploiement de machines virtuelles dans Azure, nous vous recommandons également de lire l’article [Gestion de la disponibilité des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) avant de poursuivre la définition de votre architecture de disponibilité dans Azure.

## <a name="service-level-agreements-for-different-azure-components"></a>Contrats de niveau de service (SLA) pour différents composants Azure
Azure propose différents contrats SLA de disponibilité pour différents composants, comme la mise en réseau, le stockage et les machines virtuelles. Tous ces contrats SLA sont documentés et accessibles depuis la page [Contrats de niveau de service Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). Si vous examinez le contrat [SLA pour les machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), vous constatez qu’Azure offre deux contrats SLA différents avec deux configurations différentes. Les contrats SLA sont définis comme suit :

- Une machine virtuelle à instance unique utilisant un [Stockage Premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) pour le disque du système d’exploitation et tous les disques de données fournit un pourcentage de temps de disponibilité mensuel de 99,9 %
- Plusieurs machines virtuelles (au moins deux) qui sont organisées dans un [groupe à haute disponibilité Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) fournissent un pourcentage de temps de disponibilité mensuel de 99,95 %

Estimez vos besoins de disponibilité en fonction des contrats SLA que les composants Azure peuvent fournir, puis déterminez les différents scénarios que vous devez implémenter avec SAP HANA pour bénéficier de la disponibilité nécessaire.

## <a name="next-steps"></a>Étapes suivantes
Passez aux documents ci-dessous :

- [Disponibilité de SAP HANA au sein d’une région Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilité de SAP HANA dans l’ensemble des régions Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


