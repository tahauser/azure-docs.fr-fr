
## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application

Dans cette étape, vous devez ajouter l’ID client à votre projet.

1.  Ouvrez `MainActivity` (sous `app` > `java` > *`{host}.{namespace}`*).
2.  Remplacez la ligne commençant par `final static String CLIENT_ID` par :
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Ouvrez `app` > `manifests` > `AndroidManifest.xml`.
4. Ajoutez l’activité suivante au nœud `manifest\application`. Cette opération inscrit une activité `BrowserTabActivity` pour autoriser le système d’exploitation à reprendre l’exécution de votre application une fois l’authentification effectuée :

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

