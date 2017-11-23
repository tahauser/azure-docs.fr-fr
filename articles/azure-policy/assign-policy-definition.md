---
title: "Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure | Microsoft Docs"
description: "Cet article vous guide à travers les étapes de création d’une définition de stratégie pour identifier les ressources non conformes."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: a1b2f72cf33c740a52cf817766e9950a04886141
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure
La première étape pour comprendre la conformité dans Azure est de savoir où vous en êtes avec vos propres ressources actuelles. Ce démarrage rapide vous guide pas à pas dans le processus de création d’une affectation de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques gérés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques gérés et qui sont donc *non conformes*.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="opt-in-to-azure-policy"></a>Choisir Azure Policy

Azure Policy est désormais disponible en préversion publique et vous devez vous inscrire pour en demander l’accès.

1. Accédez à Azure Policy sur https://aka.ms/getpolicy et sélectionnez **S’inscrire** dans le volet gauche.

   ![Rechercher une stratégie](media/assign-policy-definition/sign-up.png)

2. Choisissez Azure Policy en sélectionnant les abonnements que vous voulez utiliser dans la liste **Abonnement**. Sélectionnez ensuite **Inscription**.

   ![Accepter l’utilisation d’Azure Policy](media/assign-policy-definition/preview-opt-in.png)

   Votre requête est approuvée automatiquement pour la préversion. Patientez 30 minutes pour que le système traite votre inscription.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, nous créons une affectation de stratégie et attribuons la définition de stratégie *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*.

1. Sélectionnez **Attributions** dans le volet gauche de la page Azure Policy.
2. Sélectionnez **Assigner une stratégie** en haut du volet **Affectations**.

   ![Affecter une définition de stratégie](media/assign-policy-definition/select-assign-policy.png)

3. Dans la page **Assigner une stratégie**, cliquez sur le ![bouton de définition de stratégie](media/assign-policy-definition/definitions-button.png) situé en regard du champ **Stratégie** pour afficher la liste des définitions disponibles.

   ![Afficher les définitions de stratégie disponibles](media/assign-policy-definition/open-policy-definitions.png)

   Azure Policy est fourni avec des définitions de stratégie intégrées que vous pouvez utiliser. Vous y trouverez des définitions de stratégie intégrées comme :

   - Enforce tag and its value
   - Apply tag and its value
   - Require SQL Server version 12.0

4. Parcourez vos définitions de stratégie pour rechercher la définition *Audit VMs that do not use managed disks (Auditer des machines virtuelles qui n’utilisent pas de disques gérés)*. Cliquez sur cette stratégie, puis sur **Affecter**.

   ![Rechercher la définition de stratégie appropriée](media/assign-policy-definition/select-available-definition.png)

5. Attribuez un **Nom** d’affichage à l’affectation de stratégie. Dans ce cas, nous allons utiliser la définition *Audit VMs that do not use managed disks (Auditer des machines virtuelles qui n’utilisent pas de disques gérés)*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur la façon dont cette affectation de stratégie identifie toutes les machines virtuelles créées dans cet environnement qui n’utilisent pas de disques gérés.
6. Définissez le niveau tarifaire sur **Standard** pour vous assurer que la stratégie soit appliquée aux ressources existantes.

   Il existe deux niveaux tarifaires dans Azure Policy : *Gratuit* et *Standard*. Avec le niveau Gratuit, vous pouvez uniquement appliquer des stratégies à des ressources futures, alors qu’avec le niveau Standard, vous pouvez également les appliquer à des ressources existantes pour mieux comprendre votre état de conformité. Étant donné qu’il s’agit d’une préversion limitée, nous n'avons pas encore publié de modèle tarifaire. Vous ne recevrez donc pas de facture pour la sélection du niveau *Standard*. Pour en savoir plus sur les prix, consultez : [Prix Azure Policy](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

7. Sélectionnez l’**étendue** d’application souhaitée de la stratégie.  Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources.
8. Sélectionnez l’abonnement (ou le groupe de ressources) enregistré précédemment quand vous avez choisi Azure Policy. Dans cet exemple, nous utilisons l’abonnement suivant - **Azure Analytics Capacity Dev**, mais vos options peuvent différer.

   ![Rechercher la définition de stratégie appropriée](media/assign-policy-definition/assign-policy.png)

9. Sélectionnez **Attribuer**.

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Sélectionnez **Conformité** dans le volet gauche, puis recherchez l’affectation de stratégie que vous avez créée.

![Conformité à la stratégie](media/assign-policy-definition/policy-compliance.png)

Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous l’onglet **Ressources non conformes**.

Si une condition est évaluée sur vos ressources existantes et génère la valeur true pour certaines d'entre elles, ces ressources sont marquées comme non conformes à la stratégie. Le tableau suivant montre le fonctionnement des différentes actions actuellement disponibles avec le résultat de l’évaluation de conditions et l’état de conformité de vos ressources.

|Ressource  |Si la condition dans la stratégie a la valeur  |Action dans la stratégie   |État de conformité  |
|-----------|---------|---------|---------|
|Exists     |true     |REFUSER     |Non conforme |
|Exists     |False    |REFUSER     |Conforme     |
|Exists     |true     |Append   |Non conforme |
|Exists     |False    |Append   |Conforme     |
|Exists     |true     |Audit    |Non conforme |
|Exists     |False    |Audit    |Non conforme |

## <a name="clean-up-resources"></a>Supprimer des ressources

D’autres guides de cette collection sont basés sur ce démarrage rapide. Si vous prévoyez de continuer avec les didacticiels suivants, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.
1. Sélectionnez **Attributions** dans le volet gauche.
2. Recherchez l’affectation que vous venez de créer.

   ![Supprimer une affectation](media/assign-policy-definition/delete-assignment.png)

3.  Sélectionnez **Supprimer l’affectation**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie à une étendue pour vous assurer que toutes les ressources incluses dans cette étendue soient compatibles et pour identifier celles qui ne le sont pas.

Pour plus d’informations sur l’affectation de stratégies et garantir que les ressources **futures** qui sont créées sont conformes, continuez avec le didacticiel suivant :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./create-manage-policy.md)
