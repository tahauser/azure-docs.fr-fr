---
title: "Systèmes d’exploitation invités pour Azure Stack | Microsoft Docs"
description: "Ces systèmes d’exploitation invités peuvent être utilisés avec Azure Stack."
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: c9f5bee38772623fb79fa081be8eaece981cc8ab
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Systèmes d’exploitation invités pris en charge par Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="windows"></a>Windows
Azure Stack prend en charge les systèmes d’exploitation invités Windows suivants. Des images peuvent être téléchargées depuis la Place de marché vers Azure Stack. Les images des clients Windows ne sont pas disponibles sur la Place de marché.

Au cours du déploiement, Azure Stack s’assure qu’une version appropriée de l’agent invité est injectée dans l’image.

| Système d’exploitation | DESCRIPTION | Publisher | Type de système d’exploitation | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 bits | Microsoft | Windows | Centre de données |
| Windows Server 2012 | 64 bits | Microsoft | Windows | Centre de données |
| Windows Server 2012 R2 | 64 bits | Microsoft | Windows | Centre de données |
| Windows Server 2016 | 64 bits | Microsoft | Windows | Centre de données, centre de données principal, centre de données avec conteneurs |
| Windows 7 | 64 bits, Professionnel et Entreprise | Microsoft | Windows | Non  |
| Windows 8.1 | 64 bits, Professionnel et Entreprise | Microsoft | Windows | Non  |
| Windows 10 *(voir remarque 1)* | 64 bits, Professionnel et Entreprise | Microsoft | Windows | Non  |

***Remarque 1 :*** *pour déployer des systèmes d’exploitation clients Windows 10 sur Azure Stack, vous devez disposer d’une [licence Windows par utilisateur](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) ou acheter auprès d’un Qualified Multitenant Hoster ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*


## <a name="linux"></a>Linux

Les distributions Linux répertoriées ici incluent l’agent Windows Azure Linux (WALA) nécessaire.

> [!NOTE]   
> Les images créées avec des versions WALA antérieures à la version 2.2.3 ne sont *pas* prises en charge et risquent de ne pas se déployer. Certaines versions de l’agent WALA sont connues pour ne pas fonctionner sur les machines virtuelles Azure Stack, notamment les versions 2.2.12 et 2.2.13.


| Distribution | DESCRIPTION | Publisher | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Conteneur Linux |  64 bits | CoreOS | Stable |
| CentOS-based 6.9 | 64 bits | Rogue Wave | OUI |
| Basé sur CentOS 7.3 | 64 bits | Rogue Wave | OUI |
| CentOS-based 7.4 | 64 bits | Rogue Wave | OUI |
| Debian 8 Jessie | 64 bits | credativ |  OUI |
| Debian 9 "Stretch" | 64 bits | credativ | OUI |
| Oracle Linux | 64 bits | Oracle | Non  |
| Red Hat Enterprise Linux 7.x | 64 bits | Red Hat | Non  |
| SLES 11SP4 | 64 bits | SUSE | OUI |
| SLES 12SP3 | 64 bits | SUSE | OUI |
| Ubuntu 14.04-LTS | 64 bits | Canonical | OUI |
| Ubuntu 16.04-LTS | 64 bits | Canonical | OUI |

D’autres distributions de Linux pourraient être prises en charge à l’avenir.
