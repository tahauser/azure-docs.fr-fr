1. Ouvrez le Gestionnaire du Kit de développement logiciel Android en cliquant sur l’icône de la barre d’outils d’Android Studio ou sur **Outils** -> **Android** -> **Gestionnaire du Kit de développement logiciel (SDK)** à partir du menu. Recherchez la version cible du Kit de développement logiciel (SDK) Android utilisé pour votre projet, ouvrez-la en cliquant sur **Afficher la page Détails du package** et choisissez **API Google** si elles ne sont pas déjà installées.
2. Cliquez sur l’onglet **Outils SDK** . Si vous n’avez pas encore installé Google Play Service, cliquez sur **Services Google Play** comme indiqué ci-dessous. Cliquez ensuite sur **Appliquer** pour procéder à l’installation. 
   
    Notez le chemin du Kit de développement logiciel (SDK). Vous l’utiliserez à une étape suivante. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Ouvrez le fichier **build.gradle** dans le répertoire de l’application.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Ajoutez la ligne suivante sous *dépendances*: 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:9.2.0'
    ```
5. Cliquez sur l’icône **Synchronisation du projet avec les fichiers Gradle** dans la barre d’outils.
6. Ouvrez **AndroidManifest.xml** et ajoutez cette balise à la balise *application* .
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

