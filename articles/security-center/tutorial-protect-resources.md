---
title: "Didacticiel sur Azure Security Center - Protéger vos ressources avec Azure Security Center | Microsoft Docs"
description: "Ce didacticiel décrit comment configurer une stratégie d’accès juste-à-temps aux machines virtuelles et une stratégie de contrôle d’applications."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: cda204f5b54aef239cc0795b62c6fa484a27ebb5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Didacticiel : Protéger vos ressources avec Azure Security Center
Security Center limite votre exposition aux menaces en utilisant des contrôles d’accès et d’applications pour bloquer les activités malveillantes. L’accès juste-à-temps aux machines virtuelles réduit votre exposition aux attaques en vous permettant de refuser l’accès persistant aux machines virtuelles. À la place, vous fournissez un accès contrôlé et audité aux machines virtuelles uniquement en cas de besoin. Les contrôles d’applications adaptatifs permettent de renforcer la protection contre les logiciels malveillants en contrôlant les applications qui peuvent s’exécuter sur les machines virtuelles. Security Center utilise le machine learning pour analyser les processus en cours d’exécution sur la machine virtuelle et exploite ces informations pour vous aider à appliquer les règles de mise en liste verte.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer une stratégie d’accès juste-à-temps aux machines virtuelles
> * Configurer une stratégie de contrôle d’applications

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Pour parcourir les fonctionnalités traitées dans ce didacticiel, vous devez avoir accès au niveau tarifaire Standard de Security Center. Vous pouvez essayer Security Center Standard sans frais pendant 60 jours. Le démarrage rapide [Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) vous guide dans la mise à niveau vers le plan Standard.

## <a name="manage-vm-access"></a>Gérer l’accès aux machines virtuelles
L’accès juste-à-temps aux machines virtuelles peut être utilisé pour verrouiller le trafic entrant vers vos machines virtuelles Azure, ce qui réduit l’exposition aux attaques et facilite la connexion aux machines virtuelles en cas de besoin.

L’accès juste-à-temps aux machines virtuelles est en préversion.

Les ports de gestion n’ont pas besoin d’être toujours ouverts. Ils doivent uniquement être ouverts lorsque vous êtes connecté à la machine virtuelle, par exemple pour effectuer des tâches de maintenance ou de gestion. Quand la fonctionnalité juste-à-temps est activée, Security Center utilise des règles de groupe de sécurité réseau qui limitent l’accès aux ports de gestion pour qu’ils ne soient pas la cible d’attaquants.

1. Dans le menu principal de Security Center, sélectionnez **Accès juste-à-temps aux machines virtuelles** sous **DÉFENSE DE CLOUD AVANCÉE**.

  ![Accès juste-à-temps aux machines virtuelles][1]

  **Accès Juste à temps à la machine virtuelle** fournit des informations sur l’état de vos machines virtuelles :

  - **Configuré** : machines virtuelles configurées pour prendre en charge l’accès Juste à temps à la machine virtuelle.
  - **Recommandé** : machines virtuelles qui peuvent prendre en charge l’accès Juste à temps à la machine virtuelle, mais n’ont pas été configurées dans cette optique.
  - **Aucune recommandation** : voici les raisons pour lesquelles une machine virtuelle peut ne pas être recommandée :

    - Groupe de sécurité réseau manquant : la solution Juste à temps nécessite la présence d’un groupe de sécurité réseau.
    - Machine virtuelle classique : l’accès Juste à temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager.
    - Autre : catégorie d’une machine virtuelle si la solution Juste à temps est désactivée dans la stratégie de sécurité de l’abonnement ou du groupe de ressources, ou si la machine virtuelle ne dispose pas d’une adresse IP publique ni d’un groupe de sécurité réseau.

