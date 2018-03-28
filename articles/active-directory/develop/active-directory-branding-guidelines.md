---
title: Directives de personnalisation des applications | Microsoft Docs
description: Guide complet sur les ressources destinées aux développeurs pour Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: e5ce970913d767dbf6b13381cf18c1f7b05d252f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="branding-guidelines-for-applications"></a>Directives de personnalisation des applications
Cet article décrit les directives de personnalisation que vous devez suivre lors du développement d’applications avec Azure Active Directory (Azure AD). Ces directives vous permettent de diriger vos clients quand ils souhaitent utiliser leur compte professionnel ou scolaire géré dans Azure AD ou leur compte personnel pour s’inscrire et se connecter à votre application.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Comptes personnels et comptes professionnels/scolaires de Microsoft
Microsoft gère deux types de compte d’utilisateur :

* **Comptes personnels** (anciennement Windows Live ID). Ces comptes représentent la relation entre des utilisateurs *individuels* et Microsoft et sont utilisés pour accéder aux services et appareils de Microsoft destinés au grand public. Ces comptes sont prévus pour un usage personnel.
* **Comptes professionnels ou scolaires.** Ces comptes sont gérés par Microsoft pour le compte d’organisations qui utilisent Azure Active Directory. Ces comptes sont utilisés pour se connecter à Office 365 et d’autres services métier de Microsoft.

Les comptes professionnels ou scolaires Microsoft sont généralement affectés à des utilisateurs finaux (employés, étudiants, fonctionnaires) par leurs organisations (entreprise, école, administration). Ces comptes sont régis directement dans le cloud, dans la plateforme Azure AD, ou synchronisés avec Azure AD à partir d’un annuaire local tel que Windows Server Active Directory. Microsoft est le *responsable* des comptes professionnels ou scolaires, mais ces comptes sont détenus et contrôlés par l’organisation.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Faire référence aux comptes Azure AD dans votre application
Microsoft n’expose pas les utilisateurs finaux aux noms de marque Azure ou Active Directory et il doit en être de même pour vous.

* Une fois que les utilisateurs sont connectés, vous devez utiliser le nom et le logo de l’organisation autant que possible. Cela est préférable à l’utilisation de termes génériques tels que « votre organisation ».
* Quand les utilisateurs ne sont pas connectés, vous devez faire référence à leurs comptes en parlant de « comptes professionnels ou scolaires » et utiliser le logo Microsoft pour indiquer que ces comptes sont gérés par Microsoft. N’utilisez pas de termes tels que « compte d’entreprise » et « compte commercial », car ils embrouillent les utilisateurs.

## <a name="user-account-pictogram"></a>Pictogramme de compte d’utilisateur
Dans une version antérieure de ces directives, nous recommandions d’utiliser un pictogramme de « badge bleu ». Suite aux commentaires des utilisateurs et des développeurs, nous recommandons désormais d’utiliser le logo Microsoft. Cela permet aux utilisateurs de comprendre qu’ils peuvent réutiliser le compte dont ils se servent avec Office 365 ou d’autres services métier Microsoft pour se connecter à votre application.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscription et connexion avec Azure AD
Votre application peut présenter des chemins distincts pour l’inscription et la connexion, et les sections suivantes fournissent des conseils visuels pour les deux scénarios.

**Si votre application prend en charge l’inscription des utilisateurs finaux (par exemple, pour les modèles d’évaluation gratuits ou Premium)** : vous pouvez afficher un bouton de **connexion** qui permet aux utilisateurs d’accéder à votre application avec leur compte professionnel ou personnel. Azure AD affiche une invite de consentement la première fois que les utilisateurs accèdent à votre application.

**Si votre application nécessite des autorisations auxquelles seuls les administrateurs peuvent consentir ou si votre application requiert une licence d’entreprise**: vous devez séparer l’acquisition administrateur de la connexion utilisateur. Le **bouton « obtenir cette application »** redirige les administrateurs vers la connexion, après quoi ils sont invités à donner leur consentement pour le compte des utilisateurs de leur organisation. Cela présente l’avantage supplémentaire de supprimer de votre application les invites de consentement pour les utilisateurs finaux.

