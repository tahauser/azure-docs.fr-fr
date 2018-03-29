---
title: Comment lier un abonnement Azure à Azure AD B2C | Documents Microsoft
description: Guide détaillé pour activer la facturation pour un client Azure AD B2C dans un abonnement Azure.
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 12/05/2017
ms.author: davidmu
ms.openlocfilehash: bb9324b01bb810ba15994612bac2ff20dc83ab82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Liaison d’un abonnement Azure à un locataire Azure AD B2C

> [!IMPORTANT]
> Les dernières informations sur la facturation à l’utilisation et la tarification d’Azure AD B2C sont disponibles sur la page suivante : [Azure Active Directory B2C Tarification](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Les frais d’utilisation pour Azure AD B2C sont facturés à un abonnement Azure. Quand un locataire Azure AD B2C est créé, l’administrateur du locataire doit lier explicitement le locataire Azure AD B2C à un abonnement Azure. Cet article vous montre comment procéder.

> [!NOTE]
> Un abonnement lié à un locataire Azure AD B2C peut uniquement être utilisé pour la facturation de l’utilisation d’Azure AD B2C. L’abonnement ne peut pas être utilisé pour ajouter d’autres services Azure ou licences Office 365 *au sein du locataire Azure AD B2C*.

 Le lien de l’abonnement est obtenu en créant une « ressource » Azure AD B2C dans l’abonnement Azure cible. De nombreuses « ressources » Azure AD B2C peuvent être créées dans à un abonnement Azure, ainsi que d’autres ressources Azure (par exemple, machines virtuelles, stockage de données, LogicApps). Vous pouvez voir toutes les ressources au sein de l’abonnement en accédant au locataire Azure AD auquel l’abonnement est associé.

Un abonnement Azure valide est nécessaire pour continuer.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

Vous devez d’abord [créer un locataire Azure AD B2C](active-directory-b2c-get-started.md) auquel vous souhaitez lier un abonnement. Ignorez cette étape si vous avez déjà créé un locataire Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Ouvrir le portail Azure dans le locataire Azure AD qui affiche votre abonnement Azure

Accédez au locataire Azure AD qui affiche votre abonnement Azure. Ouvrez le [portail Azure](https://portal.azure.com), puis basculez vers le locataire Azure AD affichant l’abonnement Azure que vous souhaitez utiliser.

![Basculement vers votre locataire Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Rechercher Azure AD B2C dans la Place de marché Microsoft Azure

Cliquez sur le bouton **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, entrez `B2C`.

![Ajouter le bouton en surbrillance et le texte Azure AD B2C dans le champ Rechercher dans le marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Dans la liste des résultats, cliquez sur **Azure AD B2C**.

![Azure AD B2C sélectionné dans la liste des résultats](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Des informations détaillées sur Azure AD B2C sont affichées. Pour commencer la configuration de votre nouveau client Azure Active Directory B2C, cliquez sur le bouton **Créer**.

Dans l’écran de création de ressources, sélectionnez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Créer une ressource Azure AD B2C au sein de l’abonnement Azure

Dans la boîte de dialogue de création de ressources, sélectionnez un locataire Azure AD B2C dans la liste déroulante. Apparaissent tous les locataires dont vous êtes administrateur général et ceux qui ne sont pas déjà liés à un abonnement.

Le nom de la ressource Azure AD B2C est présélectionné et correspond au nom de domaine du locataire Azure AD B2C.

Pour Abonnement, sélectionnez un abonnement Azure actif dont vous êtes administrateur.

Sélectionnez un groupe de ressources et un emplacement de groupe de ressources. La sélection effectuée ici n’a aucun impact sur l’emplacement, les performances ou l’état de facturation du locataire Azure AD B2C.

![Créer une ressource B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gérer vos ressources de locataire Azure AD B2C

Une fois que vous avez correctement créé une ressource Azure AD B2C dans l’abonnement Azure, une nouvelle ressource du type « Locataire B2C » doit apparaître aux côtés de vos autres ressources Azure.

Vous pouvez utiliser cette ressource pour :

- accéder à l’abonnement pour afficher les informations de facturation ;
- accéder à votre locataire Azure AD B2C ;
- envoyer une demande de support ;
- déplacer votre ressource de locataire Azure AD B2C vers un autre abonnement Azure ou un autre groupe de ressources.

![Paramètres de ressource B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problèmes connus

### <a name="csp-subscriptions"></a>Abonnements CSP

Un locataire Azure AD B2C **ne peut pas** être lié à des abonnements CSP.

### <a name="self-imposed-restrictions"></a>Restrictions imposées automatiquement

Un utilisateur peut avoir établi une restriction régionale pour la création de ressources Azure. Cette restriction peut empêcher la création de ressources Azure AD B2C. Pour atténuer ce problème, assouplissez cette restriction.

## <a name="next-steps"></a>Étapes suivantes

Une fois ces étapes effectuées pour chacun de vos locataires Azure AD B2C, votre abonnement Azure est facturé conformément aux détails de votre contrat Azure Direct ou Enterprise.

Vous pouvez consulter les détails de l’utilisation et de la facturation dans l’abonnement Azure sélectionné. Vous pouvez également consulter les rapports détaillés d’utilisation quotidienne à l’aide de [l’API de création de rapports d’utilisation](active-directory-b2c-reference-usage-reporting-api.md).
