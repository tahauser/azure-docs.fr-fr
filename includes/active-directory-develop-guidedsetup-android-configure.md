
## <a name="register-your-application"></a>Inscrivez votre application
Vous pouvez inscrire votre application de deux manières, comme décrit dans les deux sections suivantes.

### <a name="option-1-express-mode"></a>Option 1 : mode Express
Vous pouvez inscrire rapidement votre application en procédant comme suit :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  Dans la zone **Nom de l’application**, attribuez un nom à votre application.

3. Vérifiez que la case **Guided Setup** (Installation guidée) est cochée et sélectionnez **Créer**.

4. Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="option-2-advanced-mode"></a>Option 2 : mode Avancé
Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :
1. Si vous n’avez pas encore inscrit votre application, accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Dans la zone **Nom de l’application**, attribuez un nom à votre application. 

3. Vérifiez que la case **Guided Setup** (Installation guidée) est décochée et sélectionnez **Créer**.

4. Sélectionnez **Ajouter une plateforme**, puis **Application native**, et cliquez sur **Enregistrer**.

5. Sous **app** > **java** > **{host}.{namespace}**, ouvrez `MainActivity`. 

6.  Remplacez *[Enter the application Id here]* (Entrer l’ID de l’application ici) dans la ligne suivante par l’ID de l’application que vous venez d’inscrire :

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. Sous **app** > **manifests**, ouvrez le fichier *AndroidManifest.xml*.

8. Dans le nœud `manifest\application`, ajoutez l’activité suivante. Cette action enregistre une activité `BrowserTabActivity` permettant au système d’exploitation de reprendre votre application une fois l’authentification terminée :

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. Dans le nœud `BrowserTabActivity`, remplacez `[Enter the application Id here]` par l’ID de l’application.
