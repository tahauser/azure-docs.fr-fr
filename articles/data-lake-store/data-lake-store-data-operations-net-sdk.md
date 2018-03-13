---
title: "Kit de développement logiciel (SDK) .NET : opération de gestion du système de fichiers sur Azure Data Lake Store | Microsoft Docs"
description: "Utilisez le kit de développement logiciel (SDK) .NET d’Azure Data Lake Store pour réaliser des opérations de gestion du système de fichiers sur Data Lake Store, telles que la création de dossiers, etc."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 1094a5ea3c000707aa7736d22d4df0558da32b5e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Opérations de gestion du système de fichiers sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Dans cet article, vous apprenez à réaliser des opérations de gestion du système de fichiers sur Data Lake Store avec le kit de développement logiciel (SDK) .NET. Les opérations de gestion du système de fichiers comprennent la création de dossiers dans un compte Data Lake Store, le chargement et le téléchargement de fichiers, etc.

Pour obtenir des instructions sur l’exécution des opérations de gestion des comptes sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET, consultez la section relative aux [opérations de gestion de compte sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Prérequis
* **Visual Studio 2013, 2015 ou 2017**. Les instructions ci-dessous reposent sur Visual Studio 2017.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Store**. Pour savoir comment créer un compte, consultez [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Créer une application .NET
L’exemple de code disponible [sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) vous guide tout au long du processus de création de fichiers dans le magasin, de concaténation de fichiers, de téléchargement d’un fichier et de suppression de certains fichiers du compte. Cette section de l’article vous guide tout au long des principales parties du code.

1. Ouvrez Visual Studio et créez une application console.
2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

   | Propriété | Valeur |
   | --- | --- |
   | Catégorie |Modèles/Visual C#/Windows |
   | Modèle |Application console |
   | NOM |CreateADLApplication |

4. Cliquez sur **OK** pour créer le projet.

5. Ajoutez les packages NuGet à votre projet.

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
   3. Recherchez et installez les packages NuGet suivants :

      * `Microsoft.Azure.DataLake.Store` : ce didacticiel utilise v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` : ce didacticiel utilise v2.3.1.
    
    Fermez le **Gestionnaire de package NuGet**.

6. Ouvrez **Program.cs**, supprimez le code existant, puis insérez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Déclarez les variables comme indiqué ci-dessous, et fournissez les valeurs des espaces réservés. En outre, assurez-vous que le chemin d’accès local et le nom de fichier que vous fournissez ici existent sur l’ordinateur.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net";        
            }
        }

Dans les sections suivantes de cet article, vous pouvez découvrir comment utiliser les méthodes .NET pour effectuer des opérations telles que l’authentification des utilisateurs et le chargement de fichiers.

## <a name="authentication"></a>Authentification

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application, consultez la section relative à [l’authentification de l’utilisateur avec Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Pour en savoir plus sur l’authentification entre les services dans le cadre de votre application, consultez la section relative à [l’authentification entre les services avec Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Créer un objet client
L’extrait de code suivant permet de créer l’objet client filesystem Data Lake Store qui est utilisé pour adresser des requêtes au service.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Créer un fichier un répertoire
Ajoutez l’extrait de code suivant à votre application. Cet extrait de code permet d’ajouter un fichier, ainsi que tout répertoire parent qui n’existe pas.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOuputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Ajout à un fichier
L’extrait de code suivant permet d’ajouter des données à un fichier existant dans le compte Data Lake Store.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Lire un fichier
L’extrait de code suivant permet de lire le contenu d’un fichier dans un compte Data Lake Store.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Obtenir les propriétés de fichier
L’extrait de code suivant permet de renvoyer les propriétés associées à un fichier ou un répertoire.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

La définition de la méthode `PrintDirectoryEntry` n’est disponible que dans l’exemple [sur Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Renommer un fichier
L’extrait de code suivant permet de renommer un fichier existant dans un compte Data Lake Store.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Énumérer un répertoire
L’extrait de code suivant permet d’énumérer des répertoires dans un compte Data Lake Store.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

La définition de la méthode `PrintDirectoryEntry` n’est disponible que dans l’exemple [sur Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Supprimer des répertoires de manière récursive
L’extrait de code suivant permet de supprimer un répertoire et tous ses sous-répertoires de manière récursive.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Exemples
Voici quelques exemples sur la façon d’utiliser le Kit de développement logiciel (SDK) Filesystem Data Lake Store.
* [Exemple de base sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemple avancé sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Voir aussi
* [Opérations de gestion des comptes sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK Reference (Informations de référence sur le Kit de développement logiciel (SDK) .NET Azure Data Lake Store)](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
