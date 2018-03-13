---
title: "Azure Active Directory B2C : Notes du développeur sur l’utilisation des stratégies personnalisées | Microsoft Docs"
description: "Notes à destination des développeurs pour configurer et maintenir Azure AD B2C avec des stratégies personnalisées"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/13/2017
ms.author: joroja
ms.openlocfilehash: 7f65ad324e1bf0887ea293254621466a2f1ddaff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notes de version pour la version préliminaire publique de la stratégie personnalisée Azure Active Directory B2C
L’ensemble de fonctionnalités de stratégie personnalisée est désormais disponible à des fins d’évaluation en préversion publique pour tous les clients Azure Active Directory B2C (Azure AD B2C). Cet ensemble de fonctionnalités est destiné aux développeurs d’identité avancés créant les solutions d’identité les plus complexes.  

À ce jour, cet ensemble de fonctionnalités exige que les développeurs configurent l’infrastructure d’expérience d’identité directement via la modification du fichier XML. Cette méthode de configuration est à la fois puissante et complexe. Les développeurs d’identité avancés qui utilisent l’infrastructure d’expérience d’identité doivent prévoir du temps pour suivre des procédures et lire des documents de référence. 

## <a name="features-included-in-this-public-preview"></a>Fonctionnalités incluses dans cette version préliminaire publique
Les nouvelles fonctionnalités introduites dans la préversion permettent aux développeurs d’effectuer les tâches suivantes :<br>

* Créer et charger des parcours utilisateur d’authentification personnalisés à l’aide de stratégies personnalisées. 
   * Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications. 
   * Définir le branchement conditionnel dans des parcours utilisateur. 
* Intégrer des services compatibles avec l’API REST dans vos parcours utilisateur d’authentification personnalisés.  
* Ajouter la fédération avec les fournisseurs d’identité conformes à la norme OpenIDConnect. <br>
* Ajouter la fédération avec les fournisseurs d’identité qui adhèrent au protocole SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Conditions de la version préliminaire publique

* Nous vous encourageons à utiliser les nouvelles fonctionnalités à des fins d’évaluation uniquement.<br>
* Les nouvelles fonctionnalités ne sont pas destinées à une utilisation dans un environnement de production.<br>
* Les contrats de niveau de service (SLA) ne s’appliquent pas aux nouvelles fonctionnalités. <br>
* Les demandes de support peuvent être déposées via les canaux de support habituels. <br>
* Il n’existe aucune date prévue pour une mise à disposition générale.<br>
* À notre discrétion et pour quelque raison que ce soit, Microsoft peut signaler et rejeter ou limiter des scénarios et des parcours utilisateur qui dépassent la portée de la charte du produit Azure AD B2C comme plateforme de gestion des accès et des identités des clients.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilités des développeurs de l’ensemble de fonctionnalités de stratégie personnalisée
La configuration de stratégie manuelle accorde le niveau d’accès minimal à la plateforme sous-jacente d’Azure AD B2C et entraîne la création d’une infrastructure approuvée unique et entièrement personnalisable. Les permutations possibles des fournisseurs d’identité personnalisés, les relations d’approbation, les intégrations aux services externes et les flux de travail étape par étape exigeront beaucoup de la part des développeurs avancés qui les utilisent.

Afin de tirer pleinement parti de la préversion publique, nous suggérons aux développeurs qui utilisent l’ensemble de fonctionnalités de stratégie personnalisée de respecter les consignes suivantes :
* Se familiariser au langage de configuration de l’infrastructure d’expérience d’identité (IEF) et à la gestion clé/secrets.
* S’approprier les scénarios et les intégrations personnalisées.
* Effectuer un test de scénario méthodique.
* Suivre les meilleures pratiques de test et de développement de logiciels avec au minimum un environnement de développement et de test et un environnement de production.
* Suivre les nouveaux développements des services et fournisseurs d’identité que vous intégrez. Par exemple, suivre les modifications apportées aux secrets, ainsi que les modifications planifiées et non planifiées du service.
* Configurer la surveillance active et surveiller la réactivité des environnements de production.
* Garder les adresses e-mail de contact à jour dans l’abonnement Azure et rester attentif aux e-mails de l’équipe de site en ligne Microsoft.
* Prendre les mesures adéquates lorsque cela est recommandé par l’équipe de site en ligne Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Fonctionnalités par étape et problèmes connus
Les fonctionnalités IEF et de stratégies personnalisées font l’objet d’un développement rapide et constant.  Le tableau ci-après indique la disponibilité des fonctionnalités/composants.

