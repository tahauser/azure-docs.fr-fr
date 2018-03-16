
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurer votre application Web ASP.NET avec les informations d’inscription de l’application

Dans cette étape, vous allez configurer votre projet pour utiliser SSL, puis vous servir de l’URL SSL pour configurer les informations d’inscription de votre application. Ensuite, ajoutez les informations d’inscription de l’application à votre solution via *web.config*.

1.  Dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre `Properties` (si vous ne voyez pas une fenêtre de propriétés, appuyez sur F4).
2.  Remplacez `SSL Enabled` par `True` :
3.  Copiez la valeur de `SSL URL` ci-dessus et collez-la dans le champ `Redirect URL` en haut de cette page, puis cliquez sur *Mettre à jour* :<br/><br/>![Propriétés du projet](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
