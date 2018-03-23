---
title: Problèmes de connexion à une application non issue de la galerie configurée pour l’authentification unique fédérée | Microsoft Docs
description: Instructions pour résoudre les problèmes rencontrés lors de la connexion à une application configurée pour l’authentification unique SAML fédérée avec Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cfd4e478850783983caa3042cbbb2b096a576292
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problèmes de connexion à une application non issue de la galerie configurée pour l’authentification unique fédérée

Pour résoudre votre problème, vous devez vérifier la configuration de l’application dans Azure AD de la manière suivante :

-   Vérifiez que vous avez suivi toutes les étapes de configuration pour l’application de la galerie Azure AD.

-   Vérifiez que l’identificateur et l’URL de réponse configurés dans AAD correspondent aux valeurs attendues dans l’application.

-   Vérifiez que vous avez affecté des utilisateurs à l’application.

## <a name="application-not-found-in-directory"></a>Application introuvable dans le répertoire

*Erreur AADSTS70001 : L’application avec l’identificateur « https://contoso.com » est introuvable dans l’annuaire*.

**Cause possible**

L’attribut d’émetteur envoyé de l’application vers Azure AD dans la demande SAML ne correspond pas à la valeur de l’identificateur configurée dans l’application Azure AD.

**Résolution :**

Vérifiez que l’attribut d’émetteur de la demande SAML correspond à la valeur de l’identificateur configurée dans Azure AD :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  <span id="_Hlk477190042" class="anchor"></span>Accédez à la section **Domaine et URL**. Vérifiez que la valeur située dans la zone de texte de l’identificateur correspond à celle de l’identificateur mentionnée dans l’erreur.

Une fois que vous avez mis à jour la valeur d’identificateur pour qu’elle corresponde à celle envoyée par l’application dans la demande SAML, vous pouvez vous connecter à l’application.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L’adresse de réponse ne correspond pas aux adresses de réponse configurées pour l’application. 

*Erreur AADSTS50011 : L’adresse de réponse « https://contoso.com » ne correspond pas aux adresses de réponse configurées pour l’application*. 

**Cause possible** 

La valeur AssertionConsumerServiceURL dans la demande SAML ne correspond pas à la valeur de l’URL de réponse ou au modèle configuré dans Azure AD. La valeur AssertionConsumerServiceURL dans la demande SAML est l’URL que vous voyez dans l’erreur. 

**Résolution :** 

Assurez-vous que la valeur AssertionConsumerServiceURL dans la demande SAML correspond à la valeur de l’URL de réponse configurée dans Azure AD. 
 
1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**. 

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche. 

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**. 

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory. 

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications. 

  * Si l’application que vous recherchez n’apparaît pas ici, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.
  
6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Domaine et URL**. Vérifiez ou mettez à jour la valeur figurant dans la zone de texte URL de réponse pour qu’elle corresponde à la valeur AssertionConsumerServiceURL dans la demande SAML.

  * Si vous ne voyez pas la zone de texte URL de réponse, cochez la case **Afficher les paramètres d’URL avancés**. 

Une fois que vous avez mis à jour la valeur de l’URL de réponse dans Azure AD et qu’elle correspond à celle envoyée par l’application dans la demande SAML, vous devez être en mesure de vous connecter à l’application.

## <a name="user-not-assigned-a-role"></a>Utilisateur non affecté à un rôle

*Erreur AADSTS50105 : L’utilisateur connecté « brian@contoso.com » n’est pas affecté à un rôle pour l’application*

**Cause possible**

L’utilisateur ne dispose pas des autorisations nécessaires pour accéder à l’application dans Azure AD.

**Résolution :**

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

10. Tapez **le nom complet** ou **l’adresse de messagerie** de l’utilisateur souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après quelques instants, les utilisateurs que vous avez sélectionnés seront en mesure de démarrer ces applications à l’aide des méthodes décrites dans la section de description des solutions.

## <a name="not-a-valid-saml-request"></a>Demande SAML non valide

*Erreur AADSTS75005 : La demande n’est pas un message de protocole Saml2 valide.*

**Cause possible**

Azure AD ne prend pas en charge les demandes SAML envoyées par l’application pour l’authentification unique. Voici certains problèmes courants :

-   Des champs obligatoires sont manquants dans la demande SAML

-   La méthode de demande SAML encodée

**Résolution :**

1.  Capturez la demande SAML. Pour savoir comment capturer la demande SAML, suivez le didacticiel [Comment déboguer une authentification unique SAML pour des applications dans Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

2.  Contactez le fournisseur de l’application et communiquez-lui les informations suivantes :

    -   Demande SAML

    -   [Spécifications du protocole SAML d’authentification unique Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Il doit confirmer sa prise en charge de l’implémentation SAML Azure AD pour l’authentification unique.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Aucune ressource dans la liste requiredResourceAccess

*Erreur AADSTS65005 : L’application cliente a demandé l’accès à la ressource « 00000002-0000-0000-c000-000000000000 ». La demande a échoué, car le client n’a pas spécifié cette ressource dans sa liste requiredResourceAccess*.

**Cause possible**

L’objet d’application est endommagé.

**Résolution :**

Pour résoudre ce problème, supprimez l’application du répertoire. Ensuite, pour ajouter et reconfigurer l’application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Cliquez sur **Supprimer** dans le coin supérieur gauche du volet **Vue d’ensemble** de l’application.

8.  Actualisez Azure AD et ajoutez l’application à partir de la galerie Azure AD. Ensuite, reconfigurez l’application.

Une fois l’application reconfigurée, vous devez être en mesure de vous connecter à l’application.

## <a name="certificate-or-key-not-configured"></a>Certificat ou clé non configuré(e)

Erreur AADSTS50003 : Aucune clé de signature configurée.

**Cause possible**

L’objet d’application est endommagé et Azure AD ne reconnaît pas le certificat configuré pour l’application.

**Résolution :**

Pour supprimer et créer un nouveau certificat, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

9.  Sélectionnez une date d’expiration. Cliquez ensuite sur **Enregistrer**.

10. Cochez la case **Activer le nouveau certificat** pour substituer le certificat actif. Ensuite, cliquez sur **Enregistrer** en haut du volet, puis acceptez d’activer le certificat de substitution.

11. Dans la section **Certificat de signature SAML**, cliquez sur **Supprimer** pour supprimer le certificat **Inutilisé**.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problème lors de la personnalisation des revendications SAML envoyées à une application

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez l’article [Mappage des revendications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
[Spécifications du protocole SAML d’authentification unique Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
