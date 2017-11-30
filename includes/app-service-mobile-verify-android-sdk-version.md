En raison des développements en cours, la version du Kit de développement logiciel (SDK) Android installée dans Android Studio peut être différente de celle du code. Le Kit de développement logiciel (SDK) Android utilisé dans ce didacticiel correspond à la version 26, dernière version disponible au moment de la rédaction de ce document. Le numéro de version peut augmenter à mesure que de nouvelles versions du Kit de développement logiciel (SDK) apparaissent. Nous vous recommandons d’utiliser la dernière version disponible.

Deux symptômes permettent d'identifier des versions différentes :

- Lorsque vous générez ou régénérez le projet, vous pouvez obtenir des messages d’erreur Gradle tels que `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Les objets Android standard du code dont la résolution doit reposer sur les instructions `import` peuvent générer des messages d'erreur.

Dans ce cas, la version du Kit de développement logiciel (SDK) Android installée dans Android Studio peut différer de celle du Kit de développement logiciel (SDK) cible du projet téléchargé. Pour vérifier la version, apportez les modifications suivantes :

1. Dans Android Studio, cliquez sur **Outils** > **Android** > **Gestionnaire de Kit de développement (SDK)**. Si vous n'avez pas installé la dernière version de la plateforme de Kit de développement logiciel (SDK), cliquez pour l'installer. Prenez note du numéro de la version.

2. Dans l’onglet **Explorateur de projets**, sous **Scripts Gradle**, ouvrez le fichier **build.gradle (Module: app)**. Vérifiez que les versions **compileSdkVersion** et **targetSdkVersion** sont définies sur la dernière version installée du Kit de développement logiciel (SDK). `build.gradle` peut se présenter comme suit :

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
