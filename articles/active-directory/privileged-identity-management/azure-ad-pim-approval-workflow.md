---
title: "Flux de travail d’approbation Azure Privileged Identity Management (PIM) | Microsoft Docs"
description: "En savoir plus sur les flux de travail d’approbation dans Privileged Identity Management (PIM)"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 90aec90edcbbfb7833efd85752afc8f1937f5b5a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="approvals"></a>Approbations

## <a name="overview"></a>Vue d'ensemble

Grâce aux approbations Privileged Identity Management, vous pouvez configurer des rôles afin de demander une approbation d’activation, et choisir un ou plusieurs utilisateurs ou groupes comme approbateurs délégués. Poursuivez pour découvrir comment configurer des rôles et sélectionner des approbateurs.


## <a name="new-terminology"></a>Nouvelle terminologie

*Utilisateur de rôle éligible* : un utilisateur de rôle éligible est un utilisateur au sein de votre organisation affecté à un rôle Azure AD comme éligible (le rôle requiert une activation).

*Approbateur délégué* : un approbateur délégué est une ou plusieurs personnes ou groupes au sein de votre Azure AD responsables de l’approbation des demandes d’activation de rôles.

## <a name="scenarios"></a>Scénarios

La version préliminaire privée prend en charge les scénarios suivants :

**En tant qu’Administrateur de rôle privilégié (PRA), vous pouvez :**

