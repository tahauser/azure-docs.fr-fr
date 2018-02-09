---
title: "Autoriser des comptes de développeurs à l’aide d’Azure Active Directory dans Gestion des API Azure | Microsoft Docs"
description: Comment autoriser des utilisateurs avec Azure Active Directory dans Gestion des API.
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
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Comment autoriser des comptes de développeurs avec Azure Active Directory dans Gestion des API Azure

Ce guide vous explique comment activer l’accès au portail des développeurs pour les utilisateurs d’Azure Active Directory. Il vous montre également comment gérer des groupes d’utilisateurs Azure Active Directory en ajoutant des groupes externes qui contiennent les utilisateurs d’un annuaire Azure Active Directory.

> [!WARNING]
> L’intégration d’Azure Active Directory est proposée uniquement dans les niveaux [Développeur, Standard et Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>configuration requise

- Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Importez et publiez une instance Gestion des API. Pour plus d’informations, consultez [Importer et publier](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Comment autoriser des comptes de développeurs avec Azure Active Directory

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Sélectionnez ![flèche](./media/api-management-howto-aad/arrow.png).
3. Dans la zone de recherche, tapez « API ».
4. Cliquez sur **Services Gestion des API**.
5. Sélectionnez votre instance de service APIM.
6. Sous **SÉCURITÉ**, sélectionnez **Identités**.

    ![Identités externes](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Cliquez sur **+Ajouter** en haut.

    La fenêtre **Ajouter le fournisseur d’identité** s’affiche à droite.
8. Sous **Type du fournisseur**, sélectionnez **Azure Active Directory**.

    Les contrôles qui vous permettent d’entrer d’autres informations nécessaires apparaissent dans la fenêtre. Les contrôles incluent : **ID client**, **Clé secrète client** (d’autres informations sont disponibles plus loin dans ce didacticiel).
9. Prenez note de l’**URL de redirection**.  
10. Dans votre navigateur, ouvrez un autre onglet. 
11. Ouvrez le [portail Azure](https://portal.azure.com).
12. Sélectionnez ![flèche](./media/api-management-howto-aad/arrow.png).
13. Tapez « active » pour afficher **Azure Active Directory**.
14. Sélectionnez **Azure Active Directory**.
15. Sous **GÉRER**, sélectionnez **Inscription d’application**.

    ![Inscription d'application](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Cliquez sur **Nouvelle inscription d’application**.

    La fenêtre **Créer** s’affiche sur la droite. C’est là que vous entrez les informations pertinentes relatives à l’application AAD.
17. Entrez un nom pour l’application.
18. Pour le type d’application, sélectionnez **Application web/API**.
19. Pour URL de connexion, entrez l’URL de connexion de votre portail des développeurs. Dans cet exemple, l’URL de connexion est : https://apimwithaad.portal.azure-api.net/signin.
20. Cliquez sur **Créer** pour créer l’application.
21. Pour rechercher votre application, sélectionnez **Inscriptions des applications** puis recherchez par nom.

    ![Inscription d'application](./media/api-management-howto-aad/find-your-app.png)
22. Une fois l’application inscrite, accédez à l’**URL de réponse** et assurez-vous que « l’URL de redirection » est définie sur la valeur que vous avez obtenue à l’étape 9. 
23. Si vous souhaitez configurer votre application (par exemple, remplacer l’**URL d’ID d’application**) sélectionnez **Propriétés**.

    ![Inscription d'application](./media/api-management-howto-aad/api-management-with-aad004.png)

    Si plusieurs annuaires Azure Active Directory vont être utilisés pour cette application, cliquez sur **Oui** pour **Application mutualisée**. La valeur par défaut est **Non**.
24. Définissez les autorisations de l’application en sélectionnant **Autorisations requises**.
25. Sélectionnez votre application puis cochez les options **Lire les données d’annuaire** et **Activer la connexion et lire le profil utilisateur**.

    ![Inscription d'application](./media/api-management-howto-aad/api-management-with-aad005.png)

    Pour plus d’informations sur l’application et les autorisations déléguées, consultez la page [Accès à l’API Graph][Accessing the Graph API].
26. Dans la fenêtre de gauche, copiez l’**ID d’application**.

    ![Inscription d'application](./media/api-management-howto-aad/application-id.png)
27. Revenez à votre application Gestion des API. La fenêtre **Ajouter le fournisseur d’identité** devrait s’afficher. <br/>Collez la valeur **ID d’Application** dans le champ **ID client**.
28. Revenez à la configuration Azure Active Directory, puis cliquez sur **Clés**.
29. Créez une nouvelle clé en spécifiant un nom et une durée. 
30. Cliquez sur **Enregistrer**. La clé est générée.

    Copiez la clé dans le Presse-papiers.

    ![Inscription d'application](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Notez sa valeur. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne peut plus être affichée.
    > 
    > 
31. Revenez à votre application Gestion des API. <br/>Dans la fenêtre **Ajouter le fournisseur d’identité**, collez la clé dans la zone de texte **Clé secrète client**.
32. La fenêtre **Ajouter le fournisseur d’identité** contient la zone de texte **Locataires autorisés**, dans laquelle vous spécifiez les répertoires qui ont accès aux API de l’instance du service de gestion des API. <br/>Spécifiez les domaines des instances Azure Active Directory auxquelles vous souhaitez accorder l’accès. Vous pouvez séparer plusieurs domaines par des sauts de ligne, des espaces ou des virgules.

    Plusieurs domaines peuvent être spécifiés dans la section **Locataires autorisés** . Avant qu’un utilisateur puisse se connecter à partir d’un autre domaine que le domaine d’origine dans lequel l’application a été enregistrée, l’administrateur général de l’autre domaine doit accorder à l’application l’autorisation d’accéder aux données de l’annuaire. Pour accorder l’autorisation, l’administrateur général doit accéder à `https://<URL of your developer portal>/aadadminconsent` (par exemple, https://contoso.portal.azure-api.net/aadadminconsent), entrer le nom de domaine du client Active Directory auquel il souhaite accorder l’accès, puis cliquer sur Envoyer. Dans l’exemple suivant, un administrateur général de `miaoaad.onmicrosoft.com` tente d’accorder l’autorisation à ce portail développeur spécifique. 

33. Une fois la configuration souhaitée spécifiée, cliquez sur **Ajouter**.

    ![Inscription d'application](./media/api-management-howto-aad/api-management-with-aad007.png)

Après avoir enregistré les modifications, les utilisateurs de l’annuaire Azure Active Directory spécifié peuvent se connecter au portail des développeurs en suivant les étapes de la section [Connexion au portail des développeurs avec un compte Azure Active Directory](#log_in_to_dev_portal).

![Autorisations](./media/api-management-howto-aad/api-management-aad-consent.png)

Dans l’écran suivant, l’administrateur général sera invité à confirmer l’octroi de l’autorisation. 

![Autorisations](./media/api-management-howto-aad/api-management-permissions-form.png)

Si un administrateur autre que l’administrateur global tente de se connecter avant que les autorisations ne soient accordées par un administrateur général, la tentative de connexion échoue et un écran d’erreur s’affiche.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Ajout d’un groupe Azure Active Directory externe

Après avoir activé l’accès pour les utilisateurs dans Azure Active Directory, vous pouvez ajouter des groupes Azure Active Directory à Gestion des API pour gérer plus facilement l’association des développeurs du groupe avec les produits souhaités.

Pour pouvoir configurer un groupe Azure Active Directory externe, Azure Active Directory doit d’abord être configuré dans l’onglet Identités, selon la procédure décrite dans la section précédente. 

Les groupes Azure Active Directory externes sont ajoutés à partir de l’onglet **Groupes** de votre instance Gestion des API.

![Groupes](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Sélectionnez l’onglet **Groupes** .
2. Cliquez sur le bouton **Ajouter un groupe AAD**.
3. Sélectionnez le groupe que vous souhaitez ajouter.
4. Appuyez sur le bouton **Sélectionner**.

Une fois le groupe Azure Active Directory créé, vérifiez et configurez les propriétés des groupes externes une fois qu’ils ont été ajoutés, puis cliquez sur le nom du groupe dans l’onglet **Groupes**.

À partir de là, vous pouvez modifier le **nom** et la **description** du groupe.
 
Les utilisateurs de l’annuaire Azure Active Directory configuré peuvent se connecter au portail des développeurs et consulter des groupes. Ils peuvent s’abonner aux groupes qui leur sont accessibles selon les instructions de la section suivante.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Connexion au portail des développeurs avec un compte Azure Active Directory

Pour vous connecter au portail des développeurs à l’aide d’un compte Azure Active Directory configuré dans les sections précédentes, ouvrez une nouvelle fenêtre de navigateur avec **l’URL de connexion** dans la configuration de l’application Active Directory, puis cliquez sur **Azure Active Directory**.

![Portail des développeurs][api-management-dev-portal-signin]

Entrez les informations d’identification d’un des utilisateurs dans votre annuaire Azure Active Directory, puis cliquez sur **Se connecter**.

![Se connecter][api-management-aad-signin]

Un formulaire d’inscription peut vous être présenté si certaines informations supplémentaires sont requises. Renseignez le formulaire d’inscription, puis cliquez sur **S’inscrire**.

![Inscription][api-management-complete-registration]

Votre utilisateur est maintenant connecté au portail des développeurs pour votre instance de service Gestion des API.

![Inscription terminée][api-management-registration-complete]

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

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
