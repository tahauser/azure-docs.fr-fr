---
title: "Configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD | Microsoft Docs"
description: "Comment configurer une application pour l’authentification unique par mot de passe lorsqu’elle est répertoriée dans la galerie d’applications Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: aa02bfd22a656024d8638226dc1c5551b63e6d42
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Comment configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD

Lorsque vous ajoutez une application à partir de la [Galerie d’applications Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery), vous pouvez choisir la façon dont vous souhaitez que vos utilisateurs s’y connectent. Vous pouvez configurer ce choix à tout moment en sélectionnant l’élément de navigation **Authentification unique** sur une application d’entreprise dans le [portail Azure](https://portal.azure.com/).

L’une des méthodes d’authentification unique disponibles est l’option [Authentification unique par mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work). Ceci est un excellent moyen pour commencer à intégrer rapidement des applications dans Azure AD et vous permet d’effectuer les opérations suivantes :

-   activer **l’authentification unique pour vos utilisateurs** par le stockage et la relecture en toute sécurité des noms d’utilisateur et mots de passe pour l’application que vous avez intégrée à Azure AD ;

-   **prendre en charge les applications qui requièrent plusieurs champs de connexion** pour les applications qui requièrent plus que les champs de nom d’utilisateur et de mot de passe pour la connexion ;

-   **personnaliser les étiquettes** des champs d’entrée de nom d’utilisateur et de mot de passe que vos utilisateurs voient dans le [volet d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) lorsqu’ils entrent leurs informations d’identification ;

-   autoriser vos **utilisateurs** à fournir leurs propres noms d’utilisateur et mots de passe de tout compte d’application existant qu’ils saisissent manuellement dans le [volet d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ;

-   autoriser un **membre du groupe d’entreprise** à spécifier les noms d’utilisateur et mots de passe affectés à un utilisateur à l’aide de la fonctionnalité [Accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) ;

-   autoriser un **administrateur** à spécifier les noms d’utilisateur et mots de passe affectés à un utilisateur à l’aide de la fonctionnalité Mettre à jour les informations d'identification lors de [l’affectation d’un utilisateur à une application](#assign-a-user-to-an-application-directly) ;

-   autoriser un **administrateur** à spécifier les noms d’utilisateur et mots de passe partagés utilisés par un groupe de personnes à l’aide de la fonctionnalité Mettre à jour les informations d'identification lors de [l’affectation d’un groupe à une application](#assign-an-application-to-a-group-directly).

La section ci-après décrit la procédure d’activation de la fonctionnalité [Authentification unique avec mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) pour une application qui figure déjà dans la [galerie d’applications Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

## <a name="overview-of-steps-required"></a>Vue d’ensemble des étapes requises
Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-application-for-password-single-sign-on)

-   [Affecter l’application à un utilisateur ou à un groupe](#assign-the-application-to-a-user-or-a-group)

    -   [Affecter un utilisateur directement à une application](#assign-a-user-to-an-application-directly)

    -   [Affecter une application directement à un groupe](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, procédez comme suit :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

Après un court délai, vous pouvez voir apparaître le volet de configuration de l’application.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  [Affectez des utilisateurs à l’application](#assign-a-user-to-an-application-directly).

10. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs doivent entrer les informations d’identification eux-mêmes lors du lancement.

## <a name="assign-a-user-to-an-application-directly"></a>Affecter un utilisateur directement à une application

Pour affecter un ou plusieurs utilisateurs directement à une application, procédez comme suit :

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

## <a name="assign-an-application-to-a-group-directly"></a>Affecter une application directement à un groupe

Pour affecter un ou plusieurs groupes directement à une application, procédez comme suit :

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

10. Tapez le **nom de groupe complet** du groupe souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur le **groupe** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo du groupe pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs groupes**, entrez un autre **nom de groupe complet** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter ce groupe à la liste **Sélectionné**.

13. Lorsque vous avez fini de sélectionner les groupes, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux groupes que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux groupes sélectionnés.

Après une courte période, les utilisateurs que vous avez sélectionnés sont en mesure de lancer ces applications dans le panneau d’accès.

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)
