---
title: 'Azure Active Directory B2C : stratégies personnalisées | Microsoft Docs'
description: Une rubrique sur les stratégies personnalisées Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 269b65662796c092190cd2622c240756f6bd1cf7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C : stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Que sont les stratégies personnalisées ?

Les stratégies personnalisées sont des fichiers de configuration qui définissent le comportement de votre client Azure AD B2C. Si les **stratégies prédéfinies** sont prédéfinies sur le Portail Azure AD B2C pour les tâches d’identité les plus courantes, les stratégies personnalisées, elles, peuvent être entièrement modifiés par un développeur d’identité pour effectuer un nombre quasiment illimité de tâches. Lisez la suite pour déterminer si les stratégies personnalisées vous conviennent et sont adaptées à votre scénario d’identité.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Comparaison des stratégies prédéfinies et des stratégies personnalisées

| | Stratégies prédéfinies | Stratégies personnalisées |
|-|-------------------|-----------------|
|Utilisateurs cibles | Tous les développeurs d’applications avec ou sans expertise de l’identité | Professionnels de l’identité : intégrateurs système, consultants et équipes d’identité internes. Ils sont à l’aise avec les flux OpenID Connect et comprennent les fournisseurs d’identité et l’authentification basée sur les revendications. |
|Mode de configuration | Portail Azure avec interface utilisateur conviviale | Modification directe des fichiers XML, puis chargement sur le Portail Azure |
|Personnalisation de l’interface utilisateur | Personnalisation complète de l’interface utilisateur, avec prise en charge HTML, CSS et javascript (nécessite un domaine personnalisé)<br><br>Prise en charge multilingue avec chaînes personnalisées | Identique |
| Personnalisation des attributs | Attributs standard et personnalisés | Identique |
|Gestion des jetons et des sessions | Options de jetons personnalisés et de sessions multiples | Identique |
|Identity Providers| **Aujourd'hui** : fournisseur social local prédéfini<br><br>**À l’avenir** : OIDC basé sur les normes, SAML, OAuth | **Aujourd'hui** : OIDC basé sur les normes, OAuth, SAML<br><br>**À l’avenir** : WS-Fed |
|Tâches d’identité (exemples) | Inscription ou connexion avec des comptes locaux et de nombreux comptes sociaux<br><br>Réinitialisation du mot de passe en libre-service<br><br>Modification de profil<br><br>Scénarios d’authentification multifacteur<br><br>Personnalisation des jetons et des sessions<br><br>Flux de jetons d’accès | Réalisation des mêmes tâches que les stratégies prédéfinies à l’aide de fournisseurs d’identité personnalisés ou utilisation des étendues personnalisées<br><br>Approvisionnement de l’utilisateur dans un autre système au moment de l’inscription<br><br>Envoi d’un courrier électronique de bienvenue avec votre propre fournisseur de service de messagerie<br><br>Utilisation d’un magasin d’utilisateurs en dehors de B2C<br><br>Validation des informations fournies par l’utilisateur avec un système approuvé via une API |

## <a name="policy-files"></a>Fichiers de stratégie

Une stratégie personnalisée est représentée par un ou plusieurs fichiers au format XML qui se font mutuellement référence dans une chaîne hiérarchique. Les éléments XML définissent notamment les éléments suivants : schéma de revendications, transformation de revendications, définitions de contenu, fournisseurs de revendications / profils techniques et étapes d’orchestration du parcours utilisateur.

Nous vous recommandons d’utiliser trois types de fichiers de stratégie :

