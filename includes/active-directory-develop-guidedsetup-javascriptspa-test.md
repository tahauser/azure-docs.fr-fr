## <a name="test-your-code"></a>Test de votre code

### <a name="test-with-visual-studio"></a>Effectuer des tests avec Visual Studio
Si vous utilisez Visual Studio, appuyez sur **F5** pour exécuter votre projet. Le navigateur s’ouvre sur l’emplacement http://<span></span>localhost:{port} et vous voyez le bouton **Call Microsoft Graph API** (Appeler l’API.Microsoft Graph).

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Effectuer des tests avec Python ou un autre serveur web
Si vous n’utilisez pas Visual Studio, vérifiez que votre serveur web est démarré. Configurez le serveur pour écouter un port TCP basé sur l’emplacement de votre fichier **index.html**. Pour Python, écoutez le port en exécutant l’invite de commandes à partir du dossier de l’application :
 
```bash
python -m http.server 8080
```
Ouvrez le navigateur et tapez http://<span></span>localhost:8080 ou http://<span></span>localhost:{port}, où **port** correspond au port qu’écoute votre serveur web. Vous devez voir le contenu de votre fichier index.html et le bouton **Call Microsoft Graph API** (Appeler l’API Microsoft Graph).

## <a name="test-your-application"></a>Tester votre application

Une fois votre fichier index.html chargé dans le navigateur, sélectionnez **Call Microsoft Graph API**. La première fois que vous exécutez votre application, le navigateur vous redirige vers le point de terminaison Microsoft Azure Active Directory (Azure AD) v2.0, où vous êtes invité à vous connecter :
 
![Connexion au compte JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application

La première fois que vous vous connectez à votre application, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter :

![Donner votre accord pour l’accès par l’application](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Afficher les résultats de l’application
Une fois connecté, vous devez voir vos informations de profil utilisateur dans la zone **Graph API Call Response** (Réponse à l’appel de l’API Graph).
 
![Résultats attendus de l’appel à l’API Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Vous devez également voir des informations de base sur le jeton acquis sous les sections **Access Token** (Jeton d’accès) et **ID Token Claims** (Revendications de jetons d’ID).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue **user.read** pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. L’API Microsoft Graph nécessite l’étendue **Calendars.Read** pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée **Calendars.Read** aux informations d’inscription de l’application. Ajoutez ensuite l’étendue **Calendars.Read** à l’appel **acquireTokenSilent**. 

>[!NOTE]
>L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

Si une API backend ne nécessite pas d’étendue (non recommandé), vous pouvez utiliser le **clientId** en tant qu’étendue dans les appels **acquireTokenSilent** et **acquireTokenRedirect**.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
