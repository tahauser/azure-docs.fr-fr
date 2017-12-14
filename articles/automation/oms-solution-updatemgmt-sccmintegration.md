---
title: "Cibler des mises à jour en utilisant des collections SCCM dans OMS Update Management | Microsoft Docs"
description: "Cet article vise à vous aider à configurer System Center Configuration Manager avec cette solution pour gérer les mises à jour des ordinateurs gérés SCCM."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: ec97432cd14c6289928f0419c242e1ccc2c8d876
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management"></a>Intégrer System Center Configuration Manager avec OMS Update Management

Les clients qui ont investi dans System Center Configuration Manager pour gérer des PC, serveurs et autres appareils mobiles s’appuient aussi sur sa puissance et sa maturité pour gérer les mises à jour logicielles dans le cadre de leur cycle de gestion des mises à jour logicielles.  

En tirant parti de l’intégration existant aujourd'hui entre OMS et Configuration Manager, vous pouvez signaler et mettre à jour des serveurs Windows gérés en créant et prédéfinissant des déploiements de mises à jour logicielles dans Configuration Manager et obtenir un état détaillé des déploiements de mises à jour terminés à l’aide de la [solution Update Management](../operations-management-suite/oms-solution-update-management.md). Si vous utilisez Configuration Manager pour générer des rapports de conformité de mises à jour, mais pas pour gérer les déploiements de mises à jour avec vos serveurs Windows, vous pouvez continuer d’adresser des rapports à Configuration Manager tout en gérant les mises à jour de sécurité avec la solution OMS Update Management.

## <a name="prerequisites"></a>Composants requis

* Vous devez ajouter la [solution Update Management](../operations-management-suite/oms-solution-update-management.md) à votre espace de travail Log Analytics et la lier à votre compte Automation dans le même groupe de ressources et la même région.   
* Les serveurs Windows actuellement gérés par votre environnement System Center Configuration Manager doivent aussi adresser des rapports à l’espace de travail Log Analytics dans lequel la solution Update Management est aussi activée.  
* Cette fonctionnalité prend en charge la branche actuelle de System Center Configuration Manager, version 1606 et supérieure.  Pour intégrer votre site d’administration centrale Configuration Manager ou un site principal autonome avec Log Analytics et importer des collections, consultez [Connecter Configuration Manager à Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services) ou avoir accès à Microsoft Update s’ils ne reçoivent pas de mises à jour de sécurité de Configuration Manager.   

La façon dont vous gérez les clients hébergés dans Azure IaaS avec votre environnement Configuration Manager existant dépend essentiellement de la connexion dont vous disposez entre les centres de données Azure et votre infrastructure. Cette connexion a une incidence sur les changements de conception que vous pouvez être amené à apporter à votre infrastructure Configuration Manager et sur les coûts liés à la prise en charge de ces changements.  Pour comprendre les considérations de planification que vous devez évaluer avant de poursuivre, consultez [Configuration Manager sur Azure - Questions fréquentes (FAQ)](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking).    

## <a name="configuration"></a>Configuration

### <a name="manage-software-updates-from-configuration-manager"></a>Gérer les mises à jour logicielles à partir de Configuration Manager 

Si vous avez l’intention de continuer à gérer les déploiements de mises à jour à partir de Configuration Manager, effectuez les étapes suivantes.  OMS se connecte à Configuration Manager pour appliquer les mises à jour aux ordinateurs clients connectés à votre espace de travail Log Analytics. Le contenu des mises à jour est disponible dans le cache de l’ordinateur client comme si le déploiement était géré par Configuration Manager.  

1. Créez un déploiement de mises à jour logicielles à partir du site situé en haut de votre hiérarchie Configuration Manager en suivant le processus décrit dans [Déployer des mises à jour logicielles](https://docs.microsoft.com/sccm/sum/deploy-use/deploy-software-updates).  Le seul paramètre qui doit être configuré différemment par rapport à un déploiement standard est l’option **Ne pas installer les mises à jour logicielles** pour contrôler le comportement de téléchargement du package de déploiement. Ce comportement est géré par la solution OMS Update Management en créant un déploiement de mises à jour planifié à l’étape suivante.  

1. Dans le portail OMS, ouvrez le tableau de bord Update Management.  Créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment), puis sélectionnez le regroupement Configuration Manager approprié représenté sous la forme d’un groupe d’ordinateurs OMS dans la liste déroulante.  Gardez à l’esprit les points importants suivants :
    1. Si une fenêtre de maintenance est définie dans le regroupement d’appareils Configuration Manager sélectionné, les membres du regroupement la respectent au détriment du paramètre **Durée** défini dans le déploiement planifié dans OMS.
    1. Les membres du regroupement cible doivent disposer d’une connexion Internet (qu’elle soit directe, via un serveur proxy ou via la passerelle OMS).  

À l’issue du déploiement des mises à jour avec la solution d’OMS, les ordinateurs cibles membres du groupe d’ordinateurs sélectionné installent les mises à jour à l’heure planifiée à partir de leur cache client local.  Vous pouvez [consulter l’état du déploiement des mises à jour](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments) pour surveiller les résultats de votre déploiement.  


### <a name="manage-software-updates-from-oms"></a>Gérer les mises à jour logicielles à partir d’OMS

Pour gérer les mises à jour de machines virtuelles Windows Server qui sont des clients Configuration Manager, vous devez configurer la stratégie de configuration des clients pour désactiver la fonctionnalité de gestion des mises à jour logicielles pour tous les clients gérés par cette solution.  Par défaut, les paramètres client ciblent tous les appareils de la hiérarchie.  Pour plus d’informations sur ce paramètre de stratégie et sur la façon de le configurer, consultez [Guide pratique pour configurer les paramètres client dans System Center Configuration Manager](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings).  

Après avoir apporté cette modification à la configuration, créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment), puis sélectionnez le regroupement Configuration Manager approprié représenté sous la forme d’un groupe d’ordinateurs OMS dans la liste déroulante. 

