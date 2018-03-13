---
title: "Décisions de déploiement déconnecté de Azure pour les systèmes intégrés Azure Stack | Microsoft Docs"
description: "Déterminez les décisions relatives à la planification du déploiement pour les déploiements à plusieurs nœuds de Azure Stack connectés à Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e697dec0f3d104af073fd61bac81a00e182524e1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Décisions relatives à la planification du déploiement déconnecté de Azure pour les systèmes intégrés Azure Stack
Une fois que vous avez décidé [comment vous allez intégrer Azure Stack dans votre environnement de cloud hybride](azure-stack-connection-models.md), vous pouvez finaliser vos décisions de déploiement de Azure Stack.

Avec l’option de déploiement déconnecté de Azure, vous pouvez déployer et utiliser Azure Stack sans connexion à internet. Toutefois, avec un déploiement déconnecté, vous êtes limité à un magasin d’identités AD FS et au modèle de facturation basée sur la capacité. 

Choisissez cette option si :
- Vous disposez de restrictions de sécurité (ou autre) vous obligeant à déployer Azure Stack dans un environnement non connecté à internet.
- Vous voulez empêcher l’envoi de données (y compris les données d’utilisation) à Azure.
- Vous souhaitez utiliser Azure Stack uniquement comme une solution de cloud privé déployée sur votre intranet d’entreprise, et vous n’êtes pas intéressé par des scénarios hybrides.

> [!TIP]
> Parfois, ce type d’environnement est également nommé « scénario sous marin ».

Un déploiement déconnecté ne signifie pas strictement que vous ne pourrez pas connecter plus tard votre instance de Azure Stack à Azure pour des scénarios hybrides de machine virtuelle de locataire. Cela signifie que vous ne disposez pas de connectivité à Azure au cours du déploiement ou que vous ne souhaitez pas utiliser Azure Active Directory comme magasin d’identités.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Fonctionnalités altérées ou non disponibles dans les déploiements déconnectés 
Azure Stack a été conçu pour fonctionner de façon optimale avec une connexion à Azure, il est donc important de noter que certains fonctionnalités sont altérées ou totalement indisponibles avec le mode Déconnecté. 

|Fonctionnalité|Impact dans le mode Déconnecté|
|-----|-----|
|Déploiement de machine virtuelle avec l’extension DSC pour configurer le post-déploiement de machine virtuelle|Altérée - L’extension DSC recherche le dernier WMF sur internet.|
|Déploiement de machine virtuelle avec l’extension Docker pour exécuter des commandes Docker|Altérée – Docker recherche la dernière version sur internet et cette recherche échoue.|
|Liens de documentation dans le portail de Azure Stack|Non disponible – Les liens, tels que Donner votre avis, Aide, Démarrage rapide, etc. utilisant une URL internet, ne fonctionnent pas.|
|Correction/atténuation des alertes faisant référence à un guide de correction en ligne|Non disponible – Les liens de correction d’alerte utilisant une URL internet ne fonctionnent pas.|
|Syndication de la Place de Marché – La possibilité de sélectionner et d’ajouter des packages de galerie directement depuis Azure Marketplace|Non disponible – Cette fonctionnalité nécessite une connexion à Azure et un compte Azure Active Directory.|
|Utilisation des comptes de fédération Azure Active Directory pour gérer un déploiement de Azure Stack|Non disponible – Cette fonctionnalité nécessite une connexion à Azure. Des services de fédération Active Directory (AD FS) avec une instance de Active Directory local doivent être utilisés à la place.|
|Fournisseurs de ressources tels que WebApps et SQL|Non disponible - Les fournisseurs de ressources tels que WebApps et SQL requièrent un accès Internet pour leur contenu.|
|Interface de ligne de commande (CLI)|Altérée – L’interface CLI a des fonctionnalités réduites en termes d’authentification et d’approvisionnement des principes de service.|
|Visual Studio – Cloud discovery|Altérée – Cloud Discovery découvre des clouds différents ou ne fonctionne pas du tout.|
|Visual Studio – AD FS|Altérée – Seul Visual Studio Enterprise prend en charge AD FS.
Télémétrie|Non disponible – Les données de télémétrie pour Azure Stack ainsi que les packages de galerie tiers qui dépendent des données de télémétrie.|
|Certificats|Non disponible – Une connexion à internet est nécessaire pour les services Liste de révocation de certificat (CRL) et Protocole d’état de certificat en ligne (OSCP) dans le cadre du protocole HTTPS.|
|Coffre de clés|Altérée – Un cas d’usage courant du coffre de clés est une application lisant les clés secrètes lors de l’exécution. Pour cela, l’application a besoin d’un principal de service dans le répertoire. Dans Azure Active Directory, les utilisateurs standard (non-administrateurs) sont autorisés par défaut à ajouter des principaux de service. Dans Active Directory (utilisant ADFS), ils ne peuvent pas. Cela constitue un obstacle à l’expérience de bout en bout, car un utilisateur doit toujours passer par un administrateur de répertoire pour ajouter une application.| 

## <a name="learn-more"></a>En savoir plus
- Pour plus d’informations sur les cas d’usage, l’achat, les partenaires et les fabricants de matériel OEM, consultez la page produit [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack, consultez le livre blanc : [Azure Stack : une extension de Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pour en savoir plus sur l’empaquetage et la tarification de Microsoft Azure Stack, [téléchargez le fichier PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Étapes suivantes
[Intégration au réseau du centre de données](azure-stack-network.md)