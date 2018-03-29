---
title: Créer un locataire Azure Active Directory B2C | Microsoft Docs
description: Rubrique sur la création d’un client Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: 56e0ae7454e86911c894da88b5aa8ccc03a08af3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Créer un locataire Azure Active Directory B2C dans le portail Azure

Ce démarrage rapide vous aide à créer un locataire Microsoft Azure Active Directory (Azure AD) B2C en seulement quelques minutes. Quand vous avez terminé, vous disposez d’un locataire B2C (également appelé annuaire) pour inscrire les applications B2C.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

Les fonctionnalités B2C ne peuvent pas être activées dans vos locataires existants. Vous devez créer un locataire Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Félicitations, vous avez créé un locataire Azure Active Directory B2C. Vous êtes administrateur général du locataire. Vous pouvez ajouter d’autres administrateurs généraux en fonction des besoins. Pour basculer vers votre nouveau locataire, cliquez sur le *lien permettant de gérer votre nouveau locataire*.

![Lien permettant de gérer votre nouveau locataire](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Si vous envisagez d’utiliser un client B2C pour une application de production, consultez l’article sur [les clients de mise à l’échelle pour production/clients B2C de la version préliminaire](active-directory-b2c-reference-tenant-type.md). Il existe des problèmes connus liés à la suppression d’un locataire B2C existant et à sa recréation sous le même nom de domaine. Vous devez créer un locataire B2C portant un nom de domaine différent.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Lier votre locataire à votre abonnement

Vous devez lier votre locataire Azure AD B2C à votre abonnement Azure pour activer toutes les fonctionnalités B2C et payer les frais d’utilisation. Pour plus d’informations, consultez [cet article](active-directory-b2c-how-to-enable-billing.md). Si vous ne liez pas votre locataire Azure AD B2C à votre abonnement Azure, certaines fonctionnalités sont bloquées et vous voyez un message d’avertissement (« Aucun abonnement lié à ce client B2C ou à l’abonnement ne requiert votre attention. ») dans les paramètres B2C. Il est important d’effectuer cette étape avant de passer vos applications en production.

## <a name="easy-access-to-settings"></a>Accès facile aux paramètres

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Vous pouvez également accéder au panneau en entrant `Azure AD B2C` dans **Rechercher des ressources** en haut du portail. Dans la liste des résultats, sélectionnez **Azure AD B2C** pour accéder au panneau des paramètres B2C.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Azure Active Directory B2C : inscription de votre application](active-directory-b2c-app-registration.md)