- **Un fichier de BASE**, qui contient la plupart des définitions et pour lequel Azure fournit un échantillon complet.  Nous vous recommandons d’apporter aussi peu de modifications que possible à ce fichier pour faciliter la résolution des problèmes et la maintenance à long terme de vos stratégies.
- **Un fichier d’EXTensions** qui contient les modifications de configuration propres à votre client.
- **Un fichier de la partie de confiance (RP)**, le seul fichier centré sur les tâches qui est appelé directement par l’application ou le service (la partie de confiance).  Lisez l’article sur les définitions de fichiers de stratégie pour plus d’informations.  Chaque tâche unique nécessite sa propre partie de confiance et, en fonction des exigences de personnalisation, le nombre peut être « le nombre total d’applications multiplié par le nombre total de cas d’utilisation ».


Les stratégies prédéfinies dans Azure AD B2C suivent le modèle à trois fichiers décrit ci-dessus, mais le développeur ne voit que le fichier de la partie de confiance (RP), tandis que le portail modifie en arrière-plan le fichier d’EXTensions.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Principaux concepts à connaître pour utiliser les stratégies personnalisées

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Service de gestion des accès et des identités clients d’Azure. Il comprend :

1. Un annuaire d’utilisateurs sous la forme d’un service Azure Active Directory spécifique accessible via Microsoft Graph, qui contient des données utilisateur pour les comptes locaux et les comptes fédérés. 
2. L’accès à **l’infrastructure d’expérience d’identité** qui orchestre les relations de confiance entre les utilisateurs et les entités et transmet les revendications des uns aux autres pour mener à bien une tâche de gestion des identités et des accès. 
3. Un service d’émission de jeton de sécurité (STS) qui émet des jetons d’ID, des jetons d’actualisation et des jetons d’accès (ainsi que les assertions SAML équivalentes) et les valide afin de protéger les ressources.

Azure AD B2C interagit de façon séquentielle avec les fournisseurs d’identité, les utilisateurs, d’autres systèmes et l’annuaire d’utilisateurs locaux pour effectuer une tâche d’identité (par exemple, connexion d’un utilisateur, inscription d’un nouvel utilisateur ou réinitialisation d’un mot de passe). La plateforme sous-jacente qui établit la confiance mutuelle et effectue ces étapes est appelée « infrastructure d’expérience d’identité ». Une stratégie (également appelée « parcours utilisateur » ou « stratégie d’infrastructure de confiance ») définit explicitement les acteurs, les actions, les protocoles et la séquence d’étapes à effectuer.

### <a name="identity-experience-framework"></a>Identity Experience Framework (Infrastructure d’expérience d’identité)

Plateforme Azure cloud, entièrement configurable et pilotée par des stratégies, qui orchestre les relations de confiance entre entités (en général des fournisseurs de revendications) dans des formats de protocoles standard, notamment OpenID Connect, OAuth, SAML, WS-Fed, ainsi que quelques protocoles non standard (par exemple, des échanges de revendications intersystèmes basés sur l’API REST). Le I2E crée des expériences conviviales en marque blanche qui prennent en charge HTML, CSS et javascript.  Aujourd’hui, l’infrastructure d’expérience d’identité est disponible uniquement dans le contexte du service Azure AD B2C, en priorité pour les tâches relatives au service de gestion des accès et des identités clients.

### <a name="built-in-policies"></a>Stratégies prédéfinies

Fichiers de configuration prédéfinis qui contrôlent Azure AD B2C de sorte qu’il effectue les tâches d’identité les plus couramment utilisée (par exemple, inscription des utilisateurs, connexion, réinitialisation du mot de passe) et interagisse avec les parties de confiance dont la relation est également prédéfinie dans Azure AD B2C (par exemple, le fournisseur d’identités Facebook, LinkedIn, compte Microsoft, comptes Google).  À l’avenir, les stratégies prédéfinies seront également susceptibles de permettre la personnalisation des fournisseurs d’identité qui se trouvent généralement dans le domaine de l’entreprise, par exemple Azure Active Directory Premium, Active Directory/ADFS ou le fournisseur d’identité Salesforce.


### <a name="custom-policies"></a>Stratégies personnalisées

