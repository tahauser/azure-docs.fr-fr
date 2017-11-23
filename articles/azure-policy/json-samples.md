---
title: "Exemples de modèles de stratégie | Microsoft Docs"
description: Exemples JSON pour Azure Policy
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 11/13/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 4042c3606155d1d37947afccafd64652c9659894
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="templates-for-azure-policy"></a>Modèles pour Azure Policy

Le tableau suivant inclut des liens vers des modèles json pour Azure Policy. Ces exemples se trouvent dans le [dépôt d’exemples d’Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Calcul**||
| [Images de machine virtuelle approuvées](scripts/allowed-custom-images.md) | Exige que seules les images personnalisées approuvées soient déployées dans votre environnement. Spécifiez un tableau d’identifiants d’images approuvées. |
| [Auditer lorsque la machine virtuelle n’utilise pas de disque managé](scripts/create-vm-managed-disk.md) | Vérifie si une machine virtuelle est créée sans utilisation de disques managés.|
| [Auditer si une extension n’existe pas](scripts/audit-ext-not-exist.md) | Vérifie si une extension n’est pas déployée avec une machine virtuelle. Spécifiez l’éditeur de l’extension et le type pour vérifier si elle a été déployée. |
| [Autoriser une image de machine virtuelle personnalisée à partir d’un groupe de ressources](scripts/allow-custom-vm-image.md) |  Exige que les images personnalisées proviennent d’un groupe de ressources approuvées. Spécifiez le nom du groupe de ressources approuvées. |
| [Refuser Hybrid Use Benefit](scripts/deny-hybrid-use.md) | Interdit l’utilisation d’Azure Hybrid Use Benefit (AHUB). À utiliser quand vous ne souhaitez pas autoriser l’utilisation de licences locales. |
| [Extensions de machine virtuelle non autorisées](scripts/not-allowed-vm-ext.md) | Interdit l’utilisation d’extensions spécifiées. Spécifiez un tableau qui contient les types d’extension interdite. |
| [Autoriser uniquement une image de plateforme de machine virtuelle spécifique](scripts/allow-certain-vm-image.md) | Exige que les machines virtuelles utilisent une version spécifique d’UbuntuServer. |
| [Créer une machine virtuelle à l’aide d’un disque managé](scripts/use-managed-disk-vm.md) | Exige que les machines virtuelles utilisent des disques managés.|
|**Surveillance**||
| [Auditer le paramètre de diagnostic](scripts/audit-diag-setting.md) | Vérifie si les paramètres de diagnostic ne sont pas activés pour des types de ressources spécifiés. Spécifiez un tableau de types de ressources pour vérifier si les paramètres de diagnostic sont activés. |
|**Conventions d’affectation de noms et de texte**||
| [Autoriser plusieurs modèles de nom](scripts/allow-multiple-name-patterns.md) | Permettre à un des nombreux modèles de nom d’être utilisé pour les ressources. |
| [Exiger le modèle de similitude](scripts/enforce-like-pattern.md) | Vérifier que les noms de ressource remplissent la condition de similitude d’un modèle. |
| [Exiger le modèle de correspondance](scripts/enforce-match-pattern.md) | Vérifier que les noms de ressource correspondent au modèle d’affectation de noms. |
| [Exiger le modèle de correspondance de balise](scripts/enforce-tag-match-pattern.md) | Vérifier qu’une valeur de balise correspond à un modèle de texte. |
|**Réseau**||
| [Références SKU de passerelle d’application autorisées](scripts/allowed-app-gate-sku.md) | Exige que les passerelles d’application utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Audite si Network Watcher n’est pas activé pour une région](scripts/net-watch-not-enabled.md) | Vérifie si Network Watcher n’est pas activé pour une région spécifiée. Spécifiez le nom de la région pour vérifier si Network Watcher est activé. |
| [NSG X sur chaque carte réseau](scripts/nsg-on-nic.md) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque interface de réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [NSG X sur chaque sou-réseau](scripts/nsg-on-subnet.md) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque sous-réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Bande passante d’Express Route autorisée](scripts/allowed-er-band.md) | Exige que les itinéraires express utilisent un ensemble spécifié de bandes passantes. Spécifiez un tableau de références SKU qui peuvent être spécifiées pour Express Route. |
| [Emplacement d’appairage autorisé pour Express Route](scripts/allowed-peering-er.md) | Exige que les itinéraires express utilisent des emplacements d’appairage spécifiés. Spécifiez un tableau d’emplacements d’appairage autorisés. |
| [Références SKU d’Express Route autorisées](scripts/allowed-er-skus.md) | Exige que les itinéraires express utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU autorisées. |
| [Références SKU d’équilibreur de charge autorisées](scripts/allowed-lb-skus.md) | Exige que les équilibreurs de charge utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU autorisées. |
| [Aucun appairage de réseau avec un réseau ER](scripts/no-peering-er-net.md) | Interdit un appairage de réseau avec un réseau dans un groupe de ressources spécifié. À utiliser pour empêcher la connexion avec l’infrastructure réseau gérée centrale. Spécifiez le nom du groupe de ressources pour empêcher l’association. |
| [Aucune table d’itinéraires définie par l’utilisateur](scripts/no-user-def-route-table.md)  |Interdit le déploiement de réseaux virtuels avec une table d’itinéraires définie par l’utilisateur. |
| [Références SKU de passerelle de réseau virtuel autorisées](scripts/no-user-def-route-table.md) | Exige que les passerelles de réseau virtuel utilisent un type de passerelle et une référence SKU approuvés. Spécifiez un tableau de références SKU approuvées et un tableau de types de passerelle approuvés. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](scripts/use-approved-subnet-vm-nics.md) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](scripts/use-approved-vnet-vm-nics.md) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |
|**Balises**||
| [Initiative de la stratégie de facturation en fonction de balises](scripts/billing-tags-policy-init.md) | Exige des valeurs de balise spécifiées pour le nom de produit et le centre de coût. Utilise des stratégies intégrées pour appliquer et imposer les balises nécessaires. Spécifiez les valeurs nécessaires pour les balises.  |
| [Imposer une balise et sa valeur sur les groupes de ressources](scripts/enforce-tag-rg.md) | Exige une balise et une valeur sur un groupe de ressources. Spécifiez les nom et valeur de balise nécessaires.  |
|**SQL**||
| [Auditer le paramètre d’audit au niveau de la base de données SQL](scripts/audit-sql-db-audit-setting.md) | Vérifie si les paramètres d’audit des bases de données SQL ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés.  |
| [Audit l’état du chiffrement transparent des données](scripts/audit-trans-data-enc-status.md) | Audite le chiffrement transparent des données des bases de données SQL s’il n’est pas activé.  |
| [Auditer le paramètre de détection des menaces au niveau de la base de données](scripts/audit-db-threat-det-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Auditer le paramètre d’audit au niveau du serveur SQL Server](scripts/audit-sql-ser-leve-audit-setting.md) | Vérifie si les paramètres d’audit du serveur SQL Server ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés. |
| [Auditer le paramètre de détection des menaces au niveau du serveur](scripts/audit-sql-ser-threat-det-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Auditer aucun administrateur Azure Active Directory](scripts/audit-no-aad-admin.md) | Vérifier lorsqu’il n’existe aucun administrateur Azure Active Directory affecté au serveur SQL. |
| [Références SKU de base de données SQL autorisées](scripts/allowed-sql-db-skus.md) | Exige que les bases de données SQL utilisent une référence SKU approuvée. Spécifiez un tableau d’identifiants de référence SKU autorisés ou un tableau de noms de référence SKU autorisés. |
|**Stockage**||
| [Références SKU autorisées pour les comptes de stockage et les machines virtuelles](scripts/allowed-skus-storage.md) | Exige que les comptes de stockage et les machines virtuelles utilisent des références SKU approuvées. Utilise des stratégies intégrées pour garantir les références SKU approuvées. Spécifiez un tableau de références SKU de machine virtuelle approuvées et un tableau de références SKU de compte de stockage approuvées. |
| [Garantir uniquement le trafic https pour le compte de stockage](scripts/ensure-https-stor-acct.md) | Exige que les comptes de stockage utilisent le trafic HTTPS.  |
| [Refuser le recours au niveau d’accès froid pour les comptes de stockage](scripts/deny-cool-access-tiering.md) | Interdit le recours au niveau d’accès froid pour les comptes de stockage d’objets blob.  |
| [Vérifier le chiffrement des fichiers de stockage](scripts/ensure-store-file-enc.md) | Exige que le chiffrement soit activé pour les comptes de stockage.  |
|**Stratégie intégrée**||
| [Emplacements autorisés](scripts/allowed-locs.md) | Exige que toutes les ressources soient déployées sur les emplacements approuvés. Spécifiez un tableau d’emplacements approuvées.  |
| [Types de ressources autorisés](scripts/allowed-res-types.md) | Garantit que seuls les types de ressources approuvés sont déployés. Spécifiez un tableau de types de ressources qui sont autorisés.  |
| [Références SKU de compte de stockage autorisées](scripts/allowed-stor-acct-skus.md) | Exige que les comptes de stockage utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Appliquer la balise et sa valeur par défaut](scripts/apply-tag-def-val.md) | Ajoute le nom et la valeur d’une balise spécifiée, si cette balise n’est pas fournie. Spécifiez les nom et valeur de balise à appliquer.  |
| [Imposer une balise et sa valeur](scripts/enforce-tag-val.md) | Exige un nom et une valeur de balise spécifiée. Spécifiez les nom et valeur de balise à imposer.  |
| [Types de ressources non autorisés](scripts/not-allowed-res-type.md) | Empêche le déploiement de types de ressources spécifiés. Spécifiez un tableau des types de ressources à bloquer.  |
| [Exiger SQL Server version 12.0](scripts/req-sql-12.md) | Oblige les serveurs SQL Server à utiliser la version 12.0.  |
| [Exiger le chiffrement du compte de stockage](scripts/req-store-acct-enc.md) | Oblige le stockage de compte à utiliser le chiffrement des objets blob.  |
