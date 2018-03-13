---
title: "Contrôles d’application adaptative dans Azure Security Center | Microsoft Docs"
description: "Ce document vous aide à utiliser les contrôles d’application adaptative dans Azure Security Center afin de mettre dans une liste verte des applications s’exécutant sur les machines virtuelles Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: yurid
ms.openlocfilehash: ee15b602dc90b0e777b7ccd29572b9d560ee719b
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Contrôles d’application adaptative dans Azure Security Center (version préliminaire)
Découvrez comment configurer le contrôle d’application dans Azure Security Center à l’aide de cette procédure pas à pas.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Que sont les contrôles d’application adaptative dans Azure Security Center ?
Les contrôles d’application adaptative vous aident à contrôler les applications et à décider lesquelles peuvent s’exécuter sur vos machines virtuelles situées dans Azure. Cela vous permet notamment de renforcer vos machines virtuelles contre les programmes malveillants. Security Center utilise le machine learning pour analyser les processus en cours d’exécution sur la machine virtuelle et exploite ces informations pour vous aider à appliquer les règles de mise en liste verte. Cette fonctionnalité simplifie considérablement le processus de configuration et de gestion des listes vertes d’applications, ce qui vous permet de :

- Bloquer ou vous alerter si une application malveillante tente de s’exécuter, y compris celles pouvant être omises par les solutions de logiciels anti-programme malveillant
- Respecter la stratégie de sécurité de votre organisation qui autorise uniquement l’utilisation de certains logiciels licenciés.
- Empêcher l’utilisation de logiciels indésirables sur votre environnement.
- Empêcher l’exécution des applications obsolètes et non prises en charge.
- Bloquer des outils logiciels spécifiques qui ne sont pas autorisés dans votre organisation.
- Autoriser le service informatique à contrôler l’accès aux données sensibles en utilisant l’application.

## <a name="how-to-enable-adaptive-application-controls"></a>Comment activer les contrôles d’application adaptative ?
Les contrôles d’application adaptative vous aident à définir un ensemble d’applications autorisées à s’exécuter sur des groupes de ressources configurés. Cette fonctionnalité est disponible uniquement pour les machines Windows (toutes les versions, Classic ou Azure Resource Manager). Vous pouvez suivre les étapes ci-dessous pour configurer la mise en liste verte des applications dans Security Center :

