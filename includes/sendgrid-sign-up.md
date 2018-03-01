Les clients Azure peuvent débloquer 25 000 courriers électroniques gratuits chaque mois. Ces 25 000 courriers électroniques mensuels gratuits vous donnent accès aux rapports avancés, aux analyses et à [toutes les API][all APIs] (Web, SMTP, Event, Parse, etc.). Pour plus d’informations sur les services supplémentaires proposés par SendGrid, visitez la page [Solutions SendGrid][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Création d’un compte SendGrid
1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le menu de gauche, cliquez sur **Créer une ressource**.

    ![command-bar-new][command-bar-new]
3. Cliquez sur **Modules complémentaires**, puis sur **Service de messagerie SendGrid**.

    ![store-sendgrid][sendgrid-store]
4. Remplissez le formulaire d’inscription, puis sélectionnez **Créer**.

    ![sendgrid-create][sendgrid-create]
5. Entrez un **nom** pour identifier votre service SendGrid dans vos paramètres Azure. Les noms doivent contenir entre 1 et 100 caractères et ne peuvent inclure que des lettres, des chiffres, des tirets, des points et des traits de soulignement. Le nom doit être unique dans la liste des éléments de l’Azure Store auxquels vous êtes abonné.
6. Entrez et confirmez votre **mot de passe**.
7. Choisissez votre **abonnement**.
8. Créez un **groupe de ressources** ou utilisez un groupe existant.
9. Dans la boîte de dialogue **Niveau tarifaire**, sélectionnez le plan SendGrid de votre choix pour l’inscription.

    ![sendgrid-pricing][sendgrid-pricing]
10. Saisissez un **code de promotion**, le cas échéant.
11. Entrez vos **informations de contact**.
12. Lisez et acceptez les **conditions juridiques**.
13. Après avoir confirmé votre achat, une fenêtre contextuelle **Déploiement a réussi** apparaît et votre compte s’affiche dans la section **Toutes les ressources**.

    ![toutes-les-ressources][all-resources]

    Après avoir effectué votre achat et cliqué sur le bouton **Gérer** pour lancer le processus de vérification par courrier électronique, vous recevrez un e-mail de la part de SendGrid vous demandant de vérifier votre compte. Si vous ne recevez pas cet e-mail ou si vous avez des difficultés pour vérifier votre compte, veuillez consulter ce forum aux questions.

    ![gestion][manage]

    **Vous ne pouvez envoyer que 100 e-mails/jour jusqu'à ce que vous ayez vérifié votre compte.**

    Pour modifier votre plan d’abonnement ou voir les paramètres de contact de SendGrid, cliquez sur le nom du service SendGrid pour ouvrir le tableau de bord Marketplace SendGrid.

    ![paramètres][settings]

    Pour envoyer un e-mail à l’aide de SendGrid, vous devez fournir votre clé API.

### <a name="to-find-your-sendgrid-api-key"></a>Pour trouver votre clé API SendGrid
1. Cliquez sur **Gérer**.

    ![gestion][manage]
2. Dans votre tableau de bord SendGrid, sélectionnez **Paramètres**, puis **Clés API** dans le menu de gauche.

    ![api-keys][api-keys]

3. Cliquez sur **Créer une clé API**.

    ![general-api-key][general-api-key]
4. Au minimum, fournissez le **nom de cette clé** et un accès complet à **Mail Send** (Envoi de courrier) puis sélectionnez **Enregistrer**.

    ![access][access]
5. À ce stade, votre API s’affiche. Conservez-la en sécurité.

### <a name="to-find-your-sendgrid-credentials"></a>Recherche de vos informations d’identification SendGrid
1. Cliquez sur l’icône en forme de clé pour trouver votre **nom d’utilisateur**.

    ![key][key]
2. Le mot de passe est celui que vous avez choisi lors de l’installation. Vous pouvez sélectionner **Modifier le mot de passe** ou **Réinitialiser le mot de passe** pour apporter des modifications.

Pour gérer les paramètres de remise des courriers électroniques, cliquez sur le bouton **Manage** . Cette action vous redirige vers le tableau de bord SendGrid.

    ![manage][manage]

    For more information on sending email through SendGrid, visit the [Email API Overview][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
