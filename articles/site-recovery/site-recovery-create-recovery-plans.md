---
title: "Créer et personnaliser un plan de récupération pour le basculement et la récupération dans Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment créer et personnaliser des plans de récupération dans Azure Site Recovery. Cet article explique comment basculer et récupérer des machines virtuelles et des serveurs physiques."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>Créer un plan de récupération à l’aide de Site Recovery

Cet article décrit la création et la personnalisation d’un plan de récupération dans [Azure Site Recovery](site-recovery-overview.md).

Créez un plan de récupération pour effectuer les opérations répertoriées ici.

* Définir les groupes de machines qui basculent ensemble, et qui démarrent ensuite ensemble.
* Modéliser les dépendances entre les machines, en les rassemblant au sein d’un groupe de plan de récupération. Par exemple, pour basculer et afficher une application spécifique, incluez toutes les machines virtuelles de cette application dans le même groupe de plan de récupération.
* Exécuter un basculement. Vous pouvez exécuter un basculement test, planifié ou non planifié sur un plan de récupération.

## <a name="why-use-a-recovery-plan"></a>Pourquoi utiliser un plan de récupération

Un plan de récupération vous permet de préparer un processus de récupération systématique en créant de petites unités indépendantes que vous pouvez gérer. Ces unités représentent généralement une application dans votre environnement. Un plan de récupération peut vous aider à définir la séquence suivant laquelle les machines virtuelles démarrent. Vous pouvez également utiliser un plan de récupération pour automatiser des tâches courantes au cours de la récupération.

> [!TIP]
> Une façon de vérifier que vous êtes prêt pour la migration cloud ou la récupération d’urgence est de vous assurer que chacune de vos applications est intégrée à un plan de récupération. Par ailleurs, vérifiez que chaque plan de récupération est testé pour la récupération vers Azure. En prenant ces dispositions, vous pouvez migrer ou basculer en toute confiance l’intégralité de votre centre de données vers Azure.
 
Un plan de récupération présente trois atouts principaux :

* Modéliser une application pour capturer les dépendances
* Automatiser la plupart des tâches de récupération afin de réduire le RTO
* Tester le basculement pour être prêt en cas d’urgence

### <a name="model-an-application-to-capture-dependencies"></a>Modéliser une application pour capturer les dépendances

Un plan de récupération est un groupe de machines virtuelles qui forment généralement une application, et basculent ensemble. À l’aide de constructions de plan de récupération, vous pouvez améliorer un groupe de plan de récupération et capturer les propriétés propres à l’application. 

Dans cet article, nous utilisons une application classique à trois niveaux qui peut le cas échéant disposer d’un backend SQL, d’un intergiciel (middleware) et d’un frontend web. Vous pouvez personnaliser le plan de récupération pour vous assurer que les machines virtuelles démarrent dans l’ordre approprié après un basculement. Le backend SQL doit démarrer en premier, suivi par le middleware, puis par le frontend web. Cet ordre garantit le bon fonctionnement de l’application au moment où la dernière machine virtuelle démarre. 

Par exemple, lorsque le middleware démarre, il tente de se connecter à la couche SQL. Le plan de récupération garantit que la couche SQL est déjà en cours d’exécution. Le fait de démarrer le frontend en dernier permet également de s’assurer que les utilisateurs finaux ne se connectent pas à l’URL de l’application avant que tous les composants ne soient opérationnels, et que l’application soit prête à accepter des requêtes. Pour créer ces dépendances, personnalisez le plan de récupération en ajoutant des groupes, puis sélectionnez une machine virtuelle. Pour déplacer une machine virtuelle entre des groupes, changez le groupe de cette machine.

![Capture d’écran d’un exemple de plan de récupération dans Site Recovery](./media/site-recovery-create-recovery-plans/rp.png)

Après avoir terminé la personnalisation, vous pouvez visualiser précisément les étapes de la récupération. Voici l’ordre des étapes qui sont exécutées pendant le basculement d’un plan de récupération.

1. Une étape d’arrêt tente de mettre hors tension les machines virtuelles locales. (Sauf dans un test de basculement, pendant lequel le site principal doit continuer à s’exécuter.)
2. La tentative d’arrêt déclenche le basculement en parallèle de toutes les machines virtuelles du plan de récupération. L’étape de basculement prépare les disques des machines virtuelles au moyen des données répliquées.
3. Les groupes de démarrage s’exécutent dans leur ordre et démarrent les machines virtuelles dans chaque groupe. Le groupe 1 s’exécute en premier lieu, puis c’est le tour du groupe 2, et enfin celui du groupe 3. Si un groupe comprend plusieurs machines virtuelles (un frontend web à équilibrage de charge, par exemple), elles démarrent toutes en parallèle.

