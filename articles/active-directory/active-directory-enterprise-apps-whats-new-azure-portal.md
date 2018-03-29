---
title: Nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 8d1b24708380aeed6055912fcf3538f0e5319e2d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Nouveautés relatives à la gestion des applications d’entreprise dans Azure Active Directory 

Azure Active Directory (Azure AD) propose des outils améliorés de gestion des applications d’entreprise, avec des fonctionnalités inédites permettant de simplifier et d’optimiser l’efficacité des applications.

Les améliorations apportées à Azure AD dans le [portail Azure](https://portal.azure.com) sont notamment les suivantes.

- Expérience de galerie d’applications améliorée, avec modèle de création d’application simplifié et prise en charge de tous les types d’applications auxquels vous êtes habitué. 
- Nouvelle expérience de démarrage rapide avec un pilote de votre application pour vous aider à agir. 
- Configuration de stratégies en libre-service en quelques clics. 
- Améliorations du proxy d’application, de la configuration de l’authentification unique et des fonctions d’apport de votre propre application, vous permettant d’être plus productif qu’auparavant.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Amélioration de la galerie d’applications Azure Active Directory

Ajoutez vos applications préférées, qu’elles proviennent de la [Galerie d’applications](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), qu’il s’agisse d’applications personnalisées que vous étendez au cloud ou qu’il s’agisse de nouvelles applications que vous développez.  Pour commencer à utiliser cette nouvelle capacité, cliquez sur **Ajouter** dans la vue d’ensemble **Applications d’entreprise** ou dans le panneau **Toutes les applications**.
 
  ![Ajout d'une application](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Une fois dans la galerie, toutes les applications recommandées prenant en charge l’approvisionnement d’utilisateurs apparaissent au premier plan au centre.  Vous pouvez explorer les différentes catégories d’applications qui vous intéressent ou utiliser la fonction de recherche pour trouver rapidement des applications à intégrer.

  ![Galerie d’applications](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Ajout d’applications personnalisées à partir d’un seul emplacement

En plus d’ajouter des applications pré-intégrées à partir de la galerie, vous pouvez maintenant accéder à toutes les fonctions de configuration d’application personnalisée auxquelles vous étiez habitué dans le portail de gestion classique. Vous pouvez effectuer toutes les actions à partir de ce nouveau portail unique : étendre une application locale à l’aide du proxy d’application, intégrer votre propre application avec authentification unique fédérée ou mot de passe, ou créer une toute nouvelle application à l’aide du Registre d’application.

  ![Ajout de votre propre application](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Pour ajouter votre propre application** :

1. Cliquez sur le lien **add your own** (ajoutez votre propre application) en haut de la galerie d’applications. 
2. Deux options vous sont proposées : **Deploying an existing application** (Déployer une application existante) ou **Developing a new application** (Développer une nouvelle application). Découvrez ci-dessous la différence entre ces deux options et leur utilisation.

### <a name="deploying-existing-applications"></a>Déploiement d’applications existantes

1. Si votre application est déjà en cours d’exécution et que vous souhaitez simplement l’intégrer dans Azure AD pour l’authentification unique ou l’approvisionnement, choisissez l’option **Deploying an existing application** (Déployer une application existante). Choisissez un nom pour votre application, cliquez sur **Add** (Ajouter).
2. Et voilà ! Plus besoin de connaître tous les détails relatifs à votre application en amont : vous pouvez maintenant configurer le fonctionnement de votre nouvelle application en explorant le menu à gauche et en configurant l’application comme vous le souhaitez à tout moment.

  ![Ajout d’une application existante en un seul clic](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Développement de nouvelles applications

1. Si vous développez une nouvelle application, vous pouvez accéder au Registre d’application directement à partir de la galerie :
2. Cliquez sur le lien **add your own** (ajoutez votre propre application) à partir de la galerie d’applications, sélectionnez l’option **Developing a new application** (Développer une nouvelle application) et vous verrez un lien d’accès rapide à droite de la fonction d’ajout d’application.

  ![Ajout d’une application venant d’être développée en quelques clics](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Lorsque vous ajoutez une application à l’aide du Registre d’application, vous la voyez apparaître dans la liste des applications d’entreprise où vous pouvez configurer l’authentification unique et gérer les stratégies d’accès de votre nouvelle application.

  ![Gestion de l’accès à votre nouvelle application dans Enterprise Applications (Applications d’entreprise)](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Démarrage rapide : utilisez immédiatement votre nouvelle application 

Après avoir ajouté une application, qu’elle soit à vous ou pré-intégrée, vous pouvez rapidement vous familiariser avec la nouvelle expérience d’applications grâce à la fonction de démarrage rapide personnalisée que nous avons créée. Suivez systématiquement chaque option pour être guidé à travers l’interface utilisateur et découvrir rapidement le fonctionnement avec un pilote de votre nouvelle application. 
 
  ![Expérience de démarrage rapide des nouvelles applications](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Vous pouvez accéder à cette nouvelle expérience de démarrage rapide à tout moment et pour toute application, en cliquant sur **Quick start** (Démarrage rapide) dans le menu de navigation à gauche de l’application.


## <a name="updated-application-proxy-configuration"></a>Mise à jour de la configuration du proxy d’application
À présent, supposons que l’une des nouvelles applications ajoutées est exécutée dans votre environnement local et que vous souhaitez l’intégrer à Azure AD.  L’un des aspects les plus intéressants de la nouvelle expérience de configuration d’application au sein du nouveau portail Azure AD est qu’elle fractionne le mode d’authentification de l’application à partir de sa configuration de proxy d’application. Ainsi, vous pouvez facilement exposer les applications fédérées ou à authentification unique par mot de passe exécutées dans votre réseau d’entreprise directement dans le cloud, sans avoir à créer plusieurs instances de ces applications.

En outre, vous pouvez maintenant configurer les nouvelles applications ajoutées pour être utilisées avec le proxy d’application Azure AD directement à partir du nouveau portail, y compris les applications qui prennent en charge des expériences d’authentification Windows natives.

  ![Configuration d’une application pour utiliser l’option Authentification Windows intégrée](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Pour configurer une application d’authentification Windows native avec le proxy d’application :
1. Cliquez sur l’élément de navigation Single sign-on (Authentification unique) et choisissez **Authentification Windows intégrée** à partir du panneau des paramètres d’authentification et configurez les paramètres à votre convenance.
2. Outre ces nouveaux modes d’authentification pris en charge, vous pouvez maintenant charger des certificats à partir de domaines personnalisés pour prendre en charge les applications qui s’exécutent sur des points de terminaison sécurisés au sein de votre organisation.  
 
   ![Chargement d’un certificat à utiliser avec le proxy d’application](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Pour charger un nouveau certificat pour votre application locale préférée, cliquez sur l’option **Application proxy** (Proxy d’application) à partir du menu de navigation à gauche, cliquez sur le sélecteur **Certificate** (Certificat) et chargez un fichier de certificat que nous pouvons utiliser pour chiffrer les demandes à partir de notre point de terminaison cloud dans votre application.

## <a name="advanced-federated-single-sign-on-configuration"></a>Configuration d’une authentification unique fédérée avancée

Si vous utilisez actuellement des applications fédérées, vous disposez de nombreuses nouvelles fonctionnalités dans le panneau de configuration de l’authentification unique basée sur SAML. Tout d’abord, vous pouvez maintenant personnaliser, ajouter, supprimer et mapper des attributs utilisateur existants émis en tant que revendications dans des jetons SAML.
 
  ![Personnalisation des attributs utilisateur du jeton SAML transmis à une application fédérée](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Pour contrôler la nouvelle configuration d’authentification unique fédérée :
1. Ouvrez le panneau **Single sign-on** (Authentification unique) d’une application fédérée à partir du menu de navigation de gauche et vérifiez que le mode *SAML-based sign-on** (Authentification basée sur SAML) est sélectionné. 
2. Ensuite, activez la case sous le titre **User Attributes** (Attributs utilisateur) afin de modifier tous les attributs inclus dans le jeton SAML transmis à cette application.

Vous pouvez également créer, substituer et gérer les certificats utilisés pour l’authentification unique fédérée, ainsi que modifier les personnes informées lorsque votre certificat est sur le point d’expirer. Ces nouvelles options apparaîtront sous le titre **Certificates** (Certificats) dans le même panneau d’authentification unique.
 
  ![Création d’un nouveau certificat, personnalisation de l’e-mail de notification d’expiration et des options de signature du certificat](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Paramètre Relay State (État du relais)
Enfin, nous avons également étendu l’ensemble des paramètres URL SAML pris en charge afin d’inclure le **paramètre d’état du relais**, qui est la page à laquelle vos utilisateurs accèdent au sein d’une application fédérée une fois la connexion effectuée. Il est très utile de configurer ce paramètre si vous voulez diriger les utilisateurs vers un emplacement spécifique dans l’application pour qu’ils démarrent rapidement.

  ![Configuration du paramètre d’état du relais SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Pour configurer le paramètre d’état du relais** :

1. Activez la case **Show advanced URL settings** (Afficher les paramètres URL avancés) sous le titre **Domain and URLs** (Domaine et URL) dans le panneau de configuration de l’authentification unique. 
2. Une fois cela effectué, un nouvel ensemble de zones de saisie d’URL apparaît. Il vous permet de définir ce paramètre URL SAML ainsi que d’autres.

## <a name="bring-your-own-password-sso-applications"></a>Apport de vos propres applications avec authentification unique par mot de passe

Nous savons que toutes les applications ne prennent pas immédiatement en charge la fédération. Par exemple, l’une des nouvelles applications ajoutées propose peut-être un écran de connexion personnalisé qui invite vos utilisateurs à utiliser un nom d’utilisateur et un mot de passe pour se connecter. Vous pouvez toujours intégrer ces types d’applications avec Azure AD à l’aide de notre fonctionnalité **Bring your own applications** (Apportez vos propres applications) que vous pouvez désormais configurer dans le nouveau portail.
 
  ![Intégration d’applications personnalisées de mise au coffre des mots de passe avec Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Pour contrôler la fonctionnalité « Bring your own applications »** :

1. Après avoir défini le mode d’authentification unique pour une application personnalisée ajoutée à **Authentification par mot de passe**, entrez l’URL de rendu de l’écran de connexion à l’application, puis cliquez sur **Save** (Enregistrer).  
2. Une fois cela effectué, nous décomposons automatiquement cette URL pour afficher les zones de saisie du mot de passe et du nom d’utilisateur. Vous pouvez ensuite utiliser Azure AD pour transmettre en toute sécurité les mots de passe à cette application à l’aide de l’extension de navigateur du volet accès.

## <a name="configure-self-service-application-access"></a>Configuration de l’accès aux applications en libre-service

Une fois que vous avez ajouté un grand nombre de nouvelles applications, vous pouvez permettre aux utilisateurs de parcourir et d’ajouter ces applications à leurs propres volets d’accès, sans avoir à être administrateur. Dans cette nouvelle version, vous pouvez désormais configurer et gérer l’accès aux applications en libre-service directement à partir du nouveau portail.

  ![Configuration de l’accès aux applications en libre-service pour une application avec authentification unique par mot de passe](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Pour configurer et gérer l’accès aux applications en libre-service** :

1. Pour commencer, vous pouvez sélectionner l’option **Libre-service** à partir du menu de navigation à gauche de l’application et définissez l’option **Allow users to request access to this application?** (Autoriser les utilisateurs à demander l’accès à cette application ?) sur **Yes** (Oui). 
2. Cela vous permet de spécifier les personnes autorisées à approuver l’accès à cette application, et à quel groupe les utilisateurs du libre-service sont ajoutés. En outre, si l’application est configurée pour l’authentification unique par mot de passe, une autre option vous permet également d’autoriser si vous le souhaitez ces approbateurs à gérer les mots de passe affectés à l’application.

## <a name="feedback"></a>Commentaires

Nous espérons que vous appréciez votre expérience d’Azure AD améliorée. Continuez à nous faire part de vos commentaires. Publiez vos commentaires et vos idées d’amélioration dans la section du **portail Admin** de notre [forum de commentaires](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nous sommes très heureux de créer chaque jour des nouveautés et de pouvoir bénéficier de vos commentaires pour concevoir et définir de nouvelles créations.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md).



