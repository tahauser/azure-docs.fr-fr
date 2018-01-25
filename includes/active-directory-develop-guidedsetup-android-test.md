## <a name="test-your-code"></a>Test de votre code

1. Déployez votre code sur votre appareil/émulateur.

2. Lorsque vous êtes prêt pour le test, votre application utilise un compte Microsoft Azure Active Directory (compte professionnel ou scolaire) ou un compte Microsoft (live.com, outlook.com) pour vous connecter. 

    ![Tester votre application](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Nom d'utilisateur et mot de passe](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application
La première fois que vous vous connectez à votre application, vous êtes également invité à autoriser l’application à accéder à votre profil et vous connecter, comme illustré ici : 

![Donner votre accord pour l’accès par l’application](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Afficher les résultats de l’application
Une fois connecté, vous devriez voir les résultats renvoyés par l’appel de l’API Microsoft Graph. L’appel du point de terminaison **me** de l’API Microsoft Graph renvoie le [profil utilisateur](https://graph.microsoft.com/v1.0/me). Pour obtenir la liste des points de terminaison Microsoft Graph les plus courants, consultez la [documentation pour développeurs de l’API Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription de l’application. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. L’API Microsoft Graph nécessite l’étendue *Calendars.Read* pour répertorier les calendriers de l’utilisateur. 

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée *Calendars.Read* aux informations d’inscription de l’application. Ajoutez ensuite l’étendue *Calendars.Read* à l’appel `acquireTokenSilent`. 

>[!NOTE]
>L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
