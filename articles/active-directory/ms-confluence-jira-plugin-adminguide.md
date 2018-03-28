---
title: Guide d’administration du plug-in d’authentification unique Microsoft Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et l’authentification unique Microsoft Azure Active Directory pour JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Guide d’administration du plug-in d’authentification unique Microsoft Azure Active Directory

## <a name="table-of-contents"></a>Sommaire

1. **[VUE D’ENSEMBLE](#overview)**
2. **[FONCTIONNEMENT](#how-it-works)**
3. **[PUBLIC CIBLÉ](#audience)**
4. **[HYPOTHÈSES](#assumptions)**
5. **[PRÉREQUIS](#prerequisites)**
6. **[VERSIONS PRISES EN CHARGE DE JIRA ET CONFLUENCE](#supported-versions-of-jira-and-confluence)**
7. **[INSTALLATION](#installation)**
8. **[CONFIGURATION DU PLUGIN](#plugin-configuration)**
9. **[EXPLICATION PRATIQUE POUR L’ÉCRAN DE CONFIGURATION DU MODULE COMPLÉMENTAIRE](#field-explanation-for-add---on-configuration-screen:)**
10. **[RÉSOLUTION DES PROBLÈMES](#troubleshooting)**

## <a name="overview"></a>Vue d'ensemble

Ce module complémentaire permet aux clients Microsoft Azure AD d’utiliser leur nom d’utilisateur et mot de passe d’organisation pour se connecter à des produits basés sur Atlassian Jira et Confluence. Il implémente l’authentification unique basée sur SAML 2.0.

## <a name="how-it-works"></a>Fonctionnement

Quand les utilisateurs souhaitent se connecter à l’application Atlassian Jira ou Confluence, ils peuvent cliquer sur un bouton **Login with Azure AD (Se connecter avec Azure AD)** dans la page de connexion. Ils doivent alors se connecter avec la page de connexion Organisation Azure AD.

Une fois authentifiés, les utilisateurs doivent être en mesure de se connecter à l’application. S’ils sont déjà authentifiés avec l’ID et le mot de passe d’organisation, ils sont connectés directement à l’application. Notez également que la connexion est valable à la fois pour JIRA et Confluence. Si les utilisateurs sont connectés à l’application JIRA et que Confluence est également ouvert dans la même fenêtre de navigateur, ils ne doivent se connecter qu’une seule fois et ne sont pas obligés de fournir leurs informations d’identification pour l’autre application. Les utilisateurs peuvent également accéder au produit Atlassian par le biais de MyApps sous leur compte Azure, et ils devraient être connectés sans avoir besoin de fournir leurs informations d’identification.

> [!NOTE]
> Le provisionnement d’utilisateur n’est pas effectué à l’aide de ce module complémentaire.

## <a name="audience"></a>Public ciblé

Administrateurs JIRA et Confluence qui souhaitent utiliser ce plug-in pour activer l’authentification unique à l’aide d’Azure AD.

## <a name="assumptions"></a>Hypothèses

* L’instance de JIRA/Confluence est compatible avec le protocole HTTPS
* Les utilisateurs sont déjà créés dans JIRA/Confluence
* Les utilisateurs ont un rôle attribué dans JIRA/Confluence
* Les administrateurs ont accès aux informations nécessaires pour configurer le plug-in
* JIRA/Confluence sont disponibles également à l’extérieur du réseau d’entreprise
* Le module complémentaire fonctionne uniquement avec les versions locales de JIRA et Confluence

## <a name="prerequisites"></a>Prérequis


Prenez note des prérequis suivants avant d’effectuer l’installation du module complémentaire :

* JIRA/Confluence sont installés sur une version Windows 64 bits
* Les versions de JIRA/Confluence sont compatibles avec le protocole HTTPS
* Notez la version prise en charge pour le plug-in dans la section « Versions prises en charge » ci-dessous
* JIRA/Confluence est disponible sur internet
* Informations d’identification d’administrateur pour JIRA/Confluence
* Informations d’identification d’administrateur pour Azure AD
* WebSudo doit être désactivé dans JIRA et Confluence

## <a name="supported-versions-of-jira-and-confluence"></a>Versions prises en charge de JIRA et Confluence

Les versions suivantes de JIRA et Confluence sont actuellement prises en charge :

* JIRA Core et Software : 6.0 à 7.2.0
* JIRA Service Desk : 3.0 à 3.2
* Confluence : 5.0 à 5.10

## <a name="installation"></a>Installation

L’administrateur doit effectuer les étapes ci-dessous pour installer le plug-in :

1. Connectez-vous à votre instance de JIRA/Confluence en tant qu’administrateur.
    
2. Accédez à JIRA/Confluence Administration et cliquez sur Add-ons (Modules complémentaires).
    
3. Sur la Place de Marché Atlassian, recherchez **Microsoft SAML SSO Plugin**.
 
4. La version appropriée du module complémentaire s’affiche dans les résultats de recherche.
 
5. Sélectionnez le plug-in. UPM l’installe.
 
6. Une fois le plug-in installé, il s’affiche sous User Installed (Installé par l’utilisateur), dans la section Manage add-ons (Gérer les modules complémentaires).
 
7. Vous devez configurer le plug-in avant de pouvoir l’utiliser.
 
8. Cliquez sur le plug-in. Un bouton « Configure » apparaît.
 
9. Cliquez dessus pour fournir les entrées de configuration.
    
## <a name="plugin-configuration"></a>Configuration du plug-in

L’image suivante montre l’écran de configuration du module complémentaire dans JIRA et Confluence.
    
![configuration du module complémentaire](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>Explication pratique de l’écran de configuration du module complémentaire

1.   Metadata URL : URL pour obtenir les métadonnées de fédération à partir d’Azure AD.
 
2.   Identifier : utilisé par Azure AD pour valider la source de la requête. Correspond à l’élément Identificateur dans Azure AD. Il est dérivé automatiquement par le plug-in comme https://<domaine:port>/.
 
3.   Reply URL : utilisez cette URL de réponse dans votre fournisseur d’identité pour lancer la connexion SAML. Correspond à l’élément URL de réponse dans Azure AD. Elle est dérivée automatiquement par le plug-in comme https://<domaine:port>/plugins/servlet/saml/auth.
 
4.   Sign On URL : utilisez cette URL de connexion dans votre fournisseur d’identité pour lancer la connexion SAML. Correspond à l’élément Connexion dans Azure AD. Elle est dérivée automatiquement par le plug-in comme https://<domaine:port>/plugins/servlet/saml/auth.
 
5.   IdP Entity ID : ID d’entité utilisé par votre fournisseur d’identité. Ce champ est renseigné quand l’URL des métadonnées est résolue.
 
6.   Login URL : URL de connexion de votre fournisseur d’identité. Ce champ est renseigné à partir d’Azure AD quand l’URL des métadonnées est résolue.
 
7.   Log out URL : URL de déconnexion de votre fournisseur d’identité. Ce champ est renseigné à partir d’Azure AD quand l’URL des métadonnées est résolue.
 
8.   X.509 Certificate : certificat X.509 de votre fournisseur d’identité. Ce champ est renseigné à partir d’Azure AD quand l’URL des métadonnées est résolue.
 
9.   Login Button Name : nom du bouton de connexion que votre organisation souhaite afficher. Ce texte est présenté aux utilisateurs sur le bouton de connexion sur l’écran de connexion.
 
10.   SAML User ID Locations : emplacement où l’ID d’utilisateur est attendu dans la réponse SAML. Il peut s’agir de NameID ou d’un nom d’attribut personnalisé. Cet ID doit être l’ID utilisateur JIRA/Confluence.
 
11.   Attribute name : nom de l’attribut où l’ID utilisateur peut être attendu.
 
12.   Enable Home Realm Discovery : cochez cette case si l’entreprise utilise la connexion basée sur les services AD FS.
 
13.   Domain Name : spécifiez ici le nom de domaine si la connexion est basée sur les services AD FS.
 
14.   Enable Single Sign out : cochez cette case si vous souhaitez vous déconnecter d’Azure AD quand un utilisateur se déconnecte de JIRA/Confluence.

### <a name="troubleshooting"></a>Résolution de problèmes

* Si vous obtenez des erreurs de certificats multiples
    
    * Connectez-vous à Azure AD et supprimez les différents certificats disponibles pour l’application. Vérifiez qu’un seul certificat est présent.

* Le certificat est sur le point d’expirer dans Azure AD
    
    * Les modules complémentaires prennent soin de la substitution automatique du certificat. Quand un certificat est sur le point d’expirer, un nouveau certificat doit être marqué comme actif et le certificat inutilisé doit être supprimé. Quand un utilisateur tente de se connecter à JIRA dans ce scénario, le module complémentaire extrait le nouveau certificat et l’enregistre dans le plug-in.

* Comment désactiver WebSudo (désactiver la session administrateur sécurisée)
    
    * JIRA : les sessions administrateur sécurisées (autrement dit, confirmation du mot de passe avant d’accéder aux fonctions d’administration) sont activées par défaut. Si vous souhaitez désactiver cette fonctionnalité dans votre instance de JIRA, vous pouvez spécifier la ligne suivante dans votre fichier jira-config.properties : « ira.websudo.is.disabled = true »
    
    * Confluence : effectuez les étapes indiquées à l’adresse suivante : https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* Les champs qui sont censés être renseignés par l’URL des métadonnées ne sont pas renseignés
    
    * Vérifiez si l’URL est correcte. Vérifiez si vous avez mappé le locataire et l’ID d’application corrects.
    
    * Accédez à l’URL à partir du navigateur et vérifiez si vous recevez le XML de métadonnées de fédération.

* Erreur interne du serveur :
    
    * Parcourez les journaux présents dans le répertoire des journaux de l’installation. Si vous obtenez cette erreur quand l’utilisateur essaie de se connecter à l’aide de l’authentification unique Azure AD, vous pouvez partager les journaux avec les informations de support technique fournies plus loin dans ce document.

* Erreur « ID utilisateur introuvable » quand l’utilisateur tente de se connecter
    
    * L’utilisateur n’est pas créé dans JIRA/Confluence. Vous devez le créer.

* Erreur « Application introuvable » dans Azure AD
    
    * Vérifiez si l’URL appropriée est mappée à l’application dans Azure AD.

* Informations de support technique : écrivez-vous à l’adresse suivante : [Équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Nous vous répondrons dans les 24 à 48 heures ouvrables.
    
    * Vous pouvez également créer un ticket de support auprès de Microsoft par le biais du portail Azure.