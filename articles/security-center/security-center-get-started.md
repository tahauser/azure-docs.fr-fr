---
title: "Démarrage rapide Azure Security Center - Intégrer un abonnement Azure à Security Center Standard | Microsoft Docs"
description: "Ce guide de démarrage rapide explique comment passer au niveau tarifaire Standard de Security Center pour renforcer la sécurité."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 0004db332ec13e23ed49f2c19e3454a516ca6a40
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Guide de démarrage rapide : Intégrer un abonnement Azure à Security Center Standard
Azure Security Center propose des fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble des charges de travail cloud hybrides. Alors que le niveau Gratuit offre une sécurité limitée aux seules ressources Azure, le niveau Standard étend ces fonctions aux ressources locales et à d’autres clouds. Security Center Standard aide à rechercher et à corriger les failles de sécurité, applique des contrôles d’accès et d’application pour bloquer les activités malveillantes, détecte les menaces à l’aide de l’analytique et de l’analyse décisionnelle et répond rapidement en cas d’attaque. Vous pouvez essayer Security Center Standard gratuitement pendant 60 jours.

Cet article montre comment passer au niveau Standard pour renforcer la sécurité et comment installer Microsoft Monitoring Agent sur les machines virtuelles pour surveiller les menaces et les failles de sécurité.

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Pour mettre à niveau un abonnement et passer au niveau Standard, vous devez avoir le rôle de propriétaire de l’abonnement, de collaborateur de l’abonnement ou d’administrateur de la sécurité.

## <a name="enable-your-azure-subscription"></a>Activer un abonnement Azure

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

 ![Vue d’ensemble de Security Center][2]

**Security Center - Vue d’ensemble** donne un aperçu unifié de l’état de sécurité des charges de travail cloud hybrides, ce qui permet de détecter et d’évaluer leur sécurité, ainsi que d’identifier et d’atténuer les risques. Security Center active automatiquement les abonnements Azure que ni vous ni aucun autre utilisateur de l’abonnement n’avez intégrés au niveau Gratuit.

Pour afficher et filtrer la liste des abonnements, cliquez sur l’élément de menu **Abonnements**. Security Center commence alors l’évaluation de la sécurité de ces abonnements pour identifier les failles de sécurité. Pour personnaliser les types d’évaluations, vous pouvez modifier la stratégie de sécurité. Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires.

Quelques minutes après le premier lancement de Security Center peuvent s’afficher :

- des **Recommandations** pour améliorer la sécurité des abonnements Azure ; si vous cliquez sur la vignette **Recommandations**, une liste hiérarchisée apparaît ;
- un inventaire de ressources **Calcul**, **Réseau**, **Stockage et données** et **Applications** en cours d’évaluation par Security Center, avec l’état de sécurité de chacune.

Pour tirer pleinement parti de Security Center, vous devez suivre les étapes ci-dessous afin de passer au niveau Standard et d’installer Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Passer au niveau Standard
Dans le cadre des guides de démarrage rapide et des didacticiels de Security Center, vous devez passer au niveau Standard. Les 60 premiers jours sont gratuits, et vous pouvez revenir au niveau Gratuit à tout moment.

1. Dans le menu principal de Security Center, sélectionnez **Intégration à la sécurité avancée**.

2. Dans **Intégration de la sécurité avancée**, Security Center liste les abonnements et les espaces de travail éligibles à l’intégration. Sélectionnez un abonnement dans la liste.

  ![Sélectionner un abonnement][4]

3. La **Stratégie de sécurité** donne des informations sur les groupes de ressources présents dans l’abonnement. Les **Prix** s’ouvrent également.
4. Dans **Prix**, sélectionnez **Standard** pour passer du niveau Gratuit au niveau Standard et cliquez sur **Enregistrer**.

  ![Sélectionner Standard][5]

Maintenant que vous êtes au niveau Standard, vous avez accès à d’autres fonctionnalités de Security Center, notamment les **contrôles d’application adaptatifs**, **l’accès juste-à-temps aux machines virtuelles**, les **alertes de sécurité**, la **veille des menaces**, **les scénarios d’automatisation** et bien plus encore. Notez que les alertes de sécurité ne s’afficheront que si Security Center détecte des activités malveillantes.

  ![Alertes de sécurité][7]

## <a name="automate-data-collection"></a>Automatiser la collecte de données
Azure Security Center collecte des données à partir de machines virtuelles Azure et d’ordinateurs extérieurs à Azure pour effectuer un monitoring des menaces et des failles de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse. Par défaut, Security Center vous crée un nouvel espace de travail.

Lorsque l’approvisionnement automatique est activé, Security Center installe Microsoft Monitoring Agent sur toutes les machines virtuelles Azure prises en charge et sur toutes les nouvelles. L’approvisionnement automatique est vivement conseillé.

Pour activer l’approvisionnement automatique de Microsoft Monitoring Agent :

1. Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement.
3. Dans **Stratégie de sécurité**, sélectionnez **Collecte de données**.
4. Dans **Collecte de données**, sélectionnez **Activé** pour activer l’approvisionnement automatique.
5. Sélectionnez **Enregistrer**.

  ![Activer l’approvisionnement automatique][6]

Grâce à ces nouvelles informations sur les machines virtuelles Azure, Security Center peut fournir des recommandations supplémentaires sur l’état de mise à jour du système, les configurations de la sécurité du système d’exploitation et la protection des points de terminaison, et générer des alertes de sécurité supplémentaires.

  ![Recommandations][8]

## <a name="clean-up-resources"></a>Supprimer des ressources
D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous envisagez de suivre les didacticiels et guides de démarrage rapide suivants, conservez le niveau Standard et gardez l’approvisionnement automatique activé. Dans le cas contraire, ou si vous voulez revenir au niveau Gratuit :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez la stratégie ou l’abonnement pour lequel vous voulez revenir au niveau Gratuit. La fenêtre **Stratégie de sécurité** s’ouvre.
3. Dans **COMPOSANTS DE LA STRATÉGIE**, sélectionnez **Niveau tarifaire**.
4. Sélectionnez **Gratuit** pour modifier l’abonnement et passer du niveau Standard au niveau Gratuit.
5. Sélectionnez **Enregistrer**.

Si vous voulez désactiver l’approvisionnement automatique :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique.
3. Dans **Stratégie de sécurité : collecte de données**, sélectionnez **Désactivé** sous **Intégration** pour désactiver l’approvisionnement automatique.
4. Sélectionnez **Enregistrer**.

>[!NOTE]
> La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent a été approvisionné. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.
>

## <a name="next-steps"></a>étapes suivantes
Dans ce guide de démarrage rapide, vous avez effectué une mise à niveau vers le niveau Standard et approvisionné Microsoft Monitoring Agent pour profiter de fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble de vos charges de travail cloud hybrides. Pour en savoir plus sur Security Center, enchaînez avec le guide de démarrage rapide sur l’intégration d’ordinateurs Windows en local et dans d’autres clouds.

> [!div class="nextstepaction"]
> [Démarrage rapide : Intégrer des ordinateurs Windows à Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