> [!TIP]
> La mise en séquence des groupes garantit le respect des dépendances entre les différentes couches Application. Le parallélisme, lorsque son utilisation est appropriée, améliore le RTO de la récupération de l’application.

   > [!NOTE]
   > Les machines faisant partie d’un même groupe basculent en parallèle. Les machines faisant partie de différents groupes basculent dans l’ordre des groupes. Le basculement des machines du groupe 2 ne commence que lorsque toutes les machines du groupe 1 ont basculé et démarré.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatiser la plupart des tâches de récupération afin de réduire le RTO

La récupération de grandes applications peut être une tâche complexe. S’il faut se rappeler d’un très grand nombre d’étapes manuelles dans une situation d’urgence, grandes sont les chances de se tromper. De plus, la personne amenée à déclencher le basculement peut tout ignorer de la complexité de l’application. 

Vous pouvez utiliser un plan de récupération pour automatiser les actions indispensables que vous devez effectuer à chaque étape. Vous configurez ces actions à l’aide de runbooks Azure Automation, par exemple. Grâce aux runbooks, vous automatisez des tâches de récupération courantes, comme dans les cas suivants. Pour les tâches qui ne peuvent pas être automatisées, vous pouvez insérer des actions manuelles dans vos plans de récupération.

* **Tâches sur la machine virtuelle Azure après le basculement** : ces tâches sont généralement primordiales pour la connexion à la machine virtuelle. Exemples :
    * Créer une adresse IP publique sur la machine virtuelle après le basculement
    * Affecter un groupe de sécurité réseau à la carte réseau sur la machine virtuelle ayant basculé
    * Ajouter un équilibreur de charge à un groupe à haute disponibilité
* **Tâches dans la machine virtuelle après le basculement** : ces tâches reconfigurent l’application afin qu’elle continue à fonctionner correctement dans le nouvel environnement. Exemples :
    * Modifier la chaîne de connexion de base de données dans la machine virtuelle
    * Modifier les règles ou la configuration du serveur web

> [!TIP]
> Réussissez un basculement en un clic et optimisez le RTO en créant un plan de récupération complet qui automatise les tâches post-récupération à l’aide de runbooks Automation.

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Test de basculement pour être prêt en cas de sinistre

Vous pouvez utiliser un plan de récupération pour déclencher un basculement ou un test de basculement. Effectuez toujours un test de basculement sur l’application avant de procéder à un basculement. Les tests de basculement vous permettent de vérifier le démarrage de l’application sur le site de récupération. Si vous avez oublié quelque chose dans votre configuration, vous pouvez facilement nettoyer et refaire le test de basculement. Exécutez plusieurs fois le test de basculement jusqu’à ce que vous soyez sûr que l’application est correctement récupérée.

