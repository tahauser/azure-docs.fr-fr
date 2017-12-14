
## <a name="set-up-your-project"></a>Configuration de votre projet

Cette section explique comment installer et configurer le pipeline d’authentification via l’intergiciel OWIN sur un projet ASP.NET à l’aide d’OpenID Connect. 

> Vous préférez télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) et passez à l’étape [Configuration](#create-an-application-express) pour configurer l’exemple de code avant l’exécution.

<!--start-collapse-->
> ### <a name="create-your-aspnet-project"></a>Créer votre projet ASP.NET

> 1. Dans Visual Studio : `File` > `New` > `Project`<br/>
> 2. Sous *Visual C# \Web*, sélectionnez `ASP.NET Web Application (.NET Framework)`.
> 3. Donnez un nom à votre application et cliquez sur *OK*.
> 4. Sélectionnez `Empty` et cochez la case pour ajouter des références `MVC`.
<!--end-collapse-->

## <a name="add-authentication-components"></a>Ajouter les composants d’authentification

1. Dans Visual Studio : `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Ajoutez *les packages NuGet de l’intergiciel OWIN* en saisissant la commande suivante dans la fenêtre Console du gestionnaire de package :

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>À propos de ces bibliothèques

>Les bibliothèques ci-dessus activent l’authentification unique à l’aide d’OpenID Connect via l’authentification basée sur les cookies. Une fois que l’authentification est terminée et que le jeton qui représente l’utilisateur est envoyé à votre application, l’intergiciel OWIN crée un cookie de session. Le navigateur utilise ensuite ce cookie pour les requêtes ultérieures afin que l’utilisateur n’ait pas besoin de saisir à nouveau son mot de passe, et aucune vérification complémentaire n’est nécessaire.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurer le pipeline d’authentification
Les étapes suivantes permettent de créer une classe de démarrage d’intergiciel OWIN pour configurer l’authentification OpenID Connect. Cette classe sera exécutée automatiquement au démarrage de votre processus IIS.

> Si votre projet n’a pas de fichier `Startup.cs` dans le dossier racine :<br/>
> 1. Cliquez avec le bouton droit sur le dossier racine du projet : >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nommez-le `Startup.cs`.

> Assurez-vous que la classe sélectionnée est une classe de démarrage OWIN et non une classe C# standard. Pour le savoir, vérifiez que `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` s’affiche au-dessus de l’espace de noms.


1. Ajoutez les références *OWIN* et *Microsoft.IdentityModel* à `Startup.cs` :

```csharp
using Microsoft.Owin;
using Owin;
using Microsoft.IdentityModel.Protocols;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using Microsoft.Owin.Security.Notifications;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Remplacez la classe de démarrage par le code ci-dessous :
</li>
</ol>

```csharp
public class Startup
{        
    // The Client ID is used by the application to uniquely identify itself to Azure AD.
    string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

    // RedirectUri is the URL where the user will be redirected to after they sign in.
    string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

    // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
    static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

    // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
    string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

    /// <summary>
    /// Configure OWIN to use OpenIdConnect 
    /// </summary>
    /// <param name="app"></param>
    public void Configuration(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Sets the ClientId, authority, RedirectUri as obtained from web.config
                ClientId = clientId,
                Authority = authority,
                RedirectUri = redirectUri,
                // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                PostLogoutRedirectUri = redirectUri,
                Scope = OpenIdConnectScopes.OpenIdProfile,
                // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                ResponseType = OpenIdConnectResponseTypes.IdToken,
                // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters() { ValidateIssuer = false },
                // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    AuthenticationFailed = OnAuthenticationFailed
                }
            }
        );
    }

    /// <summary>
    /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
    /// </summary>
    /// <param name="context"></param>
    /// <returns></returns>
    private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
    {
        context.HandleResponse();
        context.Response.Redirect("/?errormessage=" + context.Exception.Message);
        return Task.FromResult(0);
    }
}

```
<!--start-collapse-->
> ### <a name="more-information"></a>Informations complémentaires

> Les paramètres que vous fournissez dans *OpenIDConnectAuthenticationOptions* serviront de coordonnées pour que l’application puisse communiquer avec Azure AD. Vous devez également configurer l’authentification des cookies comme indiqué dans le code ci-dessus, car l’intergiciel OpenID Connect utilise des cookies en arrière-plan. La valeur *ValidateIssuer* indique à OpenID Connect de ne pas restreindre l’accès à une organisation spécifique.
<!--end-collapse-->

