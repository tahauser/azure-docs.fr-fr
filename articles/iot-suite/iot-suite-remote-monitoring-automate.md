---
title: "Détecter des problèmes d’appareils dans la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment utiliser des règles et des actions pour détecter automatiquement les problèmes d’appareils liés au seuil dans la solution de surveillance à distance."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 9d9fbefd81fed506bcc025fa0f44315ec831cf0d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>Détecter les problèmes à l’aide de règles de seuil

Ce didacticiel montre les fonctionnalités du moteur de règles dans la solution de surveillance à distance. Pour présenter ces fonctionnalités, le didacticiel utilise un scénario dans l’application Contoso IoT.

Contoso a une règle qui génère une alerte critique lorsque la pression signalée par un appareil de **refroidissement** dépasse 250 psi. En tant qu’opérateur, vous souhaitez identifier les appareils de **refroidissement** avec des capteurs défectueux en recherchant des pics de pression initiale. Pour identifier ces appareils, vous créez une règle qui génère un avertissement lorsque la pression dépasse 150 psi.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Afficher les règles dans votre solution
> * Créer une nouvelle règle
> * Modifier une règle existante
> * Supprimer une règle

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez terminer le didacticiel [Déployer la solution de surveillance à distance préconfigurée](iot-suite-remote-monitoring-deploy.md).

## <a name="view-the-rules-in-your-solution"></a>Afficher les règles dans votre solution

La page des **règles et actions** de la solution affiche la liste de toutes les règles en cours :

![Page des règles et actions](media/iot-suite-remote-monitoring-automate/rulesactions.png)

Pour afficher uniquement les règles qui s’appliquent aux appareils de **refroidissement**, appliquez un filtre :

![Filtrer la liste des règles](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

Vous pouvez afficher plus d’informations sur une règle et modifier celle-ci lorsque vous la sélectionnez dans la liste :

![Afficher les détails de la règle](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

Pour désactiver, activer ou supprimer une ou plusieurs règles, sélectionnez des règles dans la liste :

![Sélectionner plusieurs règles](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>Créer une nouvelle règle

Pour ajouter une nouvelle règle qui génère un avertissement lorsque la pression dans un appareil de **refroidissement** dépasse 150 psi, choisissez **Nouvelle règle** :

![Créer une règle](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

Utilisez les valeurs suivantes pour créer la règle :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| NOM             | Avertissement de refroidissement                       |
| Source           | Groupe d’appareils de **refroidissement**             |
| Champ déclencheur    | pressure                              |
| Opérateur du déclencheur | Supérieur à                          |
| Valeur de déclenchement    | 150                                   |
| Niveau de gravité   | Avertissement                               |
| Description      | Pression de refroidissement supérieure à 150 psi |

Pour enregistrer la nouvelle règle, choisissez **Appliquer**.

Vous pouvez connaître le déclenchement de la règle sur la page des **règles et actions** ou **Tableau de bord**.

## <a name="edit-an-existing-rule"></a>Modifier une règle existante

Pour modifier une règle existante, sélectionnez-la dans la liste des règles. Ensuite, dans le panneau **Détail de la règle**, choisissez **Mode d’édition**.

![Modifier une règle](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>Désactiver une règle

Pour désactiver temporairement une règle, vous pouvez la désactiver dans la liste des règles. Sélectionnez la règle à désactiver, puis choisissez **Désactiver**. L’**état** de la règle dans la liste change pour indiquer que la règle est désactivée. Vous pouvez réactiver une règle que vous avez précédemment désactivée à l’aide de la même procédure.

![Désactiver une règle](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

Vous pouvez activer et désactiver plusieurs règles en même temps. Pour cela, vous devez sélectionner plusieurs règles dans la liste.

## <a name="delete-a-rule"></a>Supprimer une règle

Pour supprimer définitivement une règle, sélectionnez-la dans la liste des règles, puis choisissez **Supprimer**.

Vous pouvez supprimer plusieurs règles en même temps. Pour cela, vous devez sélectionner plusieurs règles dans la liste.

## <a name="next-steps"></a>étapes suivantes

Ce didacticiel vous a montré comment effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Afficher les règles dans votre solution
> * Créer une nouvelle règle
> * Modifier une règle existante
> * Supprimer une règle

La détection des problèmes à l’aide de règles de seuil n’ayant plus de secrets pour vous, nous vous suggérons de découvrir les opérations suivantes :

* [Gérer et configurer vos appareils](./iot-suite-remote-monitoring-manage.md)
* [Dépanner et corriger les problèmes d’un appareil](./iot-suite-remote-monitoring-maintain.md)
* [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md)

<!-- Next tutorials in the sequence -->