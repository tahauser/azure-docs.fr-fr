---
title: Exemples de modèles Azure Resource Manager - App Service | Microsoft Docs
description: Exemples de modèles Azure Resource Manager pour la fonctionnalité Web Apps d’App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Modèles Azure Resource Manager pour Web Apps

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour la fonctionnalité Web Apps d’Azure App Service. Pour obtenir des recommandations sur la prévention des erreurs courantes lors de la création des modèles d’application web, consultez [Aide sur le déploiement d’application Web avec des modèles Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Déploiement d’une application web**||
| [Application web liée à un référentiel GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Déploie une application web Azure qui extrait le code à partir de GitHub. |
| [Application web avec des emplacements de déploiement personnalisés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Déploie une application web Azure avec des environnements/emplacements de déploiement personnalisés. |
|**Configuration d’une application web**||
| [Certificat d’application web issu de Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Déploie un certificat d’application web Azure issu d’un secret Azure Key Vault et l’utilise pour la liaison SSL. |
| [Application web avec un domaine personnalisé](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Déploie une application web Azure avec un nom d’hôte personnalisé. |
| [Application web avec un domaine personnalisé et SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Déploie une application web Azure avec un nom d’hôte personnalisé et obtient un certificat d’application web issu de Key Vault pour la liaison SSL. |
| [Application web avec l’extension GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Déploie une application web Azure avec l’extension de site GoLang. Vous pouvez ensuite exécuter des applications web développées sur GoLang sur Azure. |
| [Application web avec Java 8 et Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Déploie une application web Azure pour laquelle Java 8 et Tomcat 8 sont activés. Vous pouvez ensuite exécuter des applications Java dans Azure. |
|**Application web Linux**||
| [Application web sur Linux avec MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Déploie une application web Azure sur Linux avec Azure Database pour MySQL. |
| [Application web sur Linux avec PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Déploie une application web Azure sur Linux avec Azure Database pour PostgreSQL. |
|**Application web avec des ressources connectées**||
| [Application web avec MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Déploie une application web Azure sur Windows avec Azure Database pour MySQL. |
| [Application web avec PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Déploie une application web Azure sur Windows avec Azure Database pour PostgreSQL. |
| [Application web avec une base de données SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Déploie une application web Azure et une base de données SQL au niveau de service De base. |
| [Application web avec une connexion au stockage d’objets blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Déploie une application web Azure avec une chaîne de connexion du stockage d’objets blob Azure. Vous pouvez ensuite utiliser le stockage d’objets blob à partir de l’application web. |
| [Application web avec un cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Déploie une application web Azure avec un cache Redis. |
|**App Service Environment pour PowerApps**||
| [Créer un environnement App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Crée un environnement App Service Environment v2 dans votre réseau virtuel. |
| [Créer un environnement App Service Environment v2 avec une adresse ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Crée un environnement App Service Environment v2 dans votre réseau virtuel avec une adresse d’équilibreur de charge interne privé. |
| [Configurer le certificat SSL par défaut pour un environnement App Service Environment avec une adresse ILB ou un environnement App Service Environment v2 avec une adresse ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configure le certificat SSL par défaut pour un environnement App Service Environment avec une adresse ILB ou un environnement App Service Environment v2 avec une adresse ILB. |
| | |
