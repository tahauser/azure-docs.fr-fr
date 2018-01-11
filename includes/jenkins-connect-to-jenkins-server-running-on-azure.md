1. Dans votre navigateur, accédez à votre machine virtuelle Jenkins. Étant donné que le tableau de bord Jenkins n’est pas accessible par le biais d’un protocole HTTP non sécurisé, un message s’affiche indiquant que vous devez utiliser SSH pour accéder par tunnel à la machine virtuelle.

    ![Jenkins fournit des informations qui expliquent comment utiliser SSH pour vous connecter à la machine virtuelle Jenkins.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Ouvrez une fenêtre bash ou de terminal ayant accès à SSH.

1. Entrez la commande `ssh` suivante en remplaçant les espaces réservés **&lt;username>** et **&lt;domain>** par les valeurs spécifiées durant la création de la machine virtuelle Jenkins :

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Suivez les invites `ssh` pour vous connecter à la machine virtuelle Jenkins.

1. Entrez la commande suivante pour récupérer le mot de passe initial afin de déverrouiller Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Copiez le mot de passe affiché dans la fenêtre bash ou de terminal dans le Presse-papiers.

1. Dans votre navigateur, accédez à `http://localhost:8080`.

1. Collez le mot de passe récupéré précédemment dans le champ **Administrative password** (Mot de passe administratif), puis sélectionnez **Continue** (Continuer).

    ![Jenkins stocke un mot de passe initial que vous devez utiliser pour déverrouiller la machine virtuelle Jenkins la première fois que vous vous connectez à celle-ci.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Sélectionnez **Install suggested plugins** (Installer les plug-ins suggérés).

    ![Jenkins vous permet d’installer facilement une collection de plug-ins suggérés après l’entrée initiale](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Dans la page **Create First Admin User** (Créer le premier utilisateur administratif), créez l’utilisateur administratif et le mot de passe pour le tableau de bord Jenkins, puis sélectionnez **Save and finish** (Enregistrer et terminer).

1. Dans la page **Jenkins is ready!** (Jenkins est prêt à l’emploi), sélectionnez **Start using Jenkins** (Commencer à utiliser Jenkins).

    ![Une fois Jenkins installé et configuré pour l’accès, vous pouvez commencer à l’utiliser pour créer et générer des travaux.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)