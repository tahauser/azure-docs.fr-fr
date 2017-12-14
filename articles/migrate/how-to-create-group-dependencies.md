---
title: "Affiner un groupe d’évaluation avec mappage de dépendances de groupe dans Azure Migrate | Microsoft Docs"
description: "Explique comment affiner une évaluation à l’aide du mappage de dépendances de groupe dans le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: c30d6546e7c2d471d4b262a8af1ce593b2c1c3fb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Affiner un groupe à l’aide du mappage de dépendances de groupe

Cet article explique comment configurer le mappage de dépendances de groupe pour l’évaluation [Azure Migrate](migrate-overview.md). On utilise généralement cette méthode pour affiner les paramètres d’un groupe existant, en vérifiant par recoupement les dépendances du groupe avant d’exécuter une évaluation. Les groupes concernés par le mappage de dépendances de groupe ne doivent pas contenir plus de 10 machines.  

## <a name="modify-a-group"></a>Modifier un groupe

1. Dans le projet Azure Migrate, sous **Gérer**, cliquez sur  **Groupes** et sélectionnez le groupe.
2. Sur la page du groupe, cliquez sur  **Afficher les dépendances** pour ouvrir le mappage de dépendances du groupe. 

     ![Afficher le groupe](./media/how-to-create-group-dependencies/create-group.png)

3. Pour afficher des dépendances plus précises, cliquez sur l’intervalle de temps et modifiez-le. Par défaut, il est fixé à une heure. Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.
4. Utilisez la commande Ctrl + clic pour sélectionner des machines sur le mappage. Ajoutez ou supprimez des machines du mappage.
    - Seules les machines qui ont été détectées peuvent être ajoutées.
    - L’ajout et la suppression de machines au sein d’un groupe invalide ses évaluations passées.
    - Vous pouvez, si vous le souhaitez, créer une nouvelle évaluation lorsque vous modifiez le groupe.
5. Cliquez sur **OK** pour enregistrer le groupe.

    ![Ajout et suppression](./media/how-to-create-group-dependencies/add-remove.png)

Si vous souhaitez vérifier les dépendances d’une machine spécifique qui s’affichent dans le mappage de dépendances de groupe, [configurez le mappage de dépendances de la machine](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
