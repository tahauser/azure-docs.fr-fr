---
title: "Authentification de service à service : Python avec Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment procéder à une authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory à l’aide de Python"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: c04b870e72c5d29df95d16b96cc423441af6fd85
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Authentification de service à service auprès de Data Lake Store à l’aide de Python
> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Dans cet article, vous allez apprendre à utiliser le Kit SDK Python pour effectuer une authentification de service à service auprès d’Azure Data Lake Store. Pour plus d’informations sur l’authentification des utilisateurs finaux auprès d’Azure Data Lake Store à l’aide de Python, consultez [Authentification des utilisateurs finaux auprès de Data Lake Store avec Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Conditions préalables

* **Python** Pour télécharger Python, accédez [ici](https://www.python.org/downloads/). Cet article utilise Python 3.6.2.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « web » Azure Active Directory**. Vous devez avoir suivi la procédure [Authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Enregistrez les modifications apportées à mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>L’authentification de service à service avec clé secrète client pour al gestion de compte

Utilisez cet extrait de code pour l’authentification auprès d’Azure AD pour les opérations de gestion de compte sur Data Lake Store, telles que la création ou la suppression d’un compte Data Lake Store, etc. Vous pouvez utiliser l’extrait de code suivant pour authentifier votre application en mode non interactif, en utilisant la clé secrète client pour une application/un principal de service d’une application « Web App » Azure AD existante.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Authentification de service à service avec clé secrète client pour les opérations du système de fichiers

Utilisez l’extrait de code suivant pour procéder à une authentification auprès d’Azure AD pour les opérations du système de fichiers sur Data Lake Store, telles que la création d’un dossier, le chargement d’un fichier, etc. Vous pouvez faire appel à l’extrait de code suivant pour authentifier votre application en mode non interactif, en utilisant la clé secrète client pour une application/un principal du service. Utilisez-le avec une « application web » Azure AD existante.

    adlCreds = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE', resource = 'https://datalake.azure.net/')

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Store such as create Data Lake Store account, delete Data Lake Store account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser l’authentification de service à service auprès d’Azure Data Lake Store avec Python. Vous pouvez maintenant consulter les articles suivants, qui expliquent comment utiliser Python pour travailler avec Azure Data Lake Store.

* [Opérations de gestion des comptes sur Data Lake Store à l’aide de Python](data-lake-store-get-started-python.md)
* [Opérations de données sur Data Lake Store à l’aide de Python](data-lake-store-data-operations-python.md)