Fichiers de configuration qui définissent le comportement de l’infrastructure d’expérience d’identité de votre locataire Azure AD B2C. Une stratégie personnalisée est accessible sous la forme d’un ou plusieurs fichiers XML (voir les définitions de fichiers de stratégie) qui sont exécutés par l’infrastructure d’expérience d’identité lorsqu’ils sont appelés par une partie de confiance (par exemple, une application). Les stratégies personnalisées peuvent être modifiées directement par un développeur d’identité pour effectuer un nombre quasiment illimité de tâches. Les développeurs qui configurent des stratégies personnalisées doivent définir les relations de confiance dans leurs moindres détails pour inclure les points de terminaison de métadonnées et les définitions exactes des échanges de revendications, et configurer les secrets, les clés et les certificats selon les besoins de chaque fournisseur d’identité.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Définitions de fichiers de stratégie pour les infrastructures de confiance de l’infrastructure d’expérience d’identité

### <a name="policy-files"></a>Fichiers de stratégie

Une stratégie personnalisée est représentée par un ou plusieurs fichiers au format XML qui se font mutuellement référence dans une chaîne hiérarchique. Les éléments XML définissent notamment les éléments suivants : schéma de revendications, transformation de revendications, définitions de contenu, fournisseurs de revendications/profils techniques et étapes d’orchestration du parcours utilisateur.  Nous vous recommandons d’utiliser trois types de fichiers de stratégie :

- **Un fichier de BASE** contient la plupart des définitions et pour lequel Azure fournit un échantillon complet.  Nous vous recommandons d’apporter aussi peu de modifications que possible à ce fichier pour faciliter la résolution des problèmes et la maintenance à long terme de vos stratégies.
- **Un fichier d’EXTensions** qui contient les modifications de configuration propres à votre client.
- **Un fichier de la partie de confiance (RP)** est le seul fichier centré sur les tâches appelé directement par l’application ou le service (la partie de confiance).  Lisez l’article sur les définitions de fichiers de stratégie pour plus d’informations.  Chaque tâche unique nécessite sa propre partie de confiance et, en fonction des exigences de personnalisation, le nombre peut être « le nombre total d’applications multiplié par le nombre total de cas d’utilisation ».

![Types de fichiers de stratégie](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Type de fichier de stratégie | Exemples de noms de fichier | Utilisation recommandée | Hérite de |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Comprend le schéma de revendications principal, les transformations de revendications, les fournisseurs de revendications et les parcours utilisateur configurés par Microsoft.<br><br>Apportez aussi peu de modifications que possible à ce fichier. | Aucun |
| Extension (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Consolidez ici vos modifications au fichier de BASE.<br><br>Fournisseurs de revendications modifiés.<br><br>Parcours utilisateur modifiés.<br><br>Vos propres définitions de schéma personnalisées. | Fichier de BASE |
| Partie de confiance (RP) | B2C_1A_sign_up_sign_in.xml| Modifiez les paramètres de jeton, de forme et de session ici| Fichier Extensions(EXT) |

### <a name="inheritance-model"></a>Modèle d’héritage

Lorsqu’une application appelle le fichier de stratégie de la partie de confiance, l’infrastructure d’expérience d’identité B2C ajoute tous les éléments de BASE, puis d’EXTENSIONS et enfin du fichier de stratégie de la partie de confiance pour assembler la stratégie en vigueur.  Les éléments du même type et du même nom du fichier de la partie de confiance remplacent ceux des EXTENSIONS, et ceux des EXTENSIONS remplacent ceux de la BASE.

Les **stratégies prédéfinies** dans Azure AD B2C suivent le modèle à trois fichiers décrit ci-dessus, mais le développeur ne voit que le fichier de la partie de confiance (RP), tandis que le portail modifie en arrière-plan le fichier d’EXTensions.  L’ensemble d’Azure AD B2C partage un fichier de stratégie de BASE, sous le contrôle de l’équipe Azure B2C et mis à jour fréquemment.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md)
