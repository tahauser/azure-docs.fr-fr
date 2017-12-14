---
title: FAQ sur la migration de plateforme Security Center | Microsoft Docs
description: Ce FAQ concerne la migration de plateforme Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: terrylan
ms.openlocfilehash: 6ccf104ea09dc1fbce1dd34a06168205d6f5fac8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="security-center-platform-migration-faq"></a>FAQ sur la migration de plateforme Security Center
Au début du mois de juin 2017, Azure Security Center a commencé à utiliser Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Migration de plateforme Azure Security Center](security-center-platform-migration.md). Ce FAQ concerne la migration de plateforme.

## <a name="data-collection-agents-and-workspaces"></a>Collecte de données, agents et espaces de travail

### <a name="how-is-data-collected"></a>Comment les données sont-elles collectées ?
Security Center utilise Microsoft Monitoring Agent pour collecter des données de sécurité sur vos machines virtuelles. Les données de sécurité incluent des informations sur :

- les configurations de sécurité (utilisées pour identifier les vulnérabilités)
- les événements de sécurité (utilisés pour détecter les menaces)

Les données collectées par l’agent sont stockées soit dans un espace de travail Log Analytics existant connecté à la machine virtuelle, soit dans un espace de travail créé par Security Center. Lorsque Security Center crée un nouvel espace de travail, la géolocalisation de la machine virtuelle est prise en compte.

> [!NOTE]
> Microsoft Monitoring Agent est l’agent utilisé par Operations Management Suite (OMS), le service Log Analytics et System Center Operations Management (SCOM).
>
>

Quand le provisionnement automatique (anciennement nommé « collecte de journaux ») est activé ou que vous migrez vos abonnements, Security Center vérifie si Microsoft Monitoring Agent est déjà installé en tant qu’extension Azure sur chacune de vos machines virtuelles. Si Microsoft Monitoring Agent n’est pas installé, Security Center effectue par défaut les opérations suivantes :

- Il installe l’extension Microsoft Monitoring Agent sur la machine virtuelle.

   - Si un espace de travail créé par Security Center existe déjà dans la même zone géographique que la machine virtuelle, l’agent est connecté à cet espace de travail.
   - S’il n’existe pas d’espace de travail, Security Center crée un groupe de ressources et un espace de travail par défaut dans cette zone géographique, et connecte l’agent à cet espace de travail. La convention d’affectation de noms pour l’espace de travail et le groupe de ressources est la suivante :

       Espace de travail : DefaultWorkspace-[ID d’abonnement]-[zone géographique]

       Groupe de ressources : DefaultResouceGroup-[zone géographique]

