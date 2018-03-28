---
title: Créer une identité pour une application Azure dans le portail | Microsoft Docs
description: Décrit comment créer une application et un principal du service Azure Active Directory qui peuvent être utilisés avec le contrôle d'accès basé sur les rôles dans Azure Resource Manager pour gérer l'accès aux ressources.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: c2b8498b2d32e2c3c7ed5dca3295ae6a98fa2676
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources

Lorsqu’un code doit accéder ou modifier des ressources, vous devez configurer une application Azure Active Directory (AD). Vous assignez les autorisations nécessaires à l’application AD. Cette approche est préférable à l’exécution de l’application avec vos propres informations d’identification, car vous pouvez assigner des autorisations à l’identité de l’application différentes de vos propres autorisations. En règle générale, ces autorisations sont strictement limitées à ce que l’application doit faire.

Cet article explique comment effectuer ces étapes via le portail. Elle se concentre sur une application à locataire unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à locataire unique sont généralement utilisées pour les applications métier exécutées au sein de votre organisation.

> [!IMPORTANT]
> Au lieu de créer un principal du service, envisagez d’utiliser Managed Service Identity Azure AD pour l’identité de votre application. MSI Azure AD est une fonctionnalité de la préversion publique d’Azure Active Directory qui simplifie la création d’une identité pour le code. Si votre code s’exécute sur un service qui prend en charge MSI Azure AD et accède aux ressources qui prennent en charge l’authentification Azure Active Directory, MSI Azure AD correspond bien à vos besoins. Pour en savoir plus sur MSI Azure AD, y compris les services qui prennent actuellement en charge cette fonctionnalité, consultez [Managed Service Identity pour les ressources Azure](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Autorisations requises

Pour cette article, vous devez disposer des autorisations suffisantes pour enregistrer une application auprès de votre client Azure AD et affecter l’application à un rôle dans votre abonnement Azure. Vérifions que vous disposez des droits suffisants pour effectuer ces étapes.

### <a name="check-azure-active-directory-permissions"></a>Vérifier les autorisations Azure Active Directory

1. Sélectionnez **Azure Active Directory**.

   ![sélectionner azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Dans Azure Active Directory, sélectionnez **Paramètres utilisateur**.

   ![sélectionner les paramètres utilisateur](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Vérifiez le paramètre **Inscriptions d’applications**. S’il est défini sur **Oui**, les utilisateurs non administrateurs peuvent inscrire des applications AD. Ce paramètre signifie que n’importe quel utilisateur dans Azure AD peut inscrire une application. Vous pouvez passer à [Vérifier les autorisations d’abonnement Azure](#check-azure-subscription-permissions).

   ![afficher les inscriptions d’applications](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Si le paramètre d’inscriptions d’applications est défini sur **Non**, seuls les utilisateurs administrateurs peuvent inscrire des applications. Vérifiez si votre compte est administrateur de l’abonné Azure AD. Sélectionnez **vue d’ensemble** et consultez vos informations utilisateur. Si le rôle Utilisateur est assigné à votre compte mais que le paramètre d’inscription d’application (de l’étape précédente) est limité aux utilisateurs administrateurs, demandez à votre administrateur de vous assigner un rôle administrateur ou d’autoriser les utilisateurs à inscrire des applications.

   ![rechercher un utilisateur](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Vérifier les autorisations d’abonnement Azure

Dans votre abonnement Azure, votre compte doit disposer d’un accès `Microsoft.Authorization/*/Write` pour affecter un rôle à une application AD. Cette action est accordée par le biais du rôle [Propriétaire](../active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si le rôle **Collaborateur** est affecté à votre compte, vous ne disposez pas de l’autorisation appropriée. Vous recevez un message d’erreur lorsque vous tentez d’attribuer le principal de service à un rôle.

Pour vérifier vos autorisations d’abonnement :

1. Sélectionnez votre compte dans le coin supérieur droit, puis **Mes autorisations**.

   ![Sélectionnez les autorisations utilisateur](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Sélectionnez l’abonnement dans la liste déroulante. Sélectionnez **Cliquer ici pour afficher les détails d’accès complet pour cet abonnement**.

   ![rechercher un utilisateur](./media/resource-group-create-service-principal-portal/view-details.png)

1. Affichez les rôles qui vous sont affectés et déterminez si vous disposez des autorisations appropriées pour affecter un rôle à une application AD. Si ce n’est pas le cas, demandez à votre administrateur d’abonnement de vous ajouter un rôle Administrateur de l’accès utilisateur. Dans l’image suivante, le rôle Propriétaire est assigné à l’utilisateur, ce qui signifie que l’utilisateur dispose des autorisations appropriées.

   ![afficher les autorisations](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Créer une application Azure Active Directory

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
1. Sélectionnez **Azure Active Directory**.

   ![sélectionner azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Sélectionnez **Inscriptions d’applications**.

   ![sélectionner des inscriptions d’applications](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Sélectionnez **Nouvelle inscription d’application**.

   ![ajouter une application](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Fournissez un nom et une URL pour l’application. Sélectionnez **Application Web / API** pour le type d’application que vous souhaitez créer. Vous ne pouvez pas créer d’informations d’identification pour une [Application native](../active-directory/active-directory-application-proxy-native-client.md) ; par conséquent, ce type ne fonctionne pas pour une application automatisée. Après avoir défini les valeurs, sélectionnez **Créer**.

   ![nommer l’application](./media/resource-group-create-service-principal-portal/create-app.png)

Vous avez créé votre application.

## <a name="get-application-id-and-authentication-key"></a>Obtenir un ID d’application et une clé d’authentification

Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application et d’une clé d’authentification. Pour obtenir ces valeurs, procédez comme suit :

1. Dans **Inscriptions d’applications** dans Azure Active Directory, sélectionnez votre application.

   ![sélectionner une application](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Certains [exemples d’applications](#log-in-as-the-application) font référence à cette valeur en tant qu’ID client.

   ![ID client](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Pour générer une clé d’authentification, sélectionnez **Paramètres**.

   ![sélectionner paramètres](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Pour générer une clé d’authentification, sélectionnez **Clés**.

   ![sélectionner des clés](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

   ![enregistrer une clé](./media/resource-group-create-service-principal-portal/save-key.png)

   Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la valeur de la clé avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

   ![clé enregistrée](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obtenir l’ID de locataire

Lors d’une connexion par programmation, vous devez transmettre l’ID de locataire avec votre demande d’authentification.

1. Sélectionnez **Azure Active Directory**.

   ![sélectionner azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Pour obtenir l’ID de locataire, sélectionnez **Propriétés** pour votre client Azure AD.

   ![Sélectionnez les propriétés Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Copiez l’**ID Directory**. Cette valeur est votre ID de locataire.

   ![ID client](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Affecter l’application à un rôle

Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Accédez au niveau d’étendue que vous souhaitez affecter à l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**. Vous pouvez également sélectionner un groupe de ressources ou une ressource.

   ![sélectionner l'abonnement](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Sélectionnez l’abonnement (groupe de ressources ou ressource) auquel l’application doit être affectée.

   ![sélectionner l’abonnement pour l’affectation](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Sélectionnez **Contrôle d’accès (IAM)**.

   ![sélectionner l’accès](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Sélectionnez **Ajouter**.

   ![sélectionner ajouter](./media/resource-group-create-service-principal-portal/select-add.png)

1. Sélectionnez le rôle que vous souhaitez affecter à l’application. L’image suivante montre le code **Lecteur**.

   ![sélectionner un rôle](./media/resource-group-create-service-principal-portal/select-role.png)

1. Recherchez votre application et sélectionnez-la.

   ![rechercher une application](./media/resource-group-create-service-principal-portal/search-app.png)

1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer une application mutualisée, consultez le [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
* Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md).  
* Pour une liste des actions disponibles qui peuvent être autorisées ou refusées aux utilisateurs, consultez [Opérations du fournisseur de ressources Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).
