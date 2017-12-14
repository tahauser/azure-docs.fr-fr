
## <a name="create-an-application-express"></a>Créer une application (Express)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.  Assurez-vous que la case de l’option Guided Setup (Installation guidée) est activée.
4.  Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Ajouter les informations d’inscription de l’application à votre solution (Avancé)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail. 
3. Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4. Cliquez sur `Add Platform`, puis sélectionnez `Native Application` et appuyez sur Save (Enregistrer).
5.  Ouvrez `MainActivity` (sous `app` > `java` > *`{host}.{namespace}`*).
6.  Remplacez *[Enter the application Id here]* (Entrer l’ID de l’application ici) dans la ligne commençant par `final static String CLIENT_ID` par l’ID de l’application que vous venez d’inscrire :

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Ouvrez `AndroidManifest.xml` (sous `app` > `manifests`) et ajoutez l’activité suivante au nœud `manifest\application`. Cette opération inscrit une activité `BrowserTabActivity` pour autoriser le système d’exploitation à reprendre l’exécution de votre application une fois l’authentification effectuée :
</li>
</ol>

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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
<ol start="8">
<li>
Dans `BrowserTabActivity`, remplacez `[Enter the application Id here]` par l’ID de l’application.
</li>
</ol>
