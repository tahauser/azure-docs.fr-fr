---
title: Prise en main de Azure Active Directory | Microsoft Docs
description: "Obtenir des licences, ajouter un nom de domaine, créer une page de connexion personnalisée et ajouter la réinitialisation du mot de passe libre-service dans Azure Active Directory"
keywords: 
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.prod: 
ms.service: active-directory
ms.workload: identity
ms.technology: 
ms.assetid: 
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: 9e1a7337c2477455aa0b56f2147f46f3bf5293a8
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-ad"></a>Prise en main de Azure AD
La gestion moderne des identités requiert une fiabilité évolutive et cohérente pour garantir la disponibilité des applications et des services uniquement aux utilisateurs authentifiés. Pour prendre correctement en charge les besoins de gestion d’identité des utilisateurs, le service informatique doit disposer d’un moyen de fournir un accès aux applications SaaS publics approuvées, un moyen d’accueillir une ligne interne d’applications métiers et même des moyens pour améliorer le développement et l’utilisation d’applications locales. Toutes ces exigences montrent la nécessité de disposer d’une solution de gestion d’identité basée sur le cloud.      

Azure Active Directory (Azure AD) est le service Microsoft de gestion des annuaires et des identités, basé sur le cloud, mutualisé. Azure AD associe des services d’annuaires principaux, une gouvernance avancée des identités et une gestion des accès aux applications. La conception d’Azure AD pour l’architecture mutualisée géolocalisée à haute disponibilité signifie que vous pouvez vous reposer sur cette solution pour répondre à vos besoins les plus critiques.

Azure AD comprend une suite complète de fonctionnalités de gestion d’identité, notamment la possibilité de synchroniser les informations sur les ressources locales, une personnalisation de la marque de la société, une gestion simple des licences et même la gestion des mots de passe en libre-service. Ces fonctions simples à déployer peuvent aider à prendre en main Azure AD pour sécuriser les applications basées sur le cloud, simplifier les processus informatiques, réduire les coûts et garantir le respect des objectifs de conformité aux normes du secteur.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

Le reste de cet article vous indique comment prendre en main Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>S’inscrire à Azure Active Directory Premium
Pour la [prise en main de Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md), vous pouvez acheter des licences et les associer à votre abonnement Azure. Si vous créez un nouvel abonnement Azure, vous devrez également activer votre plan de licences et l’accès au service Azure AD. 

Vous disposez de plusieurs options pour vous inscrire à Active Directory Premium : 

- Utiliser votre abonnement Azure ou Office 365.
- Utiliser une offre de licences Enterprise Mobility + Security.
- Utiliser une offre de licences en volume Microsoft.

### <a name="verification-step"></a>Étape de vérification
Après avoir activé l’abonnement, vérifiez que vous pouvez vous connecter au service.

## <a name="add-a-custom-domain-name"></a>Ajouter un nom de domaine personnalisé
Chaque annuaire Azure AD est fourni avec un nom de domaine initial au format suivant : *nom_de_domaine*.onmicrosoft.com. Le nom de domaine initial ne peut pas être modifié ni supprimé. Toutefois, vous pouvez également [ajouter votre nom de domaine d’entreprise à Azure AD](add-custom-domain.md). Par exemple, il se peut que votre organisation dispose d’autres noms de domaine dans le cadre de ses activités et que les utilisateurs se connectent à l’aide de votre nom de domaine d’entreprise. L’ajout de noms de domaine personnalisés à Azure AD vous permet d’attribuer des noms d’utilisateurs avec lesquels vos utilisateurs sont familiarisés, comme « alice@contoso.com ». plutôt que « alice@.onmicrosoft.com ». Le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre annuaire.
2. Ajoutez une entrée DNS pour le nom de domaine au niveau du bureau d’enregistrement de noms de domaine.
3. Vérifiez le nom de domaine personnalisé dans Azure AD.