-   [activer l’approbation pour des rôles spécifiques](#enable-approval-for-specific-roles)

-   [spécifier les utilisateurs et/ou groupes approbateurs pour approuver des demandes](#specify-approver-users-and/or-groups-to-approve-requests)

-   [afficher l’historique des demandes et approbations pour tous les rôles privilégiés](#view-request-and-approval-history-for-all-privileged-roles)

**En tant d’approbateur désigné, vous pouvez :**

-   [afficher les approbations (demandes) en attente](#view-pending-approvals-requests)

-   [approuver ou rejeter des demandes d’élévation de rôle (unique et/ou en bloc)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [justifier mon approbation/rejet](#provide-justification-for-my-approval/rejection) 

**En tant qu’un utilisateur de rôle éligible, vous pouvez :**

-   [demander l’activation d’un rôle qui nécessite une approbation](#request-activation-of-a-role-that-requires-approval)

-   [afficher l’état de votre demande d’activation](#view-the-status-of-your-request-to-activate)

-   [exécuter la tâche dans Azure AD si l’activation a été approuvée](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigation

Nous avons mis à jour la navigation pour prendre en charge les approbations

![](media/azure-ad-pim-approval-workflow/image001.png)

La page d’accueil par défaut permet d’accéder facilement aux informations sur PIM et à la documentation des nouvelles approbations.

![](media/azure-ad-pim-approval-workflow/image002.png)

Nous avons également ajouté une nouvelle section pour tous les utilisateurs de PIM, « Mon historique d’audit ». Vous y trouverez toutes les informations relatives à votre identité. Celles-ci regroupent dans un emplacement pratique toutes vos demandes en attente et terminées, les décisions que vous avez prises sur les demandes que vous résolvez, et toutes vos activations de rôle passées.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Activer l’approbation pour des rôles spécifiques

Pour activer l’approbation pour un rôle spécifique, sélectionnez tout d’abord des rôles d’annuaire dans la navigation de gauche.

![](media/azure-ad-pim-approval-workflow/image004.png)

Rechercher et sélectionner des paramètres dans la navigation de gauche des rôles d’annuaire

![](media/azure-ad-pim-approval-workflow/image006.png)

Sélectionner des rôles privilégiés :

![](media/azure-ad-pim-approval-workflow/image009.png)

Sélectionnez « Activer » dans la section d’approbation Exiger :

![](media/azure-ad-pim-approval-workflow/image011.png)

Une fois activé, le panneau est développé pour afficher les détails suivants :

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Si vous ne spécifiez PAS d’approbateurs, les PRA deviennent les approbateurs par défaut. Les PRA doivent approuver TOUTES les demandes d’activation pour ce rôle.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Spécifier les utilisateurs et/ou groupes approbateurs pour approuver des demandes

Pour déléguer l’approbation, cliquez sur l’option « Sélectionner des approbateurs » :

![](media/azure-ad-pim-approval-workflow/image015.png)

Dans le panneau Sélectionner des approbateurs qui s’ouvre, vous pouvez rechercher un utilisateur ou groupe spécifique à l’aide de la barre de recherche située en haut, ou en sélectionnant dans la liste préremplie, puis cliquer sur « Sélectionner » lorsque vous avez terminé :

![](media/azure-ad-pim-approval-workflow/image017.png)

Remarque : vous pouvez sélectionner plusieurs utilisateurs ou groupes à la fois.

Votre sélection apparaît dans la liste des approbateurs sélectionnés comme indiqué ci-dessous :

![](media/azure-ad-pim-approval-workflow/image019.png)

Pour supprimer un approbateur, cliquez simplement sur le bouton Supprimer en regard de son nom.

Pour ajouter des approbateurs supplémentaires, répétez le processus.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Afficher l’historique des demandes et approbations pour tous les rôles privilégiés

Pour afficher l’historique des demandes et approbations pour tous les rôles privilégiés, sélectionnez Historique d’audit dans le tableau de bord :

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Vous pouvez trier les données par Action, puis recherchez « Activation approuvée »

### <a name="view-pending-approvals-requests"></a>Afficher les approbations (demandes) en attente

En tant qu’approbateur délégué, vous recevez une notification par courrier électronique lorsqu’une demande est en attente d’approbation. Pour afficher ces requêtes dans le portail PIM, dans le tableau de bord (dans la nouvelle navigation), sélectionnez l’onglet « Demandes d’approbation en attente » dans la barre de navigation de gauche.

![](media/azure-ad-pim-approval-workflow/image023.png)

Une liste des demandes d’approbation en attente s’affiche ici :

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Approuver ou rejeter des demandes d’élévation de rôle (unique et/ou en bloc)

Sélectionnez les demandes que vous souhaitez approuver ou refuser, puis cliquez sur le bouton dans la barre d’action qui correspond à votre décision :

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Justifier mon approbation/rejet

Un nouveau panneau s’ouvre, dans lequel vous pouvez approuver ou refuser plusieurs demandes simultanément. Entrez une justification de votre choix, puis cliquez sur Approuver (ou Refuser) au bas du panneau :

![](media/azure-ad-pim-approval-workflow/image029.png)

Une fois le processus de demande terminé, le symbole d’état reflète la décision que vous avez prise (dans cet exemple, Approuver) :

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Demander l’activation d’un rôle qui nécessite une approbation

La demande d’activation d’un rôle qui nécessite une approbation peut être effectuée dans l’ancienne navigation de PIM ou dans la nouvelle, car le processus d’activation d’un rôle est identique. Sélectionnez simplement un rôle dans la liste des rôles à activer :

![](media/azure-ad-pim-approval-workflow/image033.png)

Si un rôle de privilège requiert Multi-Factor Authentication, vous êtes invité à effectuer la tâche suivante en premier :

![](media/azure-ad-pim-approval-workflow/image035.png)

Lorsque vous avez terminé, cliquez sur Activer et entrez une justification (si nécessaire) :

![](media/azure-ad-pim-approval-workflow/image037.png)

Une notification indiquant que la demande est en attente d’approbation est envoyée au demandeur :

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Afficher l’état de votre demande d’activation

L’état d’une demande d’activation en attente doit être consulté à partir de la nouvelle navigation. Dans la barre de navigation de gauche, sélectionnez l’onglet « My Requests » (Mes demandes) :

![](media/azure-ad-pim-approval-workflow/image041.png)

L’état de la demande par défaut est « En attente », mais vous pouvez basculer pour afficher toutes les demandes ou les demandes refusées.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Exécuter la tâche dans Azure AD si l’activation a été approuvée

Une fois la demande approuvée, le rôle est actif et vous pouvez effectuer les tâches qui requièrent ce rôle.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Étapes suivantes

Vos commentaires sont précieux pour nous. N’hésitez pas à nous faire part ici de vos commentaires !
