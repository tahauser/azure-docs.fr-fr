---
title: "Vue d’ensemble du portail Azure | Microsoft Docs"
description: "Apprenez à utiliser le portail Microsoft Azure."
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/16/2015
ms.author: dwrede
ms.openlocfilehash: 45decfdec01b7086d1f9d18b31cf01cec1adb34d
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="microsoft-azure-portal-overview"></a>Présentation du portail Microsoft Azure
Le portail Microsoft Azure permet de configurer et de gérer l’ensemble de vos ressources Azure.  Ce didacticiel va vous permettre de vous familiariser avec le portail et vous montrer comment utiliser certaines des fonctionnalités clés suivantes :

* Un **marketplace complet** permettant de parcourir des milliers d’articles de Microsoft et d’autres fournisseurs qui peuvent être achetés et/ou mis en service.
* Une **expérience de navigation unifiée et évolutive** qui permet de trouver facilement les ressources qui vous intéressent et d’effectuer diverses opérations de gestion.
* **pages de gestion cohérente** (ou panneaux) qui permettent de gérer de nombreux services Azure grâce à un affichage cohérent des paramètres, des actions, des informations de facturation, des données d’utilisation et d’analyse d’intégrité, et bien plus encore.
* Une **expérience personnelle** qui vous permet de créer un écran d’accueil personnalisé avec les informations que vous voulez voir chaque fois que vous vous connectez.  Vous pouvez également personnaliser n'importe quel panneau contenant des vignettes.
  
  ![Navigation au sein de l'interface utilisateur du portail Azure][UIOrientation]

## <a name="before-you-get-started"></a>Avant de commencer
Vous aurez besoin d'un abonnement Azure valide pour suivre ce didacticiel.  Si vous n'en avez pas, [inscrivez-vous pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/) dès aujourd'hui.  Une fois abonné, vous pourrez accéder au portail à l’adresse <https://portal.azure.com>.

## <a name="how-to-create-a-resource"></a>Comment créer une ressource
Azure possède un marketplace proposant des milliers d'articles que vous pouvez créer depuis un seul et même endroit.  Supposons que vous vouliez créer une machine virtuelle Windows Server 2012.  Le hub +Nouveau constitue le point d'entrée des différentes catégories proposées par le marketplace.  Chaque catégorie comporte un petit groupe d'articles proposés avec un lien vers l'intégralité du marketplace où vous trouverez toutes les catégories et pourrez effectuer vos recherches. Pour créer cette nouvelle machine virtuelle Windows Server 2012, effectuez les actions suivantes :  

1. Windows Server 2012 est proposé, vous pouvez donc le sélectionner dans la catégorie Calcul.  
2. Saisissez les informations de base dans un formulaire.
3. Cliquez sur « Créer ». Votre machine virtuelle sera immédiatement configurée.

L'unité Notification Hub vous préviendra une fois votre ressource créée et un panneau de gestion s'ouvrira (vous pourrez toujours accéder à ces ressources plus tard).

![Catégories du portail][PortalCategories]

## <a name="how-to-find-your-resources"></a>Comment trouver vos ressources
Vous pouvez épingler à votre tableau d'accueil les ressources que vous utilisez fréquemment. Toutefois, il peut arriver que vous ayez besoin d'accéder à une ressource que vous n'utilisez pas souvent.  Le hub Parcourir ci-dessous vous permet d'accéder à toutes vos ressources.  Vous pouvez filtrer par abonnement, sélectionner ou redimensionner des colonnes et accéder aux panneaux de gestion en cliquant sur des éléments individuels.

![Parcourir le Hub][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Comment gérer une ressource et déléguer son accès
Depuis ce panneau, vous pouvez vous connecter à la machine virtuelle à l'aide du Bureau à distance, surveiller les mesures de performances clés, contrôler l'accès à la machine virtuelle à l'aide du contrôle d'accès en fonction du rôle (RBAC), configurer la machine virtuelle et effectuer d'autres tâches de gestion importantes.  La délégation de l'accès basé sur le rôle est essentielle à la gestion à grande échelle.  Cliquez [ici](active-directory/role-based-access-control-configure.md) pour en savoir plus à ce sujet. Pour déléguer l'accès à une ressource, effectuez les actions suivantes :

1. Accédez à votre ressource.
2. Cliquez sur Tous les paramètres dans la section Essentials.
3. Cliquez sur Utilisateurs dans la liste des paramètres.
4. Cliquez sur Ajouter dans la barre de commandes.
5. Choisissez un utilisateur et un rôle.

![Gestion d'une ressource][ManageResource]

## <a name="how-to-get-help"></a>Comment obtenir de l'aide
Si vous rencontrez un problème, nous sommes là pour vous.  Le portail comporte une page d'aide qui peut vous aider à trouver la solution appropriée.  En fonction de votre [plan de support](https://azure.microsoft.com/support/plans/), vous pouvez également créer des tickets de support directement depuis le portail.  Après avoir créé un ticket de support, vous pouvez gérer son cycle de vie depuis le portail. Vous pouvez accéder aux pages d'aide en cliquant sur Parcourir -> Aide + support.  

![Aide et support][HelpSupport]

## <a name="summary"></a>Résumé
Passons en revue ce que vous avez appris dans ce didacticiel :

* Vous avez appris comment vous inscrire, obtenir un abonnement et accéder au portail
* Vous vous êtes familiarisé avec l’interface utilisateur du portail et vous avez appris à créer des ressources et à les parcourir
* Vous avez appris à créer une ressource et à parcourir les ressources
* Vous avez découvert la structure ou les panneaux de gestion et comment gérer de façon cohérente différents types de ressources
* Vous avez appris à personnaliser le portail pour afficher les informations importantes au premier plan
* Vous avez appris à contrôler l’accès aux ressources à l’aide de l’accès en fonction du rôle (RBAC)
* Vous avez appris comment obtenir de l’aide et un support

Le portail Microsoft Azure simplifie grandement la création et la gestion de vos applications dans le cloud.  Jetez un œil au [blog de gestion](https://azure.microsoft.com/blog/topics/management/) pour vous tenir informé, car nous [prenons en compte les commentaires](https://feedback.azure.com/forums/223579-azure-preview-portal/) et apportons les améliorations nécessaires.  [ScottGu's blog](http://weblogs.asp.net/scottgu) fournit lui aussi toutes les mises à jour Azure disponibles.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
