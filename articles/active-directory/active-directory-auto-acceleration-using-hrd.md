---
title: "Configurer l’accélération automatique de la connexion pour une application à l’aide de la stratégie de découverte du domaine d’accueil | Microsoft Docs"
description: "Explique ce qu’est un client Azure AD et comment gérer Azure depuis Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Configurer l’accélération automatique de la connexion pour une application à l’aide de la stratégie de découverte du domaine d’accueil

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Présentation de la découverte du domaine d’accueil et de l’accélération automatique
Le document suivant présente la découverte du domaine d’accueil et l’accélération automatique.

### <a name="home-realm-discovery"></a>Découverte du domaine d’accueil
La découverte du domaine d’accueil est le processus qui permet à Azure Active Directory de déterminer, au moment de la connexion, où un utilisateur doit s’authentifier.  Quand il se connecte à un locataire Azure AD pour accéder à une ressource ou à la page de connexion courante d’Azure AD, l’utilisateur tape un nom d’utilisateur (UPN).  Azure AD l’utilise pour découvrir où l’utilisateur doit se connecter.   L’utilisateur peut devoir être dirigé vers l’un des éléments suivants pour être authentifié :

- Le locataire de base de l’utilisateur (peut être le même locataire que la ressource à laquelle l’utilisateur tente d’accéder). 
- Compte Microsoft.   L’utilisateur est un invité dans le locataire de la ressource.
- Un autre fournisseur d’identité fédéré avec le locataire Azure AD.  Un fournisseur d’identité local tel qu’AD FS, par exemple.

### <a name="auto-acceleration"></a>Accélération automatique 
Certaines organisations configurent leur locataire Azure Active Directory pour le fédérer avec un autre fournisseur d’identité, tel qu’AD FS, pour l’authentification des utilisateurs.  Dans ces cas, quand il se connecte à une application, l’utilisateur se voit présenter une page de connexion Azure AD, puis, une fois qu’il a entré son UPN, il est dirigé vers la page de connexion du fournisseur d’identité.  Si les circonstances le justifient, les administrateurs peuvent souhaiter que les utilisateurs soient dirigés directement vers la page de connexion quand ils se connectent à des applications spécifiques, passant ainsi outre la page Azure Active Directory initiale. Cette opération est dénommée « accélération automatique de la connexion ».

Dans les cas où le locataire est fédéré avec un autre fournisseur d’identité pour la connexion, l’activation de l’accélération automatique simplifie la connexion des utilisateurs si chaque personne qui se connecte peut effectivement être authentifiée par ce fournisseur d’identité.  Vous pouvez configurer l’accélération automatique pour des applications individuelles.

>[!NOTE]
>Si vous configurez une application pour l’accélération automatique, les utilisateurs invités ne peuvent pas se connecter. Diriger l’utilisateur directement vers un fournisseur d’identité fédéré pour l’authentification est un processus à sens unique, car il est impossible de revenir à la page de connexion Azure Active Directory.  Les utilisateurs invités, qui doivent éventuellement être dirigés vers d’autres locataires ou un fournisseur d’identité externe comme un compte Microsoft pour être authentifiés, ne peuvent pas se connecter à cette application, car l’étape de la découverte du domaine d’accueil est ignorée.  

Il existe deux façons de contrôler l’accélération automatique vers un fournisseur d’identité.  Au choix :    

- Utiliser une indication de domaine sur les demandes d’authentification pour une application 
- Configurer une stratégie de découverte du domaine d’accueil pour activer l’accélération automatique

## <a name="domain-hints"></a>Indications de domaine 
Les indications de domaine sont des directives incluses dans la demande d’authentification émise par une application.  Ils peuvent servir à accélérer l’utilisateur vers la page de connexion de son fournisseur d’identité fédéré ou peuvent être utilisés par une application multilocataire pour accélérer l’utilisateur directement vers la page de connexion Azure AD personnalisée pour son locataire.  Par exemple, une application largeapp.com peut permettre à ses clients d’accéder à l’application depuis une URL contoso.largeapp.com personnalisée et peut inclure une indication de domaine pour Contoso.com dans la demande d’authentification. La syntaxe des indications de domaine varie selon le protocole utilisé et est généralement configurée dans l’application.

**WS-Federation** : whr=contoso.com dans la chaîne de requête

**SAML** : demande d’authentification SAML contenant une indication de domaine ou une chaîne de requête whr=contoso.com

**OpenID Connect** : chaîne de requête domain_hint=contoso.com 