![Capture d’écran d’un exemple de test de plan de récupération dans Site Recovery](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> Chaque application étant unique, vous devez créer des plans de récupération personnalisés pour chacune. 
>
> De plus, dans l’environnement dynamique d’aujourd’hui qui est axé sur le centre de données, les applications et leurs dépendances changent fréquemment. Pour vérifier que le plan de récupération est à jour, testez le basculement de vos applications une fois par trimestre.

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Sélectionnez **Plans de récupération** > **Créer un plan de récupération**.
   Spécifiez un nom pour le plan de récupération, puis définissez une source et une cible. Le basculement et la récupération doivent être activés sur les machines virtuelles de l’emplacement source. Choisissez une source et une cible en fonction des machines virtuelles que vous voulez inclure dans le plan de récupération. 

   |Scénario                   |Source               |Cible           |
   |---------------------------|---------------------|-----------------|
   |Azure vers Azure             |Région Azure         |Région Azure     |
   |VMware vers Azure            |Serveur de configuration |Azure            |
   |Virtual Machine Manager (VMM) vers Azure               |Nom d’affichage VMM    |Azure            |
   |Entre un site Hyper-V et Microsoft Azure      |Nom du site Hyper-V    |Azure            |
   |Machines physiques vers Azure |Serveur de configuration |Azure            |
   |VMM vers VMM                 |Nom convivial de VMM    |Nom d’affichage VMM|

   > [!NOTE]
   > Un plan de récupération peut contenir des machines virtuelles ayant les mêmes source et cible. Les machines virtuelles VMware et System Center Virtual Machine Manager (VMM) ne peuvent pas coexister dans le même plan de récupération. Par contre, vous pouvez ajouter des machines virtuelles VMware et des machines physiques dans le même plan de récupération. Dans ce cas, la source pour les deux machines est un serveur de configuration.

2. Sous **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles (ou le groupe de réplication) que vous voulez ajouter au groupe par défaut (Groupe 1) dans le plan de récupération. Vous ne pouvez sélectionner que les machines virtuelles qui ont été protégées sur la source (comme sélectionné dans le plan de récupération) et qui sont protégées sur la cible (comme sélectionné dans le plan de récupération).

## <a name="customize-and-extend-recovery-plans"></a>Personnaliser et étendre les plans de récupération

Pour personnaliser et étendre des plans de récupération, consultez le volet de ressources du plan de récupération dans Site Recovery. Sélectionnez l’onglet **Personnaliser**. Vous pouvez personnaliser et étendre des plans de récupération à l’aide des options décrites ici.

- **Ajouter de nouveaux groupes** : vous pouvez ajouter jusqu’à sept groupes de plan de récupération supplémentaires au groupe par défaut. Vous pouvez ensuite ajouter d’autres machines ou d’autres groupes de réplication à ces groupes de plan de récupération. Les groupes sont numérotés dans l’ordre dans lequel vous les ajoutez. Vous ne pouvez inclure une machine virtuelle ou un groupe de réplication qu’au sein d’un seul groupe de plan de récupération.
- **Ajouter une action manuelle** : il est possible d’ajouter des actions manuelles qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque le plan de récupération s’exécute, il s’arrête au point où vous avez inséré l’action manuelle. Une boîte de dialogue vous invite à spécifier que l’action manuelle est terminée.
- **Ajouter un script** : vous pouvez ajouter des scripts qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque vous ajoutez un script, vous ajoutez un nouvel ensemble d’actions au groupe. Par exemple, un ensemble d’étapes préliminaires au sein du groupe 1 est créé avec le nom : *Groupe 1 : Étapes préliminaires*. L’ensemble des étapes préliminaires sont répertoriées dans cet ensemble. Vous ne pouvez ajouter de script sur le site principal que si vous disposez d’un serveur VMM déployé. Pour plus d’informations, consultez [Ajouter un script VMM à un plan de récupération](site-recovery-how-to-add-vmmscript.md).
- **Ajouter des Runbooks Azure** : vous pouvez étendre des plans de récupération à l’aide de Runbooks Azure. Par exemple, vous pouvez utiliser un Runbook pour automatiser des tâches, ou pour créer une récupération en une étape. Pour plus d’informations, consultez [Ajouter des Runbooks Azure Automation à des plans de récupération](site-recovery-runbook-automation.md).


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>Ajouter un script, un Runbook ou une action manuelle à un plan

Après avoir ajouté des machines virtuelles ou des groupes de réplication à un groupe de plan de récupération par défaut, vous pouvez ajouter un script ou une action manuelle au groupe de plan de récupération.

1. Ouvrez le plan de récupération.
2. Dans la liste **Étape**, sélectionnez un élément. Ensuite, sélectionnez **Script** ou **Action manuelle**.
3. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Pour faire monter ou descendre le script dans la liste, utilisez les boutons **Monter** et **Descendre**.
4. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**. Dans **Chemin du script**, entrez le chemin relatif du partage. Par exemple, **\RPScripts\RPScript.PS1**.
5. Si vous ajoutez un Runbook Automation, spécifiez le compte Automation dans lequel se trouve le Runbook. Ensuite, sélectionnez le script Runbook Azure.
6. Afin de vous assurer du bon fonctionnement du script, effectuez un basculement du plan de récupération.

Les options de script ou de Runbook sont uniquement disponibles dans les scénarios suivants, et lors d’un basculement ou d’une restauration automatique. Une action manuelle est disponible pour le basculement et la restauration automatique.


|Scénario               |Basculement |Restauration automatique |
|-----------------------|---------|---------|
|Azure vers Azure         |Runbook |Runbook  |
|VMware vers Azure        |Runbook |N/D       | 
|VMM vers Azure           |Runbook |Script   |
|Entre un site Hyper-V et Microsoft Azure  |Runbook |N/D       |
|VMM vers VMM             |Script   |Script   |


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [l’exécution des basculements](site-recovery-failover.md).  
* Pour voir le plan de récupération en action, regardez cette vidéo :
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
