---
title: "Publier les versions d’une API à l’aide de Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour apprendre à publier plusieurs versions dans Gestion des API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d63bdd3110f5c5db3e7bfec424644fdbc8d8d90c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="publish-multiple-versions-of-your-api"></a>Publier plusieurs versions de votre API 

Il est parfois peu pratique que tous les appelants de votre API utilisent exactement la même version. Parfois, vous souhaitez publier de nouvelles fonctionnalités ou des fonctionnalités différentes pour certains utilisateurs, tandis que d’autres veulent rester sur l’API qui fonctionne actuellement pour eux. Lorsque des appelants souhaitent effectuer une mise à niveau vers une version ultérieure, ils veulent être en mesure de le faire à l’aide d’une approche facile à comprendre.  Cela est possible en utilisant les **versions** dans Gestion des API Azure. Pour plus d’informations, consultez [Versions et révisions](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une nouvelle version à une API existante
> * Choisir un schéma de version
> * Ajouter la version à un produit
> * Parcourir le portail des développeurs pour afficher la version

![Version indiquée sur le portail des développeurs](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Prérequis

+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-version"></a>Ajouter une nouvelle version

![Menu contextuel de l’API : ajouter une version](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Sélectionnez **API de conférence de démonstration** dans la liste des API.
2. Sélectionnez le menu contextuel (**...** ) en regard de l’API.
3. Sélectionnez **+ Ajouter une version**.

    > [!TIP]
    > Il est également possible d’activer des versions lorsque vous créez une nouvelle API : sélectionnez **Créer une version pour cette API ?** sur l’écran **Ajouter une API**.

## <a name="choose-a-versioning-scheme"></a>Choisir un schéma de gestion de versions

Gestion des API Azure vous permet de choisir la manière selon laquelle vous autorisez les appelants à spécifier la version de votre API qu’ils souhaitent. Pour cela, vous devez choisir un **schéma de gestion de versions**. Ce schéma peut être un **chemin d’accès, un en-tête ou une chaîne de requête**. Dans notre exemple, nous utilisons le chemin.

![Écran Ajouter une version](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Laissez le **chemin** sélectionné comme **schéma de gestion de versions**.
2. Ajoutez **v1** comme **identificateur de version**.

    > [!TIP]
    > Si vous sélectionnez **en-tête** ou **chaîne de requête** comme schéma de gestion de versions, vous devez fournir une valeur supplémentaire : le nom du paramètre d’en-tête ou de chaîne de requête.

3. Ajoutez la description de votre choix.
4. Sélectionnez **Créer** pour configurer votre nouvelle version.
5. Sous **API Big Conference** dans la liste des API, deux API distinctes s’affichent maintenant : **Original** et **v1**.

    ![Versions répertoriées sous une API dans le portail Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Si vous ajoutez une version à une API sans version, nous créons toujours pour vous une API **Original** qui répond sur l’URL par défaut. Cela permet aux appelants existants de ne pas être interrompus par le processus d’ajout d’une version. Si vous créez une nouvelle API avec des versions activées au démarrage, une API Original n’est pas créée.

6. Vous pouvez maintenant modifier et configurer **v1** en tant qu’API distincte de l’API **Original**. Les modifications apportées à une version n’affectent pas l’autre.

## <a name="add-the-version-to-a-product"></a>Ajouter la version à un produit

Pour que les appelants voient votre nouvelle version, celle-ci doit être ajoutée à un **produit** (les produits ne sont pas hérités des versions parentes).

1. Sélectionnez **Produits** sur la page de gestion des services.
2. Sélectionnez **Illimité**.
3. Sélectionnez **API**.
4. Sélectionnez **Ajouter**.
5. Sélectionnez **Conference API, Version v1**.
6. Revenez à la page de gestion des services et sélectionnez **API**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Parcourir le portail des développeurs pour afficher la version

1. Sélectionnez **Portail des développeurs** dans le menu supérieur.
2. Sélectionnez **API**, notez que **API Conference** indique les versions **Original** et **v1**.
3. Sélectionnez **v1**.
4. Notez l’**URL de la demande** de la première opération de la liste. Il indique que le chemin d’accès de l’URL de l’API inclut **v1**.

    ![Menu contextuel de l’API : ajouter une version](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une nouvelle version à une API existante
> * Choisir un schéma de version 
> * Ajouter la version à un produit
> * Parcourir le portail des développeurs pour afficher la version

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Mettre à niveau et mettre à l’échelle](upgrade-and-scale.md)