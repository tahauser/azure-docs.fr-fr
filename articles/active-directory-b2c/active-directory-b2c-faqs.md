---
title: 'Forum aux questions : Azure Active Directory B2C'
description: Forum aux questions (FAQ) sur Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d213a3f8bed5af526efba39868855b6f6727a7c0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C : Forum aux questions (FAQ) 
Cette page répond aux questions fréquemment posées sur Azure Active Directory (Azure AD) B2C. N'hésitez pas à la consulter pour vous tenir au courant des mises à jour.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Puis-je utiliser les fonctionnalités d’Azure AD B2C dans mon client Azure AD existant, basé sur les employés ?
Azure AD et Azure AD B2C sont deux offres de produits distinctes qui ne peuvent pas coexister dans le même locataire.  Un locataire Azure AD représente une organisation.  Un locataire Azure AD B2C représente une collection d’identités à utiliser avec des applications par partie de confiance.  Avec les stratégies personnalisées (en préversion publique), Azure AD B2C peut fédérer avec Azure AD, ce qui permet l’authentification des employés dans une organisation.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Puis-je utiliser Azure AD B2C pour activer la connexion à des réseaux sociaux (Facebook et Google+) dans Office 365 ?
Vous ne pouvez pas utiliser Azure AD B2C pour authentifier les utilisateurs pour Microsoft Office 365.  Azure AD est la solution Microsoft permettant de gérer l’accès des employés aux applications SaaS. Elle présente des fonctionnalités conçues à cet effet, telles que l’accès conditionnel et la gestion des licences.  Azure AD B2C fournit une plateforme de gestion des identités et des accès pour la création d’applications web et mobiles.  Quand Azure AD B2C est configuré pour établir la fédération avec un locataire Azure AD, le locataire Azure AD gère l’accès des employés aux applications qui s’appuient sur Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Que sont les comptes locaux dans Azure AD B2C ? En quoi sont-ils différents des comptes professionnels ou scolaires dans Azure AD ?
Dans un locataire Azure AD, les utilisateurs qui appartiennent au locataire se connectent avec une adresse e-mail au format `<xyz>@<tenant domain>`.  Le `<tenant domain>` est l’un des domaines vérifiés dans le locataire ou le domaine `<...>.onmicrosoft.com` initial. Ce type de compte est un compte professionnel ou scolaire.

Dans un locataire Azure AD B2C, la plupart des applications demandent que l’utilisateur se connecte avec une adresse e-mail arbitraire (par exemple, joe@comcast.net, bob@gmail.com, sarah@contoso.com ou jim@live.com). Ce type de compte est un compte local.  Nous prenons également en charge les noms d’utilisateur arbitraires en tant que comptes locaux (par exemple joe, bob, sarah ou jim). Vous pourrez choisir entre ces deux types de comptes locaux pendant la configuration des fournisseurs d’identité pour Azure AD B2C sur le Portail Azure. Dans votre client Azure AD B2C, cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Nom d’utilisateur** sous Comptes locaux. 

Les comptes d’utilisateur des applications doivent toujours être créés par le biais d’une stratégie d’inscription ou de connexion ou bien à l’aide de l’API Graph Azure AD. Les comptes d’utilisateur créés sur le Portail Azure ne servent qu’à l’administration du client.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quels fournisseurs d’identité sociaux prenez-vous en charge maintenant ? Lesquels envisagez-vous de prendre en charge à l'avenir ?
Nous prenons actuellement en charge Facebook, Google+, LinkedIn, Amazon, Twitter (aperçu), WeChat (aperçu), Weibo (aperçu) et QQ (aperçu). Nous ajouterons la prise en charge d’autres fournisseurs d’identité sociaux populaires en fonction de la demande des clients.

