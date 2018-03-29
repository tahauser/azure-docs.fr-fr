---
title: Présentation des serveurs principaux multilocataires avec Azure Application Gateway | Microsoft Docs
description: Cette page présente la prise en charge des serveurs principaux multilocataires par Application Gateway.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: ''
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: f5ec916fcf45734ab85751e749bd6bb312f05b1a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Prise en charge des serveurs principaux multilocataires par Application Gateway

Azure Application Gateway prend en charge les groupes de machines virtuelles identiques, les interfaces réseau, les adresses IP publiques/privées ou les noms de domaines complets pour ses pools de serveurs principaux. Par défaut, la passerelle d’application ne modifie pas l’en-tête d’hôte HTTP entrant à partir du client et envoie l’en-tête non modifié au serveur principal. Il existe de nombreux services comme [Azure Web Apps](../app-service/app-service-web-overview.md) qui sont multilocataires par nature et s’appuient sur un en-tête d’hôte spécifique ou une extension SNI pour effectuer la résolution vers le point de terminaison approprié. Application Gateway prend désormais en charge la fonctionnalité permettant aux utilisateurs de remplacer l’en-tête d’hôte HTTP entrant en fonction des paramètres HTTP de serveur principal. Cette fonctionnalité permet la prise en charge des applications web Azure de serveurs principaux multilocataires et de la gestion des API. Elle est disponible pour les références SKU standard et WAF. La prise en charge des serveurs principaux multilocataires fonctionne également avec les scénarios SSL de bout en bout et de fin SSL.

![scénario d’application web](./media/application-gateway-web-app-overview/scenario.png)

La possibilité de spécifier un remplacement d’hôte est définie au niveau des paramètres HTTP et peut être appliquée à n’importe quel pool de serveurs principaux lors de la création de règles. Les serveurs principaux multilocataires prennent en charge les deux méthodes de remplacement d’en-tête d’hôte et l’extension SNI suivantes.

1. La possibilité de définir le nom d’hôte sur une valeur fixe dans les paramètres HTTP. Cette fonctionnalité garantit que l’en-tête d’hôte est remplacé par cette valeur pour tout le trafic en direction du pool de serveurs principaux où les paramètres HTTP sont appliqués. Lorsque vous utilisez le protocole SSL de bout en bout, ce nom d’hôte remplacé est utilisé dans l’extension SNI. Cette fonctionnalité permet de mettre en œuvre des scénarios dans lesquels où une batterie de pools de serveurs principaux attend un en-tête d’hôte différent de l’en-tête d’hôte client entrant.

2. La possibilité de dériver le nom d’hôte de l’adresse IP ou du nom de domaine complet des membres du pool de serveurs principaux. Les paramètres HTTP fournissent également une option pour choisir le nom d’hôte à partir d’un nom de domaine complet de membre du pool de serveurs principaux si configuré avec l’option permettant de dériver le nom d’hôte d’un membre du pool de serveurs principaux. Lorsque vous utilisez le protocole SSL de bout en bout, ce nom d’hôte est dérivé du nom de domaine complet et utilisé dans l’extension SNI. Cette fonctionnalité permet de mettre en œuvre des scénarios dans lesquels un pool de serveurs principaux peut avoir deux ou plusieurs services PaaS multilocataires tels que des applications web Azure et l’en-tête de l’hôte de la demande vers chaque membre contient le nom d’hôte dérivé de son nom de domaine complet.

> [!NOTE]
> Dans les deux cas précédents, les paramètres affectent uniquement le comportement du trafic en direct, et pas le comportement de la sonde d’intégrité. Les sondes personnalisées prennent déjà en charge la possibilité de spécifier un en-tête d’hôte dans la configuration de la sonde. Désormais, les sondes personnalisées prennent également en charge la possibilité de dériver le comportement d’en-tête d’hôte des paramètres HTTP configurés actuellement. Cette configuration peut être spécifiée à l’aide du paramètre `PickHostNameFromBackendHttpSettings` dans la configuration de la sonde. Pour que la fonctionnalité de bout en bout fonctionne, la sonde et les paramètres HTTP doivent être modifiés pour refléter la configuration correcte.

Avec cette fonctionnalité, les clients spécifient les options dans les paramètres HTTP et les sondes personnalisées pour la configuration appropriée. Ce paramètre est ensuite lié à un écouteur et à un pool de serveurs principaux à l’aide d’une règle.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer une passerelle d’application avec une application web en tant que membre du pool de serveurs principaux en consultant l’article suivant : [Configurer les applications web App Service avec Application Gateway](application-gateway-web-app-powershell.md)
