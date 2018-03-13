---
title: "Didacticiel : Autoriser l’accès dans Azure Cost Management | Microsoft Docs"
description: "Dans ce didacticiel, vous allez apprendre à autoriser l’accès aux données de gestion des coûts avec des comptes d’utilisateur qui définissent les niveaux d’accès aux entités."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0e2edc946c5d6ada1049fbd6a960ec138f7088f2
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-assign-access-to-cost-management-data"></a>Didacticiel : Autoriser l’accès aux données de gestion des coûts

L’accès aux données de gestion des coûts est assuré par une gestion des utilisateurs ou des entités. Les comptes d’utilisateur Cloudyn déterminent l’accès aux *entités* et aux fonctions d’administration. Il existe deux types d’accès : administrateur et utilisateur. Sauf modifié par utilisateur, l’accès administrateur concède à l’utilisateur un accès non restreint à toutes les fonctions du portail Cloudyn, notamment la gestion des utilisateurs, la gestion des listes de destinataires et l’accès aux entités racines pour toutes les données d’entité. L’accès utilisateur permet aux utilisateurs finaux d’afficher et de créer des rapports à travers l’accès dont ils disposent aux données d’entité.

Les entités sont utilisées pour refléter la structure hiérarchique de votre organisation. Elles identifient les services, divisions et équipes de votre organisation dans Cloudyn. La hiérarchie des entités permet de suivre avec précision les dépenses réalisées par les entités.

Quand vous avez inscrit votre contrat ou compte Azure, un compte avec l’autorisation d’administrateur a été créé dans Cloudyn pour vous permettre de réaliser toutes les étapes de ce didacticiel. Ce didacticiel décrit l’accès aux données de gestion des coûts, y compris la gestion des utilisateurs et la gestion des entités. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un utilisateur avec un accès administrateur
> * Créer un utilisateur avec un accès utilisateur
> * Créer des entités

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>configuration requise

- Vous devez disposer d’un compte Azure.
- Vous devez disposer d’une inscription d’évaluation ou d’un abonnement payant pour Azure Cost Management.

## <a name="create-a-user-with-admin-access"></a>Créer un utilisateur avec un accès administrateur

Même si vous disposez déjà d’un accès administrateur, vos collègues de travail au sein de votre organisation devaient peut-être également disposer d’un accès administrateur. Dans le portail Cloudyn, cliquez sur le symbole d’engrenage dans le coin supérieur droit et sélectionnez **User Management (Gestion des utilisateurs)**. Cliquez sur **Add New User (Ajouter un nouvel utilisateur)** pour ajouter un utilisateur.