2. Sélectionnez une machine virtuelle recommandée et cliquez sur **Activer juste-à-temps sur 1 machine virtuelle** pour configurer une stratégie juste-à-temps pour cette machine virtuelle :

  Vous pouvez enregistrer les ports par défaut que vous recommande Security Center, ou vous pouvez ajouter et configurer un nouveau port sur lequel activer la solution juste-à-temps. Dans ce didacticiel, nous ajoutons un port en sélectionnant **Ajouter**.

  ![Ajouter la configuration du port][2]

3. Sous **Ajouter la configuration du port**, vous identifiez :

  - Le port
  - Le type de protocole
  - Les adresses IP sources autorisées : plages d’adresses IP autorisées à accéder par le biais d’une demande approuvée
  - La durée maximale de la demande : fenêtre de temps maximale pendant laquelle un port spécifique peut être ouvert

4. Sélectionnez **OK** pour enregistrer.

## <a name="harden-vms-against-malware"></a>Renforcer la protection des machines virtuelles contre les logiciels malveillants
Les contrôles d’applications adaptatifs vous aident à définir un ensemble d’applications autorisées à s’exécuter sur des groupes de ressources configurés, ce qui permet, entre autres, de renforcer la protection de vos machines virtuelles contre les logiciels malveillants. Security Center utilise le machine learning pour analyser les processus en cours d’exécution sur la machine virtuelle et exploite ces informations pour vous aider à appliquer les règles de mise en liste verte.

Les contrôles d’applications adaptatifs sont en préversion. Cette fonctionnalité est uniquement disponible pour les ordinateurs Windows.

1. Revenez au menu principal de Security Center. Sous **DÉFENSE DE CLOUD AVANCÉ**, sélectionnez **Contrôles d’applications adaptatifs**.

   ![Contrôles d’application adaptative][3]

  La section **Groupes de ressources** contient trois onglets :

  - **Configuré** : Liste des groupes de ressources contenant les machines virtuelles qui ont été configurées avec le contrôle d’applications.
  - **Recommandé** : Liste des groupes de ressources pour lesquels le contrôle d’applications est recommandé.
  - **Aucune recommandation** : Liste des groupes de ressources contenant des machines virtuelles sans recommandation de contrôle d’applications. Par exemple, les machines virtuelles dont les applications sont toujours en cours de modification et qui n’ont pas atteint un état stable.

2. Sélectionnez l’onglet **Recommandé** pour obtenir la liste des groupes de ressources avec des recommandations de contrôle d’applications.

  ![Recommandations de contrôle d’applications][4]

3. Sélectionnez un groupe de ressources pour ouvrir l’option **Créer des règles de contrôle d’applications**. Dans **Sélectionner les machines virtuelles**, examinez la liste des machines virtuelles recommandées et décochez celles pour lesquelles vous ne souhaitez pas appliquer le contrôle d’application. Dans **Sélectionner les processus des règles de mise en liste verte**, examinez la liste des applications recommandées et décochez celles que vous ne souhaitez pas appliquer. Cette liste comprend les éléments suivants :

  - **NOM** : Chemin complet de l’application
  - **PROCESSUS** : Nombre d’applications se trouvant dans chaque chemin
  - **COMMUN** : La valeur « Oui » indique que ces processus ont été exécutés sur la plupart des machines virtuelles de ce groupe de ressources
  - **EXPLOITABLE** : Une icône d’avertissement indique si les applications peuvent être utilisées par un attaquant pour ignorer la liste verte d’applications. Nous vous recommandons de vérifier ces applications avant de les valider.

4. Une fois que vous avez terminé vos sélections, sélectionnez **Créer**.

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

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à limiter votre exposition aux menaces en effectuant les tâches suivantes :

> [!div class="checklist"]
> * Configuration d’une stratégie d’accès juste-à-temps aux machines virtuelles pour fournir un accès contrôlé et audité aux machines virtuelles uniquement en cas de besoin
> * Configuration d’une stratégie de contrôles d’applications adaptatifs pour contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles

Passez au didacticiel suivant pour en savoir plus sur la façon de répondre aux incidents de sécurité.

> [!div class="nextstepaction"]
> [Didacticiel : Répondre aux incidents de sécurité](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
