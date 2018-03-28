---
title: Architecture du Kit de développement Azure Stack | Microsoft Docs
description: Décrit l’architecture du Kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architecture du Kit de développement Microsoft Azure Stack
Le Kit de développement Azure Stack (ASDK) est un déploiement à nœud unique d’Azure Stack. Tous les composants sont installés sur des machines virtuelles exécutées sur un ordinateur hôte unique. 

## <a name="logical-architecture-diagram"></a>Schéma de l’architecture logique
Le schéma suivant illustre l’architecture logique de l’ASDK et de ses composants.

![Architecture de l’ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Rôles de machine virtuelle
L’ASDK propose des services à l’aide des machines virtuelles suivantes qui sont hébergées sur l’ordinateur hôte du Kit de développement :

| NOM | Description |
| ----- | ----- |
| **AzS-ACS01** | Services de stockage Azure Stack.|
| **AzS-ADFS01** | Services de fédération Active Directory (ADFS).  |
| **AzS-BGPNAT01** | Routeur Edge et fournit des capacités NAT et VPN pour Azure Stack. |
| **AzS-CA01** | Services d’autorité de certification pour les services de rôle Azure Stack.|
| **AzS-DC01** | Services Active Directory, DNS et DHCP pour Microsoft Azure Stack.|
| **AzS-ERCS01** | Machine virtuelle la console de récupération d’urgence. |
| **AzS-GWY01** | Services de passerelle Edge, comme les connexions de site à site VPN pour les réseaux locataires.|
| **AzS-NC01** | Contrôleur réseau qui gère les services réseau Azure Stack.  |
| **AzS-SLB01** | Services multiplexeur d’équilibrage de charge dans Azure Stack pour les clients et les services d’infrastructure Azure Stack.  |
| **AzS-SQL01** | Magasin de données interne pour les rôles d’infrastructure Azure Stack.  |
| **AzS-WAS01** | Portail d’administration Azure Stack et services Azure Resource Manager.|
| **AzS-WASP01**| Portail utilisateur (locataire) Azure Stack et services Azure Resource Manager.|
| **AzS-XRP01** | Contrôleur de gestion d’infrastructure pour Microsoft Azure Stack, notamment les fournisseurs de ressources Calcul, Réseau et Stockage.|


## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les tâches d’administration de base de l’ASDK](asdk-admin-basics.md)
