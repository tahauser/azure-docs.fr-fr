
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utiliser la bibliothèque d’authentification Microsoft (MSAL) afin d’obtenir un jeton pour l’API Microsoft Graph

Cette section montre comment utiliser MSAL afin d’obtenir un jeton pour l’API Graph de Microsoft.

1.  Dans `MainWindow.xaml.cs`, ajoutez la référence de la bibliothèque de MSAL à la classe :

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Remplacez le code de classe <code>MainWindow</code> par :
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires
#### <a name="getting-a-user-token-interactive"></a>Obtention d’un jeton d’utilisateur de manière interactive
L’appel de la méthode `AcquireTokenAsync` affiche une fenêtre invitant l’utilisateur à se connecter. En général, les applications obligent un utilisateur à se connecter de manière interactive la première fois qu’elles doivent accéder à une ressource protégée ou lorsqu’une opération silencieuse d’acquisition de jeton échoue (par exemple, en cas d’expiration du mot de passe de l’utilisateur).

#### <a name="getting-a-user-token-silently"></a>Obtention d’un jeton d’utilisateur en mode silencieux
`AcquireTokenSilentAsync` gère les acquisitions et renouvellements de jeton sans aucune interaction utilisateur. Lorsque `AcquireTokenAsync` est exécuté pour la première fois, c’est en général la méthode `AcquireTokenSilentAsync` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, ainsi que pour les demandes ou renouvellements de jetons en mode silencieux.
Au final, `AcquireTokenSilentAsync` échoue : par exemple, l’utilisateur s’est déconnecté ou a modifié son mot de passe sur un autre appareil. Lorsque MSAL détecte que le problème peut être résolu par une intervention interactive, elle déclenche une exception `MsalUiRequiredException`. Votre application peut gérer cette exception de deux manières :

1.  En adressant immédiatement un appel à `AcquireTokenAsync`, suite à quoi l’utilisateur est invité à se connecter. Cette méthode est généralement employée dans les applications en ligne où aucun contenu hors connexion dans l’application n’est disponible pour l’utilisateur. L’exemple généré par cette installation utilise cette solution : vous pouvez le voir en action lors de la première exécution de l’exemple : comme aucun utilisateur n’a jamais utilisé l’application, `PublicClientApp.Users.FirstOrDefault()` contient une valeur null et une exception `MsalUiRequiredException` est émise. Le code de l’exemple gère ensuite cette exception en appelant `AcquireTokenAsync`, suite à quoi l’utilisateur est invité à se connecter.

2.  Les applications peuvent également indiquer à l’utilisateur qu’une connexion en mode interactif est nécessaire, afin qu’il puisse choisir le bon moment pour se connecter ou qu’elle puisse réexécuter `AcquireTokenSilentAsync` ultérieurement. Cette solution est généralement retenue lorsque l’utilisateur peut utiliser d’autres fonctionnalités de l’application sans être perturbé, notamment lorsque du contenu hors connexion est disponible dans l’application. Dans ce cas, l’utilisateur peut décider quand il souhaite se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes. Ou bien, votre application peut décider de réexécuter `AcquireTokenSilentAsync` après une indisponibilité temporaire du réseau.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Appeler l’API Graph de Microsoft à l’aide du jeton que vous venez d’obtenir

1. Ajoutez la nouvelle méthode ci-dessous à votre `MainWindow.xaml.cs`. Cette méthode permet d’envoyer une demande `GET` à l’API Graph à l’aide d’un en-tête d’autorisation :

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Plus d’informations sur l’envoi d’un appel REST à une API protégée

Dans cet exemple d’application, la méthode `GetHttpContentWithToken` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui requiert un jeton, puis pour renvoyer le contenu à l’appelant. Cette méthode ajoute le jeton acquis dans l’*en-tête d’autorisation HTTP*. Dans cet exemple, la ressource est le point de terminaison *me* de l’API Graph de Microsoft, qui affiche les informations du profil de l’utilisateur.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

1. Ajoutez la méthode suivante à votre `MainWindow.xaml.cs` pour déconnecter l’utilisateur :

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Plus d’informations sur la déconnexion

`SignOutButton_Click` supprime l’utilisateur du cache utilisateur de MSAL. Cette opération permet à MSAL d’oublier l’utilisateur actuel, afin que la demande suivante d’acquisition d’un jeton n’aboutisse que si elle est effectuée en mode interactif.
Bien que l’application dans cet exemple ne prenne en charge qu’un seul utilisateur, MSAL autorise les scénarios où plusieurs comptes peuvent être connectés en même temps. C’est notamment le cas dans une application de messagerie, où un utilisateur possède plusieurs comptes.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Afficher les informations de base du jeton

1. Ajoutez la méthode suivante à votre `MainWindow.xaml.cs` pour afficher les informations de base du jeton :

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

Les jetons acquis via *OpenID Connect* contiennent également un petit sous-ensemble d’informations qui sont pertinentes pour l’utilisateur. `DisplayBasicTokenInfo` affiche les informations de base du jeton : par exemple, le nom affiché et l’ID de l’utilisateur, ainsi que la date d’expiration du jeton et la chaîne qui représente le jeton d’accès lui-même. Ces informations sont affichées. Vous pouvez cliquer sur le bouton *Call Microsoft Graph API (Appeler l’API Graph de Microsoft)* plusieurs fois et voir que le même jeton a été réutilisé pour les demandes suivantes. Vous constatez également que la date d’expiration est différée lorsque MSAL détermine qu’il est temps de renouveler le jeton.
<!--end-collapse-->

