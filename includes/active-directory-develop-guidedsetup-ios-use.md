## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utiliser la bibliothèque d’authentification Microsoft (MSAL) afin d’obtenir un jeton pour l’API Microsoft Graph

Ouvrez `ViewController.swift` et remplacez le code par :

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires
#### <a name="getting-a-user-token-interactively"></a>Obtention d’un jeton d’utilisateur de manière interactive
L’appel de la méthode `acquireToken` affiche une fenêtre de navigateur invitant l’utilisateur à se connecter. En général, les applications obligent un utilisateur à se connecter de manière interactive la première fois qu’elles doivent accéder à une ressource protégée ou lorsqu’une opération silencieuse d’acquisition de jeton échoue (par exemple, en cas d’expiration du mot de passe de l’utilisateur).

#### <a name="getting-a-user-token-silently"></a>Obtention d’un jeton d’utilisateur en mode silencieux
La méthode `acquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand `acquireToken` est exécuté pour la première fois, c’est en général la méthode `acquireTokenSilent` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, ainsi que pour les demandes ou renouvellements de jetons en mode silencieux.

`acquireTokenSilent` finira par échouer, par exemple si l’utilisateur s’est déconnecté ou a modifié son mot de passe sur un autre appareil. Quand la bibliothèque MSAL détecte que le problème peut être résolu par une intervention interactive, elle déclenche une exception `MSALErrorCode.interactionRequired`. Votre application peut gérer cette exception de deux manières :

1.  En adressant immédiatement un appel à `acquireToken`, suite à quoi l’utilisateur est invité à se connecter. Cette méthode est généralement employée dans les applications en ligne où aucun contenu hors connexion dans l’application n’est disponible pour l’utilisateur. L’exemple d’application généré par cet Assistant Installation utilise ce modèle : vous pouvez le voir en action la première fois que vous exécutez l’application. Aucun utilisateur n’ayant jamais utilisé l’application, `applicationContext.users().first` contient une valeur null et une exception ` MSALErrorCode.interactionRequired ` est levée. Le code de l’exemple gère ensuite cette exception en appelant `acquireToken`, suite à quoi l’utilisateur est invité à se connecter.

2.  Les applications peuvent également signaler à l’utilisateur qu’une connexion interactive est requise afin qu’il puisse choisir le bon moment pour se connecter ou que l’application puisse réessayer d’exécuter `acquireTokenSilent` ultérieurement. Cette solution est généralement retenue lorsque l’utilisateur peut utiliser d’autres fonctionnalités de l’application sans être perturbé, notamment lorsque du contenu hors connexion est disponible dans l’application. Dans ce cas, l’utilisateur peut décider quand il souhaite se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes. Ou bien, votre application peut décider de réexécuter `acquireTokenSilent` après une indisponibilité temporaire du réseau.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

Ajoutez la nouvelle méthode ci-dessous à `ViewController.swift`. Cette méthode permet d’envoyer une demande `GET` à l’API Microsoft Graph à l’aide d’un *en-tête d’autorisation HTTP* :

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Envoi d’un appel REST à une API protégée

Dans cet exemple d’application, la méthode `getContentWithToken()` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui requiert un jeton, puis pour renvoyer le contenu à l’appelant. Cette méthode ajoute le jeton acquis dans l’*en-tête d’autorisation HTTP*. Dans cet exemple, la ressource est le point de terminaison *me* de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Configurer la déconnexion

Ajoutez la méthode suivante à `ViewController.swift` pour déconnecter l’utilisateur :

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Plus d’informations sur la déconnexion

La méthode `signoutButton` ci-dessus supprime l’utilisateur du cache utilisateur de la bibliothèque MSAL. Cette opération indique à la bibliothèque MSAL qu’elle doit oublier l’utilisateur actuel afin que la demande suivante d’acquisition de jeton n’aboutisse que si elle est effectuée de manière interactive.

Bien que l’application de cet exemple ne prenne en charge qu’un seul utilisateur, la bibliothèque MSAL autorise les scénarios où plusieurs comptes peuvent être connectés en même temps. C’est notamment le cas dans une application de messagerie où un utilisateur a plusieurs comptes.
<!--end-collapse-->

## <a name="register-the-callback"></a>Inscrire le rappel

Une fois que l’utilisateur s’est authentifié, le navigateur le redirige vers l’application. Suivez les étapes ci-dessous pour inscrire ce rappel :

1.  Ouvrez `AppDelegate.swift` et importez la bibliothèque MSAL :

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Ajoutez la méthode suivante à votre classe <code>AppDelegate</code> pour gérer les rappels :
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```

