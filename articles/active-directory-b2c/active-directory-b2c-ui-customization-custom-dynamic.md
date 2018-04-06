---
title: 'Azure Active Directory B2C : personnalisation de l’interface utilisateur Azure AD B2C de façon dynamique avec les stratégies personnalisées'
description: Prendre en charge plusieurs expériences de personnalisation avec du contenu HTML5/CSS qui change de façon dynamique au moment de l’exécution.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/20/2017
ms.author: davidmu
ms.openlocfilehash: d8aaf0ef8cefdc0cb1c4ff3dec656d31c32f664f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C : configuration de l’interface utilisateur avec du contenu dynamique à l’aide de stratégies personnalisées
À l’aide de stratégies personnalisées Azure Active Directory B2C (Azure AD B2C), vous pouvez envoyer un paramètre dans une chaîne de requête. En transmettant le paramètre à votre point de terminaison HTML, vous pouvez changer le contenu de la page de façon dynamique. Par exemple, vous pouvez changer l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C en fonction d’un paramètre que vous transmettez depuis votre application web ou mobile. 

## <a name="prerequisites"></a>Prérequis

Cet article se concentre sur la façon de personnaliser l’interface utilisateur Azure AD B2C avec du *contenu dynamique* à l’aide de stratégies personnalisées. Pour commencer, consultez [Personnalisation de l’interface utilisateur dans une stratégie personnalisée](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>L’article Azure AD B2C, [Configurer la personnalisation de l’interface utilisateur dans une stratégie personnalisée](active-directory-b2c-ui-customization-custom.md), explique les notions de base suivantes :
> * La fonctionnalité de personnalisation de l’interface utilisateur de page.
> * Des outils essentiels pour tester la fonctionnalité de personnalisation d’interface utilisateur de page à l’aide de notre exemple de contenu.
> * Les éléments d’interface utilisateur de base de chaque type de page.
> * Bonnes pratiques pour l’application de cette fonctionnalité.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Ajouter d’un lien aux modèles HTML5/CSS pour votre parcours utilisateur

Dans une stratégie personnalisée, une définition de contenu définit l’URI de page HTML5 qui est utilisée pour une étape d’interface utilisateur spécifiée (par exemple, les pages de connexion ou d’inscription). La stratégie de base définit l’apparence par défaut en pointant vers un URI de fichiers HTML5 (dans les fichiers CSS). Dans la stratégie d’extension, vous pouvez modifier l’apparence en substituant le LoadUri du fichier HTML5. Les définitions de contenu comprennent des URL vers du contenu externe qui est défini en créant des fichiers HTML5/CSS, si nécessaire. 

La section `ContentDefinitions` contient une série d’éléments XML `ContentDefinition`. L’attribut d’ID de l’élément `ContentDefinition` spécifie le type des page qui se rapporte à la définition de contenu. Autrement dit, l’élément définit le contexte que va appliquer un modèle HTML5/CSS personnalisé. Le tableau suivant décrit l’ensemble d’ID de définition de contenu reconnus par le moteur d’infrastructure d’expérience d’identité et les types de page associés.

| ID de définition du contenu | Modèle HTML5 par défaut| Description | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Page d’erreur**. Cette page s’affiche lorsqu’une exception ou une erreur est rencontrée. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Page de sélection du fournisseur d’identité**. Cette page répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent faire leur choix au moment de la connexion. Il s’agit généralement de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sélection du fournisseur d’identité pour l’inscription**. Cette page répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent faire leur choix au moment de l’inscription. Il s’agit de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page de mot de passe oublié**. Cette page contient un formulaire que les utilisateurs doivent remplir pour lancer une réinitialisation de mot de passe.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page de connexion à un compte local**. Cette page contient un formulaire que l’utilisateur doit renseigner pour se connecter avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir une zone de saisie de texte et une zone de saisie de mot de passe. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription à un compte local**. Cette page contient un formulaire que l’utilisateur doit renseigner pour s’inscrire à un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles qu’une zone de saisie de texte, une zone de saisie de mot de passe, un bouton radio, des zones de liste déroulante à sélection unique ou des cases à sélection multiples. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Page d’authentification multi-facteur**. Cette page permet aux utilisateurs de vérifier leurs numéros de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription à un compte de réseau social**. Cette page contient un formulaire que l’utilisateur doit remplir au moment de l’inscription à l’aide d’un compte existant proposé par un fournisseur d’identité de réseaux sociaux. Cette page est similaire à la page d’inscription au compte de réseau social ci-dessus, à l’exception des champs de saisie de mot de passe. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Page de mise à jour de profil**. Cette page contient un formulaire auquel l’utilisateur peut accéder pour mettre à jour son profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Page de connexion ou d’inscription unifiée**. Cette page gère le processus d’inscription et de connexion des utilisateurs. Les utilisateurs peuvent utiliser des fournisseurs d’identité d’entreprise, des fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou des comptes locaux.  |

## <a name="serving-dynamic-content"></a>Utilisation de contenu dynamique
Dans l’article [Azure Active Directory B2C : Configurer la personnalisation de l’interface utilisateur dans une stratégie personnalisée](active-directory-b2c-ui-customization-custom.md), vous avez chargé des fichiers HTML5 vers le stockage Blob Azure. Ces fichiers HTML5 sont statiques et affichent le même contenu HTML pour chaque requête. 

Dans cet article, vous utilisez une application webASP.NET, qui peut accepter des paramètres de chaîne de requête et réagir en conséquence. 

Lors de cette procédure pas à pas, vous allez effectuer les opérations suivantes :
* Créer une application web ASP.NET Core qui héberge vos modèles HTML5. 
* Ajouter un modèle personnalisé HTML5, _unified.cshtml_. 
* Publier votre application web sur Azure App Service. 
* Définir un partage de ressources cross-origin (CORS) pour votre application web.
* Remplacer les éléments `LoadUri` pour pointer vers votre fichier HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Étape 1 : Créer une application web ASP.NET

1. Dans Visual Studio, créez un projet en sélectionnant **Fichier** > **Nouveau** > **Projet**.

2. Dans la fenêtre **Nouveau projet**, sélectionnez **Visual C#** > **Web** > **Application web ASP.NET Core (.NET Core)**.

3. Nommez l’application (par exemple, *Contoso.AADB2C.UI*), puis sélectionnez **OK**.

    ![Créer un projet Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Sélectionnez le modèle **Application web**.

5. Définissez l’authentification sur **Aucune authentification**.

    ![Sélectionnez le modèle Application web.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Cliquez sur **OK** pour créer le projet.

## <a name="step-2-create-mvc-view"></a>Étape 2 : Créer une vue MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Étape 2.1 : Télécharger le modèle HTML5 intégré à B2C
Votre modèle HTML5 personnalisé est basé sur le modèle HTML5 intégré à Azure AD B2C. Vous pouvez télécharger le [fichier unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou télécharger le modèle à partir du [pack de démarrage](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Vous utilisez ce fichier HTML5 pour créer une page de connexion ou d’inscription unifiée.

### <a name="step-22-add-the-mvc-view"></a>Étape 2.2 : Ajouter la vue MVC
1. Cliquez avec le bouton droit sur le dossier Vues/Accueil, puis sur **Ajouter** > **Nouvel élément**.

    ![Ajouter un élément MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Dans la fenêtre **Ajouter un nouvel élément - Contoso.AADB2C.UI**, sélectionnez **Web > ASP.NET**.

3. Sélectionnez **Page de vue MVC**.

4. Dans le champ **Nom**, remplacez le nom par **unified.cshtml**.

5. Sélectionnez **Ajouter**.

    ![Ajouter une vue MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Si le fichier *unified.cshtml* n’est pas déjà ouvert, double-cliquez dessus pour l’ouvrir, puis supprimez son contenu.

7. Pour cette procédure pas à pas, nous supprimons la référence à la page de disposition. Ajoutez l’extrait de code suivant au fichier _unified.cshtml_ :

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Ouvrez le fichier _unified.cshtml_ que vous avez téléchargé à partir du modèle HTML5 intégré à Azure AD B2C.

9. Remplacez les signes arobase uniques (@) par des signes arobase doubles (@@).

10. Copiez le contenu du fichier et collez-le sous la définition de la disposition. Votre code doit ressembler à ce qui suit :

    ![Fichier unified.cshtml après avoir ajouté le modèle HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Étape 2.3 : Changer l’image d’arrière-plan

Localisez l’élément `<img>` contenant la valeur `ID` *background_background_image*, puis remplacez la valeur `src` par **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** ou toute autre image d’arrière-plan que vous souhaitez utiliser.

![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Étape 2.4 : Ajouter votre vue au contrôleur MVC

1. Ouvrez **Controllers\HomeController.cs** et ajoutez la méthode suivante : 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ce code spécifie que la méthode doit utiliser un fichier de modèle de *vue* pour afficher une réponse dans le navigateur. Étant donné que nous ne spécifions pas de manière explicite le nom du fichier de modèle de *vue*, MVC est par défaut défini pour utiliser le fichier de vue _unified.cshtml_ dans le dossier */Vues/Accueil*.
    
    Une fois ajoutée la méthode _unified_, votre code doit ressembler à ce qui suit :
    
    ![Changer le contrôleur pour restituer la vue](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Déboguez votre application web et assurez-vous que la page _unified_ est accessible (par exemple, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Étape 2.5 : Publier dans Azure
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.UI**, puis sélectionnez **Publier**.

    ![Publier dans Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Sélectionnez la vignette **Microsoft Azure App Service**, puis cliquez sur **Publier**.

    ![Créer un Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    La fenêtre **Créer App Service** s’ouvre. Vous pouvez commencer à y créer toutes les ressources Azure nécessaires pour exécuter l’application web ASP.NET dans Azure.

    > [!NOTE]
    > Pour plus d’informations sur la publication, consultez : [Création d’une application web ASP.NET dans Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Dans la zone **Nom de l’application web**, tapez un nom d’application unique (les caractères autorisés sont les plages a-z, A-Z, 0-9, et le trait d’union (-)). L’URL de l’application web est `http://<app_name>.azurewebsites.NET`, où `<app_name>` correspond au nom de votre application web. Vous pouvez accepter le nom généré automatiquement, qui est unique.

4. Sélectionnez **Créer** pour commencer à créer les ressources Azure.

    ![Fournir les propriétés App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Une fois le processus de création terminé, l’Assistant publie l’application web ASP.NET dans Azure, puis lance l’application dans le navigateur par défaut.

5. Copiez l’URL de la page _unified_ (par exemple, _https://<nom_app>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Étape 3 : Configurer CORS dans Azure App Service
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **App Services**, puis sélectionnez le nom de votre application API.

    ![Sélectionner l’application API dans le portail Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Dans la section **Paramètres**, sous la section **API**, sélectionnez **CORS**.

    ![Sélectionner les paramètres CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Dans la fenêtre **CORS**, dans la zone **Origines autorisées**, effectuez une des opérations suivantes :

    * Entrez la ou les URL depuis laquelle/lesquelles vous souhaitez autoriser les appels JavaScript.
    * Entrez un astérisque ( * ) pour indiquer que tous les domaines d’origine sont acceptés.

4. Sélectionnez **Enregistrer**.

    ![Fenêtre CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Quand vous sélectionnez **Enregistrer**, l’application API accepte les appels JavaScript provenant des URL spécifiées. 

## <a name="step-4-html5-template-validation"></a>Étape 4 : Validation du modèle HTML5
Votre modèle HTML5 est prêt à être utilisé. Toutefois, il n’est pas disponible dans le code des `ContentDefinition`. Avant d’ajouter `ContentDefinition` à votre stratégie personnalisée, vérifiez les points suivants :
* Votre contenu est accessible et conforme à HTML5.
* CORS est activé sur votre serveur de contenu.

    >[!NOTE]
    >Pour vérifier que le site où vous hébergez votre contenu a activé CORS et qu’il peut tester des demandes CORS, accédez au site web [test-cors.org](http://test-cors.org/). 

* Votre contenu utilisé est sécurisé via **HTTPS**.
* Vous utilisez des *URL absolues* comme *https://yourdomain/content* pour tous les liens, les images et le contenu CSS.

## <a name="step-5-configure-your-content-definition"></a>Étape 5 : Configurer votre définition de contenu
Pour configurer `ContentDefinition`, effectuez les opérations suivantes :
1. Ouvrez le fichier de base de votre stratégie (par exemple, *TrustFrameworkBase.xml*).

2. Recherchez l’élément `<ContentDefinitions>`, puis copiez l’intégralité du contenu du nœud `<ContentDefinitions>`.

3. Ouvrez le fichier d’extension (par exemple, *TrustFrameworkExtensions.xml*), puis recherchez l’élément `<BuildingBlocks>`. Si l’élément n’existe pas, ajoutez-le.

4. Collez l’intégralité du contenu du nœud `<ContentDefinitions>` que vous avez copié en tant qu’enfant de l’élément `<BuildingBlocks>`. 

5. Recherchez le nœud `<ContentDefinition>` contenant `Id="api.signuporsignin"` dans le code XML que vous avez copié.

6. Modifiez la valeur de `LoadUri` en remplaçant _~/tenant/default/unified_ par _https://<nom_app>.azurewebsites.net/home/unified_.  
    Votre stratégie personnalisée doit ressembler à ce qui suit :
    
    ![Votre définition de contenu](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Étape 6 : Charger la stratégie sur votre locataire
1. Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), puis sélectionnez **Azure AD B2C**.

2. Sélectionnez **Infrastructure d’expérience d’identité**.

3. Sélectionnez **Toutes les stratégies**.

4. Sélectionnez **Charger la stratégie**.

5. Activez la case à cocher **Remplacer la stratégie si elle existe**.

6. Chargez le fichier *TrustFrameworkExtensions.xml*, puis assurez-vous que sa validation réussit.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Étape 7 : Tester la stratégie personnalisée en utilisant Exécuter maintenant
1. Sélectionnez **Paramètres Azure AD B2C**, puis **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >L’option Exécuter maintenant nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2. Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée, puis sélectionnez **Exécuter maintenant**.  
    Vous devez être en mesure de voir votre HTML5 personnalisé avec l’arrière-plan que vous avez créé précédemment.

    ![Votre stratégie d’inscription ou de connexion](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Étape 8 : Ajouter un contenu dynamique
Changez l’arrière-plan en fonction d’un paramètre de chaîne de requête nommé _campaignId_. Votre application par partie de confiance (applications web et mobiles) envoie le paramètre à Azure AD B2C. Votre stratégie lit le paramètre et envoie sa valeur à votre modèle HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Étape 8.1 : Ajouter un paramètre de définition de contenu

Ajoutez l’élément `ContentDefinitionParameters` en effectuant les opérations suivantes :
1. Ouvrez le fichier *SignUpOrSignin* de votre stratégie (par exemple, *SignUpOrSignin.xml*).

2. Recherchez le nœud `<DefaultUserJourney>`. 

3. Dans le nœud `<DefaultUserJourney>`, ajoutez l’extrait de code XML suivant :  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Étape 8.2 : Modifier votre code pour accepter le paramètre de chaîne de requête et remplacer l’image d’arrière-plan
Modifiez la méthode `unified` HomeController pour accepter le paramètre campaignId. La méthode vérifie ensuite la valeur du paramètre et définit la variable `ViewData["background"]` en conséquence.

1. Ouvrez le fichier *Controllers\HomeController.cs*, puis changez la méthode `unified` en ajoutant l’extrait de code suivant :

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Recherchez l’élément `<img>` avec l’ID `background_background_image` et remplacez la valeur `src` par `@ViewData["background"]`.

    ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3 : Charger les modifications et publier votre stratégie
1. Publiez votre projet Visual Studio sur Azure App Service.

2. Chargez la stratégie *SignUpOrSignin.xml* sur Azure AD B2C.

3. Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée, puis sélectionnez **Exécuter maintenant**.  
    Vous devez voir la même image d’arrière-plan que celle affichée auparavant.

4. Copiez l’URL de la barre d’adresses du navigateur.

5. Ajoutez le paramètre de chaîne de requête _campaignId_ à l’URI. Par exemple, ajoutez `&campaignId=hawaii`, comme illustré dans l’image suivante :

    ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Appuyez sur **Entrée** pour afficher l’image d’arrière-plan d’Hawaï.

    ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Remplacez la valeur par *Tokyo* et appuyez sur **Entrée**.  
    Le navigateur affiche l’arrière-plan de Tokyo.

    ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Étape 9 : Modifier le reste du parcours utilisateur
Si vous sélectionnez le lien **Inscrivez-vous dès maintenant** dans la page de connexion, le navigateur affiche l’image d’arrière-plan par défaut, pas l’image que vous avez définie. En effet, vous avez changé uniquement la page d’inscription ou de connexion. Pour changer le reste des définitions de contenu avec autodéclaration :
1. Revenez à l’étape 2 et effectuez les opérations suivantes :

    a. Téléchargez le fichier *selfasserted*.

    b. Copiez le contenu du fichier.

    c. Créez une vue, *selfasserted*.

    d. Ajoutez *selfasserted* au contrôleur **Accueil**.

2. Revenez à l’étape 4 et effectuez les opérations suivantes : 

    a. Dans votre stratégie d’extension, recherchez le nœud `<ContentDefinition>` qui contient `Id="api.selfasserted"`, `Id="api.localaccountsignup"` et `Id="api.localaccountpasswordreset"`.

    b. Définissez l’attribut `LoadUri` sur votre URI *selfasserted*.

3. Revenez à l’étape 8.2 et modifiez votre code pour accepter les paramètres de chaîne de requête, mais cette fois pour la fonction *selfasserted*. 

4. Chargez la stratégie *TrustFrameworkExtensions.xml*, puis vérifiez que sa validation réussit.

5. Exécutez le test de la stratégie, puis sélectionnez **Inscrivez-vous dès maintenant** pour voir le résultat.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facultatif) Télécharger les fichiers de stratégie et le code complets
* Une fois que vous avez [pris en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md), nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée. Des [exemples de fichiers de stratégie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization) sont à votre disposition pour référence.
* Vous pouvez télécharger le code complet dans [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