## <a name="visual-guidance-for-app-acquisition"></a>Aide visuelle pour l’acquisition de l’application
Le lien « obtenir l’application » doit rediriger l’utilisateur vers la page d’octroi d’accès d’Azure AD, afin de permettre à l’administrateur d’une organisation d’autoriser votre application à accéder aux données de son organisation hébergées par Microsoft. Pour plus d'informations sur la demande d'accès, consultez l'article [Intégration d'applications dans Azure Active Directory](active-directory-integrating-applications.md) .

Une fois que les administrateurs ont donné leur consentement à votre application, ils peuvent l’ajouter au lanceur d’applications Office 365 de leurs utilisateurs (accessible à partir de l’icône correspondante dans Office 365 et de [https://portal.office.com/myapps](https://portal.office.com/myapps)). Pour faire la promotion de cette fonctionnalité, vous pouvez utiliser des phrases telles que « Ajouter cette application à votre organisation » et afficher un bouton tel que celui-ci :

![Types d’application et scénarios](./media/active-directory-branding-guidelines/add-to-my-org.png)

Toutefois, nous vous recommandons de fournir un texte explicatif plutôt que de vous contenter des boutons. Par exemple : 

> *Si vous utilisez déjà Office 365 ou un autre service métier de Microsoft, vous pouvez octroyer l’accès de <your_app_name> aux données de votre organisation. Cela permet à vos utilisateurs d’accéder à <nom_de_votre_application> à l’aide de leur compte professionnel existant.*
> 
> 

## <a name="visual-guidance-for-sign-in"></a>Aide visuelle pour la connexion
Votre application doit afficher un bouton de connexion qui redirige les utilisateurs vers le point de terminaison de connexion correspondant au protocole que vous utilisez pour l’intégration à Azure AD. La section suivante fournit des détails sur l’aspect recommandé pour ce bouton.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogramme et « Se connecter avec Microsoft »
C’est l’association du logo Microsoft et des termes « Se connecter avec Microsoft » qui permet de distinguer Azure AD des autres fournisseurs d’identité que votre application peut prendre en charge. Si vous ne disposez pas d’espace suffisant pour « Se connecter avec Microsoft », vous pouvez utiliser l’abréviation « Se connecter ».

![Types d’application et scénarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Types d’application et scénarios](./media/active-directory-branding-guidelines/sign-in-light.png)

Vous pouvez également utiliser un modèle de couleurs sombres pour les boutons.

![Types d’application et scénarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Types d’application et scénarios](./media/active-directory-branding-guidelines/sign-in-dark.png)

Pour télécharger ces images afin de les utiliser dans votre application, cliquez avec le bouton droit de la souris sur celle que vous souhaitez utiliser et enregistrez-la sur votre ordinateur. 

## <a name="branding-dos-and-donts"></a>Choses à faire et à éviter en matière de personnalisation
**UTILISEZ** « compte professionnel ou scolaire » dans la combinaison avec le bouton « Se connecter avec Microsoft » pour fournir une explication supplémentaire afin d’aider les utilisateurs finaux à déterminer s’ils peuvent l’utiliser. **N’UTILISEZ PAS** de termes tels que « compte d’entreprise » ou « compte commercial ».

**N’UTILISEZ PAS** « ID Office 365 » ni « ID Azure ». Office 365 est également le nom d’une offre grand public de Microsoft qui n’utilise pas Azure AD pour l’authentification.

**N’UTILISEZ PAS** le logo Microsoft.

**N’UTILISEZ PAS** les utilisateurs finaux à la marque Azure ou Active Directory. Il est toutefois tout à fait concevable d’utiliser ces termes avec les développeurs, les professionnels de l’informatique et les administrateurs.

## <a name="navigation-dos-and-donts"></a>Choses à faire et à éviter pour la navigation
**FOURNISSEZ** aux utilisateurs un moyen de se déconnecter et de passer à un autre compte d’utilisateur. Bien que la plupart des gens aient un seul compte personnel Microsoft/Facebook/Google/Twitter, ils sont souvent associés à plus d’une organisation. La prise en charge de plusieurs utilisateurs connectés sera bientôt offerte.

