
## <a name="register-your-application"></a>Inscrivez votre application

Il existe plusieurs manières de créer une application ; veuillez en sélectionnez une :

### <a name="option-1-register-your-application-express-mode"></a>Option 1 : Inscrire votre application (mode Express)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :

1.  Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.  Vérifiez que l’option *Guided Setup* (Installation guidée) est bien cochée.
4.  Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="option-2-register-your-application-advanced-mode"></a>Option 2 : Inscrire votre application (mode Avancé)

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail. 
3. Vérifiez que l’option *Guided Setup* (Installation guidée) n’est pas cochée.
4.  Cliquez sur `Add Platform`, puis sélectionnez `Web`.
5. Ajouter la `Redirect URL` qui correspond à l’URL des applications en fonction de votre serveur web. Consultez les sections ci-dessous pour obtenir des instructions permettant de définir/obtenir l’URL de redirection dans Visual Studio et Python.
6. Cliquez sur *Enregistrer*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Instructions Visual Studio pour obtenir l’URL de redirection
> Suivez les instructions pour obtenir l’URL de redirection :
> 1.    Dans *l’Explorateur de solutions*, sélectionnez le projet et examinez la fenêtre `Properties` (si vous ne voyez pas une fenêtre de propriétés, appuyez sur `F4`).
> 2.    Copiez la valeur de `URL` dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Revenez au *Portail d’inscription des applications*, collez la valeur dans `Redirect URL` et cliquez sur « Enregistrer ».

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Configuration d’une URL de redirection pour Python
> Pour Python, vous pouvez définir le port du serveur web via la ligne de commande. Cette configuration guidée utilise le port 8080 pour référence, vous pouvez utiliser tout autre port disponible. Dans tous les cas, suivez les instructions ci-dessous pour configurer une URL de redirection dans les informations d’inscription de l’application :<br/>
> - Revenez au *Portail d’inscription des applications* et entrez `http://localhost:8080/` dans `Redirect URL`, ou optez pour `http://localhost:[port]/` si vous utilisez un port TCP personnalisé (*[port]* étant le numéro de port TCP) et cliquez sur « Enregistrer ».


#### <a name="configure-your-javascript-spa"></a>Configurer une application SPA JavaScript

1.  Créez un fichier nommé `msalconfig.js` contenant les informations d’inscription de l’application. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, puis sélectionnez : `Add` > `New Item` > `JavaScript File`. Nommez-le `msalconfig.js`.
2.  Ajoutez le code suivant à votre fichier `msalconfig.js` :

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Remplacez <code>Enter_the_Application_Id_here</code> par l’ID d’application que vous venez d’enregistrer
</li>
</ol>
