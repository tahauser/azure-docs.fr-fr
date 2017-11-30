
L’exemple précédent présentait une connexion standard, qui nécessite que le client contacte le fournisseur d’identité et le service principal Azure à chaque démarrage de l’application. Cette méthode est inefficace et risque de vous poser des problèmes d’utilisation si de nombreux clients tentent de démarrer votre application simultanément. Une meilleure approche consiste à mettre en cache le jeton d’autorisation renvoyé par le service Azure et à tenter de l’utiliser avant de recourir à une connexion basée sur un fournisseur.

> [!NOTE]
> Vous pouvez mettre en cache le jeton émis par le service principal Azure, que vous utilisiez l’authentification gérée par le client ou gérée par le service. Ce didacticiel utilise cette dernière.
>
>

1. Ouvrez le fichier ToDoActivity.java, puis ajoutez les instructions import suivantes :

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Ajoutez les membres suivants à la classe `ToDoActivity` :

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. Dans le fichier ToDoActivity.java, ajoutez la définition ci-après pour la méthode `cacheUserToken`.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Cette méthode stocke l’identificateur d’utilisateur et le jeton dans un fichier de préférences marqué comme privé. Cette opération doit protéger l’accès au cache afin que les autres applications de l’appareil n’aient pas accès au jeton. La préférence est exécutée dans le bac à sable (sandbox) de l’application. Toutefois, si une personne tente d'accéder à l'appareil, il est possible qu'elle puisse accéder au cache du jeton par d'autres moyens.

   > [!NOTE]
   > Vous pouvez protéger davantage le jeton avec un chiffrement si l’accès du jeton à vos données est considéré comme très sensible et que quelqu’un peut accéder à l’appareil. Une solution complètement sécurisée sort du cadre de ce didacticiel et dépend de vos besoins en matière de sécurité.
   >
   >

4. Dans le fichier ToDoActivity.java, ajoutez la définition ci-après pour la méthode `loadUserTokenCache`.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. Dans le fichier *ToDoActivity.java*, remplacez les méthodes `authenticate` et `onActivityResult` par les méthodes suivantes qui utilisent un cache de jetons. Modifiez le fournisseur de connexion si vous voulez utiliser un compte autre que Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, login and create a token cache
        else
        {
            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Générez l'application et testez l'authentification à l'aide d'un compte valide. Exécutez ceci au moins deux fois. À la première exécution, vous devez recevoir une invite vous indiquant de vous connecter et de créer le cache de jetons. Après cela, chaque exécution tente de charger le cache de jetons pour l’authentification. Vous ne devriez plus être invité à vous connecter.
