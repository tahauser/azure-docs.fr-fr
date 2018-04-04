---
title: Intégration d’applications dans Azure Active Directory
description: Ajout, mise à jour ou suppression d’une application dans Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: PatAltimore
manager: mtillman
editor: mbaldwin
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: f08e7327e266c342fe7f869f0b7a6a251792a071
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="integrating-applications-with-azure-active-directory"></a>Intégration d’applications dans Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Les développeurs et les fournisseurs de logiciels en tant que service (SaaS) peuvent développer des services cloud commerciaux ou des applications métier qui peuvent être intégrés à Azure Active Directory (Azure AD) pour fournir un processus de connexion et d’autorisation sécurisé à leurs services. Pour intégrer une application ou un service à Azure AD, le développeur doit d’abord enregistrer l’application auprès de Azure AD.

Cet article vous montre comment ajouter, mettre à jour ou supprimer l’enregistrement d’une application dans Azure AD. Vous découvrez les différents types d'applications qui peuvent être intégrées à Azure AD, comment configurer vos applications pour accéder aux autres ressources telles que les API web, et bien plus encore.

Pour plus d’informations sur les deux objets Azure AD qui représentent une application enregistrée ainsi que sur leur relation, consultez [Objets principal du service et application](active-directory-application-objects.md). Pour connaître les directives de personnalisation à utiliser lors du développement d’applications avec Azure Active Directory, consultez [Instructions de personnalisation pour les applications intégrées](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Ajout d'une application
Toute application qui souhaite utiliser les fonctionnalités d’Azure AD doit d’abord être enregistrée dans un locataire Azure AD. Le processus d'enregistrement implique de fournir à Azure AD des informations sur votre application, notamment l'URL où elle est située, l'URL à laquelle envoyer une réponse après avoir authentifié un utilisateur, l'URI qui identifie l'application, et ainsi de suite.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Enregistrement d’une nouvelle application à l’aide du portail Azure
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application**.

   ![Inscrire une nouvelle application](./media/active-directory-integrating-applications/add-app-registration.png)

4. Lorsque la page **Créer** s’affiche, saisissez les informations d’inscription de votre application : 

  - **Nom :** Saisissez un nom d’application explicite
  - **Type d’application :** 
    - Sélectionnez « Native » pour les [applications clientes](active-directory-dev-glossary.md#client-application) installées localement sur un appareil. Ce paramètre est utilisé pour les [clients natifs](active-directory-dev-glossary.md#native-client) publics OAuth.
    - Sélectionnez « Application Web / API » pour les [applications clientes](active-directory-dev-glossary.md#client-application) et les [ressources/applications API](active-directory-dev-glossary.md#resource-server) installées sur un serveur sécurisé. Ce paramètre est utilisé pour les [clients web](active-directory-dev-glossary.md#web-client) confidentiels OAuth et les [clients basés sur un agent utilisateur](active-directory-dev-glossary.md#user-agent-based-client) publics. La même application peut également exposer un client et une ressource/API.
  - **URL de connexion :** Pour les applications de type « Application Web / API », indiquez l’URL de base de votre application. Par exemple, `http://localhost:31544` peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent utiliser cette URL pour se connecter à une application web cliente. 
  - **URI de redirection :** Pour les applications « natives », indiquez un URI de redirection utilisé par Azure AD pour retourner les réponses de jeton. Saisissez une valeur spécifique à votre application, par exemple `http://MyFirstAADApp`

   ![Inscrire une nouvelle application - créer](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Si vous souhaitez des exemples spécifiques pour les applications web ou natives, consultez les [Démarrages rapides](active-directory-developers-guide.md#get-started).

5. Lorsque vous avez terminé, cliquez sur **Créer**. Azure AD assigne un ID d’application unique à votre application, et vous êtes redirigé vers la page d’enregistrement principale de votre application. Selon que votre application est une application native ou web, différentes options sont disponibles afin d’ajouter des fonctionnalités supplémentaires à votre application. Consultez la section suivante pour avoir une vue d’ensemble du consentement et des détails sur l’activation de fonctionnalités de configuration supplémentaires dans l’inscription de votre application (informations d’identification, autorisations, activation de la connexion pour les utilisateurs à partir d’autres locataires.)

  > [!NOTE]
  > Par défaut, l'application nouvellement inscrite est configurée pour autoriser **uniquement** les utilisateurs du même locataire à se connecter à votre application.
  > 
  > 

## <a name="updating-an-application"></a>Mise à jour d’une application
Une fois votre application inscrite avec Azure AD, il est possible qu’elle doive être mise à jour pour fournir un accès aux API web, être mise à la disposition d'autres organisations, et plus encore. Cette section décrit les différents scénarios dans lesquels vous pouvez configurer davantage votre application. Tout d’abord, commençons par une vue d’ensemble de l’infrastructure de consentement qu’il est important de comprendre lors de la création d’applications devant être utilisées par d’autres utilisateurs ou applications.

### <a name="overview-of-the-consent-framework"></a>Vue d’ensemble de l’infrastructure de consentement

L’infrastructure de consentement de Azure AD facilite le développement d’applications clientes natives et d’applications web multi-locataires, y compris des applications multiniveaux. Ces applications autorisent la connexion au moyen de comptes d’utilisateurs d’un locataire Azure AD différent de celui où l’application a été inscrite. Elles peuvent également avoir à accéder aux API web telles que l’API Microsoft Graph (pour l’accès à Azure Active Directory, à Intune et aux services d’Office 365) et d’autres API de services Microsoft, en plus de vos propres API web. L’infrastructure est basée sur le consentement d’un utilisateur ou d’un administrateur à l’inscription d’une application dans son répertoire, ce qui peut impliquer l’accès aux données du répertoire.

Par exemple, si une application cliente web doit lire les informations de calendrier de l’utilisateur à partir d’Office 365, cet utilisateur doit d’abord donner son consentement à l’application cliente. Une fois le consentement donné, l’application cliente sera en mesure d’appeler l’API Microsoft Graph au nom de l’utilisateur et d’utiliser les informations de calendrier en fonction des besoins. [L’API Microsoft Graph](https://graph.microsoft.io) permet d’accéder aux données d’Office 365 (comme les calendriers et les messages Exchange, les sites et les listes SharePoint, les documents OneDrive, les blocs-notes OneNote, les tâches Organiseur, les classeurs Excel, etc.), ainsi qu’aux utilisateurs et groupes d’Azure AD, et à d’autres objets de données provenant d’autres services cloud Microsoft. 

L'infrastructure de consentement est conçue sur OAuth 2.0 et ses différents flux, notamment l’octroi d’un code d’autorisation et d’informations d'identification du client, à l'aide de clients publics ou confidentiels. En utilisant OAuth 2.0, Azure AD permet de créer de nombreux types d'applications clientes, sur téléphone, tablette, serveur ou web, et d'accéder aux ressources requises.

Pour plus d’informations sur l’utilisation de l’infrastructure de consentement avec les demandes d’autorisation OAuth2.0, consultez [Autoriser l’accès aux applications web à l’aide de OAuth 2.0 et de Azure AD](active-directory-protocols-oauth-code.md) et [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md). Pour plus d’informations sur l’obtention d’un accès autorisé à Office 365 via Microsoft Graph, consultez [Authentification de l’application avec Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Exemple d’expérience de consentement

Les étapes suivantes vous montrent comment l’expérience de consentement fonctionne à la fois pour le développeur d'applications et pour l'utilisateur.

1. Supposons que vous ayez une application cliente web devant demander des autorisations spécifiques pour accéder à une ressource/API. Vous allez apprendre à effectuer cette configuration dans la section suivante, mais le portail Azure est essentiellement utilisé pour déclarer des demandes d’autorisation au moment de la configuration. Comme d’autres paramètres de configuration, elles sont incluses dans l’inscription Azure AD de l’application :
   
  ![Autorisations pour d'autres applications](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Considérons que les autorisations de votre application ont été mises à jour, que l'application s'exécute et qu’un utilisateur s'apprête à l’utiliser pour la première fois. L’application doit tout d’abord obtenir un code d’autorisation du point de terminaison `/authorize` de Azure AD. Le code d’autorisation peut ensuite être utilisé pour obtenir un nouvel accès et un jeton d’actualisation.

3. Si l’utilisateur n’est pas déjà authentifié, le point de terminaison `/authorize` de Azure AD vous invite à vous connecter.
   
  ![Connexion d’un utilisateur ou d’un administrateur à Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Une fois l'utilisateur connecté, Azure AD déterminera s’il est nécessaire d'afficher une page de consentement pour l’utilisateur. La détermination est différente selon que l’utilisateur (ou l’administrateur de son organisation), a déjà accordé ou non son consentement à l'application. Si l'autorisation n'a pas encore été accordée, Azure AD invite l'utilisateur à fournir son consentement et affiche les autorisations dont il a besoin pour fonctionner. L’ensemble des autorisations qui s’affichent dans la boîte de dialogue de consentement correspondent à celles sélectionnées dans la section Autorisations déléguées du portail Azure.
   
  ![Expérience de consentement de l'utilisateur](./media/active-directory-integrating-applications/consent.png)

5. Une fois que l'utilisateur a donné son consentement, un code d'autorisation est retourné à votre application, qui est utilisé pour acquérir un jeton d'accès et un jeton d'actualisation. Pour plus d’informations sur ce flux, consultez la [section Application web vers API web dans Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. En tant qu’administrateur, vous pouvez également donner votre consentement pour les autorisations déléguées d’une application pour le compte de tous les utilisateurs de votre client. Le consentement administrateur empêche l’affichage d’une boîte de dialogue pour chaque utilisateur dans le locataire, il se paramètre depuis la page de votre application dans le [portail Azure](https://portal.azure.com). Dans la page **Paramètres** de votre application, cliquez sur **Autorisations requises**, puis sur le bouton **Accorder des autorisations**. 

  ![Accorder des autorisations pour un consentement administrateur explicite](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Le fait d’accorder un consentement explicite à l’aide du bouton **Accorder des autorisations** est actuellement nécessaire pour les applications à page unique (SPA) qui utilisent ADAL.js. Sinon, l’application échoue lorsque le jeton d’accès est demandé.   

### <a name="configure-a-client-application-to-access-web-apis"></a>Configurer une application cliente pour accéder aux API web
Une application cliente web/confidentielle doit établir des informations d’identification sécurisées afin de pouvoir participer à un flux d’octroi d’autorisations qui requiert une authentification. La méthode d’authentification par défaut prise en charge par le portail Azure est l’ID Client + la clé secrète. Cette section décrit les étapes de configuration requises pour fournir les informations d’identification de votre client à la clé secrète.

En outre, avant qu’un client puisse accéder à une API web exposée par une application de ressources (comme l’API Microsoft Graph), l’infrastructure de consentement permet au client d’obtenir l’autorisation nécessaire en fonction des autorisations demandées. Par défaut, toutes les applications peuvent choisir des autorisations à partir de « Azure Active Directory Windows » (API Graph) et « API Gestion des services Azure  Windows». L’[autorisation « Connexion et lecture du profil utilisateur » de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) est également sélectionnée par défaut. Si votre client est en cours d’enregistrement dans un locataire possédant des comptes inscrits à Office 365, vous aurez également la possibilité de sélectionner les API web et les autorisations pour Exchange Online et SharePoint. Vous pouvez choisir à partir de [deux types d’autorisations](active-directory-dev-glossary.md#permissions) pour chaque API web souhaitée :

- Autorisations de l’application : votre application cliente doit accéder à l’API web directement en tant que telle (aucun contexte utilisateur). Ce type d'autorisation requiert le consentement de l'administrateur et n'est également pas disponible pour les applications clientes natives.

- Autorisations déléguées : votre application cliente doit accéder à l’API web en tant qu’utilisateur connecté, mais avec un accès limité par l’autorisation sélectionnée. Ce type d'autorisation peut être accordé par un utilisateur, à moins que l'autorisation nécessite le consentement de l'administrateur. 

  > [!NOTE]
  > L’ajout d’une autorisation déléguée à une application n’accorde pas automatiquement un consentement aux utilisateurs du client. Les utilisateurs doivent donner manuellement leur consentement pour les autorisations déléguées ajoutées, au moment de l’exécution, à moins que l’administrateur clique sur le bouton **Accorder des autorisations** dans la section **Autorisations requises** de la page de l’application dans le portail Azure. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Pour ajouter des informations d’identification d’application ou des autorisations pour accéder aux API web
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer.

   ![Mettre à jour l’inscription d’une application](./media/active-directory-integrating-applications/update-app-registration.png)

4. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application. Pour ajouter une clé secrète aux informations d’identification de votre application web :
  - Cliquez sur la section **Clés** de la page **Paramètres**.  
  - Ajoutez une description pour votre clé.
  - Sélectionnez une durée de un ou deux ans.
  - Cliquez sur **Enregistrer**. La colonne située le plus à droite contient la valeur de clé, après avoir enregistré les modifications de configuration. **Veillez à copier la clé** pour une utilisation dans le code de votre application client, car elle n’est plus accessible après avoir quitté cette page.

  ![Mettre à jour l’inscription d’une application - clés](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Pour ajouter des autorisations pour accéder aux API de ressources à partir de votre client
  - Cliquez sur la section **Autorisations requises** de la page **Paramètres**. 
  - Cliquez sur le bouton **Add** .
  - Cliquez sur **Sélectionner une API** pour sélectionner le type de ressources que vous souhaitez choisir.
  - Parcourez la liste des API disponibles ou utilisez la zone de recherche pour sélectionner une des applications de ressources qui exposent une API web disponibles dans votre répertoire. Cliquez sur la ressource qui vous intéresse, puis sur **Sélectionner**.
  - Vous êtes dirigé vers la page **Activer l’accès**. Sélectionnez les autorisations d’Application et/ou les autorisations déléguées votre application a besoin pour accéder à l’API.
   
  ![Mettre à jour l’inscription d’une application - API d’autorisation](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Mettre à jour l’inscription d’une application - Autorisations](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Lorsque vous avez terminé, cliquez sur le bouton **Sélectionner** sur la page **Activer l’accès**, puis le bouton **Fait** sur la page **Ajouter un accès API**. Vous êtes redirigé sur la page **Autorisations requises**, où la nouvelle ressource est ajoutée à la liste des API.

  > [!NOTE]
  > Lorsque vous cliquez sur le bouton **Terminé**, les autorisations sont automatiquement définies pour l’application de votre répertoire en fonction des autorisations pour les autres applications que vous avez configurées.  Vous pouvez afficher ces autorisations d’application dans la page **Paramètres** de l’application.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuration d’une application de ressource pour exposer les API web

Vous pouvez développer une API web et la mettre à disposition des applications clientes en exposant l’[étendue](active-directory-dev-glossary.md#scopes) et les [rôles](active-directory-dev-glossary.md#roles) d’accès. Une API web correctement configurée peut être mise à disposition tout comme les autres API web Microsoft, notamment l'API Graph et les API Office 365. Les étendues d’accès sont exposées par le biais du [manifeste de votre application](active-directory-dev-glossary.md#application-manifest), c’est-à-dire un fichier JSON représentant la configuration d’identité de votre application. 

La section suivante indique comment exposer les étendues d’accès en modifiant le manifeste de l’application de la ressource.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Ajout d’étendues d’accès à votre application de ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.

3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer.

   ![Mettre à jour l’inscription d’une application](./media/active-directory-integrating-applications/update-app-registration.png)

4. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application. Basculez vers la page **Modifier le manifeste**, en cliquant sur **Manifeste** depuis la page d’inscription de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de **Modifier** le manifeste depuis le portail. Si vous le souhaitez, vous pouvez cliquer sur **Télécharger** et le modifier localement, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application.

5. Dans cet exemple, nous allons exposer une nouvelle étendue appelée `Employees.Read.All` sur notre ressource/API, en ajoutant l’élément JSON suivant pour la collection `oauth2Permissions`. L’étendue `user_impersonation` existante est fournie par défaut lors de l’inscription. `user_impersonation`permet à une application cliente de demander l’autorisation d’accéder à la ressource, sous l’identité de l’utilisateur connecté. Veillez à ajouter la virgule après l’élément d’étendue `user_impersonation` existant, et modifiez les valeurs de propriété en fonction des besoins de votre ressource. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > La valeur d’ID doit être générée à l'aide d'un outil de génération de GUID comme [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) ou d'un programme. Elle représente un identificateur unique pour l’étendue comme exposée par l'API web. Une fois qu’un client est correctement configuré avec les autorisations pour accéder à votre API web, Azure AD émet un jeton d’accès OAuth2.0. Lorsque le client appelle l’API web, il présente le jeton d’accès dont la revendication de l’étendue (scp) a été configurée sur les autorisations demandées dans son inscription d’application.
  >
  > Vous pouvez exposer des étendues supplémentaires ultérieurement si nécessaire. Considérez que votre API web peut exposer plusieurs étendues associées à un éventail de fonctions différentes. Votre ressource peut contrôler l’accès à l’API web lors de l’exécution, en évaluant la/les revendication(s) de l’étendue (`scp`) dans le jeton d’accès OAuth 2.0 reçu.
  > 

6. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Votre API web est maintenant configurée pour être utilisée par d’autres applications de votre répertoire.  

  ![Mettre à jour l’inscription d’une application](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Vérifier que l’API web est exposée à d’autres applications dans votre locataire
1. Retournez à votre locataire Azure AD, cliquez de nouveau sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application cliente que vous souhaitez configurer.

   ![Mettre à jour l’inscription d’une application](./media/active-directory-integrating-applications/update-app-registration.png)

2. Répétez l’étape 5 comme vous l’avez fait dans [Configurer une application cliente pour accéder aux API web](#configure-a-client-application-to-access-web-apis). Lorsque vous arrivez à l’étape **Sélectionner une API**, recherchez votre ressource en saisissant son nom d’application dans le champ de recherche, puis cliquez sur **Sélectionner**. 

3. Sur la page **Activer l’accès**, vous devriez voir la nouvelle étendue, disponible pour les demandes d’autorisations clientes.

  ![Les nouvelles autorisations sont affichées.](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Informations complémentaires concernant le manifeste d’application

Le manifeste d’application sert de mécanisme de mise à jour de l’entité Application, qui définit tous les attributs de configuration d’identité d’une application Azure AD, y compris les étendues d’accès d’API dont il a été question ci-dessus. Pour plus d’informations sur l’entité Application et son schéma, consultez la [documentation relative à l’entité Application de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Cet article contient des informations de référence complètes sur les membres de l’entité Application permettant de spécifier des autorisations pour votre API, y compris :  

- Le membre appRoles, qui est une collection d’entités [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), utilisée pour définir les [autorisations d’application](active-directory-dev-glossary.md#permissions) pour une API web. 
- Le membre oauth2Permissions, qui est une collection d’entités [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), utilisée pour définir les [autorisations déléguées](active-directory-dev-glossary.md#permissions) pour une API web.

Pour des informations plus générales sur les concepts de manifeste d’application, consultez la page [Connaître le manifeste d’application Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Accès à Azure AD Graph et à Office 365 par le biais des API Microsoft Graph  

Comme mentionné précédemment, vous pouvez non seulement exposer des API et y accéder pour vos propres applications, mais également inscrire votre application cliente pour accéder aux API exposées par les ressources Microsoft. L’API Graph de Microsoft, nommée « Microsoft Graph » dans la liste des API/ressources du portail, est disponible pour toutes les applications inscrites avec Azure AD. Si vous inscrivez votre application cliente dans un locataire contenant des comptes ayant souscrits un abonnement Office 365, vous pouvez également accéder aux étendues exposées par les différentes ressources de Office 365.

Pour découvrir une description complète des étendues exposées par l’API Microsoft Graph, consultez l’article [Étendues des autorisations | Concepts d’API Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

> [!NOTE]
> En raison d'une limitation actuelle, les applications clientes natives ne peuvent appeler l'API Graph Azure AD que si elles utilisent l'autorisation « Accéder au répertoire de votre organisation ». Cette restriction ne s'applique pas aux applications web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configuration d'applications multilocataires

Lorsque vous inscrivez une application dans Azure AD, vous pouvez choisir de rendre votre application accessible uniquement aux utilisateurs de votre organisation. Vous pouvez également rendre votre application accessible aux utilisateurs d'organisations externes. Ces deux types d’applications sont appelés applications à locataire unique et applications multilocataires. Cette section décrit la modification de la configuration d'une application à locataire unique pour en faire une application multilocataire.

Il est important de noter les différences entre une application à locataire unique et une application multilocataire :  

- Une application à locataire unique est prévue pour une utilisation dans une seule organisation. Il s’agit généralement d’une application métier écrite par un développeur de l’entreprise. Une application à locataire unique est uniquement accessible par les utilisateurs disposant de comptes dans le même locataire que l’inscription de l’application. Par conséquent, elle doit être approvisionnée dans un seul répertoire.
- Une application multilocataire est prévue pour une utilisation dans plusieurs organisations. Il s’agit d’une application SaaS (software-as-a-service) web généralement écrite par un éditeur de logiciels indépendant. Les applications multilocataires doivent être approvisionnées dans chaque locataire où les utilisateurs doivent avoir accès. Pour les locataires autres que celui où l’application est inscrite, un consentement de l’utilisateur ou de l’administrateur est nécessaire pour les inscrire. Notez que les applications clientes natives sont multilocataires par défaut lorsqu’elles sont installées sur l’appareil du propriétaire de la ressource. Consultez la section [Vue d’ensemble de l’infrastructure de consentement](#overview-of-the-consent-framework) pour plus d’informations sur l’infrastructure de consentement.

Rendre une application multilocataire nécessite des modifications de l’inscription de l’application ainsi que des modifications au niveau de l’application web elle-même. Les sections suivantes couvrent ces deux points.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Modification de l’inscription de l’application pour devenir multilocataire

Si vous écrivez une application que vous souhaitez proposer à vos clients ou à des partenaires externes à votre organisation, vous devez mettre à jour la définition de l’application dans le portail Azure.

> [!IMPORTANT]
> Azure AD nécessite que l’URI ID d’application des applications multilocataires soit globalement unique. L’URI ID d’application est l’une des méthodes d’identification d'une application dans les messages de protocole. Pour une application à client unique, il suffit que l’URI ID d’application soit unique au sein de ce client. Pour une application mutualisée, l’URI doit être globalement unique afin qu’Azure AD puisse trouver l’application sur tous les clients. L’unicité globale est appliquée en obligeant l’URI ID d’application à avoir un nom d’hôte correspondant à un domaine vérifié du client Azure AD. Par exemple, si le nom de votre client est contoso.onmicrosoft.com, un URI ID d’application valide serait https://contoso.onmicrosoft.com/myapp. Si votre locataire a un domaine vérifié de contoso.com, un URI ID d’application valide serait également https://contoso.com/myapp. Si l’URI ID d’application ne suit pas ce modèle, une application ne peut pas être définie comme multi-locataire.
> 

Pour permettre aux utilisateurs externes d’accéder à votre application : 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
4. Sur la page **Paramètres**, cliquez sur **Propriétés** et basculez le **Commutateur mutualisé** sur **Oui**.

Après avoir fait les modifications, les utilisateurs et les administrateurs dans d’autres organisations sont en mesure d’accorder à leurs utilisateurs la possibilité de se connecter à votre application, permettant à votre application d’accéder à des ressources sécurisées par leurs locataires.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Modification de l’application pour devenir multilocataire

La prise en charge des applications multilocataires s’appuie en grande partie sur l’infrastructure de consentement de Azure AD. Le consentement est le mécanisme permettant à un utilisateur d’un autre locataire, d’accorder l’accès de l’application aux ressources sécurisées par le locataire de l’utilisateur. Cette expérience est appelée « consentement de l’utilisateur ».

Votre application web peut également offrir :

- La possibilité pour les administrateurs « d’inscrire ma société ». Cette expérience, appelée « consentement d’administrateur », donne à un administrateur la possibilité de donner son consentement au nom de *tous les utilisateurs* dans leur organisation. Seul l’utilisateur qui s’authentifie avec un compte appartenant au rôle Administrateur général peut fournir son consentement d’administrateur ; les autres utilisateurs reçoivent un message d’erreur.

- Une expérience d’inscription pour les utilisateurs. Il est prévu que l’utilisateur dispose d’un bouton « inscription » qui redirige le navigateur vers le point de terminaison `/authorize` OAuth2.0 de Azure AD ou un point de terminaison `/userinfo` OpenID Connect. Ces points de terminaison permettent à l'application d’obtenir des informations sur le nouvel utilisateur en inspectant l'id_token. Après la phase d’inscription, l’utilisateur reçoit une invite de consentement similaire à celle illustrée dans la section [Vue d’ensemble de l’infrastructure de consentement](#overview-of-the-consent-framework).

Pour plus d’informations sur les modifications d’application requises pour prendre en charge l’accès multilocataire et les expériences de connexion/inscription, consultez :

- [Comment connecter un utilisateur Azure Active Directory (AD) à l’aide du modèle d’application mutualisée](active-directory-devhowto-multi-tenant-overview.md)
- La liste des [exemples de code de l’architecture multilocataire](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Démarrage rapide : Ajouter la marque de votre société à votre page de connexion dans Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Activation de l’accord implicite OAuth 2.0 pour les applications à page unique

Les applications à page unique (SPA) se composent généralement d’une partie frontale reposant largement sur JavaScript qui s’exécute dans le navigateur. Celle-ci appelle l’API web principale de l’application pour effectuer sa logique métier. Pour les applications à page unique hébergées dans Azure AD, l’accord implicite OAuth 2.0 permet d’authentifier l’utilisateur auprès de Azure AD et d’obtenir un jeton qui peut servir à sécuriser les appels du client JavaScript de l’application à son API web principale. 

Une fois que l’utilisateur a donné son consentement, ce même protocole d’authentification peut servir à obtenir des jetons pour sécuriser les appels entre le client et d’autres ressources API web configurées pour l’application. Pour plus d’informations sur l’octroi d’autorisation implicite et pour vous aider à décider si cette méthode est adaptée à votre scénario d’application, consultez [Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md).

Par défaut, l’accord implicite OAuth 2.0 est désactivé pour les applications. Vous pouvez activer l’accord implicite OAuth 2.0 pour votre application en définissant la valeur `oauth2AllowImplicitFlow` dans son [manifeste d’application](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Pour activer l’accord implicite OAuth 2.0

> [!NOTE]
> Pour plus d’informations sur la façon de modifier le manifeste d’application, assurez-vous de revoir d’abord la section précédente, [Configuration d’une application de ressource pour exposer des API web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
4. Basculez vers la page **Modifier le manifeste**, en cliquant sur **Manifeste** depuis la page d’inscription de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de **Modifier** le manifeste depuis le portail. Recherchez la valeur « oauth2AllowImplicitFlow » et définissez-la sur « true ». Par défaut, elle est définie sur « false ».
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Enregistrez le manifeste mis à jour. Une fois l’enregistrement effectué, votre API web est configurée pour utiliser l’accord implicite OAuth 2.0 pour authentifier les utilisateurs.

## <a name="removing-an-application"></a>Suppression d'une application
Cette section décrit comment supprimer l’inscription d’une application de votre locataire Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Suppression d’une application créée par votre organisation
Les applications inscrites par votre organisation sont affichées sous le filtre « Mes applications » sur la page « inscriptions d’application » principale de votre locataire. Ces applications sont celles que vous avez inscrites soit manuellement au moyen du portail Azure, soit par un programme par le biais de PowerShell ou de l’API Graph. Plus précisément, ces applications sont représentées dans votre locataire par des objets Application et Principal du service. Pour plus d’informations, voir [Objets principal du service et application](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Pour supprimer une application à locataire unique de votre répertoire
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
4. Dans la page d’inscription principale de l’application, cliquez sur **Supprimer**.
5. Cliquez sur **Oui** dans le message de confirmation.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Pour supprimer une application multilocataire de son répertoire de base
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
4. À partir de la page **Paramètres**, choisissez **Propriétés** et basculez **Multilocataire** sur **Non** pour commencer par passer votre application en locataire unique, puis cliquez sur **Enregistrer**. Les objets principal du service de l’application restent dans les locataires de toutes les organisations qui ont déjà consenti.
5. Dans la page d’inscription principale de l’application, cliquez sur le bouton **Supprimer**.
6. Cliquez sur **Oui** dans le message de confirmation.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Suppression d’une application multilocataire autorisée par une autre organisation
Un sous-ensemble des applications qui s’affichent sous le filtre « Toutes les applications » (en excluant les inscriptions de « Mes applications ») sur la page « Inscriptions d’application » principale de votre locataire, est constitué des applications multilocataires. En termes techniques, ces applications multilocataires proviennent d’un autre locataire et ont été enregistrées dans votre locataire au cours du processus de consentement. Ces applications sont représentées dans votre locataire par un objet principal du service, sans objet d’application correspondant. Pour plus d’informations sur les différences entre les objets de principal du service et d’application, consultez [Objets application et principal du service dans Azure AD](active-directory-application-objects.md).

Afin de pouvoir supprimer l’accès d’une application multilocataires à votre répertoire (après avoir donné son consentement), l’administrateur de l’entreprise doit supprimer son principal du service. L’administrateur doit avoir un accès d’administrateur général et peut supprimer ou utiliser les [applets de commande PowerShell Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) au sein du portail Azure pour supprimer l’accès.

## <a name="next-steps"></a>étapes suivantes
- Pour plus d'informations sur le fonctionnement de l'authentification dans Azure AD, consultez la section [Scénarios d'authentification pour Azure AD](active-directory-authentication-scenarios.md).
- Consultez les [instructions de personnalisation pour applications intégrées](active-directory-branding-guidelines.md) afin d’obtenir des conseils sur l’aide visuelle pour votre application.
- Consultez la rubrique [Objets principal du service et application](active-directory-application-objects.md) pour plus d’informations sur la relation existant entre les objets principal du service et application de l’application.
- Pour en savoir plus sur le rôle joué par le manifeste d’application, consultez [Connaître le manifeste d’application Azure Active Directory](active-directory-application-manifest.md).
- Consultez le [glossaire du développeur Azure AD](active-directory-dev-glossary.md) pour connaître les définitions de certains des principaux concepts de développement Azure Active Directory (AD).
- Consultez le [guide du développeur Active Directory](active-directory-developers-guide.md) pour avoir une vue d’ensemble du contenu associé au développement.

