---
title: Publier une annonce dans un lab avec Azure DevTest Labs | Microsoft Docs
description: "Découvrez comment ajouter une annonce à un lab dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/29/2018
ms.author: v-craic
ms.openlocfilehash: 99b0938d5f4c8b022ead3473a0367de5d75cd6ff
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publier une annonce dans un lab avec Azure DevTest Labs

En tant qu’administrateur de lab, vous pouvez publier une annonce personnalisée dans un lab existant pour informer les utilisateurs des dernières modifications ou ajouts apportés au lab. Par exemple, pour les renseigner sur :

- Les nouvelles tailles de machine virtuelle disponibles
- Les images actuellement inutilisables
- Les mises à jour des stratégies de lab

Une fois publiée, l’annonce s’affiche dans la page Vue d’ensemble du lab, et l’utilisateur peut la sélectionner pour obtenir plus de détails.

La fonctionnalité d’annonce est destinée à être utilisée pour les notifications temporaires.  Vous pouvez facilement désactiver une annonce lorsqu’elle n’est plus nécessaire.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Étapes de la publication d’une annonce dans un lab existant

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Si nécessaire, sélectionnez **Autres services**, puis **DevTest Labs** dans la liste. (Votre lab peut déjà être affiché dans le Tableau de bord sous **Toutes les ressources**).
1. Dans la liste des labs, sélectionnez le lab dans lequel vous souhaitez publier une annonce.  
1. Dans la zone **Vue d’ensemble** du laboratoire, sélectionnez **Configuration et stratégies**.  

    ![Bouton Configuration et stratégies](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Sur la gauche, sous **PARAMÈTRES**, sélectionnez **Annonce du lab**.

    ![Bouton Annonce du lab](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Pour créer un message à l’intention des utilisateurs de ce lab, définissez **Activé** sur **Oui**.

1. Vous pouvez entrer une **Date d’expiration** pour spécifier la date et l’heure après lesquelles l’annonce ne sera plus présentée aux utilisateurs. À défaut, l’annonce restera jusqu'à ce que vous la désactiviez.

   > [!NOTE]
   > Après expiration, l’annonce ne sera plus présentée aux utilisateurs, mais elle subsistera toujours dans le volet **Annonce du labo**. Vous pourrez lui apporter des modifications et la réactiver.
   >
   >

1. Entrez un **Titre de l’annonce** et le **Texte de l’annonce**.

   Le titre peut comporter jusqu’à 100 caractères, et il est visible par l’utilisateur dans la page Vue d’ensemble du lab. Si l’utilisateur sélectionne le titre, le texte de l’annonce s’affiche.

   Le texte de l’annonce accepte Markdown. Au fur et à mesure que vous entrez le texte de l’annonce, vous pouvez voir le message s’afficher dans la zone Aperçu en bas de l’écran.

    ![Écran d’annonce du lab pour créer le message.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Sélectionnez **Enregistrer** lorsque l’annonce est prête pour la publication.

Quand vous ne souhaitez plus afficher cette annonce pour les utilisateurs du lab, revenez à la page **Annonce du lab** et définissez **Activé** sur **Non**. Si vous avez spécifié une date d’expiration, l’annonce sera automatiquement désactivée à la date et à l’heure correspondantes.

## <a name="steps-for-users-to-view-an-announcement"></a>Étapes de l’affichage d’une annonce pour la consultation par les utilisateurs

1. À partir du [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), sélectionnez un lab.

1. Si une annonce est publiée pour ce lab, un avis s’affiche en haut de la page Vue d’ensemble du lab. Cet avis est le titre de l’annonce qui a été spécifié lors de la création de l’annonce.

    ![Annonce du lab dans la page Vue d’ensemble](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. L’utilisateur peut sélectionner le message pour afficher l’annonce entière.

    ![Informations supplémentaires pour l’annonce du lab](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
* Si vous modifiez ou définissez une stratégie de lab, vous avez peut-être envie de publier une annonce pour en informer les utilisateurs. La page [Définir des stratégies et des planifications](devtest-lab-set-lab-policy.md) vous renseigne sur l’application de conventions et de restrictions sur votre abonnement à l’aide de stratégies personnalisées.
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
