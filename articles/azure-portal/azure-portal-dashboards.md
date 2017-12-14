---
title: "Créer et partager des tableaux de bord du portail Azure | Documents Microsoft"
description: "Cet article vous explique comment créer et modifier des tableaux de bord dans le portail Azure."
services: azure-portal
documentationcenter: 
author: sewatson
manager: timlt
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 7f90c882285170bac34bfe020831d3ac04010a11
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Créer et partager des tableaux de bord dans le portail Azure
Vous pouvez créer plusieurs tableaux de bord et les partager avec d’autres personnes ayant accès à vos abonnements Azure.  Cet article présente les principes fondamentaux de création, de modification, de publication et de gestion de l’accès aux tableaux de bord.

## <a name="create-a-dashboard"></a>Création d’un tableau de bord
Pour créer un tableau de bord, sélectionnez le bouton **Nouveau tableau de bord** en regard du nom du tableau de bord actuel.  

![créer un tableau de bord](./media/azure-portal-dashboards/new-dashboard.png)

Cette action crée un nouveau tableau de bord privé vide, et vous fait passer en mode de personnalisation. Vous pouvez alors renommer votre tableau de bord et ajouter ou réorganiser les mosaïques.  Dans ce mode, la galerie de mosaïques réductible remplace le menu de navigation gauche.  La galerie de mosaïques vous permet de rechercher des mosaïques pour vos ressources Azure de différentes façons : par [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), par type de ressource, par [balise](../azure-resource-manager/resource-group-using-tags.md) ou par nom de ressource.  

![personnaliser un tableau de bord](./media/azure-portal-dashboards/customize-dashboard.png)

Ajoutez des mosaïques en les faisant glisser et en les déposant sur le tableau de bord, où vous le souhaitez.

