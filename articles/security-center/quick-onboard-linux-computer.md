---
title: "Démarrage rapide Azure Security Center - Intégrer vos ordinateurs Linux à Security Center | Microsoft Docs"
description: "Ce guide de démarrage rapide décrit comment intégrer vos ordinateurs Linux à Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: 365afd2199b1b8b2e70d882f6a729384edbdffbc
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Démarrage rapide : Intégrer des ordinateurs Linux à Azure Security Center
Après avoir intégré vos abonnements Azure, vous pouvez activer Security Center pour les ressources Linux s’exécutant en dehors d’Azure, par exemple, localement ou dans d’autres clouds, en provisionnant l’agent Linux.

Ce guide de démarrage rapide décrit comment installer l’agent Linux sur un ordinateur Linux.

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Vous devez utiliser le niveau tarifaire Standard de Security Center pour commencer ce guide de démarrage rapide. Consultez [Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) pour des instructions sur la mise à niveau. Vous pouvez essayer le niveau Standard de Security Center sans frais pendant 60 jours.

## <a name="add-new-linux-computer"></a>Ajouter un nouvel ordinateur Linux

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. **Security Center - Vue d’ensemble** s’ouvre.

 ![Vue d’ensemble de Security Center][2]

3. Dans le menu principal de Security Center, sélectionnez **Intégration à la sécurité avancée**.
4. Sélectionnez **Voulez-vous ajouter des ordinateurs non-Azure**.
   ![Intégrer à la sécurité avancée][3]

5. Sous **Ajouter de nouveaux ordinateurs non-Azure**, une liste de vos espaces de travail Log Analytics est affichée. La liste inclut, le cas échéant, l’espace de travail par défaut créé pour vous par Security Center à l’activation du provisionnement automatique. Sélectionnez cet espace de travail ou un autre espace de travail à utiliser.

    ![Ajouter un ordinateur autre qu’Azure][4]

6.  Dans la page **Agent direct**, sous **TÉLÉCHARGER ET INTÉGRER L’AGENT POUR LINUX**, sélectionnez le bouton **copier** pour copier la commande *wget*.

7.  Ouvrez le bloc-notes et collez cette commande. Enregistrez ce fichier dans un emplacement accessible à partir de votre ordinateur Linux.

## <a name="install-the-agent"></a>Installer l’agent

1.  Sur votre ordinateur Linux, ouvrez le fichier précédemment enregistré. Sélectionnez tout le contenu, copiez-le, ouvrez une console de terminal et collez la commande.
2.  Une fois l’installation terminée, vous pouvez vérifier que *omsagent* est installé en exécutant la commande *pgrep*. La commande retourne le PID (ID de processus) *omsagent* comme indiqué ci-dessous :

  ![Installer l’agent][5]

Les journaux de l’agent Security Center pour Linux sont disponibles à l’emplacement : */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Journaux de l’agent][6]

Au bout d’un certain temps, jusqu'à 30 minutes, le nouvel ordinateur Linux s’affiche dans Security Center.

Vous pouvez maintenant surveiller vos machines virtuelles Azure et des ordinateurs non-Azure au même endroit. Sous **Calculer**, vous avez une vue d’ensemble de tous les ordinateurs et machines virtuelles, ainsi que des recommandations. Chaque colonne représente un ensemble de recommandations. La couleur représente l’état de sécurité actuel de l’ordinateur ou de la machine virtuelle pour cette recommandation. Security Center met également en évidence les éventuelles détections d’alertes de sécurité pour ces ordinateurs.

  ![Panneau Calculer][7] Deux types d’icônes sont représentés sur le panneau **Calculer** :

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Ordinateur non-Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Microsoft Azure

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous pouvez supprimer l’agent de l’ordinateur Linux une fois que vous n’en avez plus besoin.

Pour supprimer l’agent :

1. Télécharger le [script universel](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) de l’agent Linux sur l’ordinateur.
2. Exécutez le fichier bundle .sh avec l’argument *--purge* sur l’ordinateur, ce qui supprime complètement l’agent et sa configuration.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez provisionné l’agent sur un ordinateur Linux. Pour en savoir plus sur l’utilisation de Security Center, passez au didacticiel sur la configuration d’une stratégie de sécurité et l’évaluation de la sécurité de vos ressources.

> [!div class="nextstepaction"]
> [Didacticiel : Définir et évaluer des stratégies de sécurité](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
