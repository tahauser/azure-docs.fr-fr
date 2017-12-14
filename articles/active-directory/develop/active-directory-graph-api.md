---
title: API Graph Azure Active Directory | Microsoft Docs
description: "Vue d’ensemble et guide de démarrage rapide pour l’API Graph qui permet un accès par programme à Azure AD via les points de terminaison API REST."
services: active-directory
documentationcenter: 
author: viv-liu
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 815b9f75864ba3a08f623af12417391fba430f7d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-graph-api"></a>API Graph Azure Active Directory
> [!IMPORTANT]
> Nous vous recommandons fortement d’utiliser [Microsoft Graph](https://graph.microsoft.io/) au lieu de l’API Azure AD Graph pour accéder aux ressources Azure Active Directory. Nos efforts de développement sont maintenant axés sur Microsoft Graph et aucune autre amélioration n’est prévue pour l’API Azure AD Graph. Il existe un nombre très limité de scénarios pour lesquels l’API Azure AD Graph peut être appropriée. Pour plus d’informations, consultez le billet de blog [Microsoft Graph ou Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) dans le centre de développement Office.
> 
> 

L’API Graph Azure Active Directory fournit un accès par programme à Azure AD via les points de terminaison de l’API REST. Les applications peuvent utiliser l'API Graph pour effectuer des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur les données et objets d'annuaire. Par exemple, l’API Graph prend en charge les opérations courantes suivantes pour un objet utilisateur :

* Création d’un nouvel utilisateur dans un répertoire
* Obtention des propriétés détaillées d’un utilisateur, par exemple ses groupes
* Mise à jour des propriétés d’un utilisateur, par exemple son emplacement et son numéro de téléphone, ou modification de son mot de passe
* Vérification de l’appartenance de l’utilisateur au groupe pour les accès basés sur les rôles
* Désactivation ou suppression totale d’un compte d’utilisateur

Outre les objets utilisateur, vous pouvez effectuer des opérations similaires sur d’autres objets, comme les groupes et les applications. Pour appeler l’API Graph sur un répertoire, l’application doit être enregistrée auprès d’Azure AD et configurée de façon à autoriser l’accès au répertoire. Cette autorisation fait généralement l’objet d’un consentement de la part de l’utilisateur ou de l’administrateur.

Pour commencer à utiliser l’API Graph Azure Active Directory, consultez le [Guide de démarrage rapide pour l’API Graph](active-directory-graph-api-quickstart.md), ou la [documentation interactive de référence de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Caractéristiques
L’API Graph fournit les fonctionnalités suivantes :

* **Points de terminaison de l’API REST**: l’API Graph est un service RESTful constitué de points de terminaison accessibles via des requêtes HTTP standard. L’API Graph prend en charge les types de contenu XML ou Javascript Objet Notation (JSON) pour les requêtes et les réponses. Pour plus d'informations, consultez [Informations de référence sur l'API REST Graph Azure AD (en anglais)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Authentification via Azure AD**: chaque requête à l’API Graph doit être authentifiée par l’ajout d’un jeton web JSON (JWT, JSON Web Token) dans l’en-tête d’autorisation de la requête. Ce jeton est obtenu via une requête effectuée sur le point de terminaison du jeton Azure AD et la fourniture d’informations d’identification valides. Vous pouvez utiliser le processus d’informations d’identification client OAuth 2.0 ou le processus d’octroi de code d’autorisation afin d’obtenir un jeton pour appeler Graph. Pour plus d’informations, consultez [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorisation basée sur les rôles (RBAC)**: des groupes de sécurité sont utilisés pour affecter des RBAC dans l’API Graph. Par exemple, si vous souhaitez déterminer si un utilisateur a accès à une ressource spécifique, l'application peut appeler l'opération de [vérification de l'appartenance au groupe (opération transitive)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , qui renvoie true ou false.
* **Requête différentielle**: si vous souhaitez vérifier les modifications dans un répertoire entre deux périodes sans avoir à envoyer des requêtes fréquentes à l’API Graph, vous pouvez effectuer une demande de requête différentielle. Ce type de demande renvoie uniquement les modifications apportées entre la demande de requête différentielle précédente et la demande en cours. Pour plus d'informations, consultez [Requête différentielle de l'API Graph Azure AD (en anglais)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensions d’annuaire**: si vous développez une application devant lire ou écrire des propriétés uniques pour les objets d’annuaire, vous pouvez enregistrer et utiliser des valeurs d’extension à l’aide de l’API Graph. Par exemple, si votre application nécessite une propriété d’identifiant Skype pour chaque utilisateur, vous pouvez enregistrer la nouvelle propriété dans le répertoire et elle sera disponible sur chaque objet utilisateur. Pour plus d'informations, consultez [Extensions de schéma d'annuaire de l'API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Sécurisé par des étendues d'autorisation**: L’API Graph AAD expose des étendues d'autorisation qui permettent un accès sécurisé/consenti aux données AAD et prennent en charge différents types d'applications clientes, notamment :
  
  * les applications disposant d’une interface utilisateur qui bénéficient d'un accès délégué aux données par le biais de l'autorisation de l'utilisateur connecté (délégué)
  * celles qui utilisent un contrôle d'accès basé sur les rôles et défini par l’application, notamment les clients service/daemon (rôles d'application)
    
    Les étendues d’autorisation des rôles d’application et des rôles délégués représentent un privilège exposé par l’API Graph et peuvent être demandées par les applications clientes au moyen des [fonctionnalités d’autorisation d’inscription des applications sur le portail Azure](https://portal.azure.com). Les clients peuvent vérifier les étendues d'autorisation qui leur sont accordées en inspectant la revendication d'étendue (« scp ») reçue dans le jeton d'accès pour les autorisations déléguées et la revendication de rôles (« roles ») pour les autorisations de rôle d'application. En savoir plus sur les [Étendues d'autorisation de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).

## <a name="scenarios"></a>Scénarios
L’API Graph sert dans de nombreux scénarios d’application. Les scénarios suivants sont les plus courants :

* **Application métier (à un seul client)** : dans ce scénario, un développeur d’entreprise travaille pour une organisation qui possède un abonnement Office 365. Le développeur crée une application web qui interagit avec Azure AD pour effectuer des tâches telles que l’attribution d’une licence à un utilisateur. Cette tâche requiert l’accès à l’API Graph. Le développeur enregistre donc l’application à client unique dans Azure AD et configure les autorisations en lecture et en écriture pour l’API Graph. Ensuite, l’application est configurée pour utiliser ses propres informations d’identification ou celles de l’utilisateur actuellement connecté afin d’obtenir un jeton pour appeler l’API Graph.
* **Application Software as a Service (architecture mutualisée)** : dans ce scénario, un éditeur de logiciels indépendant (ISV) développe une application web mutualisée hébergée qui fournit des fonctionnalités de gestion utilisateur à d’autres organisations qui utilisent Azure AD. Ces fonctionnalités requièrent l’accès aux objets de répertoire. L’application doit donc appeler l’API Graph. Le développeur enregistre l’application dans Azure AD, la configure pour exiger des autorisations en lecture et en écriture pour l’API Graph, puis active l’accès externe afin que d’autres organisations puissent consentir à utiliser l’application dans leur répertoire. Lorsqu’un utilisateur d’une autre organisation s’authentifie à l’application pour la première fois, une boîte de dialogue de consentement s’affiche, avec les autorisations demandées par l’application.  Le consentement accordera à l’application les autorisations demandées sur l’API Graph dans le répertoire de l’utilisateur. Pour plus d’informations sur l’infrastructure de consentement, consultez [Vue d’ensemble de l’infrastructure de consentement](active-directory-integrating-applications.md).

## <a name="see-also"></a>Voir aussi
[Guide de démarrage rapide pour l’API Graph Azure AD](active-directory-graph-api-quickstart.md)

[Documentation REST Graph AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

