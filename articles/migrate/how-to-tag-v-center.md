---
title: Grouper des machines virtuelles avec le marquage VMware vCenter | Microsoft Docs
description: "Explique comment créer un groupe avant d’exécuter une évaluation avec le service Azure Migrate."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Grouper des machines virtuelles avec le marquage vCenter

Cet article explique comment créer un groupe de machines pour l’évaluation [Azure Migrate](migrate-overview.md) grâce au marquage dans VMware vCenter. La catégorie de balise à utiliser est spécifiée lors de la création du groupe. 

## <a name="set-up-tagging"></a>Configurer le marquage

Au cours du déploiement Azure Migrate, une machine virtuelle Azure Migrate locale détecte les machines s’exécutant sur des hôtes ESXi gérés par un serveur vCenter. Vous devez configurer le marquage vCenter avant le processus de détection.

1. Dans le client Web VMware vSphere, accédez au serveur vCenter.
2. Cliquez sur **Balises** pour passer en revue toutes les balises actives.
3. Pour marquer une machine virtuelle, sélectionnez **Objets connexes** > **Machines virtuelles**, puis sélectionnez la machine virtuelle à marquer.
4. Dans **Résumé** > **Balises**, cliquez sur **Affecter**. 
5. Cliquez sur **Nouvelle balise** et spécifiez un nom de balise et une description.
6. Pour créer une catégorie de balise, sélectionnez **Nouvelle catégorie** dans la liste déroulante.
7. Spécifiez un nom de catégorie, une description et la cardinalité. Cliquez ensuite sur **OK**.

    ![Balises de machines virtuelles](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Utiliser le marquage pour créer des groupes

1. Configurez la détection des machines locales suivant le [Didacticiel d’évaluation VMware](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. Dans **Catégorie de balise pour le groupement**, sélectionnez la catégorie de balise vCenter qui servira de point de départ au groupe d’évaluation. Azure Migrate crée automatiquement un groupe pour la catégorie sélectionnée.

    

## <a name="next-steps"></a>Étapes suivantes

[Configurer l’évaluation de machines virtuelles VMware](tutorial-assessment-vmware.md).
