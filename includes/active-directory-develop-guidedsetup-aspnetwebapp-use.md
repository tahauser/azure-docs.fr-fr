
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Ajouter un contrôleur pour gérer les demandes de connexion et de déconnexion

Cette étape explique comment créer un contrôleur pour exposer les méthodes de connexion et de déconnexion.

1.  Cliquez avec le bouton droit sur le dossier `Controllers` et sélectionnez `Add` > `Controller`.
2.  Sélectionnez `MVC (.NET version) Controller – Empty`.
3.  Cliquez sur *Ajouter*.
4.  Nommez-le `HomeController`, puis cliquez sur *Ajouter*.
5.  Ajoutez les références *OWIN* à la classe :

```csharp
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
```
<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Ajoutez les deux méthodes ci-dessous pour gérer la connexion à votre contrôleur et la déconnexion de celui-ci en lançant une demande d’authentification via du code :
</li>
</ol>

```csharp
/// <summary>
/// Send an OpenID Connect sign-in request.
/// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
/// </summary>
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
    HttpContext.GetOwinContext().Authentication.SignOut(
            OpenIdConnectAuthenticationDefaults.AuthenticationType,
            CookieAuthenticationDefaults.AuthenticationType);
}
```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Créer la page d’accueil de l’application pour connecter des utilisateurs via un bouton de connexion

Dans Visual Studio, créez une vue pour ajouter le bouton de connexion et afficher les informations de l’utilisateur après l’authentification :

1.  Cliquez avec le bouton droit sur le dossier `Views\Home` et sélectionnez `Add View`.
2.  Nommez-le `Index`.
3.  Ajoutez le code HTML suivant, qui inclut le bouton de connexion, au fichier :

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Guide</title>
</head>
<body>
@if (!Request.IsAuthenticated)
{
    <!-- If the user is not authenticated, display the sign-in button -->
    <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
        <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
        <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
        <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
        <rect x="150" y="129" width="122" height="122" fill="#F35325" />
        <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
        <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
        <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
        <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
        </svg>
    </a>
}
else
{
    <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
    <br /><br />
    @Html.ActionLink("See Your Claims", "Index", "Claims")
    <br /><br />
    @Html.ActionLink("Sign out", "SignOut", "Home")
}
@if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
{
    <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
}
</body>
</html>
```
<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires
> Cette page ajoute un bouton de connexion au format SVG avec un arrière-plan noir :<br/>![Se connecter avec Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Pour d’autres boutons de connexion, accédez à [cette page](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Conseils pour la personnalisation").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Ajouter un contrôleur pour afficher les revendications de l’utilisateur
Ce contrôleur démontre les utilisations de l’attribut `[Authorize]` pour protéger un contrôleur. Cet attribut limite l’accès au contrôleur en autorisant uniquement les utilisateurs authentifiés. Le code ci-dessous tire parti de l’attribut pour afficher les revendications de l’utilisateur qui ont été récupérées dans le cadre de la connexion.

1.  Cliquez avec le bouton droit sur le dossier `Controllers` et sélectionnez `Add` > `Controller`
2.  Sélectionnez `MVC {version} Controller – Empty`.
3.  Cliquez sur *Ajouter*.
4.  Nommez-le `ClaimsController`.
5.  Remplacez le code de votre classe de contrôleur par le code ci-dessous afin d’ajouter l’attribut `[Authorize]` à la classe :

```csharp
[Authorize]
public class ClaimsController : Controller
{
    /// <summary>
    /// Add user's claims to viewbag
    /// </summary>
    /// <returns></returns>
    public ActionResult Index()
    {
        var claimsPrincipalCurrent = System.Security.Claims.ClaimsPrincipal.Current;
        //You get the user’s first and last name below:
        ViewBag.Name = claimsPrincipalCurrent.FindFirst("name").Value;

        // The 'preferred_username' claim can be used for showing the username
        ViewBag.Username = claimsPrincipalCurrent.FindFirst("preferred_username").Value;

        // The subject claim can be used to uniquely identify the user across the web
        ViewBag.Subject = claimsPrincipalCurrent.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier).Value;

        // TenantId is the unique Tenant Id - which represents an organization in Azure AD
        ViewBag.TenantId = claimsPrincipalCurrent.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;

        return View();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires
> En raison de l’utilisation de l’attribut `[Authorize]`, toutes les méthodes de ce contrôleur peuvent uniquement être exécutées si l’utilisateur est authentifié. Si l’utilisateur n’est pas authentifié et tente d’accéder au contrôleur, OWIN initialise une demande d’authentification et oblige l’utilisateur à s’authentifier. Le code ci-dessus examine la collection de revendications de l’instance `ClaimsPrincipal.Current` pour les attributs utilisateur spécifiques inclus dans le jeton de l’utilisateur. Ces attributs incluent le nom complet et le nom d’utilisateur de l’utilisateur, ainsi que le sujet d’identificateur d’utilisateur global. Il contient également le *ID client*, qui représente l’identifiant de l’organisation de l’utilisateur. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Créer une vue pour afficher les revendications de l’utilisateur

Dans Visual Studio, créez une vue pour afficher les revendications de l’utilisateur dans une page web :

1.  Cliquez avec le bouton droit sur le dossier `Views\Claims` et sélectionnez `Add View`.
2.  Nommez-le `Index`.
3.  Ajoutez le code HTML suivant au fichier :

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Sample</title>
    <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
</head>
<body style="padding:50px">
    <h3>Main Claims:</h3>
    <table class="table table-striped table-bordered table-hover">
        <tr><td>Name</td><td>@ViewBag.Name</td></tr>
        <tr><td>Username</td><td>@ViewBag.Username</td></tr>
        <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
        <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
    </table>
    <br />
    <h3>All Claims:</h3>
    <table class="table table-striped table-bordered table-hover table-condensed">
    @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
    {
        <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
    }
</table>
    <br />
    <br />
    @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
</body>
</html>
```