Si une indication de domaine est incluse dans la demande d’authentification émise par l’application et que le locataire est fédéré avec ce domaine, Azure Active Directory tente de rediriger la connexion vers le fournisseur d’identité configuré pour ce domaine.  Si l’indication de domaine ne fait pas référence à un domaine fédéré vérifié, elle est ignorée et la découverte du domaine d’accueil normale est appelée.

Consultez ce [billet de blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) pour plus d’informations sur l’accélération automatique au moyen des indications de domaine prises en charge par Azure Active Directory.

>[!NOTE]
>Si une indication de domaine est incluse dans une demande d’authentification, sa présence remplace toute stratégie de découverte du domaine d’accueil définie pour l’application.

## <a name="home-realm-discovery-hrd-policy"></a>Stratégie de découverte du domaine d’accueil
Certaines applications ne permettent pas de configurer la demande d’authentification qu’elles émettent ; dans ce cas, il est impossible d’utiliser des indications de domaine pour contrôler l’accélération automatique.   Vous pouvez configurer l’accélération automatique par le biais d’une stratégie pour obtenir le même comportement.  

### <a name="setting-hrd-policy"></a>Définition de la stratégie de découverte du domaine d’accueil
La configuration de l’accélération automatique de la connexion sur une application comprend trois étapes.


1. Création d’une stratégie de découverte du domaine d’accueil pour l’accélération automatique
2. Recherche du principal de service auquel attacher la stratégie
3. Attachement de la stratégie au principal de service.  Les stratégies éventuellement créées dans un locataire demeurent sans effet tant qu’elles ne sont pas attachées à une entité.  Une stratégie de découverte du domaine d’accueil peut être jointe à un principal de service, et une seule stratégie de découverte du domaine d’accueil peut être active sur une entité donnée à tout moment.  

Vous pouvez utiliser l’API Microsoft Azure Active Directory Graph directement ou les applets de commande Azure Active Directory PowerShell pour configurer l’accélération automatique à l’aide de la stratégie de découverte du domaine d’accueil.

L’API Graph qui manipule la stratégie est décrite [ici](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations).

Voici un exemple de définition de la stratégie de découverte du domaine d’accueil :
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

Le type de stratégie est « HomeRealmDiscoveryPolicy ».

Si **AccelerateToFederatedDomain** est false, la stratégie n’a aucun effet. **PreferredDomain** doit indiquer un domaine vers lequel accélérer et peut être omis si le locataire n’a qu’un seul domaine fédéré.  S’il est omis et qu’il existe plusieurs domaines fédérés vérifiés, la stratégie n’a aucun effet.

Si **PreferredDomain** est spécifié, il doit correspondre à un domaine fédéré vérifié pour le locataire, et tous les utilisateurs de l’application en question doivent être en mesure de se connecter à ce domaine.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorité et évaluation des stratégies de découverte du domaine d’accueil
Les stratégies de découverte du domaine d’accueil peuvent être créées, puis affectées à des organisations et principaux de service spécifiques. Il est donc possible d’appliquer plusieurs stratégies à une application spécifique. La stratégie de découverte du domaine d’accueil appliquée suit les règles ci-dessous :


- Si une indication de domaine est présente dans la demande d’authentification, toute stratégie de découverte du domaine d’accueil est ignorée et le comportement spécifié par l’indicateur de domaine est utilisé.
- Sinon, si une stratégie est explicitement affectée au principal de service, elle est appliquée. 
- S’il n’existe aucune indication de domaine et qu’aucune stratégie n’est explicitement affectée au principal de service, une stratégie explicitement affectée à l’organisation parente du principal de service est appliquée. 
- S’il n’existe aucune indication de domaine et qu’aucune stratégie n’a été affectée au principal de service ou à l’organisation, le comportement de découverte du domaine d’accueil par défaut est utilisé.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Didacticiel pour configurer l’accélération automatique de la connexion sur une application à l’aide de la stratégie de découverte du domaine d’accueil avec des applets de commande Azure Active Directory PowerShell
Nous allons explorer quelques scénarios :


- Configurer l’accélération automatique pour une application pour un client avec un seul domaine fédéré
- Configurer l’accélération automatique pour une application vers un domaine parmi ceux qui sont vérifiés pour votre client
- Répertorier les applications pour lesquelles une stratégie est configurée

### <a name="prerequisites"></a>Composants requis
Dans les exemples ci-dessous vous créez, mettez à jour, liez et supprimez des stratégies sur des principaux de service d’application dans Azure AD.

