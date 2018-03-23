---
title: Azure Security Center et machines virtuelles Linux dans Azure | Documents Microsoft
description: Apprenez-en davantage sur la sécurité de votre machine virtuelle Linux Azure avec Azure Security Center.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6b791b2e1dbaffc90145c325dea7a85bd8abd98c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Surveiller la sécurité des machines virtuelles à l’aide d’Azure Security Center

Azure Security Center peut vous aider à acquérir une meilleure visibilité des pratiques de sécurité de vos ressources Azure. Azure Security Center assure une surveillance intégrée de la sécurité. Il peut détecter des menaces qui sans cela pourraient passer inaperçues. Ce didacticiel décrit Azure Security Center et comment effectuer les opérations suivantes :
 
> [!div class="checklist"]
> * Configurer la collecte de données
> * Définir des stratégies de sécurité
> * Afficher et résoudre des problèmes d’intégrité de configuration
> * Examiner les menaces détectées  

## <a name="security-center-overview"></a>Vue d’ensemble de Security Center

Security Center identifie des problèmes potentiels de configuration des machines virtuelles et des menaces de sécurité ciblées. Il peut s’agir de machines virtuelles dépourvues de groupes de sécurité réseau, de disques non chiffrés et d’attaques RDP (Remote Desktop Protocol) par force brute. Les informations s’affichent sur le tableau de bord de Security Center dans des graphiques aisément lisibles.

Pour accéder au tableau de bord de Security Center, dans le menu du portail Azure, sélectionnez **Security Center**. Le tableau de bord montre l’état d’intégrité de la sécurité de votre environnement Azure, différentes recommandations courantes, ainsi que l’état actuel des alertes de menace. Vous pouvez développer chaque graphique général pour afficher plus de détails.

