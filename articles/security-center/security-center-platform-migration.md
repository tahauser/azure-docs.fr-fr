---
title: "Migration de plateforme Azure Security Center | Documents Microsoft"
description: "Ce document explique certaines modifications apportées à la façon dont les données Azure Security Center sont collectées."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 89970b50a2f7246a43ac9666be4d992649605cbf
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="azure-security-center-platform-migration"></a>Migration de plateforme Azure Security Center

À compter de début juin 2017, Azure Security Center déploie d’importantes modifications quant à la façon dont les données de sécurité sont collectées et stockées.  Ces modifications apportent de nouvelles fonctionnalités, comme la possibilité de rechercher facilement des données de sécurité et un meilleur alignement sur les autres services de gestion et de surveillance Azure.

> [!NOTE]
> La migration de plateforme ne devrait pas influer sur vos ressources de production, et aucune action n’est nécessaire de votre côté.


## <a name="whats-happening-during-this-platform-migration"></a>Que se passe-t-il lors de la migration de la plateforme ?

Auparavant, Security Center utilisait l’agent de surveillance Azure pour collecter des données de sécurité sur vos machines virtuelles. Cela inclut des informations sur les configurations de sécurité, qui servent à identifier les vulnérabilités, et les événements de sécurité, qui sont utilisés pour détecter les menaces. Ces données étaient stockées dans vos comptes de stockage dans Azure.

À partir de maintenant, Security Center utilise Microsoft Monitoring Agent (le même agent que celui utilisé par Operations Management Suite et le service Log Analytics). Les données collectées à partir de cet agent sont stockées dans un [espace de travail](../log-analytics/log-analytics-manage-access.md) *Log Analytics* existant associé à votre abonnement Azure ou dans un nouvel espace de travail, en tenant compte de la zone géographique de la machine virtuelle.

## <a name="agent"></a>Agent

Dans le cadre de la transition, l’agent Microsoft Monitoring Agent (pour [Windows](../log-analytics/log-analytics-windows-agent.md) ou [Linux](../log-analytics/log-analytics-linux-agents.md)) est installé sur toutes les machines virtuelles Azure à partir desquelles les données sont actuellement collectées.  Si Microsoft Monitoring Agent est déjà installé sur la machine virtuelle, Security Center utilise cet agent.

Pendant quelque temps (en général, quelques jours), les deux agents s’exécuteront côte à côte pour assurer une transition en douceur sans aucune perte de données. Cela permettra à Microsoft de vérifier que le nouveau pipeline de données est opérationnel avant de mettre fin à l’utilisation du pipeline actuel. Après vérification, l’agent de surveillance Azure sera retiré de vos machines virtuelles. Aucune action n’est requise de votre part. Un message électronique vous informera lorsque tous les clients auront été migrés.
 
Il est déconseillé de désinstaller manuellement l’agent de surveillance Azure lors de la migration, car cela risque d’entraîner des lacunes dans les données de sécurité. Veuillez consulter le [service clientèle et support technique de Microsoft](https://support.microsoft.com/contactus/) si vous avez besoin d’aide. 

Microsoft Monitoring Agent pour Windows requiert le port TCP 443. Lisez le [Guide de résolution des problèmes d’Azure Security Center](security-center-troubleshooting-guide.md) pour plus d’informations.


> [!NOTE] 
> Étant donné que Microsoft Monitoring Agent peut être utilisé par d’autres services de surveillance et de gestion Azure, cet agent n’est pas désinstallé automatiquement lorsque vous désactivez la collecte de données dans Security Center. Toutefois, vous pouvez le désinstaller manuellement si nécessaire.

## <a name="workspace"></a>Espace de travail

Comme décrit précédemment, les données collectées à partir de Microsoft Monitoring Agent (pour le compte de Security Center) sont stockées dans un espace de travail Log Analytics existant associé à votre abonnement Azure ou dans un nouvel espace de travail, en tenant compte de la zone géographique de la machine virtuelle.

Dans le portail Azure, vous pouvez parcourir le contenu pour afficher la liste de vos espaces de travail Log Analytics, y compris ceux créés par Security Center. Un groupe de ressources associées sera créé pour les nouveaux espaces de travail. Les deux respectent la convention d’affectation de noms suivante :

- Espace de travail : *DefaultWorkspace-[ID d’abonnement]-[zone géographique]*
- Groupe de ressources : *DefaultResouceGroup-[zone géographique]* 
 
Pour les espaces de travail créés par Security Center, les données sont conservées pendant 30 jours. Pour les espaces de travail existants, la rétention dépend du niveau tarifaire de l’espace de travail.

> [!NOTE]
> Les données précédemment collectées par Security Center demeurent dans vos comptes de stockage. Une fois la migration terminée, vous pouvez supprimer ces comptes de stockage.

### <a name="oms-security-solution"></a>Solution de sécurité OMS 

Pour les clients existants qui n’ont pas installé la solution de sécurité OMS, Microsoft l’installe dans leur espace de travail, mais en ciblant uniquement les machines virtuelles Azure. Ne désinstallez pas cette solution, car il n’existe aucune correction automatique si cette opération est effectuée à partir de la console de gestion OMS.


## <a name="other-updates"></a>Autres mises à jour

Conjointement à la migration de la plateforme, nous déployons quelques mises à jour mineures supplémentaires :

- Des versions de système d’exploitation supplémentaires seront prises en charge. Consultez la liste [ici](security-center-faq.md#virtual-machines).
- La liste des vulnérabilités du système d’exploitation sera étendue. Consultez la liste [ici](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Les [prix](https://azure.microsoft.com/pricing/details/security-center/) seront calculés au prorata toutes les heures (au lieu de tous les jours, comme auparavant), ce qui entraînera des économies pour certains clients.
- La collecte de données sera nécessaire et automatiquement activée pour les clients du niveau tarifaire Standard.
- Azure Security Center commencera à détecter des solutions anti-programme malveillant qui n’ont pas été déployées via des extensions Azure. La détection de Symantec Endpoint Protection et Defender pour Windows 2016 sera disponible dans un premier temps.
- Les stratégies de protection et les notifications ne sont configurables qu’au niveau d’*abonnement*, mais la tarification peut toujours être définie au niveau du *groupe de ressources*

