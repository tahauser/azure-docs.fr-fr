---
title: "Publier une application managée Azure en utilisant le portail | Microsoft Docs"
description: "Montre comment utiliser le portail Azure pour créer une application managée Azure destinée aux membres de votre organisation."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 764eb479733a7d4acdb6e6c3eee721cb4a161c88
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publier une application du catalogue de services en utilisant le portail Azure

Vous pouvez utiliser le portail Azure pour publier des [applications managées](overview.md) destinées aux membres de votre organisation. Par exemple, un service informatique peut publier des applications managées destinées à contrôler la conformité par rapport aux normes de l’organisation. Ces applications managées sont disponibles dans le catalogue de services, et non dans la Place de marché Azure.

## <a name="prerequisites"></a>Composants requis

Quand vous publiez une application managée, vous spécifiez une identité pour gérer les ressources. Nous vous recommandons de spécifier un groupe d’utilisateurs Azure Active Directory. Pour créer un groupe d’utilisateurs Azure Active Directory, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

Le fichier .zip qui contient la définition d’application managée doit être disponible par le biais d’un URI. Nous vous recommandons de charger votre fichier .zip sur un blob de stockage. 

## <a name="create-managed-application-with-portal"></a>Créer un package d’application gérée avec le portail

1. Dans le coin supérieur gauche, sélectionnez **+ Nouveau**.

   ![Nouveau service](./media/publish-portal/new.png)

1. Recherchez le **catalogue de services**.

1. Dans les résultats, faites défiler jusqu'à l’élément **Application gérée du catalogue de services**. Sélectionnez-le.

   ![Rechercher des définitions d’application gérée](./media/publish-portal/select-managed-apps-definition.png)

1. Sélectionnez **Créer** pour démarrer le processus de création de la définition de l’application gérée.

   ![Créer la définition d’application gérée](./media/publish-portal/create-definition.png)

1. Fournissez des valeurs pour le nom, le nom d’affichage, la description, l’emplacement, l’abonnement et le groupe de ressources. Pour l’URI du fichier de package, indiquez le chemin d’accès au fichier zip que vous avez créé.

   ![Fournir des valeurs](./media/publish-portal/fill-application-values.png)

1. Lorsque vous accédez à la section Authentification et niveau de verrouillage, sélectionnez **Ajouter une autorisation**.

   ![Ajouter une autorisation](./media/publish-portal/add-authorization.png)

1. Choisissez un groupe Azure Active Directory pour gérer les ressources, puis sélectionnez **OK**.

   ![Ajouter un groupe d’autorisations](./media/publish-portal/add-auth-group.png)

1. Après avoir fourni toutes les valeurs, sélectionnez **Créer**.

   ![Créer une application gérée](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](overview.md).
* Pour obtenir des exemples d’applications managées, consultez [Exemples de projets pour des applications managées Azure](sample-projects.md).
* Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).