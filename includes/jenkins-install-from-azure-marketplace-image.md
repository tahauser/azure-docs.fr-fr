1. Dans votre navigateur, ouvrez [l’image de Place de marché Azure pour Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Sélectionnez **OBTENIR MAINTENANT**.

    ![Sélectionnez OBTENIR MAINTENANT pour démarrer le processus d’installation de l’image de Place de marché Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Après avoir consulté les détails de la tarification et les conditions, sélectionnez **Continuer**.

    ![Tarification et conditions de l’image de Place de marché Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Sélectionnez **Créer** pour configurer le serveur Jenkins dans le portail Azure. 

    ![Installez l’image de Place de marché Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Dans l’onglet **Basics (De base)**, spécifiez les valeurs suivantes :

    - **Name (Nom)** : entrez `Jenkins`.
    - **User (Utilisateur)** : entrez le nom d’utilisateur à utiliser pour la connexion à la machine virtuelle sur laquelle Jenkins est exécuté.
    - **Authentication type (Type d’authentification)** : sélectionnez **Password (Mot de passe)**.
    - **Password (Mot de passe)** : entrez le mot de passe à utiliser lors de la connexion à la machine virtuelle sur laquelle Jenkins est exécuté.
    - **Confirm password (Confirmer le mot de passe)** : entrez de nouveau le mot de passe à utiliser lors de la connexion à la machine virtuelle sur laquelle Jenkins est exécuté.
    - **Jenkins release type (Type de version Jenkins)** : sélectionnez **LTS**.
    - **Subscription (Abonnement)** : sélectionnez l’abonnement Azure dans lequel installer Jenkins.
    - **Resource group (Groupe de ressources)** : sélectionnez **Create new (Nouveau)** et entrez le nom du groupe de ressources servant de conteneur logique à la collection de ressources qui composent l’installation Jenkins.
    - **Location (Emplacement)** : sélectionnez **East US (États-Unis de l’Est)**.

    ![Entrez les informations d’authentification et de groupe de ressources de Jenkins dans l’onglet Basics (De base).](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Sélectionnez **OK** pour accéder à l’onglet **Settings (Paramètres)**. 

1. Dans l’onglet **Settings (Paramètres)**, spécifiez les valeurs suivantes :

    - **Size (Taille)** : sélectionnez l’option de dimensionnement appropriée pour votre machine virtuelle Jenkins.
    - **VM disk type (Type de disque de machine virtuelle)** : sélectionnez HDD (disque dur) ou SSD (disque SSD) pour indiquer le type de disque de stockage autorisé pour la machine virtuelle Jenkins.
    - **Public IP Address (Adresse IP publique)** : le nom de l’adresse IP est par défaut le nom Jenkins spécifié dans la page précédente suivi du suffixe -IP. Vous pouvez sélectionner l’option pour modifier cette valeur par défaut.
    - **Domain name label (Étiquette du nom de domaine)** : spécifiez la valeur de l’URL complète d’accès à la machine virtuelle Jenkins.

    ![Entrez les paramètres de la machine virtuelle pour Jenkins dans l’onglet Settings (Paramètres).](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Sélectionnez **OK** pour accéder à l’onglet **Summary (Résumé)**.

1. Lorsque l’onglet **Summary (Résumé)** s’affiche, les informations saisies sont validées. Lorsque le message **Validation passed (Validation réussie)** apparaît, cliquez sur **OK**. 

    ![L’onglet Summary (Résumé) affiche et valide les options sélectionnées.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Lorsque l’onglet **Create (Créer)** s’affiche, sélectionnez **Create (Créer)** pour créer la machine virtuelle Jenkins. Lorsque votre serveur est prêt, une notification s’affiche dans le portail Azure.

    ![Notification Jenkins est prêt.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)