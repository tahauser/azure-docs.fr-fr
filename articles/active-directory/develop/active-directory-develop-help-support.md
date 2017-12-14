---
title: "Options d’aide et de support pour les développeurs Azure Identity | Microsoft Docs"
description: "Découvrir comment obtenir une assistance par rapport à des questions liées au développement et à des problèmes soulevés lors de la création d’applications s’intégrant à Microsoft Azure Identities (Azure Active Directory et MSA)"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 06da07aa699d19602449dc365abb971867214a31
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="support-and-help-options-for-developers"></a>Options d’aide et de support pour les développeurs 

Que vous implémentiez une nouvelle fonctionnalité dans votre application ou que vous commenciez à vous familiariser avec Azure Active Directory, Microsoft identities ou l’API Microsoft Graph, vous pouvez être amené à rechercher de l’aide auprès de la Communauté ou à connaître les options de support qui sont à votre disposition en tant que développeur. Cet article vous aide à comprendre ces options, celles-ci sont résumées ici :

> [!div class="checklist"]
> * Effectuez une recherche pour voir si votre question ou votre problème n’a pas déjà été traité par la Communauté, ou si une documentation existante pour la fonctionnalité que vous essayez d’implémenter est disponible
> * Dans certains cas, vous pouvez souhaiter tout simplement utiliser nos outils de support afin de résoudre un problème particulier
> * Si vous ne trouvez pas la solution à votre problème, vous avez la possibilité de poser une question sur *Stack Overflow*
> * Si vous rencontrez des difficultés avec l’une de nos bibliothèques d’authentification, signalez un problème *GitHub*
> * Enfin, si vous voulez vous entretenir avec une personne, ouvrez une demande de support


## <a name="search"></a>Search

Si votre question concerne le développement, la réponse peut se trouver dans notre documentation, nos [exemples github](https://github.com/azure-samples) ou dans les réponses aux questions posées sur [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Recherche élargie
Pour obtenir des résultats plus rapidement, élargissez votre recherche à Stack Overflow, à notre documentation et à nos exemples de code en utilisant le texte suivant dans votre [moteur de recherche habituel](https://bing.com) :
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Sachant que *{Your Search Terms}* est l’emplacement où indiquer les mots clés de votre recherche.
<br/>

## <a name="use-our-development-support-tools"></a>Utilisation de nos outils de support de développement

|Outil  |Description  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Collez un jeton d’ID ou d’accès pour décoder les noms et valeurs des demandes |
|[Analyseur de code d’erreur](https://apps.dev.microsoft.com/portal/tools/errors)| Collez un code d’erreur obtenu au moment de la connexion ou dans les pages d’acceptation pour afficher les causes et les corrections possibles |
|[Afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Cet outil vous permet de soumettre des requêtes et de voir les réponses par rapport à l’API Microsoft Graph|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Publication d’une question sur Stack Overflow

Stack Overflow est le canal de prédilection pour les questions liées au développement ; les membres de la Communauté, comme les membres de l’équipe Microsoft, apportent directement leur contribution en vous aidant à résoudre votre problème.

Si vous ne trouvez pas de solution par l’intermédiaire de la recherche, soumettez une nouvelle question à Stack Overflow : utilisez un des mots clés suivants lors de la formulation de votre question pour aider la Communauté à identifier rapidement votre problème et à le résoudre dans les meilleurs délais :

|Composant/Zone  |Tags  |
|---------|---------|
|Bibliothèque ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Bibliothèque MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|Intergiciel (middleware) OWIN  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[API Microsoft Graph](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Toute autre zone liée à des sujets sur l’authentification ou les autorisations |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Les publications suivantes de Stack Overflow renferment des conseils sur la façon de formuler des questions, mais aussi des indications sur l’ajout de code source ; en suivant ces recommandations, vous augmentez vos chances que des membres de la Communauté comprennent et répondent rapidement à votre question :  
> - [Comment poser une bonne question](https://stackoverflow.com/help/how-to-ask)
> - [Comment créer un exemple minimal, complet et vérifiable](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Créer un problème GitHub

 Si vous découvrez un bogue ou un problème lié à nos bibliothèques, signalez ce dysfonctionnement sur nos dépôts GitHub. Nos bibliothèques étant open source, n’hésitez pas à soumettre également des demandes de tirage (pull request). L’article suivant contient une liste de bibliothèques avec leurs dépôts GitHub :

- Bibliothèques et dépôts GitHub [ADAL, MSAL et Intergiciel Owin](active-directory-authentication-libraries.md)

<br/>

## <a name="open-a-support-request"></a>Ouverture d’une demande de support

Si vous avez besoin de vous entretenir avec quelqu’un, vous pouvez ouvrir une demande de support. Si vous êtes un client Azure, plusieurs options de support s’offrent à vous. Pour comparer les formules, consultez [cette page](https://azure.microsoft.com/support/plans/). Le support technique des développeurs est également disponible pour les clients Azure. Pour obtenir plus d’informations sur l’achat de formules d’assistance Developer Support, consultez [cette page](https://azure.microsoft.com/support/plans/developer/).

- Si vous disposez déjà d’une formule d’assistance Azure, [ouvrez une demande de support ici](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Si vous n’êtes pas un client Azure, vous pouvez également ouvrir une demande de support auprès de Microsoft via [notre assistance commerciale](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Vous avez aussi la possibilité de vous adresser à [notre agent virtuel](https://support.microsoft.com/contactus/?ws=support) pour obtenir une assistance ou poser des questions.

### <a name="free-chat-support-for-a-limited-time"></a>Assistance gratuite par chat à durée limitée

Vous pouvez également utiliser notre assistance par chate ; elle est disponible pour les partenaires Microsoft sur une durée limitée. Si votre entreprise n’est pas un partenaire Microsoft, vous pouvez l’inscrire gratuitement et bénéficier d’autres avantages en vous rendant [ici](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Dès l’inscription de votre entreprise effectuée, vous pouvez démarrer la demande de conversation [ici](https://aka.ms/devchat).