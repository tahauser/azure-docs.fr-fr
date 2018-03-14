---
title: "Démarrage rapide Azure Security Center - Intégrer des ordinateurs Windows à Security Center | Microsoft Docs"
description: "Ce guide de démarrage rapide explique comment approvisionner Microsoft Monitoring Agent sur un ordinateur Windows."
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
ms.openlocfilehash: 8d9b0fcc8b72f947cbc64c6ac9a428ac29f8dfd2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Démarrage rapide : Intégrer des ordinateurs Windows à Azure Security Center
Après avoir intégré vos abonnements Azure, vous pouvez activer Security Center pour des ressources qui s’exécutent en dehors d’Azure, par exemple, en local ou dans d’autres clouds, en approvisionnant Microsoft Monitoring Agent.

Ce guide de démarrage rapide explique comment installer Microsoft Monitoring Agent sur un ordinateur Windows.

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Vous devez utiliser le niveau tarifaire Standard de Security Center pour commencer ce guide de démarrage rapide. Vous trouverez des instructions sur la mise à niveau sur la page [Intégrer un abonnement Azure à Security Center Standard](security-center-get-started.md). Vous pouvez essayer le niveau Standard de Security Center gratuitement pendant 60 jours.

## <a name="add-new-windows-computer"></a>Ajouter un nouvel ordinateur Windows

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

 ![Vue d’ensemble de Security Center][2]

3. Dans le menu principal de Security Center, sélectionnez **Intégration à la sécurité avancée**.
4. Sélectionnez **Voulez-vous ajouter des ordinateurs extérieurs à Azure**.

   ![Intégrer à la sécurité avancée][3]

5. Dans **Ajouter de nouveaux ordinateurs extérieurs à Azure** apparaît la liste de vos espaces de travail Log Analytics. Elle comprend, le cas échéant, l’espace de travail par défaut créé pour vous par Security Center à l’activation de l’approvisionnement automatique. Sélectionnez cet espace de travail ou un autre espace de travail à utiliser.

    ![Ajouter un ordinateur autre qu’Azure][4]

  Le panneau **Agent direct** s’ouvre et affiche un lien permettant de télécharger un agent Windows et des clés pour votre ID d’espace de travail, qui serviront à la configuration de l’agent.

6.  Cliquez sur le lien **Télécharger l’Agent Windows** correspondant au type de processeur de votre ordinateur pour télécharger le fichier d’installation.

7.  À droite du champ **ID de l’espace de travail**, sélectionnez l’icône de copie et collez l’ID dans le Bloc-notes.

8.  À droite du champ **Clé primaire**, sélectionnez l’icône de copie et collez la clé dans le Bloc-notes.

## <a name="install-the-agent"></a>Installer l’agent
Vous devez maintenant installer le fichier téléchargé sur l’ordinateur cible.

1. Copiez le fichier sur l’ordinateur cible et exécutez le programme d’installation.
2. Sur la page d’**accueil**, sélectionnez **Suivant**.
3. Sur la page **Termes du contrat de licence**, lisez la licence, puis sélectionnez **J’accepte**.
4. Sur la page **Dossier de destination**, modifiez ou conservez le dossier d’installation par défaut, puis sélectionnez **Suivant**.
5. Sur la page **Options d’installation de l’agent**, choisissez de connecter l’agent à Azure Log Analytics (OMS), puis sélectionnez **Suivant**.
6. Sur la page **Azure Log Analytics**, collez **l’ID de l’espace de travail** et la **Clé de l’espace de travail (clé primaire)** que vous avez copiés dans le Bloc-notes au cours de la procédure précédente.
7. Si l’ordinateur doit communiquer avec un espace de travail Log Analytics dans le cloud Azure Government, sélectionnez **Azure - Gouvernement des États-Unis** dans la liste déroulante **Azure Cloud**.  Si l’ordinateur a besoin de communiquer avec le service Log Analytics par le biais d’un serveur proxy, sélectionnez **Avancé**, puis indiquez l’URL et le numéro de port du serveur proxy.
8. Sélectionnez **Suivant** après avoir indiqué les paramètres de configuration nécessaires.

  ![Installer l’agent][5]

9. Sur la page **Prêt pour l’installation**, vérifiez vos choix, puis sélectionnez **Installer**.
10. Sur la page **Configuration effectuée**, sélectionnez **Terminer**.

Lorsque vous avez terminé, **Microsoft Monitoring Agent** apparaît dans le **Panneau de configuration**. Vous pouvez vérifier votre configuration et vous assurer que l’agent est connecté.

Pour plus d’informations sur l’installation et la configuration de l’agent, consultez la page [Connecter des ordinateurs Windows](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup-wizard).

Vous pouvez maintenant effectuer le monitoring de vos machines virtuelles Azure et de vos ordinateurs extérieurs à Azure en un seul et même endroit. La fenêtre **Calcul** affiche une vue d’ensemble de toutes les machines virtuelles et de tous les ordinateurs, ainsi que des recommandations. Chaque colonne représente un ensemble de recommandations. La couleur correspond à l’état de sécurité actuel de l’ordinateur ou de la machine virtuelle pour une recommandation donnée. Security Center met également en évidence les éventuelles détections d’alertes de sécurité pour ces ordinateurs.

  ![Panneau Calcul][6]

Deux types d’icônes sont représentés sur le panneau **Calcul** :

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Ordinateur extérieur à Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Microsoft Azure

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous pourrez supprimer l’agent de l’ordinateur Windows lorsque vous n’en aurez plus besoin.

Pour supprimer l’agent :

1. Ouvrez le **Panneau de configuration**.
2. Ouvrez **Programmes et fonctionnalités**.
3. Dans **Programmes et fonctionnalités**, sélectionnez **Microsoft Monitoring Agent**, puis cliquez sur **Désinstaller**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez approvisionné Microsoft Monitoring Agent sur un ordinateur Windows. Pour en savoir plus sur Security Center, enchaînez avec le didacticiel sur la configuration d’une stratégie de sécurité et l’évaluation de la sécurité des ressources.

> [!div class="nextstepaction"]
> [Didacticiel : Définir et évaluer des stratégies de sécurité](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