Entrez les informations requises sur l’utilisateur. Vous pouvez laisser le champ du mot de passe vide pour permettre à l’utilisateur d’en définir un nouveau lors de sa première connexion. Cloudyn envoie un lien avec les informations de connexion par e-mail à l’utilisateur quand vous sélectionnez **Notify user by email (Notifier l'utilisateur par e-mail)**. Sélectionnez des autorisations de gestion des utilisateurs pour permettre à l’utilisateur de créer et modifier d’autres utilisateurs. L’option de gestion des listes de destinataires permet à l’utilisateur de modifier les listes de destinataires.

Sous **User has admin access (L’utilisateur a un accès administrateur)**, l’entité racine de votre organisation est sélectionnée. Laissez-la sélectionnée et enregistrez les informations utilisateur. La sélection de l’entité racine permet à l’utilisateur de disposer de l’autorisation d’administrateur non seulement pour l’entité racine dans l’arborescence, mais également pour toutes les entités qui se trouvent sous cette entité.  
  ![ajouter un nouvel utilisateur avec un accès administrateur](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Créer un utilisateur avec un accès utilisateur
En principe, les utilisateurs ayant besoin d’accéder à des données de gestion des coûts, telles que des tableaux de bord et des rapports, devraient disposer d’un accès utilisateur pour pouvoir les afficher. Créez un nouvel utilisateur avec un accès utilisateur similaire à celui que vous avez créé avec l’accès administrateur, en tenant compte des différences suivantes :

- Décochez les cases **Allow User Management (Autoriser la gestion des utilisateurs)** et **Allow Recipient lists Management (Autoriser la gestion des listes de destinataires)**, et décochez tout dans la liste **User has admin access (L’utilisateur a un accès administrateur)**.
- Sélectionnez les entités auxquelles l’utilisateur a besoin d’accéder dans la liste **User has admin access (L’utilisateur a un accès administrateur)**.
- Vous pouvez également autoriser l’administrateur à accéder à des entités spécifiques, selon les besoins.

![ajouter un nouvel utilisateur avec un accès utilisateur](.\media\tutorial-user-access\new-user-access.png)

Pour visionner un didacticiel vidéo sur l’ajout d’utilisateurs, consultez [Ajout d’utilisateurs à la gestion des coûts Azure de Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="create-entities"></a>Créer des entités

Quand vous définissez la hiérarchie des entités de coût, une meilleure pratique consiste à identifier la structure de votre organisation.

Au fur et à mesure que vous créez l’arborescence, réfléchissez à la façon dont vous souhaitez ou devez afficher leurs coûts ventilés par unités commerciales, centres de coûts, environnements et services des ventes. L’arborescence des entités dans Cloudyn est flexible en raison de l’héritage des entités. Les abonnements individuels à vos comptes cloud sont liés à des entités spécifiques. Les entités sont par conséquent mutualisées. Vous pouvez autoriser des utilisateurs spécifiques à accéder uniquement à leur branche de l’entreprise à l’aide d’entités. Cela permet d’isoler les données, même à travers de grandes divisions d’une entreprise, comme des filiales. L’isolement des données facilite en outre la gouvernance.  

Quand vous avez inscrit votre contrat ou compte Azure sur Cloudyn, vos données de ressources Azure, notamment les données d’utilisation, de performances, de facturation et de balise de vos abonnements ont été copiées dans votre compte Cloudyn. Vous devez toutefois créer manuellement l’arborescence des entités. Si vous avez ignoré l’inscription à Azure Resource Manager, seules les données de facturation et quelques rapports de ressources sont disponibles dans le portail Cloudyn.

Dans le portail Cloudyn, cliquez sur **Settings (Paramètres)** dans le coin supérieur droit et sélectionnez **Cloud Accounts (comptes cloud)**. Vous commencez par une seule entité (racine), puis vous créez l’arborescence des entités sous la racine. Voici un exemple de hiérarchie d’entités similaire à celle de nombreuses sociétés informatiques, une fois l’arborescence créée :

![arborescence des entités](.\media\tutorial-user-access\entity-tree.png)

En regard de **Entities (Entités)**, cliquez sur **Add Entity (Ajouter une entité)**. Entrez les informations relatives à la personne ou le service que vous souhaitez ajouter. Les champs **Full Name (Nom complet)** et **Email (E-mail)** ne doivent pas correspondre à des utilisateurs existants. Si vous souhaitez afficher la liste des niveaux d’accès, recherchez dans l’aide *Adding an entity (Ajout d’une entité)*.

![ajouter une entité](.\media\tutorial-user-access\add-entity.png)

Quand vous avez terminé, **enregistrez** l’entité.


Pour visionner un didacticiel vidéo sur la création d’une hiérarchie d’entités de coût, consultez [Création d’une hiérarchie d’entités de coût dans la gestion des coûts Azure de Cloudyn](https://youtu.be/dAd9G7u0FmU).

Si vous êtes un utilisateur Azure Enterprise Agreement, regardez un didacticiel vidéo sur l’association des comptes et des abonnements à des entités à l’adresse [Connexion à Azure Resource Manager avec la gestion des coûts Azure de Cloudyn](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un utilisateur avec un accès administrateur
> * Créer un utilisateur avec un accès utilisateur
> * Créer des entités

Si vous n’avez pas déjà activé l’accès de l’API Azure Resource Manager pour vos comptes, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Activer des abonnements et comptes Azure](activate-subs-accounts.md)
