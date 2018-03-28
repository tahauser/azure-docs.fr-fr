---
title: App Service sur Azure Stack Update 1 | Microsoft Docs
description: Découvrez le contenu de la mise à jour d’App Service sur Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Notes de publication d’App Service sur Azure Stack Update 1

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Update 1, ainsi que les problèmes connus. Les problèmes connus ont été divisés selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1802 à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack avant de déployer Azure App Service.
>
>

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Update 1 est **69.0.13698.9**

### <a name="prerequisites"></a>Prérequis


> [!IMPORTANT]
> Azure App Service sur Azure Stack nécessite désormais un [certificat avec caractères génériques à trois sujets](azure-stack-app-service-before-you-get-started.md#get-certificates) en raison des améliorations apportées à la gestion de l’authentification unique pour Kudu dans Azure App Service.  Le nouveau sujet est **** *.sso.appservice.<region>.<domainname>.<extension>**
>
>

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Update 1 inclut les améliorations et correctifs suivants :

- **Haute disponibilité d’Azure App Service** : La mise à jour 1802 Azure Stack permet désormais le déploiement de charges de travail sur des domaines d’erreur.  L’infrastructure d’App Service devient donc tolérante aux pannes, puisqu’elle est déployée sur des domaines d’erreur.  Par défaut, tous les nouveaux déploiements d’Azure App Service comprennent cette fonctionnalité. Pour les déploiements effectués avant la mise à jour 1802 Azure Stack, consultez la [documentation relative aux domaines d’erreur App Service](azure-stack-app-service-fault-domain-update.md).

- **Possibilité d’effectuer le déploiement sur un réseau virtuel existant** : Les utilisateurs peuvent désormais déployer App Service sur Azure Stack sur un réseau virtuel existant.  Les déploiements effectués sur un réseau virtuel existant permettent aux utilisateurs de se connecter à SQL Server et au serveur de fichiers (qui sont nécessaires à Azure) via des ports privés.  Pendant le déploiement, les utilisateurs peuvent choisir d’effectuer le déploiement sur un réseau virtuel existant. Toutefois, ils [doivent créer des sous-réseaux destinés à être utilisés par App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) avant le déploiement.

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**.  Cohérentes avec celles de la version du SDK du portail Azure Stack.

- **Mises à jour des outils et frameworks d’applications suivants** :
    - Ajout de la prise en charge de **.Net Core 2.0**
    - Ajout des versions **Node.JS** :
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Ajout des versions **NPM** :
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Ajout des mises à jour **PHP** :
        - 5.6.32
        - 7.0.26 (x86 et x64)
        - 7.1.12 (x86 et x64)
    - Mise à jour de **Git pour Windows** vers la version 2.14.1
    - Mise à jour de **Mercurial** vers la version 4.5.0

  - Ajout de la prise en charge de la fonctionnalité **HTTPS uniquement** avec la fonctionnalité Domaine personnalisé dans le portail Locataire d’App Service. 

  - Ajout de la validation de la connexion au stockage dans le sélecteur de stockage personnalisé pour Azure Functions 

#### <a name="fixes"></a>Correctifs

- Lorsque vous créez un package de déploiement hors connexion, les utilisateurs ne reçoivent plus le message d’erreur « Accès refusé » quand ils ouvrent le dossier à partir du programme d’installation App Service.

- Des problèmes liés à l’utilisation de la fonctionnalité Domaines personnalisés du portail Locataire d’App Service ont été résolus.

- Il n’est plus possible pour les utilisateurs de choisir des noms d’administrateur réservés lors de l’installation.

- Il est désormais possible de déployer App Service sur un serveur de fichiers **joint à un domaine**.

- La récupération du certificat racine Azure Stack dans le script a été améliorée et il est désormais possible de valider le certificat racine dans le programme d’installation d’App Service.

- L’état incorrect qui était retourné à Azure Resource Manager lors de la suppression d’un abonnement avec un espace de noms Microsoft.Web contenant des ressources est désormais correct.

### <a name="known-issues-with-the-deployment-process"></a>Problèmes connus liés au processus de déploiement

- Il n’existe aucun problème connu lié au déploiement d’Azure App Service sur Azure Stack Update 1.

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Il n’existe aucun problème connu lié à la mise à jour d’Azure App Service sur Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Il n’existe aucun problème connu lié à l’installation d’Azure App Service sur Azure Stack Update 1.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

Reportez-vous aux [notes de publication de la mise à jour 1802 Azure Stack](azure-stack-update-1802.md).

## <a name="see-also"></a>Voir aussi

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