Publiez vos questions sur Stack Overflow à l’adresse [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso).


### <a name="identity-providers-tokens-protocols"></a>Fournisseurs d’identité, jetons et protocoles
Interfaces avec les applications et composants externes

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | Par exemple, Google+ |
| IDP-OAUTH2 |  | x |  | Par exemple, Facebook  |
| IDP-OAUTH1 |  | x |  | Par exemple, Twitter |
| IDP-SAML |  | x |  | Par exemple, Salesforce, ADFS |
| IDP-WSFED | x |  |  |  |
| OAUTH avec partie de confiance |  | x |  |  |
| OIDC avec partie de confiance |  | x |  |  |
| SAML avec partie de confiance | x |  |  |  |
| WSFED avec partie de confiance | x |  |  |  |
| API REST avec l’authentification de base et par certificat |  | x |  | Par exemple, Azure Functions |


### <a name="component-support"></a>Prise en charge des composants


| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi-Factor Authentication |  | x |  |  |
| Azure Active Directory comme annuaire local |  | x |  |  |
| Sous-système d’e-mail Azure pour l’authentification à 2 facteurs |  | x |  |  |
| Prise en charge multi-langage|  | x |  |  |
| Complexité du mot de passe | x |  |  |  |


### <a name="content-definition"></a>Définition du contenu

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Page d’erreur, api.error |  | x |  |  |
|   Page de sélection de fournisseurs d’identité, api.idpselections |  | x |  |  |
|   Sélection de fournisseurs d’identité pour l’inscription, api.idpselections.signup |  | x |  |  |
|   Mot de passe oublié, api.localaccountpasswordreset |  | x |  |  |
|   Connexion au compte local, api.localaccountsignin |  | x |  |  |
|   Inscription à un compte local, api.localaccountsignup |  | x |  |  |
|   Page MFA, api.phonefactor |  | x |  |  |
|   Autodéclaration (par exemple, inscription à un compte social), api.selfasserted |  | x |  |  |
|   Mise à jour de profil autodéclaré, api.selfasserted.profileupdate |  | x |  |  |
|   Page d’inscription ou de connexion unifiée, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Intégration App-IEF
| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Paramètre de chaîne de requête id_token_hint | x |  |  |  |
| Paramètre de chaîne de requête domain_hint |  | x |  | Disponible comme revendication, peut être passé au fournisseurs d’identité |
| Paramètre de chaîne de requête login_hint |  | x |  | Disponible comme revendication, peut être passé au fournisseurs d’identité |
| Insérer du code JSON dans le parcours utilisateur via client_assertion | x |  |  | Fonctionnalité destinée à être dépréciée |
| Insérer du code JSON dans le parcours utilisateur en tant que id_token_hint | x |  |  | Approche à adopter pour passer JSON |


### <a name="session-management"></a>Gestion des sessions

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|---------------------------------|-------------|---------|----|-------|
| Fournisseur de session par authentification unique |  | x |  |  |
| Fournisseur de session de connexion externe |  | x |  |  |
| Fournisseur de session par authentification unique SAML |  | x |  |  |


### <a name="security"></a>Sécurité
| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|---------------------------------------------|-------------|---------|----|-------|
| Clés de stratégie : générer, manuel, charger |  | x |  |  |
| Clés de stratégie : RSA/certificat, secrets |  | x |  |  |


### <a name="developer-interface"></a>Interface de développeur
| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|---------------------------------------------|-------------|---------|----|-------|
| Expérience utilisateur de l’infrastructure d’expérience d’identité dans le Portail Azure |  | x |  |  |
| Journaux du parcours utilisateur Application Insights  |  | x |  |  |
| Journaux d’événements Application Insights |x|  |  |  |



## <a name="next-steps"></a>Étapes suivantes
[Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).
