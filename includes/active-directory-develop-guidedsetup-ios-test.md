## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Tester l’interrogation de l’API Microsoft Graph à partir de votre application iOS

Pour exécuter le code dans le simulateur, appuyez sur les touches **Cmd** + **R**.

![Tester votre application dans le simulateur](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Lorsque vous êtes prêt à passer aux tests, sélectionnez **Call Microsoft Graph API** (Appeler l’API Microsoft Graph). Lorsque vous y êtes invité, entrez votre nom d’utilisateur et votre mot de passe.

### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application
La première fois que vous vous connectez à votre application, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter :

![Donner votre accord pour l’accès par l’application](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Afficher les résultats de l’application
Une fois connecté, vous devez voir vos informations de profil utilisateur retournées par l’appel de l’API Microsoft Graph dans la section **Journalisation**. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue **user.read** pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. L’API Microsoft Graph nécessite l’étendue **Calendars.Read** pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée **Calendars.Read** aux informations d’inscription de l’application. Ajoutez ensuite l’étendue **Calendars.Read** à l’appel **acquireTokenSilent**. 

>[!NOTE]
>L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
