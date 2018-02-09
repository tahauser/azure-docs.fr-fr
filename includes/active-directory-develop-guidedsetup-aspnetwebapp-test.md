## <a name="test-your-code"></a>Test de votre code

Pour tester votre application dans Visual Studio, appuyez sur **F5** afin d’exécuter votre projet. Le navigateur s’ouvre sur l’emplacement http://<span></span>localhost:{port} et vous voyez le bouton **Se connecter avec Microsoft**. Sélectionnez le bouton pour démarrer le processus de connexion.

Lorsque vous êtes prêt pour le test, utilisez un compte Microsoft Azure Active Directory (compte professionnel ou scolaire) ou un compte Microsoft personnel (<span>live.</span>com ou <span>outlook.</span>com) pour vous connecter.

![Se connecter avec Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Connexion à votre compte Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Afficher les résultats de l’application
Une fois connecté, l’utilisateur est redirigé vers la page d’accueil de votre site web. La page d’accueil correspond à l’URL HTTPS qui est spécifiée dans les informations d’inscription de votre application, dans le portail d’inscription des applications de Microsoft. La page d’accueil comprend le message de bienvenue « Bonjour \<Utilisateur>, » un lien pour se déconnecter et un lien pour afficher les revendications de l’utilisateur. Le lien des revendications de l’utilisateur redirige vers le contrôleur **Authorize** que vous avez créé précédemment.

### <a name="browse-to-see-the-users-claims"></a>Accéder aux revendications de l’utilisateur
Pour afficher les revendications de l’utilisateur, sélectionnez le lien pour accéder à la vue de contrôleur qui est disponible uniquement pour les utilisateurs authentifiés.

#### <a name="view-the-claims-results"></a>Afficher les résultats des revendications
Une fois que vous avez accédé à la vue de contrôleur, vous devez voir un tableau qui contient les propriétés de base de l’utilisateur :

|Propriété |Valeur |Description |
|---|---|---|
|**Name** |Nom complet de l’utilisateur | Prénom et nom de l’utilisateur
|**Nom d’utilisateur** |utilisateur<span>@domain.com</span> | Nom d’utilisateur employé pour identifier l’utilisateur.
|**Objet** |Objet |Chaîne qui identifie de manière unique l’utilisateur sur le web.|
|**Tenant ID** |Guid | **GUID** qui représente de manière unique l’organisation Azure AD de l’utilisateur.|

En outre, vous devez voir un tableau avec toutes les revendications qui se trouvent dans la requête d’authentification. Pour plus d’informations, consultez la [liste des revendications qui se trouvent dans un jeton d’ID AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Tester l’accès à une méthode disposant d’un attribut Authorize (facultatif)
Si vous voulez tester l’accès au contrôleur **Authorize** pour les revendications de l’utilisateur en tant qu’utilisateur anonyme, procédez aux étapes suivantes :
1. Sélectionnez le lien de déconnexion de l’utilisateur et terminez le processus de déconnexion.
2. Dans votre navigateur, tapez http://<span></span>localhost:{port}/authenticated pour accéder à votre contrôleur qui est protégé par l’attribut **Authorize**.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Résultats attendus après l’accès à un contrôleur protégé
Vous êtes invité à vous authentifier pour utiliser la vue de contrôleur protégé.

## <a name="additional-information"></a>Informations supplémentaires

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Protéger l’ensemble de votre site web
Pour protéger l’ensemble de votre site web, dans le fichier **Global.asax**, ajoutez l’attribut **AuthorizeAttribute** au filtre **GlobalFilters**, dans la méthode **Application_Start** :

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Restreindre les utilisateurs qui peuvent se connecter à votre application
Par défaut, les comptes personnels tels que les comptes outlook.com, live.com et autres, peuvent se connecter à votre application. Les comptes professionnels et scolaires qui sont intégrés à Azure AD peuvent également se connecter par défaut.

Pour restreindre les utilisateurs qui peuvent se connecter à votre application, plusieurs options sont disponibles.

#### <a name="restrict-access-to-a-single-organization"></a>Restreindre les connexions à une seule organisation
Vous pouvez limiter les connexions à votre application aux comptes d’utilisateurs d’une organisation Azure AD :
1. Dans le fichier **web.config**, modifiez la valeur du paramètre **Tenant**. Remplacez la valeur **Common** par le nom de locataire de l’organisation, tel que **contoso.onmicrosoft.com**.
2. Dans la classe **OWIN Startup**, définissez l’argument **ValidateIssuer** sur **true**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>Restreindre les connexions à une liste d’organisations
Vous pouvez limiter les connexions aux comptes d’utilisateurs d’une organisation Azure AD figurant dans la liste des organisations autorisées :
1. Dans le fichier **web.config**, dans la classe **OWIN Startup**, définissez l’argument **ValidateIssuer** sur **true**.
2. Définissez la valeur du paramètre **ValidIssuers** sur la liste des organisations autorisées.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Utiliser une méthode personnalisée pour valider les émetteurs
Vous pouvez implémenter une méthode personnalisée pour valider les émetteurs à l’aide du paramètre **IssuerValidator**. Pour plus d’informations sur l’utilisation de ce paramètre, lisez cette rubrique MSDN concernant la [classe TokenValidationParameters](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx).

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
