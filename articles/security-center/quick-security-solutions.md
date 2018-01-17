---
title: "Démarrage rapide de Azure Security Center - Connexion des solutions de sécurité | Microsoft Docs"
description: "Démarrage rapide de Azure Security Center - Connexion des solutions de sécurité"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3263bb3d-befc-428c-9f80-53de65761697
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: yurid
ms.custom: mvc
ms.openlocfilehash: 2ea4dc75c6285379d7a7eb3e85d28c89ae520dc8
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-connect-security-solutions-to-security-center"></a>Démarrage rapide : Connexion des solutions de sécurité à Security Center

En plus de la collecte de données de sécurité depuis vos ordinateurs, vous pouvez intégrer des données de sécurité à partir d’un éventail de solutions de sécurité, y compris celles prenant en charge le format d’événement commun (CEF). Le format CEF est un format standard du secteur en plus des messages Syslog. Il est utilisé par de nombreux fournisseurs de sécurité pour permettre l’intégration des événements entre différentes plateformes.

Ce démarrage rapide vous montre comment :
- Connecter une solution de sécurité à Security Center à l’aide des journaux CEF
- Valider la connexion avec la solution de sécurité

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/free/).

Pour effectuer ce démarrage rapide, vous devez utiliser le niveau tarifaire Standard de Security Center. Vous pouvez essayer Security Center Standard sans frais pendant 60 jours. Le démarrage rapide [Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) vous guide dans la mise à niveau vers le plan Standard.

Vous avez également besoin d’une [machine Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux), avec le service Syslog déjà connecté au Security Center.

## <a name="connect-solution-using-cef"></a>Connecter une solution à l’aide du format CEF

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. **Security Center - Vue d’ensemble** s’ouvre.

    ![Sélectionnez Centre de sécurité](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. Dans le menu principal de Security Center, sélectionnez **Solutions de sécurité**.
4. Dans la page Solutions de sécurité, sous **Ajouter des sources de données (3)**, cliquez sur **Ajouter** sous **CEF (Common Event Format)**.

    ![Ajouter une source de données](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. Dans la page Journaux CFE, développez la deuxième étape, **Configurer le transfert Syslog pour envoyer les journaux nécessaires à l’agent sur le port UDP 25226**, suivez les instructions ci-dessous sur votre ordinateur Linux :

    ![Configurer les messages Syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. Développez la troisième étape, **Placer le fichier de configuration de l’agent sur l’ordinateur de l’agent** et suivez les instructions ci-dessous sur votre ordinateur Linux :

    ![Configuration de l’agent](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. Développez la quatrième étape, **Redémarrer le démon syslog et l’agent**et suivez les instructions ci-dessous dans votre ordinateur Linux :

    ![Redémarrer Syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


## <a name="validate-the-connection"></a>Valider la connexion

Avant de suivre les étapes suivantes, vous devrez attendre que Syslog démarre la création de rapports à Security Center. Cela peut prendre un certain temps, variable en fonction de la taille de l’environnement.

1.  Dans le volet gauche du tableau de bord de Security Center, cliquez sur **Rechercher**.
2.  Sélectionnez l’espace de travail connecté à Syslog (machine Linux).
3.  Saisissez *CommonSecurityLog* et cliquez sur le bouton **Rechercher**.

L’exemple suivant montre le résultat de ces étapes : ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Les autres démarrages rapides et didacticiels de cette collection reposent sur ce démarrage rapide. Si vous envisagez d’utiliser les didacticiels et démarrages rapides suivants, continuez d’exécuter le niveau Standard et gardez le provisionnement automatique activé. Dans le cas contraire ou si vous voulez revenir au niveau Gratuit :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement ou la stratégie que vous voulez pour revenir au niveau Gratuit. La **Stratégie de sécurité** s’ouvre.
3. Sous **COMPOSANTS DE LA STRATÉGIE**, sélectionnez **Niveau tarifaire**.
4. Sélectionnez **Gratuit** pour changer le niveau d’abonnement de Standard à Gratuit.
5. Sélectionnez **Enregistrer**.

Si vous voulez désactiver le provisionnement automatique :

1. Revenez au menu principal de Security Center et sélectionnez **Stratégie de sécurité**.
2. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique.
3. Sous **Stratégie de sécurité : collecte de données**, sélectionnez **Désactiver** sous **Intégration** pour désactiver le provisionnement automatique.
4. Sélectionnez **Enregistrer**.

>[!NOTE]
> La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent a été approvisionné. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.
>

## <a name="next-steps"></a>étapes suivantes
Dans ce démarrage rapide, vous avez appris à connecter une solution Syslog Linux à Security Center à l’aide du format CEF. En connectant vos journaux CEF à Security Center, vous pouvez tirer parti des règles d’alerte de recherche et personnalisée et des informations riches de Threat Intelligence. Pour en savoir plus sur l’utilisation de Security Center, passez au didacticiel sur la configuration d’une stratégie de sécurité et l’évaluation de la sécurité de vos ressources.

> [!div class="nextstepaction"]
> [Didacticiel : Définir et évaluer des stratégies de sécurité](./tutorial-security-policy.md)