1.  Pour commencer, téléchargez la dernière version d’évaluation des applets de commande Azure AD PowerShell. 
2.  Une fois que vous disposez des applets de commande Azure AD PowerShell, exécutez la commande Connect pour vous connecter à Azure AD avec votre compte d’administrateur.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Exécutez la commande suivante pour afficher toutes les stratégies dans votre organisation.

    ``` powershell
    Get-AzureADPolicy
    ```

Si aucun résultat n’est retourné, aucune stratégie n’est créée dans votre locataire.

### <a name="example-setting-auto-acceleration-for-an-application"></a>Exemple : configuration de l’accélération automatique pour une application 
Dans cet exemple, vous créez une stratégie qui accélère automatiquement les utilisateurs vers un écran de connexion AD FS quand ils se connectent à une application.  Cette opération est effectuée sans qu’ils aient besoin d’entrer au préalable un nom d’utilisateur dans la page de connexion Azure AD. 

#### <a name="step-1-create-an-hrd-policy"></a>Étape 1 : Créer une stratégie de découverte du domaine d’accueil
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Si vous avez un domaine fédéré unique qui authentifie les utilisateurs pour les applications, vous devez uniquement créer une stratégie de découverte du domaine d’accueil.  
Pour afficher votre nouvelle stratégie et obtenir son ID d’objet, exécutez la commande ci-après.

``` powershell
Get-AzureADPolicy
```


Une fois que vous avez une stratégie de découverte du domaine d’accueil, vous pouvez l’affecter à plusieurs principaux de service d’application pour activer l’accélération automatique.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>Étape 2 : Rechercher le principal de service auquel affecter la stratégie  
Vous avez besoin de l’ID d’objet des principaux de service auxquels vous souhaitez affecter la stratégie. Il existe plusieurs façons de rechercher l’ID d’objet des principaux de service.    

Vous pouvez utiliser le portail, vous pouvez interroger [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accéder à notre [Afficheur Graph](https://graphexplorer.cloudapp.net/) et vous connecter à votre compte Azure AD pour afficher tous les principaux de service de votre organisation ou, dans la mesure où vous utilisez PowerShell, vous pouvez utiliser l’applet de commande get-AzureADServicePrincipal pour répertorier les principes de service et leurs ID.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Étape 3 : Affecter la stratégie à votre principal de service  
Une fois que vous disposez de l’ID d’objet du principal de service de l’application pour laquelle vous souhaitez configurer l’accélération automatique, exécutez la commande suivante pour associer la stratégie de découverte du domaine d’accueil que vous avez créée à l’étape 1 au principal de service que vous avez localisé à l’étape 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Vous pouvez répéter cette commande pour chaque principal de service auquel vous souhaitez ajouter la stratégie.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Étape 4 : Vérifier les principaux de service d’application auxquels votre stratégie d’accélération automatique est affectée
Pour déterminer les applications pour lesquelles la stratégie d’accélération automatique est configurée, utilisez l’applet de commande Get-AzureADPolicyAppliedObject et passez-lui l’ID d’objet de la stratégie sur laquelle porte la vérification.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Étape 5 : Vous avez terminé !
Testez l’application pour vérifier que la nouvelle stratégie fonctionne.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Exemple : répertorier les applications pour lesquelles une stratégie d’accélération automatique est configurée

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>Étape 1 : Répertorier toutes les stratégies créées dans votre organisation 

``` powershell
Get-AzureADPolicy
```

Notez **l’ID d’objet** de la stratégie dont vous souhaitez répertorier les affectations.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>Étape 2 : Répertorier les principaux de service auxquels la stratégie est affectée  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Exemple : suppression d’une stratégie d’accélération automatique pour une application
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>Étape 1 : Utiliser l’exemple précédent pour obtenir l’ID d’objet de la stratégie et celui du principal de service d’application dont vous souhaitez la supprimer
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Étape 2 : Supprimer l’affectation de stratégie du principal de service d’application  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>Étape 3 : Vérifier la suppression en répertoriant les principaux de service auxquels la stratégie est affectée 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Étapes suivantes
- Pour plus d'informations sur le fonctionnement de l'authentification dans Azure AD, consultez la section [Scénarios d'authentification pour Azure AD](develop/active-directory-authentication-scenarios.md).
- Pour plus d’informations sur l’authentification unique de l’utilisateur, consultez [Accès aux applications et authentification unique avec Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Consultez le [guide du développeur Active Directory](develop/active-directory-developers-guide.md) pour avoir une vue d’ensemble du contenu associé au développement.