- Il active une solution Security Center sur l’espace de travail par niveau tarifaire associé de la machine virtuelle dans Security Center. Pour plus d’informations sur la tarification, consultez la page de [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Pour les abonnements migrés uniquement, Security Center supprime également l’Agent de surveillance Azure précédent.

> [!NOTE]
> Vous pouvez remplacer l’installation automatique de Microsoft Monitoring Agent et utiliser votre propre espace de travail.  Consultez les sections indiquant [comment empêcher l’installation automatique de l’agent et la création de l’espace de travail](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) et [comment utiliser votre espace de travail existant](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

L’emplacement de l’espace de travail est basé sur l’emplacement de la machine virtuelle. Pour plus d’informations, consultez [Sécurité des données](security-center-data-security.md). Si un abonnement contient des machines virtuelles se trouvant dans plusieurs zones géographiques, Security Center crée plusieurs espaces de travail. Plusieurs espaces de travail sont créés pour tenir à jour les règles de confidentialité des données.

> [!NOTE]
> Avant la migration de plateforme, Security Center collecte des données de sécurité sur vos machines virtuelles à l’aide d’Azure Monitoring Agent et stocke des données dans le compte de stockage. Après la migration de la plateforme, Security Center utilise Microsoft Monitoring Agent et l’espace de travail pour collecter et stocker les mêmes données. Le compte de stockage peut être supprimé après la migration.  Security Center supprime également les Agents de surveillance Azure précédemment installés après la migration de la plateforme.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Est-ce que je suis facturé pour Log Analytics ou OMS lorsqu’ils sont installés sur des espaces de travail créés par Security Center ?
Non. Les espaces de travail créés par Security Center, bien qu’ils soient configurés pour une facturation OMS par nœud, n’entraînent pas de frais OMS. La facturation Security Center est toujours basée sur la stratégie de sécurité Security Center et les solutions installées sur l’espace de travail :

- **Niveau Gratuit** : Security Center active la solution « SecurityCenterFree » sur l’espace de travail par défaut. Vous n’êtes pas facturé pour le niveau gratuit.
- **Niveau Standard** : Security Center active la solution « Security » sur l’espace de travail par défaut.

Pour plus d’informations sur la tarification, consultez la page de [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/). La page de tarification traite les modifications apportées à la facturation au prorata et au stockage des données de sécurité à partir de juin 2017.

> [!NOTE]
> Le niveau tarifaire OMS des espaces de travail créés par Security Center n’affecte pas la facturation Security Center.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Qu’est-ce qui rend une machine virtuelle apte pour le provisionnement automatique de l’installation de Microsoft Monitoring Agent ?
Les machines virtuelles Windows ou Linux IaaS sont retenues dans les cas suivants :

- L’extension Microsoft Monitoring Agent n’est pas actuellement installée sur la machine virtuelle.
- La machine virtuelle est en cours d’exécution.
- L’agent de machine virtuelle Windows ou Linux est installé.
- La machine virtuelle n’est pas utilisée comme pare-feu d’applications web ou comme pare-feu de nouvelle génération.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Puis-je supprimer les espaces de travail par défaut créés par Security Center ?
**Il n’est pas recommandé de supprimer l’espace de travail par défaut.** Security Center utilise les espaces de travail par défaut pour stocker les données de sécurité de vos machines virtuelles.  Si vous supprimez un espace de travail, Security Center n’est pas en mesure pas collecter ces données, et certaines recommandations de sécurité et alertes ne sont pas disponibles.

Pour effectuer une récupération, supprimez Microsoft Monitoring Agent sur les machines virtuelles connectées à l’espace de travail supprimé. Security Center réinstalle l’agent et crée des nouveaux espaces de travail par défaut.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Comment puis-je utiliser mon espace de travail Log Analytics existant ?

Vous pouvez sélectionner un espace de travail Log Analytics existant pour stocker les données collectées par Security Center. Pour utiliser votre espace de travail Log Analytics existant :

- L’espace de travail doit être associé à votre abonnement Azure sélectionné.
- Au minimum, vous devez avoir des autorisations de lecture pour accéder à l’espace de travail.

Pour sélectionner un espace de travail Log Analytics existant :

1. Dans **Stratégie de sécurité : collecte de données**, sélectionnez **Use another workspace** (Utiliser un autre espace de travail).

   ![Utiliser un autre espace de travail][5]

2. Dans le menu déroulant, sélectionnez un espace de travail pour stocker les données collectées.

   > [!NOTE]
   > Dans le menu déroulant, seuls les espaces de travail auxquels vous avez accès et se trouvant dans votre abonnement Azure sont affichés.
   >
   >

3. Sélectionnez **Enregistrer**.
4. Après avoir sélectionné **Enregistrer**, vous serez invité à reconfigurer les machines virtuelles surveillées.

   - Sélectionnez **Non** si vous souhaitez que les nouveaux paramètres de l’espace de travail **s’appliquent uniquement aux nouvelles machines virtuelles**. Les nouveaux paramètres de l’espace de travail s’appliquent uniquement aux nouvelles installations d’agent ; machines virtuelles nouvellement détectées qui n’ont pas Microsoft Monitoring Agent installé.
   - Sélectionnez **Oui** si vous souhaitez que les nouveaux paramètres de l’espace de travail **s’appliquent à toutes les machines virtuelles**. En outre, chaque machine virtuelle connectée à un espace de travail créé par Security Center est reconnectée au nouvel espace de travail cible.

   > [!NOTE]
   > Si vous sélectionnez Oui, vous ne devez pas supprimer les espaces de travail créés par Security Center tant que toutes les machines virtuelles n’ont pas été reconnectées au nouvel espace de travail cible. Cette opération échoue si un espace de travail est supprimé trop tôt.
   >
   >

   - Sélectionnez **Annuler** pour annuler l’opération.

      ![Reconfigurer les machines virtuelles surveillées][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Que se passe-t-il si l’agent Microsoft Monitoring Agent est déjà installé en tant qu’extension sur la machine virtuelle ?
Security Center n’écrase pas les connexions existantes des espaces de travail utilisateur. Security Center stocke les données de sécurité de la machine virtuelle dans l’espace de travail déjà connecté. Security Center met à jour la version d’extension pour inclure l’ID de ressource Azure de la machine virtuelle afin de prendre en charge l’utilisation de Security Center.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Que se passe-t-il si Microsoft Monitoring Agent est installé sur la machine, mais pas en tant qu’extension ?
Si Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas en tant qu’extension Azure), Security Center n’installe pas Microsoft Monitoring Agent et la surveillance de la sécurité est limitée.

Pour plus d’informations, consultez la prochaine section, [Que se passe-t-il si l’agent direct SCOM ou OMS est déjà installé sur ma machine virtuelle ?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>Que se passe-t-il si l’agent direct SCOM ou OMS est déjà installé sur ma machine virtuelle ?
Security Center ne peut pas identifier à l’avance qu’un agent est installé.  Il tente d’installer l’extension Microsoft Monitoring Agent et échoue en raison de la présence de l’agent déjà installé.  Cet échec empêche le remplacement des paramètres de connexion de l’agent dans son espace de travail et évite la création d’un multihébergement.

> [!NOTE]
> La version de l’agent est mise à jour avec la dernière version de l’agent OMS.  Cela s’applique également aux utilisateurs SCOM.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Quel est l’impact de la suppression de ces extensions ?
Si vous supprimez l’extension Microsoft Monitoring, Security Center n’est pas en mesure de collecter des données de sécurité sur la machine virtuelle et certaines recommandations de sécurité et les alertes ne sont pas disponibles. Sous 24 heures, Security Center détermine que l’extension est absente de la machine virtuelle et la réinstalle.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Comment empêcher l’installation automatique de l’agent et la création de l’espace de travail ?
Vous pouvez désactiver l’approvisionnement automatique pour vos abonnements dans la stratégie de sécurité, mais ce n’est pas recommandé. La désactivation de l’approvisionnement automatique a pour effet de limiter les alertes et recommandations de Security Center. L’approvisionnement automatique est requis pour les abonnements appartenant au niveau tarifaire Standard. Pour désactiver l’approvisionnement automatique :

1. Si votre abonnement est configuré pour le niveau Standard, ouvrez la stratégie de sécurité de cet abonnement, puis sélectionnez le niveau **Gratuit**.

   ![Niveau tarifaire ][1]

2. Ensuite, désactivez l’approvisionnement automatique en sélectionnant **Non** sur le panneau **Stratégie de sécurité : collecte de données**.
   ![Collecte de données][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Dois-je refuser l’installation automatique de l’agent et la création de l’espace de travail ?

> [!NOTE]
> Veillez à consulter les sections [Quelles sont les implications d’un refus ?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) et [Étapes recommandées en cas de refus](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) si vous choisissez de refuser le provisionnement automatique.
>
>

Vous pouvez souhaiter refuser le provisionnement automatique si les points suivants s’appliquent à vous :

- L’installation automatique de l’agent par Security Center s’applique à l’ensemble de l’abonnement.  Vous ne pouvez pas appliquer l’installation automatique à un sous-ensemble de machines virtuelles. Si des machines virtuelles critiques ne peuvent pas être installées avec Microsoft Monitoring Agent, vous devez refuser le provisionnement automatique.
- L’installation de l’extension Microsoft Monitoring Agent met à jour la version de l’agent. Cela s’applique à un agent direct et à un agent SCOM. Si l’agent SCOM installé est en version 2012 et qu’il est mis à niveau, les fonctionnalités de facilité de gestion peuvent être perdues quand le serveur SCOM est également en version 2012. Vous devez envisager de refuser le provisionnement automatique si l’agent SCOM installé est en version 2012.
- Si vous disposez d’un espace de travail personnalisé externe à l’abonnement (un espace de travail centralisé), vous devez refuser le provisionnement automatique. Vous pouvez installer manuellement l’extension Microsoft Monitoring Agent et la connecter à votre espace de travail sans que Security Center ne remplace la connexion.
- Si vous souhaitez éviter de créer plusieurs espaces de travail par abonnement et que vous disposez de votre propre espace de travail personnalisé dans l’abonnement, deux options s’offrent à vous :

   1. Vous pouvez refuser le provisionnement automatique. Après la migration, définissez les paramètres d’espace de travail par défaut comme décrit dans [Comment puis-je utiliser mon espace de travail Log Analytics existant ?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Vous pouvez aussi autoriser l’exécution de la migration, l’installation de l’agent Microsoft Monitoring Agent sur les machines virtuelles et la connexion des machines virtuelles à l’espace de travail créé. Sélectionnez ensuite votre propre espace de travail personnalisé en définissant le paramètre d’espace de travail par défaut avec l’activation de la reconfiguration des agents déjà installés. Pour plus d’informations, consultez [Comment puis-je utiliser mon espace de travail Log Analytics existant ?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quelles sont les implications d’un refus du provisionnement automatique ?
Une fois la migration terminée, Security Center n’est pas en mesure de collecter des données de sécurité sur la machine virtuelle, et certaines recommandations de sécurité et alertes ne sont pas disponibles. En cas de refus, vous devez installer Microsoft Monitoring Agent manuellement. Consultez les [étapes recommandées en cas de refus](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quelles sont les étapes recommandées en cas de refus du provisionnement automatique ?
Vous devez installer manuellement Microsoft Monitoring Agent pour que Security Center puisse collecter des données de sécurité sur vos machines virtuelles et fournir des recommandations et des alertes. Pour obtenir des conseils sur l’installation, consultez [Connecter des ordinateurs Windows au service Log Analytics dans Azure](../log-analytics/log-analytics-windows-agent.md).

Vous pouvez connecter l’agent à n’importe quel espace de travail personnalisé existant ou à l’espace de travail créé par Security Center. Si les solutions « Security » ou « SecurityCenterFree » ne sont pas activées pour un espace de travail personnalisé, vous devez appliquer une solution. Pour ce faire, sélectionnez l’abonnement ou l’espace de travail personnalisé, puis appliquez un niveau tarifaire à l’aide du panneau **Stratégie de sécurité - Niveau tarifaire**.

   ![Niveau tarifaire ][1]

Security Center active la solution appropriée sur l’espace de travail en fonction du niveau tarifaire sélectionné.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Comment supprimer des extensions OMS installées par Security Center ?
Vous pouvez supprimer manuellement l’agent Microsoft Monitoring Agent. Ce n’est pas recommandé car cela limite les recommandations de Security Center et les alertes.

> [!NOTE]
> Si la collecte de données est activée, Security Center réinstalle l’agent après l’avoir supprimé.  Vous devez désactiver la collecte de données avant de supprimer manuellement l’agent. Consultez [Comment empêcher l’installation automatique de l’agent et la création de l’espace de travail ?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) pour obtenir des instructions sur la désactivation de la collecte de données.
>
>

Pour supprimer manuellement l’agent :

1.  Dans le portail, ouvrez **Log Analytics**.
2.  Dans le panneau Log Analytics, sélectionnez un espace de travail :
3.  Sélectionnez chaque machine virtuelle que vous ne souhaitez pas surveiller et sélectionnez **Déconnecter**.

   ![Supprimer l’agent][3]

> [!NOTE]
> Si une machine virtuelle Linux possède déjà un agent OM qui n’est pas un agent d’extension, la suppression de l’extension a pour effet de supprimer également l’agent. Le client doit alors le réinstaller.
>
>

## <a name="existing-oms-customers"></a>Clients OMS existants

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Est-ce que Security Center peut écraser les connexions existantes entre les machines virtuelles et les espaces de travail ?
Si l’agent Microsoft Monitoring Agent est déjà installé sur une machine virtuelle comme une extension Azure, Security Center n’écrase pas la connexion à l’espace de travail existante. En revanche, Security Center utilise l’espace de travail existant.

Une solution Security Center est installée sur l’espace de travail si elle ne l’était pas déjà. Celle-ci est appliquée uniquement aux machines virtuelles appropriées. Lorsque vous ajoutez une solution, elle est déployée automatiquement par défaut sur tous les agents Windows et Linux connectés à votre espace de travail Log Analytics. Le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) est une fonctionnalité OMS qui permet d’appliquer une étendue à vos solutions.

Si Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas en tant qu’extension Azure), Security Center n’installe pas Microsoft Monitoring Agent et la surveillance de la sécurité est limitée.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Que dois-je faire si je soupçonne la migration de la plateforme de données d’interférer avec la connexion entre une de mes machines virtuelles et mon espace de travail ?
Cela ne devrait pas se produire. Mais le cas échéant, vous devez [Créer une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md) et fournir les informations suivantes :

- L’ID de ressource Azure de la machine virtuelle affectée
- L’ID de ressource Azure de l’espace de travail configuré sur l’extension avant que la connexion ne soit interrompue
- L’agent et la version installés précédemment

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>Est-ce que Security Center installe des solutions sur mes espaces de travail OMS existants ? Quelles sont les conséquences sur la facturation ?
Lorsque Security Center détecte qu’une machine virtuelle est déjà connectée à un espace de travail que vous avez créé, il active des solutions sur cet espace de travail en fonction de votre niveau tarifaire. Les solutions sont appliquées uniquement aux machines virtuelles Azure pertinentes, via le [ciblage de solution](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), de sorte que la facturation reste la même.

- **Niveau Gratuit** : Security Center installe la solution « SecurityCenterFree » sur l’espace de travail. Vous n’êtes pas facturé pour le niveau gratuit.
- **Niveau Standard** : Security Center installe la solution « Security » sur l’espace de travail.

   ![Solutions sur l’espace de travail par défaut][4]

> [!NOTE]
> La solution « Sécurité » dans Log Analytics correspond à la solution Security & Audit d’OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Je dispose déjà d’espaces de travail dans mon environnement. Puis-je les utiliser pour collecter des données de sécurité ?
Si l’agent Microsoft Monitoring Agent est déjà installé comme une extension Azure sur une machine virtuelle, Security Center utilise l’espace de travail connecté existant. Une solution de Security Center est installée sur l’espace de travail si elle ne l’était pas déjà. La solution est appliquée uniquement aux machines virtuelles appropriées via le [ciblage de solution](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Lorsque Security Center installe l’agent Microsoft Monitoring Agent sur des machines virtuelles, il utilise les espaces de travail par défaut créés par Security Center. Les clients seront bientôt en mesure de configurer les espaces de travail utilisés.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Je dispose déjà de la solution de sécurité sur mes espaces de travail. Quelles sont les conséquences sur la facturation ?
La solution Security & Audit permet d’activer les fonctionnalités Security Center de niveau Standard pour les machines virtuelles Azure. Si la solution Security & Audit est déjà installée sur un espace de travail, Security Center utilise la solution existante. Il n’y aura aucune modification de la facturation.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la migration de plateforme Security Center, consultez

- [Migration de plateforme Azure Security Center](security-center-platform-migration.md)
- [Guide de résolution des problèmes d’Azure Security Center](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
