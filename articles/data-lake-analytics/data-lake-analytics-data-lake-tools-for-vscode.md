---
title: "Azure Data Lake Tools : Utiliser Azure Data Lake Tools pour Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour créer, tester et exécuter des scripts U-SQL. "
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: jejiang
ms.openlocfilehash: 7e1e2c0a5481a81e9267bcf87076076b377a1496
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utiliser Azure Data Lake Tools pour Visual Studio Code

Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code (VS Code) pour créer, tester et exécuter des scripts U-SQL. Ces informations sont également décrites dans la vidéo suivante :

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Prérequis

Azure Data Lake Tools pour VSCode prend en charge Windows, Linux et MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Pour MacOS et Linux :
- [SDK .NET Core 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Installer Data Lake Tools

Après avoir installé les prérequis, vous pouvez installer Data Lake Tools pour VS Code.

**Pour installer Data Lake Tools**

1. Ouvrez Visual Studio Code.
2. Cliquez sur **Extensions** dans le volet gauche. Entrez **Azure Data Lake** dans la zone de recherche.
3. Cliquez sur **Installer** en regard de **Azure Data Lake Tools**. Après quelques secondes, le bouton **Installer** change en **Recharger**.
4. Cliquez sur **Recharger** pour activer l’extension **Azure Data Lake Tools**.
5. Cliquez sur **Recharger la fenêtre** pour confirmer. **Azure Data Lake Tools** s’affiche dans le volet Extensions.

    ![Volet Extensions de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Activer Azure Data Lake Tools
Créez un fichier .usql ou ouvrez un fichier .usql existant pour activer l’extension. 

## <a name="open-the-sample-script"></a>Ouvrir l’exemple de script
Ouvrez la palette de commandes (Ctrl+Maj+P) et entrez **ADL: Open Sample Script**. Une autre instance de cet exemple s’ouvre. Vous pouvez également modifier, configurer et envoyer un script sur cette instance.

## <a name="work-with-u-sql"></a>Utilisation de U-SQL

Vous devez ouvrir un dossier ou un fichier U-SQL pour utiliser U-SQL.

**Pour ouvrir un dossier pour votre projet U-SQL**

1. À partir de Visual Studio Code, sélectionnez le menu **Fichier**, puis **Ouvrir un dossier**.
2. Spécifiez un dossier, puis sélectionnez **Sélectionner le dossier**.
3. Sélectionnez le menu **Fichier**, puis **Nouveau**. Un fichier Untitled-1 est ajouté au projet.
4. Entrez le code suivant dans le fichier Untitled-1 :

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Le script crée un fichier departments.csv avec des données dans le dossier /output.

5. Enregistrez le fichier sous **myUSQL.usql** dans le dossier ouvert.

**Pour compiler un script U-SQL**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL: Compile Script**. Les résultats de la compilation s’affichent dans la fenêtre **Output**. Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionnez **ADL: Compile Script** pour compiler un travail U-SQL. Le résultat de la compilation s’affiche dans le volet **Output**.
 
**Pour envoyer un script U-SQL**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL: Submit Job**.  Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionner **ADL: Submit Job**. 

 Une fois que vous avez envoyé un travail U-SQL, les journaux d’envoi apparaissent dans la fenêtre **Output** dans VS Code. La vue des travaux apparaît dans le volet de droite. Si l’envoi a réussi, l’URL du travail est également affichée. Vous pouvez ouvrir l’URL du travail dans un navigateur web pour suivre l’état du travail en temps réel. Dans l’onglet Job View Summary (Résumé de la vue des travaux), vous pouvez voir les détails du travail. Les fonctions principales incluent le renvoi du script, la duplication du script, l’ouverture dans le portail. Dans l’onglet Job View Data (Données de la vue des travaux), vous pouvez vous référer aux fichiers d’entrée, aux fichiers de sortie, aux ressources. Vous pouvez télécharger les fichiers sur l’ordinateur local.

   ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Définir le contexte par défaut**

 Vous pouvez définir le contexte par défaut pour appliquer ce paramètre à tous les fichiers de script, si vous n’avez pas défini de paramètres pour chaque fichier.

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL: Set Default Context**.
3. Ou cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **ADL: Set Default Context**, puis choisissez le compte, la base de données et le schéma souhaités. Le paramètre est enregistré dans le fichier de configuration xxx_settings.json.

    ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Définir les paramètres de script**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL: Set Script Parameters**.
3. Le fichier xxx_settings.json s’ouvre avec les propriétés suivantes :

  - Compte : un compte Data Lake Analytics sous votre abonnement Azure, nécessaire pour compiler et exécuter des travaux U-SQL. Il faut donc configurer le compte d’ordinateur avant de compiler et d’exécuter des travaux U-SQL.
    - Base de données : une base de données sous votre compte. La valeur par défaut est **master**.
    - Schéma : un schéma sous votre base de données. La valeur par défaut est **dbo**.
    - Paramètres facultatifs :
        - Priorité : la plage de priorité est comprise entre 1 et 1000. 1 correspond à la priorité la plus élevée. La valeur par défaut est **1000**.
        - Parallélisme : la plage de parallélisme est comprise entre 1 et 150. La valeur par défaut est le parallélisme maximal autorisé dans votre compte Azure Data Lake Analytics. 

        ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > Une fois la configuration enregistrée, les informations du compte, de la base de données et du schéma s’affichent dans la barre d’état en bas à gauche du fichier .usql correspondant si vous n’avez pas défini de contexte par défaut.

**Set Git Ignore**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2. Entrez **ADL:  Set Git Ignore**.

    - Si votre dossier de travail VSCode ne contient pas de fichier **.gitIgnore**, un fichier nommé **.gitIgnor** est créé dans votre dossier. Quatre éléments (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** et **obj**) sont ajoutés au fichier par défaut. Vous pouvez effectuer d’autres mises à jour si nécessaire.
    - Si votre dossier de travail VSCode contient déjà un fichier **.gitIgnore**, l’outil ajoute quatre éléments (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** et **obj**) à votre fichier **.gitIgnore** si ces quatre éléments ne s’y trouvent pas.

    ![Fichier de configuration de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Utiliser le fichier code-behind Python, R et C sharp
Azure Data Lake Tool prend en charge plusieurs types de code personnalisé. Pour connaître les instructions, consultez [Développer en U-SQL avec Python, R et C sharp pour Azure Data Lake Analytics dans VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Utiliser les assemblys

Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Vous pouvez utiliser Data Lake Tools pour inscrire des assemblys de code personnalisé dans le catalogue Data Lake Analytics.

**Pour inscrire un assembly**

Vous pouvez inscrire l’assembly par le biais des commandes **ADL: Register Assembly** ou **ADL: Register Assembly (Advanced)**.

**Pour inscrire par le biais de la commande ADL: Register Assembly command**
1.  Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2.  Entrez **ADL: Register Assembly**. 
3.  Spécifiez le chemin d’accès de l’assembly local. 
4.  Sélectionnez un compte Data Lake Analytics.
5.  Sélectionnez une base de données.

Résultats : le portail s’ouvre dans le navigateur et affiche le processus d’inscription de l’assembly.  

Une autre méthode pratique pour déclencher la commande **ADL: Register Assembly** consiste à cliquer avec le bouton droit sur le fichier .dll dans l’Explorateur de fichiers. 

**Pour inscrire par le biais de la commande ADL: Register Assembly (Advanced)**
1.  Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2.  Entrez **ADL: Register Assembly (Advanced)**. 
3.  Spécifiez le chemin d’accès de l’assembly local. 
4.  Le fichier JSON s’affiche. Examinez et modifiez, si nécessaire, les paramètres des ressources et les dépendances de l’assembly. Les instructions s’affichent dans la fenêtre **Output**. Pour procéder à l’inscription de l’assembly, enregistrez (Ctrl+S) le fichier JSON.

    ![Data Lake Tools pour Visual Studio Code - code behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- Dépendances de l’assembly : Azure Data Lake Tools détecte automatiquement si la DLL a des dépendances. Une fois détectées, les dépendances s’affichent dans le fichier JSON. 
   >- Ressources : vous pouvez charger vos ressources DLL (par exemple .txt, .png et .csv) dans le cadre de l’inscription de l’assembly. 

Une autre méthode pour déclencher la commande **ADL: Register Assembly (Advanced)** consiste à cliquer avec le bouton droit sur le fichier .dll dans l’Explorateur de fichiers. 

Le code U-SQL suivant montre comment appeler un assembly. Dans l’exemple, le nom de l’assembly est *test*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="connect-to-azure"></a>Connexion à Azure

Avant de pouvoir compiler et exécuter des scripts U-SQL dans Data Lake Analytics, vous devez vous connecter à votre compte Azure.

**Pour vous connecter à Azure**

1.  Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes. 
2.  Entrez **ADL: Login**. Les informations de connexion s’affichent dans la zone supérieure.

    ![Palette de commandes de Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Informations de connexion d’appareil Data Lake Tools pour Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Cliquez sur **Copier et ouvrir** pour ouvrir la page web de connexion https://aka.ms/devicelogin. Collez le code **G567LX42V** dans la zone de texte, puis sélectionnez **Continuer**.

   ![Data Lake Tools pour Visual Studio Code - Coller le code de connexion](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Suivez les instructions pour vous connecter à partir de la page web. Une fois connecté, le nom de votre compte Azure s’affiche dans la barre d’état dans le coin inférieur gauche de la fenêtre **VS Code**. 

    > [!NOTE] 
    >- Data Lake Tool se connecte automatiquement après une première connexion, mais vous ne vous êtes pas encore déconnecté.
    >- Si votre compte a deux facteurs activés, nous vous recommandons d’utiliser l’authentification par téléphone plutôt qu’un code PIN.


Pour vous déconnecter, entrez la commande **ADL: Logout**.

## <a name="list-your-data-lake-analytics-accounts"></a>Répertorier vos comptes Data Lake Analytics

Pour tester la connexion, obtenez une liste de vos comptes Data Lake Analytics.

**Pour répertorier les comptes Data Lake Analytics dans votre abonnement Azure**

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: List Accounts**. Les comptes s’affichent dans le panneau **Sortie**.


## <a name="access-the-data-lake-analytics-catalog"></a>Accès au catalogue Data Lake Analytics

Une fois connecté à Azure, vous pouvez utiliser les étapes suivantes pour accéder au catalogue U-SQL.

**Pour accéder aux métadonnées Azure Data Lake Analytics**

1.  Sélectionnez Ctrl+Maj+P et entrez **ADL: List Tables**.
2.  Sélectionnez l’un des comptes Data Lake Analytics.
3.  Sélectionnez l’une des bases de données Data Lake Analytics.
4.  Sélectionnez l’un des schémas. La liste des tables s’affiche.

## <a name="view-data-lake-analytics-jobs"></a>Afficher les travaux Data Lake Analytics

**Pour afficher les travaux Data Lake Analytics**
1.  Ouvrez la palette de commandes (Ctrl+Maj+P) et sélectionnez **ADL: Show Jobs**. 
2.  Sélectionnez un compte local ou Data Lake Analytics. 
3.  Attendez que la liste des travaux pour le compte apparaisse.
4.  Sélectionnez un travail dans la liste des travaux. Data Lake Tools ouvre la vue des travaux dans le volet de droite et affiche des informations dans la **SORTIE** VS Code.

    ![Data Lake Tools pour Visual Studio Code - Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Intégration d’Azure Data Lake Storage

Vous pouvez utiliser les commandes Azure Data Lake Storage pour :
 - Parcourir les ressources de stockage Azure Data Lake Storage [Répertorier le chemin de stockage](#list-the-storage-path) 
 - Afficher un aperçu du fichier de stockage Azure Data Lake Storage [Afficher un aperçu du fichier de stockage](#preview-the-storage-file) 
 - Charger le fichier directement sur Azure Data Lake Storage dans VS Code [Charger un fichier ou un dossier](#upload-file-or-folder).
 - Télécharger le fichier directement à partir d’Azure Data Lake Storage dans VS Code [Télécharger le fichier](#download-file)

### <a name="list-the-storage-path"></a>Répertorier le chemin de stockage 

**Pour répertorier le chemin de stockage par le biais de la palette de commandes**

Cliquez avec le bouton droit sur l’éditeur de script, et sélectionnez **ADL: List Path** (ADL : Lister le chemin).

Choisissez le dossier dans la liste ou cliquez sur **Enter Path** (Entrer le chemin) ou **Browse from Root** (Naviguer à partir de la racine) (Enter Path est utilisé en guise d’exemple). -> Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez ou accédez au chemin du dossier de stockage (par exemple : /output/). -> La palette de commandes répertorie les informations sur le chemin en fonction des informations que vous avez entrées.

![Data Lake Tools pour Visual Studio Code - Répertorier le chemin de stockage, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Il est plus pratique d’utiliser le menu contextuel accessible par un clic droit pour répertorier le chemin relatif.

**Pour répertorier le chemin de stockage par le biais d’un clic droit**

Cliquez avec le bouton droit sur la chaîne du chemin pour sélectionner **List Path** (Lister le chemin) et continuer.

![Data Lake Tools pour Visual Studio Code - Menu contextuel accessible par un clic droit](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Afficher un aperçu du fichier de stockage

Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **ADL: Preview File** (ADL : Afficher un aperçu du fichier).

Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez un chemin de fichier de stockage Azure (par exemple, /output/SearchLog.txt). -> Résultat : le fichier s’ouvre dans VSCode.

   ![Data Lake Tools pour Visual Studio Code - Aperçu du fichier, résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Vous pouvez également afficher un aperçu du fichier à l’aide du menu accessible par un clic droit sur le chemin complet ou relatif du fichier dans l’éditeur de script. 

### <a name="upload-file-or-folder"></a>Charger un fichier ou un dossier

1. Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **Upload File** (Charger un fichier) ou **Upload Folder** (Charger un dossier).

2. Choisissez un ou plusieurs fichiers si vous avez sélectionné Upload File, ou le dossier tout entier si vous avez sélectionné Upload Folder, puis cliquez sur **Upload** (Charger). -> Choisissez le dossier de stockage dans la liste, ou cliquez sur **Enter Path** (Entrer un chemin) ou **Browse from Root** (Naviguer à partir de la racine) (Enter Path est utilisé dans l’exemple). -> Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez ou accédez au chemin du dossier de stockage (par exemple : /output/). -> Cliquez sur **Choose Current Folder** (Choisir le dossier actuel) pour spécifier la destination du chargement.

   ![Data Lake Tools pour Visual Studio Code - État du chargement](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Vous pouvez également charger un fichier dans le stockage à l’aide du menu accessible par un clic droit sur le chemin complet du fichier ou le chemin relatif du fichier dans l’éditeur de script.

En même temps, vous pouvez surveiller [l’état du chargement](#check-storage-tasks-status).


### <a name="download-file"></a>Télécharger un fichier 
Vous pouvez télécharger des fichiers en entrant les commandes **ADL: Download File** (ADL : Télécharger un fichier) ou **ADL: Download File (Advanced)** (ADL : Télécharger un fichier (Avancé)).

**Pour télécharger des fichiers par le biais de la commande ADL: Download Storage File (Advanced)**
1. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Download File (Advanced)** (Télécharger un fichier (Avancé)).
2. VS Code affiche un fichier JSON. Vous pouvez entrer des chemins de fichiers et télécharger plusieurs fichiers en même temps. Les instructions s’affichent dans la fenêtre **Output**. Pour continuer à télécharger le fichier, enregistrez (Ctrl+S) le fichier JSON.

    ![Téléchargement de fichiers Data Lake Tools pour Visual Studio Code avec la configuration](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Résultats : la fenêtre **Output** affiche l’état de chargement du fichier.

    ![Téléchargement de plusieurs fichiers Data Lake Tools pour Visual Studio Code - Résultats](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

En même temps, vous pouvez surveiller [l’état du téléchargement](#check-storage-tasks-status).

**Pour télécharger des fichiers par le biais de la commande ADL: Download Storage File**

1. Cliquez avec le bouton droit sur l’éditeur de script, sélectionnez **Download File** (Télécharger un fichier), puis sélectionnez le dossier de destination dans la boîte de dialogue **Select Folder** (Sélectionner un dossier).

2. Choisissez le dossier dans la liste ou cliquez sur **Enter Path** (Entrer le chemin) ou **Browse from Root** (Naviguer à partir de la racine) (Enter Path est utilisé en guise d’exemple). -> Sélectionnez votre compte ADLA (**ADLA Account**). -> Entrez ou accédez au chemin du dossier de stockage (par exemple : /output/) -> choisissez un fichier à télécharger.

   ![Data Lake Tools pour Visual Studio Code - État du téléchargement](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Vous pouvez également télécharger des fichiers de stockage à l’aide du menu accessible par un clic droit sur le chemin complet ou le chemin relatif du fichier dans l’éditeur de script.

En même temps, vous pouvez surveiller [l’état du téléchargement](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Vérifier l’état des tâches de stockage
L’état s’affiche en bas de la barre d’état à l’issue du téléchargement et du chargement.
1. Cliquez sur la barre d’état ci-dessous pour afficher l’état du téléchargement et du chargement dans le panneau **OUTPUT**.

   ![Data Lake Tools pour Visual Studio Code - Vérifier l’état du stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Intégration de l’Explorateur VSCode avec Azure Data Lake

**Intégration à Azure** 

- Avant de vous connecter à Azure, vous pouvez toujours développer **DATALAKE EXPLORER** (Explorateur Data Lake), puis cliquer sur **Sign in to Azur** pour vous connecter à Azure. Une fois connecté, tous les abonnements Azure apparaissent sous votre compte Azure, dans le panneau gauche de **DATALAKE EXPLORER**. 

   ![Explorateur Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![Explorateur Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**Navigation dans les métadonnées ADLA** 

- Développez votre abonnement Azure pour parcourir votre base de données SQL-U, afficher les **schémas**, les **informations d’identification**, les **assemblys**, les **tables**, les **index** et autres éléments, sous le nœud U-SQL Databases (Bases de données U-SQL).

**Gestion des entités de métadonnées ADLA**

- Développez **U-SQL Databases** pour créer une base de données, un schéma, une table, des types de table, un index ou des statistiques en double-cliquant sur l’option **Script to Create** (Script à créer) dans le menu contextuel sous le nœud correspondant. Dans la page de script ouverte, modifiez le script en fonction de vos besoins, puis envoyez le travail en cliquant avec le bouton droit sur l’option **ADL: Submit Job** (ADL : Envoyer le travail) du menu contextuel. Une fois l’élément créé, cliquez sur l’option **Refresh** (Actualiser) dans le menu contextuel pour afficher le nouvel élément. Vous pouvez également supprimer l’élément en cliquant avec le bouton droit sur l’option **Delete** (Supprimer) du menu contextuel.

   ![L’Explorateur DataLake crée un nouveau menu d’élément](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![L’Explorateur DataLake crée un nouveau script d’élément](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**Inscription d'assembly ADLA**

 - Vous pouvez **inscrire l’assembly** dans la base de données correspondante en cliquant avec le bouton droit sur le nœud **Assemblys**.

    ![Explorateur Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**Intégration à ADLS** 

 - Accédez au **compte de stockage** pour **afficher un aperçu**, **télécharger**, **supprimer**, **copier le chemin d’accès relatif** ou **copier le chemin d’accès complet** via le menu contextuel du nœud de fichier. Vous pouvez **actualiser**, **charger**, **charger un dossier** ou **supprimer** en cliquant avec le bouton droit dans le menu contextuel du nœud de dossier.

   ![Explorateur Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

   ![Explorateur Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Ouvrir l’explorateur de stockage ADL sur le portail
1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **Open Web Azure Storage Explorer** ou cliquez avec le bouton droit sur un chemin relatif ou le chemin complet dans l’éditeur de script, puis sélectionnez **Open Web Azure Storage Explorer**.
3. Sélectionnez un compte Data Lake Analytics.

Data Lake Tools ouvre le chemin de stockage Azure dans le portail Azure. Vous pouvez rechercher le chemin et afficher un aperçu du fichier à partir du web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Exécution locale et débogage local pour les utilisateurs de Windows
L’exécution locale de U-SQL teste vos données locales et valide votre script localement, avant que votre code soit publié dans Data Lake Analytics. La fonctionnalité de débogage local vous permet d’effectuer les tâches suivantes avant que votre code soit soumis à Data Lake Analytics : 
- Déboguer votre code-behind C# 
- Examiner le code 
- Valider votre script localement

Pour obtenir des instructions sur l’exécution locale et le débogage local, consultez [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Fonctionnalités supplémentaires

Data Lake Tools pour VS Code prend en charge les fonctionnalités suivantes :

-   Saisie semi-automatique IntelliSense : des suggestions s’affichent dans des fenêtres indépendantes autour des éléments tels que les mots clés, les méthodes et les variables. Des icônes différentes représentent des types d’objets différents :

    - Type de données Scala
    - Type de données complexe
    - Types définis par l’utilisateur (UDT) intégrés
    - Classes et collection .NET
    - Expressions C#
    - UDF, UDO et UDAAG C# intégrés 
    - Fonctions U-SQL
    - Fonction de fenêtrage U-SQL
 
    ![Data Lake Tools pour Visual Studio Code - Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Saisie semi-automatique IntelliSense sur les métadonnées Data Lake Analytics : Data Lake Tools télécharge les informations de métadonnées Data Lake Analytics localement. La fonctionnalité IntelliSense remplit automatiquement les objets, notamment les bases de données, les schémas, les tables, les vues, les fonctions table, les procédures et les assemblys C# à partir des métadonnées Data Lake Analytics.
 
    ![Data Lake Tools pour Visual Studio Code - Métadonnées IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marqueur d’erreur IntelliSense : Data Lake Tools souligne les erreurs d’édition pour U-SQL et C#. 
-   Coloration syntaxique : Data Lake Tools utilise différentes couleurs pour différencier les éléments tels que les variables, les mots clés, les types de données et les fonctions. 

    ![Data Lake Tools pour Visual Studio Code - Points clés de la syntaxe](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>étapes suivantes
- [Développer en U-SQL avec Python, R et C sharp pour Azure Data Lake Analytics dans VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Didacticiel : Bien démarrer avec Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Développer des assemblys U-SQL pour des travaux Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




