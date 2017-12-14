
## <a name="create-an-application-express"></a>Créer une application (Express)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).
2.  Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.  Assurez-vous que la case de l’option Guided Setup (Installation guidée) est activée.
4.  Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Ajouter les informations d’inscription de l’application à votre solution (Avancé)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail. 
3. Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4. Cliquez sur `Add Platform`, puis sélectionnez `Native Application` et appuyez sur Save (Enregistrer).
5. Copiez le GUID dans Application ID (ID de l’application), revenez à Visual Studio, ouvrez `App.xaml.cs` et remplacez `your_client_id_here` par l’ID de l’application que vous venez d’enregistrer :

```csharp
private static string ClientId = "your_application_id_here";
```
