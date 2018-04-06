---
title: Présentation de l’authentification dans Azure Automation
description: Cet article présente une vue d’ensemble de la sécurité de l’automatisation et des différentes méthodes d’authentification disponibles pour les comptes Automation dans Azure Automation.
keywords: sécurité de l’automatisation ; automation sécurisée ; authentification d’automatisation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 4280c0de3f5c38aa8a72bad391544b03116e26c9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Présentation de l’authentification dans Azure Automation  
Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs de cloud comme Amazon Web Services (AWS).  Pour qu’un Runbook exécute les actions requises, il doit avoir les autorisations nécessaires pour accéder en toute sécurité aux ressources avec les droits minimaux requis dans l’abonnement.

Cet article couvre les différents scénarios d’authentification pris en charge par Azure Automation et vous montre comment vous lancer en fonction de l’environnement ou des environnements que vous devez gérer.  

## <a name="automation-account-overview"></a>Présentation du compte Automation
Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Les comptes Automation vous permettent d’isoler vos ressources Automation (Runbooks, ressources, configurations) des ressources contenues dans d’autres comptes Automation. Vous pouvez utiliser des comptes Automation pour séparer les ressources dans des environnements logiques distincts. Par exemple, vous pouvez utiliser un compte pour le développement, un autre pour la production et un autre pour l’environnement local.  Un compte Azure Automation est différent de votre compte Microsoft ou des comptes créés dans votre abonnement Azure.

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais les comptes Automation peuvent gérer toutes les ressources dans votre abonnement. L’existence de stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique constitue la raison principale de création de comptes Automation dans différentes régions.

Toutes les tâches que vous effectuez sur les ressources à l’aide d’Azure Resource Manager et des applets de commande Azure dans Azure Automation doivent s’authentifier sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory.  L’authentification basée sur les certificats était la méthode d’authentification d’origine avec Azure Classic, mais elle était compliquée à configurer.  L’authentification auprès d’Azure avec l’utilisateur Azure AD a été réintroduite en 2014 non seulement pour simplifier le processus de configuration d’un compte d’authentification, mais également pour prendre en charge la possibilité de s’authentifier de manière non interactive auprès d’Azure avec un seul compte d’utilisateur compatible avec les ressources Azure Resource Manager et les ressources classiques.   

Actuellement, lorsque vous créez un compte Automation dans le portail Azure, il crée automatiquement :

* Un compte d’identification, qui crée un principal du service dans Azure Active Directory, un certificat, et attribue le contrôle d’accès en fonction du rôle (RBAC) Collaborateur, qui est utilisé pour gérer les ressources Resource Manager à l’aide de Runbooks.
* Un compte d’identification Classic en chargeant un certificat de gestion, qui est utilisé pour gérer les ressources classiques Azure à l’aide de Runbooks.  

Le contrôle d’accès en fonction du rôle est disponible avec Azure Resource Manager pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification, et pour authentifier ce principal du service.  Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md) .  

Les Runbooks exécutés sur un Runbook Worker hybride dans votre centre de données ou sur des services informatiques dans AWS ne peuvent pas utiliser la même méthode que celle généralement utilisée pour l’authentification des Runbooks auprès des ressources Azure.  Cela est dû au fait que ces ressources sont en cours d’exécution en dehors d’Azure et, par conséquent, elles nécessitent leurs propres informations d’identification de sécurité définies dans Automation pour s’authentifier auprès des ressources auxquelles elles accèdent localement.  

## <a name="authentication-methods"></a>Méthodes d’authentification
Le tableau suivant résume les différentes méthodes d’authentification pour chaque environnement pris en charge par Azure Automation et l’article décrivant comment configurer l’authentification pour vos runbooks.

| Méthode | Environnement | Article |
| --- | --- | --- |
| Compte d’utilisateur Azure AD |Azure Resource Manager et Azure Classic |[Authentifier des Runbooks avec un compte d’utilisateur Azure AD](automation-create-aduser-account.md) |
| Compte d’identification Azure |Azure Resource Manager |[Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md) |
| Compte d’identification Azure Classic |Azure Classic |[Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md) |
| Authentification Windows |Centre de données local |[Authentifier des Runbooks pour des Runbook Workers hybrides](automation-hybrid-runbook-worker.md) |
| Informations d'identification AWS |Amazon Web Services |[Authentifier des Runbooks avec Amazon Web Services (AWS)](automation-config-aws-account.md) |
