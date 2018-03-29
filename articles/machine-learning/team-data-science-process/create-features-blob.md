---
title: Créer des fonctionnalités pour les données de stockage d’objets blob Azure à l’aide de Pandas | Microsoft Docs
description: Comment créer des fonctionnalités pour les données stockées dans un conteneur d’objets blob Azure avec le package Python Pandas.
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev
ms.openlocfilehash: a5a4b92f04fcb4683f792263f3477ecea4d7239e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Créer des fonctionnalités pour les données de stockage d’objets blob Azure à l’aide de Pandas
Ce document montre comment créer des fonctionnalités pour les données stockées dans un conteneur d’objets blob Azure à l’aide du package Python [Pandas](http://pandas.pydata.org/) . Après avoir décrit le chargement des données dans une trame de données Pandas, il montre comment générer des fonctionnalités catégorielles à l’aide de scripts Python avec des valeurs d’indicateur et des caractéristiques de compartimentage.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Ce **menu** pointe vers des rubriques qui expliquent comment créer des fonctionnalités pour les données dans différents environnements. Cette tâche est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous avez créé un compte de stockage d’objets blob Azure et que vous y avez stocké vos données. Si vous avez besoin d’instructions pour configurer un compte, voir [Créer un compte Stockage Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="load-the-data-into-a-pandas-data-frame"></a>Chargement des données dans une trame de données Pandas
Pour explorer et exploiter un jeu de données, téléchargez-le depuis la source blob vers un fichier local. Puis chargez-le dans une trame de données Pandas. Voici les étapes à suivre pour cette procédure :

1. Téléchargez les données à partir du blob Azure avec l’exemple de code Python à l’aide du service du blob. Remplacez la variable dans le code ci-dessous par vos propres valeurs :
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Lisez les données du fichier téléchargé dans une table Pandas.
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Vous êtes maintenant prêt à explorer les données et à générer des fonctionnalités sur cet ensemble de données.

## <a name="blob-featuregen"></a>Génération de fonctionnalités
Les deux sections suivantes indiquent comment générer des fonctionnalités catégorielles avec des valeurs d’indicateur et des caractéristiques de compartimentage à l’aide de scripts Python.

### <a name="blob-countfeature"></a>Génération de caractéristiques à partir de valeurs d’indicateur
Pour créer des caractéristiques de catégorie, procédez comme suit :

1. Examinez la distribution de la colonne de catégorie :
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Générez les valeurs d’indicateur pour chacune des valeurs de colonne :
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Créez une jointure entre la colonne d’indicateurs et le bloc de données d’origine :
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Supprimez la variable d’origine :
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Génération de caractéristiques de compartimentage
Pour générer des fonctionnalités compartimentées, procédez comme suit :

1. Ajoutez une séquence de colonnes pour compartimenter une colonne numérique :
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Convertissez le compartimentage en une séquence de variables booléennes :
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Enfin, créez une jointure entre les variables factices et le bloc de données d’origine :
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Réécriture de données dans l’objet blob Azure pour une exploitation dans Azure Machine Learning
Pour exploiter dans Azure Machine Learning les données que vous avez explorées, échantillonnées ou implémentées, importez les données dans un objet blob Azure. D'autres fonctionnalités peuvent également être créées dans Azure Machine Learning Studio. Les étapes suivantes montrent comment charger les données :

1. Écrivez le bloc de données dans le fichier local.
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Chargez les données dans le blob Azure, en procédant comme suit :
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. À présent, les données sont lisibles à partir de l’objet blob à l’aide du module [Importer des données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) d’Azure Machine Learning comme le montre la capture d'écran suivantes :

![objet blob de lecteur](./media/data-blob/reader_blob.png)

