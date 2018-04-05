---
title: Créer et personnaliser des plans de récupération pour le basculement et la récupération dans Azure Site Recovery | Microsoft Docs
description: Découvrez comment créer et personnaliser des plans de récupération dans Azure Site Recovery. Cet article explique comment basculer et récupérer des machines virtuelles et des serveurs physiques.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-customize-recovery-plans"></a>Créer et personnaliser des plans de récupération

Cet article décrit la création et la personnalisation d’un plan de récupération dans [Azure Site Recovery](site-recovery-overview.md). Avant de commencer, [découvrez-en plus](recovery-plan-overview.md) sur les plans de récupération.

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Dans le coffre Recovery Services, sélectionnez **Recovery Plans (Site Recovery) (Plans de récupération (Site Recovery))** > **+Plan de récupération**.
2. Dans **Créer un plan de récupération**, spécifiez un nom pour le plan.
3. Choisissez une source et une cible basées sur les ordinateurs dans le plan, puis sélectionnez **Resource Manager** comme modèle de déploiement. Le basculement et la récupération doivent être activés sur les ordinateurs de l’emplacement source. 

   **Type de basculement** | **Source** | **Cible** 
   --- | --- | ---
   Azure vers Azure | Région Azure |Région Azure
   VMware vers Azure | Serveur de configuration | Azure
   Machines physiques vers Azure | Serveur de configuration | Azure   
   Hyper-V géré par VMM vers Azure  | Nom d’affichage VMM | Azure
   Hyper-V sans VMM vers Azure | Nom du site Hyper-V | Azure
   VMM vers VMM |Nom convivial de VMM | Nom d’affichage VMM 

   > [!NOTE]
   > Un plan de récupération peut contenir des ordinateurs ayant les mêmes source et cible. Des machines virtuelles VMware et Hyper-V gérées par VMM ne peuvent pas se trouver dans le même plan. Les serveurs physiques et machines virtuelles VMware peuvent être dans le même plan, où la source est un serveur de configuration.

2. Dans **Sélectionner les machines virtuelles**, sélectionnez les ordinateurs (ou le groupe de réplication) que vous voulez ajouter au plan. Cliquez ensuite sur **OK**.
    - Les ordinateurs sont ajoutés au groupe par défaut (Groupe 1) dans le plan. Après le basculement, tous les ordinateurs dans ce groupe démarrent en même temps.
    - Vous pouvez sélectionner uniquement les ordinateurs qui se trouvent dans les emplacements source et cible que vous avez spécifiés. 
1. Cliquez sur **OK** pour créer le plan.

## <a name="add-a-group-to-a-plan"></a>Ajouter un groupe à un plan

Vous créez d’autres groupes et ajoutez des ordinateurs à différents groupes de façon à pouvoir spécifier un comportement différent pour chaque groupe. Par exemple, vous pouvez spécifier le moment où les ordinateurs dans un groupe doivent démarrer après un basculement, ou spécifier des actions personnalisées par groupe.

1. Dans **Plans de récupération**, cliquez avec le bouton droit sur le plan > **Personnaliser**. Par défaut, après avoir créé un plan, tous les ordinateurs que vous avez ajoutés à ce dernier se trouvent dans le Groupe 1 par défaut.
2. Cliquez sur **+Groupe**. Par défaut, un nouveau groupe est numéroté dans l’ordre selon lequel il est ajouté. Vous pouvez avoir sept groupes maximum.
3. Sélectionnez l’ordinateur que vous souhaitez déplacer dans le nouveau groupe, cliquez sur **Modifier le groupe**, puis sélectionnez le nouveau groupe. Ou bien, cliquez avec le bouton droit sur le nom du groupe > **Élément protégé** et ajoutez des ordinateurs au groupe. Un groupe de réplication ou un ordinateur peut uniquement appartenir à un groupe dans un plan de récupération.


## <a name="add-a-script-or-manual-action"></a>Ajouter un script ou une action manuelle

Vous pouvez personnaliser un plan de récupération en ajoutant un script ou une action manuelle. Notez les points suivants :

- Si vous exécutez une réplication vers Azure, vous pouvez intégrer les runbooks Azure Automation dans votre plan de récupération. [Plus d’informations](site-recovery-runbook-automation.md)
- Si vous répliquez des machines virtuelles Hyper-V gérées par System Center VMM, vous pouvez créer un script sur le serveur VMM local et l’inclure dans le plan de récupération.
- Lorsque vous ajoutez un script, vous ajoutez un nouvel ensemble d’actions au groupe. Par exemple, un ensemble d’étapes préliminaires au sein du groupe 1 est créé avec le nom : *Groupe 1 : Étapes préliminaires*. L’ensemble des étapes préliminaires sont répertoriées dans cet ensemble. Vous ne pouvez ajouter de script sur le site principal que si vous disposez d’un serveur VMM déployé.
- Si vous ajoutez une action manuelle lorsque le plan de récupération s’exécute, il s’arrête au point où vous avez inséré l’action manuelle. Une boîte de dialogue vous invite à spécifier que l’action manuelle est terminée.
- Pour créer un script sur le serveur VMM, suivez les instructions de [cet article](hyper-v-vmm-recovery-script.md).
- Les scripts peuvent être appliqués pendant le basculement vers le site secondaire et pendant la restauration automatique du site secondaire vers le site principal. La prise en charge dépend de votre scénario de réplication :
    
    **Scénario** | **Type de basculement** | **Restauration automatique**
    --- | --- | --- 
    Azure vers Azure  | Runbook | Runbook
    VMware vers Azure | Runbook | N/D 
    Hyper-V avec VMM vers Azure | Runbook | Script
    Entre un site Hyper-V et Microsoft Azure | Runbook | N/D
    Site VMM vers site VMM secondaire | Script | Script

1. Dans le plan de récupération, cliquez sur l’étape à laquelle l’action doit être ajoutée et spécifiez quand l’action doit se produire : a. Si vous souhaitez que l’action se produise avant que les ordinateurs du groupe démarrent après le basculement, sélectionnez **Ajouter une action préalable**.
    b. Si vous souhaitez que l’action se produise après que les ordinateurs du groupe démarrent après le basculement, sélectionnez **Ajouter une action postérieure**. Pour déplacer l’action dans la liste, utilisez les boutons **Monter** et **Descendre**.
2. Dans **Insérer une action**, sélectionnez **Script** ou **Action manuelle**.
3. Si vous souhaitez ajouter une action manuelle, procédez comme suit : a. Tapez un nom pour l’action, ainsi que les instructions de l’action. La personne qui exécute le basculement verra ces instructions.
    b. Spécifiez si vous souhaitez ajouter l’action manuelle pour tous les types de basculement (Test, Basculement, Basculement planifié (le cas échéant)). Cliquez ensuite sur **OK**.
4. Si vous souhaitez ajouter un script, procédez comme suit : a. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**, puis dans **Chemin du script**, entrez le chemin d’accès relatif au partage. Par exemple, si le partage est situé à l’emplacement \\<VMMServerName>\MSSCVMMLibrary\RPScripts, spécifiez le chemin d’accès : \RPScripts\RPScript.PS1.
    b. Si vous ajoutez un Runbook Azure Automation, spécifiez le **Compte Azure Automation** dans lequel se trouve le Runbook, puis sélectionnez le **Script Runbook Azure** approprié.
5. Afin de vous assurer du bon fonctionnement du script, effectuez un test de basculement du plan de récupération.

## <a name="watch-a-video"></a>Regarder une vidéo

Regardez une vidéo qui montre comment créer un plan de récupération.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’exécution des basculements](site-recovery-failover.md).  

    
