
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utiliser MSAL pour obtenir un jeton pour l’API Microsoft Graph

Dans cette section, vous allez utiliser MSAL pour obtenir un jeton pour l’API Microsoft Graph.

1.  Dans le fichier *MainWindow.xaml.cs*, ajoutez la référence de MSAL à la classe :

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Remplacez le code de classe `MainWindow` par le suivant :

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
### <a name="more-information"></a>Plus d’informations
#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive
L’appel de la méthode `AcquireTokenAsync` affiche une fenêtre invitant les utilisateurs à se connecter. Les applications requièrent généralement que les utilisateurs se connectent de manière interactive la première fois qu’ils cherchent à accéder à une ressource protégée. Ils peuvent également avoir besoin de se connecter en cas d’échec d’une opération en mode silencieux pour obtenir un jeton (par exemple, quand un mot de passe utilisateur a expiré).

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux
La méthode `AcquireTokenSilentAsync` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand `AcquireTokenAsync` est exécuté pour la première fois, la méthode `AcquireTokenSilentAsync` est généralement celle à utiliser pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, étant donné que les appels pour les demandes ou renouvellements de jetons se font en mode silencieux.

La méthode `AcquireTokenSilentAsync` peut échouer. Cet échec peut être dû à une déconnexion de l’utilisateur ou à la modification de son mot de passe sur un autre appareil. Quand la bibliothèque MSAL détecte que le problème peut être résolu par une intervention interactive, elle déclenche une exception `MsalUiRequiredException`. Votre application peut gérer cette exception de deux manières :

* Elle peut appeler immédiatement `AcquireTokenAsync`. Cet appel invite l’utilisateur à se connecter. Cette méthode est généralement employée dans les applications en ligne où aucun contenu hors connexion n’est disponible pour l’utilisateur. L’exemple généré par cette installation guidée utilise ce modèle, que vous pouvez voir en action la première fois que vous exécutez l’exemple. 
    * Aucun utilisateur n’ayant encore utilisé l’application, `PublicClientApp.Users.FirstOrDefault()` contient une valeur null, et une exception `MsalUiRequiredException` est levée. 
    * Le code de l’exemple gère ensuite cette exception en appelant `AcquireTokenAsync`, après quoi l’utilisateur est invité à se connecter.

* Il peut également afficher à la place une indication visuelle informant les utilisateurs qu’une connexion interactive est nécessaire, pour permettre à ces derniers de sélectionner le bon moment pour se connecter. L’application peut également effectuer une nouvelle tentative de `AcquireTokenSilentAsync` ultérieurement. Ce modèle est souvent utilisé quand les utilisateurs peuvent utiliser d’autres fonctionnalités de l’application sans interruption, par exemple, quand le contenu hors connexion est disponible dans l’application. Dans ce cas, les utilisateurs peuvent décider de se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes. L’application peut également décider d’effectuer une nouvelle tentative de `AcquireTokenSilentAsync` une fois le réseau rétabli après une indisponibilité temporaire.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

Ajoutez la nouvelle méthode suivante à votre fichier `MainWindow.xaml.cs`. Cette méthode permet d’envoyer une demande `GET` à l’API Graph à l’aide d’un en-tête d’autorisation :

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Informations supplémentaires sur l’envoi d’un appel REST à une API protégée

Dans cet exemple d’application, vous utiliserez la méthode `GetHttpContentWithToken` pour envoyer une requête HTTP `GET` à une ressource protégée qui requiert un jeton, puis pour retourner le contenu à l’appelant. Cette méthode ajoute le jeton acquis à l’en-tête d’autorisation HTTP. Dans cet exemple, la ressource est le point de terminaison *me* de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Ajouter une méthode pour déconnecter un utilisateur

Pour déconnecter un utilisateur, ajoutez la méthode suivante à votre fichier `MainWindow.xaml.cs` :

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
### <a name="more-information-about-user-sign-out"></a>Plus d'informations sur la déconnexion d’utilisateurs

La méthode `SignOutButton_Click` supprime l’utilisateur du cache utilisateur de MSAL en ordonnant à MSAL d’oublier l’utilisateur actuel pour que la demande suivante d’acquisition de jeton n’aboutisse que si elle est effectuée de manière interactive.

Bien que l’application de cet exemple ne prenne en charge qu’un seul utilisateur, MSAL autorise les scénarios où plusieurs comptes peuvent être connectés en même temps. C’est le cas, par exemple, d’une application de messagerie hébergeant plusieurs comptes d’un même utilisateur.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Afficher les informations de base du jeton

Pour afficher les informations de base du jeton, ajoutez la méthode suivante à votre fichier *MainWindow.xaml.cs* :

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
### <a name="more-information"></a>Plus d’informations

Outre le jeton d’accès qui est utilisé pour appeler l’API Microsoft Graph, MSAL obtient également un jeton d’ID une fois l’utilisateur connecté. Ce jeton contient un petit sous-ensemble d’informations pertinentes pour les utilisateurs. La méthode `DisplayBasicTokenInfo` affiche les informations de base du jeton. Par exemple, le nom affiché et l’ID de l’utilisateur, ainsi que la date d’expiration du jeton et la chaîne qui représente le jeton d’accès lui-même. Si vous cliquez plusieurs fois sur le bouton *Call Microsoft Graph API* (Appeler l’API Microsoft Graph), vous observerez que le même jeton a été réutilisé pour les demandes suivantes. Vous constatez également que la date d’expiration est différée lorsque MSAL détermine qu’il est temps de renouveler le jeton.
<!--end-collapse-->

