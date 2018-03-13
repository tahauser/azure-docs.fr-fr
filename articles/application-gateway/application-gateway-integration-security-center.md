---
title: "Intégration d’Application Gateway à Azure Security Center | Microsoft Docs"
description: "Cette page fournit des informations sur l’intégration d’Application Gateway à Azure Security Center."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: 68d4f9cb5fc9c9f15a355d9fdade922889d2aa30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Vue d’ensemble de l’intégration entre Application Gateway et Azure Security Center

Découvrez comment Application Gateway et Azure Security Center protègent vos ressources d’application web. Le pare-feu d’applications web (WAF) de la passerelle d’application s’intègre à [Security Center](../security-center/security-center-intro.md) afin d’offrir une vision claire permettant de bloquer, détecter et traiter les menaces auxquelles les applications web non protégées de votre environnement sont confrontées.

## <a name="overview"></a>Vue d'ensemble

Le WAF d’Application Gateway est une recommandation du Security Center visant à protéger les applications web des failles de sécurité et des vulnérabilités. Les ressources web qui ne sont pas protégées par le WAF apparaissent dans le centre de sécurité sous la forme de recommandations à gravité élevée. Les recommandations relatives aux pare-feux d’applications web s’affichent sur la page **Vue d’ensemble** sous **Applications**.

![Intégration au centre de sécurité][1]

Cliquez sur n’importe quelle recommandation relative au pare-feu d’applications web pour ouvrir une nouvelle page présentant les détails de la recommandation.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Ajouter un pare-feu d’applications web à une ressource existante

Accédez à **Tous les services** > **Sécurité + Identité** > **Security Center**, puis dans **Security Center - Vue d’ensemble**, cliquez sur **Applications**. Dans **Security Center - Applications**, la table contient une liste d’applications détectées par Security Center dans votre abonnement.

![applications web][3]

Cliquez sur une application web confrontée à un problème critique pour visualiser la page **État de la sécurité de l’application**. Dans l’image ci-dessous, l’application web qui n’est pas protégée par un pare-feu d’applications web s’affiche. 

![ressources web non protégées][2]

Cliquez sur **Ajouter un pare-feu d’application web** sous **Recommandations** pour ouvrir la page **Ajouter un pare-feu d’application web**.

Si vous ne disposez d’aucune passerelle Application Gateway ou que vous souhaitez en créer une, cliquez sur **Créer nouveau**, puis dans **Nouveau pare-feu d’application web**, cliquez sur **Microsoft - Application Gateway**. Vous serez dirigé vers les étapes de création d’une passerelle Application Gateway. À ce stade, votre application web est ajoutée sous forme de ressource protégée. Security Center effectue désormais le suivi de protection par un pare-feu d’applications web de cette ressource. Cela n’a pas pour effet de l’ajouter comme membre du pool principal.

S’il existe une passerelle d’application, vous pouvez la choisir sous **Utiliser la solution existante**

![Page d’ajout de pare-feu d’applications web][4]

L’ajout d’une application web à une passerelle d’application par le biais de Security Center n’ajoute pas la ressource en tant que membre du pool principal. Cette opération doit être effectuée directement sur la ressource de la passerelle d’application.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Ajouter une ressource à un pare-feu d’applications web existant

Accédez à **Tous les services** > **Sécurité + Identité** > **Security Center**, puis dans **Security Center - Vue d’ensemble**, cliquez sur **Solutions de partenaire**. Les passerelles d’application prenant en charge Security Center s’affichent dans la page **Solutions de partenaire**.

![Solutions de partenaires][7]

Cliquez sur **Associer l’application** pour ouvrir **Associer les applications**. Cet emplacement vous permet de sélectionner des applications existantes. Choisissez les applications à protéger, puis cliquez sur **OK**. Cette méthode n’ajoute pas l’application web au pool principal de la passerelle d’application. Cette opération définit les ressources sous forme de ressource protégée de sorte que Security Center puisse en assurer le suivi. Pour ajouter la ressource en tant que membre du pool principal, vous devez effectuer cette opération sur la passerelle d’application. Dans la page active, vous pouvez cliquer sur **Console de solution** afin d’accéder à la ressource de passerelle d’application qui vous permet d’ajouter l’application web au pool principal.

![applications de solutions de partenaires][6]

## <a name="finalize-configuration"></a>Finaliser la configuration

Security Center effectue le suivi des applications ajoutées à une passerelle d’application sous forme de ressource protégée.  Il analyse l’état de cette ressource et permet de s’assurer qu’elle est protégée par une passerelle d’application. L’étape suivante consiste à ajouter l’adresse IP privée, l’adresse IP publique ou la carte réseau de votre machine virtuelle au pool principal de la passerelle d’application. Tant que cette opération n’est pas effectuée, une recommandation supplémentaire de **finalisation de la protection de l’application** s’affiche jusqu’à ce que la ressource soit ajoutée.

![Page d’ajout de pare-feu d’applications web][5]

## <a name="security-alerts"></a>Alertes de sécurité

Dans Security Center, accédez à **DÉTECTION** > **Alertes de sécurité**.  Vous y trouverez des alertes WAF relatives aux passerelles de votre application. Les alertes sont réparties selon la règle WAF.

![alertes de sécurité][8]

Cliquez sur une règle pour fournir une liste des alertes relatives à cette règle WAF spécifique. Chaque alerte affiche des informations supplémentaires sur la recherche. Les informations comportent un lien vers la passerelle d’application.
 
![détails de l’alerte][9]

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer un pare-feu d’applications web sur une passerelle d’application existante, consultez [Créer ou mettre à jour une passerelle d’application Azure avec un pare-feu d’applications web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png