Azure AD B2C a également ajouté la prise en charge des [stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Ces [stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom) permettent au développeur de créer sa propre stratégie qui avec n’importe quel fournisseur d’identité prend en charge [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) ou SAML. 

Bien démarrer avec les stratégies personnalisées en consultant notre [pack de démarrage des stratégies personnalisées](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Puis-je configurer des étendues pour collecter plus d’informations sur les clients depuis différents fournisseurs d’identité sociaux ?
Non, mais cette fonctionnalité est sur notre feuille de route. Les étendues par défaut utilisées pour notre jeu de fournisseurs d'identité sociaux pris en charge sont :

* Facebook : e-mail
* Google+ : e-mail
* Compte Microsoft : profil de messagerie openid
* Amazon : profil
* LinkedIn : r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Mon application doit-elle être exécutée sous Azure pour fonctionner avec Azure AD B2C ?
Non, vous pouvez héberger votre application n'importe où (dans le cloud ou sur site). Pour interagir avec Azure AD B2C, il suffit qu’elle puisse envoyer et recevoir des requêtes HTTP sur les points de terminaison accessibles publiquement.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Je dispose de plusieurs locataires Azure AD B2C. Comment puis-je les gérer sur le portail Azure ?
Avant d’ouvrir « Azure AD B2C » dans le menu à gauche du portail Azure, vous devez basculer dans le répertoire que vous souhaitez gérer.  Changez de répertoire en cliquant sur votre identité dans le coin supérieur droit du portail Azure, puis sélectionnez un répertoire dans la liste déroulante qui s’affiche.  Pour obtenir une procédure détaillée avec des images, consultez [Accéder aux paramètres d’Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Comment puis-je personnaliser les e-mails de vérification (le contenu et le champ « De: ») envoyés par Azure AD B2C ?
Vous pouvez utiliser la [fonctionnalité de personnalisation de la société](../active-directory/customize-branding.md) pour personnaliser le contenu des e-mails de vérification. Plus précisément, ces deux éléments du message peuvent être personnalisés :

* **Logo de bannière**: affiché en bas à droite.
* **Couleur d’arrière-plan**: affiché en haut.

    ![Capture d’écran d’un e-mail de vérification personnalisée](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La signature d’e-mail contient le nom du client Azure AD B2C que vous avez fourni lors de la création du client Azure AD B2C. Vous pouvez modifier le nom à l’aide de ces instructions :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général.
1. Ouvrez le panneau **Azure Active Directory**.
1. Cliquez sur l’onglet **Propriétés**.
1. Changez le champ **Nom**.
1. Cliquez sur **Enregistrer** dans la partie supérieure de la page.

Il n’existe actuellement aucun moyen de modifier le champ « De : » de l’e-mail. Votez sur [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) si vous êtes intéressé par la personnalisation du corps de l’e-mail de vérification.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Comment puis-je migrer mes noms d’utilisateur, mots de passe et profils existants à partir de ma base de données vers Azure AD B2C ?
Vous pouvez utiliser l’API Graph Azure AD pour écrire l’outil de migration. Consultez le [guide de migration des utilisateurs](active-directory-b2c-user-migration.md) pour plus d’informations.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quelle stratégie de mot de passe est utilisée pour les comptes locaux dans Azure AD B2C ?
La stratégie de mot de passe Azure AD B2C pour les comptes locaux est basée sur la stratégie pour Azure AD. Les stratégies de réinitialisation du mot de passe, d’inscription ou de connexion et d’inscription Azure AD B2C utilisent des mots de passe « forts » et qui n’expirent pas. Pour plus d’informations, consultez [Stratégie de mot de passe dans Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Puis-je utiliser Azure AD Connect pour migrer les identités de clients stockées dans mon répertoire Active Directory local vers Azure AD B2C ?
Non, Azure AD Connect n'est pas conçu pour fonctionner avec Azure AD B2C. Utilisez l’[API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) pour la migration des utilisateurs.  Consultez le [guide de migration des utilisateurs](active-directory-b2c-user-migration.md) pour plus d’informations.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Mon application peut-elle ouvrir des pages Azure Active Directory B2C dans un iFrame ?
Non, pour des raisons de sécurité, les pages Azure AD B2C ne peuvent pas être ouvertes dans un iFrame.  Notre service communique avec le navigateur pour interdire les iFrames.  La communauté de sécurité en général et la spécification OAUTH2 déconseillent d’utiliser des iFrames pour les expériences d’identité en raison du risque d’attaques par détournement de clics.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C fonctionne-t-il avec les systèmes CRM, tels que Microsoft Dynamics ?
L’intégration avec le portail Microsoft Dynamics 365 est disponible.  Consultez [Configuration du portail Dynamics 365 pour utiliser Azure AD B2C pour l’authentification ](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C fonctionne-t-il avec SharePoint localement 2016 ou version antérieure ?
Azure AD B2C n’est pas conçu pour le scénario de partage partenaire externe SharePoint ; consultez plutôt cet article sur [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Dois-je utiliser Azure AD B2C ou B2B pour gérer les identités externes ?
Lisez cet article sur les [identités externes](../active-directory/active-directory-b2b-compare-external-identities.md) pour en savoir plus sur l’application des fonctionnalités appropriées à vos scénarios d’identités externes.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quelles sont les fonctionnalités de création de rapports et d’audit proposées par Azure AD B2C ? Sont-elles identiques à celles d’Azure AD Premium ?
Non, Azure AD B2C ne prend pas en charge le même ensemble de rapports qu'Azure AD Premium. Toutefois, de nombreux points communs existent :

* Les **rapports de connexion** sont disponibles dans le portail Azure uniquement (Azure Active Directory > Activité > Connexions), pas par le biais de l’API Graph. Ils fournissent un enregistrement de chaque connexion avec des détails réduits.
* Les **rapports d’audit** sont disponibles dans le portail Azure uniquement (Azure Active Directory > Activité > Journaux d’audit), pas par le biais de l’API Graph. Ils incluent les activités d’administration et d’application. 
* Les **rapports d’utilisation** sont disponibles par le biais de [l’API de création de rapports d’utilisation](active-directory-b2c-reference-usage-reporting-api.md) uniquement, pas par le biais du portail Azure. Ils comprennent le nombre d’utilisateurs, le nombre de connexions et le volume de MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Puis-je localiser l'interface utilisateur des pages présentées par Azure AD B2C ? Quelles sont les langues prises en charge ?
Oui.  Découvrez la [personnalisation linguistique](active-directory-b2c-reference-language-customization.md), qui est en préversion publique.  Nous fournissons des traductions en 36 langues, et vous pouvez remplacer n’importe quelle chaîne pour l’adapter à vos besoins.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Puis-je utiliser mes propres URL dans les pages d’inscription et de connexion présentées par Azure AD B2C ? Par exemple, puis-je remplacer l’URL login.microsoftonline.com par login.contoso.com ?
Pas actuellement. Cette fonctionnalité est sur notre feuille de route. La vérification de votre domaine sous l’onglet **Domaines** sur le portail Azure ne remplit pas cet objectif.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Comment supprimer mon client Azure AD B2C ?
Procédez comme suit pour supprimer votre client Azure AD B2C :

1. Suivez ces étapes pour [accéder aux paramètres d’Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
1. Accédez à **Applications**, **Fournisseurs d’identité** et à **Toutes les stratégies**, puis supprimez toutes les entrées dans chacun d’eux.
1. Connectez-vous maintenant au [portail Azure](https://portal.azure.com/) en tant qu’administrateur d’abonnements. (Utilisez le compte professionnel ou scolaire ou le compte Microsoft que vous avez utilisé pour vous inscrire à Azure.)
1. Passez au client Azure AD B2C que vous voulez supprimer.
2. Accédez au menu Active Directory sur la gauche.
3. Sélectionnez **Utilisateurs et groupes**.
4. Sélectionnez chaque utilisateur tour à tour (excluez l’utilisateur Administrateur d’abonnement sous lequel vous êtes connecté actuellement). Cliquez sur **Supprimer** en bas de la page et sur **OUI** lorsque vous y êtes invité.
5. Cliquez sur **Inscriptions des applications**.
6. Sélectionnez l’application appelée **b2c-extensions-app**. Cliquez sur **Supprimer** et sur **Oui** lorsque vous y êtes invité.
7. Sélectionnez **Vue d’ensemble**.
8. Cliquez sur **Supprimer le répertoire**. Pour terminer le processus, suivez les instructions à l’écran.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Puis-je obtenir Azure AD B2C dans le cadre d’Enterprise Mobility Suite ?
Non, Azure AD B2C est un service Azure avec paiement à l’utilisation et ne fait pas partie d’Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Comment signaler des problèmes avec Azure AD B2C ?
Consultez [Azure Active Directory B2C : dépôt de demandes de support](active-directory-b2c-support.md).