Il existe une nouvelle catégorie appelée **Général** , qui regroupe les mosaïques qui ne sont pas associées à une ressource particulière.  Dans cet exemple, nous épinglons la mosaïque Markdown.  Cette mosaïque sert à ajouter du contenu personnalisé à votre tableau de bord.  La mosaïque prend en charge le texte brut, la [syntaxe Markdown](https://daringfireball.net/projects/markdown/syntax)et un ensemble limité de code HTML.  (Pour des raisons de sécurité, vous ne pouvez pas injecter des balises `<script>` ni utiliser certains éléments de style CSS qui risqueraient d’interférer avec le portail.) 

![ajouter markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Modifier un tableau de bord
Après avoir créé votre tableau de bord, vous pouvez épingler des mosaïques à partir de la galerie de mosaïques ou de la représentation des panneaux sous forme de mosaïque. Épinglons la représentation de notre groupe de ressources. Vous pouvez épingler un élément lorsque vous le parcourez ou à partir du volet du groupe de ressources. Ces deux approches entraînent l’épinglage de la représentation du groupe de ressources.

![Épingler au tableau de bord](./media/azure-portal-dashboards/pin-to-dashboard.png)

Une fois l’élément épinglé, il apparaît sur votre tableau de bord.

![afficher le tableau de bord](./media/azure-portal-dashboards/view-dashboard.png)

Maintenant qu’une mosaïque Markdown et un groupe de ressources sont épinglés au tableau de bord, nous pouvons les redimensionner et les réorganiser de manière appropriée.

En pointant et en sélectionnant « ... » ou en cliquant avec le bouton droit sur une mosaïque, vous pouvez voir toutes les commandes contextuelles pour cette mosaïque. Par défaut, deux options sont disponibles :

1. **Détacher du tableau de bord** : supprime la mosaïque du tableau de bord
2. **Personnaliser** : passe en mode de personnalisation

![personnaliser une mosaïque](./media/azure-portal-dashboards/customize-tile.png)

Si vous sélectionnez Personnaliser, vous pouvez redimensionner et réorganiser les mosaïques. Pour redimensionner une mosaïque, sélectionnez la nouvelle taille dans le menu contextuel, comme illustré dans l’image suivante.

![redimensionner une mosaïque](./media/azure-portal-dashboards/resize-tile.png)

Ou, si la mosaïque prend en charge n’importe quelle taille, vous pouvez faire glisser le coin inférieur droit jusqu’à la taille souhaitée.

![redimensionner une mosaïque](./media/azure-portal-dashboards/resize-corner.png)

Après avoir redimensionné les mosaïques, affichez le tableau de bord.

![afficher la mosaïque](./media/azure-portal-dashboards/view-tile.png)

Une fois que vous avez terminé la personnalisation d’un tableau de bord, sélectionnez simplement **Personnalisation terminée** pour quitter le mode de personnalisation ou cliquez avec le bouton droit et sélectionnez **Personnalisation terminée** dans le menu contextuel.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publier un tableau de bord et gérer le contrôle d’accès
Par défaut, lorsque vous créez un tableau de bord, celui-ci est privé, ce qui signifie que vous êtes la seule personne à pouvoir le consulter.  Pour que les autres utilisateurs puissent le voir, utilisez le bouton **Partager** , disponible avec les autres commandes de tableau de bord.

![partager un tableau de bord](./media/azure-portal-dashboards/share-dashboard.png)

Vous êtes invité à choisir un abonnement et un groupe de ressources dans lesquels votre tableau de bord doit être publié. Pour intégrer en toute transparence les tableaux de bord dans l’écosystème, nous avons mis en œuvre des tableaux de bord partagés en tant que ressources Azure (de sorte que vous ne pouvez pas les partager en tapant une adresse de messagerie).  L’accès aux informations affichées par la plupart des mosaïques dans le portail est régi par le [contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md)(RBAC) Azure. Du point de vue du contrôle d’accès, les tableaux de bord partagés sont traités de la même manière qu’une machine virtuelle ou un compte de stockage.  

Supposons que vous disposez d’un abonnement Azure et que les membres de votre équipe ont reçu les rôles de **propriétaire**, **contributeur** ou **lecteur** de l’abonnement.  Les utilisateurs associés au rôle de propriétaire ou de contributeur peuvent répertorier, afficher, créer, modifier ou supprimer des tableaux de bord dans cet abonnement.  Les utilisateurs ayant le rôle de lecteur peuvent répertorier et afficher des tableaux de bord, sans les modifier ou les supprimer.  Les utilisateurs ayant un accès en lecture peuvent apporter des modifications locales à un tableau de bord partagé, mais ne peuvent pas republier ces modifications sur le serveur.  Toutefois, ils peuvent effectuer une copie du tableau de bord pour leur usage personnel.  Comme toujours, les mosaïques individuelles du tableau de bord appliquent leurs propres règles en matière de contrôle d’accès, compte tenu des ressources auxquelles elles correspondent.  

Pour plus de commodité, l’expérience de publication du portail vous guide vers un modèle dans lequel vous placez les tableaux de bord dans un groupe de ressources appelé **tableaux de bord**.  

![publier un tableau de bord](./media/azure-portal-dashboards/publish-dashboard.png)

Vous pouvez également choisir de publier un tableau de bord dans un groupe de ressources donné.  Le contrôle d’accès pour ce tableau de bord correspond au contrôle d’accès pour le groupe de ressources.  Les utilisateurs qui peuvent gérer les ressources de ce groupe de ressources ont également accès aux tableaux de bord.

![publier un tableau de bord dans un groupe de ressources](./media/azure-portal-dashboards/publish-to-resource-group.png)

Une fois votre tableau de bord publié, le volet de contrôle **Partage + accès** s’actualise et affiche des informations sur le tableau de bord publié, notamment un lien permettant de gérer l’accès des utilisateurs au tableau de bord.  Ce lien lance le panneau standard Contrôle d’accès en fonction du rôle, utilisé pour gérer l’accès à toutes les ressources Azure.  Vous pouvez toujours revenir à cette vue en sélectionnant **Partager**.

![gérer le contrôle d’accès](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour gérer les ressources, voir [Gérer les ressources Azure sur le portail](../azure-resource-manager/resource-group-portal.md).
* Pour déployer des ressources, voir [Déployer des ressources à l’aide de modèles Resource Manager et du portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

