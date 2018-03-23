## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application

Pour cette étape, vous devez configurer l’URL de redirection des informations d’inscription de votre application, puis ajouter l’ID d’application à votre application SPA JavaScript.

### <a name="configure-redirect-url"></a>Configurer l’URL de redirection

Configurez le champ `Redirect URL` avec l’URL de la page index.html de votre serveur web, puis cliquez sur *Mettre à jour*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Instructions Visual Studio pour obtenir l’URL de redirection
> Pour obtenir l’URL de redirection :
> 1.    Dans *l’Explorateur de solutions*, sélectionnez le projet et examinez la fenêtre `Properties` (si vous ne voyez pas une fenêtre de propriétés, appuyez sur `F4`).
> 2.    Copiez la valeur de `URL` dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Collez la valeur comme une `Redirect URL` en haut de la page, puis cliquez sur `Update`.

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Configuration d’une URL de redirection pour Python
> Pour Python, vous pouvez définir le port du serveur web via la ligne de commande. Cette configuration guidée utilise le port 8080 pour référence, vous pouvez utiliser tout autre port disponible. Dans tous les cas, suivez les instructions suivantes pour configurer une URL de redirection dans les informations d’inscription de l’application :<br/>
> Définissez `http://localhost:8080/` comme une `Redirect URL` en haut de cette page, ou utilisez `http://localhost:[port]/` si vous utilisez un port TCP personnalisé (où *[port]* correspond au numéro de port TCP personnalisé), puis cliquez sur « Update » (Mettre à jour).

### <a name="configure-your-javascript-spa-application"></a>Configurer votre application SPA JavaScript

1.  Créez un fichier nommé `msalconfig.js` contenant les informations d’inscription de l’application. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, puis sélectionnez : `Add` > `New Item` > `JavaScript File`. Nommez-le `msalconfig.js`.
2.  Ajoutez le code suivant à votre fichier `msalconfig.js` :

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