### <a name="verification-step"></a>Étape de vérification
Après avoir ajouté un domaine personnalisé, assurez-vous qu’il possède l’état **Vérifié** affiché sur le panneau **Noms de domaine personnalisés** du portail Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Ajouter votre marque à votre page de connexion 
Pour éviter toute confusion, de nombreuses entreprises veulent que tous les sites Web et services qu’elles gèrent aient un aspect similaire. Azure Active Directory (AD) offre cette possibilité en vous permettant de [personnaliser l’apparence la page de connexion pour qu’elle affiche le logo de votre société et sa palette de couleurs personnalisée](customize-branding.md). La page de connexion s’affiche lorsque vous vous connectez à Office 365 ou à d’autres applications Web qui utilisent Azure AD comme fournisseur d’identité. Vous interagissez avec cette page pour saisir vos informations d’identification.

### <a name="verification-step"></a>Étape de vérification
Connectez-vous au portail Azure et assurez-vous que votre page de connexion personnalisée ainsi que toutes les personnalisations de langues supplémentaires ont été configurées correctement. 

## <a name="add-new-users"></a>Ajouter de nouveaux utilisateurs
Vous pouvez [ajouter de nouveaux utilisateurs au Azure AD de votre organisation](add-users-azure-active-directory.md) un par un à l’aide du portail Azure ou en synchronisant vos données de ressources locales de Windows Server AD. Vous pouvez ajouter des utilisateurs basés sur le cloud directement depuis le portail Azure AD ou en synchronisant les informations des utilisateurs locaux.

Pour activer la synchronisation d’identité locale avec Azure AD, vous devez installer et configurer [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) sur un serveur de votre organisation. Cette application gère la synchronisation des utilisateurs et des groupes à partir de votre source d’identité existante avec votre client Azure AD.

### <a name="verification-step"></a>Étape de vérification
Après la création ou la synchronisation des nouveaux utilisateurs, assurez-vous qu’ils sont visibles dans Azure AD.

## <a name="assign-licenses"></a>Attribuer des licences
Bien qu’il suffise d’obtenir un abonnement pour configurer les fonctionnalités payantes, vous devez quand même [attribuer des licences utilisateur](license-users-groups.md) pour les fonctionnalités Azure AD Premium payantes. Une licence doit être attribuée à tout utilisateur ayant besoin d’accéder à une fonctionnalité Azure AD payante ou géré par le biais de ce type de fonctionnalité. L’attribution de licence est un mappage entre un utilisateur et un service acheté, comme Azure AD Premium, Azure AD Basic ou Enterprise Mobility + Security.

Vous pouvez utiliser l’attribution de licence basée sur le groupe pour configurer des règles comme dans les exemples suivants :

- Tous les utilisateurs de votre annuaire obtiennent automatiquement une licence
- Toute personne avec le poste approprié obtient une licence
- Vous pouvez déléguer la décision à d’autres responsables au sein de l’organisation (à l’aide de groupes en libre-service)

### <a name="verification-step"></a>Étape de vérification
Revoyez les licences attribuées et disponibles sous **Azure Active Directory** > **Licences** > **Tous les produits**.

## <a name="configure-self-service-password-reset"></a>Configurer la réinitialisation du mot de passe libre-service
Grâce à la [réinitialisation de mot de passe en libre-service](active-directory-passwords-getting-started.md), les administrateurs informatiques disposent d’un moyen simple pour permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes ou leurs mots de passe. Le système inclut des rapports détaillés de suivi d’utilisation ainsi que des notifications pour vous prévenir de toute utilisation malveillante ou de tout abus.

### <a name="verification-step"></a>Étape de vérification
Revoyez les propriétés SSPR activées sous **Azure Active Directory** > **Réinitialisation du mot de passe** pour vous assurer que les attributions correctes d’utilisateur et de groupe sont ont été effectuées. 


## <a name="next-steps"></a>Étapes suivantes
[Page du service Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

[Page d’informations de tarification relatives à Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)