1. Ouvrez le tableau de bord **Security Center**.
2. Dans le volet de gauche, sélectionnez **Adaptive application controls** (Contrôles d’application adaptatifs) situé sous **Advanced cloud defense** (Défense de cloud avancée).

    ![Défense](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

La page **Adaptive application controls** (Contrôles d’application adaptatifs) apparaît.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

La section **Groupes de ressources** contient trois onglets :

* **Configuré** : liste des groupes de ressources contenant les machines virtuelles qui ont été configurées avec le contrôle d’application.
* **Recommandé** : liste des groupes de ressources pour lesquels le contrôle d’application adaptative est recommandé. Security Center utilise le machine learning pour identifier les machines virtuelles potentiellement concernées par le contrôle d’application en fonction de leur capacité à exécuter les mêmes applications en continu.
* **Aucune recommandation** : liste des groupes de ressources contenant des machines virtuelles sans recommandations de contrôle d’application. Par exemple, les machines virtuelles dont les applications sont toujours en cours de modification et qui n’ont pas atteint un état stable.

### <a name="configure-a-new-application-control-policy"></a>Configurer une nouvelle stratégie de contrôle des applications
1. Cliquez sur l’onglet **Recommandé** pour obtenir une liste des groupes de ressources disposant de recommandations de contrôle d’application :

  ![Recommandé](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  Cette liste comprend les éléments suivants :

  - **NOM** : le nom de l’abonnement et des groupes de ressources
  - **MACHINES VIRTUELLES** : le nombre de machines virtuelles dans le groupe de ressources
  - **ÉTAT** : l’état des recommandations qui seront ouvertes dans la plupart des cas
  - **GRAVITÉ** : le niveau de gravité des recommandations

2. Sélectionnez un groupe de ressources pour ouvrir l’option **Créer des règles de contrôle d’applications**.

  ![Règles de contrôle d’application](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Dans **Sélectionner les machines virtuelles**, examinez la liste des machines virtuelles recommandées et décochez celles pour lesquelles vous ne souhaitez pas appliquer le contrôle d’application. Dans **Sélectionner les processus des règles de mise en liste verte**, examinez la liste des applications recommandées et décochez celles que vous ne souhaitez pas appliquer. Cette liste comprend les éléments suivants :

  - **NOM** : le chemin d’accès complet de l’application
  - **PROCESSUS** : le nombre d’applications se trouvant dans chaque chemin d’accès
  - **COMMUN** : la valeur « Yes » indique que ces processus ont été exécutés sur la plupart des machines virtuelles de ce groupe de ressources.
  - **EXPLOITABLE** : une icône d’avertissement indique si les applications peuvent être utilisées par une personne malveillante pour ignorer la liste verte d’application. Nous vous recommandons de vérifier ces applications avant de les valider.
  - **UTILISATEURS** : les utilisateurs autorisés à exécuter l’application

4. Une fois que vous avez terminé vos sélections, sélectionnez **Créer**.

Par défaut, Security Center autorise toujours le contrôle d’application dans le mode *Audit*. Après avoir confirmé que la liste verte n’a aucun effet négatif sur votre charge de travail, vous pouvez passer en mode *Appliquer*.

Security Center se base sur l’équivalent de deux semaines de données au minimum pour établir une ligne de base et remplir les recommandations uniques par groupe de machines virtuelles. Les nouveaux clients du niveau Standard de Security Center peuvent s’attendre à un comportement dans lequel leurs groupes de machines virtuelles apparaîtront d’abord sous l’onglet *Aucune recommandation*.

> [!NOTE]
> Afin d’assurer les meilleures pratiques de sécurité, Security Center essaie toujours de créer une règle d’éditeur pour les applications à mettre en liste verte. Si une application ne possède pas d’informations d’éditeur (l’application n’est pas signée), une règle de chemin d’accès est créée pour le chemin d’accès complet d’un fichier EXE spécifique.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Modifier et surveiller un groupe configuré avec le contrôle d’application

1. Pour modifier et surveiller un groupe configuré avec le contrôle d’application, revenez à la page **Adaptive application controls** (Contrôles d’application adaptatifs) et sélectionnez **CONFIGURÉ** sous **Groupes de ressources** :

  ![Groupes de ressources](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Cette liste comprend les éléments suivants :

  - **NOM** : le nom de l’abonnement et des groupes de ressources
  - **MACHINES VIRTUELLES** : le nombre de machines virtuelles dans le groupe de ressources
  - **MODE** : le mode Audit enregistre les tentatives d’exécution des applications hors liste verte et le mode Bloquer interdit l’exécution d’applications hors liste verte
  - **PROBLÈMES** : toutes les violations en cours

2. Sélectionnez un groupe de ressources pour apporter des modifications à la page **Modifier la stratégie de contrôle d’application**.

  ![Protection](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Dans **Mode de Protection**, vous pouvez sélectionner une des options suivantes :

  - **Audit** : dans ce mode, la solution de contrôle d’application n’applique pas les règles et effectue uniquement les audits de l’activité des machines virtuelles protégées. Ce mode est recommandé pour les scénarios dont vous souhaitez tout d’abord observer le comportement global avant de bloquer l’exécution d’une application dans la machine virtuelle cible.
  - **Appliquer** : dans ce mode, la solution de contrôle d’application applique les règles, puis s’assure que les applications n’ayant pas d’autorisation soient bloquées.

  Comme nous l’avons indiqué précédemment, une nouvelle stratégie de contrôle d’application est toujours configurée par défaut dans le mode *Audit*. Dans **Extension de stratégie**, vous pouvez ajouter les chemins d’accès d’application que vous souhaitez mettre en liste verte. Une fois que vous ajoutez ces chemins d’accès, Security Center crée les règles appropriées pour ces applications en plus de celles déjà en place.

  Dans la section **Problèmes récents**, toutes les violations actuelles sont répertoriées.

  ![Problèmes](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  Cette liste comprend les éléments suivants :
  - **PROBLÈMES** : toutes les violations enregistrées, y compris les éléments suivants :

      - **ViolationsBlocked** : lorsque le mode Appliquer est activé pour la solution et une application hors liste verte tente de s’exécuter.
      - **ViolationsAudited** : lorsque le mode Audit est activé pour la solution et une application hors liste verte s’exécute.
      - **RulesViolatedManually** : lorsqu’un utilisateur a essayé de configurer manuellement des règles sur les machines virtuelles sans utiliser le portail de gestion ASC.

 - **NO. DE MACHINES VIRTUELLES** : le nombre de machines virtuelles avec ce type de problème.

  Si vous cliquez sur chacune de ces lignes, vous êtes redirigé vers la page du [journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) où vous pouvez consulter des informations sur toutes les machines virtuelles avec ce type de violation. En cliquant sur les trois points à la fin de chaque ligne, vous pouvez supprimer une entrée spécifique. La section **Machines virtuelles configurées** répertorie les machines virtuelles pour lesquelles ces règles s’appliquent.

  ![Machines virtuelles configurées](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  Sous **Règles de mise en liste verte de l’éditeur**, la liste contient :

  - **RÈGLE** : les applications pour lesquelles une règle d’éditeur a été créée en fonction des informations de certificat trouvées pour chaque application
  - **UTILISATEURS** : nombre d’utilisateurs autorisés à s’exécuter à chaque application

  Consultez [Présentation des règles d’éditeur dans Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) pour plus d’informations.

  ![Règles de mise en liste verte](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  En cliquant sur les trois points à la fin de chaque ligne, vous pouvez supprimer une règle spécifique ou modifier les utilisateurs autorisés.

  La section **Règles de mise en liste verte du chemin d’accès** répertorie la totalité du chemin d’accès d’application (y compris l’exécutable) des applications n’ayant pas été signées avec un certificat numérique mais qui sont toujours dans les règles de mise en liste verte.

  > [!NOTE]
  > Par défaut, afin d’assurer les meilleures pratiques de sécurité, Security Center essaie toujours de créer une règle d’éditeur pour les fichiers EXE à mettre en liste verte. Si un fichier EXE ne possède pas d’informations d’éditeur (qui n’est pas signé), une règle de chemin d’accès est créée pour le chemin d’accès complet du fichier EXE spécifique.

  ![Règles de mise en liste verte du chemin d’accès](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  La liste contient :
  - **NOM** : le correctif complet de l’exécutable
  - **UTILISATEURS** : nombre d’utilisateurs autorisés à s’exécuter à chaque application

  En cliquant sur les trois points à la fin de chaque ligne, vous pouvez supprimer une règle spécifique ou modifier les utilisateurs autorisés.

4. Après avoir apporté des modifications sur la page **Adaptive application controls** (Contrôles d’application adaptatifs), cliquez sur le bouton **Enregistrer**. Si vous décidez de ne pas appliquer les modifications, cliquez sur **Ignorer**.

### <a name="not-recommended-list"></a>Liste non recommandée

Security Center ne recommande la mise en liste verte d’application que pour les machines virtuelles exécutant un ensemble stable d’applications. Les recommandations ne sont pas créées si les applications sur les machines virtuelles associées sont en cours de modification.

![Recommandation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

La liste contient :
- **NOM** : le nom de l’abonnement et des groupes de ressources
- **MACHINES VIRTUELLES** : le nombre de machines virtuelles dans le groupe de ressources

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découvert comment utiliser les contrôles d’application adaptative dans Azure Security Center afin d’inclure dans une liste verte des applications s’exécutant sur les machines virtuelles Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
