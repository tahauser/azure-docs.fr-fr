---
title: "S’inscrire à l’aide des informations de partenaire CSP à la Gestion des coûts Azure | Microsoft Docs"
description: "Utilisez vos informations de partenaire CSP pour vous inscrire à la Gestion des coûts Azure par Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 0e5e247182335b88f109aa6624a40bc782975b97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>S’inscrire au programme pour les partenaires CSP et afficher les données de coût

En tant que partenaire CSP, vous pouvez vous inscrire à la Gestion des coûts Azure par Cloudyn. Votre inscription vous donne accès au portail Cloudyn. Ce guide de démarrage rapide décrit le processus d’inscription nécessaire pour créer un abonnement d’évaluation Cloudyn et vous connecter au portail Cloudyn. Il explique également comment afficher immédiatement des données de coût.


>[!NOTE]

>Seuls les partenaires et les fournisseurs CSP Indirect peuvent effectuer une inscription Cloudyn.
>
>La configuration de l’API Partner Center est nécessaire pour l’authentification et l’accès aux données. Un compte d’administrateur général Partner Center est nécessaire pour provisionner l’accès à l’API.
Pour plus d’informations, consultez [Se connecter à l’API Espace partenaires](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx).
>
>L’accès à Cloudyn peut être mis à disposition par les revendeurs CSP Indirect une fois que leur fournisseur CSP Indirect s’est inscrit à Cloudyn. Les revendeurs CSP Indirect peuvent ensuite fournir l’accès à Cloudyn aux clients et abonnements Azure.

## <a name="log-in-to-azure"></a>Connexion à Azure

- Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Créer une inscription d’évaluation

1. Dans le portail Azure, cliquez sur **Gestion des coûts + Facturation** dans la liste des services.
2. Sous **Vue d’ensemble**, cliquez sur **Gestion des coûts**.  
    ![Page Gestion des coûts](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Sur la page **Gestion des coûts**, cliquez sur le bouton d’**accès à la Gestion des coûts** pour ouvrir la page d’inscription de Cloudyn dans une nouvelle fenêtre.
4. Sur la page d’inscription à une évaluation du portail Cloudyn, tapez le nom de votre société, sélectionnez l’option d’**administrateur de programme partenaire CSP Microsoft** et cliquez sur **Suivant**.  
5. Entrez un **ID d’application**, un **ID de commerce** et une **clé secrète d’application**, puis sélectionnez le **plan tarifaire par défaut**. Si vous n’avez pas les informations nécessaires à portée de main, connectez-vous au portail Espace partenaires à l’adresse [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) avec votre compte d’administrateur principal et procédez comme suit :
  1. Accédez à **Tableau de bord** > **Paramètres du compte** > **Gestion des applications**.
  2. Si vous avez déjà créé une application web, ignorez cette étape. Dans le cas contraire, cliquez sur **Ajouter une application Web** dans la section **Application web**.
  3. Copiez l’identificateur unique **ID d’application** à partir de votre application web.
  4. Copiez le GUID de **l’ID de commerce** à partir de votre application web.
  5. Sélectionnez la durée de validité de la clé, à savoir un ou deux ans, selon les besoins. Sélectionnez **Ajouter une clé**, puis copiez et enregistrez la valeur de la clé secrète.  
    ![Espace partenaires CSP](./media/quick-register-csp/csp-partner-center.png)
  6. Revenez à la page d’inscription et collez les informations.  
      ![Informations d’identification du compte CSP](./media/quick-register-csp/csp-reg.png)
6. Acceptez les conditions d’utilisation, puis validez les informations. Cliquez sur **Suivant** pour autoriser Cloudyn à collecter des données de ressource Azure. Les données collectées incluent les données d’utilisation, de performances, de facturation et de balise de vos abonnements.  
7. L’option pour **inviter d’autres parties prenantes** permet d’ajouter des utilisateurs en tapant leurs adresses e-mail. Lorsque vous avez terminé, cliquez sur **Suivant**. L’ajout de toutes vos données de facturation à Cloudyn prend environ deux heures.
8. Cliquez sur le bouton d’**accès à Cloudyn** pour ouvrir le portail Cloudyn. La page de **gestion des comptes cloud** devrait alors afficher les informations de votre compte CSP inscrit.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurer l’accès CSP indirect dans Cloudyn

Par défaut, l’API Espace partenaires est accessible uniquement aux fournisseurs de solutions Cloud (CSP, Cloud Solution Providers). Toutefois, un fournisseur CSP direct peut configurer l’accès pour ses clients ou partenaires CSP indirects à l’aide de groupes d’entités dans Cloudyn.

Pour activer l’accès pour les clients ou partenaires CSP indirects, suivez les étapes décrites dans [Créer une inscription d’évaluation](#create-a-trial-registration) pour configurer une inscription d’évaluation. Effectuez les étapes suivantes pour segmenter les données CSP indirectes à l’aide de groupes d’entités Cloudyn. Ensuite, assignez les autorisations utilisateur appropriées pour les groupes d’entités.

1. Créez un groupe d’entités avec les informations fournies dans [Créer des entités](tutorial-user-access.md#create-entities).
2. Suivez les étapes fournies dans [Assigning subscriptions to Cost Entities (Affectation d’abonnements à des entités de coût)](https://support.cloudyn.com/hc/en-us/articles/115005139425-Video-Assigning-subscriptions-to-Cost-Entities). Associez le compte du client CSP indirect et ses abonnements Azure à l’entité que vous avez créée.
3. Suivez les étapes fournies dans [Créer un utilisateur avec un accès administrateur](tutorial-user-access.md#create-a-user-with-admin-access) pour créer un compte d’utilisateur avec un accès administrateur. Ensuite, vérifiez que le compte d’utilisateur dispose d’un accès administrateur aux entités spécifiques que vous avez créées pour le compte indirect.

Les partenaires CSP indirects se connectent au portail Cloudyn à l’aide des comptes que vous avez créés pour eux.


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé vos informations CSP pour vous inscrire à la Gestion des coûts. Vous vous êtes également connecté au portail Cloudyn et avez affiché des données de coût. Pour en savoir plus sur la Gestion des coûts Azure par Cloudyn, poursuivez avec le didacticiel sur la Gestion des coûts.

> [!div class="nextstepaction"]
> [Réviser l’utilisation et les coûts](./tutorial-review-usage.md)
