---
title: "Fonctionnalités de Log Analytics pour les fournisseurs de services | Microsoft Docs"
description: "Log Analytics permet aux fournisseurs de services gérés (MSP), grandes entreprises, éditeurs de logiciels indépendants (ISV) et fournisseurs de service d’hébergement de gérer et de surveiller les serveurs situés dans l’infrastructure locale ou cloud d’un client."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 3910038e788352df45ab00c0f697d9a5426b3498
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="log-analytics-features-for-service-providers"></a>Fonctionnalités de Log Analytics pour les fournisseurs de services
Log Analytics permet aux fournisseurs de services gérés (MSP), grandes entreprises, éditeurs de logiciels indépendants (ISV) et fournisseurs de service d’hébergement de gérer et de surveiller les serveurs situés dans l’infrastructure locale ou cloud d’un client. 

Les grandes entreprises et les fournisseurs de services présentent de nombreux points communs, en particulier si les ressources informatiques de plusieurs divisions sont gérées par une équipe informatique centralisée. Le terme *fournisseur de services* est utilisé dans ce document par souci de simplicité, mais sachez que les entreprises et d’autres clients bénéficient de la même fonctionnalité.

## <a name="cloud-solution-provider"></a>Fournisseur de solutions cloud
Pour les partenaires et fournisseurs de services qui font partie du programme [Fournisseur de solutions cloud (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), Log Analytics est l’un des services Azure offerts dans le cadre d’un [abonnement Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

Pour Log Analytics, les fonctionnalités suivantes sont activées dans les abonnements *Fournisseur de solutions cloud*.

En tant que *fournisseur de solutions cloud*, vous pouvez :

* Créer des espaces de travail Log Analytics dans l’abonnement d’un client.
* Accéder aux espaces de travail créés par des clients. 
* Ajouter et supprimer l’accès utilisateur à l’espace de travail à l’aide de la gestion des utilisateurs Azure. Notez que la page de gestion des utilisateurs sous Paramètres n’est pas accessible à partir de l’espace de travail d’un client dans le portail OMS.
  * Log Analytics ne prend pas encore en charge l’accès en fonction du rôle ; si un utilisateur se voit affecter l’autorisation `reader` dans le portail Azure, il peut apporter des modifications de configuration dans le portail OMS.

Pour vous connecter à l’abonnement d’un client, vous devez spécifier l’identificateur du client. L’identificateur du client est souvent constitué de la dernière partie de l’adresse e-mail utilisée pour la connexion.

* Dans le portail OMS, ajoutez `?tenant=contoso.com` dans l’URL du portail. Par exemple, `mms.microsoft.com/?tenant=contoso.com`
* Dans PowerShell, utilisez le paramètre `-Tenant contoso.com` quand vous utilisez l’applet de commande `Add-AzureRmAccount`.
* L’identificateur du client est ajouté automatiquement quand vous utilisez le lien `OMS portal` à partir du portail Azure pour ouvrir le portail OMS pour l’espace de travail sélectionné et vous y connecter.

En tant que *client* d’un fournisseur de solutions cloud, vous pouvez :

* Créer des espaces de travail Log Analytics dans un abonnement CSP.
* Accéder aux espaces de travail créés par le CSP.
  * Utilisez le lien `OMS portal` à partir du portail Azure pour ouvrir le portail OMS pour l’espace de travail sélectionné et vous y connecter.
* Afficher et utiliser la page de gestion des utilisateurs sous Paramètres dans le portail OMS

> [!NOTE]
> Les solutions Sauvegarde et Site Recovery incluses pour Log Analytics ne peuvent pas se connecter à un coffre Recovery Services et ne peuvent pas être configurées dans le cadre d’un abonnement CSP. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Gestion de plusieurs clients à l’aide de Log Analytics
Nous vous recommandons de créer un espace de travail Log Analytics pour chaque client que vous gérez. Un espace de travail Log Analytics offre :

* un emplacement géographique pour le stockage des données ; 
* des données granulaires pour la facturation ; 
* l’isolation des données. 
* une configuration unique.

En créant un espace de travail par client, vous pouvez séparer les données des clients et faire le suivi de l’utilisation de chaque client.

Vous trouverez plus d’informations sur quand et pourquoi créer plusieurs espaces de travail dans [Gestion de l’accès à Log Analytics](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Vous pouvez automatiser les opérations de création et de configuration d’espaces de travail pour des clients à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md), de [modèles Resource Manager](log-analytics-template-workspace-configuration.md) ou de l’[API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Les modèles Resource Manager vous permettent d’utiliser une configuration maître pour créer et configurer des espaces de travail. Chaque fois qu’un espace de travail est créé pour un client, vous avez la certitude qu’il est automatiquement configuré selon vos exigences. Quand vous mettez à jour vos exigences, le modèle est mis à jour et réappliqué aux espaces de travail existants. Grâce à ce processus, même les espaces de travail existants répondent à vos nouvelles normes.    

Si vous gérez plusieurs espaces de travail Log Analytics, nous vous recommandons d’intégrer chacun d’entre eux à votre système de création de tickets/console Opérateur existant à l’aide de la fonctionnalité [Alertes](log-analytics-alerts.md). L’avantage de l’intégration des espaces de travail à des systèmes existants, c’est que l’équipe du support peut continuer à suivre les mêmes processus. Log Analytics vérifie régulièrement chaque espace de travail selon les critères d’alerte que vous spécifiez et génère une alerte quand une action est nécessaire.

Pour obtenir des vues personnalisées des données, utilisez la fonctionnalité [tableau de bord](../azure-portal/azure-portal-dashboards.md) dans le portail Azure.  

Pour les rapports exécutifs qui résument les données de plusieurs espaces de travail, vous pouvez utiliser l’intégration entre Log Analytics et [PowerBI](log-analytics-powerbi.md). Si vous souhaitez intégrer un autre système de création de rapports, utilisez l’API Recherche (par le biais de PowerShell ou de [REST](log-analytics-log-search-api.md)) pour exécuter des requêtes et exporter les résultats de la recherche.

## <a name="next-steps"></a>Étapes suivantes
* Automatiser la création et la configuration des espaces de travail à l’aide de [modèles Resource Manager](log-analytics-template-workspace-configuration.md)
* Automatiser la création des espaces de travail à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Utiliser [Alertes](log-analytics-alerts.md) pour intégrer les espaces de travail aux systèmes existants
* Générer des rapports de synthèse à l’aide de [PowerBI](log-analytics-powerbi.md)

