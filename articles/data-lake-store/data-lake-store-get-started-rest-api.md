---
title: "API REST : Opérations de gestion des comptes sur Azure Data Lake Store | Microsoft Docs"
description: "Utilisez Azure Data Lake Store et l’API REST WebHDFS pour effectuer des opérations de gestion des comptes dans Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5fafde870a01a6ceb5e86f7b00b0ca11b748c68a
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Opérations de gestion des comptes sur Azure Data Lake Store à l’aide de L’API REST
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Dans cet article, vous allez découvrir comment réaliser des opérations de gestion des comptes sur Data Lake Store avec l’API REST. Ces opérations incluent la création ou la suppression d’un compte Data Lake Store, etc. Pour obtenir des instructions sur l’exécution des opérations du système de fichiers sur Data Lake Store à l’aide de l’API REST, consultez la section relative aux [opérations du système de fichiers sur Data Lake Store à l’aide de l’API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Conditions préalables
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Cet article utilise cURL pour montrer comment effectuer des appels d’API REST sur un compte Data Lake Store.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifier à l’aide d’Azure Active Directory ?
Vous avez le choix entre deux méthodes pour vous authentifier à l’aide d’Azure Active Directory :

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application (interactive), consultez la section relative à l’[authentification de l’utilisateur avec Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Pour en savoir plus sur l’authentification entre les services pour votre application (non interactive), consultez la section relative à l’[authentification entre les services avec Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-store-account"></a>Créer un compte Data Lake Store
Cette opération est basée sur l’appel d’API REST défini [ici](https://msdn.microsoft.com/library/mt694078.aspx).

Utilisez la commande cURL suivante. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Dans la commande ci-dessus, remplacez \<`REDACTED`\> par le jeton d’autorisation que vous avez récupéré précédemment. La charge utile de la demande pour cette commande est contenue dans le fichier **input.json** fourni pour le paramètre `-d` ci-dessus. Le contenu du fichier input.json ressemble à l’extrait de code suivant :

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Supprimer un compte Data Lake Store
Cette opération est basée sur l’appel d’API REST défini [ici](https://msdn.microsoft.com/library/mt694075.aspx).

Utilisez la commande cURL suivante pour supprimer un compte Data Lake Store. Remplacez **\<yourstorename>** par le nom de votre Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Un résultat similaire à l’extrait de code suivant doit s’afficher :

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Étapes suivantes
* [Opérations du système de fichiers sur Data Lake Store à l’aide de l’API REST](data-lake-store-data-operations-rest-api.md)

## <a name="see-also"></a>Voir aussi
* [Référence relative à l’API REST d’Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

