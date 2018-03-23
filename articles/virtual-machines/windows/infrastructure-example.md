---
title: Procédure pas à pas d’exemple d’infrastructure Azure | Microsoft Docs
description: Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement d’un exemple d’infrastructure dans Azure.
documentationcenter: ''
services: virtual-machines-windows
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c532657951d6d0241a5d8d25a56bb237ad481567
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Procédure pas à pas d’exemple d’infrastructure Azure pour les machines virtuelles Windows
Cet article vous guide à travers la création d’un exemple d’infrastructure d’application. Nous détaillons la conception d’une infrastructure pour un magasin en ligne simple qui réunit toutes les instructions et les décisions concernant les conventions de dénomination, les groupes à haute disponibilité, les réseaux virtuels et équilibreurs de charge, ainsi que le déploiement de vos machines virtuelles.

## <a name="example-workload"></a>Exemple de charge de travail
Adventure Works Cycles souhaite créer une application de magasin en ligne dans Azure, avec :

* Deux serveurs IIS exécutant le client frontal dans une couche web
* Deux serveurs IIS pour le traitement des données et des commandes dans une couche d’application
* Deux instances Microsoft SQL Server avec des groupes de disponibilité AlwaysOn (deux serveurs SQL et un témoin de nœud majoritaire) pour le stockage des données sur les produits et des commandes dans une couche de base de données
* Deux contrôleurs de domaine Active Directory pour les comptes clients et les fournisseurs dans une couche d’authentification
* Tous les serveurs se trouvent dans deux sous-réseaux :
  * un sous-réseau frontal pour les serveurs web 
  * un sous-réseau principal pour les serveurs d’applications, le cluster SQL et les contrôleurs de domaine

![Diagramme de différentes couches pour l’infrastructure d’applications](./media/infrastructure-example/example-tiers.png)

La charge du trafic web entrant sécurisé doit être répartie sur les serveurs web lorsque les clients parcourent le magasin en ligne. Le trafic de traitement des commandes sous la forme de requêtes HTTP provenant des serveurs web doit être équilibré sur les serveurs d’applications. En outre, l’infrastructure doit être conçue pour la haute disponibilité.

La conception qui en résulte doit comprendre :

* Un compte et un abonnement Azure
* un seul groupe de ressources.
* Azure Managed Disks
* un réseau virtuel avec deux sous-réseaux ;
* Des groupes à haute disponibilité pour machines virtuelles avec un rôle similaire
* Machines virtuelles

Tous les éléments ci-dessus sont conformes aux conventions de dénomination :

* Adventure Works Cycles utilise **[Charge de travail informatique]-[Emplacement]-[Ressources Azure]** comme préfixe
  * Pour cet exemple, « **azos** » (Azure On-line Store) est le nom de la charge de travail informatique et « **use** » (États-Unis de l’Est 2) est l’emplacement
* Les réseaux virtuels utilisent AZOS-USE-VN**[numéro]**
* Les groupes à haute disponibilité utilisent azos-use-as-**[rôle]**
* Les noms de machine virtuelle utilisent azos-use-vm-**[nom de machine virtuelle]**

## <a name="azure-subscriptions-and-accounts"></a>Abonnements et comptes Azure
Adventure Works Cycles utilise son abonnement d’entreprise, nommé Adventure Works Enterprise Subscription, pour fournir des informations de facturation pour cette charge de travail informatique.

## <a name="storage"></a>Stockage
Adventure Works Cycles a déterminé que des disques managés Azure doivent être utilisés. Lors de la création de machines virtuelles, les deux niveaux disponibles sont utilisés :

* **Stockage standard** pour les serveurs web, les serveurs d’applications et les contrôleurs de domaine et leurs disques de données.
* **Stockage Premium** pour le stockage Premium de machines virtuelles SQL Server et de leurs disques de données.

## <a name="virtual-network-and-subnets"></a>Réseau virtuel et sous-réseaux
Étant donné que le réseau virtuel n’a pas besoin d’une connectivité continue au réseau Adventure Work Cycles local, il a été décidé d’adopter un réseau virtuel cloud.

Un réseau virtuel cloud a été créé avec les paramètres suivants via le portail Azure :

* Nom : AZOS-USE-VN01
* Emplacement : East US 2
* Espace d’adressage du réseau virtuel : 10.0.0.0/8
* Premier sous-réseau :
  * Nom : FrontEnd
  * Espace d’adressage : 10.0.1.0/24
* Second sous-réseau :
  * Nom : BackEnd
  * Espace d’adressage : 10.0.2.0/24

## <a name="availability-sets"></a>Groupes à haute disponibilité
Pour assurer une haute disponibilité sur les quatre couches de son magasin en ligne, Adventure Work Cycles a adopté quatre groupes à haute disponibilité :

* **azos-use-as-web** pour les serveurs web
* **azos-use-as-app** pour les serveurs d’application
* **azos-use-as-sql** pour les serveurs SQL
* **azos-use-as-dc** pour les contrôleurs de domaine

## <a name="virtual-machines"></a>Machines virtuelles
Adventure Works Cycles a donné les noms suivants à ses machines virtuelles Azure :

* **azos-use-vm-web01** pour le premier serveur web
* **azfae-vm-vm-web02** pour le second serveur web
* **azos-use-vm-app01** pour le premier serveur d’applications
* **azos-use-vm-app02** pour le second serveur d’applications
* **azos-use-vm-sql01** pour le premier serveur SQL Server dans le cluster
* **azos-use-vm-sql02** pour le second serveur SQL Server dans le cluster
* **azos-use-vm-dc01** pour le premier contrôleur de domaine
* **azos-use-vm-dc02** pour le second contrôleur de domaine

Voici la configuration obtenue.

![Infrastructure d’applications finale déployée dans Azure](./media/infrastructure-example/example-config.png)

Cette configuration comprend :

* un réseau virtuel cloud avec deux sous-réseaux (FrontEnd et BackEnd) ;
* des disques managés Azure avec des disques Standard et Premium ;
* quatre groupes à haute disponibilité, un pour chaque niveau du magasin en ligne ;
* les machines virtuelles pour les quatre niveaux ;
* un jeu d’équilibrage de charge externe pour le trafic Web basé sur HTTPS depuis Internet vers les serveurs web ;
* un jeu d’équilibrage de charge interne pour le trafic Web non crypté depuis les serveurs Web vers les serveurs d’applications.
* un seul groupe de ressources.
