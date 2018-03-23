---
title: Comment obtenir une certification AppSource pour Azure Active Directory | Microsoft Docs
description: Plus d’informations sur l’obtention de votre application AppSource certifié pour Azure Active Directory.
services: active-directory
documentationcenter: ''
author: andretms
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5601ad80e271364fec519cf34bcdc2f650f3bb92
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Comment obtenir une certification AppSource pour Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) est une destination pour les utilisateurs professionnels permettant de découvrir, d’essayer et de gérer des applications SaaS métier (applications SaaS autonomes et module complémentaire pour des produits SaaS Microsoft existant).

Pour répertorier une application SaaS autonome sur AppSource, votre application doit accepter l’authentification unique des comptes professionnels d’une société ou d’une organisation qui dispose d’Azure Active Directory. Le processus de connexion doit utiliser les protocoles [OpenID Connect](./active-directory-protocols-openid-connect-code.md) ou [OAuth 2.0](./active-directory-protocols-oauth-code.md). L’intégration SAML n’est pas acceptée pour la certification AppSource.

## <a name="guides-and-code-samples"></a>Guides et exemples de code
Si vous souhaitez en savoir plus sur la façon d’intégrer votre application à Azure Active Directory à l’aide d’Open ID Connect, suivez nos guides et exemples de code dans la section [Prise en main](./active-directory-developers-guide.md#get-started "d’Azure Active Directory pour les développeurs").

## <a name="multi-tenant-applications"></a>Applications multilocataires

Une application qui accepte les connexions des utilisateurs de toutes les entreprises ou organisations qui disposent d’Azure Active Directory sans qu’une instance, une configuration ou un déploiement distincts ne soient nécessaires est appelée une *application multilocataire*. AppSource recommande que les applications implémentent une architecture mutualisée pour activer l’expérience d’essai gratuit *d’un seul clic*.

Pour activer une architecture mutualisée sur votre application :
- Définissez la propriété `Multi-Tenanted` sur `Yes` dans les informations d’inscription de votre application dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (par défaut, les applications créées dans le portail Azure sont configurées en tant que *locataires uniques*).
- Mettez à jour votre code pour envoyer des requêtes au point de terminaison « `common` » (mettez à jour le point de terminaison de *https://login.microsoftonline.com/{yourtenant}* vers *https://login.microsoftonline.com/common*)
- Pour certaines plateformes, comme ASP.NET, vous devez également mettre à jour votre code afin d’accepter plusieurs émetteurs.

Pour plus d’informations sur l’architecture mutualisée, consultez : [Comment connecter un utilisateur Azure Active Directory (AD) à l’aide du modèle d’application mutualisée](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Applications à locataire unique
Les applications qui acceptent uniquement les connexions des utilisateurs d’une instance Azure Active Directory définie sont appelées *applications à locataire unique*. Les utilisateurs externes (y compris les comptes professionnels ou scolaires d’autres organisations ou les comptes personnels) peuvent se connecter à une application à locataire unique après l’ajout de chaque utilisateur en tant que *compte invité* à l’instance Azure Active Directory auprès de laquelle l’application est inscrite. Vous pouvez ajouter des utilisateurs en tant que comptes invités à Azure Active Directory via la [*collaboration Azure AD B2B*](../active-directory-b2b-what-is-azure-ad-b2b.md), et cela peut être effectué [par programmation](../active-directory-b2b-code-samples.md). Lorsque vous ajoutez un utilisateur en tant que compte invité à Azure Active Directory, un e-mail d’invitation est envoyé à l’utilisateur, qui doit accepter l’invitation en cliquant sur le lien présent dans cet e-mail. Les invitations qui sont envoyées à un utilisateur supplémentaire dans une organisation hôte qui est également membre de l’organisation partenaire ne doit pas accepter d’invitation pour se connecter.

Les applications à locataire unique peuvent activer l’expérience *Me contacter*, mais si vous souhaitez activer l’expérience d’essai gratuit/d’un simple clic qu’AppSource recommande, activez l’architecture mutualisée de votre application à la place.


## <a name="appsource-trial-experiences"></a>Expérience d’essai gratuit AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Essai gratuit (expérience d’essai gratuit menée par le client) 
*L’essai gratuit mené par le client* est l’expérience recommandée par AppSource, car elle offre un accès d’un simple clic à votre application. Vous trouverez ci-dessous une illustration de cette expérience :<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Un utilisateur trouve votre application sur le site web AppSource.</li><li>Il sélectionne l’option « Essai gratuit ».</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource redirige l’utilisateur vers une URL de votre site web.</li><li>Votre site web lance le processus <i>d’authentification unique</i> automatiquement (au chargement de la page).</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>L’utilisateur est redirigé vers la page de connexion à Microsoft.</li><li>L’utilisateur fournit des informations d’identification pour se connecter.</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>L’utilisateur donne son consentement pour votre application.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Le processus de connexion se termine et l’utilisateur est redirigé vers votre site web.</li><li>L’utilisateur commence l’essai gratuit.</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Me contacter (expérience d’essai gratuit menée par le partenaire)
*L’expérience d’essai gratuit menée par le partenaire* peut être utilisée quand une opération manuelle ou à long terme doit se produire pour approvisionner l’utilisateur/la société : par exemple, votre application doit approvisionner des machines virtuelles, des instances de base de données ou des opérations prenant beaucoup de temps. Dans ce cas, après que l’utilisateur a sélectionné le bouton *« Demander une version d’évaluation »* et a rempli un formulaire, AppSource vous envoie les informations de contact de l’utilisateur. Suite à la réception de ces informations, vous pouvez approvisionner l’environnement et envoyer les instructions à l’utilisateur pour qu’il puisse accéder à l’expérience d’essai gratuit :<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Un utilisateur trouve votre application sur le site web AppSource.</li><li>Il sélectionne l’option « Me contacter ».</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Il remplit un formulaire avec ses informations de contact.</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Vous recevez les informations de l’utilisateur.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Configurez l’environnement.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Contactez l’utilisateur avec les informations relatives à l’essai gratuit.</td>
        </tr>
        </table><br/><br/>
        <ul><li>Vous recevez les informations de l’utilisateur et configurez l’instance d’essai gratuit.</li><li>Vous envoyez à l’utilisateur le lien hypertexte permettant d’accéder à votre application.</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>L’utilisateur accède à votre application et termine le processus d’authentification unique.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>L’utilisateur donne son consentement pour votre application.</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Le processus de connexion se termine et l’utilisateur est redirigé vers votre site web.</li><li>L’utilisateur commence l’essai gratuit.</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Plus d’informations
Pour plus d’informations sur l’expérience d’essai gratuit AppSource, regardez [cette vidéo](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la création d’applications qui prennent en charge les connexions Azure Active Directory, consultez [Scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios). 

- Pour plus d’informations sur comment répertorier votre application SaaS dans AppSource, consultez [les informations sur les partenaires AppSource](https://appsource.microsoft.com/partners).


## <a name="get-support"></a>Obtenir de l’aide
Pour l’intégration Azure Active Directory, nous utilisons [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) avec la communauté pour proposer de l’aide. 

Nous vous recommandons vivement de poser vos questions sur Stack Overflow d’abord et de parcourir les problèmes existants pour voir si quelqu’un d’autre a déjà posé la même question. Assurez-vous que vos questions ou commentaires portent les mentions [`[azure-active-directory]` et `[appsource]`](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->