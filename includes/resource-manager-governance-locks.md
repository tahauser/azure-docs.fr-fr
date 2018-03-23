---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
Les verrous de ressources empêchent les utilisateurs de votre organisation de supprimer ou de modifier accidentellement des ressources critiques. Contrairement au contrôle d’accès basé sur les rôles, les verrous de ressources permettent d’appliquer une restriction à tous les utilisateurs et rôles. 

Vous pouvez définir le niveau de verrouillage sur **CanNotDelete** ou **ReadOnly**. Dans le portail, les niveaux des verrous sont appelés **Supprimer** et **Lecture seule** respectivement.

* **CanNotDelete** signifie que les utilisateurs autorisés peuvent lire et modifier une ressource, mais pas la supprimer. 
* **ReadOnly** signifie que les utilisateurs autorisés peuvent lire une ressource, mais pas la supprimer ni la mettre à jour. Appliquer ce verrou revient à limiter à tous les utilisateurs autorisés les autorisations accordées par le rôle **Lecteur**. 

> [!TIP]
> Soyez prudent lorsque vous appliquez un verrou **ReadOnly**. Certaines opérations semblables à des opérations de lecture nécessitent en fait des actions supplémentaires. Par exemple, un verrou **ReadOnly** sur un compte de stockage empêche tous les utilisateurs de répertorier les clés. L’opération de listage de clés est gérée via une demande POST, car les clés retournées sont disponibles pour les opérations d’écriture. Un verrou **ReadOnly** sur une ressource App Service empêche l’Explorateur de serveurs Visual Studio d’afficher les fichiers de la ressource, car cette interaction requiert un accès en écriture.

Lorsque vous appliquez un verrou à une étendue parente, toutes les ressources de cette étendue héritent du même verrou. Même les ressources que vous ajoutez par la suite héritent du verrou du parent. Le verrou le plus restrictif de l’héritage est prioritaire.

Les verrous Resource Manager s'appliquent uniquement aux opérations qui se produisent dans le plan de gestion, c'est-à-dire les opérations envoyées à `https://management.azure.com`. Les verrous ne limitent pas la manière dont les ressources traitent leurs propres fonctions. Les modifications des ressources sont limitées, mais pas les opérations sur les ressources. Par exemple, un verrou ReadOnly une base de données SQL empêche la suppression ou la modification de la base de données. Il ne vous empêche pas de créer, de mettre à jour ou de supprimer des données dans la base de données. Les transactions de données sont autorisées, car ces opérations ne sont pas envoyées à `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Personnes autorisées à créer ou supprimer des verrous dans votre organisation
Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions `Microsoft.Authorization/locks/*`. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions.
