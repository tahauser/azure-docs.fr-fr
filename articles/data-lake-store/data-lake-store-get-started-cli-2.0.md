---
title: "Utiliser l’interface de ligne de commande Azure 2.0 pour la prise en main d’Azure Data Lake Store | Microsoft Docs"
description: "Utilisation de l’interface de ligne de commande multi-plateforme Azure 2.0 pour créer un compte Data Lake Store et effectuer des opérations de base"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: b93b9a801b3e5e1fd869cda7f3d67554e2d03195
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Prise en main d’Azure Data Lake Store avec Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Apprenez à utiliser Azure CLI 2.0 pour créer un compte Azure Data Lake Store et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake Store, consultez [Vue d’ensemble de Data Lake Store](data-lake-store-overview.md).

Azure CLI 2.0 est la nouvelle expérience de ligne de commande Azure pour la gestion des ressources Azure. Elle peut être utilisée sur macOS, Linux et Windows. Pour plus d’informations, voir [Présentation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure). Pour obtenir la liste complète des commandes et de la syntaxe, consultez la [Référence Azure Data Lake Store CLI 2.0](https://docs.microsoft.com/cli/azure/dls).


## <a name="prerequisites"></a>Prérequis

Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI 2.0** - Voir [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) pour plus d’instructions.

## <a name="authentication"></a>Authentification

Pour l’authentification auprès de Data Lake Store, cet article utilise une approche plus simple où vous vous connectez en tant qu’utilisateur final. Le niveau d’accès au compte et au système de fichiers Data Lake Store est alors régi par le niveau d’accès de l’utilisateur connecté. Cependant, il existe d’autres approches pour l’authentification sur Data Lake Store, à savoir **l’authentification de l’utilisateur final** ou **l’authentification de service à service**. Pour obtenir des instructions et plus d’informations sur l’authentification, consultez [l’authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [l’authentification de service à service](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

1. Connectez-vous à votre abonnement Azure.

    ```azurecli
    az login
    ```

    Vous obtenez un code que vous utiliserez à l’étape suivante. Ouvrez la page https://aka.ms/devicelogin dans un navigateur web et entrez le code pour vous authentifier. Vous êtes invité à vous connecter en utilisant vos informations d’identification.

2. Une fois que vous êtes connecté, la fenêtre répertorie tous les abonnements Azure qui sont associés à votre compte. Exécutez la commande suivante pour utiliser un abonnement spécifique.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure Data Lake Store

1. Créez un groupe de ressources. Dans la commande suivante, définissez des valeurs de paramètre que vous souhaitez utiliser. Si le nom de l'emplacement contient des espaces, entourez-le de guillemets. Par exemple, « East US 2 ». 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Créez le compte Data Lake Store.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Créer des dossiers dans un compte Data Lake Store

Vous pouvez créer des dossiers dans votre compte Azure Data Lake Store pour gérer et stocker des données. Utilisez la commande suivante pour créer un dossier nommé **mynewfolder** à la racine du Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> Le paramètre `--folder` permet de s’assurer que la commande crée un dossier. Si ce paramètre n’est pas présent, la commande crée un fichier vide appelé mynewfolder à la racine du compte Data Lake Store.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Charger des données dans un compte Azure Data Lake Store

Vous pouvez charger des données dans Data Lake Store directement à la racine ou dans un dossier que vous avez créé dans le compte. Les extraits de code ci-dessous montrent comment charger des exemples de données dans le dossier (**mynewfolder**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et stockez-le dans un répertoire local sur votre ordinateur, comme C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Pour la destination, vous devez spécifier le chemin d’accès complet, y compris le nom de fichier.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Répertorier les fichiers dans un compte Data Lake Store

Utilisez la commande suivante pour afficher les fichiers dans un compte Data Lake Store.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

Le résultat doit ressembler à ceci :

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Renommer, télécharger et supprimer des données de votre compte Data Lake Store 

* Utilisez la commande suivante **pour renommer un fichier** :
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* Utilisez la commande suivante **pour télécharger un fichier**. Assurez-vous que le chemin de destination que vous spécifiez existe.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > La commande crée le dossier de destination, si celui-ci n’existe pas.
    > 
    >

* Utilisez la commande suivante **pour supprimer un fichier** :
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Si vous souhaitez supprimer simultanément le dossier **mynewfolder** et le fichier **vehicle1_09142014_copy.csv** en une seule commande, utilisez le paramètre --recurse

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Fonctionne avec les autorisations et les listes ACL pour un compte Data Lake Store

Dans cette section, vous allez apprendre à gérer les listes ACL et les autorisations à l’aide d’Azure CLI 2.0. Pour obtenir une présentation détaillée sur la façon dont les listes ACL sont implémentées dans Azure Data Lake Store, voir [Contrôle d’accès dans Azure Data Lake Store](data-lake-store-access-control.md).

* **Pour mettre à jour le propriétaire d’un fichier/dossier**, utilisez la commande suivante :

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Pour mettre à jour les autorisations d’un fichier/dossier**, utilisez la commande suivante :

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Pour obtenir les listes ACL pour un chemin d’accès donné**, utilisez la commande suivante :

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    Le résultat doit ressembler à ce qui suit :

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Pour définir une entrée pour une liste ACL**, utilisez la commande suivante :

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Pour supprimer une entrée d’une liste ACL**, utilisez la commande suivante :

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Pour supprimer dans son intégralité une liste ACL par défaut**, utilisez la commande suivante :

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Pour supprimer dans son intégralité une liste ACL autre que la liste ACL par défaut**, utilisez la commande suivante :

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Supprimer un compte Data Lake Store
Utilisez la commande suivante pour supprimer un compte Data Lake Store.

```azurecli
az dls account delete --account mydatalakestore
```

Quand vous y êtes invité, entrez **Y** pour supprimer le compte.

## <a name="next-steps"></a>Étapes suivantes
* [Utiliser Azure Data Lake Store pour les données volumineuses](data-lake-store-data-scenarios.md) 
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
