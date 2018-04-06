---
title: Guide de publication Cortana Intelligence AppSource | Microsoft Docs
description: En tant que partenaire Microsoft, voici toutes les étapes à suivre pour publier votre solution Cortana Intelligence sur AppSource.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams
ms.openlocfilehash: 1a5671eda967c1bba585d31b001bb465526709f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Guide de publication Cortana Intelligence AppSource

## <a name="overview"></a>Vue d'ensemble
AppSource constitue la destination unique pour les décideurs d’entreprise, afin de découvrir et de tester en toute fluidité les solutions/applications professionnelles générées par les partenaires et évaluées par Microsoft. Regardez [cette vidéo](https://youtu.be/hpq_Y9LuIB8) pour découvrir comment AppSource fonctionne. 

En tant que partenaire Microsoft, vous pouvez réellement tirer parti de la publication sur AppSource si :
- vous avez créé une solution/application intelligente à l’aide de [Cortana Intelligence Suite](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable) ;
- votre solution ou votre application répond à un problème métier spécifique ;
- vous avez créé des modules ou une propriété intellectuelle que vos clients peuvent réutiliser relativement rapidement de manière prévisible.

Examinons les [solutions Cortana Intelligence](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) déjà publiées sur AppSource. 

Cet article passe en revue les étapes permettant d’obtenir la solution Cortana Intelligence d’un partenaire publiée sur AppSource

## <a name="getting-started"></a>Prise en main
1. Consultez la page 9 du guide [Partner Community Benefits Guide](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF) pour être répertorié en tant que partenaire d’analyse avancée.
1. Remplissez le formulaire de [soumission de votre application](https://appsource.microsoft.com/en-us/partners/list-an-app).

    En réponse à la question *« Choisissez les produits pour lesquels votre application est conçue* », cochez la case **Autres** et sélectionnez « Cortana Intelligence » dans le contrôle d’édition.
1. Pour qu’une application Cortana Intelligence puisse être intégrée à AppSource, elle doit être certifiée par l’équipe en charge des technologies de solution partenaire de Cortana Intelligence. Pour démarrer ce processus, veuillez partager plus d’informations sur votre application en renseignant le formulaire d’enquête sur la page [d’évaluation des solutions Cortana Intelligence pour la publication sur AppSource](https://aka.ms/cisappsrceval). Ce site est protégé par un mot de passe et comporte des instructions sur la façon d’obtenir le mot de passe.

## <a name="solution-evaluation-criteria"></a>Critères d’évaluation de la solution
Voici la liste des critères que l’application doit remplir
1. L’application doit répondre à un problème métier spécifique lié à un cas d’usage au sein d’une zone fonctionnelle donnée. Ce problème doit pouvoir être répété avec une configuration minimale pour des propositions de valeur prédéfinies applicables sur une courte période.
1. La solution doit utiliser au moins l’un des composants suivants :

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Bot Framework
    - Analysis Services
    - Microsoft R Server autonome
    - R Services dans SQL 2016 ou HDInsight Premium
1. La solution doit générer au moins 1 000 $ par mois et par client à l’aide de DPOR/CSP.
1. La solution doit utiliser l’authentification unique fédérée Azure Active Directory (SSO fédérée AAD) avec le consentement activé pour l’authentification des utilisateurs et les contrôles d’accès aux ressources. Vous devez montrer à l’équipe d’évaluation que votre solution est compatible avec la SSO fédérée AAD afin que votre application puisse être intégrée à AppSource.

     Pour voir ce que signifie être compatible avec la SSO fédérée AAD, regardez la vidéo de [présentation de l’expérience d’évaluation d’AppSource](https://aka.ms/trialexperienceforwebapps) à 02:35. Si votre application n’est pas compatible avec la SSO fédérée AAD, voici quelques documents à ce propos
   1. [Connexion d’un seul clic](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application).
     
1. Utilisez Power BI dans votre solution : cela est facultatif mais fortement recommandé, car il est prouvé que cela génère un plus grand nombre de leads.

## <a name="devcenter-account-setup"></a>Configuration du compte DevCenter
Voici comment inscrire votre entreprise pour qu’elle devienne un éditeur auprès de Microsoft. Si vous êtes déjà un éditeur inscrit avec un compte DevCenter existant, partagez l’ID de courrier électronique associé à votre compte DevCenter. Une fois que la publication de votre application est approuvée, vous avez accès à la documentation complète sur le [profil du serveur de publication Cloud Portal Manage](https://cloudpartner.azure.com/#documentation/manage-publisher-profile).

Si vous n’en avez pas, voici les principales étapes pour configurer un compte DevCenter.
1. Créez un compte Microsoft [ici](https://signup.live.com/signup.aspx).
1. Accédez à la [page d’inscription](http://go.microsoft.com/fwlink/?LinkId=615100) Microsoft DevCenter et cliquez sur « S’inscrire ».
1. Lorsque vous êtes invité à payer, utilisez le code que nous vous avons fourni. Si vous n’avez pas de code, contactez [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Sélectionnez [le pays/la région](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) dans lequel/laquelle vous vivez, ou où se trouve votre entreprise. **Vous ne pourrez plus modifier ces informations.**
1. Sélectionnez votre [type de compte de développeur](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees). Pour AppSource, sélectionnez **Entreprise**. Pour un compte de société, veillez à consulter ces [recommandations](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Entrez les coordonnées que vous souhaitez utiliser pour votre compte de développeur.
Pour obtenir des instructions étape par étape détaillées sur la façon de configurer un compte DevCenter, consultez les instructions [ici](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Fournir des informations pour les vendeurs Microsoft
L’un des principaux avantages d’AppSource pour les partenaires est de leur permettre de collaborer avec les vendeurs Microsoft en présentant les applications partenaires aux clients potentiels.

Fournissez les [informations sur les solutions partenaires pour les vendeurs Microsoft](https://aka.ms/aapartnerappinfo) et envoyez-les à [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Cette étape est obligatoire pour que les applications Cortana Intelligence soient approuvées pour publication.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Créer une procédure client pas à pas attrayante sur AppSource
Tout d’abord, consultez [Optimisation des stocks de Neal Analytics](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) sur AppSource. Chaque entrée d’application dans AppSource est associée à un titre, un résumé (100 caractères maximum), une description (1 300 caractères maximum), des images, des vidéos (facultatif) et des documents pdf, en plus du point d’entrée pour une version d’évaluation. Les partenaires doivent tirer parti de tous ces éléments pour créer une expérience client attrayante.

Les partenaires doivent réfléchir au contenu qu’ils ajoutent sur AppSource en tant qu’orchestration de vente de bout en bout. Les clients lisent le titre et la description pour comprendre la proposition de valeur, puis consultent les images et les vidéos afin de découvrir à quoi sert la solution. Les études de cas peuvent aider les clients potentiels à entrevoir comment les clients existants tirent parti de l’application. 

Tout cela doit motiver le client à s’intéresser à l’application et à en savoir plus. Imaginez ces éléments comme une présentation qu’un bon vendeur technique pourrait faire aux nouveaux clients. Pour la description, nous suggérons de décomposer le texte en sous-sections en fonction des propositions de valeur, chaque sous-section étant mise en avant grâce à un sous-titre. 

Les visiteurs jettent généralement un œil au champ « Résumé de l’offre » et aux sous-titres pour se faire une idée des objectifs de l’application et savoir en quelques secondes pourquoi ils devraient utiliser l’application. Par conséquent, il est important de retenir l’attention de l’utilisateur et de lui donner une raison de découvrir les spécificités de l’application.

Voyez ce que ces partenaires ont réalisé.
- [Optimisation des stocks de Neal Analytics](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Personnalisation de la vente au détail de Plexure](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [Services citoyens d’AvePoint](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

L’acte final de la vente consiste à présenter une démonstration de l’application ou de la solution en montrant comment la proposition de valeur peut être obtenue. C’est exactement ce à quoi sert une expérience d’évaluation client sur AppSource. Une bonne démonstration vise à :
- Résumer de nouveau la proposition de valeur de l’application et répertorier les personnes de l’entreprise du client qui interagiront avec la solution
- Raconter une histoire et définir le contexte d’un exemple de client rencontrant des problèmes spécifiques
- Expliquez comment la situation peut généralement évoluer et toucher le client (avant), et comparez à ce qui se passerait si la solution était utilisée. Pour ce faire, vous pouvez utiliser des tableaux de bord PowerBI, etc.
- Résumez l’apport de la solution grâce à des algorithmes d’apprentissage automatique spécifiques, etc.

Le contenu ajouté dans AppSource doit être de haute qualité et suffisamment bien conçu pour permettre les actions suivantes :
- L’équipe de vente technique d’un partenaire doit l’utiliser pour orchestrer ses ventes. L’utilisation de l’application par vos équipes de vente est un bon signe que vous pouvez vous attendre à ce que les équipes de vente MS fassent de même. Cela permettra au point de contact du client d’être en mesure de transmettre de manière cohérente les mêmes informations à ses collègues et supérieurs afin d’obtenir le budget et les approbations avant d’effectuer une offre d’achat.
- Un client visitant le site peut passer le contenu en revue par lui-même et être très heureux de répondre aux communications du partenaire pour passer aux étapes suivantes.

C’est pourquoi les partenaires doivent réfléchir au contenu qu’ils ajoutent sur AppSource en tant qu’orchestration de vente de bout en bout. Selon le fil conducteur et les fonctionnalités à présenter dans l’évaluation, le type d’offre peut être défini.

## <a name="publish-your-app-on-the-publishing-portal"></a>Publier votre application sur le portail de publication
Une fois les étapes ci-dessus évaluées pour votre application, vous obtiendrez l’accès au portail de publication et pourrez consulter [Comment publier une offre Cortana Intelligence via le portail des partenaires cloud](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) pour obtenir des instructions détaillées sur les étapes suivantes.

Si vous avez besoin d’informations sur des champs, envoyez un e-mail à <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Mon application est publiée sur AppSource - et maintenant ?
Tout d’abord, félicitations pour la publication de votre application.
Le niveau de retours que vous obtenez à partir de la publication de votre application sur AppSource dépend fortement de la façon dont vous influencez le public cible. Consultez [Optimiser la croissance de votre application Cortana Intelligence sur AppSource](http://aka.ms/aagrowthhackguide) pour savoir comment vous pouvez optimiser les retours.

Pour toute question ou suggestion, contactez-nous à l’adresse <appsourcecissupport@microsoft.com>.

