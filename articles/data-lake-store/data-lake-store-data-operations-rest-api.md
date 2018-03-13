---
title: "API REST : opération de gestion du système de fichiers sur Azure Data Lake Store | Microsoft Docs"
description: "Utiliser les API REST de WebHDFS pour effectuer des opérations de gestion du système de fichiers sur Data Lake Store"
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
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: a850b3fdff956abe41ac9a4af10a96dc119a75f4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Opérations de gestion du système de fichiers sur Data Lake Store à l’aide de l’API REST
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Dans cet article, vous allez découvrir comment utiliser les API REST de WebHDFS et de Data Lake Store pour effectuer des opérations de gestion du système de fichiers sur Azure Data Lake Store. Pour obtenir des instructions sur l’exécution des opérations de gestion des comptes sur Data Lake Store à l’aide de l’API REST, consultez la section relative aux [opérations de gestion des comptes sur Data Lake Store à l’aide de l’API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Conditions préalables
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d’Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).

* **[cURL](http://curl.haxx.se/)**. Cet article utilise cURL pour montrer comment effectuer des appels d’API REST sur un compte Data Lake Store.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifier à l’aide d’Azure Active Directory ?
Vous avez le choix entre deux méthodes pour vous authentifier à l’aide d’Azure Active Directory :

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application (interactive), consultez la section relative à l’[authentification de l’utilisateur avec Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Pour en savoir plus sur l’authentification entre les services pour votre application (non interactive), consultez la section relative à l’[authentification entre les services avec Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Créez les dossiers.
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilisez la commande cURL suivante. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Dans la commande précédente, remplacez \<`REDACTED`\> par le jeton d’autorisation que vous avez récupéré précédemment. Cette commande crée un répertoire appelé **mytempdir** sous le dossier racine de votre compte Data Lake Store.

Si l’opération s’est déroulée correctement, vous devez voir une réponse similaire à l’extrait de code suivant :

    {"boolean":true}

## <a name="list-folders"></a>Répertorier les dossiers
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilisez la commande cURL suivante. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

Dans la commande précédente, remplacez \<`REDACTED`\> par le jeton d’autorisation que vous avez récupéré précédemment.

Si l’opération s’est déroulée correctement, vous devez voir une réponse similaire à l’extrait de code suivant :

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Télécharger des données
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Utilisez la commande cURL suivante. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

Dans la syntaxe précédente, le paramètre **-T** correspond à l’emplacement du fichier que vous chargez.

Le résultat ressemble à l’extrait de code suivant :
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Lire les données
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lecture des données d’un compte Data Lake Store s’effectue en deux étapes.

* D’abord, envoyez une demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Cet appel retourne un emplacement où envoyer la demande GET suivante.
* Ensuite, envoyez la deuxième demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Cet appel affiche le contenu du fichier.

Toutefois, comme les paramètres d’entrée sont les mêmes pour la première et la deuxième étape, vous pouvez utiliser le paramètre `-L` pour envoyer la première demande. L’option `-L` permet essentiellement de combiner les deux demandes en une seule et d’indiquer à cURL de réexécuter la demande sur le nouvel emplacement. Le résultat final de tous les appels de la demande s’affiche, comme illustrée dans l’extrait de code suivant. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Le résultat doit ressembler à l’extrait de code suivant :

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Renommer un fichier
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilisez la commande cURL suivante pour renommer un fichier. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Le résultat doit ressembler à l’extrait de code suivant :

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Supprimer un fichier
Cette opération est basée sur l’appel de l’API REST de WebHDFS défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilisez la commande cURL suivante pour supprimer un fichier. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Un résultat similaire à ce qui suit s’affiche normalement :

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Étapes suivantes
* [Opérations de gestion des comptes sur  Data Lake Store à l’aide de l’API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Voir aussi
* [Référence relative à l’API REST d’Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