![Tableau de bord de Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center va au-delà de la découverte de données et fournit des recommandations concernant les problèmes qu’il détecte. Par exemple, si une machine virtuelle a été déployée sans groupe de sécurité réseau associé, Security Center affiche une recommandation ainsi que des étapes de correction que vous pouvez suivre. Vous bénéficiez d’une correction automatisée sans quitter le contexte de Security Center.  

![Recommandations](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurer la collecte de données

Avant d’obtenir une visibilité des configurations de sécurité des machines virtuelles, vous devez configurer la collecte de données de Security Center. Ceci implique d’activer la collecte de données et de créer un compte de stockage Azure pour stocker les données collectées. 

1. Dans le tableau de bord de Security Center, cliquez sur **Stratégie de sécurité** puis sélectionnez votre abonnement. 
2. Pour **Collecte de données**, sélectionnez **Activée**.
3. Pour créer un compte de stockage, sélectionnez **Choisir un compte de stockage**. Ensuite, sélectionnez **OK**.
4. Dans le panneau **Stratégie de sécurité**, sélectionnez **Enregistrer**. 

L’agent de collecte de données de Security Center est alors installé sur toutes les machines virtuelles, puis la collecte de données commence. 

## <a name="set-up-a-security-policy"></a>Configurer une stratégie de sécurité

Les stratégies de sécurité permettent de définir les éléments pour lesquels Security Center collecte des données et formule des recommandations. Vous pouvez appliquer des stratégies de sécurité différentes à différents ensembles de ressources Azure. Bien que par défaut les ressources Azure soient évaluées pour tous les éléments de la stratégie, vous pouvez désactiver des éléments individuels de la stratégie pour toutes les ressources ou pour un groupe de ressources Azure. Pour obtenir des informations détaillées sur les stratégies de sécurité de Security Center, consultez [Définir des stratégies de sécurité dans Azure Security Center](../../security-center/security-center-policies.md). 

Pour configurer une stratégie de sécurité pour toutes les ressources Azure :

1. Dans le tableau de bord de Security Center, cliquez sur **Stratégie de sécurité** puis sélectionnez votre abonnement.
2. Sélectionnez **Stratégie de prévention**.
3. Activez ou désactivez les éléments de la stratégie que vous voulez appliquer à toutes les ressources Azure.
4. Quand vous avez fini de sélectionner vos paramètres, choisissez **OK**.
5. Dans le panneau **Stratégie de sécurité**, sélectionnez **Enregistrer**. 

Pour définir une stratégie pour un groupe de ressources spécifique :

1. Dans le tableau de bord de Security Center, sélectionnez **Stratégie de sécurité** puis choisissez un groupe de ressources.
2. Sélectionnez **Stratégie de prévention**.
3. Activez ou désactivez les éléments de la stratégie que vous voulez appliquer au groupe de ressources.
4. Sous **HÉRITAGE**, sélectionnez **Unique**.
5. Quand vous avez fini de sélectionner vos paramètres, choisissez **OK**.
6. Dans le panneau **Stratégie de sécurité**, sélectionnez **Enregistrer**.  

Dans cette page, vous pouvez également désactiver la collecte des données pour un groupe de ressources spécifique.

Dans l’exemple suivant, une stratégie unique a été créée pour un groupe de ressources nommé *myResoureGroup*. Dans cette stratégie, les recommandations en matière de chiffrement de disque et de pare-feu d’applications web sont désactivées.

![Stratégie unique](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Afficher l’état de configuration des machines virtuelles

Après que vous avez activé la collecte de données et défini une stratégie de sécurité, Security Center commence à fournir des alertes et des recommandations. À mesure que des machines virtuelles sont déployées, l’agent de collecte de données est installé. Security Center reçoit ensuite des données relatives aux nouvelles machines virtuelles. Pour des informations détaillées sur l’intégrité de la configuration des machines virtuelles, consultez [Protéger vos machines virtuelles dans Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

À mesure que des données sont collectées, les informations sur l’état d’intégrité des ressources de chaque machine virtuelle et des ressources Azure associées sont agrégées. Les informations sont affichées dans un graphique aisément lisible. 

Pour afficher l’intégrité des ressources :

1.  Dans le tableau de bord de Security Center, sous **Intégrité de la sécurité des ressources**, sélectionnez **Calcul**. 
2.  Dans le panneau **Calcul**, sélectionnez **Machines virtuelles**. Cette vue fournit un récapitulatif de l’état de la configuration de toutes vos machines virtuelles.

![Calculer l’état d’intégrité](./media/tutorial-azure-security/compute-health.png)

Pour voir toutes les recommandations relatives à une machine virtuelle, sélectionnez celle-ci. Les recommandations et la correction sont traitées plus en détail dans la section suivante de ce didacticiel.

## <a name="remediate-configuration-issues"></a>Corriger les problèmes de configuration

Quand Security Center commence à recevoir des données de configuration, des recommandations sont formulées en fonction de la stratégie de sécurité configurée. Par exemple, si une machine virtuelle a été configurée sans groupe de sécurité réseau associé, une recommandation préconise d’en créer un. 

Pour afficher la liste de toutes les recommandations : 

1. Dans le tableau de bord de Security Center, sélectionnez **Recommandations**.
2. Sélectionnez une recommandation spécifique. La liste de toutes les ressources auxquelles la recommandation s’applique s’affiche.
3. Pour appliquer une recommandation, sélectionnez une ressource spécifique. 
4. Suivez les instructions pour les étapes de correction. 

Dans de nombreux cas, Security Center propose des mesures que vous pouvez prendre pour suivre une recommandation sans quitter Security Center. Dans l’exemple suivant, Security Center détecte un groupe de sécurité réseau qui a une règle de trafic entrant non restreinte. Dans la page de recommandation, vous pouvez sélectionner le bouton **Modifier les règles de trafic entrant**. L’interface utilisateur nécessaire pour modifier la règle apparaît. 

![Recommandations](./media/tutorial-azure-security/remediation.png)

Les recommandations sont marquées comme étant résolues à mesure qu’elles sont appliquées. 

## <a name="view-detected-threats"></a>Consulter les menaces détectées

En plus des recommandations concernant la configuration des ressources, Security Center affiche des alertes de détection de menaces. La fonctionnalité d’alertes de sécurité agrège les données collectées à partir de chaque machine virtuelle, des journaux de réseau Azure et des solutions partenaires connectées pour détecter les menaces de sécurité au niveau des ressources Azure. Pour obtenir des informations détaillées sur les fonctionnalités de détection des menaces de Security Center, consultez [Fonctionnalités de détection d’Azure Security Center](../../security-center/security-center-detection-capabilities.md).

La fonctionnalité d’alertes de sécurité nécessite que le niveau tarifaire de Security Center soit porté de *Gratuit* à *Standard*. Une **version d’évaluation gratuite** de 30 jours est disponible quand vous passez à ce niveau tarifaire supérieur. 

Pour changer de niveau tarifaire :  

1. Dans le tableau de bord de Security Center, cliquez sur **Stratégie de sécurité** puis sélectionnez votre abonnement.
2. Sélectionnez **Niveau tarifaire**.
3. Sélectionnez le nouveau niveau puis choisissez **Sélectionner**.
4. Dans le panneau **Stratégie de sécurité**, sélectionnez **Enregistrer**. 

Après que vous avez changé le niveau tarifaire, le graphique des alertes de sécurité commence à se remplir à mesure que des menaces de sécurité sont détectées.

![Alertes de sécurité](./media/tutorial-azure-security/security-alerts.png)

Sélectionnez une alerte pour afficher des informations la concernant. Par exemple, vous pouvez voir une description de la menace, l’heure de la détection, toutes les tentatives de la menace et la correction recommandée. Dans l’exemple suivant, une attaque RDP par force brute a été détectée, avec 294 tentatives RDP qui ont échoué. Une solution recommandée est fournie.

![Attaque RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel vous a montré comment configurer Azure Security Center, puis examiner les machines virtuelles dans Azure Security Center. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer la collecte de données
> * Définir des stratégies de sécurité
> * Afficher et résoudre des problèmes d’intégrité de configuration
> * Examiner les menaces détectées

Passez au didacticiel suivant pour en savoir plus sur la création d’un pipeline d’intégration continue/de livraison continue avec Jenkins, GitHub et Docker.

> [!div class="nextstepaction"]
> [Créer une infrastructure d’intégration continue/de livraison continue avec Jenkins, GitHub et Docker](tutorial-jenkins-github-docker-cicd.md)

