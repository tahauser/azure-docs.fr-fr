---
title: "Gérer des comptes de développeurs à l’aide de groupes dans Gestion des API Azure | Microsoft Docs"
description: "Apprenez à gérer des comptes de développeurs à l'aide de groupes dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: f4e1f8a701b5584138b92526e0e65e28d45e7c04
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure
Dans Gestion des API, les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits sont d'abord visibles pour les groupes. Les développeurs de ces groupes peuvent afficher les produits associés aux groupes et s'y abonner. 

Le service Gestion des API possède les groupes système suivants, qui ne sont pas modifiables :

* **Administrateurs** : les administrateurs d’abonnements Azure sont membres de ce groupe. Les administrateurs gèrent les instances du service Gestion des API, créant les API, opérations et produits qui sont utilisés par les développeurs.
* **Développeurs** : les utilisateurs authentifiés du portail des développeurs appartiennent à ce groupe. Les développeurs sont les clients qui génèrent des applications grâce à vos API. Les développeurs bénéficient d'un accès au portail des développeurs et génèrent des applications qui appellent les opérations d'une API.
* **Invités** : les utilisateurs non authentifiés du portail des développeurs, comme les prospects, qui consultent le portail des développeurs d’une instance d’API Management appartiennent à ce groupe. Ils peuvent recevoir certains accès en lecture seule, comme la possibilité d'afficher les API, mais pas de les appeler.

Outre ces groupes système, les administrateurs peuvent créer des groupes personnalisés ou [utiliser des groupes externes dans des locataires Azure Active Directory qui leur sont associés][leverage external groups in associated Azure Active Directory tenants]. Des groupes externes et personnalisés peuvent être utilisés avec des groupes système offrant une certaine visibilité aux développeurs et un accès aux produits d’API. Vous pourriez, par exemple, créer un groupe personnalisé pour les développeurs affiliés à une organisation partenaire spécifique et leur permettre d’accéder aux API à partir d’un produit contenant uniquement des API pertinentes. Un utilisateur peut être membre de plusieurs groupes.

Ce guide explique comment les administrateurs de l'instance Gestion des API peuvent ajouter de nouveaux groupes et les associer à des produits et des développeurs.

En plus de créer et gérer des groupes dans le portail de publication, vous pouvez créer et gérer vos groupes à l'aide de l’entité [Groupe](https://msdn.microsoft.com/library/azure/dn776329.aspx) de l’API REST de gestion des API.

## <a name="prerequisites"></a>Prérequis

Effectuez les tâches indiquées dans cet article : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"></a>Création d’un groupe

Cette section montre comment ajouter un nouveau groupe à votre compte Gestion des API.

1. Sélectionnez l’onglet **Groupes** à gauche de l’écran.
2. Cliquez sur **+Ajouter**.
3. Entrez un nom unique pour le groupe, et éventuellement une description.
4. Appuyez sur **Créer**.

    ![Ajouter un groupe](./media/api-management-howto-create-groups/groups001.png)

Une fois le groupe créé, il est ajouté à la liste **Groupes**. <br/>Pour modifier le **nom** ou la **description** du groupe, cliquez sur le nom du groupe, puis sur **Paramètres**.<br/>Pour supprimer le groupe, cliquez sur son nom, puis appuyez sur **Supprimer**.

Maintenant que le groupe est créé, il peut être associé à des produits et des développeurs.

## <a name="associate-group-product"></a>Association d’un groupe à un produit

1. Sélectionnez l’onglet **Produits** vers la gauche.
2. Cliquez sur le nom du produit souhaité.
3. Appuyez sur **Contrôle d’accès**.
4. Cliquez sur **+ Ajouter un groupe**.

    ![Associer un groupe à un produit](./media/api-management-howto-create-groups/groups002.png)
5. Sélectionnez le groupe que vous souhaitez ajouter.

    ![Associer un groupe à un produit](./media/api-management-howto-create-groups/groups003.png)

    Pour supprimer un groupe du produit, cliquez sur **Supprimer**.

    ![Supprimer un groupe](./media/api-management-howto-create-groups/groups004.png)

Une fois le produit associé à un groupe, les développeurs de ce groupe peuvent le voir et s'y abonner.

> [!NOTE]
> Pour ajouter des groupes Azure Active Directory, consultez la rubrique [Comment autoriser des comptes de développeur utilisant Azure Active Directory dans Gestion des API Azure](api-management-howto-aad.md).

## <a name="associate-group-developer"></a>Association des groupes aux développeurs

Cette section montre comment associer des groupes à des membres.

1. Sélectionnez l’onglet **Groupes** à gauche de l’écran.
2. Sélectionnez **Membres**.

    ![Ajouter un membre](./media/api-management-howto-create-groups/groups005.png)
3. Appuyez sur **+Ajouter** et sélectionnez un membre.

    ![Ajouter un membre](./media/api-management-howto-create-groups/groups006.png)
4. Appuyez sur **Sélectionner**.

Une fois l’association entre le développeur et le groupe ajoutée, vous pouvez la consulter dans l’onglet **Utilisateurs** .

## <a name="next-steps"></a>Étapes suivantes

* Une fois le développeur ajouté à un groupe, il peut voir tous les produits associés à ce groupe et s'y abonner. Pour plus d’informations, consultez la page [Création et publication d’un produit dans Gestion des API Azure][How create and publish a product in Azure API Management].
* En plus de créer et gérer des groupes dans le portail de publication, vous pouvez créer et gérer vos groupes à l'aide de l’entité [Groupe](https://msdn.microsoft.com/library/azure/dn776329.aspx) de l’API REST de gestion des API.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
