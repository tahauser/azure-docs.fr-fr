---
title: "Python : Opérations de gestion des comptes sur Azure Data Lake Store | Microsoft Docs"
description: "Découvrez comment gérer le Kit de développement logiciel (SDK) Python au moyen des opérations de gestion des comptes sur Azure Data Lake Store."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 834dbc93348e00b0a0821bcbca989b0c2d539056
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Opérations de gestion des comptes sur Azure Data Lake Store à l’aide de Python
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Découvrez comment utiliser le Kit de développement logiciel (SDK) Python pour Azure Data Lake Store afin d’effectuer des opérations simples de gestion des comptes (création de compte ou d’une liste des comptes existants, etc.). Pour obtenir des instructions sur l’exécution des opérations de gestion du système de fichiers sur Data Lake Store à l’aide de Python, consultez la section relative aux [opérations du système de fichiers sur Data Lake Store à l’aide de Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Conditions préalables

* **Python** Pour télécharger Python, accédez [ici](https://www.python.org/downloads/). Cet article utilise Python 3.6.2.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un groupe de ressources Azure**. Pour obtenir des instructions, consultez la page [Créer un groupe de ressources Azure](../azure-resource-manager/resource-group-portal.md).

## <a name="install-the-modules"></a>Installer les modules

Pour utiliser Data Lake Store avec Python, vous devez installer trois modules.

* Le module `azure-mgmt-resource`, qui inclut des modules Azure pour Active Directory, etc.
* Le module `azure-mgmt-datalake-store`, qui inclut les opérations de gestion des comptes Azure Data Lake Store. Pour plus d’informations sur ce module, consultez les [informations de référence sur les modules de gestion Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* Le module `azure-datalake-store`, qui inclut les opérations de gestion du système de fichiers Azure Data Lake Store. Pour plus d’informations sur ce module, consultez les [informations de référence sur les modules du système de fichiers Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Utilisez les commandes suivantes pour installer les modules.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Créer une application Python

1. Utilisez l’IDE de votre choix pour créer une application Python, par exemple, **mysample.py**.

2. Ajoutez l’extrait de code suivant pour importer les modules requis

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Enregistrez les modifications apportées à mysample.py.

## <a name="authentication"></a>Authentification

Dans cette section, nous aborderons les différentes méthodes permettant de s’authentifier auprès d’Azure AD. Voici les options disponibles :

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application, consultez la section relative à [l’authentification de l’utilisateur avec Data Lake Store à l’aide de Python](data-lake-store-end-user-authenticate-python.md).
* Pour en savoir plus sur l’authentification entre les services dans le cadre de votre application, consultez la section relative à [l’authentification entre les services avec Data Lake Store à l’aide de Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-store-account"></a>Créer un compte Data Lake Store et client

L’extrait de code suivant crée dans un premier temps le client du compte Data Lake Store. Il utilise l’objet client pour créer le compte Data Lake Store. Enfin, l’extrait de code crée un objet client de système de fichiers.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Répertorier les comptes Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Supprimer le compte Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Étapes suivantes
* [Opérations du système de fichiers sur Data Lake Store à l’aide de Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Voir aussi
* [Documents de référence sur la gestion des comptes avec Azure Data Lake Store et Python](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Documents de référence sur la gestion de système de fichiers avec Azure Data Lake Store et Python](http://azure-datalake-store.readthedocs.io/en/latest)
* [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
