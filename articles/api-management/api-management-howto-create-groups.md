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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 1587243bcd5f2b9af98b8b529c152ba49ef676be
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure
Dans Gestion des API, les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits sont d'abord visibles pour les groupes. Les développeurs de ces groupes peuvent afficher les produits associés aux groupes et s'y abonner. 

Le service Gestion des API possède les groupes système suivants, qui ne sont pas modifiables.

* **Administrateurs** : les administrateurs d’abonnements Azure sont membres de ce groupe. Les administrateurs gèrent les instances du service Gestion des API, créant les API, opérations et produits qui sont utilisés par les développeurs.
* **Développeurs** : les utilisateurs authentifiés du portail des développeurs appartiennent à ce groupe. Les développeurs sont les clients qui génèrent des applications grâce à vos API. Les développeurs bénéficient d'un accès au portail des développeurs et génèrent des applications qui appellent les opérations d'une API.
* **Invités** : les utilisateurs non authentifiés du portail des développeurs, comme les prospects, qui consultent le portail des développeurs d’une instance d’API Management appartiennent à ce groupe. Ils peuvent recevoir certains accès en lecture seule, comme la possibilité d'afficher les API, mais pas de les appeler.

Outre ces groupes système, les administrateurs peuvent créer des groupes personnalisés ou [utiliser des groupes externes dans des locataires Azure Active Directory qui leur sont associés][leverage external groups in associated Azure Active Directory tenants]. Des groupes externes et personnalisés peuvent être utilisés avec des groupes système offrant une certaine visibilité aux développeurs et un accès aux produits d’API. Vous pourriez, par exemple, créer un groupe personnalisé pour les développeurs affiliés à une organisation partenaire spécifique et leur permettre d’accéder aux API à partir d’un produit contenant uniquement des API pertinentes. Un utilisateur peut être membre de plusieurs groupes.

Ce guide explique comment les administrateurs de l'instance Gestion des API peuvent ajouter de nouveaux groupes et les associer à des produits et des développeurs.

> [!NOTE]
> En plus de créer et gérer des groupes dans le portail de publication, vous pouvez créer et gérer vos groupes à l'aide de l’entité [Groupe](https://msdn.microsoft.com/library/azure/dn776329.aspx) de l’API REST de gestion des API.
> 
> 

## <a name="create-group"></a>Création d’un groupe
Pour créer un groupe, cliquez sur **Portail de publication** dans le portail Azure de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs][api-management-management-console]

> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance].
> 
> 

Cliquez sur **Groupes** dans le menu **Gestion des API** à gauche, puis sur **Ajouter un groupe**.

![Add new group][api-management-add-group]

Entrez un nom unique pour le groupe, et éventuellement une description, puis cliquez sur **Enregistrer**.

![Add new group][api-management-add-group-window]

Le nouveau groupe s'affiche dans l'onglet des groupes. Pour modifier le **nom** ou la **description** du groupe, cliquez sur le nom du groupe dans la liste. Pour supprimer le groupe, cliquez sur **Supprimer**.

![Group added][api-management-new-group]

Maintenant que le groupe est créé, il peut être associé à des produits et des développeurs.

## <a name="associate-group-product"></a>Association d’un groupe à un produit
Pour associer un groupe à un produit, cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche, puis cliquez sur le nom du produit souhaité.

![Set visibility][api-management-add-group-to-product]

Utilisez l'onglet **Visibilité** pour ajouter et supprimer des groupes et pour voir les groupes actuellement associés au produit. Pour ajouter ou supprimer des groupes, activez ou désactivez les cases à cocher des groupes voulus, puis cliquez sur **Enregistrer**.

![Set visibility][api-management-add-group-to-product-visibility]

> [!NOTE]
> Pour ajouter des groupes Azure Active Directory, consultez la rubrique [Comment autoriser des comptes de développeur utilisant Azure Active Directory dans Gestion des API Azure](api-management-howto-aad.md).
> 
> Pour configurer les groupes depuis l’onglet**Visibilité** d’un produit, cliquez sur **Gérer des groupes**.
> 
> 

Une fois le produit associé à un groupe, les développeurs de ce groupe peuvent le voir et s'y abonner.

## <a name="associate-group-developer"></a>Association des groupes aux développeurs
Pour associer des groupes aux développeurs, cliquez sur **Utilisateurs** dans le menu **Gestion des API** sur la gauche, puis activez la case à cocher à côté des développeurs à associer au groupe.

![Add developer to group][api-management-add-group-to-developer]

Une fois que les développeurs souhaités sont sélectionnés, cliquez sur le groupe dans la liste déroulante **Ajouter au groupe** . Les développeurs peuvent être retirés des groupes via la liste déroulante **Supprimer du groupe** . 

![Développeurs][api-management-add-group-to-developer-saved]

Une fois l’association entre le développeur et le groupe ajoutée, vous pouvez la consulter dans l’onglet **Utilisateurs** .

## <a name="next-steps"></a>Étapes suivantes
* Une fois le développeur ajouté à un groupe, il peut voir tous les produits associés à ce groupe et s'y abonner. Pour plus d’informations, consultez la page [Création et publication d’un produit dans Gestion des API Azure][How create and publish a product in Azure API Management].
* En plus de créer et gérer des groupes dans le portail de publication, vous pouvez créer et gérer vos groupes à l'aide de l’entité [Groupe](https://msdn.microsoft.com/library/azure/dn776329.aspx) de l’API REST de gestion des API.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
