---
title: "Autoriser des comptes de développeurs à l’aide d’Azure Active Directory - Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment autoriser des utilisateurs à l’aide d’Azure Active Directory dans Gestion des API."
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d89257cba70fb82d56fb1beef8a8efe66a8af02d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoriser des comptes de développeurs à l’aide d’Azure Active Directory dans Gestion des API Azure

Cet article explique comment activer l’accès au portail des développeurs pour les utilisateurs à partir d’Azure Active Directory (Azure AD). Il montre également comment gérer des groupes d’utilisateurs Azure AD en ajoutant des groupes externes qui contiennent les utilisateurs.

> [!NOTE]
> L’intégration d’Azure AD est proposée uniquement dans les niveaux [Développeur, Standard et Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>Prérequis

- Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, consultez [Importer et publier](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoriser des comptes de développeurs à l’aide d’Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Sélectionnez ![flèche](./media/api-management-howto-aad/arrow.png).
3. Dans la zone de recherche, tapez **api**.
4. Sélectionnez **Services Gestion des API**.
5. Sélectionnez votre instance de service Gestion des API.
6. Sous **SÉCURITÉ**, sélectionnez **Identités**.

7. Cliquez sur **+Ajouter** en haut.

    Le volet **Ajouter le fournisseur d’identité** s’affiche à droite.
8. Sous **Type du fournisseur**, sélectionnez **Azure Active Directory**.

    Les contrôles qui vous permettent d’entrer d’autres informations nécessaires apparaissent dans le volet. Il s’agit notamment des contrôles **ID client** et **Clé secrète client**. (Vous trouverez des informations sur ces contrôles plus loin dans l’article.)
9. Prenez note du contenu de **l’URL de redirection**.
    
   ![Étapes à suivre pour ajouter un fournisseur d’identité dans le portail Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Dans votre navigateur, ouvrez un autre onglet. 
11. Accédez au [portail Azure](https://portal.azure.com).
12. Sélectionnez ![flèche](./media/api-management-howto-aad/arrow.png).
13. Tapez **active**. Le volet **Azure Active Directory** s’affiche.
14. Sélectionnez **Azure Active Directory**.
15. Sous **GÉRER**, sélectionnez **Inscriptions des applications**.
16. Sélectionnez **Nouvelle inscription d’application**.

    ![Sélections pour la création d’une inscription d’application](./media/api-management-howto-aad/api-management-with-aad002.png)

    Le volet **Créer** s’affiche à droite. C’est là que vous entrez les informations pertinentes relatives à l’application Azure AD.
17. Entrez un nom pour l’application.
18. Pour le type d’application, sélectionnez **Application web/API**.
19. Pour l’URL de connexion, entrez l’URL de connexion de votre portail des développeurs. Dans cet exemple, l’URL de connexion est https://apimwithaad.portal.azure-api.net/signin.
20. Sélectionnez **Créer** pour créer l’application.
21. Pour rechercher votre application, sélectionnez **Inscriptions des applications** puis recherchez par nom.

    ![Zone dans laquelle vous recherchez une application](./media/api-management-howto-aad/find-your-app.png)
22. Une fois l’application inscrite, accédez à **l’URL de réponse** et vérifiez que **l’URL de redirection** a la valeur que vous avez obtenue à l’étape 9. 
23. Si vous souhaitez configurer votre application (par exemple, pour changer **l’URL d’ID d’application**), sélectionnez **Propriétés**.

    ![Ouverture du panneau Propriétés](./media/api-management-howto-aad/api-management-with-aad004.png)

    Si plusieurs instances d’Azure AD vont être utilisées pour cette application, sélectionnez **Oui** pour **Mutualisé**. La valeur par défaut est **Non**.
24. Définissez les autorisations de l’application en sélectionnant **Autorisations requises**.
25. Sélectionnez votre application puis cochez les cases **Lire les données d’annuaire** et **Activer la connexion et lire le profil utilisateur**.

    ![Cases à cocher pour les autorisations](./media/api-management-howto-aad/api-management-with-aad005.png)

    Pour plus d’informations sur les autorisations d’application et les autorisations déléguées, consultez [Accès à l’API Graph][Accessing the Graph API].
26. Dans le volet de gauche, copiez la valeur de **l’ID d’application**.

    ![Valeur de l’ID d’application](./media/api-management-howto-aad/application-id.png)
27. Revenez à votre application Gestion des API. 

    Dans la fenêtre **Ajouter le fournisseur d’identité**, collez la valeur de **l’ID d’application** dans la zone **ID client**.
28. Revenez à la configuration d’Azure AD, puis sélectionnez **Clés**.
29. Créez une clé en spécifiant un nom et une durée. 
30. Sélectionnez **Enregistrer**. La clé est générée.

    Copiez la clé dans le Presse-papiers.

    ![Sélections pour la création d’une clé](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Notez sa valeur. Une fois que vous fermez le volet de configuration d’Azure AD, la clé ne peut plus être affichée.
    > 
    > 
31. Revenez à votre application Gestion des API. 

    Dans la fenêtre **Ajouter le fournisseur d’identité**, collez la clé dans la zone de texte **Clé secrète client**.
32. La fenêtre **Ajouter le fournisseur d’identité** contient également la zone de texte **Locataires autorisés**. Spécifiez les domaines des instances Azure AD auxquelles vous souhaitez accorder l’accès aux API de l’instance de service Gestion des API. Vous pouvez séparer plusieurs domaines par des sauts de ligne, des espaces ou des virgules.

    Vous pouvez spécifier plusieurs domaines dans la section **Locataires autorisés**. Pour qu’un utilisateur puisse se connecter à partir d’un autre domaine que le domaine d’origine dans lequel l’application a été enregistrée, l’administrateur général de l’autre domaine doit d’abord accorder à l’application l’autorisation d’accéder aux données de l’annuaire. Pour accorder l’autorisation, l’administrateur général doit :
    
    a. Accéder à `https://<URL of your developer portal>/aadadminconsent` (par exemple, https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Taper le nom de domaine du locataire Azure AD auquel il souhaite autoriser l’accès.
    
    c. Sélectionnez **Envoyer**. 
    
    Dans l’exemple suivant, un administrateur général de miaoaad.onmicrosoft.com tente d’accorder une autorisation à ce portail de développeurs spécifique. 

33. Après avoir spécifié la configuration désirée, sélectionnez **Ajouter**.

    ![Bouton Ajouter dans le volet Ajouter le fournisseur d’identité](./media/api-management-howto-aad/api-management-with-aad007.png)

Une fois les changements enregistrés, les utilisateurs de l’instance Azure AD spécifiée peuvent se connecter au portail des développeurs en suivant les étapes de la section [Se connecter au portail des développeurs avec un compte Azure AD](#log_in_to_dev_portal).

![Entrée du nom d’un locataire Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Dans l’écran suivant, l’administrateur général est invité à confirmer l’octroi de l’autorisation. 

![Confirmation de l’octroi d’autorisations](./media/api-management-howto-aad/api-management-permissions-form.png)

Si un administrateur autre que l’administrateur général tente de se connecter avant que l’administrateur général n’octroie les autorisations, la tentative de connexion échoue et un écran d’erreur s’affiche.

## <a name="add-an-external-azure-ad-group"></a>Ajouter un groupe Azure AD externe

Après avoir activé l’accès pour les utilisateurs dans une instance Azure AD, vous pouvez ajouter des groupes Azure AD dans Gestion des API. Vous pourrez ainsi gérer plus facilement l’association entre les développeurs du groupe et les produits désirés.

Pour configurer un groupe Azure AD externe, vous devez d’abord configurer l’instance Azure AD sous l’onglet **Identités** en suivant la procédure décrite dans la section précédente. 

Vous ajoutez des groupes Azure AD externes à partir de l’onglet **Groupes** de votre instance Gestion des API.

1. Sélectionnez l’onglet **Groupes** .
2. Sélectionnez le bouton **Ajouter un groupe AAD**.
   ![Bouton Ajouter un groupe AAD](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Sélectionnez le groupe à ajouter.
4. Appuyez sur le bouton **Sélectionner**.

Après avoir ajouté un groupe Azure AD externe, vous pouvez passer en revue ses propriétés et les configurer. Sélectionnez le nom du groupe sous l’onglet **Groupes**. À ce stade, vous pouvez modifier les informations **Nom** et **Description** du groupe.
 
Les utilisateurs de l’instance Azure AD configurée peuvent à présent se connecter au portail des développeurs. Ils peuvent afficher les groupes pour lesquels ils disposent d’une visibilité et s’abonner à ces groupes.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Se connecter au portail des développeurs avec un compte Azure AD

Pour vous connecter au portail des développeurs à l’aide d’un compte Azure AD que vous avez configuré dans les sections précédentes :

1. Ouvrez une nouvelle fenêtre de navigateur à l’aide de l’URL de connexion provenant de la configuration de l’application Active Directory, puis sélectionnez **Azure Active Directory**.

   ![page de connexion][api-management-dev-portal-signin]

2. Entrez les informations d’identification de l’un des utilisateurs dans Azure AD, puis sélectionnez **Se connecter**.

   ![Connexion avec un nom d’utilisateur et un mot de passe][api-management-aad-signin]

3. Un formulaire d’inscription peut vous être présenté si certaines informations supplémentaires sont nécessaires. Renseignez le formulaire d’inscription, puis sélectionnez **S’inscrire**.

   ![Bouton S’inscrire sur le formulaire d’inscription][api-management-complete-registration]

Votre utilisateur est maintenant inscrit auprès du portail des développeurs pour votre instance de service Gestion des API.

![Portail des développeurs une fois l’inscription terminée][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
