---
title: "Exemples de modèles Azure Resource Manager - App Service | Microsoft Docs"
description: "Exemples de modèles Azure Resource Manager - App Service"
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
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Modèles Azure Resource Manager pour Azure Web Apps

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager. Pour obtenir des recommandations sur la prévention des erreurs courantes lors de la création des modèles d’application web, consultez [Guidance on deploying web apps with Azure Resource Manager templates](web-sites-rm-template-guidance.md) (Conseils sur le déploiement d’applications web avec des modèles Azure Resource Manager).

| | |
|-|-|
|**Déployer une application web**||
| [Application web liée à un référentiel GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Déploie une application web Azure qui extrait le code à partir de GitHub. |
| [Application web avec des emplacements de déploiement personnalisés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Déploie une application web Azure avec des environnements/emplacements de déploiement personnalisés. |
|**Configurer une application web**||
| [Certificat d’application web issu de Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Déploie un certificat d’application web Azure issu du secret de Key Vault et l’utilise pour la liaison SSL. |
| [Application web avec un domaine personnalisé](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Déploie une application web Azure avec un nom d’hôte personnalisé. |
| [Application web avec un domaine personnalisé et SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Déploie une application web Azure avec un nom d’hôte personnalisé et obtient le certificat d’application web issu de Key Vault pour la liaison SSL. |
| [Application web avec l’extension GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Déploie une application web Azure avec l’extension de site Golang, ce qui vous permet d’exécuter des applications web développées sur Golang sur Azure. |
| [Application web avec Java 8 et Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Déploie une application web Azure avec Java 8 et Tomcat 8 activés, ce qui vous permet d’exécuter des applications Java dans Azure. |
|**Application web Linux**||
| [Web App sur Linux avec MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Déploie une application web Azure sur Linux avec une base de données Azure pour MySQL. |
| [Web App sur Linux avec PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Déploie une application web Azure sur Linux avec une base de données Azure pour PostgreSQL. |
|**Application web avec des ressources connectées**||
| [Application web avec MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Déploie une application web Azure sur Windows avec une base de données Azure pour MySQL. |
| [Application web avec PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Déploie une application web Azure sur Windows avec une base de données Azure pour PostgreSQL. |
| [Application web avec une base de données SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Déploie une application web Azure et la base de données SQL au niveau du service de « Base ». |
| [Application web avec connexion Stockage Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Déploie une application web Azure avec une chaîne de connexion Stockage Blob, ce qui vous permet d’utiliser Stockage Blob Azure à partir de l’application web. |
| [Application web avec le cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Déploie une application web Azure avec le cache Redis. |
|**Environnement App Service**||
| [Créer un environnement App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Crée un environnement App Service Environment v2 dans votre réseau virtuel. |
| [Créer un environnement App Service Environment v2 avec une adresse ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Crée un environnement App Service Environment v2 dans votre réseau virtuel avec une adresse d’équilibreur de charge interne privé. |
| [Configurer le certificat SSL par défaut pour un environnement App Service Environment avec une adresse ILB ou un environnement App Service Environment v2 avec une adresse ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configure le certificat SSL par défaut pour un environnement App Service Environment avec une adresse ILB ou un environnement App Service Environment v2 avec une adresse ILB. |
| | |
