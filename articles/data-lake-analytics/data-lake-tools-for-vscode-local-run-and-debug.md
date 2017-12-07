---
title: "Azure Data Lake Tools : exécution locale et débogage local U-SQL avec Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour l’exécution locale et le débogage local."
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview storage file,upload to storage path
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: d109e4d57f4ad5ab2be73805ba41bf9ed362cccb
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="u-sql-local-run-and-local-debug-for-windows-with-visual-studio-code"></a>Exécution locale et débogage local U-SQL pour Windows avec Visual Studio Code
Dans ce document, vous allez découvrir comment exécuter des travaux U-SQL sur un ordinateur de développement local pour accélérer les premières phases de codage ou pour déboguer le code localement dans Visual Studio Code. Pour obtenir des instructions concernant Azure Data Lake Tools pour Visual Studio Code, consultez [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 


## <a name="set-up-the-u-sql-local-run-environment"></a>Configurer l’environnement d’exécution locale U-SQL

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Download Local Run Dependency** pour télécharger les packages.  

   ![Télécharger les packages ADL LocalRun Dependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Recherchez les packages de dépendance à partir du chemin indiqué dans le volet **Sortie**, puis installez BuildTools et Win10SDK 10240. Voici un exemple de chemin :  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Rechercher les packages de dépendances](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Pour installer **BuildTools**, cliquez sur visualcppbuildtools_full.exe dans le dossier LocalRunDependency, puis suivez les instructions de l’Assistant.   

    ![Installer BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Pour installer **Win10SDK 10240**, cliquez sur sdksetup.exe dans le dossier LocalRunDependency/Win10SDK_10.0.10240_2, puis suivez les instructions de l’Assistant.  

    ![Installer Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Définissez la variable d’environnement. Définissez la variable d’environnement **SCOPE_CPP_SDK** sur :  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Démarrer le service d’exécution locale et envoyer le travail U-SQL à un compte local 
S’il s’agit de votre première utilisation, utilisez **ADL: Download Local Run Dependency** pour télécharger les packages d’exécution locale si vous n’avez pas [configurer d’environnement d’exécution locale U-SQL](#set-up-the-u-sql-local-run-environment).

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Start Local Run Service**.   
2. Sélectionnez **Accepter** pour accepter les termes du contrat de licence du logiciel Microsoft pour la première fois. 

   ![Accepter les termes du contrat de licence du logiciel Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. La console cmd s’ouvre. Les nouveaux utilisateurs doivent entrer **3**, puis rechercher le chemin de dossier local pour l’entrée et la sortie des données. Pour les autres options, vous pouvez utiliser les valeurs par défaut. 

   ![Data Lake Tools pour Visual Studio Code - cmd exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes et entrez **ADL: Submit Job**, puis sélectionnez **Local** pour envoyer le travail à votre compte local.

   ![Data Lake Tools pour Visual Studio Code - Sélectionner Local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Après avoir envoyé le travail, vous pouvez afficher les détails de l’envoi. Pour afficher les détails de l’envoi, sélectionnez **jobUrl** dans la fenêtre **Sortie**. Vous pouvez également afficher l’état de l’envoi du travail à partir de la console cmd. Entrez **7** dans la console cmd si vous souhaitez connaître plus de détails sur le travail.

   ![Data Lake Tools pour Visual Studio Code - Sortie exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools pour Visual Studio Code - État cmd exécution locale](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Démarrer un débogage local pour le travail U-SQL  
S’il s’agit de votre première utilisation :

1. Utilisez **ADL: Download Local Run Dependency** pour télécharger les packages d’exécution locale si vous n’avez pas [configurer d’environnement d’exécution locale U-SQL](#set-up-the-u-sql-local-run-environment).
2. Installez le kit .NET Core SDK 2.0 comme suggéré dans la boîte de message, s’il n’est pas encore installé.
 
  ![rappel d’installation Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installez C# pour Visual Studio Code comme suggéré dans la boîte de message, s’il n’est pas encore installé. Cliquez sur **Installer** pour continuer, puis redémarrez VSCode.

    ![Rappel concernant l’installation de C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Suivez les étapes ci-dessous pour effectuer le débogage local :
  
1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Start Local Run Service**. La console cmd s’ouvre. Vérifiez que la valeur **DataRoot** est définie.
2. Définissez un point d’arrêt dans votre code C# code-behind.
3. De retour dans l’Éditeur de script, cliquez avec le bouton droit et sélectionnez **ADL: Local Debug** (Débogage local).
    
   ![Data Lake Tools pour Visual Studio Code - Résultat débogage local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Étapes suivantes
* [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Développer en U-SQL avec Python, R et C sharp pour Azure Data Lake Analytics dans VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Développer des assemblys U-SQL pour des travaux Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)
* [Prise en main de Data Lake Analytics à l’aide de PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Utiliser les outils Data Lake pour Visual Studio pour le développement d’applications U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilisation du catalogue Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
