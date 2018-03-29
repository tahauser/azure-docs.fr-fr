---
title: Notes de publication du kit SDK Azure pour .NET 3.0 | Microsoft Docs
description: Notes de publication du kit SDK Azure pour .NET 3.0
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: 207aa137b25e44baf73e7f481ebc8b6362dfa245
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Notes de publication du kit SDK Azure pour .NET 3.0

Cette rubrique comprend des notes de publication relatives à la version 3.0 du kit SDK Azure pour .NET.

## <a name="azure-sdk-for-net-30-release-summary"></a>Synthèse de publication du kit SDK Azure pour .NET 3.0

Date de sortie : 07/03/2017
 
Dans cette version, aucune modification importante n’a été apportée au kit SDK Azure 3.0. Par ailleurs, aucun processus de mise à niveau n’est requis pour tirer parti de ce SDK avec des projets existants de service cloud. Pour autoriser l’utilisation du Kit de développement logiciel (SDK) Azure 3.0 sans nécessiter de processus de mise à niveau, le Kit SDK Azure 3.0 s’installe dans les mêmes répertoires que le Kit SDK Azure 2.9. La plupart des composants ont gardé la version principale 2.9, mais ont mis à jour le numéro de build.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- Dans Visual Studio 2017, cette version du Kit SDK Azure pour .NET est intégrée dans la charge de travail Azure. Tous les outils nécessaires pour effectuer le développement Azure équipent l’ensemble des versions de Visual Studio, à compter de la version 2017. Pour Visual Studio 2015, le kit SDK est également disponible par le biais de WebPI. Maintenant que Visual Studio 2017 a été publié, nous avons interrompu la publication du kit SDK Azure pour .NET pour Visual Studio 2013.

### <a name="azure-diagnostics"></a>Azure Diagnostics

- Le comportement a été modifié pour stocker uniquement une chaîne de connexion partielle, la clé étant remplacée par un jeton de chaîne de connexion de stockage de diagnostics de services cloud. La véritable clé de stockage est désormais stockée dans le dossier de profil utilisateur, ce qui permet de contrôler son accès. Visual Studio lit la clé de stockage à partir du dossier de profil utilisateur, dans le cadre du processus local de débogage et de publication. 
- En réaction à la modification décrite ci-dessus, l’équipe Visual Studio Online a amélioré le modèle des tâches de déploiement des services cloud Azure, de manière à donner les moyens aux utilisateurs de spécifier la clé de stockage dédiée à la définition de l’extension de diagnostics lors de la publication sur Azure, durant l’intégration et le déploiement continus.
- Nous avons rendu possible le stockage d’une chaîne sécurisée de connexion et la création de jetons pour Azure Diagnostics, pour vous aider à résoudre les problèmes de configuration au sein des différents environnements.
 
### <a name="windows-server-2016-virtual-machines"></a>Machines virtuelles Windows Server 2016

- Visual Studio prend désormais en charge le déploiement de services Cloud sur les machines virtuelles de famille 5 du système d’exploitation (Windows Server 2016). Pour les services cloud existants, vous pouvez modifier vos paramètres de manière à cibler la nouvelle famille du système d’exploitation. Lors de la création de nouveaux services cloud, si vous décidez de créer le service à l’aide de .net 4.6 ou d’une version antérieure, le service utilisera par défaut la famille 5 du système d’exploitation.  Pour plus d’informations, passez en revue le [tableau de prise en charge de la famille de SE invité](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Problèmes connus

- Le kit SDK Azure .NET 3.0 mentionnait un problème survenant lors de la suppression de Visual Studio 2017 dans une configuration côte à côte avec Visual Studio 2015.  Si le kit SDK Windows Azure est installé pour Visual Studio 2015, l’émulateur de stockage Microsoft Azure et l’émulateur de calcul Microsoft Azure sont supprimés si vous désinstallez Visual Studio 2017.  Cela génère une erreur lors de la création et du débogage de projets de services cloud dans Visual Studio 2015. Pour résoudre ce problème, réinstallez le kit SDK Azure à partir de Web Platform Installer.  Ce problème sera résolu dans une prochaine mise à jour de Visual Studio 2017.   .

 
### <a name="azure-in-role-cache"></a>In-Role Cache Azure 

- La prise en charge d’Azure In-Role Cache a pris fin le 30 novembre 2016. Pour plus de détails, cliquez [ici](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




