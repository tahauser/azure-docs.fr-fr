---
title: "Créer et personnaliser des plans de récupération pour le basculement et la récupération dans Azure Site Recovery | Microsoft Docs"
description: "Décrit comment créer et personnaliser des plans de récupération dans Azure Site Recovery en vue de basculer et récupérer des machines virtuelles et des serveurs physiques"
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
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>Créer des plans de récupération


Cet article fournit des informations sur la création et la personnalisation des plans de récupération dans [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Créez des plans de récupération. Ceux-ci effectuent les opérations suivantes :

* Ils définissent les groupes de machines qui basculent ensemble, puis démarrent ensemble.
* Ils modélisent les dépendances entre les machines, en les rassemblant au sein d’un groupe de plan de récupération. Par exemple, pour basculer et afficher une application spécifique, vous devez regrouper toutes les machines virtuelles pour cette application dans un même groupe de plan de récupération.
* Exécuter un basculement. Vous pouvez exécuter un basculement test, planifié ou non planifié sur un plan de récupération.

## <a name="why-use-recovery-plans"></a>Pourquoi utiliser des plans de récupération ?

Les plans de récupération vous permettent de planifier un processus de récupération systématique en créant de petites unités indépendantes que vous pouvez gérer. Ces unités représentent généralement une application dans votre environnement. Un plan de récupération vous permet non seulement de définir la séquence de démarrage de la machine virtuelle, mais également d’automatiser des tâches courantes pendant la récupération.


**Principalement, une méthode pour vérifier que vous êtes prêt pour la migration vers le cloud ou la récupération d’urgence consiste à vous assurer que chacune de vos applications fait partie d’un plan de récupération et que chacun des plans de récupération est testé pour la récupération vers Microsoft Azure. Grâce à ces préparatifs, vous pouvez migrer ou basculer en toute confiance l’intégralité de votre centre de données vers Microsoft Azure.**
 
Voici les trois propositions de valeur clés d’un plan de récupération :

### <a name="model-an-application-to-capture-dependencies"></a>Modéliser une application pour capturer les dépendances

Un plan de récupération est un groupe de machines virtuelles, comprenant généralement une application, qui basculent ensemble. La création d’un plan de récupération vous permet d’améliorer ce groupe afin de capturer les propriétés spécifiques à l’application.
 
Prenons l’exemple d’une application à trois niveaux classique avec

* un serveur principal SQL
* un intergiciel (middleware)
* un serveur frontal web

Le plan de récupération peut être personnalisé afin de s’assurer que les machines virtuelles démarrent dans le bon ordre après un basculement. Le serveur principal SQL doit démarrer en premier, suivi de l’intergiciel (middleware) et le serveur frontal web doit démarrer en dernier. Cet ordre permet de s’assurer que l’application fonctionne au moment où la dernière machine virtuelle démarre. Par exemple, lorsque l’intergiciel (middleware) démarre, il tente de se connecter au niveau SQL, et le plan de récupération a vérifié que le niveau SQL est déjà en cours d’exécution. Les serveurs frontaux qui démarrent en dernier vérifient également que les utilisateurs finaux ne se connectent pas à l’URL de l’application par erreur tant que tous les composants ne sont pas en cours d’exécution et que l’application n’est pas prête à accepter des requêtes. Pour créer ces dépendances, vous pouvez personnaliser le plan de récupération pour ajouter des groupes. Sélectionnez ensuite une machine virtuelle et modifiez son groupe pour la déplacer entre les groupes.

![Exemple de plan de récupération](./media/site-recovery-create-recovery-plans/rp.png)

Lorsque vous avez terminé la personnalisation, vous pouvez visualiser les étapes exactes de la récupération. Voici l’ordre des étapes exécutées pendant le basculement d’un plan de récupération :

* Tout d’abord, une étape d’arrêt tente d’arrêter les machines virtuelles locales (sauf en cas de test de basculement pendant lequel le site principal doit rester en cours d’exécution)
* Le basculement en parallèle de toutes les machines virtuelles du plan de récupération est ensuite déclenché. L’étape de basculement prépare les disques des machines virtuelles à partir des données répliquées.
* Enfin, les groupes de démarrage sont exécutés dans l’ordre, en commençant par les machines virtuelles de chaque groupe : Groupe 1 en premier, puis Groupe 2 et enfin Groupe 3. Si un groupe comprend plusieurs machines virtuelles (un serveur frontal web à équilibrage de charge par exemple), elles sont toutes démarrées en parallèle.

**Le séquencement entre les groupes permet de s’assurer que les dépendances entre différents niveaux d’application sont respectées et que le parallélisme, si applicable, améliore le RTO de la récupération d’application.**

   > [!NOTE]
   > Les machines faisant partie d’un même groupe basculent en parallèle. Les machines faisant partie de différents groupes basculent dans l’ordre des groupes. Le basculement des machines du Groupe 2 ne commence que lorsque toutes les machines du Groupe 1 ont basculé et démarré.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatiser la plupart des tâches de récupération afin de réduire le RTO

La récupération d’applications de grande taille peut être une tâche complexe. Il est également difficile de mémoriser les étapes exactes de personnalisation après un basculement ou une migration. Parfois ce n’est pas vous mais une autre personne, qui ne connaît pas la complexité de l’application, qui doit déclencher le basculement. Se rappeler d’un très grand nombre d’étapes manuelles dans une situation d’urgence est difficile et source d’erreurs. Un plan de récupération vous permet d’automatiser les actions nécessaires, que vous devez effectuer à chaque étape, à l’aide de runbooks Microsoft Azure Automation. Grâce aux runbooks, vous pouvez automatiser des tâches de récupération courantes, comme dans les exemples ci-dessous. Pour les tâches ne pouvant pas être automatisées, les plans de récupération vous offrent également la possibilité d’inclure des actions manuelles.

* Tâches sur la machine virtuelle Azure après le basculement : elles sont généralement nécessaires pour que vous puissiez vous connecter à la machine virtuelle. Par exemple :
    * Créer une adresse IP publique sur la machine virtuelle après le basculement
    * Assigner un groupe de sécurité réseau (NSG) à la carte réseau de la machine virtuelle basculée
    * Ajouter un équilibreur de charge à un groupe à haute disponibilité
* Tâches dans la machine virtuelle après le basculement : elles reconfigurent l’application afin qu’elle continue à fonctionner correctement dans le nouvel environnement. Par exemple :
    * Modifier la chaîne de connexion de la base de données dans la machine virtuelle
    * Modifier la configuration/les règles du serveur web

**Un plan de récupération complet qui automatise les tâches après récupération à l’aide de runbooks d’automatisation vous permet d’obtenir un basculement en un clic et d’optimiser le RTO.**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Test de basculement pour être prêt en cas de sinistre

Un plan de récupération peut être utilisé pour déclencher un basculement ou un test de basculement. Vous devez toujours effectuer un test de basculement sur l’application avant d’exécuter un basculement. Le test de basculement vous permet de vérifier si l’application démarrera sur le site de récupération.  Si vous avez manqué quelque chose, vous pouvez facilement procéder à un nettoyage et répéter le test de basculement. Exécutez plusieurs fois le test de basculement jusqu’à être sûr que l’application est correctement récupérée.

![Tester le plan de récupération](./media/site-recovery-create-recovery-plans/rptest.png)

**Chaque application est différente et vous devez créer des plans de récupération personnalisés pour chacune. En outre, dans l’environnement dynamique du centre de données, les applications et leurs dépendances changent. Testez le basculement de vos applications une fois par trimestre pour vérifier que le plan de récupération est à jour.**

## <a name="how-to-create-a-recovery-plan"></a>Comment créer un plan de récupération

1. Cliquez sur l’onglet **Plans de récupération** > **Créer un plan de récupération**.
   Spécifiez un nom pour le plan de récupération, puis définissez une source et une cible. Le basculement et la récupération doivent être activés sur les machines virtuelles de l’emplacement source. Choisissez une source et une cible en fonction des machines virtuelles que vous voulez inclure dans le plan de récupération. 

   |Scénario                   |Source               |Cible           |
   |---------------------------|---------------------|-----------------|
   |Azure vers Azure             |Région Azure         |Région Azure     |
   |VMware vers Azure            |Serveur de configuration |Azure            |
   |VMM vers Azure               |Nom convivial de VMM    |Azure            |
   |Site Hyper-V vers Azure      |Nom du site Hyper-V    |Azure            |
   |Machines physiques vers Azure |Serveur de configuration |Azure            |
   |VMM vers VMM                 |Nom convivial de VMM    |Nom convivial de VMM|

   > [!NOTE]
   > Un plan de récupération peut contenir des machines virtuelles ayant les mêmes source et cible. Les machines virtuelles de VMware et VMM ne peuvent pas faire partie du même plan de récupération. Des machines virtuelles VMware et des machines physiques peuvent toutefois être ajoutées à un même plan car la source des deux est un serveur de configuration.

2. Dans **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles (ou le groupe de réplication) que vous voulez ajouter au groupe par défaut (Groupe 1) dans le plan de récupération. Seules les machines virtuelles ayant été protégées sur la source (comme sélectionné dans le plan de récupération) et qui sont protégées vis-à-vis de la cible (comme sélectionné dans le plan de récupération) pourront être sélectionnées.

## <a name="how-to-customize-and-extend-recovery-plans"></a>Comment personnaliser et étendre les plans de récupération

Vous pouvez personnaliser et étendre les plans de récupération en accédant au panneau des ressources de plan de récupération Site Recovery, puis en cliquant sur l’onglet Personnaliser.

Vous pouvez personnaliser et étendre les plans de récupération :

- **Ajouter de nouveaux groupes** : ajouter des groupes de plan de récupération supplémentaires (jusqu’à sept) au groupe par défaut, puis ajouter des ordinateurs ou groupes de réplication supplémentaires à ces groupes de plan de récupération. Les groupes sont numérotés dans l’ordre dans lequel vous les ajoutez. Vous ne pouvez inclure une machine virtuelle ou un groupe de réplication qu’au sein d’un seul plan de récupération.
- **Ajouter une action manuelle**: il est possible d’ajouter des actions manuelles qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque le plan de récupération s’exécute, il s’arrête au point où vous avez inséré l’action manuelle. Une boîte de dialogue vous invite à spécifier que l’action manuelle est terminée.
- **Ajouter un script** : vous pouvez ajouter des scripts qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque vous ajoutez un script, vous ajoutez un nouvel ensemble d’actions au groupe. Par exemple, un ensemble d’étapes préliminaires au sein du Groupe 1 est créé avec le nom : Groupe 1 : Étapes préliminaires. L’ensemble des étapes préliminaires sont répertoriées dans cet ensemble. Pour ajouter un script sur le site principal, vous devez disposer d’un serveur VMM déployé. [Plus d’informations](site-recovery-how-to-add-vmmscript.md)
- **Ajouter des runbooks Azure** : vous pouvez étendre des plans de récupération avec des runbooks Azure. Par exemple, pour automatiser des tâches ou pour créer une récupération en une seule étape. [Plus d’informations](site-recovery-runbook-automation.md)


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>Comment ajouter un script, un runbook ou une action manuelle à un plan

Vous pouvez ajouter un script ou une action manuelle au groupe de plan de récupération par défaut après y avoir ajouté des machines virtuelles ou des groupes de réplication et après avoir créé le plan.

1. Ouvrez le plan de récupération.
2. Cliquez sur un élément de la liste **Étape,** puis cliquez sur **Script** ou sur **Action manuelle**.
3. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Pour faire monter ou descendre le script, utilisez les boutons de **Déplacer vers le haut** et de **Déplacer vers le bas**.
4. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**. Dans **Chemin d’accès du script**, tapez le chemin d’accès relatif au partage. Dans l’exemple VMM ci-dessous, spécifiez le chemin d’accès : **\RPScripts\RPScript.PS1**.
5. Si vous ajoutez un Runbook Azure Automation, spécifiez le compte Azure Automation dans lequel se trouve le runbook, puis sélectionnez le script runbook Azure approprié.
6. Afin de vous assurer du bon fonctionnement du script, exécutez un basculement du plan de récupération.

Les options de script ou de runbook sont disponibles dans les scénarios suivants uniquement lorsque vous effectuez un basculement ou une restauration automatique. Une action manuelle est disponible pour le basculement et la restauration automatique.


|Scénario               |Basculement |Restauration automatique |
|-----------------------|---------|---------|
|Azure vers Azure         |Runbooks |Runbook  |
|VMware vers Azure        |Runbooks |N/D       | 
|VMM vers Azure           |Runbooks |Script   |
|Site Hyper-V vers Azure  |Runbooks |N/D       |
|VMM vers VMM             |Script   |Script   |


## <a name="next-steps"></a>étapes suivantes

[En savoir plus](site-recovery-failover.md) sur l’exécution des basculements.

Regardez cette vidéo pour voir le plan de récupération en action.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
