---
title: Conditions techniques préalables pour créer une image de machine virtuelle pour Azure Marketplace | Microsoft Docs
description: Découvrez la configuration requise pour créer et déployer une image de machine virtuelle dans Azure Marketplace que d’autres utilisateurs peuvent acheter.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: cf1f061c28dd0c106823d34ad39aac5e577c8b41
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Conditions techniques préalables pour créer une image de machine virtuelle pour Azure Marketplace
Avant de commencer, lisez attentivement cette procédure, de manière à comprendre à quel moment effectuer une étape et pourquoi chaque étape doit être exécutée. Autant que possible, préparez les informations et autres données de votre entreprise, téléchargez les outils nécessaires et/ou créez les composants techniques avant de lancer le processus de création de l’offre. Cet article devrait vous aider à mieux comprendre ces étapes.  

## <a name="download-needed-tools--applications"></a>Télécharger les outils et applications nécessaires
Vous devez préparer les éléments suivants avant de commencer le processus :

* En fonction du système d’exploitation que vous ciblez, installez les [applets de commande Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) ou [l’outil d’interface de ligne de commande Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) à partir de la page de [téléchargements Azure](https://azure.microsoft.com/downloads/).
* Installez l’Explorateur de stockage Azure à partir de CodePlex.
* Téléchargez et installez l’outil « Certification Test Tool for Azure Certified » :
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Vous avez besoin d’un ordinateur Windows pour exécuter l’outil de certification. Si vous n’avez pas d’ordinateur Windows disponible, vous pouvez exécuter l’outil dans une machine virtuelle Windows dans Azure.

## <a name="platforms-supported"></a>Plateformes prises en charge :
Vous pouvez développer des machines virtuelles Azure sur Windows ou Linux. Certains éléments du processus de publication, tels que la création d’un disque dur virtuel compatible Azure, demandent différents outils et étapes en fonction du système d’exploitation que vous utilisez.  

* Si vous utilisez Linux, reportez-vous à la section « Créer un disque dur virtuel compatible Azure (basé sur Linux) » du [Guide de publication d’une image de machine virtuelle](marketplace-publishing-vm-image-creation.md).
* Si vous utilisez Windows, reportez-vous à la section « Créer un disque dur virtuel compatible Azure (basé sur Windows) » du [Guide de publication d’une image de machine virtuelle](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Vous devez accéder à une machine virtuelle Windows pour :
> 
> * Exécuter l’outil de validation de certification.
> * Créer l’URL de signature d’accès partagé au disque dur virtuel pour l’envoi de la certification du disque dur virtuel.
> 
> 

## <a name="develop-your-vhd"></a>Développement de votre disque dur virtuel
Vous pouvez développer des disques durs virtuels Azure dans le cloud ou localement :

* Un développement dans le cloud signifie que toutes les étapes de développement sont effectuées à distance sur un disque dur virtuel résidant sur Azure.
* Un développement local nécessite de télécharger un disque dur virtuel et de le développer dans une infrastructure locale. Bien que cela soit possible, nous ne le recommandons pas. Notez que le développement local pour Windows ou SQL implique que vous disposiez des clés de licence locales correspondantes. Vous ne pouvez pas inclure ou installer SQL Server après la création d'une machine virtuelle. Vous devez également baser votre offre sur une image SQL approuvée du portail Azure. Si vous décidez d’effectuer un développement local, certaines étapes diffèrent de celle d’un développement dans le cloud. Pour les informations correspondantes, consultez [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
