---
title: Cibler des mises à jour en utilisant des collections SCCM dans Azure Automation - Update Management
description: Cet article vise à vous aider à configurer System Center Configuration Manager avec cette solution pour gérer les mises à jour des ordinateurs gérés SCCM.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 481195538a8c0ece572b4589ea2c2303e559fc44
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Intégrer System Center Configuration Manager à Update Management

Les clients qui ont investi dans System Center Configuration Manager pour gérer des PC, serveurs et autres appareils mobiles s’appuient aussi sur sa puissance et sa maturité pour gérer les mises à jour logicielles dans le cadre de leur cycle de gestion des mises à jour logicielles.

Vous pouvez signaler et mettre à jour des serveurs Windows gérés en créant et prédéfinissant des déploiements de mises à jour logicielles dans Configuration Manager et obtenir un état détaillé des déploiements de mises à jour terminés à l’aide de la [solution Update Management](automation-update-management.md). Si vous utilisez Configuration Manager pour générer des rapports de conformité de mises à jour, mais pas pour gérer les déploiements de mises à jour avec vos serveurs Windows, vous pouvez continuer d’adresser des rapports à Configuration Manager tout en gérant les mises à jour de sécurité avec la solution Update Management.

## <a name="prerequisites"></a>Prérequis


* Vous devez avoir ajouté la [solution Update Management](automation-update-management.md) à votre compte Automation.
* Les serveurs Windows actuellement gérés par votre environnement System Center Configuration Manager doivent aussi adresser des rapports à l’espace de travail Log Analytics dans lequel la solution Update Management est aussi activée.
* Cette fonctionnalité est activée dans la branche actuelle de System Center Configuration Manager, version 1606 et supérieure. Pour intégrer votre site d’administration centrale Configuration Manager ou un site principal autonome avec Log Analytics et importer des collections, consultez [Connecter Configuration Manager à Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services) ou avoir accès à Microsoft Update s’ils ne reçoivent pas de mises à jour de sécurité de Configuration Manager.   

La façon dont vous gérez les clients hébergés dans Azure IaaS avec votre environnement Configuration Manager existant dépend essentiellement de la connexion dont vous disposez entre les centres de données Azure et votre infrastructure. Cette connexion a une incidence sur les changements de conception que vous pouvez être amené à apporter à votre infrastructure Configuration Manager et sur les coûts liés à la prise en charge de ces changements. Pour comprendre les considérations de planification que vous devez évaluer avant de poursuivre, consultez [Configuration Manager sur Azure - Questions fréquentes (FAQ)](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuration

### <a name="manage-software-updates-from-configuration-manager"></a>Gérer les mises à jour logicielles à partir de Configuration Manager 

Si vous avez l’intention de continuer à gérer les déploiements de mises à jour à partir de Configuration Manager, effectuez les étapes suivantes. Azure Automation se connecte à Configuration Manager pour appliquer les mises à jour aux ordinateurs clients connectés à votre espace de travail Log Analytics. Le contenu des mises à jour est disponible dans le cache de l’ordinateur client comme si le déploiement était géré par Configuration Manager.

1. Créez un déploiement de mises à jour logicielles à partir du site situé en haut de votre hiérarchie Configuration Manager en suivant le processus décrit dans [Déployer des mises à jour logicielles](/sccm/sum/deploy-use/deploy-software-updates). Le seul paramètre qui doit être configuré différemment par rapport à un déploiement standard est l’option **Ne pas installer les mises à jour logicielles** pour contrôler le comportement de téléchargement du package de déploiement. Ce comportement est géré par la solution Update Management en créant un déploiement de mises à jour planifié à l’étape suivante.

1. Dans Azure Automation, sélectionnez **Update Management**. Créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](automation-tutorial-update-management.md#schedule-an-update-deployment), puis sélectionnez **Groupes importés** dans la liste déroulante **Type** pour sélectionner le regroupement Configuration Manager approprié. Gardez à l’esprit les points importants suivants : a. Si une fenêtre de maintenance est définie dans le regroupement d’appareils Configuration Manager sélectionné, les membres du regroupement la respectent au détriment du paramètre **Durée** défini dans le déploiement planifié.
    b. Les membres du regroupement cible doivent disposer d’une connexion Internet (qu’elle soit directe, via un serveur proxy ou via la passerelle OMS).

À l’issue du déploiement des mises à jour par le biais d’Azure Automation, les ordinateurs cibles membres du groupe d’ordinateurs sélectionné installent les mises à jour à l’heure planifiée à partir de leur cache client local. Vous pouvez [consulter l’état du déploiement des mises à jour](automation-tutorial-update-management.md#view-results-of-an-update-deployment) pour surveiller les résultats de votre déploiement.

### <a name="manage-software-updates-from-azure-automation"></a>Gérer les mises à jour logicielles à partir d’Azure Automation

Pour gérer les mises à jour de machines virtuelles Windows Server qui sont des clients Configuration Manager, vous devez configurer la stratégie de configuration des clients pour désactiver la fonctionnalité de gestion des mises à jour logicielles pour tous les clients gérés par cette solution. Par défaut, les paramètres client ciblent tous les appareils de la hiérarchie. Pour plus d’informations sur ce paramètre de stratégie et sur la façon de le configurer, consultez [Guide pratique pour configurer les paramètres client dans System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Après avoir apporté cette modification à la configuration, vous créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](automation-tutorial-update-management.md#schedule-an-update-deployment), puis vous sélectionnez **Groupes importés** dans la liste déroulante **Type** pour sélectionner le regroupement Configuration Manager approprié.

## <a name="next-steps"></a>Étapes suivantes
